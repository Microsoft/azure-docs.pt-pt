---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70737496"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Error ConditionHeadersNotSupported from a Web Application using Azure Files from Browser

O erro condicionadoNotSupported ocorre ao aceder a conteúdos hospedados em Ficheiros Azure através de uma aplicação que faz uso de cabeçalhos condicionais, como um navegador web, falha o acesso. O erro indica que os cabeçalhos de condição não são suportados.

![Erro de cabeçalhos condicional de Ficheiros Azure](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Causa

Os cabeçalhos condicional ainda não são suportados. As aplicações que as implementam terão de solicitar o ficheiro completo sempre que o ficheiro for acedido.

### <a name="workaround"></a>Solução

Quando um novo ficheiro é carregado, a propriedade de controlo de cache por padrão é "sem cache". Para forçar o pedido de pedido do ficheiro sempre, a propriedade de controlo de cache do ficheiro precisa de ser atualizada de "no-cache" para "no-cache, no-store, must-revalida". Isto pode ser conseguido usando [o Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

![Modificação de cache de conteúdo do explorador de armazenamento para cabeçalhos condicional sintetizados De Ficheiros Azure](media/storage-files-condition-headers/storage-explorer-cache.png)