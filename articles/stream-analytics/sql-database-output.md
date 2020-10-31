---
title: Saída da Base de Dados Azure SQL da Azure Stream Analytics
description: Este artigo descreve a Base de Dados Azure SQL como uma saída para o Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 9d5ddb508740cf5fec670d258926419512e3d549
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129835"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Saída da Base de Dados Azure SQL da Azure Stream Analytics

Pode utilizar [a Base de Dados Azure SQL](https://azure.microsoft.com/services/sql-database/) como uma saída para dados de natureza relacional ou para aplicações que dependem do conteúdo alojado numa base de dados relacional. Os empregos do Azure Stream Analytics escrevem para uma tabela existente na Base de Dados SQL. O esquema de mesa deve corresponder exatamente aos campos e aos seus tipos na produção do seu trabalho. Também pode especificar [o Azure Synapse Analytics](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) como uma saída através da opção de saída SQL Database. Para aprender sobre formas de melhorar a produção de escrita, consulte o [Stream Analytics com Azure SQL Database como](stream-analytics-sql-output-perf.md) artigo de saída.

Também pode utilizar [a Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) como saída. Tem de [configurar o ponto final público em SQL Managed Instance](../azure-sql/managed-instance/public-endpoint-configure.md) e, em seguida, configurar manualmente as seguintes definições no Azure Stream Analytics. A máquina virtual Azure que executa o SQL Server com uma base de dados anexada também é suportada configurando manualmente as definições abaixo.

## <a name="output-configuration"></a>Configuração de saída

A tabela que se segue lista os nomes dos imóveis e a sua descrição para a criação de uma saída sql Database.

| Nome da propriedade | Description |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para esta base de dados. |
| Base de Dados | O nome da base de dados para onde está a enviar a sua produção. |
| Nome do servidor | O nome lógico do servidor SQL ou nome de instância gerido. Para a SQL Managed Instance, é necessário especificar a porta 3342. Por exemplo, *sampleserver.public.database.windows.net,3342* |
| Nome de utilizador | O nome de utilizador que tem acesso à base de dados. Stream Analytics suporta apenas a autenticação SQL. |
| Palavra-passe | A senha para ligar à base de dados. |
| Tabela | O nome da mesa onde a saída está escrita. O nome da mesa é sensível a maiôs. O esquema desta tabela deve corresponder exatamente ao número de campos e aos seus tipos que a sua saída de trabalho gera. |
|Herdar o esquema de partição| Uma opção para herdar o esquema de partição do seu passo de consulta anterior, para permitir topologia totalmente paralela com vários escritores à mesa. Para obter mais informações, consulte [a saída Azure Stream Analytics para a Base de Dados Azure SQL](stream-analytics-sql-output-perf.md).|
|Contagem de lotes max| O limite superior recomendado para o número de registos enviados com cada transação de inserção a granel.|

Existem dois adaptadores que permitem a saída do Azure Stream Analytics para a Azure Synapse Analytics (anteriormente SQL Data Warehouse): SQL Database e Azure Synapse. Recomendamos que escolha o adaptador Azure Synapse Analytics em vez do adaptador SQL Database se alguma das seguintes condições for verdadeira:

* **Produção** : Se a sua produção esperada agora ou no futuro for superior a 10MB/seg, utilize a opção de saída Azure Synapse para um melhor desempenho.

* **Entradas Partições** : Se tiver oito ou mais divisórias de entrada, utilize a opção de saída Azure Synapse para uma melhor escala.

## <a name="partitioning"></a>Criação de partições

A partição tem de ser ativada e baseia-se na cláusula PARTITION BY na consulta. Quando a opção de partição herdada está ativada, segue a partição de entrada para [consultas totalmente paralizáveis](stream-analytics-scale-jobs.md). Para saber mais sobre como obter um melhor desempenho de produção de escrita quando estiver a carregar dados na Base de Dados Azure SQL, consulte [a saída Azure Stream Analytics para a Base de Dados Azure SQL](stream-analytics-sql-output-perf.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída

Pode configurar o tamanho máximo da mensagem utilizando a **contagem de lotes Max** . O máximo de predefinição é de 10.000 e o mínimo de predefinição é de 100 linhas por cada inserção a granel. Para mais informações, consulte [os limites do Azure SQL](../azure-sql/database/resource-limits-logical-server.md). Cada lote é inicialmente inserido a granel com a contagem máxima de lote. O lote é dividido ao meio (até à contagem mínima de lotes) com base em erros retripáveis do SQL.

## <a name="next-steps"></a>Passos seguintes

* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Crie um trabalho Azure Stream Analytics usando um modelo ARM](quick-create-azure-resource-manager.md)
* [Quickstart: Criar um trabalho stream analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics em Código de Estúdio Visual](quick-create-visual-studio-code.md)