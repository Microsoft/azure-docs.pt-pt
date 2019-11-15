---
title: 'Azure ExpressRoute: fluxos de trabalho de configuração de circuito'
description: Esta página mostra os fluxos de trabalho para configurar o circuito do ExpressRoute e de peerings
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: ae6c2b7257ee6a8184f3a5bb002f24cb75a86d67
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083329"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Fluxos de trabalho do ExpressRoute para o aprovisionamento e estados dos circuitos
Esta página explica o serviço de aprovisionamento e encaminhamento de fluxos de trabalho de configuração num alto nível.

![fluxo de trabalho do circuito](./media/expressroute-workflows/expressroute-circuit-workflow.png)

A figura a seguir e correspondentes passos mostram as tarefas que tem de seguir para ter um circuito ExpressRoute aprovisionado ponto-a-ponto. 

1. Utilize o PowerShell para configurar um circuito do ExpressRoute. Siga as instruções no [circuitos do ExpressRoute criar](expressroute-howto-circuit-classic.md) artigo para obter mais detalhes.
2. Conectividade de ordem de fornecedor de serviços. Este processo varia. Contacte o seu fornecedor de conectividade para obter mais detalhes sobre como encomendar conectividade.
3. Certifique-se de que se o circuito foi aprovisionado com êxito, verificando o circuito do ExpressRoute através do PowerShell, o estado de aprovisionamento. 
4. Configure domínios de encaminhamento. Se o seu fornecedor de conectividade gerencia a camada 3 para, também irá configurar o encaminhamento para o seu circuito. Se o seu fornecedor de conectividade oferecer apenas serviços de camada 2, deve configurar o encaminhamento por diretrizes descritas a [requisitos de encaminhamento](expressroute-routing.md) e [configuração do encaminhamento](expressroute-howto-routing-classic.md) páginas.
   
   * Ativar o peering privado do Azure - ativar este peering ligar a VMs / implementados dentro de redes virtuais de serviços cloud.

   * Habilitar emparelhamento da Microsoft – Habilite esta para acessar o Office 365. Além disso, todos os serviços de PaaS do Azure são acessíveis através do peering da Microsoft.
     
     > [!IMPORTANT]
     > Tem de garantir que utilizar um proxy separado / edge para ligar à Microsoft que o utilizar para a Internet. Utilizar a mesma margem para ExpressRoute e a Internet, causam encaminhamento assimétrico e causar falhas de conectividade para a sua rede.
     > 
     > 
     
     ![fluxos de trabalho de encaminhamento](./media/expressroute-workflows/routing-workflow.png)
5. Ligar redes virtuais para circuitos do ExpressRoute - pode ligar redes virtuais para o seu circuito do ExpressRoute. Siga as instruções [para ligar VNets](expressroute-howto-linkvnet-arm.md) para o seu circuito. Nestas VNets podem ser na mesma subscrição do Azure como o circuito do ExpressRoute, ou pode ser numa subscrição diferente.

## <a name="expressroute-circuit-provisioning-states"></a>Circuito do ExpressRoute a aprovisionar Estados
Cada circuito ExpressRoute tem dois Estados:

* Estado de aprovisionamento do fornecedor de serviço
* Estado

Estado representa o estado de aprovisionamento da Microsoft. Esta propriedade é definida como ativado quando cria um circuito do Expressroute

O estado de aprovisionamento de fornecedor de conectividade representa o estado no lado do fornecedor de conectividade. Ele pode ser *NotProvisioned*, *aprovisionamento*, ou *aprovisionado*. O circuito do ExpressRoute tem de estar no Estado aprovisionado para que possa usá-lo.

### <a name="possible-states-of-an-expressroute-circuit"></a>Estados possíveis de um circuito do ExpressRoute
Esta secção lista os Estados possíveis para um circuito do ExpressRoute.

**No momento de criação**

Verá o circuito do ExpressRoute no seguinte estado quando executar o cmdlet do PowerShell para criar o circuito do ExpressRoute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Quando o fornecedor de conectividade está no processo de aprovisionamento do circuito**

Verá o circuito do ExpressRoute no seguinte estado assim que passar a chave de serviço para o fornecedor de conectividade e iniciou o processo de aprovisionamento.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Quando o fornecedor de conectividade concluiu o processo de aprovisionamento**

Verá o circuito do ExpressRoute no seguinte estado quando o fornecedor de conectividade concluiu o processo de aprovisionamento.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Aprovisionado e ativado é que o único Estado o circuito pode ter para que possa usá-lo. Se estiver a utilizar um fornecedor de camada 2, pode configurar o encaminhamento para o seu circuito apenas quando está neste estado.

**Quando o fornecedor de conectividade é desaprovisionar o circuito**

Se tiver solicitado o fornecedor de serviços para desaprovisionar o circuito do ExpressRoute, verá o circuito definido no seguinte estado quando o fornecedor do serviço terminar o processo de desaprovisionamento.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Pode optar por ativá-lo novamente se necessário, ou executar cmdlets do PowerShell para eliminar o circuito.  

> [!IMPORTANT]
> Se executar o cmdlet do PowerShell para eliminar o circuito quando está a ser aprovisionada a ServiceProviderProvisioningState ou aprovisionado a operação falhará. Trabalhe em conjunto com o seu fornecedor de conectividade para desaprovisionar o circuito do ExpressRoute primeiro e, em seguida, elimine o circuito. A Microsoft continuará a ter ao faturar o circuito até executar o cmdlet PowerShell para eliminar o circuito.
> 
> 

## <a name="routing-session-configuration-state"></a>Estado de configuração do encaminhamento sessão
O estado de aprovisionamento de BGP permite-lhe saber se foi ativada a sessão de BGP no limite da Microsoft. O estado tem de estar ativado para que possa ser capaz de usar o peering.

É importante verificar o estado da sessão BGP especialmente para peering da Microsoft. Além do Estado de aprovisionamento de BGP, há outro Estado chamado *anunciados Estado prefixos públicos*. O estado de prefixos públicos anunciados tem de constar *configurado* Estado, tanto para a sessão de BGP ficar operacional e para o roteamento funcionar ponto-a-ponto. 

Se o estado de prefixo públicos anunciados é definido como um *validação necessária* de estado, a sessão de BGP não estiver ativada, à medida que os prefixos anunciados não corresponde ao número em qualquer um dos registos do encaminhamento. 

> [!IMPORTANT]
> Se o estado de prefixos públicos anunciados está *validação manual* de estado, tem de abrir um pedido de suporte [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornecer evidências de que é proprietário, os endereços IP anunciados juntamente com o número de sistema autónomo associado.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Configurar a ligação do ExpressRoute.
  
  * [Crie um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)

