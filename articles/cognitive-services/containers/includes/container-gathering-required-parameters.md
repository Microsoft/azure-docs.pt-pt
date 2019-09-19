---
title: Coletando parâmetros necessários
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Os parâmetros para todos os contêineres de serviços cognitivas
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 06f68d28d06dec7c9e738ad8cb50ca337fa840be
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102021"
---
## <a name="gathering-required-parameters"></a>Coletando parâmetros necessários

Há três parâmetros principais para todos os contêineres de serviços cognitivas que são necessários. O contrato de licença de usuário final (EULA) deve estar presente com um valor `accept`de. Além disso, uma URL de ponto de extremidade e uma chave de API são necessárias.

> [!NOTE]
> A única exceção a esses três parâmetros obrigatórios é quando os contêineres são considerados contêineres "offline". Os contêineres offline não relatam o uso, não são medidos e seguem uma metodologia de cobrança diferente.

### <a name="endpoint-uri-endpoint_uri"></a>URI do ponto de extremidade`{ENDPOINT_URI}`

O valor do URI do **ponto de extremidade** está disponível na página de *visão geral* portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *visão geral* , focalize o ponto de extremidade e `Copy to clipboard` um <span class="docon docon-edit-copy x-hidden-focus"></span> ícone será exibido. Copie e use onde for necessário.

![Coletar o URI do ponto de extremidade para uso posterior](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Novas`{API_KEY}`

Essa chave é usada para iniciar o contêiner e está disponível na página chaves do portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *chaves* e clique no `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone.

![Obter uma das duas chaves para uso posterior](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Essas chaves de assinatura são usadas para acessar sua API de serviço cognitiva. Não compartilhe suas chaves. Armazene-os com segurança, por exemplo, usando Azure Key Vault. Também recomendamos regenerar essas chaves regularmente. Apenas uma chave é necessária para fazer uma chamada à API. Ao regenerar a primeira chave, você pode usar a segunda chave para obter acesso contínuo ao serviço.