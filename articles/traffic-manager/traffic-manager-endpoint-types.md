---
title: Tipos de fim do gestor de tráfego / Microsoft Docs
description: Este artigo explica diferentes tipos de pontos finais que podem ser usados com o Gestor de Tráfego Azure
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rohink
ms.openlocfilehash: 3d8f899a7899243129d31c2620a51dc764a8e917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250936"
---
# <a name="traffic-manager-endpoints"></a>Pontos finais do Gestor de tráfego

O Microsoft Azure Traffic Manager permite-lhe controlar a forma como o tráfego de rede é distribuído para implementações de aplicações em execução em diferentes datacenters. Irá configurar cada implementação de aplicação como um "ponto final" no Gestor de Tráfego. Quando o Gestor de Tráfego recebe um pedido de DNS, seleciona um ponto final disponível para devolver na resposta ao DNS. O gestor de tráfego baseia a escolha no estado atual do ponto final e no método de encaminhamento de tráfego. Para mais informações, consulte como funciona o [Gestor de Tráfego.](traffic-manager-how-it-works.md)

Existem três tipos de ponto final suportados pelo Gestor de Tráfego:

* Os **pontos finais do Azure** são utilizados pelos serviços alojados no Azure.
* Os **pontos finais externos** são utilizados para endereços IPv4/IPv6, FQDNs ou serviços alojados fora do Azure, que podem estar no local ou pertencer a um fornecedor de alojamento diferente.
* Os **pontos finais aninhados** são utilizados para combinar perfis do Gestor de Tráfego de forma a criar esquemas de encaminhamento de tráfego mais flexíveis para suportar as necessidades de implementações mais complexas e de maior dimensão.

Não existem restrições sobre a forma como os pontos finais de diferentes tipos são combinados num só perfil do Gestor de Tráfego. Cada perfil pode conter qualquer combinação de tipos de ponto final.

As seguintes secções descrevem cada tipo de ponto final em maior profundidade.

## <a name="azure-endpoints"></a>Pontos finais do Azure

Os pontos finais azure são utilizados para serviços baseados no Azure em Traffic Manager. São suportados os seguintes tipos de recursos Azure:

* Serviços de nuvem PaaS.
* Aplicações Web
* Slots de aplicativos web
* Recursos PublicIPAddress (que podem ser ligados a VMs diretamente ou através de um Equilíbrio de Carga Azure). O publicIpAddress deve ter um nome DNS atribuído para ser usado num perfil do Gestor de Tráfego.

Os recursos do PublicIPAddress são recursos do Gestor de Recursos Azure. Não existem no modelo clássico de implantação. Assim, são apenas apoiados nas experiências do Gestor de Recursos Azure do Gestor de Tráfego. Os outros tipos de pontos finais são suportados através do Gestor de Recursos e do modelo clássico de implementação.

Ao utilizar pontos finais Azure, o Traffic Manager deteta quando uma Aplicação Web é interrompida e iniciada. Este estatuto reflete-se no estado do ponto final. Consulte [a monitorização](traffic-manager-monitoring.md#endpoint-and-profile-status) do ponto final do Gestor de Tráfego para obter mais detalhes. Quando o serviço subjacente é interrompido, o Gestor de Tráfego não realiza controlos de saúde de ponto final ou tráfego direto para o ponto final. Não há eventos de faturação do Gestor de Tráfego para a instância de paragem. Quando o serviço é reiniciado, a faturação retoma e o ponto final é elegível para receber tráfego. Esta deteção não se aplica aos pontos finais do PublicIpAddress.

## <a name="external-endpoints"></a>Pontos finais externos

Os pontos finais externos são utilizados para endereços IPv4/IPv6, FQDNs ou para serviços fora do Azure. A utilização de pontos finais de endereço IPv4/IPv6 permite ao gestor de tráfego verificar a saúde dos pontos finais sem exigir um nome DNS para os mesmos. Como resultado, o Gestor de Tráfego pode responder a consultas com registos A/AAAA ao devolver esse ponto final numa resposta. Os serviços fora do Azure podem incluir um serviço hospedado no local ou com um fornecedor diferente. Os pontos finais externos podem ser utilizados individualmente ou combinados com pontos finais Azure no mesmo perfil do Traffic Manager, exceto para pontos finais especificados como endereços IPv4 ou IPv6 que só podem ser pontos finais externos. Combinar pontos finais Azure com pontos finais externos permite vários cenários:

* Proporcionar um maior redundância para uma aplicação existente no local, quer num modelo ativo ativo ou passivo ativo utilizando o Azure. 
* Encaminhe o tráfego para pontos finais que não tenham um nome DNS associado a eles. Além disso, diminua a latência geral de lupa de procuração dNS removendo a necessidade de executar uma segunda consulta de DNS para obter um endereço IP de um nome DNS devolvido.
* Reduza a latência de aplicações para utilizadores em todo o mundo, alargar uma aplicação existente no local para localizações geográficas adicionais em Azure. Para mais informações, consulte o [encaminhamento de tráfego 'Performance' do Gestor de Tráfego.](traffic-manager-routing-methods.md#performance)
* Fornecer capacidade adicional para uma aplicação existente no local, contínua ou como uma solução "burst-to-cloud" para fazer face a um pico de procura utilizando o Azure.

Em certos casos, é útil utilizar pontos finais externos para serviços de referência Azure (por exemplo, ver [faQ](traffic-manager-faqs.md#traffic-manager-endpoints)). Neste caso, os controlos de saúde são faturados à taxa de pontos finais do Azure, e não à taxa de pontos finais externos. No entanto, ao contrário dos pontos finais do Azure, se parar ou eliminar o serviço subjacente, a faturação do exame de saúde continua até desativar ou eliminar o ponto final no Traffic Manager.

## <a name="nested-endpoints"></a>Pontos finais aninhados

Os pontos finais aninhados combinam vários perfis do Traffic Manager para criar esquemas flexíveis de encaminhamento de tráfego e apoiar as necessidades de implementações maiores e complexas. Com os pontos finais da Nested, um perfil de "criança" é adicionado como um ponto final para um perfil 'pai'. Tanto os perfis da criança como dos pais podem conter outros pontos finais de qualquer tipo, incluindo outros perfis aninhados. Para mais informações, consulte [os perfis do Gestor de Tráfego.](traffic-manager-nested-profiles.md)

## <a name="web-apps-as-endpoints"></a>Web Apps como pontos finais

Algumas considerações adicionais aplicam-se ao configurar as Aplicações Web como pontos finais no Traffic Manager:

1. Apenas as Aplicações Web no SKU 'Standard' ou superior são elegíveis para utilização com o Traffic Manager. Tenta adicionar uma Aplicação Web de uma falha sku inferior. A degradação do SKU de uma Aplicação Web existente resulta em Traffic Manager deixar de enviar tráfego para essa Web App. Para obter mais informações sobre planos suportados consulte os Planos de [Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Quando um ponto final recebe um pedido HTTP, utiliza o cabeçalho 'anfitrião' no pedido para determinar qual a Aplicação Web que deve servir o pedido. O cabeçalho do hospedeiro contém o nome DNS utilizado para iniciar o pedido, por exemplo, "contosoapp.azurewebsites.net". Para utilizar um nome DNS diferente com a sua Web App, o nome DNS deve ser registado como um nome de domínio personalizado para a App. Ao adicionar um ponto final da Web App como ponto final do Azure, o nome DNS do perfil do Gestor de Tráfego está automaticamente registado para a App. Este registo é automaticamente removido quando o ponto final é eliminado.
3. Cada perfil do Traffic Manager pode ter, no máximo, um ponto final da Web App de cada região do Azure. Para contornar esta restrição, pode configurar uma Aplicação Web como um ponto final externo. Para mais informações, consulte as [FAQ](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Habilitar e desativar pontos finais

Desativar um ponto final no Traffic Manager pode ser útil para remover temporariamente o tráfego de um ponto final que está em modo de manutenção ou ser reimplantado. Uma vez que o ponto final esteja a funcionar novamente, pode ser reativado.

Os pontos finais podem ser ativados e desativados através do portal Traffic Manager, PowerShell, CLI ou REST API.

> [!NOTE]
> Desativar um ponto final do Azure não tem nada a ver com o seu estado de implantação em Azure. Um serviço Azure (como um VM ou Web App continua em execução e capaz de receber tráfego mesmo quando desativado no Traffic Manager. O tráfego pode ser endereçado diretamente à instância de serviço e não através do nome DNS do perfil do Gestor de Tráfego. Para mais informações, consulte como funciona o [Gestor de Tráfego.](traffic-manager-how-it-works.md)

A elegibilidade atual de cada ponto final para receber tráfego depende dos seguintes fatores:

* O estado do perfil (ativado/desativado)
* O estado do ponto final (ativado/desativado)
* Os resultados dos exames de saúde para esse ponto final

Para mais detalhes, consulte [a monitorização](traffic-manager-monitoring.md#endpoint-and-profile-status)do ponto final do Gestor de Tráfego .

> [!NOTE]
> Uma vez que o Gestor de Tráfego trabalha ao nível do DNS, não é capaz de influenciar as ligações existentes a qualquer ponto final. Quando um ponto final não está disponível, o Traffic Manager direciona novas ligações para outro ponto final disponível. No entanto, o hospedeiro por detrás do ponto final desativado ou pouco saudável pode continuar a receber tráfego através das ligações existentes até que essas sessões sejam terminadas. As candidaturas devem limitar a duração da sessão para permitir que o tráfego escorra das ligações existentes.

Se todos os pontos finais de um perfil estiverem desativados, ou se o perfil em si estiver desativado, então o Gestor de Tráfego envia uma resposta 'NXDOMAIN' a uma nova consulta de DNS.

## <a name="faqs"></a>FAQs

* [Posso usar o Traffic Manager com pontos finais de várias subscrições?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Posso usar o Gestor de Tráfego com slots de 'Staging' do Serviço cloud?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [O Gestor de Tráfego suporta pontos finais IPv6?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-ipv6-endpoints)

* [Posso usar o Traffic Manager com mais de uma Web App na mesma região?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Como posso mover os pontos finais do meu perfil de Gestor de Tráfego Para um grupo de recursos diferente?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Passos seguintes

* Saiba como funciona o [Gestor de Tráfego.](traffic-manager-how-it-works.md)
* Saiba mais sobre [a monitorização do ponto final](traffic-manager-monitoring.md)do Gestor de Tráfego e a falha automática .
* Conheça os [métodos de encaminhamento](traffic-manager-routing-methods.md)de tráfego do Gestor de Tráfego .
