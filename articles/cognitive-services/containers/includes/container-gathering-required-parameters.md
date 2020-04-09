---
title: Recolha de parâmetros necessários
services: cognitive-services
author: aahill
manager: nitinme
description: Os parâmetros para todos os recipientes dos Serviços Cognitivos
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875056"
---
## <a name="gathering-required-parameters"></a>Recolha de parâmetros necessários

Existem três parâmetros primários para todos os recipientes dos Serviços Cognitivos que são necessários. O acordo de licença de utilizador final (EULA) deve estar presente com um valor de `accept`. Além disso, são necessários tanto um URL endpoint como uma chave API.

### <a name="endpoint-uri-endpoint_uri"></a>Ponto final URI`{ENDPOINT_URI}`

O valor **Endpoint** URI está disponível na página de *visão geral* do portal Azure do correspondente recurso do Serviço Cognitivo. Navegue para a página *de visão geral,* paire sobre o Ponto Final, e aparecerá um `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone. Copiar e usar sempre que necessário.

![Junte o ponto final uri para uso posterior](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Chaves`{API_KEY}`

Esta chave é usada para iniciar o recipiente, e está disponível na página chaves do portal Azure do recurso correspondente do Serviço Cognitivo. Navegue na página *Keys* e `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> clique no ícone.

![Obtenha uma das duas chaves para uso posterior](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Estas chaves de subscrição são usadas para aceder à sua API do Serviço Cognitivo. Não partilhe as chaves. Guarde-os de forma segura, por exemplo, utilizando o Cofre de Chaves Azure. Recomendamos também a regeneração regular destas teclas. Só uma chave é necessária para fazer uma chamada da API. Ao regenerar a primeira tecla, pode utilizar a segunda tecla para o acesso continuado ao serviço.