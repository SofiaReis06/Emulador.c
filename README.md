#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MEMORY_SIZE 1024
#define REGISTER_COUNT 8

typedef struct {
    int registers[REGISTER_COUNT];
    int memory[MEMORY_SIZE];
    int pc; // Program Counter
    int ir; // Instruction Register
} CPU;

void initializeCPU(CPU *cpu) {
    memset(cpu->registers, 0, sizeof(cpu->registers));
    memset(cpu->memory, 0, sizeof(cpu->memory));
    cpu->pc = 0;
    cpu->ir = 0;
}

int loadProgram(CPU *cpu, const char *filename) {
    FILE *file = fopen(filename, "rb");
    if (!file) {
        perror("Erro ao abrir o arquivo");
        return 0;
    }

    size_t bytesRead = fread(cpu->memory, sizeof(int), MEMORY_SIZE, file);
    fclose(file);

    if (bytesRead == 0) {
        fprintf(stderr, "Arquivo vazio ou erro na leitura.\n");
        return 0;
    }

    return 1;
}

void executeInstruction(CPU *cpu) {
    cpu->ir = cpu->memory[cpu->pc];
    // Decodificar e executar a instrução
    // Exemplo simples: suponha que a instrução seja um código de operação
    switch (cpu->ir) {
        case 0: // NOP
            break;
        case 1: // INCREMENTAR REGISTRO 0
            cpu->registers[0]++;
            break;
        // Adicionar mais casos conforme o conjunto de instruções
        default:
            fprintf(stderr, "Instrução desconhecida: %d\n", cpu->ir);
            exit(EXIT_FAILURE);
    }
    cpu->pc++;
}

void run(CPU *cpu) {
    while (cpu->pc < MEMORY_SIZE) {
        executeInstruction(cpu);
    }
}

int main(int argc, char *argv[]) {
    if (argc != 2) {
        fprintf(stderr, "Uso: %s <arquivo_programa>\n", argv[0]);
        return EXIT_FAILURE;
    }

    CPU cpu;
    initializeCPU(&cpu);

    if (!loadProgram(&cpu, argv[1])) {
        return EXIT_FAILURE;
    }

    run(&cpu);

    return EXIT_SUCCESS;
}