---
title: Tipos de ponto de extremidade do Gerenciador de tráfego | Microsoft Docs
description: Este artigo explica os diferentes tipos de pontos de extremidade que podem ser usados com o Gerenciador de tráfego do Azure
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: allensu
ms.openlocfilehash: 1b72fc510543ff57f5fac1b03e76df7ffed3fbfe
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333819"
---
# <a name="traffic-manager-endpoints"></a>Pontos finais do Gestor de Tráfego

Gerenciador de Tráfego do Microsoft Azure permite que você controle como o tráfego de rede é distribuído para implantações de aplicativo em execução em data centers diferentes. Você configura cada implantação de aplicativo como um "ponto de extremidade" no Gerenciador de tráfego. Quando o Traffic Manager recebe uma solicitação DNS, ele escolhe um ponto de extremidade disponível para retornar na resposta DNS. O Gerenciador de tráfego baseia a escolha no status do ponto de extremidade atual e o método de roteamento de tráfego. Para obter mais informações, consulte [como funciona o Gerenciador de tráfego](traffic-manager-how-it-works.md).

Há três tipos de ponto de extremidade com suporte do Gerenciador de tráfego:

* Os **pontos de extremidade do Azure** são usados para serviços hospedados no Azure.
* Os **pontos de extremidade externos** são usados para endereços IPv4/IPv6, FQDNs ou para serviços hospedados fora do Azure que podem ser locais ou com um provedor de hospedagem diferente.
* **Pontos de extremidade aninhados** são usados para combinar perfis do Gerenciador de tráfego para criar esquemas de roteamento de tráfego mais flexíveis para dar suporte às necessidades de implantações maiores e mais complexas.

Não há nenhuma restrição sobre como os pontos de extremidade de diferentes tipos são combinados em um único perfil do Gerenciador de tráfego. Cada perfil pode conter qualquer combinação de tipos de ponto de extremidade.

As seções a seguir descrevem cada tipo de ponto de extremidade em maior profundidade.

## <a name="azure-endpoints"></a>Pontos de extremidade do Azure

Os pontos de extremidade do Azure são usados para serviços baseados no Azure no Gerenciador de tráfego. Há suporte para os seguintes tipos de recursos do Azure:

* Serviços de nuvem PaaS.
* Aplicações Web
* Slots do aplicativo Web
* Recursos de PublicIPAddress (que podem ser conectados a VMs diretamente ou por meio de um Azure Load Balancer). O publicIpAddress deve ter um nome DNS atribuído para ser usado em um perfil do Gerenciador de tráfego.

Os recursos do PublicIPAddress são Azure Resource Manager recursos. Eles não existem no modelo de implantação clássico. Portanto, eles só têm suporte nas experiências de Azure Resource Manager do Gerenciador de tráfego. Os outros tipos de ponto de extremidade têm suporte por meio do Resource Manager e do modelo de implantação clássico.

Ao usar os pontos de extremidade do Azure, o Gerenciador de tráfego detecta quando um aplicativo Web é interrompido e iniciado. Esse status é refletido no status do ponto de extremidade. Consulte [monitoramento de ponto de extremidade do Gerenciador de tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status) para obter detalhes. Quando o serviço subjacente é interrompido, o Gerenciador de tráfego não executa verificações de integridade do ponto de extremidade ou o tráfego direto para o ponto de extremidade. Nenhum evento de cobrança do Gerenciador de tráfego ocorre para a instância interrompida. Quando o serviço é reiniciado, a cobrança é retomada e o ponto de extremidade fica qualificado para receber o tráfego. Essa detecção não se aplica a pontos de extremidade PublicIpAddress.

## <a name="external-endpoints"></a>Pontos de extremidade externos

Os pontos de extremidade externos são usados para endereços IPv4/IPv6, FQDNs ou para serviços fora do Azure. O uso de pontos de extremidade de endereço IPv4/IPv6 permite que o Gerenciador de tráfego Verifique a integridade dos pontos de extremidade sem exigir um nome DNS para eles. Como resultado, o Gerenciador de tráfego pode responder a consultas com registros A/AAAA ao retornar esse ponto de extremidade em uma resposta. Os serviços fora do Azure podem incluir um serviço hospedado localmente ou com um provedor diferente. Os pontos de extremidade externos podem ser usados individualmente ou combinados com Pontos de Extremidade do Azure no mesmo perfil do Gerenciador de tráfego, exceto os pontos de extremidade especificados como endereços IPv4 ou IPv6 que só podem ser pontos de extremidade externos. A combinação dos pontos de extremidade do Azure com pontos de extremidade externos permite vários cenários:

* Fornecer maior redundância para um aplicativo local existente em um modelo de failover ativo-ativo ou ativo-passivo usando o Azure. 
* Rotear o tráfego para pontos de extremidade que não têm um nome DNS associado a eles. Além disso, diminua a latência de pesquisa de DNS geral removendo a necessidade de executar uma segunda consulta DNS para obter um endereço IP de um nome DNS retornado.
* Reduza a latência de aplicativos para usuários em todo o mundo, estenda um aplicativo local existente para locais geográficos adicionais no Azure. Para obter mais informações, consulte [Roteamento de tráfego de "desempenho](traffic-manager-routing-methods.md#performance)" do Gerenciador de tráfego.
* Forneça capacidade adicional para um aplicativo local existente, continuamente ou como uma solução de "intermitência para nuvem" para atender a um pico de demanda usando o Azure.

Em determinados casos, é útil usar pontos de extremidade externos para fazer referência aos serviços do Azure (para obter exemplos, consulte as [perguntas frequentes](traffic-manager-faqs.md#traffic-manager-endpoints)). Nesse caso, as verificações de integridade são cobradas na taxa de pontos de extremidade do Azure, não na taxa de pontos de extremidade externos. No entanto, ao contrário dos pontos de extremidade do Azure, se você parar ou excluir o serviço subjacente, a cobrança de verificação de integridade continuará até você desabilitar ou excluir o ponto de extremidade no Gerenciador de tráfego.

## <a name="nested-endpoints"></a>Pontos de extremidade aninhados

Os pontos de extremidade aninhados combinam vários perfis do Gerenciador de tráfego para criar esquemas flexíveis de roteamento de tráfego e dar suporte às necessidades de implantações maiores e complexas. Com pontos de extremidade aninhados, um perfil "filho" é adicionado como um ponto de extremidades a um perfil "pai". Os perfis filho e pai podem conter outros pontos de extremidade de qualquer tipo, incluindo outros perfis aninhados. Para obter mais informações, consulte [perfis aninhados do Gerenciador de tráfego](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Aplicativos Web como pontos de extremidade

Algumas considerações adicionais se aplicam ao configurar aplicativos Web como pontos de extremidade no Gerenciador de tráfego:

1. Somente os aplicativos Web no SKU ' Standard ' ou superior estão qualificados para uso com o Gerenciador de tráfego. Tentativas de adicionar um aplicativo Web de uma SKU inferior falham. O downgrade da SKU de um aplicativo Web existente resulta no Gerenciador de tráfego que não envia mais tráfego para o aplicativo Web. Para obter mais informações sobre os planos com suporte, consulte os [planos do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Quando um ponto de extremidade recebe uma solicitação HTTP, ele usa o cabeçalho "host" na solicitação para determinar qual aplicativo Web deve atender à solicitação. O cabeçalho de host contém o nome DNS usado para iniciar a solicitação, por exemplo, ' contosoapp.azurewebsites.net '. Para usar um nome DNS diferente com seu aplicativo Web, o nome DNS deve ser registrado como um nome de domínio personalizado para o aplicativo. Ao adicionar um ponto de extremidade do aplicativo Web como um ponto de extremidade do Azure, o nome DNS do perfil do Gerenciador de tráfego é registrado automaticamente para o aplicativo. Esse registro é removido automaticamente quando o ponto de extremidade é excluído.
3. Cada perfil do Gerenciador de tráfego pode ter no máximo um ponto de extremidade do aplicativo Web de cada região do Azure. Para contornar essa restrição, você pode configurar um aplicativo Web como um ponto de extremidade externo. Para obter mais informações, consulte as [perguntas frequentes](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Habilitando e desabilitando pontos de extremidade

Desabilitar um ponto de extremidade no Gerenciador de tráfego pode ser útil para remover temporariamente o tráfego de um ponto de extremidade que está no modo de manutenção ou sendo reimplantado. Depois que o ponto de extremidade estiver em execução novamente, ele poderá ser habilitado novamente.

Os pontos de extremidade podem ser habilitados e desabilitados por meio do portal do Gerenciador de tráfego, do PowerShell, da CLI ou da API REST.

> [!NOTE]
> Desabilitar um ponto de extremidade do Azure não tem nada a ver com seu estado de implantação no Azure. Um serviço do Azure (como uma VM ou aplicativo Web permanece em execução e pode receber tráfego mesmo quando desabilitado no Gerenciador de tráfego. O tráfego pode ser resolvido diretamente para a instância do serviço em vez de por meio do nome DNS do perfil do Gerenciador de tráfego. Para obter mais informações, consulte [como funciona o Gerenciador de tráfego](traffic-manager-how-it-works.md).

A qualificação atual de cada ponto de extremidade para receber tráfego depende dos seguintes fatores:

* O status do perfil (habilitado/desabilitado)
* O status do ponto de extremidade (habilitado/desabilitado)
* Os resultados das verificações de integridade para esse ponto de extremidade

Para obter detalhes, consulte [monitoramento de ponto de extremidade do Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Como o Gerenciador de tráfego funciona no nível do DNS, ele não pode influenciar as conexões existentes com nenhum ponto de extremidade. Quando um ponto de extremidade está indisponível, o Gerenciador de tráfego direciona novas conexões para outro ponto de extremidade disponível. No entanto, o host por trás do ponto de extremidade desabilitado ou não íntegro pode continuar a receber tráfego por meio de conexões existentes até que essas sessões sejam encerradas. Os aplicativos devem limitar a duração da sessão para permitir que o tráfego dissipe de conexões existentes.

Se todos os pontos de extremidade em um perfil estiverem desabilitados ou se o próprio perfil estiver desabilitado, o Gerenciador de tráfego enviará uma resposta "NXDOMAIN" para uma nova consulta DNS.

## <a name="faqs"></a>FAQs

* [Posso usar o Gerenciador de tráfego com pontos de extremidade de várias assinaturas?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Posso usar o Gerenciador de tráfego com slots de ' preparo ' do serviço de nuvem?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [O Gerenciador de tráfego dá suporte a pontos de extremidade IPv6?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-ipv6-endpoints)

* [Posso usar o Gerenciador de tráfego com mais de um aplicativo Web na mesma região?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Como fazer mover os pontos de extremidade do Azure do meu perfil do Gerenciador de tráfego para um grupo de recursos diferente?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group)

## <a name="next-steps"></a>Passos Seguintes

* Saiba [como funciona o Gerenciador de tráfego](traffic-manager-how-it-works.md).
* Saiba mais sobre o [monitoramento de ponto de extremidade](traffic-manager-monitoring.md)do Traffic Manager e o failover automático.
* Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Traffic Manager.
