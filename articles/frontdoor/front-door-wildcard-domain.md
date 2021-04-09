---
title: Porta frontal Azure - Suporte para domínios wildcard
description: Este artigo ajuda-o a entender como a Azure Front Door suporta mapeamento e gestão de domínios wildcard na lista de domínios personalizados.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 18504f1ed4200889b20c9608c9c0ad2c13c9aaa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94425767"
---
# <a name="wildcard-domains"></a>Domínios wildcard

Além de domínios e subdomínios de ápice, também pode mapear um domínio wildcard para os seus anfitriões frontais ou domínios personalizados para o seu perfil Azure Front Door. Ter domínios wildcard na configuração da porta frontal Azure simplifica o comportamento de encaminhamento de tráfego para vários subdomínios para uma API, aplicação ou website a partir da mesma regra de encaminhamento. Não é necessário modificar a configuração para adicionar ou especificar cada subdomínio separadamente. Como exemplo, pode definir o encaminhamento para `customer1.contoso.com` , e utilizando a mesma regra de `customer2.contoso.com` `customerN.contoso.com` encaminhamento e adicionando o domínio wildcard `*.contoso.com` .

Os cenários-chave que são melhorados com o suporte para domínios wildcard incluem:

- Não precisa de embarcar em cada subdomínio no seu perfil da Porta Frontal Azure e, em seguida, permitir que o HTTPS encaste um certificado para cada subdomínio.
- Já não é obrigado a alterar a configuração da porta frontal Azure se uma aplicação adicionar um novo subdomínio. Anteriormente, tinha de adicionar o subdomínio, ligar-lhe um certificado, anexar uma política de firewall de aplicação web (WAF) e, em seguida, adicionar o domínio a diferentes regras de encaminhamento.

> [!NOTE]
> Atualmente, a adição de domínios wildcard através do Azure DNS é suportada apenas através da API, PowerShell e do Azure CLI. O suporte para adicionar e gerir domínios wildcard no portal Azure não está disponível.

## <a name="adding-wildcard-domains"></a>Adicionar domínios wildcard

Pode adicionar um domínio wildcard na secção para anfitriões ou domínios frontais. Semelhante aos subdomínios, a Azure Front Door valida que há mapeamento de registo CNAME para o seu domínio wildcard. Este mapeamento DNS pode ser um mapeamento de registo CNAME direto como `*.contoso.com` mapeado para `contoso.azurefd.net` . Ou pode usar um mapeamento temporário adversante. Por exemplo, `afdverify.contoso.com` mapeado para `afdverify.contoso.azurefd.net` validar o mapa de registos CNAME para o wildcard.

> [!NOTE]
> O DNS do Azure suporta registos de carateres universais.

Pode adicionar o maior número de subdomínios de nível único do domínio wildcard em anfitriões frontais, até ao limite dos anfitriões frontais. Esta funcionalidade pode ser necessária para:

- Definindo uma rota diferente para um subdomínio do que o resto dos domínios (do domínio wildcard).

- Ter uma política de WAF diferente para um subdomínio específico. Por exemplo, `*.contoso.com` permite adicionar sem ter que provar `foo.contoso.com` novamente a propriedade do domínio. Mas não permite `foo.bar.contoso.com` porque não é um subdomínio de um único nível `*.contoso.com` de. Para adicionar `foo.bar.contoso.com` sem validação adicional da propriedade do domínio, `*.bar.contosonews.com` é necessário adicionar.

Pode adicionar domínios wildcard e seus subdomínios com determinadas limitações:

- Se um domínio wildcard for adicionado a um perfil da porta frontal Azure:
  - O domínio wildcard não pode ser adicionado a qualquer outro perfil da Porta Frontal Azure.
  - Subdomínios de primeiro nível do domínio wildcard não podem ser adicionados a outro perfil da Porta Frontal Azure ou a um perfil da Rede de Entrega de Conteúdos Azure.
- Se um subdomínio de um domínio wildcard já for adicionado a um perfil da Porta Frontal Azure ou a um perfil da Rede de Entrega de Conteúdos Azure, o domínio wildcard não pode ser utilizado para outro perfil da Porta Frontal Azure.
- Se dois perfis (Azure Front Door ou Azure Content Delivery Network) tiverem vários subdomínios de um domínio raiz, então os domínios wildcard não podem ser adicionados a nenhum dos perfis.

## <a name="certificate-binding"></a>Vinculação de certificado

Para aceitar o tráfego HTTPS no seu domínio wildcard, tem de ativar HTTPS no domínio wildcard. A obrigação de um certificado para um domínio wildcard requer um certificado wildcard. Ou seja, o nome do certificado também deve ter o domínio wildcard.

> [!NOTE]
> Atualmente, apenas usando a sua própria opção de certificado SSL personalizado está disponível para ativar HTTPS para domínios wildcard. Os certificados geridos pela Azure Front Door não podem ser utilizados para domínios wildcard.

Pode optar por utilizar o mesmo certificado wildcard da Azure Key Vault ou a partir de certificados geridos pela Porta Frontal Azure para subdomínios.

Se for adicionado um subdomínio para um domínio wildcard que já tenha um certificado associado a ele, então não pode desativar HTTPS para o subdomínio. O subdomínio utiliza a ligação de certificado para o domínio wildcard, a menos que um certificado gerido por Key Vault ou Azure Front Door o substitua.

## <a name="waf-policies"></a>Políticas waf

As políticas WAF podem ser anexadas a domínios wildcard, semelhantes a outros domínios. Uma política de WAF diferente pode ser aplicada a um subdomínio de um domínio wildcard. Para os subdomínios, deve especificar a política da WAF a ser utilizada mesmo que seja a mesma política que o domínio wildcard. Os subdomínios *não* herdam automaticamente a política da WAF a partir do domínio wildcard.

Se não quiser que uma política da WAF seja executada por um subdomínio, pode criar uma política de WAF vazia sem regras geridas ou personalizadas.

## <a name="routing-rules"></a>Regras de encaminhamento

Ao configurar uma regra de encaminhamento, pode selecionar um domínio wildcard como anfitrião frontal. Você também pode ter diferentes comportamentos de rota para domínios wildcard e subdomínios. Como descrito em [Como Azure Front Door faz a correspondência](front-door-route-matching.md)de rota, a correspondência mais específica para o domínio através de diferentes regras de encaminhamento é escolhida em tempo de execução.

> [!IMPORTANT]
> Deve ter padrões de caminho correspondentes nas suas regras de encaminhamento, ou os seus clientes verão falhas. Por exemplo, tem duas regras de encaminhamento como a Route 1 `*.foo.com/*` (mapeada para a piscina traseira A) e a Rota 2 `/bar.foo.com/somePath/*` (mapeada para a piscina b traseira). Em seguida, um pedido chega para `bar.foo.com/anotherPath/*` . A Azure Front Door seleciona a Rota 2 com base numa correspondência de domínio mais específica, apenas para não encontrar padrões de caminho correspondentes nas rotas.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um perfil da Porta Frontal Azure](quickstart-create-front-door.md).
- Saiba como [adicionar um domínio personalizado na Porta frontal Azure.](front-door-custom-domain.md)
- Saiba como [ativar HTTPS num domínio personalizado.](front-door-custom-domain-https.md)
