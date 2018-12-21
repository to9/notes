### 交叉编译arm平台boost库之spinlock自旋锁问题

#### 交叉编译目CPU为cortex-a9架构armv7指令集
```bash
root@0b37e82df797:/opt/test_boost_spinlock# arm-linux-gnueabi-g++-4.7 -v                                                                               
Using built-in specs.
COLLECT_GCC=arm-linux-gnueabi-g++-4.7
COLLECT_LTO_WRAPPER=/usr/lib/gcc-cross/arm-linux-gnueabi/4.7/lto-wrapper
Target: arm-linux-gnueabi
Configured with: ../src/configure -v --with-pkgversion='Ubuntu/Linaro 4.7.3-12ubuntu1' --with-bugurl=file:///usr/share/doc/gcc-4.7/README.Bugs --enable-languages=c,c++,go,fortran,objc,obj-c++ --prefix=/usr --program-suffix=-4.7 --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --with-gxx-include-dir=/usr/arm-linux-gnueabi/include/c++/4.7.3 --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-gnu-unique-object --disable-libmudflap --disable-libitm --enable-plugin --with-system-zlib --enable-objc-gc --with-cloog --enable-cloog-backend=ppl --disable-cloog-version-check --disable-ppl-version-check --enable-multiarch --enable-multilib --disable-sjlj-exceptions --with-arch=armv5t --with-float=soft --disable-werror --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=arm-linux-gnueabi --program-prefix=arm-linux-gnueabi- --includedir=/usr/arm-linux-gnueabi/include
Thread model: posix
gcc version 4.7.3 (Ubuntu/Linaro 4.7.3-12ubuntu1) 
```

通过上面查看gcc编译器版本可看到默认的是--with-arch=armv5t指令集，对于新的架构而言我们应该使用最新指令集，原因请看后面分析。

编译main.cpp文件两种方式：
第一中使用了默认交叉编译器配置，也就是我们通过-v看到的配置，所以默认使用的是armv5t指令集进行编译。
第二种是我们指定了交叉编译器使用armv7指令集，cortex-a9架构，浮点等配置。

```
1.arm-linux-gnueabi-g++-4.7 -g main.cpp -o main -Iboost/  -L/opt/build-make-relwithdebinfo/external/boost/libboost.a
2.arm-linux-gnueabi-g++-4.7 -march=armv7-a -mthumb-interwork -mfloat-abi=soft -mfpu=neon -mtune=cortex-a9 -g main.cpp -o main -Iboost/  -L/opt/build-make-relwithdebinfo/external/boost/libboost.a
```


```c++
#include <boost/smart_ptr/detail/spinlock.hpp>

int main()
{
   int i = 0;

   boost::detail::spinlock spin_lock;
   spin_lock.lock();

   return 0;
}
```

boost/boost/smart_ptr/detail/spinlock_gcc_arm.hpp
```c++
#ifndef BOOST_SMART_PTR_DETAIL_SPINLOCK_GCC_ARM_HPP_INCLUDED
#define BOOST_SMART_PTR_DETAIL_SPINLOCK_GCC_ARM_HPP_INCLUDED

//
//  Copyright (c) 2008, 2011 Peter Dimov
//
//  Distributed under the Boost Software License, Version 1.0.
//  See accompanying file LICENSE_1_0.txt or copy at
//  http://www.boost.org/LICENSE_1_0.txt)
//

#include <boost/smart_ptr/detail/yield_k.hpp>


#if defined(__ARM_ARCH_7__) || defined(__ARM_ARCH_7A__) || defined(__ARM_ARCH_7R__) || defined(__ARM_ARCH_7M__) || defined(__ARM_ARCH_7EM__) || defined(__ARM_ARCH_7S__)

# define BOOST_SP_ARM_BARRIER "dmb"
# define BOOST_SP_ARM_HAS_LDREX

#elif defined(__ARM_ARCH_6__) || defined(__ARM_ARCH_6J__) || defined(__ARM_ARCH_6K__) || defined(__ARM_ARCH_6Z__) || defined(__ARM_ARCH_6ZK__) || defined(__ARM_ARCH_6T2__)

# define BOOST_SP_ARM_BARRIER "mcr p15, 0, r0, c7, c10, 5"
# define BOOST_SP_ARM_HAS_LDREX

#else

# define BOOST_SP_ARM_BARRIER ""

#endif

namespace boost
{

namespace detail
{

class spinlock
{
public:

    int v_;

public:

    bool try_lock()
    {
        int r;

#ifdef BOOST_SP_ARM_HAS_LDREX

        __asm__ __volatile__(
            "ldrex %0, [%2]; \n"
            "cmp %0, %1; \n"
            "strexne %0, %1, [%2]; \n"
            BOOST_SP_ARM_BARRIER :
            "=&r"( r ): // outputs
            "r"( 1 ), "r"( &v_ ): // inputs
            "memory", "cc" );

#else

        __asm__ __volatile__(
            "swp %0, %1, [%2];\n"
            BOOST_SP_ARM_BARRIER :
            "=&r"( r ): // outputs
            "r"( 1 ), "r"( &v_ ): // inputs
            "memory", "cc" );

#endif


        return r == 0;
    }

    void lock()
    {
        for( unsigned k = 0; !try_lock(); ++k )
        {
            boost::detail::yield( k );
        }
    }

    void unlock()
    {
        __asm__ __volatile__( BOOST_SP_ARM_BARRIER ::: "memory" );
        *const_cast< int volatile* >( &v_ ) = 0;
        __asm__ __volatile__( BOOST_SP_ARM_BARRIER ::: "memory" );
    }

public:

    class scoped_lock
    {
    private:

        spinlock & sp_;

        scoped_lock( scoped_lock const & );
        scoped_lock & operator=( scoped_lock const & );

    public:

        explicit scoped_lock( spinlock & sp ): sp_( sp )
        {
            sp.lock();
        }

        ~scoped_lock()
        {
            sp_.unlock();
        }
    };
};

} // namespace detail
} // namespace boost

#define BOOST_DETAIL_SPINLOCK_INIT {0}

#undef BOOST_SP_ARM_BARRIER
#undef BOOST_SP_ARM_HAS_LDREX

#endif // #ifndef BOOST_SMART_PTR_DETAIL_SPINLOCK_GCC_ARM_HPP_INCLUDED
```

#### 选择第一种方式编译：
当我们使用了默认编译器配置进行交叉编译
```
1.arm-linux-gnueabi-g++-4.7 -g main.cpp -o main -Iboost/  -L/opt/build-make-relwithdebinfo/external/boost/libboost.a
```

gdb看到try_lock被编译函数内容为如下：
```c
  __asm__ __volatile__(
      "swp %0, %1, [%2];\n"
      BOOST_SP_ARM_BARRIER :
      "=&r"( r ): // outputs
      "r"( 1 ), "r"( &v_ ): // inputs
      "memory", "cc" );
```

```
Dump of assembler code for function boost::detail::spinlock::try_lock():
   0x000106e4 <+0>:	push	{r4, r11}
   0x000106e8 <+4>:	add	r11, sp, #4
   0x000106ec <+8>:	sub	sp, sp, #16
   0x000106f0 <+12>:	str	r0, [r11, #-16]
=> 0x000106f4 <+16>:	ldr	r2, [r11, #-16]
   0x000106f8 <+20>:	mov	r3, #1
   0x000106fc <+24>:	swp	r4, r3, [r2]
   0x00010700 <+28>:	str	r4, [r11, #-8]
   0x00010704 <+32>:	ldr	r3, [r11, #-8]
   0x00010708 <+36>:	cmp	r3, #0
   0x0001070c <+40>:	movne	r3, #0
   0x00010710 <+44>:	moveq	r3, #1
   0x00010714 <+48>:	and	r3, r3, #255	; 0xff
   0x00010718 <+52>:	mov	r0, r3
   0x0001071c <+56>:	sub	sp, r11, #4
   0x00010720 <+60>:	pop	{r4, r11}
   0x00010724 <+64>:	bx	lr
End of assembler du
```
编译选择的是以下汇编分支，该分支在是在armv5或者更老架构上使用，新的ARM架构官网已经不推荐使用SWP和SWPB指令。所以我们应该根据boost源码spinlock_gcc_arm.hpp文件中选择试用新架构的处理方式。

#### 选择第二种方式编译：
我们指定一些关于处理器信息进行编译
```
2.arm-linux-gnueabi-g++-4.7 -march=armv7-a -mthumb-interwork -mfloat-abi=soft -mfpu=neon -mtune=cortex-a9 -g main.cpp -o main -Iboost/  -L/opt/build-make-relwithdebinfo/external/boost/libboost.a
```
gdb再次看到try_lock被编译函数内容为如下：

```c
  __asm__ __volatile__(
      "ldrex %0, [%2]; \n"
      "cmp %0, %1; \n"
      "strexne %0, %1, [%2]; \n"
      BOOST_SP_ARM_BARRIER :
      "=&r"( r ): // outputs
      "r"( 1 ), "r"( &v_ ): // inputs
      "memory", "cc" );
```

```
Dump of assembler code for function boost::detail::spinlock::try_lock():
   0x000106e4 <+0>:	push	{r4, r11}
   0x000106e8 <+4>:	add	r11, sp, #4
   0x000106ec <+8>:	sub	sp, sp, #16
   0x000106f0 <+12>:	str	r0, [r11, #-16]
=> 0x000106f4 <+16>:	ldr	r2, [r11, #-16]
   0x000106f8 <+20>:	mov	r3, #1
   0x000106fc <+24>:	ldrex	r4, [r2]
   0x00010700 <+28>:	cmp	r4, r3
   0x00010704 <+32>:	strexne	r4, r3, [r2]
   0x00010708 <+36>:	dmb	sy
   0x0001070c <+40>:	str	r4, [r11, #-8]
   0x00010710 <+44>:	ldr	r3, [r11, #-8]
   0x00010714 <+48>:	cmp	r3, #0
   0x00010718 <+52>:	movne	r3, #0
   0x0001071c <+56>:	moveq	r3, #1
   0x00010720 <+60>:	uxtb	r3, r3
   0x00010724 <+64>:	mov	r0, r3
   0x00010728 <+68>:	sub	sp, r11, #4
   0x0001072c <+72>:	pop	{r4, r11}
   0x00010730 <+76>:	bx	lr
End of assembler dump.
```
当指定gcc参数后，编译出来的结果即是我们想要的结果。

#### 总结下：
我们为什么需要选择正确指令集进行交叉编译呢？
在我们使用boost库时候发现在使用默认的比较老的armv5指令集进行编译时候，程序是可以运行，但是当我们在多核ARM中使用spinlock功能时候，如boost库smart_ptr时候，程序会经常coredump，经过coredump分析我们发现问题发生在spinlock问题上，也就是我们使用的是armv7指令集的多核cortex-a9处理器,却使用的是老的指令集进行交叉编译程序，从而boost中自旋锁spinlock被编译为使用老的swp指令进行处理，由于多核原因导致一个cup核解锁后其他的锁在一级缓存中就失效了所以coredump了。

#### 附ARM Options-m参数说明：
"-march=name","-mthumb=name","-mfloat=name","-mfpu=name","-mtune=name"

http://gcc.gnu.org/onlinedocs/gcc/ARM-Options.html#ARM-Options
