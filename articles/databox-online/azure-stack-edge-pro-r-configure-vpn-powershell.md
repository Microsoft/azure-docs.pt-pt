---
title: Configure VPN no seu dispositivo Azure Stack Edge Pro R usando Azure PowerShell
description: Descreve como configurar a VPN no seu dispositivo Azure Stack Edge Pro R utilizando um script Azure PowerShell para criar recursos Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 2139080367cdce9a5f018afab0970a7bd0e7504c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467307"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Configure VPN no seu dispositivo Azure Stack Edge Pro R via Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

A opção VPN fornece uma segunda camada de encriptação para os dados em movimento sobre *TLS* do seu dispositivo Azure Stack Edge Pro R para Azure. Pode configurar a VPN no seu dispositivo Azure Stack Edge Pro R através do portal Azure ou via Azure PowerShell.

Este artigo descreve os passos necessários para configurar VPN no seu dispositivo Azure Stack Edge Pro R utilizando um Azure PowerShell para criar a configuração na nuvem.

## <a name="about-vpn-setup"></a>Sobre a configuração VPN

Uma ligação VPN transversal é constituída por um gateway VPN Azure, um dispositivo VPN no local e um túnel VPN IPsec S2S que liga os dois. O fluxo de trabalho típico inclui os seguintes passos:

- Configurar pré-requisitos.
- Crie os recursos necessários para o Azure.
    - Criar e configurar um gateway Azure VPN (gateway de rede virtual).
    - Crie e configura um gateway de rede local Azure que represente a sua rede no local e dispositivo VPN.
    - Crie e configuure uma ligação Azure VPN entre o gateway Azure VPN e o gateway de rede local.
    - Crie o Azure Firewall e adicione regras de rede e aplicativos.
    - Crie a tabela de encaminhamento Azure e adicione rotas.
- Configurar a VPN na UI web local do dispositivo. Configura o dispositivo VPN no local representado pela porta de entrada da rede local para estabelecer o verdadeiro túnel S2S VPN com o gateway Azure VPN.

Os passos detalhados são fornecidos nas seguintes secções.

## <a name="configure-prerequisites"></a>Configurar pré-requisitos

1. Deverá ter acesso a um dispositivo Azure Stack Edge Pro R instalado de acordo com as instruções na Instalação do [seu dispositivo Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md). Este dispositivo servirá como o dispositivo VPN no local para criar a ligação VPN com a Azure. 

2. O seu dispositivo VPN deve ter um endereço IP público estático (externo). Este endereço não devia ser NAT.

3. Deverá ter acesso a uma Subscrição Azure válida que esteja ativada para o serviço Azure Stack Edge em Azure. Utilize esta subscrição para criar um recurso correspondente no Azure para gerir o seu dispositivo Azure Stack Edge Pro R.  

4. Tem acesso a um cliente Windows que utilizará para aceder ao seu dispositivo Azure Stack Edge Pro R. Utilizará este cliente para criar programáticamente a configuração na nuvem.

    1. Para instalar a versão necessária do PowerShell no seu cliente Windows, execute os seguintes comandos:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Para ligar à sua conta Estruição e subscrição, execute os seguintes comandos:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Forneça o nome de subscrição Azure que está a usar com o seu dispositivo Azure Stack Edge Pro R para configurar VPN.

    3. [Descarregue o script](https://aka.ms/ase-vpn-deployment) necessário para criar configuração na nuvem. O script irá:
        
        - Criar uma rede Virtual Azure e as seguintes sub-redes: *GatewaySubnet* e *AzureFirewallSubnet*.
        - Criar e configurar uma porta VPN Azure.
        - Criar e configurar uma porta de rede local Azure.
        - Crie e configuure uma ligação Azure VPN entre o gateway Azure VPN e o gateway de rede local.
        - Crie um Azure Firewall e adicione regras de rede, regras de aplicação.
        - Crie uma tabela de encaminhamento Azure e adicione-lhe rotas.


## <a name="use-the-script"></a>Use o script

Primeiro modifica o `parameters.json` ficheiro para inserir os seus parâmetros. Em seguida, execute o script usando o ficheiro json modificado.

Cada um destes passos é discutido nas seguintes secções.

### <a name="download-service-tags-file"></a>Descarregar ficheiro de tags de serviço

Pode já ter um `ServiceTags.json` ficheiro na pasta onde descarregou o script. Caso contrário, pode descarregar o ficheiro de tags de serviço.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Alterar ficheiro de parâmetros

O primeiro passo seria modificar o `parameters.json` ficheiro e guardar as alterações. 


Para os recursos Azure que cria, irá fornecer os seguintes nomes:

|Nome do parâmetro  |Descrição  |
|---------|---------|
|virtualNetworks_vnet_name    | Nome da Rede Virtual Azure        |
|azureFirewalls_firewall_name     | Nome Azure Firewall        |
|routeTables_routetable_name     | Nome da tabela Azure Route        |
|publicIPAddresses_VNGW_public_ip_name     | Nome de endereço IP público para o seu gateway de rede Virtual       |
|virtualNetworkGateways_VNGW_name    | Nome do gateway Azure VPN (gateway de rede virtual)        |
|publicIPAddresses_firewall_public_ip_name     | Nome de endereço IP público para o seu Azure Firewall         |
|localNetworkGateways_LNGW_name    |Nome de gateway de rede local Azure          |
|connections_vngw_lngw_name    | Nome de ligação Azure VPN. Esta é a ligação entre o seu gateway de rede virtual e o gateway de rede local.       |
|localização     |Esta é a região em que pretende criar a sua rede virtual. Selecione a mesma região que a associada ao seu dispositivo.         |

Os seguintes endereços IP e espaços de endereço relativos aos recursos Azure que são criados, incluindo a rede virtual e sub-redes associadas (padrão, firewall, GatewaySubnet).

|Nome do parâmetro  |Descrição  |
|---------|---------|
|VnetIPvv4AddressSpace    | Este é o espaço de endereço associado à sua rede virtual.       |
|DefaultssubnetiPv4AddressSpace    |Este é o espaço de endereço associado à `Default` sub-rede da sua rede virtual.         |
|FirewallSubnetiPv4AddressSpace    |Este é o espaço de endereço associado à `Firewall` sub-rede da sua rede virtual.          |
|GatewaySubnetiPv4AddressSpace    |Este é o espaço de endereço associado à `GatewaySubnet` sua rede virtual.          |
|GatewaySubnetipv4bgpPeeringAddress    | Este é o endereço IP que está reservado para comunicação BGP e é baseado no espaço de endereço associado à `GatewaySubnet` sua rede virtual.          |

Os seguintes endereços IP e espaços de endereço relativos à rede no local (onde está implantado o seu dispositivo Azure Stack Edge Pro R).

|Nome do parâmetro  |Descrição  |
|---------|---------|
|CustomerNetworkAddressSpace    |  Este é o espaço de endereço do seu endereço IP privado.       |
|CustomerPublicNetworkAddressSpace    |  Este é o espaço de endereço ip público.       |
|DbeIOTNetworkAddressSpace    |Este endereço IP é reservado pelo serviço IoT. Não altere este parâmetro.        |
|AzureVPNsharedKey    |Esta chave partilhada é utilizada durante a criação do recurso de ligação Azure VPN. Esta chave também é fornecida como o segredo compartilhado VPN durante a configuração local da UI VPN.         |
|DBE-Gateway-ipaddress   | Endereço IP público para o seu dispositivo Azure Stack Edge Pro R. Isto pode não ser conhecido e você pode executar o script com um endereço IP reservado. Edite a porta de entrada de rede local mais tarde com o endereço IP real.     |

#### <a name="caveats-to-keep-in-mind"></a>Ressalvas a ter em mente:

- Não terá o endereço IP do dispositivo Azure Stack Edge Pro R. Pode utilizar um endereço IP reservado para criar o seu recurso e, posteriormente, modificar o gateway de rede local Azure para atribuir o endereço IP do dispositivo real e o espaço de endereço da rede local para o dispositivo.
- Com base na direção da IETF na Internet Assigned Numbers Authority (IANA), utilize qualquer sub-rede de 172.16.x.y a 172.24.z.a. Reservamos os intervalos de endereços IPv4 172.24 para a rede Azure.

### <a name="run-the-script"></a>Executar o script

Siga estes passos para utilizar o modificado `parameters.json` e executar o script para criar recursos Azure.

1. Execute o PowerShell como um administrador.

2. Mude para o diretório onde o script está localizado.

3. Execute o script.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    É apresentada abaixo uma saída de exemplo.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    O guião leva aproximadamente 90 minutos para ser executado. Após a conclusão do script, um registo de implementação é gerado na mesma pasta onde reside o script.


## <a name="verify-the-azure-resources"></a>Verifique os recursos do Azure

Depois de executar o script com sucesso, verifique se todos os recursos foram criados em Azure.

Em seguida, configurará a VPN na UI web local do seu dispositivo.


## <a name="vpn-configuration-on-the-device"></a>Configuração VPN no dispositivo

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>Verificar VPN

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>Validar transferência de dados através da VPN

Para confirmar que a VPN está a funcionar, copie os dados para uma participação da SMB. Siga os passos no [Adicionar uma parte](azure-stack-edge-j-series-manage-shares.md#add-a-share) no seu dispositivo Azure Stack Edge Pro R. 

1. Copie um ficheiro, por exemplo, \data\pictures\waterfall.jpg para a parte SMB que montou no seu sistema de clientes. 
2. Verifique se este ficheiro aparece na sua conta de armazenamento na nuvem.

Para validar que os dados estão a passar pela VPN:

1. Abra o recurso De Ligação presente no grupo de recursos. 

2. Verifique o valor Data in e Data out.
 
3. Abra o Gateway de Rede Virtual no seu grupo de recursos. Veja os gráficos **da entrada total do túnel** e da saída total do **túnel.**
 
## <a name="debug-issues"></a>Problemas de depurar

Para depurar quaisquer problemas, utilize os seguintes comandos:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

A parte da amostra é mostrada abaixo:


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>Passos seguintes

[Configure VPN via UI local no seu dispositivo Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
