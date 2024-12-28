Versão para Linux;
Implementadas funções de cadastro de funcionário com variáveis nome, cargo, CPF e salário; consulta de funcionários por cpf; modo administrador com senha 'admin' para consulta de todos os cadastros e atualização e remoção de cadastro.
Implementadas validações de escolhas.



#include <stdio.h>
#include <string.h>

typedef struct {

    char nome [50], cargo [50];
    long long int cpf;
    float salario;

} funcionario;

//declarar protótipo da função FILE antes do main
FILE *criarArquivo (const char *executavel, funcionario registro);
FILE *adminArquivo ();
FILE *adminAtualizar ();
FILE *tempArquivo;

int confereAdmin = 0;

int main ()

{      
    const char *chamarArquivo = "funcionario.txt";
    
    char inicia = 0;
    funcionario novoRegistro;
    //funcionario pesquisaCPFVR;
    //funcionario *pesquisaCPF;
    //pesquisaCPF = &pesquisaCPFVR;
    //não foram necessários structs, por que?

    printf ("Digite 'I' para cadastrar.\nDigite 'C' para consultar cadastros.\nDigite 'A' para logar como Admin.\nDigite 'F' para sair.\n");
    inicia = getchar();

    // iniciar arquivo texto

    while (inicia == 'i' || inicia == 'I'){
    //atualizar arquivo texto
        while (getchar() != '\n' && !feof(stdin));

        printf ("Digite o nome do funcionário: ");
        fgets (novoRegistro.nome, sizeof (novoRegistro.nome), stdin);
        strtok(novoRegistro.nome, "\n");

        printf ("Digite o cargo: ");
        fgets (novoRegistro.cargo, sizeof (novoRegistro.cargo), stdin);
        strtok(novoRegistro.cargo, "\n");

        printf ("Digite o CPF: ");
        scanf ("%lld", &novoRegistro.cpf); 
        fflush(stdin);
            if (novoRegistro.cpf <= 0){
                printf ("Formato inválido. Digite novamente!\n");
                scanf ("%lld", &novoRegistro.cpf);
            }
  
        printf ("Digite o salario: ");
        scanf ("%f", &novoRegistro.salario);
        fflush(stdin);

        //arquivotexto é um ponteiro para criar arquivo
        FILE *arquivo = criarArquivo (chamarArquivo, novoRegistro);

        //aqui estava dando erro de comando não encontrado, mas lembrei de limpar o buffer do teclado para inserir corretamente a entrada de scanf
        while (getchar() != '\n' && !feof(stdin));
    
        printf ("Digite 'F' para finalizar ou 'I' para continuar.\n");
        scanf (" %c", &inicia);

        fflush(stdin);


    }

    //printar no sistema os dados
    while (inicia == 'C' || inicia == 'c'){

        FILE *arquivotexto = fopen ("funcionario.txt", "r");
        if (arquivotexto == NULL){
            printf ("Erro ao abrir o arquivo.\n");
            return 1;
        }

        //criadas variáveis de pesquisa por cpf e número de encontrados
        long long int pesquisaCPF;
        int encontrado = 0;

        printf ("Digite o CPF do funcionário, apenas números. Se desejar sair, digite 00: ");
        scanf ("%lld", &pesquisaCPF);

        fflush(stdin);


        if (pesquisaCPF == 00){

            printf ("Fechando...");   
            break;
        }

        funcionario registroLido;

        //fscanf com formatação diferente de scanf com variável tipo struct
        while (fscanf (arquivotexto, "Nome: %49[^|]|Cargo: %49[^|]|CPF: %lld|Salário: %f\n", registroLido.nome, registroLido.cargo, &registroLido.cpf, &registroLido.salario) == 4){
 
        //necessário comparar se o registro lido é o mesmo da variável de entrada na pesquisa por cpf
            if (registroLido.cpf == pesquisaCPF){
                printf ("Nome: %s|Cargo: %s|CPF: %lld|Salário: %.2f\n", registroLido.nome, registroLido.cargo, registroLido.cpf, registroLido.salario);

                encontrado = 1;
                
                break;
            }
        }

        if (!encontrado){
            printf("Cpf não encontrado!\n");
        }

        fclose (arquivotexto);
    }

    if (inicia == 'a' || inicia == 'A'){  
        //logar com senha 'admin'
        char senha [10];
        printf ("Digite a senha: ");
        scanf ("%s", senha);
        

            if (strcmp(senha, "admin") ==0){
                confereAdmin = 1;
                FILE *arquivotexto = adminArquivo ();
                    if (arquivotexto == NULL){
                        return 1;
            }        
        }
            else{ 
                printf ("Senha incorreta!");
                return 1;
        }
    }

    while (inicia == 'f' || inicia == 'F'){

        printf ("Saíndo...");
        break;
    }
}

//atualização e cadastro
//chama função criar arquivo com ponteiro pra struct
//o nome do arquivo será "executavel"
//o modo append faz o arquivo adicionar dados sem sobscrever
FILE *criarArquivo (const char *executavel, funcionario registro){;

    FILE *arquivotexto = fopen ("funcionario.txt", "a");

    fprintf (arquivotexto, "Nome: %s|Cargo: %s|CPF: %lld|Salário: %.2f\n", registro.nome, registro.cargo, registro.cpf, registro.salario);

    fclose (arquivotexto);
    return NULL;  


}
//função admin com leitura e escrita na tela

    FILE *adminArquivo () {
    FILE *arquivotexto = fopen ("funcionario.txt", "r");

        if (arquivotexto == NULL){
            printf ("Erro ao abrir arquivo!");
            return NULL;
        }
        //para ler e printar os dados, user "r" para leitura, fscanf para ler no arquivo e printf com formato printf para printar no console **importante
        funcionario registroLido;
        while (fscanf (arquivotexto, "Nome: %49[^|]|Cargo: %49[^|]|CPF: %lld|Salário: %f\n", 
                  registroLido.nome, registroLido.cargo, 
                  &registroLido.cpf, &registroLido.salario) == 4){

            printf ("Nome: %s|Cargo: %s|CPF: %lld|Salário: %.2f\n", registroLido.nome, registroLido.cargo, registroLido.cpf, registroLido.salario);

        }
        FILE *arquivotextoAdmin = adminAtualizar ();

    fclose (arquivotexto);
    return NULL;

}

    FILE *adminAtualizar () {

    FILE *arquivotextoAdmin = fopen ("funcionario.txt", "r+");
    FILE *tempArquivo = fopen ("temp.txt", "w");

    if (tempArquivo == NULL) {
        printf ("Erro ao criar arquivo temporário");
        fclose (arquivotextoAdmin);

        return NULL;

    }

    if (arquivotextoAdmin == NULL || tempArquivo == NULL) {
    printf("Erro ao abrir arquivos.\n");
    if (arquivotextoAdmin != NULL) fclose(arquivotextoAdmin);
    if (tempArquivo != NULL) fclose(tempArquivo);
    return NULL;
    }

    long long int busca_admCPF;
    char ler_modificarAdmin;
    funcionario atualizarAdmin;
    int encontrado = 0;


        printf ("Digite o CPF do funcionário que deseja atualizar ou remover:\n");
        scanf ("%lld", &busca_admCPF);

        printf ("Digite 'M' para atualizar e 'R' para remover cadastro. Para sair digite 'F':\n");
        // %c com espaço pra limpar buffer 
        scanf  (" %c", &ler_modificarAdmin);
                
        switch (ler_modificarAdmin) {

            case 'M':
            case 'm': 

                while (fscanf (arquivotextoAdmin, "Nome: %49[^|]|Cargo: %49[^|]|CPF: %lld|Salário: %f\n", atualizarAdmin.nome, atualizarAdmin.cargo, &atualizarAdmin.cpf, &atualizarAdmin.salario) == 4){
                
                    fflush(stdin);

                    if (busca_admCPF == atualizarAdmin.cpf) {

                        encontrado = 1;
                        //manipulação de buffer no scanf pra evitar estouro de memoria
                        printf ("Digite o nome: ");
                        scanf (" %49[^\n]", atualizarAdmin.nome);
                        printf ("Digite o cargo: ");
                        scanf (" %49[^\n]", atualizarAdmin.cargo);
                        printf ("Digite o CPF: ");
                        scanf ("%lld", &atualizarAdmin.cpf);

                        if (atualizarAdmin.cpf <= 0){
                            printf ("Formato inválido. Digite novamente!\n");
                            scanf ("%lld", &atualizarAdmin.cpf);
                        }


                        printf ("Digite o salário: ");
                        scanf ("%f", &atualizarAdmin.salario);

                        printf ("Atualizado!");

                        fprintf (tempArquivo, "Nome: %s|Cargo: %s|CPF: %lld|Salário: %.2f\n", atualizarAdmin.nome, atualizarAdmin.cargo, atualizarAdmin.cpf, atualizarAdmin.salario);
                    
                    }
                    else {
                        //se não for o cadastro que quero, copia o registro e cola de volta
                        fprintf (tempArquivo, "Nome: %s|Cargo: %s|CPF: %lld|Salário: %.2f\n", atualizarAdmin.nome, atualizarAdmin.cargo, atualizarAdmin.cpf, atualizarAdmin.salario);

                    }
                  
                }

                if (encontrado == 0){
                    
                    printf ("Nenhum CPF encontrado!\n");

                } 
            
            break;

            case 'r':
            case 'R':        

                while (fscanf (arquivotextoAdmin, "Nome: %49[^|]|Cargo: %49[^|]|CPF: %lld|Salário: %f\n", atualizarAdmin.nome, atualizarAdmin.cargo, &atualizarAdmin.cpf, &atualizarAdmin.salario) == 4){


                    if (busca_admCPF == atualizarAdmin.cpf){
                    
                    encontrado = 1;

                    printf ("Registro removido!");

                    continue;

                    }
                    
                    //o else faz os valores que não coincidem com o pesquisado não serem afetados. Não utilize uma nova lógica de comparação
                    else {
                    
                        fprintf(tempArquivo, "Nome: %s|Cargo: %s|CPF: %lld|Salário: %.2f\n", atualizarAdmin.nome, atualizarAdmin.cargo, atualizarAdmin.cpf, atualizarAdmin.salario);
            
                    }
                    
                }
                    //se o resultado da busca por cpf bater, ele não copia os dados pro arquivo temporario. se o resultado não bater, ele copia e grava
                
                
                if (!encontrado){

                    printf ("CPF Não encontrado.\n");

                }

                break;

            //para apenas fechar o programa, ele precisa ler todo o arquivo, copiar do arquivo temporario e printar de novo o arquivo. apenas fechar o arquivo vai deletar tudo
            case 'f':
            case 'F':

                while (fscanf (arquivotextoAdmin, "Nome: %49[^|]|Cargo: %49[^|]|CPF: %lld|Salário: %f\n", atualizarAdmin.nome, atualizarAdmin.cargo, &atualizarAdmin.cpf, &atualizarAdmin.salario) == 4){
                    
                    fprintf(tempArquivo, "Nome: %s|Cargo: %s|CPF: %lld|Salário: %.2f\n", atualizarAdmin.nome, atualizarAdmin.cargo, atualizarAdmin.cpf, atualizarAdmin.salario);
                }

                 printf ("Fechando...");
                    
            
            break;

            
        }
        
        //printf ("Nome: %s|Cargo: %s|CPF: %lld|Salário: %.2f\n", registroLido.nome, registroLido.cargo, registroLido.cpf, registroLido.salario);

        fclose (tempArquivo);
        fclose (arquivotextoAdmin);

        //tem que remover o antigo arquivo e renomear o arquivo temporario para o orginial
        remove("funcionario.txt");
        rename ("temp.txt", "funcionario.txt");
        
        //chamar função de escrita para modificar cadastros

        return tempArquivo;

}   



