6-RECURSIVE DESCENT PARSING----------------------------------------------------------
#include <stdio.h>
#include <conio.h>
#include <string.h>

int i = 0, f = 0;
char str[30];

void E();
void Eprime();
void T();
void Tprime();
void F();

void E() {
    printf("\nE -> T E'");
    T();
    Eprime();
}

void Eprime() {
    if (str[i] == '+') {
        printf("\nE' -> + T E'");
        i++;
        T();
        Eprime();
    } else if (str[i] == ')' || str[i] == '$') {
        printf("\nE' -> ^");
    }
}

void T() {
    printf("\nT -> F T'");
    F();
    Tprime();
}

void Tprime() {
    if (str[i] == '*') {
        printf("\nT' -> * F T'");
        i++;
        F();
        Tprime();
    } else if (str[i] == ')' || str[i] == '+' || str[i] == '$') {
        printf("\nT' -> ^");
    }
}

void F() {
    if (str[i] == 'a') {
        printf("\nF -> a");
        i++;
    } else if (str[i] == '(') {
        printf("\nF -> (E)");
        i++;
        E();
        if (str[i] == ')') {
            i++;
        } else {
            f = 1;
        }
    } else {
        f = 1;
    }
}

void main() {
    int len;
    clrscr(); // works in Turbo C
    printf("Enter the string: ");
    scanf("%s", str);
    len = strlen(str);
    str[len] = '$';
    str[len + 1] = '\0'; // null-terminate the string

    E();

    if (str[i] == '$' && f == 0) {
        printf("\nString successfully parsed!");
    } else {
        printf("\nSyntax Error!");
    }

    getch(); // holds the screen
}
-----------------------------7 SHIFT REDUCE PARSER-----------------------------
#include <stdio.h>
#include <conio.h>
#include <string.h>

int z, i, j, c;
char a[20], stk[20];

void reduce();

void main() {
    clrscr(); // Clears the screen (Turbo C specific)

    printf("GRAMMAR is:\n");
    printf("E -> E + E\nE -> E * E\nE -> (E)\nE -> a\n");

    printf("Enter input string: ");
    gets(a); // Not safe in modern C, but okay for Turbo C

    c = strlen(a);
    a[c] = '$'; // End marker
    a[c + 1] = '\0';

    stk[0] = '$';
    stk[1] = '\0';

    printf("\nStack\t\tInput\t\tAction");

    for (i = 1, j = 0; j < c; i++, j++) {
        stk[i] = a[j];
        stk[i + 1] = '\0';
        a[j] = ' ';

        printf("\n%s\t\t%s\tshift->%c", stk, a, stk[i]);
        reduce();
    }

    // Final reduction after all shifts
    if (a[j] == '$')
        reduce();

    // Final acceptance check
    if (stk[1] == 'E' && stk[2] == '\0')
        printf("\n%s\t\t%s\tAccept", stk, a);
    else
        printf("\n%s\t\t%s\tError", stk, a);

    getch(); // Hold screen
}

void reduce() {
    // Reduce by E -> a
    for (z = 0; z < strlen(stk); z++) {
        if (stk[z] == 'a') {
            stk[z] = 'E';
            stk[z + 1] = '\0';
            printf("\n%s\t\t%s\tReduce by E -> a", stk, a);
        }
    }

    // Reduce by E -> E + E
    for (z = 0; z < strlen(stk) - 2; z++) {
        if (stk[z] == 'E' && stk[z + 1] == '+' && stk[z + 2] == 'E') {
            stk[z] = 'E';
            stk[z + 1] = '\0';
            stk[z + 2] = '\0';
            i = z + 1;
            printf("\n%s\t\t%s\tReduce by E -> E + E", stk, a);
        }
    }

    // Reduce by E -> E * E
    for (z = 0; z < strlen(stk) - 2; z++) {
        if (stk[z] == 'E' && stk[z + 1] == '*' && stk[z + 2] == 'E') {
            stk[z] = 'E';
            stk[z + 1] = '\0';
            stk[z + 2] = '\0';
            i = z + 1;
            printf("\n%s\t\t%s\tReduce by E -> E * E", stk, a);
        }
    }

    // Reduce by E -> (E)
    for (z = 0; z < strlen(stk) - 2; z++) {
        if (stk[z] == '(' && stk[z + 1] == 'E' && stk[z + 2] == ')') {
            stk[z] = 'E';
            stk[z + 1] = '\0';
            stk[z + 2] = '\0';
            i = z + 1;
            printf("\n%s\t\t%s\tReduce by E -> (E)", stk, a);
        }
    }
}
----------------8 Implement Operator Precedence Parser algorithm. -------------------------------------
#include <stdio.h>
#include <conio.h>
#include <string.h>

void main() {
    char stack[50], ip[50], opt[10][10][1], ter[10];
    int i, j, k, n, top = 0, col = 0, row = 0;
    clrscr();

    // Initialize stack and input
    for (i = 0; i < 50; i++) {
        stack[i] = '\0';
        ip[i] = '\0';
    }

    // Initialize precedence table
    for (i = 0; i < 10; i++) {
        for (j = 0; j < 10; j++) {
            opt[i][j][0] = '\0';
        }
    }

    // Read terminals
    printf("Enter the number of terminals: ");
    scanf("%d", &n);
    printf("Enter the terminals (single string, no spaces): ");
    scanf("%s", ter);

    // Read precedence table
    printf("\nEnter the operator precedence table values (<, >, =):\n");
    for (i = 0; i < n; i++) {
        for (j = 0; j < n; j++) {
            printf("Enter value for %c %c: ", ter[i], ter[j]);
            scanf(" %s", opt[i][j]);
        }
    }

    // Display the precedence table
    printf("\nOPERATOR PRECEDENCE TABLE:\n\t");
    for (i = 0; i < n; i++) {
        printf("%c\t", ter[i]);
    }
    for (i = 0; i < n; i++) {
        printf("\n%c\t", ter[i]);
        for (j = 0; j < n; j++) {
            printf("%c\t", opt[i][j][0]);
        }
    }

    // Input string
    printf("\n\nEnter the input string (end with $): ");
    scanf("%s", ip);

    // Begin parsing
    stack[top] = '$';
    i = 0;
    printf("\n\nSTACK\t\tINPUT\t\tACTION");

    while (i <= strlen(ip)) {
        // Find row and column
        for (k = 0; k < n; k++) {
            if (stack[top] == ter[k]) row = k;
            if (ip[i] == ter[k]) col = k;
        }

        printf("\n");
        for (k = 0; k <= top; k++) printf("%c", stack[k]);
        printf("\t\t");
        for (k = i; k < strlen(ip); k++) printf("%c", ip[k]);
        printf("\t\t");

        // Accept condition
        if (stack[top] == '$' && ip[i] == '$') {
            printf("Accept");
            break;
        }

        // Shift condition
        if (opt[row][col][0] == '<' || opt[row][col][0] == '=') {
            stack[++top] = opt[row][col][0];
            stack[++top] = ip[i];
            printf("Shift %c", ip[i]);
            i++;
        }
        // Reduce condition
        else if (opt[row][col][0] == '>') {
            while (stack[top] != '<' && top > 0) {
                top--;
            }
            if (stack[top] == '<') top--;
            printf("Reduce");
        }
        // Invalid condition
        else {
            printf("Reject");
            break;
        }
    }

    getch();
}
-------------------Exno 8 Implement the backend of the compiler to produce three address code generation------------------------------
#include <stdio.h>
#include <conio.h>
#include <stdlib.h>
#include <string.h>

struct three {
    char data[10], temp[7];
}s[30];

void main() {
    char d1[7], d2[7] = "t";
    int i = 0, j = 1, len = 0;
    FILE *f1, *f2;
    clrscr();

    // Open input and output files
    f1 = fopen("sum.txt", "r");
    f2 = fopen("out.txt", "w");

    // Check if file exists
    if (f1 == NULL) {
        printf("Input file not found!");
        getch();
        exit(0);
    }

    // Read tokens from input file
    while (fscanf(f1, "%s", s[len].data) != EOF)
        len++;

    // Generate first temp variable t1
    itoa(j, d1, 10);
    strcat(d2, d1);
    strcpy(s[j].temp, d2);
    strcpy(d1, "");
    strcpy(d2, "t");

    // Handle the first operator
    if (!strcmp(s[3].data, "+")) {
        fprintf(f2, "%s = %s + %s", s[j].temp, s[i + 2].data, s[i + 4].data);
        j++;
    } else if (!strcmp(s[3].data, "-")) {
        fprintf(f2, "%s = %s - %s", s[j].temp, s[i + 2].data, s[i + 4].data);
        j++;
    }

    // Loop through remaining operators
    for (i = 4; i < len - 2; i += 2) {
        itoa(j, d1, 10);
        strcat(d2, d1);
        strcpy(s[j].temp, d2);

        if (!strcmp(s[i + 1].data, "+"))
            fprintf(f2, "\n%s = %s + %s", s[j].temp, s[j - 1].temp, s[i + 2].data);
        else if (!strcmp(s[i + 1].data, "-"))
            fprintf(f2, "\n%s = %s - %s", s[j].temp, s[j - 1].temp, s[i + 2].data);

        strcpy(d1, "");
        strcpy(d2, "t");
        j++;
    }

    // Final assignment
    fprintf(f2, "\n%s = %s", s[0].data, s[j - 1].temp);

    fclose(f1);
    fclose(f2);
    printf("Three Address Code generated in out.txt");
    getch();
}
-----------------------9  Symbol Table---------------------------------------------------
#include <stdio.h>
#include <conio.h>
#include <ctype.h>
#include <stdlib.h>

void main()
{
    int i = 0, j = 0, x = 0, n;
    void *p, *add[15]; // Increased array size for safety
    char ch, srch, b[15], d[15], c;

    clrscr();
    printf("Enter Expression terminated by $: ");

    while ((c = getchar()) != '$')
    {
        b[i] = c;
        i++;
    }
    n = i - 1;

    printf("\nGiven Expression: ");
    for (i = 0; i <= n; i++)
    {
        printf("%c", b[i]);
    }

    printf("\n\nSymbol Table\n");
    printf("Symbol \t Address \t Type");

    j = 0;
    while (j <= n)
    {
        c = b[j];
        if (isalpha(c))
        {
            p = malloc(sizeof(char));
            add[x] = p;
            d[x] = c;
            printf("\n  %c \t %u \t Identifier", c, (unsigned int)p);
            x++;
        }
        else
        {
            ch = c;
            if (ch == '+' || ch == '-' || ch == '*' || ch == '=')
            {
                p = malloc(sizeof(char));
                add[x] = p;
                d[x] = ch;
                printf("\n  %c \t %u \t Operator", ch, (unsigned int)p);
                x++;
            }
        }
        j++;
    }

    getch();
}
-------------------- Implementation of code optimization Techniques------------------------------------------------
#include <stdio.h>
#include <string.h>

struct op {
    char l;
    char r[20];
} op[10], pr[10];

void main() {
    int a, i, k, j, n, z = 0, m, q;
    char *p, *l;
    char temp, t;
    char *tem;

    printf("Enter the Number of Values: ");
    scanf("%d", &n);

    for (i = 0; i < n; i++) {
        printf("left: ");
        scanf(" %c", &op[i].l);
        printf("right: ");
        scanf(" %s", op[i].r);
    }

    printf("\nIntermediate Code:\n");
    for (i = 0; i < n; i++) {
        printf("%c = %s\n", op[i].l, op[i].r);
    }

    // Dead Code Elimination
    for (i = 0; i < n - 1; i++) {
        temp = op[i].l;
        for (j = 0; j < n; j++) {
            p = strchr(op[j].r, temp);
            if (p) {
                pr[z].l = op[i].l;
                strcpy(pr[z].r, op[i].r);
                z++;
                break;
            }
        }
    }
    pr[z].l = op[n - 1].l;
    strcpy(pr[z].r, op[n - 1].r);
    z++;

    printf("\nAfter Dead Code Elimination:\n");
    for (k = 0; k < z; k++) {
        printf("%c = %s\n", pr[k].l, pr[k].r);
    }

    // Common Expression Substitution
    for (m = 0; m < z; m++) {
        tem = pr[m].r;
        for (j = m + 1; j < z; j++) {
            p = strstr(tem, pr[j].r);
            if (p) {
                t = pr[j].l;
                pr[j].l = pr[m].l;
                for (i = 0; i < z; i++) {
                    l = strchr(pr[i].r, t);
                    if (l) {
                        a = l - pr[i].r;
                        pr[i].r[a] = pr[m].l;
                    }
                }
            }
        }
    }

    printf("\nEliminate Common Expression:\n");
    for (i = 0; i < z; i++) {
        printf("%c = %s\n", pr[i].l, pr[i].r);
    }

    // Final Optimization - Remove duplicate lines
    for (i = 0; i < z; i++) {
        for (j = i + 1; j < z; j++) {
            q = strcmp(pr[i].r, pr[j].r);
            if ((pr[i].l == pr[j].l) && !q) {
                pr[i].l = '\0'; // mark for deletion
            }
        }
    }

    printf("\nOptimized Code:\n");
    for (i = 0; i < z; i++) {
        if (pr[i].l != '\0') {
            printf("%c = %s\n", pr[i].l, pr[i].r);
        }
    }
}


