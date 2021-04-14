```c
//在windows下测试成功
#include <stdio.h>
#include<stdlib.h>
int main(void)
{
    char buf[100];
    FILE *fp;
    if((fp=fopen("com3","r"))==NULL) //指定串口名
        puts("this way doesn't work!/n");
    else
        puts("thiw way works!/n");


    while(1){
        memset(buf,0,sizeof(buf));
        if( fgets(buf,100,fp) != NULL )
        printf("%s\n",buf);
    }

    fclose(fp);
    return 0;
}

```

