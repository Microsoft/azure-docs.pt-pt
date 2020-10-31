---
title: Saída de armazenamento de mesa da Azure Stream Analytics
description: Este artigo descreve o armazenamento da tabela Azure como uma saída para o Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ea775ef472687485dbd2f30c4f60adc33c0eaa73
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127353"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Saída de armazenamento de mesa da Azure Stream Analytics

[O armazenamento da Azure Table](../storage/common/storage-introduction.md) oferece um armazenamento altamente disponível e massivamente escalável, para que uma aplicação possa escalar automaticamente para satisfazer a procura do utilizador. O armazenamento de mesa é a loja de chaves/atributos NoSQL da Microsoft, que pode utilizar para dados estruturados com menos restrições no esquema. O armazenamento da tabela Azure pode ser usado para armazenar dados para persistência e recuperação eficiente.

A tabela que se segue lista os nomes dos imóveis e as suas descrições para a criação de uma saída de tabela.

| Nome da propriedade | Description |
| --- | --- |
| Alias de saída |Um nome amigável usado em consultas para direcionar a saída de consulta para este armazenamento de mesa. |
| Conta de armazenamento |O nome da conta de armazenamento para onde está a enviar a sua produção. |
| Chave da conta de armazenamento |A chave de acesso associada à conta de armazenamento. |
| Nome da tabela |O nome da mesa. A mesa é criada se não existir. |
| Chave de partição |O nome da coluna de saída que contém a chave de partição. A chave de partição é um identificador único para a partição dentro de uma tabela que forma a primeira parte da chave primária de uma entidade. É um valor de corda que pode ser até 1 KB de tamanho. |
| Chave de linha |O nome da coluna de saída que contém a tecla de linha. A chave da linha é um identificador único para uma entidade dentro de uma divisória. É a segunda parte da chave primária de uma entidade. A tecla de linha é um valor de corda que pode ser até 1 KB de tamanho. |
| Tamanho do lote |O número de registos de uma operação de lote. O incumprimento (100) é suficiente para a maioria dos postos de trabalho. Consulte a especificação de [funcionamento do lote de mesa](/java/api/com.microsoft.azure.storage.table.tablebatchoperation) para obter mais detalhes sobre a modificação desta definição. |

## <a name="partitioning"></a>Criação de partições

A chave de partição é qualquer coluna de saída. O número de autores de saída segue a partição de entrada para [consultas totalmente paralelas.](stream-analytics-scale-jobs.md)

## <a name="output-batch-size"></a>Tamanho do lote de saída

Para obter o tamanho máximo da mensagem, consulte [os limites de armazenamento do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). O padrão é de 100 entidades por transação única, mas pode configugá-lo a um valor menor, conforme necessário.

## <a name="next-steps"></a>Passos seguintes

* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Crie um trabalho Azure Stream Analytics usando um modelo ARM](quick-create-azure-resource-manager.md)
* [Quickstart: Criar um trabalho stream analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Quickstart: Criar um trabalho Azure Stream Analytics em Código de Estúdio Visual](quick-create-visual-studio-code.md)