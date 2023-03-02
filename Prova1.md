→ Calculando desempenho em processadores com Pipeline

CPI = CPU time cycles / Instruction Count 
CPU time cycles = Instruction Count x CPI 

**CPI pipeline = Número de ciclos (n. instruções + 4) / número de instruções** 

Speed Up = CPI multi-ciclo / CPI pipeline → Solução antiga / Solução nova → O quanto melhorou 

Desempenho Multi-ciclo:

1. Numerar as instruções
2. Contar número de instruções **executadas** 



#**Hazards do Pipeline:**



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
5. Colocar 3 NOPs depois do desvio para dar tempo dele ser resolvido
6. Contar o número de instruções executadas (incluindo os NOPs) 
7. Calcular o número de ciclos: Número de Instruções + 4
8. Calcular CPI: número de ciclos / número de instruções (sem NOPs)
9. SpeedUp = CPI Multiciclo / CPI Pipeline

#**Otimizações do Pipeline**

1. Adiantamento: solução do hardware para conflitos de dados. Pra isso, precisamos adicionar uma unidade de forwarding, ela detectará a dependência e adiantará o resultado que vai ser usado para a próxima instrução. Sempre monitora o registrador que será escrito por uma instrução na posição N e os registradores que serão usados em instruções em N+1 e N+2. 
- Funciona bem pra instruções aritméticas e lógicas (tipo R)

<aside>
📃 Não espera pelo resultado lido da memória, mas utiliza o valor que ainda será escrito na memória.

</aside>

- Para operações de LOAD, ainda precisamos adicionar uma bolha (stall) mesmo com a implementação de adiantamento.
- Quando uma bolha é adicionada, o multiplex adiciona 0 nos bits de controle da instrução e impede a próxima instrução de entrar.
- Stalls reduzem o desempenho

→ Como colocar menos NOPs para resolver conflitos?

Antecipa a resolução do desvio e transforma a instrução que entrou erradamente