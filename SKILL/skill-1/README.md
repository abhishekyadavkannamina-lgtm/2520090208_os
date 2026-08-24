#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid;

    printf("Parent process started\n");
    printf("Parent PID: %d\n", getpid());

    pid = fork();

    if (pid < 0) {
        perror("fork failed");
        return 1;
    }

    if (pid == 0) {
        // Child process
        printf("\nChild process\n");
        printf("Child PID: %d\n", getpid());
        printf("Child PPID: %d\n", getppid());
    } 
    else {
        // Parent process
        printf("\nParent process\n");
        printf("Parent PID: %d\n", getpid());
        printf("Child PID: %d\n", pid);

        wait(NULL);

        printf("Child process completed\n");
    }

    return 0;
}
