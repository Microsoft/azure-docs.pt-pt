---
title: Criar uma investigação personalizada usando o PowerShell-Aplicativo Azure gateway
description: Saiba como criar uma investigação personalizada para o gateway de aplicativo usando o PowerShell no modelo de implantação clássico
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: e01a1cad98ded9d7ce8683b6adf38b5d53959774
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966807"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Criar uma investigação personalizada para o gateway de Aplicativo Azure (clássico) usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Neste artigo, você adiciona uma investigação personalizada a um gateway de aplicativo existente com o PowerShell. Investigações personalizadas são úteis para aplicativos que têm uma página de verificação de integridade específica ou para aplicativos que não fornecem uma resposta bem-sucedida no aplicativo Web padrão.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../azure-resource-manager/management/deployment-models.md). Este artigo aborda o uso do modelo de implantação clássico. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como [executar estes passos com o modelo do Resource Manager](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para criar um gateway de aplicação:

1. Crie um recurso do gateway de aplicação.
2. Crie um ficheiro XML de configuração ou um objeto de configuração.
3. Confirme a configuração para o recurso de gateway de aplicação criado recentemente.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Criar um recurso de gateway de aplicativo com uma investigação personalizada

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
> O valor predefinido para *InstanceCount* é 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é Médio. Você pode escolher entre pequeno, médio e grande.
> 
> 

*VirtualIPs* e *DnsName* são apresentados em branco, porque o gateway ainda não foi iniciado. Esses valores são criados quando o gateway está em estado de execução.

### <a name="configure-an-application-gateway-by-using-xml"></a>Configurar um gateway de aplicativo usando XML

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

O exemplo a seguir mostra como usar um arquivo de configuração para configurar o gateway de aplicativo para balancear a carga do tráfego HTTP na porta pública 80 e enviar o tráfego de rede para a porta de back-end 80 entre dois endereços IP usando uma investigação personalizada.

> [!IMPORTANT]
> O item do protocolo Http ou Https é sensível às maiúsculas e minúsculas.

Um novo item de configuração \<\> de investigação é adicionado para configurar investigações personalizadas.

Os parâmetros de configuração são:

|Parâmetro|Descrição|
|---|---|
|**Nome** |Nome de referência para investigação personalizada. |
| **Protocolo** | Protocolo usado (os valores possíveis são HTTP ou HTTPS).|
| **Host** e **caminho** | Caminho de URL completo que é invocado pelo gateway de aplicativo para determinar a integridade da instância. Por exemplo, se você tiver um site http:\//contoso.com/, a investigação personalizada poderá ser configurada para "http:\//contoso.com/path/custompath.htm" para verificações de investigação ter uma resposta HTTP bem-sucedida.|
| **Intervalo** | Configura as verificações de intervalo de investigação em segundos.|
| **Tempo limite** | Define o tempo limite de investigação para uma verificação de resposta HTTP.|
| **UnhealthyThreshold** | O número de respostas HTTP com falha necessárias para sinalizar a instância de back-end como não *íntegra*.|

O nome da investigação é referenciado no \<BackendHttpSettings\> configuração para atribuir qual pool de back-end usa configurações de investigação personalizadas.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Adicionar uma investigação personalizada a um gateway de aplicativo existente

A alteração da configuração atual de um gateway de aplicativo requer três etapas: obter o arquivo de configuração XML atual, modificar para ter uma investigação personalizada e configurar o gateway de aplicativo com as novas configurações de XML.

1. Obtenha o arquivo XML usando `Get-AzureApplicationGatewayConfig`. Esse cmdlet exporta o XML de configuração a ser modificado para adicionar uma configuração de investigação.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Abra o arquivo XML em um editor de texto. Adicione uma seção `<probe>` após `<frontendport>`.

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

   Na seção backendHttpSettings do XML, adicione o nome da investigação, conforme mostrado no exemplo a seguir:

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

   Salve o arquivo XML.

1. Atualize a configuração do gateway de aplicativo com o novo arquivo XML usando `Set-AzureApplicationGatewayConfig`. Esse cmdlet atualiza o gateway de aplicativo com a nova configuração.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Passos seguintes

Se você quiser configurar o descarregamento de protocolo SSL (SSL), consulte [configurar um gateway de aplicativo para descarregamento SSL](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interno, veja [Create an application gateway with an internal load balancer (ILB) (Criar um gateway de aplicação com um balanceador de carga interno (ILB))](application-gateway-ilb.md).

