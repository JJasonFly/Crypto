话不多说，直接上代码
```
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
//SHA函数的结构体
typedef struct SHA1Context_58
{
	unsigned Message_H_58[5];

	unsigned Length_Low_58;
	unsigned Length_High_58;

	unsigned char Message_Block_58[64];
	int Message_Block_Index_58;

	int Computed;
	int Corrupted;
} SHA1Context_58;

//函数申明
void SHA1Reset_58(SHA1Context_58 *context);//赋初值函数
void SHA1ProcessMessageBlock_58(SHA1Context_58 *context_58);//数据分组、处理函数
void SHA1PadMessage_58(SHA1Context_58 *context);//消息填充函数
void SHA1InputBuffer_58(SHA1Context_58 *context, const unsigned char *message_array, unsigned length);//输入函数
void SHADataExtend_63(unsigned long word_63[80]);

//循环左移bits位
#define SHA1CircularShift_58(bits,word) ((((word) << (bits)) & 0xFFFFFFFF) | ((word) >> (32-(bits))))

void SHA1Reset_58(SHA1Context_58 *context_58)//赋初值
{
	context_58->Length_Low_58 = 0;
	context_58->Length_High_58 = 0;
	context_58->Message_Block_Index_58 = 0;

	context_58->Message_H_58[0] = 0x67452301;
	context_58->Message_H_58[1] = 0xEFCDAB89;
	context_58->Message_H_58[2] = 0x98BADCFE;
	context_58->Message_H_58[3] = 0x10325476;
	context_58->Message_H_58[4] = 0xC3D2E1F0;

	context_58->Computed = 0;
	context_58->Corrupted = 0;
}

void SHA1PadMessage_58(SHA1Context_58 *context_58)//消息填充
{
	if (context_58->Message_Block_Index_58 > 55)
	{
		context_58->Message_Block_58[context_58->Message_Block_Index_58++] = 0x80;
		while (context_58->Message_Block_Index_58 < 64)
		{
			context_58->Message_Block_58[context_58->Message_Block_Index_58++] = 0;
		}

		SHA1ProcessMessageBlock_58(context_58);

		while (context_58->Message_Block_Index_58 < 56)
		{
			context_58->Message_Block_58[context_58->Message_Block_Index_58++] = 0;
		}
	}
	else
	{
		context_58->Message_Block_58[context_58->Message_Block_Index_58++] = 0x80;
		while (context_58->Message_Block_Index_58 < 56)
		{
			context_58->Message_Block_58[context_58->Message_Block_Index_58++] = 0;
		}
	}

	context_58->Message_Block_58[56] = (context_58->Length_High_58 >> 24) & 0xFF;
	context_58->Message_Block_58[57] = (context_58->Length_High_58 >> 16) & 0xFF;
	context_58->Message_Block_58[58] = (context_58->Length_High_58 >> 8) & 0xFF;
	context_58->Message_Block_58[59] = (context_58->Length_High_58) & 0xFF;
	context_58->Message_Block_58[60] = (context_58->Length_Low_58 >> 24) & 0xFF;
	context_58->Message_Block_58[61] = (context_58->Length_Low_58 >> 16) & 0xFF;
	context_58->Message_Block_58[62] = (context_58->Length_Low_58 >> 8) & 0xFF;
	context_58->Message_Block_58[63] = (context_58->Length_Low_58) & 0xFF;

	SHA1ProcessMessageBlock_58(context_58);
}

unsigned SHALogic_58(const unsigned B, const unsigned C, const unsigned D, const unsigned t)//逻辑函数
{
	switch (t / 20)
	{
	case 0: return ((B & C) | ((~B) & D)); break;
	case 2: return ((B & C) | (B & D) | (C & D)); break;
	case 1:
	case 3: return (B ^ C ^ D); break;
	}
}

void SHA1ProcessMessageBlock_58(SHA1Context_58 *context_58)//数据拓展、轮运算、压缩
{
	const unsigned K[] =
	{
		0x5A827999,
		0x6ED9EBA1,
		0x8F1BBCDC,
		0xCA62C1D6
	};
	int t;
	unsigned temp;
	unsigned W[80];
	unsigned A, B, C, D, E;

	//数据拓展
	for (t = 0; t < 16; t++)
	{
		W[t] = ((unsigned)context_58->Message_Block_58[t * 4]) << 24;
		W[t] |= ((unsigned)context_58->Message_Block_58[t * 4 + 1]) << 16;
		W[t] |= ((unsigned)context_58->Message_Block_58[t * 4 + 2]) << 8;
		W[t] |= ((unsigned)context_58->Message_Block_58[t * 4 + 3]);
	}

	for (t = 16; t < 80; t++)
	{
		W[t] = SHA1CircularShift_58(1, W[t - 3] ^ W[t - 8] ^ W[t - 14] ^ W[t - 16]);
	}

	//初始化变量
	A = context_58->Message_H_58[0];
	B = context_58->Message_H_58[1];
	C = context_58->Message_H_58[2];
	D = context_58->Message_H_58[3];
	E = context_58->Message_H_58[4];

	printf("W[0] = %08lx   ", W[0]);
	printf("W[1] = %08lx   ", W[1]);
	printf("W[14] = %08lx   ", W[14]);
	printf("W[15] = %08lx   ", W[15]);
	printf("W[16] = %08lx  ", W[16]);
	printf("W[79] = %08lx\n", W[79]);
	
	for (t = 0; t < 80; t++)//压缩函数
	{
		temp = SHA1CircularShift_58(5, A) + SHALogic_58(B, C, D, t) + E + W[t] + K[t / 20];
		temp &= 0xFFFFFFFF;
		E = D;
		D = C;
		C = SHA1CircularShift_58(30, B);
		B = A;
		A = temp;
	}
	

	context_58->Message_H_58[0] = (context_58->Message_H_58[0] + A) & 0xFFFFFFFF;
	context_58->Message_H_58[1] = (context_58->Message_H_58[1] + B) & 0xFFFFFFFF;
	context_58->Message_H_58[2] = (context_58->Message_H_58[2] + C) & 0xFFFFFFFF;
	context_58->Message_H_58[3] = (context_58->Message_H_58[3] + D) & 0xFFFFFFFF;
	context_58->Message_H_58[4] = (context_58->Message_H_58[4] + E) & 0xFFFFFFFF;
	context_58->Message_Block_Index_58 = 0;
}




int SHA1Result_58(SHA1Context_58 *context)
{

	if (context->Corrupted)
	{
		return 0;
	}

	if (!context->Computed)
	{
		SHA1PadMessage_58(context);
		context->Computed = 1;
	}

	return 1;
}

void SHA1InputBuffer_58(SHA1Context_58 *context_58, const unsigned char *message_array_58, unsigned length_58)
{
	if (!length_58)
	{
		return 0;
	}

	if (context_58->Computed || context_58->Corrupted)
	{
		context_58->Corrupted = 1;
		return 0;
	}

	while (length_58-- && !context_58->Corrupted)
	{
		context_58->Message_Block_58[context_58->Message_Block_Index_58++] = (*message_array_58 & 0xFF);
		context_58->Length_Low_58 += 8;
		context_58->Length_Low_58 &= 0xFFFFFFFF;
		if (context_58->Length_Low_58 == 0)
		{
			context_58->Length_High_58++;
			context_58->Length_High_58 &= 0xFFFFFFFF;
			if (context_58->Length_High_58 == 0)
			{
				context_58->Corrupted = 1;
			}
		}

		if (context_58->Message_Block_Index_58 == 64)
		{
			SHA1ProcessMessageBlock_58(context_58);
		}

		message_array_58++;
	}
}


int main()
{
	SHA1Context_58 sha;
	int i;
	unsigned char input[64];

	printf("ASCII string：");
	scanf("%s", input);
	printf("\n");

	SHA1Reset_58(&sha);
	SHA1InputBuffer_58(&sha, (const unsigned char *)input, strlen(input));

	if (!SHA1Result_58(&sha))
	{
		fprintf(stderr, "ERROR-- could not compute message digest\n");
	}
	else
	{
		printf("\n\n\nThe resulting 160-bit message digest is：\n\n");
		for (i = 0; i < 5; i++)
		{
			printf("%x ", sha.Message_H_58[i]);
		}
		printf("\n\n");
	}
	system("pause");
	return 0;
}
```
