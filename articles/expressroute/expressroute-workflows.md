---
title: 'Azure ExpressRoute: fluxos de trabalho de configuração de circuito'
description: Esta página mostra os fluxos de trabalho para configurar o circuito do ExpressRoute e de peerings
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864371"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Fluxos de trabalho do ExpressRoute para aprovisionamento de circuitos e estados dos circuitos
Esta página explica o serviço de aprovisionamento e encaminhamento de fluxos de trabalho de configuração num alto nível.

![fluxo de trabalho do circuito](./media/expressroute-workflows/expressroute-circuit-workflow.png)

A figura a seguir e as etapas correspondentes descrevem as tarefas para provisionar um circuito de ExpressRoute de ponta a ponta. 

1. Utilize o PowerShell para configurar um circuito do ExpressRoute. Siga as instruções no [circuitos do ExpressRoute criar](expressroute-howto-circuit-classic.md) artigo para obter mais detalhes.
2. Conectividade de ordem de fornecedor de serviços. Este processo varia. Contacte o seu fornecedor de conectividade para obter mais detalhes sobre como encomendar conectividade.
3. Certifique-se de que se o circuito foi aprovisionado com êxito, verificando o circuito do ExpressRoute através do PowerShell, o estado de aprovisionamento. 
4. Configure domínios de encaminhamento. Se seu provedor de conectividade gerencia a configuração de camada 3, ele configurará o roteamento para o circuito. Se seu provedor de conectividade oferece apenas serviços de camada 2, você deve configurar o roteamento de acordo com as diretrizes descritas nas páginas de [configuração de roteamento](expressroute-howto-routing-classic.md) e [requisitos de roteamento](expressroute-routing.md) .
   
   * Ativar o peering privado do Azure - ativar este peering ligar a VMs / implementados dentro de redes virtuais de serviços cloud.

   * Habilitar o emparelhamento da Microsoft – habilite isso para acessar o Microsoft serviços online, como o Office 365. Todos os serviços de PaaS do Azure são acessíveis através do peering da Microsoft.
     
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

O estado de aprovisionamento de fornecedor de conectividade representa o estado no lado do fornecedor de conectividade. Ele pode ser *NotProvisioned*, *aprovisionamento*, ou *aprovisionado*. O circuito do ExpressRoute deve estar em um estado provisionado na ordem de configuração de emparelhamento.

### <a name="possible-states-of-an-expressroute-circuit"></a>Estados possíveis de um circuito do ExpressRoute
Esta seção lista os possíveis estados de um circuito do ExpressRoute.

**No momento de criação**

O circuito do ExpressRoute relatará os seguintes Estados na criação de recursos.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Quando o fornecedor de conectividade está no processo de aprovisionamento do circuito**

O circuito do ExpressRoute relatará os seguintes Estados enquanto o provedor de conectividade estiver trabalhando para provisionar o circuito.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Quando o fornecedor de conectividade concluiu o processo de aprovisionamento**

O circuito do ExpressRoute relatará os seguintes Estados assim que o provedor de conectividade tiver provisionado com êxito o circuito.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Quando o fornecedor de conectividade é desaprovisionar o circuito**

Se o circuito do ExpressRoute precisar ser desprovisionado, o circuito relatará os Estados a seguir quando o provedor de serviços tiver concluído o processo de desprovisionamento.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Pode optar por ativá-lo novamente se necessário, ou executar cmdlets do PowerShell para eliminar o circuito.  

> [!IMPORTANT]
> Um circuito não pode ser excluído quando o ServiceProviderProvisioningState é provisionado ou provisionado. O provedor de conectividade precisa desprovisionar o circuito antes que ele possa ser excluído. A Microsoft continuará a cobrar o circuito até que o recurso de circuito do ExpressRoute seja excluído no Azure.
> 

## <a name="routing-session-configuration-state"></a>Estado de configuração do encaminhamento sessão
O estado de provisionamento do BGP relata se a sessão BGP foi habilitada no Microsoft Edge. O estado deve ser habilitado para usar o emparelhamento privado ou da Microsoft.

É importante verificar o estado da sessão BGP especialmente para peering da Microsoft. Além do Estado de aprovisionamento de BGP, há outro Estado chamado *anunciados Estado prefixos públicos*. O estado de prefixos públicos anunciados deve estar no estado *configurado* , tanto para a sessão BGP quanto para que o roteamento funcione de ponta a ponta. 

Se o estado de prefixo públicos anunciados é definido como um *validação necessária* de estado, a sessão de BGP não estiver ativada, à medida que os prefixos anunciados não corresponde ao número em qualquer um dos registos do encaminhamento. 

> [!IMPORTANT]
> Se o estado de prefixos públicos anunciados estiver no estado de *validação manual* , você precisará abrir um tíquete de suporte com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornecer evidências de que você possui os endereços IP anunciados junto com o número do sistema autônomo associado.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Configurar a ligação do ExpressRoute.
  
  * [Crie um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)

