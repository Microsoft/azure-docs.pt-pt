---
title: Guia de desempenho e de escalabilidade da Atividade de cópia
description: Conheça os factores-chave que afetam o desempenho do movimento de dados na Azure Data Factory quando utilizar a atividade da cópia.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/15/2020
ms.openlocfilehash: 75f9080b43333168802a72e60751eec2a765c6d4
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580816"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guia de desempenho e de escalabilidade da Atividade de cópia

> [!div class="op_single_selector" title1="Selecione a versão da Azure Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-activity-performance.md)
> * [Versão atual](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Por vezes, você quer realizar uma migração de dados em larga escala de data lake ou armazém de dados empresariais (EDW), para Azure. Outras vezes você quer ingerir grandes quantidades de dados, de diferentes fontes para Azure, para análise de big data. Em cada caso, é fundamental alcançar o melhor desempenho e escalabilidade.

A Azure Data Factory (ADF) fornece um mecanismo para ingerir dados. A ADF tem as seguintes vantagens:

* Lida com grandes quantidades de dados
* É altamente performante
* É rentável

Estas vantagens fazem da ADF um excelente ajuste para engenheiros de dados que querem construir oleodutos escaláveis de ingestão de dados que são altamente performantes.

Depois de ler este artigo, poderá responder às seguintes perguntas:

* Que nível de desempenho e escalabilidade posso conseguir utilizando a atividade de cópia da ADF para cenários de migração de dados e ingestão de dados?
* Que passos devo tomar para afinar o desempenho da atividade de cópias ADF?
* Que botões de otimização perf ADF posso utilizar para otimizar o desempenho para uma única execução de atividade de cópia?
* Que outros fatores fora da ADF devem considerar ao otimizar o desempenho da cópia?

> [!NOTE]
> Se não estiver familiarizado com a atividade da cópia em geral, consulte a [visão geral](copy-activity-overview.md) da atividade da cópia antes de ler este artigo.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Desempenho de cópia e escalabilidade alcançável usando ADF

A ADF oferece uma arquitetura sem servidor que permite o paralelismo a diferentes níveis.

Esta arquitetura permite-lhe desenvolver oleodutos que maximizem o movimento de dados para o seu ambiente. Estes gasodutos utilizam plenamente os seguintes recursos:

* Largura de banda de rede entre as lojas de dados de origem e destino
* Operações de entrada/saída de dados de origem ou destino por segundo (IOPS) e largura de banda

Esta utilização completa significa que pode estimar a produção global medindo a produção mínima disponível com os seguintes recursos:

* Loja de dados de origem
* Arquivo de dados de destino
* Largura de banda de rede entre as lojas de dados de origem e destino

O quadro abaixo mostra o cálculo da duração do movimento de dados. A duração de cada célula é calculada com base numa determinada rede e na largura de banda da loja de dados e num dado tamanho de carga útil.

> [!NOTE]
> A duração fornecida abaixo destina-se a representar um desempenho exequível numa solução de integração de dados de ponta a ponta implementada através da ADF, utilizando uma ou mais técnicas de otimização de desempenho descritas nas [funcionalidades de otimização de desempenho do Copy](#copy-performance-optimization-features), incluindo a utilização do ForEach para partição e desova de várias atividades de cópia simultânea. Recomendamos que siga os passos estabelecidos nas [etapas de afinação de desempenho](#performance-tuning-steps) para otimizar o desempenho da cópia para o seu conjunto de dados específico e configuração do sistema. Deve utilizar os números obtidos nos seus testes de afinação de desempenho para planeamento de implantação de produção, planeamento de capacidades e projeção de faturação.

&nbsp;

| Tamanho dos dados / <br/> largura de banda | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27.3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 horas    | 2,3 horas   | 0,5 horas   | 0,2 horas  | 0,05 horas | 0,02 horas | 0,0 horas   |
| **1 TB**                    | 46,6 horas   | 23,3 horas  | 4,7 horas   | 2,3 horas  | 0,5 horas  | 0,2 horas  | 0,05 horas  |
| **10 TB**                   | 19,4 dias  | 9,7 dias  | 1,9 dias  | 0,9 dias | 0,2 dias | 0,1 dias | 0,02 dias |
| **100 TB**                  | 194,2 dias | 97,1 dias | 19,4 dias | 9,7 dias | 1,9 dias | 1 dia    | 0,2 dias  |
| **1 PB**                    | 64.7 mo    | 32.4 mo   | 6.5 mo    | 3.2 mo   | 0.6 mo   | 0.3 mo   | 0.06 mo   |
| **10 PB**                   | 647.3 mo   | 323.6 mo  | 64.7 mo   | 31.6 mo  | 6.5 mo   | 3.2 mo   | 0.6 mo    |
| | |  | | |  | | |

A cópia ADF é escalável a diferentes níveis:

![como as balanças de cópias ADF](media/copy-activity-performance/adf-copy-scalability.png)

* O fluxo de controlo ADF pode iniciar várias atividades de cópia em paralelo, por exemplo, utilizando [para cada laço](control-flow-for-each-activity.md).

* Uma única atividade de cópia pode tirar partido dos recursos de computação escaláveis.
  * Ao utilizar o tempo de execução da integração do Azure (IR), pode especificar [até 256 unidades de integração de dados (DIS)](#data-integration-units) para cada atividade de cópia, de forma sem servidor.
  * Ao utilizar o IR auto-hospedado, pode tomar qualquer uma das seguintes abordagens:
    * Dimensione manualmente a máquina.
    * Dimensionar para várias máquinas[(até 4 nós)](create-self-hosted-integration-runtime.md#high-availability-and-scalability)e uma única atividade de cópia dividirá o seu ficheiro definido em todos os nós.

* Uma única atividade de cópia lê e escreve para a loja de dados utilizando vários fios [em paralelo](#parallel-copy).

## <a name="performance-tuning-steps"></a>Passos de afinação de desempenho

Tome os seguintes passos para afinar o desempenho do seu serviço Azure Data Factory com a atividade de cópia:

1. **Pegue um conjunto de dados de teste e estabeleça uma linha de base.**

    Durante o desenvolvimento, teste o seu oleoduto utilizando a atividade de cópia contra uma amostra de dados representativa. O conjunto de dados que escolher deve representar os seus padrões de dados típicos ao longo dos seguintes atributos:

    * Estrutura de pasta
    * Padrão de arquivo
    * Esquema de dados

    E o seu conjunto de dados deve ser grande o suficiente para avaliar o desempenho da cópia. Um bom tamanho leva pelo menos 10 minutos para a atividade da cópia ser concluída. Recolher detalhes de execução e características de desempenho após [monitorização da atividade da cópia](copy-activity-monitoring.md).

2. **Como maximizar o desempenho de uma única atividade de cópia:**

    Recomendamos que maximize primeiro o desempenho utilizando uma única atividade de cópia.

    * **Se a atividade de cópia estiver a ser executada num tempo de integração _do Azure:_**

        Comece com valores predefinidos para [Unidades de Integração de Dados (DIU)](#data-integration-units) e definições [de cópia paralela.](#parallel-copy)

    * **Se a atividade de cópia estiver a ser executada num tempo de integração _auto-hospedado:_**

        Recomendamos que utilize uma máquina dedicada para hospedar o IR. A máquina deve ser separada do servidor que hospeda a loja de dados. Comece com valores predefinidos para a definição [de cópia paralela](#parallel-copy) e utilize um único nó para o IR auto-hospedado.

    Faça um teste de desempenho. Tome nota do desempenho alcançado. Inclua os valores reais utilizados, tais como DIs e cópias paralelas. Consulte a [monitorização da atividade de cópia](copy-activity-monitoring.md) sobre como recolher resultados de execução e definições de desempenho utilizadas. Saiba como [resolver o desempenho da atividade da cópia](copy-activity-performance-troubleshooting.md) para identificar e resolver o estrangulamento.

    Iterate para realizar ensaios de desempenho adicionais seguindo a orientação de resolução de problemas e afinação. Uma vez que a atividade de cópia única é executada não pode obter melhor produção, considere se maximiza a produção agregada executando várias cópias simultaneamente. Esta opção é discutida na próxima bala numerada.

3. **Como maximizar a produção agregada executando várias cópias simultaneamente:**

    Por esta altura, já maximizou o desempenho de uma única atividade de cópia. Se ainda não alcançou os limites superiores de produção do seu ambiente, pode executar várias atividades de cópia em paralelo. Pode funcionar em paralelo utilizando as construções de fluxo de controlo ADF. Uma dessas construções é o [for cada laço.](control-flow-for-each-activity.md) Para obter mais informações, consulte os seguintes artigos sobre modelos de solução:

    * [Copiar ficheiros de vários contentores](solution-template-copy-files-multiple-containers.md)
    * [Migrar dados da Amazon S3 para a ADLS Gen2](solution-template-migration-s3-azure.md)
    * [Cópia a granel com uma mesa de controlo](solution-template-bulk-copy-with-control-table.md)

4. **Expanda a configuração para todo o conjunto de dados.**

    Quando estiver satisfeito com os resultados e desempenho da execução, pode expandir a definição e o pipeline para cobrir todo o conjunto de dados.

## <a name="troubleshoot-copy-activity-performance"></a>Desempenho da atividade da cópia de resolução de problemas

Siga os [passos de afinação](#performance-tuning-steps) de desempenho para planear e realizar o teste de desempenho para o seu cenário. E aprender a resolver problemas com o problema de desempenho de cada atividade de cópia na Azure Data Factory a partir do desempenho da atividade da [cópia de resolução de problemas](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Copiar funcionalidades de otimização de desempenho

A Azure Data Factory fornece as seguintes funcionalidades de otimização de desempenho:

* [Unidades de Integração de Dados](#data-integration-units)
* [Escalabilidade de tempo de execução de integração auto-hospedada](#self-hosted-integration-runtime-scalability)
* [Cópia paralela](#parallel-copy)
* [Cópia encenada](#staged-copy)

### <a name="data-integration-units"></a>Unidades de Integração de Dados

Uma Unidade de Integração de Dados (DIU) é uma medida que representa o poder de uma única unidade na Azure Data Factory. A energia é uma combinação de CPU, memória e alocação de recursos de rede. O DIU aplica-se apenas ao [tempo de funcionamento da integração do Azure.](concepts-integration-runtime.md#azure-integration-runtime) O DIU não se aplica ao [tempo de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime). [Saiba mais aqui](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Escalabilidade de tempo de execução de integração auto-hospedada

Talvez queira acolher uma carga de trabalho cada vez mais simultânea. Ou talvez queira obter um desempenho mais elevado no seu nível de carga de trabalho atual. Pode aumentar a escala de processamento através das seguintes abordagens:

* Pode _aumentar_ o IR auto-alojado, aumentando o número de [empregos simultâneos](create-self-hosted-integration-runtime.md#scale-up) que podem funcionar num nó.  
A escala só funciona se o processador e a memória do nó estiverem a ser menos do que totalmente utilizados.
* Pode _escalar_ o IR auto-hospedado, adicionando mais nóns (máquinas).

Para obter mais informações, consulte:

* [Funcionalidades de otimização do desempenho da atividade de cópia: Escalabilidade de tempo de execução de integração auto-hospedada](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [Criar e configurar um tempo de integração auto-hospedado: Considerações de escala](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>Cópia paralela

Pode definir a `parallelCopies` propriedade para indicar o paralelismo que pretende que a atividade da cópia seja utilizada. Pense nesta propriedade como o número máximo de fios dentro da atividade da cópia. Os fios funcionam em paralelo. Os fios ou lêem a partir da sua fonte ou escrevem para as suas lojas de dados de lavatórios. [Saiba mais](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Cópia encenada

Uma operação de cópia de dados pode enviar os dados _diretamente_ para a loja de dados da pia. Em alternativa, pode optar por utilizar o armazenamento Blob como uma loja _de preparação provisória._ [Saiba mais](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Passos seguintes

Consulte os outros artigos de atividade de cópia:

* [Visão geral da atividade da cópia](copy-activity-overview.md)
* [Desempenho da atividade da cópia de resolução de problemas](copy-activity-performance-troubleshooting.md)
* [Copiar funcionalidades de otimização do desempenho da atividade](copy-activity-performance-features.md)
* [Utilize a Azure Data Factory para migrar dados do seu lago de dados ou armazém de dados para Azure](data-migration-guidance-overview.md)
* [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)
