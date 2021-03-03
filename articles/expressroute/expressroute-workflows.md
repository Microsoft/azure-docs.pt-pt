---
title: 'Azure ExpressRoute: Fluxo de trabalho de configuração de circuitos'
description: Esta página mostra o fluxo de trabalho para configurar circuitos ExpressRoute e espreitar
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperf-fy21q1
ms.openlocfilehash: 24ad325cae2ee71ad49ee8ee055a83ceb8fa7ef2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721740"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Fluxos de trabalho do ExpressRoute para o aprovisionamento e estados dos circuitos

Este artigo acompanha-o através do fornecimento de serviços e fluxos de trabalho de configuração de encaminhamento a um nível elevado. As secções seguintes descrevem as tarefas de provisão de um circuito ExpressRoute de ponta a ponta.

## <a name="workflow-steps"></a>Passos de fluxo de trabalho

### <a name="1-prerequisites"></a>1. Pré-requisitos

Certifique-se de que os pré-requisitos são cumpridos. Para obter uma lista completa, consulte [Pré-requisitos e lista de verificação](expressroute-prerequisites.md).

* Foi criada uma subscrição Azure.
* A conectividade física foi estabelecida com o parceiro ExpressRoute ou configurada via ExpressRoute Direct. Rever a localização, ver [Localizações e parceiros](expressroute-locations-providers.md#partners) para ver o parceiro ExpressRoute e a conectividade ExpressRoute Direct em locais de observação.

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. Encomendar conectividade ou configuração ExpressRoute Direct

Encomende conectividade ao prestador de serviços ou configuure ExpressRoute Direct.

#### <a name="expressroute-partner-model"></a>Modelo parceiro ExpressRoute

Encomende conectividade do prestador de serviços. Este processo varia. Contacte o seu fornecedor de conectividade para obter mais detalhes sobre como encomendar conectividade.

* Selecione o parceiro ExpressRoute
* Selecione a localização de observação
* Selecione a largura de banda
* Selecione o modelo de faturação
* Selecione suplemento padrão ou premium

#### <a name="expressroute-direct-model"></a>Modelo ExpressRoute Direct

* Ver capacidade expressRoute Direta disponível em locais de observação.
* Reserve portas criando o recurso ExpressRoute Direct na sua subscrição Azure.
* Solicite e receba a Carta de Autorização e encomende as ligações cruzadas físicas ao fornecedor de localização.
* Ativar o estado administrativo e ver os níveis de luz e a ligação física utilizando [o Azure Monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics).

### <a name="3-create-an-expressroute-circuit"></a>3. Criar um circuito ExpressRoute

#### <a name="expressroute-partner-model"></a>Modelo parceiro ExpressRoute

Verifique se o parceiro ExpressRoute está pronto para providenciar conectividade. O seu circuito ExpressRoute é faturado a partir do momento em que uma chave de serviço é emitida. Utilize as instruções em [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) para criar o seu circuito.

#### <a name="expressroute-direct-model"></a>Modelo ExpressRoute Direct

Certifique-se de que a ligação física e o estado de administração estão ativados em ambas as ligações. Consulte como [configurar o ExpressRoute Direct](how-to-expressroute-direct-portal.md) para obter orientação. O seu circuito ExpressRoute é faturado a partir do momento em que uma chave de serviço é emitida. Utilize as instruções em [Criar um circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) para criar o seu circuito.

### <a name="4-service-provider-provisions-connectivity"></a>4. A conectividade das disposições do prestador de serviços

Esta secção diz apenas respeito ao modelo de conectividade do parceiro ExpressRoute:

* Fornecer a chave de serviço (s-key) ao fornecedor de conectividade.
* Fornecer informações adicionais necessárias pelo fornecedor de conectividade (por exemplo, ID VPN).
* Se o fornecedor gerir a configuração do encaminhamento, forneça os detalhes necessários.

Pode certificar-se de que o circuito foi a provisionado com sucesso, verificando o estado de provisionamento do circuito ExpressRoute utilizando o PowerShell, o portal Azure ou cli.

### <a name="5-configure-routing-domains"></a>5. Configure domínios de encaminhamento

Configure os domínios de encaminhamento. Se o seu fornecedor de conectividade gerir a configuração da Camada 3, eles configurarão o encaminhamento para o seu circuito. Se o seu fornecedor de conectividade apenas oferecer serviços da Camada 2 ou se estiver a utilizar o ExpressRoute Direct, tem de configurar o encaminhamento de acordo com as diretrizes descritas nos [requisitos de encaminhamento](expressroute-routing.md) e artigos [de configuração de encaminhamento.](expressroute-howto-routing-classic.md)

#### <a name="for-azure-private-peering"></a>Para azure espreitar privadamente

Permitir que o espreitamento privado se conecte a VMs e serviços de nuvem implantados dentro da rede virtual Azure.

* Sub-redes IPv4:
    * Sub-rede de espreitar para o caminho 1 (/30)
    * Sub-rede de espreitar para o caminho 2 (/30)
* Sub-redes IPv6 (opcional):
    * Sub-rede de perspeção para o caminho 1 (/126)
    * Sub-rede de perspeção para o caminho 2 (/126)
* VLAN ID para espreitar
* ASN para espreitar
* ExpressRoute ASN = 12076
* Haxixe MD5 (Opcional)

#### <a name="for-microsoft-peering"></a>Para olhando a Microsoft

Ative isto para aceder a serviços online da Microsoft, como o Microsoft 365. Além disso, todos os serviços Azure PaaS estão acessíveis através do persto da Microsoft. Deve certificar-se de que utiliza um proxy/edge separado para ligar à Microsoft do que aquele que utiliza para a Internet. A utilização da mesma borda tanto para o ExpressRoute como para a Internet irá causar o encaminhamento assimétrico e causar interrupções de conectividade para a sua rede.

* Sub-redes IPv4:
    * Sub-rede de pares para o caminho 1 (/30) - deve ser IP público
    * Sub-rede de pares para o caminho 2 (/30) - deve ser IP público
* Sub-redes IPv6 (opcional):
    * Sub-rede de pares para o caminho 1 (/126) - deve ser IP público
    * Sub-rede de pares para o caminho 2 (/126) - deve ser IP público
* VLAN ID para espreitar
* ASN para espreitar
* Prefixos anunciados - devem ser prefixos IP públicos
* Cliente ASN (opcional se diferente de espreitar ASN)
* RIR/IRR para validação de IP e ASN
* ExpressRoute ASN = 12076
* Haxixe MD5 (Opcional)

### <a name="6-start-using-the-expressroute-circuit"></a>6. Comece a utilizar o circuito ExpressRoute

* Pode ligar as redes virtuais Azure ao seu circuito ExpressRoute para permitir a conectividade desde as instalações até à rede virtual Azure. Consulte o [Link a VNet para obter orientação num](expressroute-howto-linkvnet-arm.md) artigo de circuito. Estes VNets podem estar na mesma subscrição Azure que o circuito ExpressRoute, ou podem estar numa subscrição diferente.
* Conecte-se aos serviços Azure e microsoft cloud através do espreitamento da Microsoft.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>Estados de provisionamento de circuitos parceiros ExpressRoute

A secção seguinte descreve os diferentes estados do circuito ExpressRoute para o modelo de conectividade parceiro ExpressRoute.
Cada circuito parceiro ExpressRoute tem dois estados:

* **ServiceProviderProvisioningState** representa o estado do lado do fornecedor de conectividade. Pode ser *NotProvisioned*, *Provisioning,* ou *Provisioned*. O circuito ExpressRoute deve estar num estado provisionado para configurar o seu olhar. **Este estado diz respeito apenas aos circuitos parceiros ExpressRoute e não é exibido nas propriedades de um circuito ExpressRoute Direct**.

* **O estado** representa o estado de provisionamento da Microsoft. Esta propriedade está definida para Ativado quando cria um circuito ExpressRoute

### <a name="possible-states-of-an-expressroute-circuit"></a>Possíveis estados de um circuito ExpressRoute

Esta secção descreve os possíveis estados de um circuito ExpressRoute criado sob o modelo de conectividade parceiro ExpressRoute.

**Na época da criação**

O circuito ExpressRoute reportará os seguintes estados na criação de recursos.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**Quando o fornecedor de conectividade está em processo de provisionamento do circuito**

O circuito ExpressRoute reportará os seguintes estados enquanto o fornecedor de conectividade está a trabalhar para providenciar o circuito.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**Quando o fornecedor de conectividade tiver concluído o processo de provisionamento**

O circuito ExpressRoute reportará os seguintes estados assim que o fornecedor de conectividade tiver previsto com sucesso o circuito.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**Quando o fornecedor de conectividade está a desprovisionar o circuito**

Se o circuito ExpressRoute precisar de ser desprovisionado, o circuito reportará os seguintes estados assim que o prestador de serviços tiver concluído o processo de desprovisionamento.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

Pode optar por voltar a ativar, se necessário, ou executar cmdlets PowerShell para eliminar o circuito.  

> [!IMPORTANT]
> Um circuito não pode ser eliminado quando o ServiceProviderProvisioningState é Provisioning ou Provisioned. O fornecedor de conectividade precisa de desprovisionar o circuito antes de poder ser eliminado. A Microsoft continuará a faturar o circuito até que o recurso do circuito ExpressRoute seja eliminado em Azure.
> 

## <a name="routing-session-configuration-state"></a>Estado de configuração da sessão de encaminhamento

O estado de provisionamento do BGP informa se a sessão de BGP foi ativada no Microsoft Edge. O estado deve ser habilitado a usar o olhar privado ou microsoft.

É importante verificar o estado da sessão de BGP especialmente para o espreitamento da Microsoft. Além do estado de provisionamento do BGP, existe outro estado chamado *estado de prefixos públicos anunciados.* O estado de prefixos públicos anunciados deve estar no estado *configurado,* tanto para a sessão de BGP estar de pé como para o seu encaminhamento para o trabalho de ponta a ponta. 

Se o estado de prefixo público anunciado for definido como um estado *de validação necessário,* a sessão de BGP não está ativada, uma vez que os prefixos anunciados não correspondem ao número de AS em nenhum dos registos de encaminhamento.

> [!IMPORTANT]
> Se o estado de prefixos públicos anunciados estiver em estado *de validação manual,* é necessário abrir um bilhete de apoio com suporte da [Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornecer provas de que possui os endereços IP anunciados juntamente com o número do Sistema Autónomo associado.
> 
> 

## <a name="next-steps"></a>Passos seguintes

* Configurar a ligação do ExpressRoute.
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configure o encaminhamento](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
