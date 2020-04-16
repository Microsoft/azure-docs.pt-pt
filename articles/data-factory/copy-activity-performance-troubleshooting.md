---
title: Desempenho da atividade da cópia de resolução de problemas
description: Saiba como resolver o desempenho da atividade de cópia na Azure Data Factory.
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
ms.openlocfilehash: 6df1903e828c0c4cafa6589d4a85f4016bed893e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414133"
---
# <a name="troubleshoot-copy-activity-performance"></a>Desempenho da atividade da cópia de resolução de problemas

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como resolver problemas na questão do desempenho da atividade na Azure Data Factory. 

Depois de executar uma atividade de cópia, pode recolher o resultado de execução e as estatísticas de desempenho na visualização de monitorização da atividade de [cópia.](copy-activity-monitoring.md) A seguir encontra-se um exemplo.

![Monitorizar detalhes de execução da atividade de cópia](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Sugestões de otimização do desempenho

Em alguns cenários, quando executa uma atividade de cópia na Data Factory, verá "Dicas de **afinação** de desempenho" no topo, como mostra o exemplo acima. As dicas dizem-lhe o estrangulamento identificado pela ADF para esta execução de cópia em particular, juntamente com a sugestão de como aumentar a entrada de cópia. Tente fazer a mudança reordenada e, em seguida, executar a cópia novamente.

Como referência, atualmente as dicas de afinação de desempenho fornecem sugestões para os seguintes casos:

| Categoria              | Sugestões de otimização do desempenho                                      |
| --------------------- | ------------------------------------------------------------ |
| Data store específico   | Carregar dados no **Azure Synpase Analytics (anteriormente SQL DW)**: sugerir a utilização da declaração PolyBase ou COPY se não for utilizado. |
| &nbsp;                | Copiar dados de/para a Base de **Dados SQL do Azure:** quando o DTU estiver em alta utilização, sugere a atualização para um nível mais elevado. |
| &nbsp;                | Copiar dados de/para **Azure Cosmos DB:** quando a RU estiver em alta utilização, sugerir a atualização para ru maior. |
| &nbsp;                | Ingerir dados da **Amazon Redshift**: sugira a utilização do UNLOAD se não for utilizado. |
| Estrangulamento da loja de dados | Se algumas operações de leitura/escrita forem estranguladas pela loja de dados durante a cópia, sugira que verifique e aumente a taxa de pedido permitida para o armazenamento de dados, ou reduza a carga de trabalho simultânea. |
| Tempo de execução de integração  | Se utilizar um Tempo de Execução de **Integração Auto-hospedado (IR)** e a atividade de cópia aguarda muito tempo na fila até que o IR tenha recursos disponíveis para executar, sugira que se esgote/suba o seu IR. |
| &nbsp;                | Se utilizar um Tempo de Funcionamento de **Integração Azure** que se encontra numa região não ótima, resultando em leitura/escrita lenta, sugira configurar a utilização de um IR noutra região. |
| Tolerância a falhas       | Se configurar a tolerância à falha e saltar linhas incompatíveis resulta num desempenho lento, sugira que os dados de origem e de sumo sejam compatíveis. |
| Cópia encenada           | Se a cópia encenada estiver configurada, mas não útil para o seu par de sumidouros, sugira removê-la. |
| Retomar                | Quando a atividade de cópia é retomada a partir do último ponto de falha, mas por acaso altera a definição de DIU após a execução original, note que a nova definição de DIU não entra em vigor. |

## <a name="understand-copy-activity-execution-details"></a>Compreender detalhes de execução de atividade de cópia

Os detalhes e durações de execução na parte inferior da visão de monitorização da atividade de cópia descreve as fases-chave pelas quais a sua atividade de cópia passa (ver exemplo no início deste artigo), o que é especialmente útil para resolver o desempenho da cópia. O estrangulamento da sua execução de cópia é aquele com maior duração. Consulte a tabela seguinte sobre a definição de cada palco e aprenda a trabalhar na cópia de [Troubleshoot em Atividade](#troubleshoot-copy-activity-on-azure-ir) de cópia seletiva de Azure IR e [Troubleshoot em IR auto-hospedado](#troubleshoot-copy-activity-on-self-hosted-ir) com tais informações.

| Fase           | Descrição                                                  |
| --------------- | ------------------------------------------------------------ |
| Filas           | O tempo decorrido até que a atividade de cópia realmente comece no tempo de execução da integração. |
| Roteiro pré-cópia | O tempo decorrido entre a atividade de cópia a partir do IR e a atividade de cópia terminando a execução do script pré-cópia na loja de dados do lavatório. Aplicar quando configurar o script pré-cópia para pias de base de dados, por exemplo, ao escrever dados na Base de Dados Azure SQL, limpe antes de copiar novos dados. |
| Transferir        | O tempo decorrido entre o final do passo anterior e o IR transferindo todos os dados de origem para afundar. Subpassos em "Transferência" corre em paralelo.<br><br>- **Hora de primeiro passar:** O tempo decorrido entre o final do passo anterior e o momento em que o IR recebe o primeiro byte da loja de dados de origem. Aplica-se a fontes não baseadas em ficheiros.<br>- **Fonte de listagem:** A quantidade de tempo gasto na enumeração de ficheiros de origem ou partições de dados. Este último aplica-se quando configura opções de partição para fontes de base de dados, por exemplo, quando copia dados de bases de dados como o Oracle/SAP HANA/Teradata/Netezza/etc.<br/>-**Leitura a partir da fonte:** A quantidade de tempo gasto na recuperação de dados da loja de dados de origem.<br/>- **Escrever para afundar:** A quantidade de tempo gasto na escrita de dados para afundar a loja de dados. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Atividade de cópia de resolução de problemas no Azure IR

Siga as etapas de [afinação](copy-activity-performance.md#performance-tuning-steps) do Desempenho para planear e realizar o teste de desempenho para o seu cenário. 

Quando o desempenho da atividade de cópia não corresponder às suas expectativas, para resolver a atividade de cópia única em execução no Funcionamento da Integração Azure, se vir dicas de [afinação](#performance-tuning-tips) de desempenho amostradas na vista de monitorização de cópias, aplique a sugestão e tente novamente. Caso contrário, compreenda os detalhes da execução da [atividade de cópia,](#understand-copy-activity-execution-details)verifique qual a fase com **maior** duração e aplique as orientações abaixo para aumentar o desempenho da cópia:

- **"Pre-copy script" experimentou uma longa duração:** significa que o script pré-cópia que corre na base de dados da pia demora muito tempo a terminar. Sintonize a lógica especificada do script pré-cópia para melhorar o desempenho. Se precisar de mais ajuda para melhorar o script, contacte a sua equipa de base de dados.

- **"Transfer - Tempo para primeiro byte" experiente duração**de trabalho longa : significa que a sua consulta de origem demora muito tempo a devolver quaisquer dados. Verifique e otimize a consulta ou servidor. Se precisar de mais ajuda, contacte a sua equipa de lojas de dados.

- **"Transferência - Fonte de listagem" experimentou uma longa duração de trabalho:** significa que enumerar ficheiros de origem ou divisórias de dados de base de dados de origem é lenta.

  - Ao copiar dados da fonte baseada em ficheiros, se utilizar`wildcardFolderPath` o `wildcardFileName` **filtro wildcard** no caminho da pasta ou no nome do ficheiro (ou ), ou utilizar o **último filtro de tempo modificado** (ou),`modifiedDatetimeStart` `modifiedDatetimeEnd`note que esse filtro resultaria na listagem de todos os ficheiros sob a pasta especificada para o lado do cliente e aplicaria o filtro. Tal enumeração de ficheiros pode tornar-se o estrangulamento especialmente quando apenas um pequeno conjunto de ficheiros cumpriu a regra do filtro.

    - Verifique se pode copiar ficheiros com base no caminho ou nome de [ficheiro seleção em data](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Tal forma não traz encargos para o lado da fonte de listagem.

    - Verifique se pode usar o filtro nativo da loja de dados, especificamente "**prefixo**" para Amazon S3 e Azure Blob. O filtro prefixo é um filtro do lado do servidor da loja de dados e teria um desempenho muito melhor.

    - Considere dividir um conjunto de dados simples em vários conjuntos de dados mais pequenos, e deixar que esses trabalhos de cópia sejam executados simultaneamente cada parte de dados. Pode fazê-lo com Lookup/GetMetadata + ForEach + Copy. Consulte [os ficheiros Copy de vários recipientes](solution-template-copy-files-multiple-containers.md) ou emigra dados do Amazon S3 para os modelos de solução [ADLS Gen2](solution-template-migration-s3-azure.md) como exemplo geral.

  - Verifique se a ADF reporta algum erro de estrangulamento na fonte ou se a sua loja de dados está em estado de alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

  - Utilize o Azure IR na mesma ou perto da região da loja de dados de origem.

- **"Transferência - leitura da fonte" vivida longa duração de trabalho:** 

  - Adote as melhores práticas de carregamento de dados específicos do conector, se aplicável. Por exemplo, ao copiar dados da [Amazon Redshift,](connector-amazon-redshift.md)configure para utilizar o Redshift UNLOAD.

  - Verifique se a ADF reporta algum erro de estrangulamento na fonte ou se o seu armazenamento de dados está em alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

  - Verifique a sua fonte de cópia e o padrão de afundar: 

    - Se o seu padrão de cópia suportar mais do que 4 Unidades de Integração de Dados (DIUs) - consulte [esta secção](copy-activity-performance-features.md#data-integration-units) em detalhes, geralmente pode tentar aumentar os DIUs para obter um melhor desempenho. 

    - Caso contrário, considere dividir um único conjunto de dados grandes em vários conjuntos de dados mais pequenos, e deixar esses trabalhos de cópia executados simultaneamente cada parte de dados. Pode fazê-lo com Lookup/GetMetadata + ForEach + Copy. Consulte [ficheiros Copios os ficheiros de vários recipientes](solution-template-copy-files-multiple-containers.md), [migrar dados da Amazon S3 para ADLS Gen2](solution-template-migration-s3-azure.md), ou copiar a granel com [modelos](solution-template-bulk-copy-with-control-table.md) de solução de tabela de controlo como exemplo geral.

  - Utilize o Azure IR na mesma ou perto da região da loja de dados de origem.

- **"Transferência - escrita para afundar" com longa duração de trabalho:**

  - Adote as melhores práticas de carregamento de dados específicos do conector, se aplicável. Por exemplo, ao copiar dados para [o Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (anteriormente SQL DW), utilize a declaração PolyBase ou COPY. 

  - Verifique se a ADF reporta algum erro de estrangulamento na pia ou se o seu armazenamento de dados está em alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

  - Verifique a sua fonte de cópia e o padrão de afundar: 

    - Se o seu padrão de cópia suportar mais do que 4 Unidades de Integração de Dados (DIUs) - consulte [esta secção](copy-activity-performance-features.md#data-integration-units) em detalhes, geralmente pode tentar aumentar os DIUs para obter um melhor desempenho. 

    - Caso contrário, afina gradualmente as [cópias paralelas,](copy-activity-performance-features.md)note que muitas cópias paralelas podem até prejudicar o desempenho.

  - Utilize o Azure IR na mesma ou perto da região da loja de dados.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Atividade de cópia de resolução de problemas em IR auto-hospedado

Siga as etapas de [afinação](copy-activity-performance.md#performance-tuning-steps) do Desempenho para planear e realizar o teste de desempenho para o seu cenário. 

Quando o desempenho da cópia não corresponder às suas expectativas, para resolver a atividade de cópia única em funcionamento no Tempo de Funcionamento da Integração Azure, se vir dicas de [afinação](#performance-tuning-tips) de desempenho mostradas na vista de monitorização de cópias, aplique a sugestão e tente novamente. Caso contrário, compreenda os detalhes da execução da [atividade de cópia,](#understand-copy-activity-execution-details)verifique qual a fase com **maior** duração e aplique as orientações abaixo para aumentar o desempenho da cópia:

- **"Fila" experimentou longa duração:** significa que a atividade de cópia aguarda muito tempo na fila até que o seu IR auto-hospedado tenha recursos para executar. Verifique a capacidade e utilização do IR e [faça escala para cima ou para fora](create-self-hosted-integration-runtime.md#high-availability-and-scalability) de acordo com a sua carga de trabalho.

- **"Transfer - Tempo para primeiro byte" experiente duração**de trabalho longa : significa que a sua consulta de origem demora muito tempo a devolver quaisquer dados. Verifique e otimize a consulta ou servidor. Se precisar de mais ajuda, contacte a sua equipa de lojas de dados.

- **"Transferência - Fonte de listagem" experimentou uma longa duração de trabalho:** significa que enumerar ficheiros de origem ou divisórias de dados de base de dados de origem é lenta.

  - Verifique se a máquina de infravermelhos auto-hospedada tem baixa latência ligada à loja de dados de origem. Se a sua fonte estiver em Azure, pode utilizar [esta ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina de infravermelhos auto-hospedada para a região de Azure, quanto melhor.

  - Ao copiar dados da fonte baseada em ficheiros, se utilizar`wildcardFolderPath` o `wildcardFileName` **filtro wildcard** no caminho da pasta ou no nome do ficheiro (ou ), ou utilizar o **último filtro de tempo modificado** (ou),`modifiedDatetimeStart` `modifiedDatetimeEnd`note que esse filtro resultaria na listagem de todos os ficheiros sob a pasta especificada para o lado do cliente e aplicaria o filtro. Tal enumeração de ficheiros pode tornar-se o estrangulamento especialmente quando apenas um pequeno conjunto de ficheiros cumpriu a regra do filtro.

    - Verifique se pode copiar ficheiros com base no caminho ou nome de [ficheiro seleção em data](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Tal forma não traz encargos para o lado da fonte de listagem.

    - Verifique se pode usar o filtro nativo da loja de dados, especificamente "**prefixo**" para Amazon S3 e Azure Blob. O filtro prefixo é um filtro do lado do servidor da loja de dados e teria um desempenho muito melhor.

    - Considere dividir um conjunto de dados simples em vários conjuntos de dados mais pequenos, e deixar que esses trabalhos de cópia sejam executados simultaneamente cada parte de dados. Pode fazê-lo com Lookup/GetMetadata + ForEach + Copy. Consulte [os ficheiros Copy de vários recipientes](solution-template-copy-files-multiple-containers.md) ou emigra dados do Amazon S3 para os modelos de solução [ADLS Gen2](solution-template-migration-s3-azure.md) como exemplo geral.

  - Verifique se a ADF reporta algum erro de estrangulamento na fonte ou se a sua loja de dados está em estado de alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

- **"Transferência - leitura da fonte" vivida longa duração de trabalho:** 

  - Verifique se a máquina de infravermelhos auto-hospedada tem baixa latência ligada à loja de dados de origem. Se a sua fonte estiver em Azure, pode utilizar [esta ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina de infravermelhos auto-hospedada para as regiões de Azure, quanto melhor.

  - Verifique se a máquina de infravermelhos auto-hospedada tem largura de banda de entrada suficiente para ler e transferir os dados de forma eficiente. Se a sua loja de dados de origem estiver em Azure, pode utilizar [esta ferramenta](https://www.azurespeed.com/Azure/Download) para verificar a velocidade de descarregamento.

  - Verifique a CPU do IR auto-hospedado e a tendência de utilização da memória no portal Azure - > a sua página geral de > de dados. Considere [aumentar/sair o IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) se o uso do CPU for elevado ou a memória disponível for baixa.

  - Adote as melhores práticas de carregamento de dados específicos do conector, se aplicável. Por exemplo:

    - Ao copiar dados da [Oracle](connector-oracle.md#oracle-as-source), [Netezza,](connector-netezza.md#netezza-as-source) [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [Tabela SAP](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub,](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)permitem opções de partilha de dados para copiar dados em paralelo.

    - Ao copiar dados do [HDFS,](connector-hdfs.md)configure para utilizar o DistCp.

    - Ao copiar dados da [Amazon Redshift,](connector-amazon-redshift.md)configure para utilizar o Redshift UNLOAD.

  - Verifique se a ADF reporta qualquer erro de estrangulamento na fonte ou se o seu armazenamento de dados está em alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

  - Verifique a sua fonte de cópia e o padrão de afundar: 

    - Se copiar dados de lojas de dados ativadas por oposição à partilha, considere afinar gradualmente as [cópias paralelas,](copy-activity-performance-features.md)note que muitas cópias paralelas podem até prejudicar o desempenho.

    - Caso contrário, considere dividir um único conjunto de dados grandes em vários conjuntos de dados mais pequenos, e deixar esses trabalhos de cópia executados simultaneamente cada parte de dados. Pode fazê-lo com Lookup/GetMetadata + ForEach + Copy. Consulte [ficheiros Copios os ficheiros de vários recipientes](solution-template-copy-files-multiple-containers.md), [migrar dados da Amazon S3 para ADLS Gen2](solution-template-migration-s3-azure.md), ou copiar a granel com [modelos](solution-template-bulk-copy-with-control-table.md) de solução de tabela de controlo como exemplo geral.

- **"Transferência - escrita para afundar" com longa duração de trabalho:**

  - Adote as melhores práticas de carregamento de dados específicos do conector, se aplicável. Por exemplo, ao copiar dados para [o Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (anteriormente SQL DW), utilize a declaração PolyBase ou COPY. 

  - Verifique se a máquina de infravermelhos auto-hospedada tem baixa latência ligada à loja de dados do lavatório. Se a pia estiver em Azure, pode utilizar [esta ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina de infravermelhos auto-hospedada para a região de Azure, quanto melhor.

  - Verifique se a máquina de infravermelhos auto-hospedada tem largura de banda de saída suficiente para transferir e escrever os dados de forma eficiente. Se a sua loja de dados estiver em Azure, pode utilizar [esta ferramenta](https://www.azurespeed.com/Azure/UploadLargeFile) para verificar a velocidade de carregamento.

  - Verifique se o CPU do IR auto-hospedado e a tendência de utilização da memória no portal Azure -> a sua página geral de > fábrica de dados. Considere [aumentar/sair o IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) se o uso do CPU for elevado ou a memória disponível for baixa.

  - Verifique se a ADF reporta algum erro de estrangulamento na pia ou se o seu armazenamento de dados está em alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

  - Considere afinar gradualmente as [cópias paralelas,](copy-activity-performance-features.md)note que muitas cópias paralelas podem até prejudicar o desempenho.

## <a name="other-references"></a>Outras referências

Aqui está a monitorização de desempenho e referências de afinação para algumas das lojas de dados suportadas:

* Armazenamento Azure Blob: Metas de [escalabilidade e desempenho para armazenamento blob](../storage/blobs/scalability-targets.md) e performance e lista de verificação de [escalabilidade para armazenamento Blob](../storage/blobs/storage-performance-checklist.md).
* Armazenamento de mesa azul: Metas de [escalabilidade e desempenho para armazenamento](../storage/tables/scalability-targets.md) de mesa e performance e lista de verificação de [escalabilidade para armazenamento](../storage/tables/storage-performance-checklist.md)de mesa .
* Base de dados Azure SQL: Pode [monitorizar o desempenho](../sql-database/sql-database-single-database-monitor.md) e verificar a percentagem da Unidade de Transações de Bases de Dados (DTU).
* Armazém de Dados Azure SQL: A sua capacidade é medida em Unidades de Armazém de Dados (DWUs). Ver Gerir a potência da computação no Armazém de [Dados Azure SQL (visão geral)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Níveis de desempenho em Azure Cosmos DB](../cosmos-db/performance-levels.md).
* No local, SQL Server: [Monitor e sintonização para desempenho](https://msdn.microsoft.com/library/ms189081.aspx).
* Servidor de ficheiros no local: [Sintonização de desempenho para servidores](https://msdn.microsoft.com/library/dn567661.aspx)de ficheiros .

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Copiar guia de desempenho e escalabilidade da atividade](copy-activity-performance.md)
- [Copiar funcionalidades de otimização de desempenho de atividade](copy-activity-performance-features.md)
- [Utilize a Azure Data Factory para migrar dados do seu lago de dados ou armazém de dados para o Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)
