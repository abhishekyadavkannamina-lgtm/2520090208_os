#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>
#include <termios.h>

#define MAX_INPUT 100

void disable_raw_mode(struct termios *old) {
    tcsetattr(STDIN_FILENO, TCSANOW, old);
}

void enable_raw_mode(struct termios *old) {
    struct termios raw;

    tcgetattr(STDIN_FILENO, old);
    raw = *old;

    raw.c_lflag &= ~(ICANON | ECHO);

    tcsetattr(STDIN_FILENO, TCSANOW, &raw);
}

int main() {
    char input[MAX_INPUT];
    int index;
    char ch;
    struct termios old;

    enable_raw_mode(&old);

    while (1) {
        index = 0;

        printf("\nmini-shell> ");
        fflush(stdout);

        while (1) {
            ch = getchar();

            /* Enter key */
            if (ch == '\n' || ch == '\r') {
                input[index] = '\0';
                printf("\n");
                break;
            }

            /* Backspace */
            if (ch == 127 || ch == '\b') {
                if (index > 0) {
                    index--;
                    printf("\b \b");
                    fflush(stdout);
                }
                continue;
            }

            /* Store normal characters */
            if (index < MAX_INPUT - 1) {
                input[index] = ch;
                index++;

                putchar(ch);
                fflush(stdout);
            }
        }

        /* Exit condition */
        if (strcmp(input, "exit") == 0) {
            printf("Exiting mini shell...\n");
            break;
        }

        /* Empty input */
        if (strlen(input) == 0) {
            continue;
        }

        printf("You entered: %s\n", input);
    }

    disable_raw_mode(&old);

    return 0;
}
