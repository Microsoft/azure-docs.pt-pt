---
title: APIs de cumprimento saaS no mercado comercial da Microsoft
description: Uma introdução às APIs de realização que lhe permitem integrar as suas ofertas SaaS no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2e6381afb19018822f6f37171a5ca4b3d929b42e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88037529"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>APIs de cumprimento saaS no mercado comercial da Microsoft

As APIs de Realização saaS permitem que os editores, também conhecidos como fornecedores independentes de software (ISVs), publiquem e vendam as suas aplicações SaaS no Microsoft AppSource, Azure Marketplace e no portal Azure. Estas APIs permitem que as aplicações ISV participem em todos os canais via comércio: direto, orientado por parceiros (revendedor) e liderado por campo.  A integração com estas APIs é um requisito para a criação e publicação de uma oferta de SaaS transacionável no Partner Center.

Os ISVs devem implementar os seguintes fluxos de API adicionando no seu código de serviço SaaS para manter o mesmo estado de subscrição tanto para os ISVs como para a Microsoft:

* Fluxo de página de aterragem: A Microsoft notifica a editora de que a oferta da editora SaaS foi comprada por um cliente no mercado.
* Fluxo de ativação: A Editora notifica a Microsoft de que uma conta SaaS recém-comprada foi configurada do lado da editora.
* Fluxo de atualização: Alteração do plano adquirido e/ou do número de lugares adquiridos.
* Suspender e restabelecer o fluxo: Suspender a oferta saaS adquirida no caso de o método de pagamento do cliente deixar de ser válido. A oferta suspensa pode ser restabelecida quando a questão com o método de pagamento for resolvida.
* Fluxos Webhook: A Microsoft notificará a editora sobre as alterações de subscrição do SaaS e o cancelamento desencadeado pelo cliente do lado da Microsoft.

Para o cancelamento da subscrição SaaS adquirida, a integração é opcional, pois pode ser feita pelo cliente do lado da Microsoft.

A integração correta com as APIs de Realização de SaaS é fundamental para garantir que

* os clientes finais que compraram a oferta saaS da editora são faturados corretamente pela Microsoft.
* os clientes finais estão a obter a experiência correta do utilizador de comprar, configurar, utilizar e gerir subscrições saaS compradas no mercado.

Estas APIs permitem que as ofertas da editora participem em todos os canais via comércio:

* direto
* liderado por parceiros (revendedor, CSP)
* liderado por campo

No cenário do revendedor (CSP), a CSP está a adquirir a oferta saaS em nome do cliente final. Espera-se que um cliente utilize a oferta saaS, mas o CSP é a entidade que faz o seguinte:

* faturação do cliente
* alteração dos planos de subscrição/quantidade de lugares adquiridos
* cancelamento das assinaturas

O Editor não é obrigado a implementar nenhum dos fluxos de chamada da API de forma diferente para este cenário.

Para mais informações sobre a CSP, consulte https://partner.microsoft.com/licensing .

>[!Warning]
>A versão atual desta API é a versão 2, que deve ser usada para todas as novas ofertas do SaaS. A versão 1 da API é depreciada e está a ser mantida para apoiar as ofertas existentes.

>[!Note]
>As APIs de cumprimento saaS destinam-se apenas a ser chamadas de um serviço de backend da editora. A integração com as APIs diretamente da página web da editora não é suportada. Deve ser utilizado apenas o fluxo de autenticação de serviço-a-serviço.

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, registe a sua aplicação SaaS no [portal Azure,](https://ms.portal.azure.com) conforme explicado no [Registo de uma Aplicação AD Azure.](./pc-saas-registration.md)  Em seguida, utilize a versão mais atual desta interface para desenvolvimento: [SaaS Fulfillment API Versão 2](./pc-saas-fulfillment-api-v2.md).
