---
layout: algoritmo
title: Huffman
date: 2019-05-29 00:29:13
tags:Algoritmo, huffman, compressao, C
---
Algorítmo de huffman em C, algorítmo de compressão com árvores binárias.

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
typedef struct no_arvore {
	struct no_arvore *esquerda, *direita;
	int frequencia;
	char c;
} *no;
 
struct no_arvore pool[256] = {{0}};
no qqq[255], *q = qqq - 1;
int numero_nos = 0, qend = 1;
char *code[128] = {0}, buf[1024];
 
no novo_no(int frequencia, char c, no a, no b)
{
	no n = pool + numero_nos++;
	if (frequencia) n->c = c, n->frequencia = frequencia;
	else {
		n->esquerda = a, n->direita = b;
		n->frequencia = a->frequencia + b->frequencia;
	}
	return n;
}
 
void qinsert(no n)
{
	int j, i = qend++;
	while ((j = i / 2)) {
		if (q[j]->frequencia <= n->frequencia) break;
		q[i] = q[j], i = j;
	}
	q[i] = n;
}
 
no qremove()
{
	int i, l;
	no n = q[i = 1];
 
	if (qend < 2) return 0;
	qend--;
	while ((l = i * 2) < qend) {
		if (l + 1 < qend && q[l + 1]->frequencia < q[l]->frequencia) l++;
		q[i] = q[l], i = l;
	}
	q[i] = q[qend];
	return n;
}
 
void constroi_codigo(no n, char *s, int len)
{
	static char *out = buf;
	if (n->c) {
		s[len] = 0;
		strcpy(out, s);
		code[n->c] = out;
		out += len + 1;
		return;
	}
 
	s[len] = '0'; constroi_codigo(n->esquerda,  s, len + 1);
	s[len] = '1'; constroi_codigo(n->direita, s, len + 1);
}
 
void init(const char *s)
{
	int i, frequencia[128] = {0};
	char c[16];
 
	while (*s) frequencia[(int)*s++]++;
 
	for (i = 0; i < 128; i++)
		if (frequencia[i]) qinsert(novo_no(frequencia[i], i, 0, 0));
 
	while (qend > 2) 
		qinsert(novo_no(0, 0, qremove(), qremove()));
 
	constroi_codigo(q[1], c, 0);
}
 
void codifica(const char *s, char *out)
{
	while (*s) {
		strcpy(out, code[*s]);
		out += strlen(code[*s++]);
	}
}
 
void decocodifica(const char *s, no t)
{
	no n = t;
	while (*s) {
		if (*s++ == '0') n = n->esquerda;
		else n = n->direita;
 
		if (n->c) putchar(n->c), n = t;
	}
 
	putchar('\n');
	if (t != n) printf("garbage input\n");
}
 
int main(void)
{
	  FILE* arquivo = fopen("arquivotexto.txt", "r");
	  FILE* arquivo2 = fopen("arquivocomprimido.txt", "w");
	  FILE* arquivo3 = fopen("arquivobinario.txt", "w");
    if(!arquivo){
        perror("Erro: ");
        return 1;
    }  
    char Linha[150];
    
	int i;
	const char *str = fgets(Linha, 150, arquivo);//"Nardier";

        char buf[1024];
 
	init(str);
	for (i = 0; i < 128; i++)
		if (code[i]) printf("'%c': %s\n", i, code[i]);
 
	codifica(str, buf);
	printf("codificado: %s\n", buf);
	fprintf(arquivo2, buf);
 
	printf("decodificado: ");
	decocodifica(buf, q[1]);
	printf("convertido para binario: ");
	char texto[150];
	char resultado[150]; 
	int numero, tam = strlen(str), cont = 0; 
	while(cont < tam) 
	{ 
	numero = (int) str[cont]; 
	itoa(numero,resultado,2); 
	printf("%s ",resultado); 
	cont++; 
	fprintf(arquivo3, resultado);
	} 
 
	return 0;
	fclose(arquivo);
	fclose(arquivo2);
	fclose(arquivo3);
}
```