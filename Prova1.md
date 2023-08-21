→ Calculando desempenho em processadores com Pipeline

Utiliza-se o conceito de CPI(Clocks por instrução), quanto mais próximo de 1 o CPI, significa que temos um processador bem otimizado e que não utiliza de muitos NOPs, pois estes contam como instruções e deixam o CPU inativo(perde tempo/clocks à toa). Em Superescalares é possível que o CPI seja menor que 1 pois podemos ter 2 instruções acontecendo ao mesmo tempo, ou seja durante o mesmo período de clock.

> CPI = Ciclos totais de execução do programa / Numero de instruções 

Quantos ciclos de clock para uma CPU executando certo número de instruções?

> CPU Clock Cycles = Numero de instruções * CPI

Para saber quanto tempo demora pra executar?

> CPU Exec. time = CPU CLock Cycles * Tempo de Ciclo de clock

> CPU Exec. time = Número de instruções * CPI * Tempo de Ciclo de clock

**CPI pipeline = Número de ciclos (n. instruções + 4) / número de instruções** 

Speed Up = CPI multi-ciclo / CPI pipeline → Solução antiga / Solução nova → O quanto melhorou 

Desempenho Multi-ciclo:

1. Numerar as instruções
2. Contar número de instruções **executadas** 



## **Hazards do Pipeline:**



1. Dependência de dados: quando há leitura de dados que são escritos em uma instrução anterior que ainda não atualizou o registrador

<aside>
📃 Em operações do tipo R, na primeira metade do ciclo o dado é escrito e na segunda metade do ciclo ele é lido. Então pode ocorrer uma escrita e uma leitura num mesmo ciclo, sem conflitos.

</aside>

Solução mais simples (software): adicionar NOPS (instruções que não operam nada), para atrasar a instrução dependente em 1 ou 2 ciclos. Ou rearrumar o código de forma que a nova sequência não tenha as dependências. Desvantagem: dependência do compilador.

1. Conflito de controle ou conflito de Branch: quando existem desvios no código e precisamos decidir quais as próximas instruções do pipeline antes da condição do desvio ser realmente testada. A resolução desse desvio é no estágio de MEM, ou seja, já se passaram 3 estágios. 

Solução mais simples (software): adicionar 3 NOPs após o desvio, pois saberemos depois de 3 estágios se o desvio vai acontecer ou não.

→ Como calcular o desempenho do pipeline depois do software inserir NOPs?

SpeedUp quando conflitos são resolvidos pelo compilador:

1. Numerar as instruções
2. Identificar os conflitos (dependências de dados)
3. Colocar 1 ou 2 NOPs entre instruções com dependência de dados
4. Identificar as dependências de controle (de desvio)
5. Colocar 3 NOPs depois do desvio para dar tempo dele ser resolvido. 
6. Contar o número de instruções executadas (incluindo os NOPs) 
7. Calcular o número de ciclos: Número de Instruções + 4
8. Calcular CPI: número de ciclos / número de instruções (sem NOPs)
9. SpeedUp = CPI Multiciclo / CPI Pipeline

## **Otimizações do Pipeline**

1. Adiantamento: solução do hardware para conflitos de dados. Pra isso, precisamos adicionar uma unidade de forwarding, ela detectará a dependência e adiantará o resultado que vai ser usado para a próxima instrução. Sempre monitora o registrador que será escrito por uma instrução na posição N e os registradores que serão usados em instruções em N+1 e N+2. 
- Funciona bem pra instruções aritméticas e lógicas ( tipo R )

<aside>
📃 Não espera pelo resultado lido da memória, mas utiliza o valor que ainda será escrito na memória.

</aside>

- Para operações de LOAD, ainda precisamos adicionar uma bolha (stall) mesmo com a implementação de adiantamento.
- Quando uma bolha é adicionada, o multiplex adiciona 0 nos bits de controle da instrução e impede a próxima instrução de entrar.
- Stalls reduzem o desempenho

### → Como colocar menos NOPs para resolver conflitos de branch?

**Unidade de adiantamento**

Resolvemos a resolução da branch já no estágio ID para saber se a branch foi TAKEN ou NOT-TAKEN e com isso já reduzimos a penalidade de 3 Ciclos(NOPs) para apenas 1.

Antecipa a resolução do desvio e transforma a instrução que entrou erradamente na instrução que a branch está apontando. 

![image](https://user-images.githubusercontent.com/118495219/222492296-7e6ec564-1970-4060-bca3-7da3143a2e24.png)

*No estágio ID podemos ver a unidade "Control" que é basicamente uma pequena ALU para comparar valores dos Registradores e saber se o desvio ocorrerá. Este resultado passa por um MUX para decidir se o novo endereço da branch irá para o PC(program counter), ou seja a branch foi taken, ou o pipeline continuará normalmente já que a branch foi not-taken*


#  Exceções no RISC-V 

No contexto do RISC-V, as exceções representam situações inesperadas que interrompem o fluxo regular de instruções. Elas podem ser causadas por várias razões, incluindo instruções ilegais ou falhas de acesso à memória. 

Quando ocorre uma exceção, o RISC-V salva o endereço da instrução que causou a exceção e transfere o controle para um manipulador de exceções predefinido. Essas exceções são tratadas por diferentes níveis de privilégio no RISC-V, como modos de usuário (U), supervisor (S) e máquina (M). A compreensão das exceções e de sua manipulação é crucial para a otimização e segurança de qualquer sistema baseado em RISC-V.

# Reservation Station e Reorder Buffer (Sempre cai na prova!)

Ambos são componentes importantes para a execução _out of order_, esses mecanismos ajudam a melhorar o desempenho da CPU possibilitando que instruções sejam executadas sem depender da ordem que tem no programa.

    Reservation Station (RS):

        Objetivo: As Reservation Stations têm como objetivo preparar instruções para execução assim que todos os seus operandos estiverem prontos. Eles ajudam a executar instruções fora de ordem, garantindo que as instruções sejam executadas assim que todos os seus pré-requisitos forem atendidos.

        Funcionamento: Cada entrada na Reservation Station armazena informações sobre uma instrução, incluindo seus operandos. Se um operando ainda não estiver disponível (porque, por exemplo, está sendo produzido por uma instrução anterior que ainda não foi executada), a Reservation Station rastreará onde esse operando será gerado.

        Benefícios: Ao permitir que instruções esperem ativamente pelos operandos de que precisam, as Reservation Stations ajudam a aumentar o paralelismo a nível de instrução. Instruções que não dependem de outras podem ser executadas sem esperar por aquelas que têm dependências.
        
    Reorder Buffer (ROB):

        Objetivo: O principal objetivo do Reorder Buffer é garantir que as instruções sejam "retiradas" (ou seja, suas execuções sejam finalizadas e seus efeitos se tornem visíveis para o programa) na ordem do programa original, mesmo que sejam executadas fora de ordem. Isso é crucial para garantir a consistência e a semântica correta do programa.

        Funcionamento: O ROB armazena instruções em ordem de programa. Cada entrada no buffer contém o tipo de instrução, o destino (se houver), o valor do resultado e o status da instrução. Uma vez que uma instrução foi executada, ela é marcada como tal no ROB. Quando a instrução mais antiga no ROB foi executada e todos os seus efeitos são conhecidos, ela é retirada: seus efeitos são realmente aplicados e ela é removida do ROB.

        Benefícios: Além de garantir a ordem correta das instruções, o ROB também permite a recuperação eficiente de erros. Por exemplo, se um branch for mal predito, instruções subsequentes no ROB podem ser simplesmente descartadas, pois seus efeitos ainda não foram aplicados ao estado real da máquina.

![image](https://github.com/pedrocgr/Infraestrutura_Hardware/assets/118495219/daf81317-2c28-436a-a6c5-bc94aa91bde1)

*As duas ferramentas, ROB e RS, trabalham em conjunto como pode ser visto na imagem acima para permitir alto desempenho com execução fora de ordem, aproveitando ao máximo o hardware disponível e buscando aumentar o _throughput_ de instruções e minimizar os ciclos da CPU desperdiçados.*
