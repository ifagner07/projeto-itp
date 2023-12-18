#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_TABLE_NAME 50
#define MAX_COLUMNS 10
#define MAX_STRING_LENGTH 256
#define MAX_TABLES 100

// Protótipos das funções
void criar_inserir_tabela();
void consultar_tabela();
void excluir_tabela();
void consultar_dados();
void inserir_linha();
void excluir_linha(char *nome_tabela, int linha);
void listar_tabelas();

// Lista de tabelas
char tabelas[MAX_TABLES][MAX_TABLE_NAME];
int num_tabelas = 0;

int main() {
    int opcao;
    do {
        printf("\nEscolha uma opcao:\n");
        printf("1. Criar e Inserir Tabela\n");
        printf("2. Inserir Dados em Tabela Existente\n");
        printf("3. Consultar Tabela\n");
        printf("4. Consultar Dados\n");
        printf("5. Excluir Tabela\n");
        printf("6. Excluir Linha\n");
        printf("7. Sair\n");

        printf("Opcao: ");
        scanf("%d", &opcao);

        switch (opcao) {
            case 1:
                criar_inserir_tabela();
                break;
            case 2:
                inserir_linha();
                break;
            case 3:
                consultar_tabela();
                break;
            case 4:
                consultar_dados();
                break;
            case 5:
                excluir_tabela();
                break;
            case 6:
                // Adiciona a opcao de escolher uma tabela e uma linha para excluir
                listar_tabelas();
                char nome_tabela[MAX_TABLE_NAME];
                printf("Digite o nome da tabela: ");
                scanf("%s", nome_tabela);
                int linha;
                printf("Digite o numero da linha a ser excluida: ");
                scanf("%d", &linha);
                excluir_linha(nome_tabela, linha);
                break;
            case 7:
                printf("Saindo do programa.\n");
                break;
            default:
                printf("Opcao invalida. Tente novamente.\n");
        }
    } while (opcao != 7);

    return 0;
}

void listar_tabelas() {
    printf("Tabelas disponiveis:\n");
    for (int i = 0; i < num_tabelas; ++i) {
        printf("%d. %s\n", i + 1, tabelas[i]);
    }
}

void criar_inserir_tabela() {
    char nome_tabela[MAX_TABLE_NAME];
    printf("Digite o nome da tabela: ");
    scanf("%s", nome_tabela);

    // Verifica se a tabela ja existe
    for (int i = 0; i < num_tabelas; ++i) {
        if (strcmp(tabelas[i], nome_tabela) == 0) {
            printf("Erro: Tabela '%s' ja existe.\n", nome_tabela);
            return;
        }
    }

    FILE *arquivo = fopen(nome_tabela, "r");
    if (arquivo != NULL) {
        fclose(arquivo);
        printf("Erro: Tabela '%s' ja existe.\n", nome_tabela);
        return;
    }

    arquivo = fopen(nome_tabela, "w");
    if (arquivo == NULL) {
        perror("Erro ao criar a tabela");
        return;
    }

    // Adiciona a tabela a lista
    strcpy(tabelas[num_tabelas], nome_tabela);
    num_tabelas++;

    int num_colunas;
    printf("Digite o numero de colunas (ate %d): ", MAX_COLUMNS);
    scanf("%d", &num_colunas);

    fprintf(arquivo, "%d\n", num_colunas);

    for (int i = 0; i < num_colunas; ++i) {
        char nome_coluna[MAX_STRING_LENGTH];
        printf("Digite o nome da coluna %d: ", i + 1);
        scanf("%s", nome_coluna);
        fprintf(arquivo, "%s\t", nome_coluna);
    }
    fprintf(arquivo, "\n");

    printf("Digite o numero de linhas: ");
    int num_linhas;
    scanf("%d", &num_linhas);

    for (int i = 0; i < num_linhas; ++i) {
        for (int j = 0; j < num_colunas; ++j) {
            char dado[MAX_STRING_LENGTH];
            printf("Digite o dado para a coluna %d da linha %d: ", j + 1, i + 1);
            scanf("%s", dado);
            fprintf(arquivo, "%s\t", dado);
        }
        fprintf(arquivo, "\n");
    }

    fclose(arquivo);
    printf("Tabela '%s' criada e dados inseridos com sucesso!\n", nome_tabela);
}

void inserir_linha() {
    char nome_tabela[MAX_TABLE_NAME];
    printf("Digite o nome da tabela: ");
    scanf("%s", nome_tabela);

    FILE *arquivo = fopen(nome_tabela, "a");
    if (arquivo == NULL) {
        perror("Erro ao abrir a tabela");
        return;
    }

    int num_colunas;
    printf("Digite o numero de colunas (ate %d): ", MAX_COLUMNS);
    scanf("%d", &num_colunas);

    for (int i = 0; i < num_colunas; ++i) {
        char dado[MAX_STRING_LENGTH];
        printf("Digite o dado para a coluna %d: ", i + 1);
        scanf("%s", dado);
        fprintf(arquivo, "%s\t", dado);
    }
    fprintf(arquivo, "\n");

    fclose(arquivo);
    printf("Dados inseridos com sucesso na tabela '%s'!\n", nome_tabela);
}

void consultar_tabela() {
    char nome_tabela[MAX_TABLE_NAME];
    int escolha;

    // Listar as tabelas disponiveis
    listar_tabelas();

    // Solicitar ao usuario que escolha uma tabela
    printf("Escolha o numero da tabela que deseja consultar: ");
    scanf("%d", &escolha);

    // Mapear a escolha do usuario para o nome da tabela (simulado aqui)
    switch (escolha) {
        case 1:
            strcpy(nome_tabela, "tabela1");
            break;
        case 2:
            strcpy(nome_tabela, "tabela2");
            break;
        case 3:
            strcpy(nome_tabela, "tabela3");
            break;
        default:
            printf("Opcao invalida.\n");
            return;
    }

    FILE *arquivo = fopen(nome_tabela, "r");
    if (arquivo == NULL) {
        perror("Erro ao abrir o arquivo para leitura");
        return;
    }

    int num_colunas;
    fscanf(arquivo, "%d", &num_colunas);

    printf("Conteudo da tabela '%s':\n", nome_tabela);

    for (int i = 0; i < num_colunas; ++i) {
        char nome_coluna[MAX_STRING_LENGTH];
        fscanf(arquivo, "%s", nome_coluna);
        printf("%-15s", nome_coluna); // Adiciona espacamento fixo para melhor alinhamento
    }
    printf("\n");

    char entrada[MAX_STRING_LENGTH];
    int coluna_atual = 0;

    while (fscanf(arquivo, "%s", entrada) != EOF) {
        printf("%-15s", entrada); // Adiciona espacamento fixo para melhor alinhamento
        coluna_atual++;

        if (coluna_atual == num_colunas) {
            // Adiciona uma quebra de linha apos cada linha da tabela
            printf("\n");
            coluna_atual = 0; // Reinicia o contador de colunas
        }
    }

    fclose(arquivo);
}

void consultar_dados() {
    char nome_tabela[MAX_TABLE_NAME];
    printf("Digite o nome da tabela: ");
    scanf("%s", nome_tabela);

    FILE *arquivo = fopen(nome_tabela, "r");
    if (arquivo == NULL) {
        perror("Erro ao abrir o arquivo para leitura");
        return;
    }

    int num_colunas, num_linhas;
    fscanf(arquivo, "%d", &num_colunas);
    fscanf(arquivo, "%d", &num_linhas);

    char nome_coluna[MAX_STRING_LENGTH];
    printf("Digite o nome da coluna cujos dados serao consultados: ");
    scanf("%s", nome_coluna);

    char coluna[MAX_STRING_LENGTH];
    int coluna_atual = 0;

    // Percorre as colunas e encontra a posicao da coluna especificada
    while (fscanf(arquivo, "%s", coluna) != EOF) {
        if (strcmp(coluna, nome_coluna) == 0) {
            break;
        }
        coluna_atual++;
    }

    if (coluna_atual == num_colunas) {
        printf("Coluna '%s' nao encontrada!\n", nome_coluna);
        fclose(arquivo);
        return;
    }

    printf("Digite o numero inicial da linha cujos dados serao consultados: ");
    int linha_inicial;
    scanf("%d", &linha_inicial);

    printf("Digite o numero final da linha cujos dados serao consultados: ");
    int linha_final;
    scanf("%d", &linha_final);

    if (linha_inicial < 1 || linha_inicial > num_linhas || linha_final < 1 || linha_final > num_linhas) {
        printf("Erro: o numero da linha inserido e invalido.\n");
        fclose(arquivo);
        return;
    }

    // Percorre as linhas especificadas e imprime o dado na coluna especificada
    for (int linha = linha_inicial; linha <= linha_final; linha++) {
        int posicao_final = (linha - 1) * (num_colunas + 1) + coluna_atual;
        fseek(arquivo, posicao_final * MAX_STRING_LENGTH, SEEK_CUR);

        // Le o dado na posicao especificada
        char dado[MAX_STRING_LENGTH];
        fscanf(arquivo, "%s", dado);

        printf("O dado na linha %d e coluna '%s' e: %s\n", linha, nome_coluna, dado);
    }

    fclose(arquivo);
}

void excluir_tabela() {
    char nome_tabela[MAX_TABLE_NAME];
    printf("Digite o nome da tabela a ser excluida: ");
    scanf("%s", nome_tabela);

    if (remove(nome_tabela) == 0) {
        printf("Tabela '%s' excluida com sucesso!\n", nome_tabela);
    } else {
        perror("Erro ao excluir a tabela");
    }
}

void excluir_linha(char *nome_tabela, int linha) {
    FILE *arquivo = fopen(nome_tabela, "r+");
    FILE *arquivo_novo = fopen("nome_temporario.txt", "w");

    if (arquivo == NULL || arquivo_novo == NULL) {
        perror("Erro ao abrir o arquivo para leitura e escrita");
        if (arquivo != NULL) fclose(arquivo);
        if (arquivo_novo != NULL) fclose(arquivo_novo);
        return;
    }

    char linha_atual[MAX_STRING_LENGTH];
    int numero_linha = 1;

    // Copiar todas as linhas, exceto a linha desejada, para o arquivo temporario
    while (fgets(linha_atual, sizeof(linha_atual), arquivo) != NULL) {
        if (numero_linha != linha) {
            fputs(linha_atual, arquivo_novo);
        }
        numero_linha++;
    }

    fclose(arquivo);
    fclose(arquivo_novo);

    // Remover o arquivo original e renomear o temporario
    if (remove(nome_tabela) == 0) {
        if (rename("nome_temporario.txt", nome_tabela) == 0) {
            printf("Linha %d excluida com sucesso!\n", linha);
        } else {
            perror("Erro ao renomear o arquivo temporario");
        }
    } else {
        perror("Erro ao remover o arquivo original");
    }
}
