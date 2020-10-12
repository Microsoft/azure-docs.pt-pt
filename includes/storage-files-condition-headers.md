---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "70737496"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Problemas de erroSalheadersNotSupported a partir de uma aplicação web usando ficheiros Azure do Navegador

O erro do ConditionHeadersNotSupported ocorre ao aceder aos conteúdos alojados nos Ficheiros Azure através de uma aplicação que faz uso de cabeçalhos condicional, como um navegador web, o acesso falha. O erro indica que os cabeçalhos da condição não são suportados.

![Erro de cabeçalhos condicional de ficheiros Azure](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Causa

Os cabeçalhos condicional ainda não estão suportados. As aplicações que as implementam terão de solicitar o ficheiro completo sempre que o ficheiro for acedido.

### <a name="workaround"></a>Solução

Quando um novo ficheiro é carregado, a propriedade de controlo de cache por padrão é "no-cache". Para forçar o pedido de solicitar o ficheiro sempre, a propriedade de cache-control do ficheiro precisa de ser atualizada de "no-cache" para "no-cache, no-store, must-revalidate". Isto pode ser conseguido usando [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Modificação da cache do conteúdo do explorador de armazenamento para cabeçalhos condicional de ficheiros Azure](media/storage-files-condition-headers/storage-explorer-cache.png)