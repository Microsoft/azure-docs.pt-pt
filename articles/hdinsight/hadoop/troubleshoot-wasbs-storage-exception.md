---
title: A conta a ser acedida não suporta erro http no Azure HDInsight
description: Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165556"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>A conta a ser acedida não suporta erro http no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

A seguinte mensagem de erro é recebida:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Causa

Existem múltiplas razões pelas quais a mensagem de erro é recebida:

* A conta de armazenamento tem [transferência segura](../../storage/common/storage-require-secure-transfer.md) ativada e o [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) incorreto está a ser utilizado.

* Foi criado um cluster com uma conta de armazenamento que tinha transferência segura *desativada.* Posteriormente, a transferência segura foi ativada na conta de armazenamento.

## <a name="resolution"></a>Resolução

Se a transferência segura estiver ativada para o Armazenamento de Azure ou data Lake Storage Gen2, o URI será `wasbs://` ou `abfss://`, respectivamente.  Ver também, [transferência segura.](../../storage/common/storage-require-secure-transfer.md)

Para novos clusters, utilize uma conta de armazenamento que já tenha a definição de transferência segura desejada. Não altere a definição de transferência segura para uma conta de armazenamento que esteja a ser utilizada por um cluster existente.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
