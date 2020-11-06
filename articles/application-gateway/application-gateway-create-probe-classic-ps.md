---
title: Crie uma sonda personalizada utilizando o modelo de implementação Clássico - Azure Application Gateway
description: Saiba como criar uma sonda personalizada para o Application Gateway utilizando o PowerShell no modelo clássico de implementação
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 13441899eeb5ca2b7c60977ab2858fe40a398d1a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397863"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Crie uma sonda personalizada para Azure Application Gateway (clássico) utilizando o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Neste artigo, adicione uma sonda personalizada a uma porta de entrada de aplicação existente com o PowerShell. As sondas personalizadas são úteis para aplicações que têm uma página específica de verificação de saúde ou para aplicações que não fornecem uma resposta bem sucedida na aplicação web padrão.

> [!IMPORTANT]
> A Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Classic.](../azure-resource-manager/management/deployment-models.md) Este artigo abrange utilizando o modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.  Saiba como [executar estes passos com o modelo do Resource Manager](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para criar um gateway de aplicação:

1. Crie um recurso do gateway de aplicação.
2. Crie um ficheiro XML de configuração ou um objeto de configuração.
3. Confirme a configuração para o recurso de gateway de aplicação criado recentemente.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Crie um recurso de gateway de aplicação com uma sonda personalizada

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
> O valor predefinido para *InstanceCount* é 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é Médio. Pode escolher entre Pequeno, Médio e Grande.
> 
> 

*VirtualIPs* e *DnsName* são apresentados em branco, porque o gateway ainda não foi iniciado. Estes valores são criados uma vez que o portal está no estado de execução.

### <a name="configure-an-application-gateway-by-using-xml"></a>Configure um gateway de aplicações utilizando xML

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

O exemplo a seguir mostra como utilizar um ficheiro de configuração para configurar o gateway de aplicações para carregar o tráfego HTTP na porta pública 80 e enviar tráfego de rede para porta de back-end 80 entre dois endereços IP utilizando uma sonda personalizada.

> [!IMPORTANT]
> O item do protocolo Http ou Https é sensível às maiúsculas e minúsculas.

Um novo item de configuração \<Probe\> é adicionado para configurar sondas personalizadas.

Os parâmetros de configuração são:

|Parâmetro|Descrição|
|---|---|
|**Nome** |Nome de referência para sonda personalizada. |
| **Protocolo** | Protocolo utilizado (os valores possíveis são HTTP ou HTTPS).|
| **Anfitrião** e **Caminho** | Percurso URL completo que é invocado pela porta de aplicação para determinar a saúde do caso. Por exemplo, se tiver um website http: \/ /contoso.com/, então a sonda personalizada pode ser configurada para "http: \/ /contoso.com/path/custompath.htm" para que as verificações da sonda tenham uma resposta HTTP bem sucedida.|
| **Intervalo** | Configura o intervalo da sonda verifica em segundos.|
| **Intervalo de tempo** | Define o tempo de tempo da sonda para uma verificação de resposta HTTP.|
| **InsalubreThreshold** | O número de respostas HTTP falhadas necessárias para sinalizar o caso de back-end como *pouco saudável*.|

O nome da sonda é referenciado na \<BackendHttpSettings\> configuração para atribuir qual o pool back-end que utiliza configurações de sonda personalizadas.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Adicione uma sonda personalizada a um gateway de aplicações existente

Alterar a configuração atual de um gateway de aplicações requer três passos: Obtenha o ficheiro de configuração XML atual, modifique para ter uma sonda personalizada e configufique o gateway de aplicação com as novas definições de XML.

1. Obtenha o ficheiro XML utilizando `Get-AzureApplicationGatewayConfig` . Este cmdlet exporta a configuração XML a ser modificada para adicionar uma definição de sonda.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Abra o ficheiro XML num editor de texto. Adicione uma `<probe>` secção depois `<frontendport>` .

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

   Na secção backendHttpSettings do XML, adicione o nome da sonda como mostrado no seguinte exemplo:

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

1. Atualize a configuração do gateway de aplicação com o novo ficheiro XML utilizando `Set-AzureApplicationGatewayConfig` . Este cmdlet atualiza o gateway da aplicação com a nova configuração.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Passos seguintes

Se pretender configurar a Segurança da Camada de Transporte (TLS), anteriormente conhecida como descarga Secure Sockets Layer (SSL), consulte [configurar um gateway de aplicações para descarregamento de TLS](./tutorial-ssl-powershell.md).

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interno, veja [Create an application gateway with an internal load balancer (ILB) (Criar um gateway de aplicação com um balanceador de carga interno (ILB))](./application-gateway-ilb-arm.md).