---
title: Coletando parâmetros necessários
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Os parâmetros para todos os contêineres de serviços cognitivas
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465940"
---
## <a name="gathering-required-parameters"></a>Coletando parâmetros necessários

Há três parâmetros principais para todos os contêineres de serviços cognitivas que são necessários. O contrato de licença de usuário final (EULA) deve estar presente com um valor de `accept`. Além disso, uma URL de ponto de extremidade e uma chave de API são necessárias.

### <a name="endpoint-uri-endpoint_uri"></a>URI do ponto de extremidade `{ENDPOINT_URI}`

O valor do URI do **ponto de extremidade** está disponível na página de *visão geral* portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *visão geral* , passe o mouse sobre o ponto de <span class="docon docon-edit-copy x-hidden-focus"></span> extremidade e um ícone de `Copy to clipboard` aparecerá. Copie e use onde for necessário.

![Coletar o URI do ponto de extremidade para uso posterior](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Chaves `{API_KEY}`

Essa chave é usada para iniciar o contêiner e está disponível na página chaves do portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *chaves* e clique no ícone de `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> .

![Obter uma das duas chaves para uso posterior](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Essas chaves de assinatura são usadas para acessar sua API de serviço cognitiva. Não compartilhe suas chaves. Armazene-os com segurança, por exemplo, usando Azure Key Vault. Também recomendamos regenerar essas chaves regularmente. Apenas uma chave é necessária para fazer uma chamada à API. Ao regenerar a primeira chave, você pode usar a segunda chave para obter acesso contínuo ao serviço.