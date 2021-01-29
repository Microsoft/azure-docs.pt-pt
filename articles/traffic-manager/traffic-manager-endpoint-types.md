---
title: Tipos de pontos de terminação do gestor de tráfego | Microsoft Docs
description: Este artigo explica diferentes tipos de pontos finais que podem ser usados com Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: 7686f2f97da0113704216dcab741c063a80d3136
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051232"
---
# <a name="traffic-manager-endpoints"></a>Pontos finais do Gestor de Tráfego

O Microsoft Azure Traffic Manager permite-lhe controlar a forma como o tráfego de rede é distribuído para implementações de aplicações executadas em diferentes datacenters. Irá configurar cada implementação de aplicação como um "ponto final" no Gestor de Tráfego. Quando o Gestor de Tráfego recebe um pedido de DNS, seleciona um ponto final disponível para devolver na resposta ao DNS. O gestor de tráfego baseia a escolha no estado atual do ponto final e no método de encaminhamento de tráfego. Para mais informações, consulte [como funciona o Gestor de Tráfego.](traffic-manager-how-it-works.md)

Existem três tipos de ponto final suportados pelo Gestor de Tráfego:

* Os **pontos finais do Azure** são utilizados pelos serviços alojados no Azure.
* **Os pontos finais externos** são utilizados para endereços IPv4/IPv6, FQDNs ou para serviços alojados fora de Azure. Os serviços de tese podem ser no local ou com um fornecedor de hospedagem diferente.
* Os **pontos finais aninhados** são utilizados para combinar perfis do Gestor de Tráfego de forma a criar esquemas de encaminhamento de tráfego mais flexíveis para suportar as necessidades de implementações mais complexas e de maior dimensão.

Não há restrição de como os pontos finais de diferentes tipos são combinados num único perfil de Gestor de Tráfego. Cada perfil pode conter qualquer combinação de tipos de ponto final.

As seguintes secções descrevem cada tipo de ponto final em maior profundidade.

## <a name="azure-endpoints"></a>Pontos finais Azure

Os pontos finais do Azure são utilizados para serviços baseados em Azure em Traffic Manager. São suportados os seguintes tipos de recursos Azure:

* Serviços de nuvem PaaS.
* Aplicações Web
* Slots de aplicativos web
* Recursos PublicIPAddress (que podem ser ligados a VMs diretamente ou através de um Balançador de Carga Azure). O publicIpAddress deve ter um nome DNS atribuído para ser usado num perfil de Gestor de Tráfego.

Os recursos publicIPAddress são recursos do Gestor de Recursos Azure. Não existem no modelo clássico de implementação e só são suportados nas experiências do Gestor de Recursos Azure do Traffic Manager. Os outros tipos de pontos finais são suportados tanto através do Gestor de Recursos como do modelo clássico de implementação.

Ao utilizar os pontos finais do Azure, o Traffic Manager deteta quando uma Aplicação Web é interrompida e iniciada. Este estatuto reflete-se no estado do ponto final. Consulte [a monitorização do ponto final do Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status) para obter mais informações. Quando o serviço subjacente é interrompido, o Gestor de Tráfego não faz verificações de saúde no ponto final ou tráfego direto para o ponto final. Não ocorrem eventos de faturação do Traffic Manager para a paragem. Quando o serviço é reiniciado, a faturação retoma e o ponto final é elegível para receber tráfego. Esta deteção não se aplica aos pontos finais do PublicIpAddress.

## <a name="external-endpoints"></a>Pontos finais externos

Os pontos finais externos são utilizados para endereços IPv4/IPv6, FQDNs ou para serviços fora de Azure. A utilização de pontos finais de endereço IPv4/IPv6 permite ao gestor de tráfego verificar a saúde dos pontos finais sem necessitar de um nome DNS para os mesmos. Como resultado, o Traffic Manager pode responder a perguntas com registos A/AAAA ao devolver esse ponto final numa resposta. Os serviços fora do Azure podem incluir um serviço hospedado no local ou com um fornecedor diferente. Os pontos finais externos podem ser utilizados individualmente ou combinados com pontos finais do Azure no mesmo perfil de Gestor de Tráfego. A exceção é para os pontos finais especificados como endereços IPvv4 ou IPv6, que só podem ser pontos finais externos. Combinar pontos finais do Azure com pontos finais externos permite vários cenários:

* Proporcionar um maior despedimento para uma aplicação existente no local, quer num modelo activo-activo quer activo-passivo, utilizando o Azure. 
* Encaminhe o tráfego para pontos finais que não têm um nome DNS associado a eles. Também reduz a latência geral de procura de DNS, removendo a necessidade de executar uma segunda consulta de DNS para obter um endereço IP de um nome DNS devolvido.
* Reduza a latência da aplicação para utilizadores em todo o mundo, alargar uma aplicação existente no local para outros locais geográficos em Azure. Para obter mais informações, consulte o encaminhamento de [tráfego 'Performance' do Gestor de Tráfego](traffic-manager-routing-methods.md#performance).
* Fornecer mais capacidade para uma aplicação existente no local, quer continuamente, quer como solução "burst-to-cloud" para responder a um pico de procura utilizando o Azure.

Em alguns casos, é útil usar pontos finais externos para referenciar os serviços Azure. Consulte as [FAQ](traffic-manager-faqs.md#traffic-manager-endpoints) por exemplo. Os controlos de saúde são cobrados à taxa de pontos finais do Azure, e não à taxa de pontos finais externos. Ao contrário dos pontos finais do Azure, se parar ou apagar o serviço subjacente, a faturação do cheque de saúde continua. A faturação para assim que desativar ou eliminar o ponto final no Gestor de Tráfego.

## <a name="nested-endpoints"></a>Pontos finais aninhados

Os pontos finais aninhados combinam vários perfis de Gestor de Tráfego para criar esquemas flexíveis de encaminhamento de tráfego para suportar as necessidades de implementações maiores e complexas. Com os pontos finais de Aninhado, um perfil de "criança" é adicionado como ponto final a um perfil 'pai'. Tanto os perfis da criança como dos pais podem conter outros pontos finais de qualquer tipo, incluindo outros perfis aninhados. 

Para mais informações, consulte [os perfis do Gestor de Tráfego aninhado.](traffic-manager-nested-profiles.md)

## <a name="web-apps-as-endpoints"></a>Web Apps como pontos finais

Algumas considerações mais se aplicam ao configurar as Web Apps como pontos finais no Traffic Manager:

1. Apenas as Aplicações Web no SKU 'Standard' ou acima são elegíveis para utilização com o Traffic Manager. Tentativas de adicionar uma Aplicação Web de uma falha SKU inferior. A degradação do SKU de uma Aplicação Web existente resulta em Traffic Manager deixar de enviar tráfego para essa Web App. Para obter mais informações sobre planos suportados, consulte os [Planos de Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Quando um ponto final recebe um pedido HTTP, utiliza o cabeçalho 'anfitrião' no pedido para determinar qual a Web App que deve servir o pedido. O cabeçalho do anfitrião contém o nome DNS utilizado para iniciar o pedido, por exemplo ,"contosoapp.azurewebsites.net". Para utilizar um nome DNS diferente com a sua Web App, o nome DNS deve ser registado como um nome de domínio personalizado para a App. Ao adicionar um ponto final da Web App como ponto final do Azure, o nome DNS de perfil de Gestor de Tráfego é automaticamente registado para a App. Este registo é automaticamente removido quando o ponto final é eliminado.
3. Cada perfil de Gestor de Tráfego pode ter no máximo um ponto final da Web App de cada região do Azure. Para trabalhar em torno deste constrangimento, você pode configurar uma Web App como um ponto final externo. Para mais informações, consulte as [FAQ.](traffic-manager-faqs.md#traffic-manager-endpoints)

## <a name="enabling-and-disabling-endpoints"></a>Habilitar e desativar pontos finais

Desativar um ponto final no Traffic Manager pode ser útil para remover temporariamente o tráfego de um ponto final que se encontra em modo de manutenção ou que está a ser redistribuído. Uma vez que o ponto final esteja a funcionar novamente, pode ser re-activado.

Pode ativar ou desativar os pontos finais através do portal Traffic Manager, PowerShell, CLI ou REST API.

> [!NOTE]
> Desativar um ponto final do Azure não tem nada a ver com o seu estado de implantação em Azure. Um serviço Azure (como um VM ou Web App continua em funcionamento e capaz de receber tráfego mesmo quando desativado em Traffic Manager. O tráfego pode ser endereçado diretamente à instância de serviço e não através do nome DNS do perfil do Gestor de Tráfego. Para mais informações, consulte [como funciona o Gestor de Tráfego.](traffic-manager-how-it-works.md)

A elegibilidade atual de cada ponto final para receber tráfego depende dos seguintes fatores:

* O estado do perfil (ativado/desativado)
* O estado do ponto final (ativado/desativado)
* Os resultados dos exames de saúde para esse ponto final

Para mais informações, consulte [a monitorização do ponto final do Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Uma vez que o Gestor de Tráfego funciona ao nível do DNS, não é capaz de influenciar as ligações existentes a qualquer ponto final. Quando um ponto final não está disponível, o Gestor de Tráfego direciona novas ligações para outro ponto final disponível. No entanto, o hospedeiro por detrás do ponto final deficiente ou pouco saudável pode continuar a receber tráfego através das ligações existentes até que essas sessões sejam encerradas. As aplicações devem limitar a duração da sessão para permitir que o tráfego se escorra das ligações existentes.

Se todos os pontos finais de um perfil forem desativados, ou se o próprio perfil for desativado, então o Traffic Manager envia uma resposta 'NXDOMAIN' a uma nova consulta de DNS.

## <a name="faqs"></a>FAQs

* [Posso utilizar o Traffic Manager com pontos finais de várias subscrições?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Posso utilizar o Gestor de Tráfego com slots de 'Staging' do Serviço de Nuvem?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [O Gestor de Tráfego suporta pontos finais IPv6?](./traffic-manager-faqs.md#does-traffic-manager-support-ipv6-endpoints)

* [Posso usar o Traffic Manager com mais de uma Web App na mesma região?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Como posso mover os pontos finais do Azure do meu perfil de Gerente de Tráfego para um grupo de recursos diferente?](./traffic-manager-faqs.md#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Passos seguintes

* Saiba [como funciona o Gestor de Tráfego.](traffic-manager-how-it-works.md)
* Saiba mais sobre [a monitorização do ponto final](traffic-manager-monitoring.md)do Traffic Manager e a falha automática.
* Saiba mais sobre [os métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md)do Traffic Manager .