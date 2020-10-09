---
title: Recolha de parâmetros necessários
services: cognitive-services
author: aahill
manager: nitinme
description: Os parâmetros para todos os contentores dos Serviços Cognitivos
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80875056"
---
## <a name="gathering-required-parameters"></a>Recolha de parâmetros necessários

Existem três parâmetros primários para todos os contentores dos Serviços Cognitivos que são necessários. O contrato de licença de utilizador final (EULA) deve estar presente com um valor de `accept` . Além disso, tanto um URL endpoint como uma chave API são necessários.

### <a name="endpoint-uri-endpoint_uri"></a>Endpoint URI `{ENDPOINT_URI}`

O valor **Endpoint** URI está disponível na página *geral* do portal Azure do recurso correspondente do Serviço Cognitivo. Navegue para a página *overview,* paire sobre o Ponto final e aparecerá um `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone. Copiar e usar sempre que necessário.

![Reúna o ponto final uri para utilização posterior](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Chaves `{API_KEY}`

Esta chave é utilizada para iniciar o contentor e está disponível na página Chaves do portal Azure do recurso serviço cognitivo correspondente. Navegue na página *Chaves* e clique no `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone.

![Pegue uma das duas chaves para uso posterior](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Estas chaves de subscrição são usadas para aceder à sua API de Serviço Cognitivo. Não partilhem as chaves. Guarde-os de forma segura, por exemplo, utilizando o Cofre da Chave Azure. Recomendamos também a regeneração regular destas teclas. Apenas uma chave é necessária para fazer uma chamada da API. Ao regenerar a primeira chave, pode utilizar a segunda chave para o acesso continuado ao serviço.