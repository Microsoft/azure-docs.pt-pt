---
title: Utilização padrão/premium da porta frontal azul com partilha de recursos de origem cruzada
description: Saiba como utilizar a Porta Frontal Azure (AFD) com a partilha de recursos de origem cruzada (CORS).
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099370"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>Utilização do Azure Front Door Standard/Premium com partilha de recursos de origem cruzada (CORS)

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

## <a name="what-is-cors"></a>O que é o CORS?

O CORS (Partilha de Recursos de Várias Origens) é uma funcionalidade HTTP que permite a execução de uma aplicação Web num domínio para aceder a recursos noutro domínio. Para reduzir a possibilidade de ataques de scripts cross-site, todos os navegadores web modernos implementam uma restrição de segurança conhecida como [política de origem .](https://www.w3.org/Security/wiki/Same_Origin_Policy) Isto impede que uma página web chame APIs em um domínio diferente.  O CORS fornece uma forma segura de permitir que uma origem (o domínio de origem) chame APIs noutra origem.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Como funciona

Existem dois tipos de pedidos de CORS, *pedidos simples* e *pedidos complexos.*

### <a name="for-simple-requests"></a>Para pedidos simples:

1. O navegador envia o pedido CORS com outro cabeçalho de pedido **Origin** HTTP. O valor deste cabeçalho é a origem que serviu a página-mãe, que é definida como a combinação de *protocolo,* *domínio* e *porta.*  Quando uma página de https \: //www.contoso.com tenta aceder aos dados de um utilizador na origem fabrikam.com, o seguinte cabeçalho de pedido seria enviado para fabrikam.com:

   `Origin: https://www.contoso.com`

2. O servidor pode responder com qualquer um dos seguintes:

   * Um **cabeçalho access-Control-Allow-Origin** na sua resposta indicando qual o local de origem permitido. Por exemplo:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Um código de erro HTTP, como o 403, se o servidor não permitir o pedido de origem cruzada após verificar o cabeçalho Origin

   * Um **cabeçalho Access-Control-Allow-Origin** com um wildcard que permite todas as origens:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Para pedidos complexos:

Um pedido complexo é um pedido de CORS onde o navegador é obrigado a enviar um *pedido de pré-voo* (isto é, uma sonda preliminar) antes de enviar o pedido real de CORS. O pedido de pré-voo solicita a permissão do servidor se o pedido original do CORS pode continuar e é um `OPTIONS` pedido para o mesmo URL.

> [!TIP]
> Para obter mais detalhes sobre os fluxos CORS e armadilhas comuns, consulte o [Guia do CORS para REST APIs](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Cenários de origem ou wildcard ou de origem única
O CORS na Porta Frontal Azure funcionará automaticamente sem configuração extra quando o cabeçalho **Access-Control-Allow-Origin** estiver definido para wildcard (*) ou uma única origem.  A Azure Front Door cache a primeira resposta e os pedidos subsequentes usarão o mesmo cabeçalho.

Se já foram feitos pedidos à Porta Frontal Azure antes de o CORS ser definido na sua origem, terá de expurgar o conteúdo do seu conteúdo do ponto final para recarregar o conteúdo com o cabeçalho **Access-Control-Allow-Origin.**

## <a name="multiple-origin-scenarios"></a>Cenários de origem múltipla
Se você precisa permitir uma lista específica de origens para CORS, as coisas ficam um pouco mais complicadas. O problema ocorre quando o CDN caches o cabeçalho **Access-Control-Allow-Origin** para a primeira origem CORS.  Quando uma origem CORS diferente fizer outro pedido, o CDN servirá o cabeçalho cached **Access-Control-Allow-Origin,** que não combina. Há várias formas de corrigir este problema.

### <a name="azure-front-door-rule-set"></a>Conjunto de regras da porta da frente Azure

Na Porta frontal Azure, pode criar uma regra nas regras da porta da frente Azure [definidas](concept-rule-set.md) para verificar o cabeçalho **Origin** no pedido. Se for uma origem válida, a sua regra definirá o cabeçalho **Access-Control-Allow-Origin** com o valor correto. Neste caso, o cabeçalho **Access-Control-Allow-Origin** do servidor de origem do ficheiro é ignorado e o motor de regras da AFD gere completamente as origens permitidas do CORS.

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="Screenshot de exemplo de regras com conjunto de regras.":::

> [!TIP]
> Pode adicionar ações adicionais à sua regra para modificar cabeçalhos de resposta adicionais, tais como **Acesso-Control-Allow-Methods**.
> 

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar um Front Door](create-front-door-portal.md).
