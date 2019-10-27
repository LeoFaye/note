# equals 和 hashcode
equals相等，hashcode一定相等，反过来，hashcode相等，equals不一定相等。  
hashSet, hashMap等判断的时候先比较hashcode是否相等，能够快速排除不相等的，如果hashcode相等，进一步通过equals来判断。