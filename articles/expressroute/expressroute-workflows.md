---
title: 'Azure ExpressRoute: Fluxos de trabalho de configuração de circuitos'
description: Esta página mostra os fluxos de trabalho para configurar o circuito ExpressRoute e os seus pares
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75864371"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Fluxos de trabalho do ExpressRoute para o aprovisionamento e estados dos circuitos
Esta página acompanha-o através dos fluxos de trabalho de configuração de fornecimento e encaminhamento de serviços a um nível elevado.

![fluxo de trabalho do circuito](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Os seguintes números e passos correspondentes descrevem as tarefas de provisão de um circuito ExpressRoute de ponta a ponta. 

1. Utilize o PowerShell para configurar um circuito ExpressRoute. Siga as instruções no artigo dos [circuitos Create ExpressRoute](expressroute-howto-circuit-classic.md) para mais detalhes.
2. Encomende conectividade do prestador de serviços. Este processo varia. Contacte o seu fornecedor de conectividade para obter mais detalhes sobre como encomendar conectividade.
3. Certifique-se de que o circuito foi a provisionado com sucesso, verificando o estado de provisionamento do circuito ExpressRoute através da PowerShell. 
4. Configure os domínios de encaminhamento. Se o seu fornecedor de conectividade gerir a configuração da Camada 3, eles configurarão o encaminhamento para o seu circuito. Se o seu fornecedor de conectividade apenas oferecer serviços de Camada 2, deve configurar o encaminhamento de acordo com as diretrizes descritas nos [requisitos de encaminhamento](expressroute-routing.md) e páginas [de configuração de encaminhamento.](expressroute-howto-routing-classic.md)
   
   * Ativar o azure peering privado - Permita que este espreitante se conecte a VMs/serviços de nuvem implantados dentro de redes virtuais.

   * Ativar o persto da Microsoft - Ative isto para aceder a serviços online da Microsoft, como o Office 365. Todos os serviços Azure PaaS estão acessíveis através do persto da Microsoft.
     
     > [!IMPORTANT]
     > Deve certificar-se de que utiliza um proxy/edge separado para ligar à Microsoft do que aquele que utiliza para a Internet. A utilização da mesma borda tanto para o ExpressRoute como para a Internet irá causar o encaminhamento assimétrico e causar interrupções de conectividade para a sua rede.
     > 
     > 
     
     ![fluxos de trabalho de encaminhamento](./media/expressroute-workflows/routing-workflow.png)
5. Ligação de redes virtuais aos circuitos ExpressRoute - Pode ligar redes virtuais ao seu circuito ExpressRoute. Siga as instruções [para ligar os VNets](expressroute-howto-linkvnet-arm.md) ao seu circuito. Estes VNets podem estar na mesma subscrição Azure que o circuito ExpressRoute, ou podem estar numa subscrição diferente.

## <a name="expressroute-circuit-provisioning-states"></a>Estados de provisionamento do circuito ExpressRoute
Cada circuito ExpressRoute tem dois estados:

* Estado de prestação de serviços
* Estado

O estado representa o estado de provisionamento da Microsoft. Esta propriedade está definida para Ativado quando cria um circuito Expressroute

O estado de fornecimento do fornecedor de conectividade representa o estado do lado do fornecedor de conectividade. Pode ser *NotProvisioned*, *Provisioning,* ou *Provisioned*. O circuito ExpressRoute deve estar num estado provisionado para configurar o seu olhar.

### <a name="possible-states-of-an-expressroute-circuit"></a>Possíveis estados de um circuito ExpressRoute
Esta secção lista os possíveis estados de um circuito ExpressRoute.

**Na época da criação**

O circuito ExpressRoute reportará os seguintes estados na criação de recursos.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Quando o fornecedor de conectividade está em processo de provisionamento do circuito**

O circuito ExpressRoute reportará os seguintes estados enquanto o fornecedor de conectividade está a trabalhar para providenciar o circuito.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Quando o fornecedor de conectividade tiver concluído o processo de provisionamento**

O circuito ExpressRoute reportará os seguintes estados assim que o fornecedor de conectividade tiver previsto com sucesso o circuito.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Quando o fornecedor de conectividade está a desprovisionar o circuito**

Se o circuito ExpressRoute precisar de ser desprovisionado, o circuito reportará os seguintes estados assim que o prestador de serviços tiver concluído o processo de desprovisionamento.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Pode optar por voltar a ativar, se necessário, ou executar cmdlets PowerShell para eliminar o circuito.  

> [!IMPORTANT]
> Um circuito não pode ser eliminado quando o ServiceProviderProvisioningState é Provisioning ou Provisioned. O fornecedor de conectividade precisa de desprovisionar o circuito antes de poder ser eliminado. A Microsoft continuará a faturar o circuito até que o recurso do circuito ExpressRoute seja eliminado em Azure.
> 

## <a name="routing-session-configuration-state"></a>Estado de configuração da sessão de encaminhamento
O estado de provisionamento do BGP informa se a sessão de BGP foi ativada na borda da Microsoft. O estado deve ser habilitado a usar o olhar privado ou microsoft.

É importante verificar o estado da sessão de BGP especialmente para o espreitamento da Microsoft. Além do estado de provisionamento do BGP, existe outro estado chamado *estado de prefixos públicos anunciados.* O estado de prefixos públicos anunciados deve estar no estado *configurado,* tanto para a sessão de BGP estar de pé como para o seu encaminhamento para o trabalho de ponta a ponta. 

Se o estado de prefixo público anunciado for definido como um estado *de validação necessário,* a sessão de BGP não está ativada, uma vez que os prefixos anunciados não correspondem ao número de AS em nenhum dos registos de encaminhamento. 

> [!IMPORTANT]
> Se o estado de prefixos públicos anunciados estiver em estado *de validação manual,* é necessário abrir um bilhete de apoio com suporte da [Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornecer provas de que possui os endereços IP anunciados juntamente com o número do Sistema Autónomo associado.
> 
> 

## <a name="next-steps"></a>Próximos passos
* Configurar a ligação do ExpressRoute.
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)

