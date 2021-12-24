# HDFC
## How to Deal with Files in C basically
### C에서 파일을 다루는 기초적인 방법

---

#### *사용된 표준 라이브러리*
```CPP
#include <stdio.h>
```

---

#### *추가 표현*
```
EOF : 파일의 끝 (-1)
텍스트 파일(Text File) : 문자로 이루어진 파일
이진 파일(Binary File) : 데이터로 이루어진 파일
CR(Carriage Return) : 캐럿(Caret)이 그 라인의 맨 앞줄로 이동 (\r)
LF(Line feed) : 캐럿(Caret)이 다음 줄로 이동 (\n)
파일 위치 지시자 : FILE 구조체 멤버 중 파일의 위치를 저장하는 멤버가 존재하는데, 해당 값은 입출력에 의해 갱신 후 유지
```

---

# 1. 파일 불러오기

> ## 1-1. fopen

스트림 형성을 위힌 파일과 스트림 방식에 따른 정보를 FILE 구조체에 담아 해당 주소를 반환한다.
```CPP
FILE * fopen(cosnt  char* filename, cosnt char* mode);
    -> 관련 파일의 FILE 구조체의 주소 반환 (예외 시, NULL 반환)
```

>> ### 1-1-1. (const char*)mode
```
 1. "r"/"rb" : 읽기 (불러올 파일이 없을 시, 에러)
 2. "w"/"wb" : 쓰기 (불러올 파일이 없을 시, 자동 생성)
 3. "a"/"ab" : 이어 쓰기 (불러올 파일이 없을 시, 자동 생성)

 4. "r+"/"rb+"/"r+b" :  읽기/쓰기 (불러올 파일이 없을 시, 에러)
 5. "w+"/"wb+"/"w+b" : 읽기/쓰기 (불러올 파일이 없을 시, 자동 생성)
 6. "a+"/"ab+"/"a+b" : 읽기/이어 쓰기 (불러올 파일이 없을 시, 자동 생성)
 
 ※"b"는 바이너리 파일을 의미한다.
```
 
> ## 1-2. fclose

호출한 파일을 닫아준다.
```CPP
int fclose(FILE* stream);
    -> 0 반환 (예외 시, EOF 반환)
```

>> ### 1-2-1. 이유
 - 운영체제가 할당한 자원의 반환
    - 스트림을 통한 자원 낭비를 방지

 - 버퍼링 되었던 데이터의 출력
    - 버퍼로 인해 데이터를 모두 파일에 저장하지 못한 경우 프로그램의 종료로 인한 데이터 손실을 방지

---

# 2. 입출력

> ## 2-1. 입력

>> ### 2-1-1. 텍스트 파일(Text File)

>>> #### 2-1-1-1. fgetc

파일에서 문자를 읽어온다.
```CPP
int fgetc(FILE* stream);
    -> 읽은 값을 아스키코드로 반환

※int getc(FILE* stream); : fgetc를 래핑한 매크로 함수
```

>>> #### 2-1-1-2. fgets

파일에서 문자열을 읽어온다.
```CPP
char* fgets(char* s, int n, FILE* stream);
    -> 문자열 주소 반환 (예외 또는 파일의 끝에 도달 시, NULL 반환)
```

>> ### 2-1-2. 이진 파일(Binary File)

>>> #### 2-1-2-1. fread

파일에서 데이터를 읽어온다. (size 크기만큼 count 개수만큼 버퍼에 저장하여 가져옴)
```CPP
size_t fread(void* buffer, size_t size, size_t count, FILE* stream);
    -> count 반환 (예외 또는 파일의 끝에 도달 시, count 이하 값 반환)
```

>> ### 2-1-3. 포맷에 따른 문자열

>>> #### 2-1-3-1. fscanf

포맷에 따른 문자열을 읽어온다.
```CPP
int fscanf(FILE* stream, const char* s, ... );
    -> 입력받은 수 반환 (예외 또는 파일의 끝에 도달 시, EOF 반환)
```

---

> ## 2-2. 출력(쓰기)

>> ### 2-2-1. 텍스트 파일(Text File)

>>> #### 2-2-1-1. fputc

파일에 문자를 쓴다.
```CPP
int fputc(int c, FILE* stream);
    -> 쓴 값을 아스키코드로 반환 (예외 시, EOF 반환)

※int putc(int c, FILE* stream); : fputc를 래핑한 매크로 함수
```

>>> #### 2-2-1-2. fputs

파일에 문자열을 쓴다.
```CPP
char* fputs(const char* s, FILE* stream);
    -> 문자열 길이(0 이상 값) 반환 (예외 시, 0 미만 값 반환)
```

>> ### 2-2-2. 이진 파일(Binary File)

>>> #### 2-2-2-1. fwrite

파일에 데이터를 쓴다. (size 크기만큼 count 개수만큼 버퍼에서 가져와 저장)
```CPP
size_t fwrite(const void* buffer, size_t size, size_t count, FILE* stream);
    -> count 반환 (예외 또는 파일의 끝에 도달 시, count 이하 값 반환)
```

>> ### 2-2-3. 포맷에 따른 문자열

>>> #### 2-2-3-1. fprintf

파일에 포맷에 따른 문자열을 쓴다. (동시에 출력)
```CPP
int fprintf(FILE* stream, const char* s, ... );
    -> 문자 수 반환 (예외 또는 파일의 끝에 도달 시, EOF 반환)
```

---

# 3. (파일 스트림)버퍼 비우기

> ## 3-1. fflush

출력 버퍼를 비운다. (파일로부터 읽힌 데이터는 바로 지워지기 때문에 입력 버퍼는 따로 비워줄 필요가 없다)

※출력 버퍼를 비운 다는 것은 저장된 데이터를 목적지로 전송하는 것이다.

```CPP
int fflush(FILE* stream);
    -> 0 반환 (예외 시, EOF 반환)
```

---

# 4. 기타

> ## 4-1. feof

파일의 끝임을 확인한다.
```CPP
int feof(FILE* stream);
    -> 파일의 끝인 경우, 0이 아닌 값 반환 (예외 시, 0 반환)
```

> ## 4-2. 파일 위치 지시자

>> ### 4-2-1. 이동

>>> #### 4-2-1-1. fseek

파일 위치 지시자를 이동시킨다. (offset만큼 오른쪽으로 이동 (음수 : 왼쪽으로 이동))
```CPP
int fseek(FILE* stream, long offset, int wherefrom);
    -> 0 반환 (예외 시, 0)
```

>>>> ##### 4-2-1-1-1. (int)wherefrom
```CPP
 1. SEEK_SET : 파일의 앞 (0)
 2. SEEK_CUR : 현재 파일 위치 지시자 (1)
 3. SEEK_END : 파일의 끝 (2)

 ※이는 매크로 상수이며 이동 시작 위치를 의미한다.
```

>> ### 4-2-2. 출력

>>> #### 4-2-2-1. ftell

파일 위치 지시자의 위치 정보를 반환한다.
```CPP
long ftell(FILE* stream);
    -> 파일 위치 지시자 위치 정보 반환
```

---

# *부가 설명*

> ## 1. OS별 개행문자
```
1. MS-Dos(Windows) : \r\n
2. Mac(~버전9) : \r
3. Unix/Linux/Mac(버전10~) : \n

※C에서는 \n으로 변환한다.
```

> ## 2. 표준 스트림(Standard Stream) 
```
1. (FILE*)stdin : 표준 입력 스트림
2. (FILE*)stdout : 표준 출력 스트림
3. (FILE*)stderr : 표준 에러 스트림

※UNIX에서는 일반 출력과 에러를 구분한다.
```
<img src="https://github.com/SWK-0507/Dealing_FILE_in_C/blob/main/unix_IO_rediretion.jpeg?raw=true" width="100%" height="100%" title="UNIX IO Redirection" alt="UNIX IO Redirection"></img>
---
