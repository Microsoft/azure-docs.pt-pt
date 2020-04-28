---
title: 'Azure ExpressRoute: Fluxos de trabalho de configuração de circuitos'
description: Esta página mostra os fluxos de trabalho para configurar circuito expressRoute e observações
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864371"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Fluxos de trabalho do ExpressRoute para o aprovisionamento e estados dos circuitos
Esta página acompanha-o através dos fluxos de trabalho de configuração de fornecimento de serviços e de encaminhamento a um nível elevado.

![fluxo de trabalho de circuito](./media/expressroute-workflows/expressroute-circuit-workflow.png)

A figura seguinte e os passos correspondentes delineiam as tarefas para fornecer um circuito ExpressRoute de ponta a ponta. 

1. Utilize o PowerShell para configurar um circuito ExpressRoute. Siga as instruções no artigo dos [circuitos Create ExpressRoute](expressroute-howto-circuit-classic.md) para obter mais detalhes.
2. Encomende a conectividade do prestador de serviços. Este processo varia. Contacte o seu fornecedor de conectividade para obter mais detalhes sobre como encomendar a conectividade.
3. Certifique-se de que o circuito foi aprovisionado com sucesso, verificando o estado de provisionamento do circuito ExpressRoute através da PowerShell. 
4. Configure os domínios de encaminhamento. Se o seu fornecedor de conectividade gerir a configuração da Camada 3, configurará o encaminhamento para o seu circuito. Se o seu fornecedor de conectividade apenas oferecer serviços de Camada 2, deve configurar o encaminhamento de acordo com as diretrizes descritas nos requisitos de [encaminhamento](expressroute-routing.md) e nas páginas de configuração de [encaminhamento.](expressroute-howto-routing-classic.md)
   
   * Enable Azure private peering - Enable this peering to connect to VMs /cloud services deployed in virtual networks.

   * Ativar o peering da Microsoft - Ative isto para aceder a serviços online da Microsoft, como o Office 365. Todos os serviços Azure PaaS são acessíveis através do peering da Microsoft.
     
     > [!IMPORTANT]
     > Deve certificar-se de que utiliza um proxy/edge separado para se ligar à Microsoft do que aquele que utiliza para a Internet. Usar a mesma borda tanto para o ExpressRoute como para a Internet causará encaminhamento assimétrico e causará interrupções de conectividade para a sua rede.
     > 
     > 
     
     ![fluxos de trabalho de encaminhamento](./media/expressroute-workflows/routing-workflow.png)
5. Ligando redes virtuais aos circuitos ExpressRoute - Pode ligar redes virtuais ao circuito ExpressRoute. Siga as instruções [para ligar os VNets](expressroute-howto-linkvnet-arm.md) ao seu circuito. Estes VNets podem estar na mesma subscrição do Azure que o circuito ExpressRoute, ou podem estar numa subscrição diferente.

## <a name="expressroute-circuit-provisioning-states"></a>Estados de provisionamento de circuitos ExpressRoute
Cada circuito ExpressRoute tem dois estados:

* Estado de prestação de serviços
* Estado

O estado representa o estado de provisionamento da Microsoft. Esta propriedade está definida para Ativar quando criar um circuito expressroute

O estado de fornecimento de fornecedores de conectividade representa o Estado do lado do fornecedor de conectividade. Pode ser *Não Provisioned,* *Provisioning*ou *Provisioned*. O circuito ExpressRoute deve estar em estado provisionado para configurar o peering.

### <a name="possible-states-of-an-expressroute-circuit"></a>Possíveis estados de um circuito ExpressRoute
Esta secção lista os possíveis estados de um circuito ExpressRoute.

**No momento da criação**

O circuito ExpressRoute reportará os seguintes estados na criação de recursos.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Quando o fornecedor de conectividade está em processo de provisionamento do circuito**

O circuito ExpressRoute reportará os seguintes estados enquanto o fornecedor de conectividade está a trabalhar para fornecer o circuito.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Quando o fornecedor de conectividade tiver concluído o processo de provisionamento**

O circuito ExpressRoute reportará os seguintes estados assim que o fornecedor de conectividade tiver aprovisionado com sucesso o circuito.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Quando o fornecedor de conectividade está a desprovisionar o circuito**

Se o circuito ExpressRoute tiver de ser desprovisionado, o circuito reportará os seguintes estados assim que o prestador de serviços tiver concluído o processo de desprovisionamento.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Pode optar por reativar, se necessário, ou executar cmdlets PowerShell para eliminar o circuito.  

> [!IMPORTANT]
> Um circuito não pode ser eliminado quando o ServiceProviderProvisioningState é provisionamento ou provisionamento. O fornecedor de conectividade precisa de desprovisionar o circuito antes de poder ser eliminado. A Microsoft continuará a faturar o circuito até que o recurso do circuito ExpressRoute seja eliminado em Azure.
> 

## <a name="routing-session-configuration-state"></a>Estado de configuração da sessão de encaminhamento
O Estado de fornecimento de BGP informa se a sessão de BGP tiver sido ativada na borda da Microsoft. O Estado deve ser habilitado a utilizar o peering privado ou microsoft.

É importante verificar o estado da sessão de BGP especialmente para o peering da Microsoft. Além do estado de provisionamento do BGP, há outro Estado chamado estado de *prefixos públicos anunciados.* O estado de prefixos públicos anunciados deve estar no estado *configurado,* tanto para que a sessão do BGP esteja em cima como para o seu encaminhamento para trabalhar de ponta a ponta. 

Se o estado de prefixo público anunciado for definido para um estado *de validação necessário,* a sessão de BGP não está ativada, uma vez que os prefixos anunciados não correspondem ao número AS em nenhum dos registos de encaminhamento. 

> [!IMPORTANT]
> Se o estado de prefixos públicos anunciado estiver em estado de *validação manual,* precisa de abrir um bilhete de suporte com suporte da [Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornecer provas de que possui os endereços IP anunciados juntamente com o número do Sistema Autónomo associado.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Configurar a ligação do ExpressRoute.
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)

