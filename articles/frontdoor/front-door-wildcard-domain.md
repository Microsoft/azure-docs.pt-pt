---
title: Porta da Frente Azure - Suporte para domínios wildcard
description: Este artigo ajuda-o a entender como o Azure Front Door suporta o mapeamento e a gestão de domínios wildcard na lista de domínios personalizados.
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: 6d8a6d6f0b05b9b7fd0144959c82b6a2c9e659a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81768316"
---
# <a name="wildcard-domains"></a>Domínios wildcard

Além de domínios ápice e subdomínios, pode mapear um nome de domínio wildcard para a sua lista de anfitriões frontais ou domínios personalizados no seu perfil de Porta Frontal Azure. Ter domínios wildcard na configuração da Porta Frontal Do Azure simplifica o comportamento de encaminhamento de tráfego para vários subdomínios para uma API, aplicação ou website a partir da mesma regra de encaminhamento. Não é necessário modificar a configuração para adicionar ou especificar cada subdomínio separadamente. Como exemplo, pode definir o `customer1.contoso.com`encaminhamento para, `customer2.contoso.com`e `customerN.contoso.com` usando a mesma `*.contoso.com`regra de encaminhamento e adicionando o domínio wildcard .

Os cenários-chave que são melhorados com suporte para domínios wildcard incluem:

- Não é necessário embarcar em cada subdomínio no perfil da Porta Frontal Azure e, em seguida, ativar HTTPS para vincular um certificado para cada subdomínio.
- Já não é obrigado a alterar a configuração da sua produção Azure Front Door se uma aplicação adicionar um novo subdomínio. Anteriormente, teve de adicionar o subdomínio, ligar-lhe um certificado, anexar uma política de firewall de aplicação web (WAF) e, em seguida, adicionar o domínio a diferentes regras de encaminhamento.

> [!NOTE]
> Atualmente, os domínios wildcard são suportados apenas através da API, PowerShell e do Azure CLI. O suporte para adicionar e gerir domínios wildcard no portal Azure não está disponível.

## <a name="adding-wildcard-domains"></a>Adicionar domínios wildcard

Pode adicionar um domínio wildcard sob a secção para anfitriões ou domínios frontais. Semelhante aos subdomínios, a Porta Frontal Azure valida que existe um mapeamento de registo CNAME para o seu domínio wildcard. Este mapeamento DNS pode ser um `*.contoso.com` mapeamento `contoso.azurefd.net`de registo CNAME direto como mapeado para . Ou pode usar um mapeamento temporário afdcheck. Por exemplo, `afdverify.contoso.com` mapeado para `afdverify.contoso.azurefd.net` validar o mapa de registos CNAME para o wildcard.

> [!NOTE]
> O DNS do Azure suporta registos de carateres universais.

Pode adicionar tantos subdomínios de nível único do domínio wildcard nos anfitriões frontais, até ao limite dos anfitriões frontais. Esta funcionalidade pode ser necessária para:

- Definindo uma rota diferente para um subdomínio do que o resto dos domínios (a partir do domínio wildcard).

- Ter uma política de WAF diferente para um subdomínio específico. Por exemplo, `*.contoso.com` `foo.contoso.com` permite adicionar sem ter que provar novamente a propriedade do domínio. Mas não permite `foo.bar.contoso.com` porque não é um subdomínio `*.contoso.com`de nível único de . Para `foo.bar.contoso.com` adicionar sem validação adicional da propriedade do domínio, `*.bar.contosonews.com` precisa de ser adicionado.

Pode adicionar domínios wildcard e seus subdomínios com determinadas limitações:

- Se um domínio wildcard for adicionado a um perfil azure front door:
  - O domínio wildcard não pode ser adicionado a qualquer outro perfil da Porta frontal azure.
  - Os subdomínios de primeiro nível do domínio wildcard não podem ser adicionados a outro perfil da Porta Frontal Azure ou a um perfil da Rede de Entrega de Conteúdos Azure.
- Se um subdomínio de um domínio wildcard for adicionado a um perfil de Porta Frontal Azure ou ao perfil da Rede de Entrega de Conteúdos Azure, então o domínio wildcard não pode ser adicionado a outros perfis da Porta Frontal Azure.
- Se dois perfis (Azure Front Door ou Azure Content Delivery Network) tiverem vários subdomínios de um domínio raiz, então os domínios wildcard não podem ser adicionados a nenhum dos perfis.

## <a name="certificate-binding"></a>Encadernação de certificado

Para aceitar o tráfego HTTPS no seu domínio wildcard, deve ativar HTTPS no domínio wildcard. O certificado vinculativo para um domínio wildcard requer um certificado wildcard. Ou seja, o nome do certificado deve também ter o domínio wildcard.

> [!NOTE]
> Atualmente, apenas a utilização da sua própria opção de certificado SSL personalizada está disponível para ativar HTTPS para domínios wildcard. Os certificados geridos pela Porta Frontal Azure não podem ser usados para domínios wildcard.

Pode optar por utilizar o mesmo certificado wildcard do Azure Key Vault ou dos certificados geridos pela Azure Front Door para subdomínios.

Se for adicionado um subdomínio para um domínio wildcard que já tenha um certificado associado ao mesmo, então https para o subdomínio não pode ser desativado. O subdomínio utiliza o certificado de ligação para o domínio wildcard, a menos que um cofre de chave diferente ou certificado gerido pela Porta Frontal Azure o substitua.

## <a name="waf-policies"></a>Políticas waf

As políticas de WAF podem ser anexadas a domínios wildcard, semelhantes a outros domínios. Uma política de WAF diferente pode ser aplicada a um subdomínio de um domínio wildcard. Para os subdomínios, deve especificar a política waf a ser usada mesmo que seja a mesma política que o domínio wildcard. Os subdomínios *não* herdam automaticamente a política waf do domínio wildcard.

Se não quiser que uma política de WAF seja executada por um subdomínio, pode criar uma política de WAF vazia sem regras geridas ou personalizadas.

## <a name="routing-rules"></a>Regras de encaminhamento

Ao configurar uma regra de encaminhamento, pode selecionar um domínio wildcard como anfitrião frontal. Você também pode ter diferentes comportamentos de rota para domínios e subdomínios wildcard. Como descrito em [How Azure Front Door faz a correspondência](front-door-route-matching.md)de rotas, a correspondência mais específica para o domínio através de diferentes regras de encaminhamento é escolhida no tempo de execução.

> [!IMPORTANT]
> Você deve ter padrões de caminho correspondentes através das suas regras de encaminhamento, ou seus clientes verão falhas. Por exemplo, você tem duas regras`*.foo.com/*` de encaminhamento como Route 1 (mapeada para a piscina traseira A) e Route 2 (mapeada`bar.foo.com/somePath/*` para a piscina traseira B). Então, chega um `bar.foo.com/anotherPath/*`pedido para. A Porta Frontal Azure seleciona a Rota 2 com base num jogo de domínio mais específico, apenas para não encontrar padrões de caminho correspondentes nas rotas.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [criar um perfil de Porta Frontal Azure.](quickstart-create-front-door.md)
- Aprenda a [adicionar um domínio personalizado na Porta frontal do Azure](front-door-custom-domain.md).
- Saiba como [ativar HTTPS num domínio personalizado](front-door-custom-domain-https.md).
