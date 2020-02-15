> ### Atoi

- 为了方便, 我在调用的时候封装一层
```
func CommonAtoi(s string) (i int) {
   if i, err := strconv.Atoi(s); err == nil {
      return i
   } else {
      return 0
   }
}
```

- 源码
```
// Atoi returns the result of ParseInt(s, 10, 0) converted to type int.
func Atoi(s string) (int, error) {
   const fnAtoi = "Atoi"

   sLen := len(s)
   if intSize == 32 && (0 < sLen && sLen < 10) ||
      intSize == 64 && (0 < sLen && sLen < 19) {
      // Fast path for small integers that fit int type.
      s0 := s
      //截取第一个符号位后的字符
      if s[0] == '-' || s[0] == '+' {
         s = s[1:]
         if len(s) < 1 {
            return 0, &NumError{fnAtoi, s0, ErrSyntax}
         }
      }

      n := 0
      for _, ch := range []byte(s) {
         //减去字符0的编码
         ch -= '0'
         if ch > 9 {
            return 0, &NumError{fnAtoi, s0, ErrSyntax}
         }
         n = n*10 + int(ch)
      }
      if s0[0] == '-' {
         n = -n
      }
      return n, nil
   }

   // Slow path for invalid or big integers.
   i64, err := ParseInt(s, 10, 0)
   if nerr, ok := err.(*NumError); ok {
      nerr.Func = fnAtoi
   }
   return int(i64), err
}
```

>### Itoa

```txt
func Itoa(i int) string {
   return FormatInt(int64(i), 10)
}
```
- 源码
```
func Itoa(i int) string {
   return FormatInt(int64(i), 10)
}

func FormatInt(i int64, base int) string {
   //0~99 采用字符串截取
   if fastSmalls && 0 <= i && i < nSmalls && base == 10 {
      return small(int(i))
   }

   _, s := formatBits(nil, uint64(i), base, i < 0, false)
   return s
}

us := uint(u)
for us >= 100 {
   //取出最后两位数, *2是因为smallsString用两位数表示0~99
   is := us % 100 * 2
   us /= 100
   i -= 2
   a[i+1] = smallsString[is+1]
   a[i+0] = smallsString[is+0]
}

//处理前面剩余的一位或两位数
// us < 100
is := us * 2
i--
a[i] = smallsString[is+1]
if us >= 10 {
   i--
   a[i] = smallsString[is]
}

```
