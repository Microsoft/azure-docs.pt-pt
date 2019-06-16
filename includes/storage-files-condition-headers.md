---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159871"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Erro ConditionHeadersNotSupported de aplicações Web utilizando os ficheiros do Azure a partir de Browser

Quando aceder ao conteúdo alojado nos ficheiros do Azure através de uma aplicação que faz com usa de cabeçalhos condicionais, como um navegador da web, de acesso falha, exibindo um erro de ConditionHeadersNotSupported.

![Erro de ConditionHeaderNotSupported](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Causa

Cabeçalhos condicionais ainda não são suportados. Aplicativos implementá-las precisará solicitar o ficheiro completo, sempre que aceder ao ficheiro.

### <a name="workaround"></a>Solução

Quando um novo ficheiro é carregado, a propriedade de controlo de cache por predefinição é "no-cache". Para forçar o aplicativo para solicitar o ficheiro de cada vez, propriedade de controlo de cache do ficheiro tem de ser atualizado de "no-cache" para "no-cache, no-store, tem-revalide". Isso pode ser obtido usando [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

![Modificação de cache de conteúdos do Explorador de armazenamento](media/storage-files-condition-headers/storage-explorer-cache.png)