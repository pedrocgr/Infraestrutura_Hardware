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
5. Colocar 3 NOPs depois do desvio para dar tempo dele ser resolvido
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

→ Como colocar menos NOPs para resolver conflitos?

Antecipa a resolução do desvio e transforma a instrução que entrou erradamente
