---
title: Porta da Frente Azure - Suporte para domínios wildcard
description: Este artigo ajuda-o a entender como o Azure Front Door suporta o mapeamento e a gestão de domínios wildcard na lista de domínios personalizados
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537443"
---
# <a name="wildcard-domains"></a>Domínios wildcard

Além de domínios ápice e subdomínios, também pode mapear um nome de domínio wildcard para a sua lista de anfitriões frontend ou domínios personalizados do seu perfil front door. Ter domínios wildcard no seu config Front Door simplifica o comportamento de encaminhamento de tráfego para vários subdomínios para uma API, aplicação ou website a partir da mesma regra de encaminhamento sem ter que modificar a configuração para adicionar e/ou especificar cada subdomínio separadamente. Como exemplo, pode definir o `customer1.contoso.com`encaminhamento para , `customer2.contoso.com`e `customerN.contoso.com` usando a `*.contoso.com`mesma regra de encaminhamento adicionando um domínio wildcard .

Alguns dos cenários-chave que são resolvidos com suporte para domínios wildcard incluem:

- Já não é necessário embarcar em cada subdomínio da porta da frente e, em seguida, permitir que https ligue um certificado para cada subdomínio.
- Se uma aplicação adicionar um novo subdomínio, então já não é necessário alterar a configuração da sua produção Front Door. Caso contrário, antes, exigia a adição do subdomínio, vinculando-lhe um certificado, anexando uma política de firewall de aplicação web (WAF), adicionando o domínio a diferentes regras de encaminhamento.

> [!NOTE]
> Atualmente, os domínios wildcard são suportados apenas através da API, PowerShell e CLI. O suporte para a gestão de domínios wildcard através do portal Azure não está disponível.

## <a name="adding-wildcard-domains"></a>Adicionar domínios wildcard

Pode embarcar num domínio wildcard sob a secção Frontend Hosts ou Domains. Semelhante aos subdomínios, a Porta frontal valida que existe também um mapeamento CNAME para o seu domínio wildcard. Este mapeamento DNS pode ser `*.contoso.com` um mapeamento `contoso.azurefd.net` CNAME direto como mapeado para ou através do afdcheck mapeamento temporário como `afdverify.contoso.com` mapeado para `afdverify.contoso.azurefd.net` validar mapa CNAME para wildcard também (Azure DNS suporta registos wildcard).

Também pode adicionar tantos subdomínios de nível único do domínio wildcard nos anfitriões frontais se não estiverem a atingir o máximo. limite dos anfitriões frontend. Esta funcionalidade pode ser necessária para definir uma rota diferente para um subdomínio do que o resto dos domínios (a partir do domínio wildcard) ou ter uma política waf diferente para um subdomínio específico. Assim, `*.contoso.com` permitirá `foo.contoso.com` adicionar sem ter que `foo.bar.contoso.com` provar novamente a propriedade do `*.contoso.com`domínio, mas não porque este não é um subdomínio de nível único de . Para `foo.bar.contoso.com` adicionar sem validação adicional da propriedade do domínio, `*.bar.contosonews.com` terá de ser adicionado.

### <a name="limitations"></a>Limitações

1. Se um domínio wildcard for adicionado num dado perfil da Porta da Frente, então o mesmo não pode ser adicionado a qualquer outro perfil da Porta da Frente. 
2. Se um domínio wildcard for adicionado num dado perfil front door, então quaisquer subdomínios desse domínio wildcard não podem ser adicionados a outra Porta Frontal ou a um CDN Azure a partir do perfil da Microsoft
3. Se um subdomínio de um domínio wildcard for adicionado quer num perfil front door quer num CDN Azure a partir do perfil da Microsoft, então o domínio wildcard não pode ser adicionado a qualquer outro perfil da Porta Da Frente. 
4. Se dois perfis (Porta Frontal ou CDN Azure da Microsoft) tiverem vários subdomínios de um domínio raiz, então os domínios wildcard não podem ser adicionados em nenhum dos perfis.

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>Ligação de certificados para domínios wildcard e seus subdomínios

Para aceitar o tráfego HTTPS no seu domínio wildcard, deve ativar HTTPS no domínio wildcard. O certificado vinculativo para o domínio wildcard requer um certificado wildcard, ou seja, o nome do certificado também deve ter o domínio wildcard.

> [!NOTE]
> Atualmente, apenas a utilização da sua própria opção de certificado SSL personalizada está disponível para ativar HTTPS para domínios wildcard. Os certificados geridos pela Porta Da Frente não podem ser utilizados para domínios wildcard. 

Pode optar por utilizar o mesmo certificado wildcard do seu Cofre chave para os subdomínios, ou, também é suportado o uso de certificados geridos pela porta frontal para subdomínios.
Se for adicionado um subdomínio para um domínio wildcard e o domínio wildcard já tiver um certificado associado, então https para este subdomínio não pode ser desativado. O subdomínio utilizará, por defeito, a ligação do certificado do domínio wildcard, a menos que seja ultrapassado por um certificado de cofre chave diferente ou certificado gerido pela Porta Frontal.

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>Firewall de aplicação web para domínios wildcard e seus subdomínios

As políticas de WAF podem ser anexadas a um domínio wildcard semelhante a outros domínios. Uma política de WAF diferente pode ser aplicada a um subdomínio de um domínio wildcard. Para os subdomínios, deve especificar explicitamente a política waf a utilizar e mesmo que seja a mesma política que o domínio wildcard. Os subdomínios **não** herdarão automaticamente a política de WAF do domínio wildcard.

Se tiver um cenário em que não queira que o WAF corra para um subdomínio, então pode criar uma política waf em branco sem regras geridas ou personalizadas.

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>Regras de encaminhamento para domínios wildcard e seus subdomínios

Ao configurar uma regra de encaminhamento, pode selecionar um domínio wildcard como anfitrião frontal. Você também pode ter diferentes comportamentos de rota para domínio wildcard vs. subdomínios. Como descrito na forma como a Porta Da Frente faz a correspondência de [rotas,](front-door-route-matching.md)a correspondência mais específica para o domínio através de diferentes regras de encaminhamento será escolhida no tempo de funcionamento.

> [!WARNING]
> Se tiver duas regras de encaminhamento, como a **Route 1**: `*.foo.com/*` mapeada para backend Pool A `bar.foo.com/anotherPath/*`e Route **2**: `bar.foo.com/somePath/*` mapeada para backend Pool B e se um pedido chegar, então os seus clientes verão falhas, uma vez que a Porta Da Frente não encontrará qualquer correspondência em ambas as rotas. Isto porque, de acordo com o algoritmo de correspondência de [rotas,](front-door-route-matching.md)a Porta Frontal irá selecionar a Rota 2 com base em correspondência de domínio mais específica, mas apenas para descobrir que não existem padrões de caminho correspondentes. 


## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Aprenda a [adicionar um domínio personalizado na Porta da Frente](front-door-custom-domain.md).
- Saiba como [ativar HTTPS num domínio personalizado](front-door-custom-domain-https.md).
