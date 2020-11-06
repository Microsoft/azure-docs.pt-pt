---
title: Compreender os preços da Azure Data Factory através de exemplos
description: Este artigo explica e demonstra o modelo de preços da Azure Data Factory com exemplos detalhados
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 13a05089ae6365bb5d279105f8c010278bd0adb8
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93396010"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Compreender os preços do Data Factory através de exemplos

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explica e demonstra o modelo de preços da Azure Data Factory com exemplos detalhados.

> [!NOTE]
> Os preços utilizados nestes exemplos abaixo são hipotéticos e não se destinam a implicar preços reais.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Copiar dados de AWS S3 para Azure Blob armazenamento por hora

Neste cenário, pretende copiar dados do armazenamento AWS S3 para Azure Blob num horário de hora a hora.

Para realizar o cenário, é necessário criar um oleoduto com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para que os dados sejam copiados da AWS S3.

2. Um conjunto de dados de saída para os dados do Azure Storage.

3. Um gatilho de horário para executar o oleoduto a cada hora.

   ![O diagrama mostra um oleoduto com um gatilho de horário. No oleoduto, a atividade de cópia flui para um conjunto de dados de entrada, que flui para um serviço ligado A W S3 e atividade de cópia também flui para um conjunto de dados de saída, que flui para um serviço ligado ao Armazenamento Azure.](media/pricing-concepts/scenario1.png)

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar Serviço Linked | 2 Entidade de leitura/escrita  |
| Criar conjuntos de dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviços ligados) |
| Criar Pipeline | 3 Entidades de leitura/escrita (1 para criação de gasodutos, 2 para referências de conjuntos de dados) |
| Obter Pipeline | 1 Entidade de leitura/escrita |
| Executar Pipeline | 2 A atividade é executada (1 para o gatilho, 1 para execuções de atividade) |
| Copiar Dados Pressuposto: tempo de execução = 10 min | 10 \* 4 Tempo de execução da integração Azure (definição padrão de DI = 4) Para obter mais informações sobre unidades de integração de dados e otimizar o desempenho da cópia, consulte [este artigo](copy-activity-performance.md) |
| Monitor Pipeline Assumption: Apenas 1 corrida ocorreu | 2 Registos de execução de monitorização recuperados (1 para o gasoduto, 1 para a execução da atividade) |

**Preço total do cenário: $0.16811**

- Operações de Fábrica de Dados = **$0.0001**
  - Ler/escrever = 10 \* 00001 = $0.0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 2 \* 000005 = $0.00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução da Orquestração do Oleoduto &amp; = **$0.168**
  - Operações Execuções = 001 \* 2 = 0,002 [1 run = $1/1000 = 0,001]
  - Atividades de Movimento de Dados = $0.166 (Prostado para 10 minutos de tempo de execução. $0,25/hora no tempo de funcionamento da integração Azure)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Copie os dados e transforme-se com a Azure Databricks por hora

Neste cenário, pretende copiar dados de armazenamento AWS S3 para Azure Blob e transformar os dados com Azure Databricks num horário de hora.

Para realizar o cenário, é necessário criar um oleoduto com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a serem copiados a partir de AWS S3, e um conjunto de dados de saída para os dados sobre o armazenamento do Azure.
2. Uma atividade da Azure Databricks para a transformação de dados.
3. Um gatilho de horário para executar o oleoduto a cada hora.

![O diagrama mostra um oleoduto com um gatilho de horário. No oleoduto, a atividade de cópia flui para um conjunto de dados de entrada, um conjunto de dados de saída e uma atividade DataBricks, que funciona em Azure Databricks. O conjunto de dados de entrada flui para um serviço ligado A W S3. O conjunto de dados de saída flui para um serviço ligado ao Azure Storage.](media/pricing-concepts/scenario2.png)

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar Serviço Linked | 3 Entidade de leitura/escrita  |
| Criar conjuntos de dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviços ligados) |
| Criar Pipeline | 3 Entidades de leitura/escrita (1 para criação de gasodutos, 2 para referências de conjuntos de dados) |
| Obter Pipeline | 1 Entidade de leitura/escrita |
| Executar Pipeline | 3 Funciona a atividade (1 para o gatilho, 2 para execuções de atividade) |
| Copiar Dados Pressuposto: tempo de execução = 10 min | 10 \* 4 Tempo de execução da integração Azure (definição padrão de DI = 4) Para obter mais informações sobre unidades de integração de dados e otimizar o desempenho da cópia, consulte [este artigo](copy-activity-performance.md) |
| Monitor Pipeline Assumption: Apenas 1 corrida ocorreu | 3 Registos de execução de monitorização recuperados (1 para o gasoduto, 2 para a execução da atividade) |
| Executar atividade databricks Suposição: tempo de execução = 10 min | Execução da atividade do gasoduto externo de 10 min |

**Preço total do cenário: $0.16916**

- Operações de Fábrica de Dados = **$0.00012**
  - Ler/escrever = 11 \* 00001 = $0.00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 3 \* 000005 = $0.00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução da Orquestração do Oleoduto &amp; = **$0.16904**
  - Operações Execuções = 001 \* 3 = 0,003 [1 run = $1/1000 = 0,001]
  - Atividades de Movimento de Dados = $0.166 (Prostado para 10 minutos de tempo de execução. $0,25/hora no tempo de funcionamento da integração Azure)
  - Atividade do Gasoduto Externo = $0.000041 (Prostado para 10 minutos de tempo de execução. $0.00025/hora no Tempo de Execução da Integração Azure)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Copie os dados e transforme-os com parâmetros dinâmicos por hora

Neste cenário, pretende copiar dados de armazenamento AWS S3 para Azure Blob e transformar-se com Azure Databricks (com parâmetros dinâmicos no script) numa programação de hora a hora.

Para realizar o cenário, é necessário criar um oleoduto com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a serem copiados a partir de AWS S3, um conjunto de dados de saída para os dados sobre o armazenamento do Azure.
2. Uma atividade de procura para passar parâmetros dinamicamente para o script de transformação.
3. Uma atividade da Azure Databricks para a transformação de dados.
4. Um gatilho de horário para executar o oleoduto a cada hora.

![O diagrama mostra um oleoduto com um gatilho de horário. No oleoduto, a atividade de cópia flui para um conjunto de dados de entrada, um conjunto de dados de saída e atividade de procura que flui para uma atividade dataBricks, que funciona em Azure Databricks. O conjunto de dados de entrada flui para um serviço ligado A W S3. O conjunto de dados de saída flui para um serviço ligado ao Azure Storage.](media/pricing-concepts/scenario3.png)

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar Serviço Linked | 3 Entidade de leitura/escrita  |
| Criar conjuntos de dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviços ligados) |
| Criar Pipeline | 3 Entidades de leitura/escrita (1 para criação de gasodutos, 2 para referências de conjuntos de dados) |
| Obter Pipeline | 1 Entidade de leitura/escrita |
| Executar Pipeline | 4 Operações executadas (1 para o gatilho, 3 para execuções de atividade) |
| Copiar Dados Pressuposto: tempo de execução = 10 min | 10 \* 4 Tempo de execução da integração Azure (definição padrão de DI = 4) Para obter mais informações sobre unidades de integração de dados e otimizar o desempenho da cópia, consulte [este artigo](copy-activity-performance.md) |
| Monitor Pipeline Assumption: Apenas 1 corrida ocorreu | 4 Registos de execução de monitorização recuperados (1 para o gasoduto, 3 para a execução da atividade) |
| Executar Atividade de procuração Suposição: tempo de execução = 1 min | Execução de atividade de 1 min pipeline |
| Executar atividade databricks Suposição: tempo de execução = 10 min | Execução da atividade do gasoduto externo de 10 min |

**Preço total do cenário: $0.17020**

- Operações de Fábrica de Dados = **$0.00013**
  - Ler/escrever = 11 \* 00001 = $0.00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 4 \* 000005 = $0.00002 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução da Orquestração do Oleoduto &amp; = **$0.17007**
  - Operações Executa = 001 \* 4 = 0,004 [1 run = $1/1000 = 0,001]
  - Atividades de Movimento de Dados = $0.166 (Prostado para 10 minutos de tempo de execução. $0,25/hora no tempo de funcionamento da integração Azure)
  - Atividade do gasoduto = $0.00003 (Prostado por 1 minuto de tempo de execução. $0.002/hora no tempo de funcionamento da integração Azure)
  - Atividade do Gasoduto Externo = $0.000041 (Prostado para 10 minutos de tempo de execução. $0.00025/hora no Tempo de Execução da Integração Azure)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Usando o fluxo de dados de mapeamento depurado para um dia de trabalho normal

Como Engenheiro de Dados, a Sam é responsável por conceber, construir e testar fluxos de dados de mapeamento todos os dias. Sam entra no UI ADF de manhã e permite o modo Debug para Fluxos de Dados. O TTL predefinido para sessões de Debug é de 60 minutos. O Sam trabalha durante 8 horas durante 8 horas, por isso a sessão do Debug nunca expira. Portanto, as acusações de Sam para o dia serão:

**8 (horas) x 8 (núcleos otimizados por computação) x $0,193 = $12,35**

Ao mesmo tempo, Chris, outro Engenheiro de Dados, também entra no UI do navegador ADF para perfis de dados e trabalhos de design ETL. Chris não trabalha na ADF o dia todo como o Sam. Chris só precisa usar o depurar fluxo de dados por 1 hora durante o mesmo período e no mesmo dia que Sam acima. Estas são as acusações que Chris incorre para o uso de depurg:

**1 (hora) x 8 (núcleos de propósito geral) x $0,274 = $2,19**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transforme os dados na loja blob com fluxos de dados de mapeamento

Neste cenário, pretende transformar os dados na Blob Store visualmente em fluxos de dados de mapeamento ADF num horário de hora a hora.

Para realizar o cenário, é necessário criar um oleoduto com os seguintes itens:

1. Uma atividade de Fluxo de Dados com a lógica de transformação.

2. Um conjunto de dados de entrada para os dados do Azure Storage.

3. Um conjunto de dados de saída para os dados do Azure Storage.

4. Um gatilho de horário para executar o oleoduto a cada hora.

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar Serviço Linked | 2 Entidade de leitura/escrita  |
| Criar conjuntos de dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviços ligados) |
| Criar Pipeline | 3 Entidades de leitura/escrita (1 para criação de gasodutos, 2 para referências de conjuntos de dados) |
| Obter Pipeline | 1 Entidade de leitura/escrita |
| Executar Pipeline | 2 A atividade é executada (1 para o gatilho, 1 para execuções de atividade) |
| Pressupostos do fluxo de dados: tempo de execução = 10 min + 10 min TTL | 10 \* 16 núcleos de Computação Geral com TTL de 10 |
| Monitor Pipeline Assumption: Apenas 1 corrida ocorreu | 2 Registos de execução de monitorização recuperados (1 para o gasoduto, 1 para a execução da atividade) |

**Preço total do cenário: $1.4631**

- Operações de Fábrica de Dados = **$0.0001**
  - Ler/escrever = 10 \* 00001 = $0.0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 2 \* 000005 = $0.00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução da Orquestração do Oleoduto &amp; = **$1.463**
  - Operações Execuções = 001 \* 2 = 0,002 [1 run = $1/1000 = 0,001]
  - Atividades de fluxo de dados = $1.461 prociado por 20 minutos (tempo de execução de 10 minutos + 10 minutos TTL). $0.274/hora no Tempo de Execução da Integração Azure com 16 núcleos de cálculo geral

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Integração de dados na Azure Data Factory Gerido VNET
Neste cenário, pretende eliminar ficheiros originais sobre o Armazenamento Azure Blob e copiar dados da Base de Dados Azure SQL para o Azure Blob Storage. Vais fazer esta execução duas vezes em diferentes oleodutos. O tempo de execução destes dois oleodutos está a sobrepor-se.
![Cenário4 ](media/pricing-concepts/scenario-4.png) Para realizar o cenário, é necessário criar dois oleodutos com os seguintes itens:
  - Uma atividade de pipeline – Apagar Atividade.
  - Uma atividade de cópia com um conjunto de dados de entrada para que os dados sejam copiados do armazenamento da Azure Blob.
  - Um conjunto de dados de saída para os dados da Base de Dados Azure SQL.
  - Um horário dispara para executar o oleoduto.


| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar Serviço Linked | 4 Entidade de leitura/escrita |
| Criar conjuntos de dados | 8 Entidades de leitura/escrita (4 para criação de conjuntos de dados, 4 para referências de serviços ligados) |
| Criar Pipeline | 6 Entidades de leitura/escrita (2 para criação de gasodutos, 4 para referências de conjuntos de dados) |
| Obter Pipeline | 2 Entidade de leitura/escrita |
| Executar Pipeline | 6 Funciona a atividade (2 para o gatilho, 4 para execuções de atividade) |
| Executar Apagar Atividade: cada tempo de execução = 5 min. A execução da Atividade de Eliminação no primeiro oleoduto é das 10:00 AM UTC às 10:05 UTC. A execução da Atividade de Eliminação no segundo oleoduto é das 10:02 AM UTC às 10:07 UTC.|Total de 7 min execução da atividade do gasoduto em VNET gerido. A atividade do gasoduto suporta até 50 concurrency em VNET gerido. |
| Copiar Dados Pressuposto: cada tempo de execução = 10 min. A execução da Cópia no primeiro oleoduto é das 10:06 AM UTC às 10:15 UTC. A execução da Atividade de Eliminação no segundo oleoduto é das 10:08 AM UTC às 10:17 UTC. | 10 * 4 Tempo de execução da integração Azure (definição padrão de DI = 4) Para obter mais informações sobre unidades de integração de dados e otimizar o desempenho da cópia, consulte [este artigo](copy-activity-performance.md) |
| Monitor Pipeline Assumption: Apenas 2 corridas ocorreram | 6 Registos de execução de monitorização recuperados (2 para o gasoduto, 4 para execução de atividade) |


**Preço total do cenário: $0.45523**

- Operações de Fábrica de Dados = $0.00023
  - Ler/escrever = 20*00001 = $0.0002 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 6*000005 = $0.00003 [1 Monitorização = $0,25/50000 = 0,000005]
- Orquestração de Pipeline & Execução = $0,455
  - Operações Executadas = 0,001*6 = 0,006 [1 run = $1/1000 = 0,001]
  - Atividades de Movimento de Dados = $0.333 (Prostado por 10 minutos de tempo de execução. $0,25/hora no tempo de funcionamento da integração Azure)
  - Atividade do gasoduto = $0.116 (Prostado para 7 minutos de tempo de execução. $1/hora no tempo de execução da integração Azure)

> [!NOTE]
> Estes preços são apenas para fins.

**FAQ**

P: Se eu gostaria de realizar mais de 50 atividades de pipeline, estas atividades podem ser executadas simultaneamente?

R: Serão permitidas atividades de gasodutos simultâneos Max 50.  A atividade do 51º gasoduto será em fila até que seja aberta uma "ranhura livre". O mesmo para a atividade externa. Serão permitidas atividades externas simultâneas no máximo 800.

## <a name="next-steps"></a>Passos seguintes

Agora que entende os preços da Azure Data Factory, pode começar!

- [Criar uma fábrica de dados utilizando a UI da Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Introdução ao Azure Data Factory](introduction.md)

- [Autoria visual na Azure Data Factory](author-visually.md)
