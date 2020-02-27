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
ms.openlocfilehash: eda567fda13d6caca679d0ce4947e042eca9530d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652012"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Detetar e manipular erros de serviço do Lote

É importante lembrar-se de verificar se há erros ao trabalhar com um serviço REST API. Não é incomum que erros ocorram quando se executam trabalhos de lote.

## <a name="common-errors"></a>Erros comuns 

- Falhas de networking - estes são pedidos que nunca chegaram ao Lote ou a resposta do Lote não chegou ao cliente a tempo.
- Erros internos do servidor - estes são resposta padrão 5xx código de estado HTTP.
- O estrangulamento pode causar erros como 429 ou 503 respostas do código de estado HTTP com o cabeçalho Retry-after.
- Erros 4xx que incluem erros como JáExiste e InvalidOperation. Isto significa que o recurso não está no estado correto para a transição do Estado.

## <a name="when-to-retry"></a>Quando voltar a tentar

As APIs do Lote irão notificá-lo se houver uma falha. Todos podem ser julgados novamente, e todos incluem um manipulador global de retry para esse fim. É melhor usar este mecanismo incorporado.

Depois de uma falha, deve esperar um pouco (alguns segundos entre tentativas) antes de tentar novamente. Se voltar a tentar com demasiada frequência ou muito rapidamente, o manipulador de retry irá acelerar.


Para obter informações detalhadas sobre cada API e as suas políticas de retry predefinidos, leia [o Estado do Lote e os Códigos de Erro](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
