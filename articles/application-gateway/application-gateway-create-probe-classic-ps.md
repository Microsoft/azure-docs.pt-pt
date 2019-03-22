---
title: Criar PowerShell clássico uma sonda personalizada - Gateway de aplicação do Azure - | Documentos da Microsoft
description: Saiba como criar uma sonda personalizada para o Gateway de aplicação com o PowerShell no modelo de implementação clássica
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 338a7be1-835c-48e9-a072-95662dc30f5e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 17893a37bbaf67014c9b34dd446af204b907ff24
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004981"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Criar uma sonda personalizada para o Gateway de aplicação do Azure (clássico) com o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Neste artigo, é possível adicionar uma sonda personalizada para um gateway de aplicação existente com o PowerShell. As pesquisas personalizadas são úteis para aplicações que têm uma página de verificação de estado de funcionamento específico ou para aplicativos que não fornecem uma resposta com êxito na aplicação web predefinida.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como [executar estes passos com o modelo do Resource Manager](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para criar um gateway de aplicação:

1. Crie um recurso do gateway de aplicação.
2. Crie um ficheiro XML de configuração ou um objeto de configuração.
3. Confirme a configuração para o recurso de gateway de aplicação criado recentemente.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Criar um recurso de gateway de aplicação com uma sonda personalizada

Para criar o gateway, utilize o cmdlet `New-AzureApplicationGateway`, substituindo os valores pelos seus próprios valores. Neste ponto, ainda não é contabilizada a faturação do gateway. A faturação passará a ser contabilizada num passo posterior, quando o gateway tiver sido iniciado com êxito.

O exemplo que se segue cria um gateway de aplicação com uma rede virtual denominada “testvnet1” e uma sub-rede denominada “subnet-1”.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Para validar a criação do gateway, pode utilizar o cmdlet `Get-AzureApplicationGateway`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> O valor predefinido para *InstanceCount* é 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é Médio. Pode escolher entre pequeno, médio e grande.
> 
> 

*VirtualIPs* e *DnsName* são apresentados em branco, porque o gateway ainda não foi iniciado. Estes valores são criados assim que o gateway estiver em execução.

### <a name="configure-an-application-gateway-by-using-xml"></a>Configurar um gateway de aplicação com XML

No exemplo que se segue, vai utilizar um ficheiro XML para configurar todas as definições do gateway de aplicação e consolidá-las no recurso do gateway de aplicação.  

Copie o seguinte texto para o Bloco de Notas.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Edite os valores entre parêntesis dos itens de configuração. Guarde o ficheiro com a extensão .xml.

O exemplo seguinte mostra como utilizar um ficheiro de configuração para configurar o gateway de aplicação para balancear o tráfego HTTP na porta pública 80 e enviar o tráfego de rede para a porta de back-end 80 entre dois endereços IP ao utilizar uma sonda personalizada.

> [!IMPORTANT]
> O item do protocolo Http ou Https é sensível às maiúsculas e minúsculas.

Um novo item de configuração \<sonda\> é adicionado ao configurar as pesquisas personalizadas.

Os parâmetros de configuração são:

|Parâmetro|Descrição|
|---|---|
|**Nome** |Nome de referência para a sonda personalizada. |
| **Protocolo** | Protocolo utilizado (os valores possíveis são HTTP ou HTTPS).|
| **Host** e **caminho** | Caminho do URL completo que é invocado por gateway de aplicação para determinar o estado de funcionamento da instância. Por exemplo, se tiver um Web site http://contoso.com/, a sonda personalizada pode ser configurada para "http://contoso.com/path/custompath.htm" para a sonda verifica para ter uma resposta HTTP com êxito.|
| **Intervalo** | Configura as verificações de intervalo de sonda em segundos.|
| **Tempo limite** | Define o limite de tempo de pesquisa para uma verificação de resposta HTTP.|
| **UnhealthyThreshold** | O número de respostas HTTP falhados necessários para sinalizar a instância de back-end como *mau estado de funcionamento*.|

O nome da sonda é referenciado no \<BackendHttpSettings\> configuração para atribuir o conjunto de back-end utiliza as definições de sonda personalizada.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Adicionar uma sonda personalizada para um gateway de aplicação existente

Alteração da configuração atual de um gateway de aplicação requer três passos: Obter o ficheiro de configuração atual do XML e modificar para ter uma sonda personalizada para configurar o gateway de aplicação com as novas definições de XML.

1. Obter o arquivo XML usando `Get-AzureApplicationGatewayConfig`. Este cmdlet exporta o XML modificado para adicionar uma definição de sonda de configuração.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Abra o ficheiro XML num editor de texto. Adicionar uma `<probe>` secção após `<frontendport>`.

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   Na secção backendHttpSettings do XML, adicione o nome da sonda, conforme mostrado no exemplo a seguir:

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   Guarde o ficheiro XML.

1. Atualizar a configuração do gateway de aplicação com o novo arquivo XML com `Set-AzureApplicationGatewayConfig`. Este cmdlet atualiza o gateway de aplicação com a nova configuração.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Passos Seguintes

Se quiser configurar a descarga de Secure Sockets Layer (SSL), veja [configurar um gateway de aplicação para descarga de SSL](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interno, veja [Create an application gateway with an internal load balancer (ILB) (Criar um gateway de aplicação com um balanceador de carga interno (ILB))](application-gateway-ilb.md).

