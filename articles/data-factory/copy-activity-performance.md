---
title: Copiar guia de desempenho e escalabilidade da atividade
description: Conheça os factores-chave que afetam o desempenho do movimento de dados na Azure Data Factory quando utiliza a atividade da cópia.
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
ms.date: 03/11/2020
ms.openlocfilehash: 231b0d77dc441e70dc0ec8de313291bb6b4f9292
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261401"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Copiar guia de desempenho e escalabilidade da atividade

> [!div class="op_single_selector" title1="Selecione a versão da Azure Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-activity-performance.md)
> * [Versão atual](copy-activity-performance.md)

Quer pretenda realizar uma migração de dados em larga escala do data lake ou do armazém de dados da empresa (EDW) para o Azure, ou se pretende ingerir dados em escala de diferentes fontes para OT para análise de big data, é fundamental alcançar o melhor desempenho e escalabilidade.  A Azure Data Factory fornece um mecanismo performativo, resiliente e rentável para ingerir dados em escala, tornando-o um ótimo ajuste para engenheiros de dados que procuram construir oleodutos de ingestão de dados altamente performantes e escaláveis.

Depois de ler este artigo, poderá responder às seguintes perguntas:

- Que nível de desempenho e escalabilidade posso conseguir usando a atividade de cópia ADF para cenários de migração de dados e ingestão de dados?

- Que medidas devo tomar para afinar o desempenho da atividade de cópia seletiva?
- Que botões de otimização de perf aADF posso utilizar para otimizar o desempenho para uma única execução de atividade de cópia?
- Que outros fatores fora da ADF devem considerar na otimização do desempenho da cópia?

> [!NOTE]
> Se não estiver familiarizado com a atividade da cópia em geral, consulte a [visão geral](copy-activity-overview.md) da atividade da cópia antes de ler este artigo.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Desempenho de cópia e escalabilidade alcançável usando ADF

A ADF oferece uma arquitetura sem servidores que permite o paralelismo a diferentes níveis, o que permite que os desenvolvedores construam oleodutos para utilizar totalmente a largura de banda da sua rede, bem como o armazenamento de IOPS e largura de banda para maximizar a produção de movimento de dados para o seu ambiente.  Isto significa que a produção que pode obter pode ser estimada medindo o rendimento mínimo oferecido pela loja de dados de origem, a loja de dados de destino e a largura de banda da rede entre a fonte e o destino.  A tabela abaixo calcula a duração da cópia com base no tamanho dos dados e no limite de largura de banda para o seu ambiente. 

| Tamanho dos dados / <br/> largura de banda | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 horas    | 2,3 horas   | 0,5 horas   | 0,2 horas  | 0,05 horas | 0,02 horas | 0,0 horas   |
| **1 TB**                    | 46,6 horas   | 23,3 horas  | 4,7 horas   | 2,3 horas  | 0,5 horas  | 0,2 horas  | 0,05 horas  |
| **10 TB**                   | 19,4 dias  | 9,7 dias  | 1,9 dias  | 0,9 dias | 0,2 dias | 0,1 dias | 0,02 dias |
| **100 TB**                  | 194.2 dias | 97,1 dias | 19,4 dias | 9,7 dias | 1,9 dias | 1 dia    | 0,2 dias  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3.2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647.3 mo   | 323.6 mo  | 64,7 mo   | 31.6 mo  | 6,5 mo   | 3.2 mo   | 0,6 mo    |

A cópia ADF é escalável a diferentes níveis:

![como as escalas de cópia ADF](media/copy-activity-performance/adf-copy-scalability.png)

- O fluxo de controlo ADF pode iniciar múltiplas atividades de cópia em paralelo, por exemplo, utilizando [Para cada loop](control-flow-for-each-activity.md).
- Uma única atividade de cópia pode tirar partido dos recursos de computação escaláveis: ao utilizar o Tempo de Execução de Integração Azure, pode especificar [até 256 DIUs](#data-integration-units) para cada atividade de cópia de forma sem servidores; ao utilizar o tempo de funcionamento de integração auto-hospedado, pode aumentar manualmente a máquina ou escalar para várias máquinas[(até 4 nós](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), e uma única atividade de cópia irá dividir o seu ficheiro definido em todos os nós.
- Uma única atividade de cópia lê e escreve para a loja de dados usando múltiplos fios [em paralelo](#parallel-copy).

## <a name="performance-tuning-steps"></a>Passos de afinação de desempenho

Tome estes passos para afinar o desempenho do seu serviço Azure Data Factory com a atividade de cópia.

1. **Pegue um conjunto de dados de teste e estabeleça uma linha de base.** Durante a fase de desenvolvimento, teste o seu pipeline utilizando a atividade de cópia contra uma amostra representativa de dados. O conjunto de dados que escolher deve representar os seus padrões típicos de dados (estrutura de pastas, padrão de ficheiros, esquema de dados, e assim por diante), e é grande o suficiente para avaliar o desempenho da cópia, por exemplo, leva 10 minutos ou mais para a atividade de cópia ser concluída. Recolher detalhes de execução e características de desempenho após [a monitorização](copy-activity-monitoring.md)da atividade de cópia .

2. **Como maximizar o desempenho de uma única atividade de cópia:**

   Para começar, recomendamos que maximize primeiro o desempenho usando uma única atividade de cópia.

   - **Se a atividade de cópia estiver a ser executada num Tempo de Integração Azure:** comece com valores predefinidos para Unidades de [Integração de Dados (DIU)](#data-integration-units) e definições [paralelas de cópias.](#parallel-copy) 

   - **Se a atividade de cópia estiver a ser executada num Tempo de Integração auto-hospedado:** recomendamos que utilize uma máquina dedicada separada do servidor que hospeda a loja de dados para hospedar o tempo de execução da integração. Comece com valores predefinidos para a definição [de cópia paralela](#parallel-copy) e utilizando um único nó para o IR auto-hospedado.  

   Execute uma execução de teste de desempenho e tome nota do desempenho alcançado, bem como dos valores reais utilizados como DIUs e cópias paralelas. Consulte a [monitorização](copy-activity-monitoring.md) da atividade de cópia sobre como recolher os resultados de execução e as definições de desempenho utilizadas, e aprenda a como copiar o desempenho da atividade da [Troubleshoot](copy-activity-performance-troubleshooting.md) para identificar e resolver o estrangulamento. 

   Iterado para realizar ensaios de desempenho adicionais após a resolução de problemas e orientação de afinação. Uma vez que a execução da atividade de cópia única não pode obter uma melhor produção, considere maximizar a produção agregada executando várias cópias simultaneamente referindo-se ao passo 3.


3. **Como maximizar a produção agregada executando várias cópias simultaneamente:**

   Agora que maximizou o desempenho de uma única atividade de cópia, se ainda não atingiu os limites superiores de produção do seu ambiente – rede, loja de dados de origem e loja de dados de destino – pode executar múltiplas atividades de cópia em paralelo utilizando construções de fluxo de controlo ADF como [For Each loop](control-flow-for-each-activity.md). Consulte [ficheiros Copios os ficheiros de vários recipientes](solution-template-copy-files-multiple-containers.md), [migrar dados da Amazon S3 para ADLS Gen2](solution-template-migration-s3-azure.md), ou copiar a granel com [modelos](solution-template-bulk-copy-with-control-table.md) de solução de tabela de controlo como exemplo geral.

5. **Expanda a configuração para todo o seu conjunto de dados.** Quando estiver satisfeito com os resultados da execução e o desempenho, pode expandir a definição e o pipeline para cobrir todo o conjunto de dados.

## <a name="troubleshoot-copy-activity-performance"></a>Desempenho da atividade da cópia de resolução de problemas

Siga as etapas de [afinação](#performance-tuning-steps) do Desempenho para planear e realizar o teste de desempenho para o seu cenário. E aprenda a resolver o problema de cada problema de desempenho de cada executação de cópias na Azure Data Factory a partir do desempenho da atividade da [cópia Troubleshoot](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Copiar funcionalidades de otimização de desempenho

A Azure Data Factory fornece as seguintes funcionalidades de otimização de desempenho:

- [Unidades de Integração de Dados](#data-integration-units)
- [Escalabilidade do tempo de execução da integração auto-hospedada](#self-hosted-integration-runtime-scalability)
- [Cópia paralela](#parallel-copy)
- [Cópia encenada](#staged-copy)

### <a name="data-integration-units"></a>Unidades de Integração de Dados

Uma Unidade de Integração de Dados é uma medida que representa o poder (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade na Azure Data Factory. A Unidade de Integração de Dados aplica-se apenas ao tempo de execução da [integração do Azure,](concepts-integration-runtime.md#azure-integration-runtime)mas não ao tempo de execução da [integração auto-hospedada.](concepts-integration-runtime.md#self-hosted-integration-runtime) [Saiba mais](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Escalabilidade do tempo de execução da integração auto-hospedada

Para hospedar uma carga de trabalho simultânea ou para obter um desempenho mais elevado, pode aumentar ou escalar o Tempo de Execução de Integração Auto-hospedado. [Saiba mais](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Cópia paralela

Pode definir cópiaparalela para indicar o paralelismo que pretende que a atividade da cópia utilize. Pode pensar nesta propriedade como o número máximo de fios dentro da atividade de cópia que lê a partir da sua fonte ou escrever para as suas lojas de dados de pia em paralelo. [Saiba mais](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Cópia encenada

Quando copia dados de uma loja de dados de origem para uma loja de dados de sumidouro, pode optar por utilizar o armazenamento Blob como uma loja de encenação provisória. [Saiba mais](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade da cópia de resolução de problemas](copy-activity-performance-troubleshooting.md)
- [Copiar funcionalidades de otimização de desempenho de atividade](copy-activity-performance-features.md)
- [Utilize a Azure Data Factory para migrar dados do seu lago de dados ou armazém de dados para o Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)
