---
title: Saída DB do Azure Cosmos da Azure Stream Analytics
description: Este artigo descreve como obter dados de Azure Stream Analytics para Azure Cosmos DB.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 9382ac8dc71d1ccb3a85dc0a7a027c8e99296cc6
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016582"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>Saída DB do Azure Cosmos da Azure Stream Analytics

[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um serviço de base de dados distribuído globalmente que oferece escala elástica ilimitada em todo o mundo, consulta rica e indexação automática sobre modelos de dados schema-agnósticos. Para saber mais sobre as opções de recipientes DB do Azure Cosmos para stream analytics, consulte o [Stream Analytics com Azure Cosmos DB como](stream-analytics-documentdb-output.md) artigo de saída.

A produção de DB Azure Cosmos da Stream Analytics não está atualmente disponível nas regiões Azure China 21Vianet e Azure Germany (T-Systems International).

> [!Note]
> O Azure Stream Analytics só suporta a ligação ao Azure Cosmos DB utilizando o SQL API.
> Outras APIs DB de Azure Cosmos ainda não estão apoiadas. Se apontar o Azure Stream Analytics para as contas DB do Azure Cosmos criadas com outras APIs, os dados podem não estar devidamente armazenados.

A tabela seguinte descreve as propriedades para criar uma saída DB Azure Cosmos.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um pseudónimo para remeter esta saída na sua consulta Stream Analytics. |
| Sink | Azure Cosmos DB. |
| Opção de Importar | Escolha **o Cosmos DB da sua subscrição** ou **forneça manualmente as definições de DB cosmos**.
| ID da Conta | O nome ou ponto final URI da conta DB da Azure Cosmos. |
| Chave da conta | A chave de acesso partilhada para a conta DB da Azure Cosmos. |
| Base de Dados | O nome da base de dados Azure Cosmos DB. |
| Nome do contentor | O nome do recipiente a ser usado, que deve existir em Cosmos DB. Exemplo:  <br /><ul><li> _MyContainer_: Deve existir um recipiente chamado "MyContainer".</li>|
| ID do documento |Opcional. O nome do campo em eventos de saída que são usados para especificar a chave primária em que inserem ou atualizar operações são baseadas.

## <a name="partitioning"></a>Criação de partições

A chave de partição baseia-se na cláusula PARTITION BY na consulta. O número de autores de saída segue a partição de entrada para [consultas totalmente paralelas.](stream-analytics-scale-jobs.md) Stream Analytics converte a chave de divisão de saída Cosmos DB para uma corda. Por exemplo, se tiver uma chave de partição com um valor de 1 do tipo bigint, é convertido em "1" de corda tipo. Esta conversão acontece sempre independentemente de a propriedade de partição ser escrita para Cosmos DB.

## <a name="output-batch-size"></a>Tamanho do lote de saída

Para obter o tamanho máximo da mensagem, consulte [os limites de DB Azure Cosmos](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). O tamanho do lote e a frequência de escrita são ajustados dinamicamente com base nas respostas DB do Azure Cosmos. Não existem limitações pré-determinadas da Stream Analytics.

## <a name="next-steps"></a>Próximos passos

* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Crie um trabalho Azure Stream Analytics usando um modelo ARM](quick-create-azure-resource-manager.md)
* [Quickstart: Criar um trabalho stream analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics em Código de Estúdio Visual](quick-create-visual-studio-code.md)
