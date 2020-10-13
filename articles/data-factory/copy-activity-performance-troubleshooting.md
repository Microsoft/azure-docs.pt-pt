---
title: Desempenho da atividade da cópia de resolução de problemas
description: Saiba como resolver o desempenho da atividade da cópia na Azure Data Factory.
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
ms.date: 06/10/2020
ms.openlocfilehash: d464124c6841cb2e3186d521b93d7ae08f94c9e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440529"
---
# <a name="troubleshoot-copy-activity-performance"></a>Desempenho da atividade da cópia de resolução de problemas

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como resolver problemas de desempenho da atividade de cópia na Azure Data Factory. 

Depois de executar uma atividade de cópia, pode recolher os resultados de execução e as estatísticas de desempenho na [visualização de monitorização da atividade](copy-activity-monitoring.md) de cópia. A seguir encontra-se um exemplo.

![Monitorize detalhes da atividade da cópia](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Sugestões de otimização do desempenho

Em alguns cenários, quando executar uma atividade de cópia na Data Factory, verá **"Dicas de afinação** de desempenho" no topo, como mostra o exemplo acima. As dicas dizem-lhe o estrangulamento identificado pela ADF para esta cópia em particular, juntamente com sugestões sobre como aumentar o rendimento da cópia. Tente fazer a mudança recomued, e depois volte a executar a cópia.

Como referência, atualmente as dicas de afinação de desempenho fornecem sugestões para os seguintes casos:

| Categoria              | Sugestões de otimização do desempenho                                      |
| --------------------- | ------------------------------------------------------------ |
| Data Store específico   | Carregar dados em **Azure Synpase Analytics (anteriormente SQL DW)**: sugerir a utilização da declaração PolyBase ou COPY se não for utilizado. |
| &nbsp;                | Copiar dados de/para **Azure SQL Database:** quando o DTU estiver em alta utilização, sugira a atualização para um nível mais elevado. |
| &nbsp;                | Copiando dados de/para **Azure Cosmos DB:** quando ru estiver em alta utilização, sugira upgrade para RU maior. |
|                       | Copiar dados da **Tabela SAP:** ao copiar uma grande quantidade de dados, sugira aproveitar a opção de partição do conector SAP para ativar a carga paralela e aumentar o número máximo de partição. |
| &nbsp;                | Ingerir dados da **Amazon Redshift**: sugerir usar UNLOAD se não for usado. |
| Estrangulamento da loja de dados | Se algumas operações de leitura/escrita forem estranguladas pela loja de dados durante a cópia, sugira verificar e aumentar a taxa de pedido permitida para a loja de dados, ou reduzir a carga de trabalho simultânea. |
| Tempo de execução da integração  | Se utilizar um **Tempo de Execução de Integração Auto-hospedado (IR)** e a atividade de cópias aguardar muito tempo na fila até que o IR tenha recursos disponíveis para executar, sugira que escalone o seu IR. |
| &nbsp;                | Se utilizar um Tempo de **Execução de Integração Azure** que se encontra numa região não ideal, resultando em leitura/escrita lenta, sugira configurar a utilização de um IR noutra região. |
| Tolerância a falhas       | Se configurar a tolerância a falhas e saltar linhas incompatíveis resulta num desempenho lento, sugerir que os dados de origem e de lavatório são compatíveis. |
| Cópia encenada           | Se a cópia encenada estiver configurada, mas não for útil para o seu par de lava-água, sugira que a retire. |
| Retomar                | Quando a atividade de cópia é retomada a partir do último ponto de falha, mas por acaso altera a definição de DIU após a execução original, note que a nova definição de DIU não faz efeito. |

## <a name="understand-copy-activity-execution-details"></a>Compreender detalhes da execução da atividade de cópia

Os detalhes e durações de execução na parte inferior da visualização de monitorização da atividade da cópia descrevem as fases-chave pela qual a sua atividade de cópia passa (ver exemplo no início deste artigo), o que é especialmente útil para resolver problemas no desempenho da cópia. O estrangulamento da sua cópia é o que tem a maior duração. Consulte a tabela seguinte na definição de cada etapa e aprenda a lidar com a atividade de [cópia de resolução de problemas na](#troubleshoot-copy-activity-on-azure-ir) atividade de cópia Azure IR e [Troubleshoot copy em IR auto-hospedado](#troubleshoot-copy-activity-on-self-hosted-ir) com tais informações.

| Fase           | Descrição                                                  |
| --------------- | ------------------------------------------------------------ |
| Fila           | O tempo decorrido até que a atividade da cópia realmente comece no tempo de integração. |
| Script pré-cópia | O tempo decorrido entre a atividade de cópia a partir do IR e a atividade de cópia terminando a execução do script pré-cópia na loja de dados do lavatório. Aplicar quando configurar o script pré-cópia para lavatórios de base de dados, por exemplo, ao escrever dados na Base de Dados Azure SQL, limpe antes de copiar novos dados. |
| Transferir        | O tempo decorrido entre o final do escalão anterior e o IR transferindo todos os dados de fonte para afundar. <br/>Note que os sub-passos em transferência são executados em paralelo, e algumas operações não são mostradas agora, por exemplo, o formato de ficheiro de parsing/geração.<br><br/>- **Hora de primeiro byte:** O tempo decorrido entre o final do escalão anterior e o momento em que o IR recebe o primeiro byte da loja de dados de origem. Aplica-se a fontes não baseadas em ficheiros.<br>- **Fonte de listagem:** A quantidade de tempo gasto em enumerar ficheiros de origem ou divisórias de dados. Este último aplica-se quando configura opções de partição para fontes de base de dados, por exemplo, quando copia dados de bases de dados como o Oracle/SAP HANA/Teradata/Netezza/etc.<br/>-**Leitura da fonte:** A quantidade de tempo gasto na recuperação de dados da loja de dados de origem.<br/>- **Escrevendo para afundar:** A quantidade de tempo gasto em escrever dados para afundar a loja de dados. Note que alguns conectores não têm esta métrica no momento, incluindo Azure Cognitive Search, Azure Data Explorer, Azure Table storage, Oracle, SQL Server, Common Data Service, Dynamics 365, Dynamics CRM, Salesforce/Salesforce Service Cloud. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Atividade de cópia de resolução de problemas no Azure IR

Siga os [passos de afinação](copy-activity-performance.md#performance-tuning-steps) de desempenho para planear e realizar o teste de desempenho para o seu cenário. 

Quando o desempenho da atividade da cópia não corresponder às suas expectativas, para resolver problemas com a atividade de cópia única em execução no Tempo de Execução da Integração Azure, se vir [as dicas de afinação](#performance-tuning-tips) de desempenho apresentadas na vista de monitorização da cópia, aplique a sugestão e tente novamente. Caso contrário, [compreenda os detalhes da execução da atividade](#understand-copy-activity-execution-details)da cópia, verifique qual a fase com a duração **mais longa** e aplique as orientações abaixo para aumentar o desempenho da cópia:

- **O "script pré-cópia" experimentou uma longa duração:** significa que o script pré-cópia que está a ser classificado na base de dados da pia demora muito tempo a terminar. Sintonize a lógica de script pré-cópia especificada para melhorar o desempenho. Se precisar de mais ajuda para melhorar o script, contacte a sua equipa de base de dados.

- **"Transfer - Tempo para primeiro byte" experimentou longa duração de trabalho**: significa que a sua consulta de origem demora muito tempo a devolver quaisquer dados. Verifique e otimize a consulta ou o servidor. Se precisar de mais ajuda, contacte a sua equipa de loja de dados.

- **"Transfer - Listing source" experimentou uma longa duração de trabalho:** significa que enumerar ficheiros de origem ou partições de dados de base de dados de origem é lento.
  - Ao copiar dados de origem baseada em ficheiros, se utilizar **o filtro wildcard** na trajetória da pasta ou no nome do ficheiro `wildcardFolderPath` (ou ) ou utilizar o `wildcardFileName` último filtro de tempo modificado do **ficheiro** ( ou ), note que tal filtro resultaria na cópia da atividade que lista todos `modifiedDatetimeStart` os `modifiedDatetimeEnd` ficheiros sob a pasta especificada para o lado do cliente e, em seguida, aplique o filtro. Tal enumeração de ficheiros pode tornar-se o estrangulamento, especialmente quando apenas um pequeno conjunto de ficheiros cumpriu a regra do filtro.

    - Verifique se pode [copiar ficheiros com base no caminho ou nome](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)do ficheiro dividido em data . Tal forma não traz encargos para a listagem do lado da fonte.

    - Verifique se pode usar o filtro nativo da data store, especificamente "**prefixo**" para Amazon S3 e Azure Blob. O filtro prefixo é um filtro do lado do servidor da loja de dados e teria um desempenho muito melhor.

    - Considere dividir um único conjunto de dados de grande dimensão em vários conjuntos de dados menores, e deixar que esses trabalhos de cópia corram simultaneamente cada uma das partes de dados. Pode fazê-lo com Lookup/GetMetadata + ForEach + Copy. Consulte [os ficheiros Copy de vários contentores](solution-template-copy-files-multiple-containers.md) ou migrar dados do Amazon S3 para os modelos de solução [ADLS Gen2](solution-template-migration-s3-azure.md) como exemplo geral.

  - Verifique se a ADF reporta algum erro de estrangulamento na fonte ou se a sua loja de dados está em estado de alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

  - Utilize o Azure IR na mesma ou perto da sua região de loja de dados de origem.

- **"Transferência - leitura de fonte" experimentou longa duração de trabalho:** 

  - Adote as melhores práticas de carregamento de dados específicos do conector, se for aplicável. Por exemplo, ao copiar dados da [Amazon Redshift,](connector-amazon-redshift.md)configuure-se a utilizar o Redshift UNLOAD.

  - Verifique se a ADF reporta algum erro de estrangulamento na fonte ou se a sua loja de dados está em alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

  - Verifique a sua fonte de cópia e o padrão da pia: 

    - Se o seu padrão de cópia suportar mais de 4 Unidades de Integração de Dados (DIS) - consulte [esta secção](copy-activity-performance-features.md#data-integration-units) em detalhes, geralmente pode tentar aumentar os DIUs para obter um melhor desempenho. 

    - Caso contrário, considere dividir um único conjunto de dados de grande dimensão em vários conjuntos de dados mais pequenos, e deixar que esses trabalhos de cópia corram simultaneamente cada uma das partes de dados. Pode fazê-lo com Lookup/GetMetadata + ForEach + Copy. Consulte [os ficheiros Copy de vários contentores](solution-template-copy-files-multiple-containers.md), [Migrar dados do Amazon S3 para a ADLS Gen2](solution-template-migration-s3-azure.md), ou [copiar a granel com modelos](solution-template-bulk-copy-with-control-table.md) de tabela de controlo como exemplo geral.

  - Utilize o Azure IR na mesma ou perto da sua região de loja de dados de origem.

- **"Transfer - writing to sink" experimentou longa duração de trabalho:**

  - Adote as melhores práticas de carregamento de dados específicos do conector, se for aplicável. Por exemplo, ao copiar dados para [a Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (anteriormente SQL DW), utilize a declaração PolyBase ou COPY. 

  - Verifique se a ADF reporta algum erro de estrangulamento na pia ou se a sua loja de dados está em alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

  - Verifique a sua fonte de cópia e o padrão da pia: 

    - Se o seu padrão de cópia suportar mais de 4 Unidades de Integração de Dados (DIS) - consulte [esta secção](copy-activity-performance-features.md#data-integration-units) em detalhes, geralmente pode tentar aumentar os DIUs para obter um melhor desempenho. 

    - Caso contrário, afinar gradualmente as [cópias paralelas,](copy-activity-performance-features.md)note que muitas cópias paralelas podem até prejudicar o desempenho.

  - Utilize o Azure IR na mesma ou perto da sua região de loja de dados de lavatórios.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Atividade de cópia de resolução de problemas em IR auto-hospedado

Siga os [passos de afinação](copy-activity-performance.md#performance-tuning-steps) de desempenho para planear e realizar o teste de desempenho para o seu cenário. 

Quando o desempenho da cópia não corresponder às suas expectativas, para resolver problemas com a atividade de cópia única em execução no Tempo de Execução da Integração Azure, se vir [as dicas de afinação](#performance-tuning-tips) de desempenho apresentadas na vista de monitorização da cópia, aplique a sugestão e tente novamente. Caso contrário, [compreenda os detalhes da execução da atividade](#understand-copy-activity-execution-details)da cópia, verifique qual a fase com a duração **mais longa** e aplique as orientações abaixo para aumentar o desempenho da cópia:

- **"Fila" experimentou longa duração:** significa que a atividade da cópia aguarda muito tempo na fila até que o seu IR auto-hospedado tenha recursos para executar. Verifique a capacidade e utilização do IR e [escale de](create-self-hosted-integration-runtime.md#high-availability-and-scalability) acordo com a sua carga de trabalho.

- **"Transfer - Tempo para primeiro byte" experimentou longa duração de trabalho**: significa que a sua consulta de origem demora muito tempo a devolver quaisquer dados. Verifique e otimize a consulta ou o servidor. Se precisar de mais ajuda, contacte a sua equipa de loja de dados.

- **"Transfer - Listing source" experimentou uma longa duração de trabalho:** significa que enumerar ficheiros de origem ou partições de dados de base de dados de origem é lento.

  - Verifique se a máquina de infravermelhos auto-hospedada tem baixa ligação de latência à loja de dados de origem. Se a sua fonte estiver em Azure, pode utilizar [esta ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina de infravermelhos auto-hospedada para a região de Azure, quanto menos melhor.

  - Ao copiar dados de origem baseada em ficheiros, se utilizar **o filtro wildcard** na trajetória da pasta ou no nome do ficheiro `wildcardFolderPath` (ou ) ou utilizar o `wildcardFileName` último filtro de tempo modificado do **ficheiro** ( ou ), note que tal filtro resultaria na cópia da atividade que lista todos `modifiedDatetimeStart` os `modifiedDatetimeEnd` ficheiros sob a pasta especificada para o lado do cliente e, em seguida, aplique o filtro. Tal enumeração de ficheiros pode tornar-se o estrangulamento, especialmente quando apenas um pequeno conjunto de ficheiros cumpriu a regra do filtro.

    - Verifique se pode [copiar ficheiros com base no caminho ou nome](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)do ficheiro dividido em data . Tal forma não traz encargos para a listagem do lado da fonte.

    - Verifique se pode usar o filtro nativo da data store, especificamente "**prefixo**" para Amazon S3 e Azure Blob. O filtro prefixo é um filtro do lado do servidor da loja de dados e teria um desempenho muito melhor.

    - Considere dividir um único conjunto de dados de grande dimensão em vários conjuntos de dados menores, e deixar que esses trabalhos de cópia corram simultaneamente cada uma das partes de dados. Pode fazê-lo com Lookup/GetMetadata + ForEach + Copy. Consulte [os ficheiros Copy de vários contentores](solution-template-copy-files-multiple-containers.md) ou migrar dados do Amazon S3 para os modelos de solução [ADLS Gen2](solution-template-migration-s3-azure.md) como exemplo geral.

  - Verifique se a ADF reporta algum erro de estrangulamento na fonte ou se a sua loja de dados está em estado de alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

- **"Transferência - leitura de fonte" experimentou longa duração de trabalho:** 

  - Verifique se a máquina de infravermelhos auto-hospedada tem baixa ligação de latência à loja de dados de origem. Se a sua fonte estiver em Azure, pode utilizar [esta ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina de infravermelhos auto-hospedada para as regiões de Azure, quanto menos melhor.

  - Verifique se a máquina de INFRAVERMELHO auto-hospedada tem largura de banda de entrada suficiente para ler e transferir os dados de forma eficiente. Se a sua loja de dados de origem estiver em Azure, pode utilizar [esta ferramenta](https://www.azurespeed.com/Azure/Download) para verificar a velocidade de descarregamento.

  - Consulte a tendência de utilização do CPU e da memória do AUTO-hospedado no portal Azure -> a sua página geral de > da sua fábrica de dados. Considere [aumentar/eliminar](create-self-hosted-integration-runtime.md#high-availability-and-scalability) o IR se a utilização do CPU for alta ou a memória disponível for baixa.

  - Adote as melhores práticas de carregamento de dados específicos do conector, se for aplicável. Por exemplo:

    - Ao copiar dados da [Oracle](connector-oracle.md#oracle-as-source), [Netezza,](connector-netezza.md#netezza-as-source) [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP Table](connector-sap-table.md#sap-table-as-source)e SAP [Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)permita que as opções de partição de dados copiem dados em paralelo.

    - Ao copiar dados do [HDFS,](connector-hdfs.md)configuure-se para utilizar o DistCp.

    - Ao copiar dados da [Amazon Redshift,](connector-amazon-redshift.md)configuure-se a utilizar o Redshift UNLOAD.

  - Verifique se a ADF reporta algum erro de estrangulamento na fonte ou se a sua loja de dados está em alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

  - Verifique a sua fonte de cópia e o padrão da pia: 

    - Se copiar dados de lojas de dados ativadas por opção de partição, considere afinar gradualmente as [cópias paralelas](copy-activity-performance-features.md), note que muitas cópias paralelas podem até prejudicar o desempenho.

    - Caso contrário, considere dividir um único conjunto de dados de grande dimensão em vários conjuntos de dados mais pequenos, e deixar que esses trabalhos de cópia corram simultaneamente cada uma das partes de dados. Pode fazê-lo com Lookup/GetMetadata + ForEach + Copy. Consulte [os ficheiros Copy de vários contentores](solution-template-copy-files-multiple-containers.md), [Migrar dados do Amazon S3 para a ADLS Gen2](solution-template-migration-s3-azure.md), ou [copiar a granel com modelos](solution-template-bulk-copy-with-control-table.md) de tabela de controlo como exemplo geral.

- **"Transfer - writing to sink" experimentou longa duração de trabalho:**

  - Adote as melhores práticas de carregamento de dados específicos do conector, se for aplicável. Por exemplo, ao copiar dados para [a Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (anteriormente SQL DW), utilize a declaração PolyBase ou COPY. 

  - Verifique se a máquina de infravermelhos auto-hospedada tem baixa ligação de latência à loja de dados de sumidouro. Se a pia estiver em Azure, pode utilizar [esta ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina de infravermelhos auto-hospedada para a região de Azure, quanto menos melhor.

  - Verifique se a máquina de infravermelhos auto-hospedada tem largura de banda de saída suficiente para transferir e escrever os dados de forma eficiente. Se a sua loja de dados de lavatório estiver em Azure, pode utilizar [esta ferramenta](https://www.azurespeed.com/Azure/UploadLargeFile) para verificar a velocidade de upload.

  - Verifique se o CPU auto-hospedado e a tendência de utilização da memória no portal Azure -> a sua base de dados - > página geral. Considere [aumentar/eliminar](create-self-hosted-integration-runtime.md#high-availability-and-scalability) o IR se a utilização do CPU for alta ou a memória disponível for baixa.

  - Verifique se a ADF reporta algum erro de estrangulamento na pia ou se a sua loja de dados está em alta utilização. Em caso afirmativo, reduza as suas cargas de trabalho na loja de dados ou tente contactar o administrador da sua loja de dados para aumentar o limite de estrangulamento ou o recurso disponível.

  - Considere afinar gradualmente as [cópias paralelas,](copy-activity-performance-features.md)note que muitas cópias paralelas podem até prejudicar o desempenho.

## <a name="other-references"></a>Outras referências

Aqui está a monitorização de desempenho e afinação de referências para algumas das lojas de dados suportadas:

* Armazenamento Azure Blob: [Objetivos de escalabilidade e desempenho para armazenamento blob](../storage/blobs/scalability-targets.md) e [performance e escalabilidade lista de verificação para armazenamento blob](../storage/blobs/storage-performance-checklist.md).
* Armazenamento da tabela azul: [Metas de escalabilidade e desempenho para armazenamento de mesa](../storage/tables/scalability-targets.md) e performance e lista de [verificação de escala para armazenamento de mesa](../storage/tables/storage-performance-checklist.md).
* Base de Dados Azure SQL: Pode [monitorizar o desempenho](../sql-database/sql-database-single-database-monitor.md) e verificar a percentagem de Transações de Base de Dados (DTU).
* Azure Synapse Analytics (anteriormente SQL Data Warehouse): A sua capacidade é medida em Unidades de Armazém de Dados (DWUs). Consulte [a potência de computação em Azure Synapse Analytics (Visão geral)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Níveis de desempenho em Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server: [Monitor e sintonizar para o desempenho](https://msdn.microsoft.com/library/ms189081.aspx).
* Servidor de ficheiros no local: [Ajuste de desempenho para servidores de ficheiros](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Copiar o desempenho da atividade e o guia de escalabilidade](copy-activity-performance.md)
- [Copiar funcionalidades de otimização do desempenho da atividade](copy-activity-performance-features.md)
- [Utilize a Azure Data Factory para migrar dados do seu lago de dados ou armazém de dados para Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)
