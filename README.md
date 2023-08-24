#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#define SHORT_URL_LENGTH 7
#define HASH_TABLE_SIZE 100

struct URLNode {
    char shortUrl[SHORT_URL_LENGTH + 1];
    char longUrl[200];
    struct URLNode* next;
};

struct URLNode* hashTable[HASH_TABLE_SIZE] = {NULL};

unsigned int hash(char* str) {
    unsigned int hash = 0;
    for (int i = 0; str[i]; i++) {
        hash = hash * 31 + str[i];
    }
    return hash % HASH_TABLE_SIZE;
}

void generateShortURL(char* shortUrl) {
    const char charset[] = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    for (int i = 0; i < SHORT_URL_LENGTH; i++) {
        shortUrl[i] = charset[rand() % (sizeof(charset) - 1)];
    }
    shortUrl[SHORT_URL_LENGTH] = '\0';
}

void shortenURL(char* longUrl) {
    struct URLNode* newNode = (struct URLNode*)malloc(sizeof(struct URLNode));
    if (!newNode) {
        printf("Memory allocation error.\n");
        return;
    }

    generateShortURL(newNode->shortUrl);
    strcpy(newNode->longUrl, longUrl);
    newNode->next = NULL;

    unsigned int index = hash(newNode->shortUrl);

    if (!hashTable[index]) {
        hashTable[index] = newNode;
    } else {
        struct URLNode* current = hashTable[index];
        while (current->next) {
            current = current->next;
        }
        current->next = newNode;
    }

    printf("Short URL: %s\n", newNode->shortUrl);
}

void expandURL(char* shortUrl) {
    unsigned int index = hash(shortUrl);

    struct URLNode* current = hashTable[index];
    while (current) {
        if (strcmp(current->shortUrl, shortUrl) == 0) {
            printf("Expanded URL: %s\n", current->longUrl);
            return;
        }
        current = current->next;
    }

    printf("Short URL not found.\n");
}

int main() {
    srand(time(NULL));

    char longUrl[200];
    printf("Enter a long URL: ");
    scanf("%s", longUrl);

    shortenURL(longUrl);

    char shortUrl[SHORT_URL_LENGTH + 1];
    printf("Enter a short URL to expand: ");
    scanf("%s", shortUrl);

    expandURL(shortUrl);

    return 0;
}
