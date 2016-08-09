# Anonymization (by Roland van Rijswijk)

This is a copy-and-paste of Roland´s code to anonymize IP addresse.sdon tThe only thing that you MUST TO do is to change the string "SALT GOES HERE" for any string that you want. Then, the IP addresses in your data will be anonymized as a 256-bit (32-byte) hash value (i.e., SHA-256).

```
#include <stdio.h>
#include <string.h>
#include <openssl/evp.h>

int main(int argc, char* argv[])
{
	FILE* f = fopen(argv[1], "r");
	char buf[4096];
	char* ptr = NULL;
	char* str = NULL;

	char salt[] = "SALT GOES HERE";

	unsigned char digest[32] = { 0 };

	int i = 0;
	unsigned int len = 32;

	while (fgets(buf, 4096, f) != NULL)
	{
		int tokcount = 0;

		ptr = &buf[0];

		do
		{
			str = strsep(&ptr, ",");

			if (str != NULL)
			{
				tokcount++;

				if (tokcount == 3) /* This is the IP */
				{
					EVP_MD_CTX ctx;

					printf(" ");

					EVP_DigestInit(&ctx, EVP_sha256());
					EVP_DigestUpdate(&ctx, salt, sizeof(salt));
					EVP_DigestUpdate(&ctx, str, strlen(str));
					EVP_DigestFinal(&ctx, digest, &len);

					for (i = 0; i < len; i++)
					{
						printf("%02x", digest[i]);
					}
					printf(",");
				}
				else
				{
					if (strchr(str, '\n') != NULL) printf("%s", str); 
					else printf("%s,", str);
				}
			}
		}
		while (str != NULL);

		printf("\n");
	}

	return 0;
}
```
