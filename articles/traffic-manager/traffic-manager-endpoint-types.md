---
title: Tipos de ponto final do Gestor de tráfego | Documentos da Microsoft
description: Este artigo explica os diferentes tipos de pontos de extremidade que podem ser utilizados com o Gestor de tráfego do Azure
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: b609a0ace0b428e1af81634c6a25485e3a5e89bb
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916663"
---
# <a name="traffic-manager-endpoints"></a>Pontos finais do Gestor de tráfego
O Gestor de tráfego do Microsoft Azure permite-lhe controlar a forma como o tráfego de rede é distribuído para implementações de aplicações em execução em datacenters diferentes. Configurar a cada implementação de aplicação como um ponto 'final' no Gestor de tráfego. Quando o Gestor de tráfego recebe um pedido DNS, ele escolhe um ponto de extremidade disponível para devolver na resposta DNS. O Gestor de tráfego bases a escolha sobre o estado atual do ponto final e o método de encaminhamento de tráfego. Para obter mais informações, consulte [como funciona o Gestor de tráfego](traffic-manager-how-it-works.md).

Existem três tipos de ponto de extremidade suportada pelo Gestor de tráfego:
* **Pontos finais do Azure** são utilizados para os serviços alojados no Azure.
* **Pontos finais externos** são utilizados para endereços IPv4/IPv6 ou, para os serviços alojados fora do Azure que pode ser local ou num fornecedor de alojamento diferentes.
* **Aninhados pontos finais** são utilizados para combinar os perfis do Gestor de tráfego para criar esquemas de encaminhamento de tráfego de mensagens em fila mais flexíveis para suportar as necessidades de Implantações maiores e mais complexas.

Não existe nenhuma restrição como pontos finais de diferentes tipos são combinados num único perfil do Gestor de tráfego. Cada perfil pode conter qualquer combinação de tipos de ponto final.

As secções seguintes descrevem cada tipo de ponto final mais detalhadamente.

## <a name="azure-endpoints"></a>Pontos finais do Azure

Pontos finais do Azure são utilizados para serviços baseados no Azure no Gestor de tráfego. São suportados os seguintes tipos de recursos do Azure:

* Serviços de cloud de PaaS.
* Aplicações Web
* Ranhuras de aplicação Web
* Recursos PublicIPAddress (que podem ser ligado a VMs diretamente ou através de um balanceador de carga do Azure). O publicIpAddress tem de ter um nome DNS atribuído para ser usado num perfil do Gestor de tráfego.

Recursos PublicIPAddress são recursos do Azure Resource Manager. Eles não existem no modelo de implementação clássica. Portanto, elas são apenas suportadas no Gestor de tráfego do Azure Resource Manager experiências. Os outros tipos de ponto de extremidade são suportados através do Resource Manager e o modelo de implementação clássica.

Quando utilizar pontos finais do Azure, o Gestor de tráfego Deteta quando uma aplicação Web, serviço cloud ou uma VM de IaaS do "Clássico" é parada e iniciada. Este estado é refletido no estado de ponto final. Ver [monitorização de pontos finais do Gestor de tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status) para obter detalhes. Quando o serviço subjacente está parado, o Gestor de tráfego não efetua as verificações de estado de funcionamento do ponto final ou direcionar o tráfego para o ponto final. Não existem eventos de faturação do Gestor de tráfego ocorrerem para a instância de parado. Quando o serviço é reiniciado, é elegível para receber o tráfego de retoma de faturação e o ponto final. Esta deteção não se aplica aos pontos finais PublicIpAddress.

## <a name="external-endpoints"></a>Pontos finais externos

Pontos finais externos são utilizados para qualquer um dos endereços IPv4/IPv6 ou, para serviços fora do Azure. Uso de pontos finais do endereço IPv4/IPv6 permite tráfego manager verificar o estado de funcionamento dos pontos finais sem a necessidade de um nome DNS para os mesmos. Como resultado, o Gestor de tráfego pode responder a consultas com registos de A/AAAA ao retornar esse ponto final numa resposta. Serviços fora do Azure podem incluir um serviço alojado no local ou com um fornecedor diferente. Pontos finais externos podem ser utilizados individualmente ou combinados com pontos finais do Azure no perfil do Gestor de tráfego do mesmo, exceto para pontos de extremidade especificado como endereços IPv4 ou IPv6, que só podem ser a pontos finais externos. A combinação de pontos finais do Azure com pontos finais externos permite vários cenários:

* Fornece redundância de maior para uma aplicação no local existente em qualquer um de um modelo de ativação pós-falha ativa-ativa ou ativa-passiva com o Azure. 
* Encaminhar o tráfego para pontos de extremidade que não tem um nome DNS associado a eles. Além disso, diminua a latência de pesquisa DNS global, removendo a necessidade de executar uma segunda consulta DNS para obter um endereço IP de um nome DNS devolvido. 
* Reduzir a latência de aplicação para utilizadores em todo o mundo, expansão de um aplicativo no local existente para as localizações geográficas adicionais no Azure. Para obter mais informações, consulte [Gestor de tráfego "Performance" encaminhamento de tráfego](traffic-manager-routing-methods.md#performance).
* Fornece capacidade adicional para um existente no local aplicativo, continuamente ou como uma solução de "burst-to-cloud" para satisfazer um pico no pedido com o Azure.

Em certos casos, é útil utilizar pontos finais externos para fazer referência a serviços do Azure (para obter exemplos, consulte a [FAQ](traffic-manager-faqs.md#traffic-manager-endpoints)). Neste caso, as verificações de estado de funcionamento são faturadas à tarifa de pontos finais do Azure, não a taxa de pontos finais externos. No entanto, ao contrário dos pontos finais do Azure, se para ou eliminar o serviço subjacente, verificação de integridade faturação continua até que desativa ou elimina o ponto final no Gestor de tráfego.

## <a name="nested-endpoints"></a>Pontos de extremidade aninhados

Pontos de extremidade aninhados combinam vários perfis do Gestor de tráfego para criar esquemas de encaminhamento de tráfego flexíveis e suporte às necessidades de Implantações maiores e complexas. Com pontos finais de aninhados, um perfil de "subordinado" é adicionado como um ponto final a um perfil de 'parent'. Perfis de subordinados e principais podem conter outros pontos finais de qualquer tipo, incluindo outros perfis aninhados. Para obter mais informações, consulte [aninhada de perfis do Gestor de tráfego](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>As aplicações como pontos finais de Web

Algumas considerações adicionais aplicam-se ao configurar aplicações Web como pontos finais no Traffic Manager:

1. Só as aplicações Web no SKU "Standard" ou superior são elegíveis para utilização com o Gestor de tráfego. Falha de tentativas para adicionar uma aplicação Web de um SKU inferior. Desatualização de SKU de uma aplicação Web existente resulta no Gestor de tráfego deixe de enviar tráfego para essa aplicação Web. Para obter mais informações sobre os planos suportados, consulte o [planos do serviço de aplicações](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Quando um ponto de extremidade recebe um pedido HTTP, ele usa o cabeçalho de "host" no pedido para determinar qual aplicativo Web deverá responder ao pedido. O cabeçalho de anfitrião contém o nome DNS utilizado para iniciar o pedido, por exemplo "contosoapp.azurewebsites.net'. Para utilizar um nome DNS diferente com a sua aplicação Web, o nome DNS tem de ser registado como um nome de domínio personalizado para a aplicação. Ao adicionar um ponto de final de aplicação Web como um ponto final do Azure, o nome DNS de perfil do Gestor de tráfego é registrado automaticamente para a aplicação. Este registo é removido automaticamente quando o ponto final é eliminado.
3. Cada perfil de Gestor de tráfego pode ter no máximo uma aplicação Web ponto final de cada região do Azure. Para contornar esta restrição, pode configurar uma aplicação Web como um ponto final externo. Para obter mais informações, consulte a [FAQ](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Ativar e desativar pontos finais

Desativar um ponto final no Gestor de tráfego pode ser útil para remover temporariamente o tráfego de um ponto final que está no modo de manutenção ou que vai ser implementado. Quando o ponto final está novamente em execução, pode ser ativada novamente.

Pontos de extremidade podem ser ativados e desativados através do portal do Gestor de tráfego, PowerShell, CLI ou REST API.

> [!NOTE]
> Desativar um ponto final do Azure não tem nada a ver com o estado de implementação no Azure. Um serviço do Azure (por exemplo, uma VM ou a aplicação Web permanece em execução e para receber tráfego, mesmo quando desativado no Traffic Manager. É possível resolver o tráfego diretamente para a instância do serviço, em vez de através do nome DNS de perfil do Gestor de tráfego. Para obter mais informações, consulte [como funciona o Gestor de tráfego](traffic-manager-how-it-works.md).

A elegibilidade atual de cada ponto de extremidade para receber o tráfego depende dos seguintes fatores:

* O estado do perfil (ativado/desativado)
* O estado de ponto final (ativado/desativado)
* Os resultados do Estado de funcionamento verifica a existência de ponto de extremidade

Para obter detalhes, consulte [monitorização de pontos finais do Gestor de tráfego](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Uma vez que o Gestor de tráfego funciona ao nível do DNS, é não é possível influenciar as ligações existentes para qualquer ponto final. Quando um ponto final não estiver disponível, o Gestor de tráfego direciona novas ligações para outro ponto de extremidade disponível. No entanto, o anfitrião por trás do ponto final desativado ou mau estado de funcionamento pode continuar a receber tráfego através de ligações existentes até que essas sessões estão terminadas. Aplicativos devem limitar a duração de sessão para permitir o tráfego drenar a partir de ligações existentes.

Se todos os pontos finais num perfil estiverem desativados, ou se o próprio perfil está desabilitado, o Gestor de tráfego envia uma resposta de 'NXDOMAIN' para uma nova consulta DNS.


## <a name="next-steps"></a>Passos Seguintes

* Saiba mais [como funciona o Gestor de tráfego](traffic-manager-how-it-works.md).
* Saiba mais sobre o Gestor de tráfego [ativação pós-falha automática e monitorização do ponto de extremidade](traffic-manager-monitoring.md).
* Saiba mais sobre o Gestor de tráfego [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md).
