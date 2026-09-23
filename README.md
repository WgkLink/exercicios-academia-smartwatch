# Exercícios de academia: acelerômetro do Galaxy Watch 4

Conjunto de séries temporais rotuladas para classificação de 3 exercícios de braço, gravados pelo acelerômetro de um Galaxy Watch 4 usado no pulso. Trabalho Final da disciplina de Mineração de Séries Temporais (PUCPR).

| Classe | Pasta | Execução | Carga |
|---|---|---|---|
| Rosca bíceps | `rosca` | polia | 5 kg |
| Elevação lateral | `elevacao` | halteres | 3 kg |
| Tríceps | `triceps` | polia | 5 kg |

## Estrutura

```
dataset/
  train/   rosca/  elevacao/  triceps/   (21/09/2026, 10 séries por classe)
  test/    rosca/  elevacao/  triceps/   (22/09/2026, 10 séries por classe)
```

Total: 60 séries, 30 de treino e 30 de teste.

## Formato dos arquivos

Cada `<classe>_<split>_NN.csv` é **uma série de 10 repetições** de um exercício.

| Coluna | Conteúdo |
|---|---|
| `time` | instante absoluto em nanossegundos (Unix) |
| `seconds_elapsed` | tempo em segundos desde o início da gravação (pode começar negativo) |
| `x`, `y`, `z` | aceleração linear, sem gravidade, em m/s² |

A **ordem das colunas varia** entre arquivos (`x,z,y` em 56 deles e `z,y,x` em 4), então o carregamento identifica as colunas pelo nome, nunca pela posição.

Taxa nominal de 100 Hz e **efetiva de ~57 Hz**: cerca de 7% dos intervalos têm lacunas de ~100 ms, causadas pela transmissão do relógio para o celular. Por isso o notebook reamostra as séries antes de usá-las. As primeiras amostras de algumas gravações trazem um pico de inicialização do sensor (11 a 17 m/s², contra ~0,2 m/s² em repouso), descartado no pré-processamento.

## Protocolo de coleta

- **Sujeitos:** 2 pessoas, uma em cada conjunto: a sessão de **treino** foi gravada por uma pessoa e a de **teste** por outra. Relógio Galaxy Watch 4 no **pulso esquerdo** nas duas, sempre na mesma posição, com os mesmos exercícios, as mesmas máquinas e as mesmas cargas.
- **Registro:** app Sensor Logger 1.66, com o celular SM-G991B recebendo os dados do relógio; sensor `Watch Accelerometer`.
- **Local:** academia Sou Mais Murici, em São José dos Pinhais (PR).
- **Execução:** cada gravação começa e termina com o braço parado e contém **10 repetições**, com duração média de 28 s. A contagem foi conferida por uma segunda pessoa durante a coleta.
- **Independência:** cada série é uma gravação separada, com descanso entre elas; os exercícios foram alternados dentro de cada sessão.
- **Divisão treino/teste:** **por pessoa**. Treino em 21/09/2026 com a pessoa A e teste em 22/09/2026 com a pessoa B. Nenhuma gravação de teste veio da mesma pessoa nem do mesmo dia do treino, então o conjunto de teste mede a generalização para um sujeito novo.

## Código

O notebook que usa este conjunto de dados (pré-processamento, baseline 1NN-DTW, MiniROCKET, acurácia e matrizes de confusão) está no Google Colab: **https://colab.research.google.com/drive/1I4Vc6lhRaAe0GS0JJWMbSNPkI-o9MkyC?usp=sharing**. Ele baixa este repositório com `git clone` e roda de ponta a ponta.

Resultados no conjunto de teste: **90,0%** de acurácia com o baseline 1NN-DTW (janela 0,05) e **93,3%** com o MiniROCKET (1000 kernels). Todos os erros ocorreram entre rosca e tríceps; a elevação lateral foi classificada corretamente em todas as séries.
