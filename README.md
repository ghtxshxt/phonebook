#include "stdio.h" 
#include <malloc.h> 
#include <string.h>

typedef struct
{
	char* name;
	int sex;
	int age;
	char phone[15];
} Record;

void PrintRecord(const Record*);
void PrintFileRecord(FILE*, const Record*);
int ScanFileRecord(FILE*, Record*);
void ReplaceStr(const char* source, char* destination, const char oldChar, const char newChar);

int main()
{

	Record item;
	char* myName = "Stephen Hawking";
	char* myPhone = "+7(878)7776666";

	item.name = (char*)malloc((strlen(myName) + 1) * sizeof(char));

	if (item.name == NULL) return -1;

	strcpy(item.name, myName);
	strcpy(item.phone, myPhone);

	item.sex = 1;
	item.age = 72;

	FILE* pFile;
	pFile = fopen("myfile.txt", "a");
	remove("myfile.txt");
	if (pFile != NULL)
	{
		PrintFileRecord(pFile, &item);
		fclose(pFile);
	} 

	pFile = fopen("myfile.txt", "r");
	if (pFile != NULL)
	{
		while (ScanFileRecord(pFile, &item) != EOF)
		{

			PrintRecord(&item);
		}
		fclose(pFile);
	}

	getchar();

	free(item.name);

	return 0;
}

void PrintRecord(const Record *itemPrint)
{

	printf("%s\n %d\n %d\n %s\n", itemPrint->name, itemPrint->age, itemPrint->sex, itemPrint->phone);

}

void PrintFileRecord(FILE* pFile, const Record* itemPrint)
{

	char* name = (char*)malloc((strlen(itemPrint->name) + 1) * sizeof(char));
	char* phone = (char*)malloc((strlen(itemPrint->phone) + 1) * sizeof(char));

	ReplaceStr(itemPrint->name, name, ' ', '_');
	ReplaceStr(itemPrint->phone, phone, ' ', '_');

	fprintf(pFile, "%s %d %d %s\n", name, itemPrint->age, itemPrint->sex, phone);

	free(name);
	free(phone);
}

int ScanFileRecord(FILE* pFile, Record* itemScan)
{

	int result = fscanf(pFile, "%s %d %d %s\n", itemScan->name, &itemScan->age, &itemScan->sex, itemScan->phone);

	if (4 == result)
	{
		ReplaceStr(itemScan->name, itemScan->name, '_', ' ');
		ReplaceStr(itemScan->phone, itemScan->phone, '_', ' ');
	}

	return result;
}

void ReplaceStr(const char* source, char* destination, const char oldChar, const char newChar)
{

	while (0 != *source)
	{
		if (oldChar == *source)
			*destination = newChar;
		else
			*destination = *source;

		source++;
		destination++;
	}

	*destination = 0;
}

