---
title: Rejulgar tarefas após um erro
description: Verifique se há erros e tente novamente.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919996"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Detetar e manipular erros de serviço do Lote

É importante lembrar-se de verificar se há erros ao trabalhar com um serviço REST API. Não é incomum que erros ocorram quando se executam trabalhos de lote.

## <a name="common-errors"></a>Erros comuns 

- Falhas de networking - estes são pedidos que nunca chegaram ao Lote ou a resposta do Lote não chegou ao cliente a tempo.
- Erros internos do servidor - estes são resposta padrão 5xx código de estado HTTP.
- O estrangulamento pode causar erros como 429 ou 503 respostas do código de estado HTTP com o cabeçalho Retry-after.
- Erros 4xx que incluem erros como JáExiste e InvalidOperation. Isto significa que o recurso não está no estado correto para a transição do Estado.

Para obter informações detalhadas sobre os vários tipos de códigos de erro e códigos de erro específicos, consulte o [Estado do Lote e códigos](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)de erro .

## <a name="when-to-retry"></a>Quando voltar a tentar

As APIs do Lote irão notificá-lo se houver uma falha. Todos podem ser julgados novamente, e todos incluem um manipulador global de retry para esse fim. É melhor usar este mecanismo incorporado.

Depois de uma falha, deve esperar um pouco (alguns segundos entre tentativas) antes de tentar novamente. Se voltar a tentar com demasiada frequência ou muito rapidamente, o manipulador de retry irá acelerar.

### <a name="for-more-information"></a>Para obter mais informações:  

[As APIs do lote e as ferramentas](batch-apis-tools.md) ligam-se a informações de referência da API. A API .NET, por exemplo, tem uma [classe RetryPolicyProvider]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) onde a política de retry necessária deve ser especificada. 

Para obter informações detalhadas sobre cada API e as suas políticas de retry predefinidos, leia [o Estado do Lote e os Códigos de Erro](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
