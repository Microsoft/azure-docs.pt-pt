---
title: Configure VPN no seu dispositivo Azure Stack Edge Mini R utilizando a Azure PowerShell
description: Descreve como configurar a VPN no seu dispositivo Azure Stack Edge Mini R utilizando um script Azure PowerShell para criar recursos Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 9fa4c678a04342b47601f81ede7c49ab841f42ba
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630967"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>Configure VPN no seu dispositivo Azure Stack Edge Mini R via Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

A opção VPN fornece uma segunda camada de encriptação para os dados em movimento sobre *TLS* do seu dispositivo Azure Stack Edge Mini R ou Azure Stack Edge Pro R para Azure. Pode configurar a VPN no seu dispositivo Azure Stack Edge Mini R através do portal Azure ou através do Azure PowerShell. 

Este artigo descreve os passos necessários para configurar uma VPN Point-to-Site (P2S) no seu dispositivo Azure Stack Edge Mini R utilizando um script Azure PowerShell para criar a configuração na nuvem. A configuração no dispositivo Azure Stack Edge é feita através da UI local.

## <a name="about-vpn-setup"></a>Sobre a configuração VPN

Uma ligação de gateway P2S VPN permite criar uma ligação segura à sua rede virtual a partir de um computador cliente individual ou do seu dispositivo Azure Stack Edge Mini R. Inicie a ligação P2S a partir do computador cliente ou do dispositivo. A ligação P2S neste caso utiliza iKEv2 VPN, uma solução VPN IPsec baseada em padrões.

O fluxo de trabalho típico inclui os seguintes passos:

1. Configurar pré-requisitos.
2. Crie os recursos necessários para o Azure.
    1. Criar e configurar uma rede virtual e sub-redes necessárias. 
    2. Criar e configurar um gateway Azure VPN (gateway de rede virtual).
    3. Crie o Azure Firewall e adicione regras de rede e aplicativos.
    4. Crie tabelas de encaminhamento Azure e adicione rotas.
    5. Ativar ponto a local no gateway VPN.
        1. Adicione a piscina de endereços do cliente.
        2. Tipo de túnel de configuração.
        3. Tipo de autenticação configurada.
        4. Criar certificado.
        5. O certificado de upload.
    6. Baixe a lista telefónica.
3. Configurar a VPN na UI web local do dispositivo. 
    1. Forneça a lista telefónica.
    2. Fornecer ficheiro de tags de serviço (json).


Os passos detalhados são fornecidos nas seguintes secções.

## <a name="configure-prerequisites"></a>Configurar pré-requisitos

- Deverá ter acesso a um dispositivo Azure Stack Edge Mini R instalado de acordo com as instruções na Instalação do [seu dispositivo Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md). Este dispositivo estabelecerá uma ligação P2S com o Azure. 

- Deverá ter acesso a uma Subscrição Azure válida que esteja ativada para o serviço Azure Stack Edge em Azure. Utilize esta subscrição para criar um recurso correspondente no Azure para gerir o seu dispositivo Azure Stack Edge Mini R.  

- Tem acesso a um cliente Windows que utilizará para aceder ao seu dispositivo Azure Stack Edge Mini R. Utilizará este cliente para criar programáticamente a configuração na nuvem.

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
        Forneça o nome de subscrição Azure que está a usar com o seu dispositivo Azure Stack Edge Mini R para configurar VPN.

    3. [Descarregue o script](https://aka.ms/ase-vpn-deployment) necessário para criar configuração na nuvem. O script irá:
        
        - Criar uma rede Virtual Azure e as seguintes sub-redes: *GatewaySubnet* e *AzureFirewallSubnet*.
        - Criar e configurar uma porta VPN Azure.
        - Criar e configurar uma porta de rede local Azure.
        - Crie e configuure uma ligação Azure VPN entre o gateway Azure VPN e o gateway de rede local.
        - Crie um Azure Firewall e adicione regras de rede, regras de aplicação.
        - Crie uma tabela de encaminhamento Azure e adicione-lhe rotas.

    4. Crie o grupo de recursos no portal Azure sob o qual pretende que os recursos Azure sejam criados. Aceda à lista de serviços no portal Azure, selecione **Grupo de Recursos** e, em seguida, selecione + **Adicionar**. Forneça as informações de subscrição e o nome do seu grupo de recursos e, em seguida, selecione **Criar**. Se for a este grupo de recursos, não deverá ter quaisquer recursos neste momento.

        ![Grupo de recursos do Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. Terá de ter um certificado codificado Base 64 em `.cer` formato para o seu dispositivo Azure Stack Edge Mini R. Este certificado deve ser enviado para o seu dispositivo Azure Stack Edge como `pfx` com uma chave privada. Este certificado também precisa de ser instalado na raiz fidedigna da loja no cliente que está a tentar estabelecer a ligação P2S.

## <a name="use-the-script"></a>Use o script

Primeiro modifica o `parameters-p2s.json` ficheiro para inserir os seus parâmetros. Em seguida, execute o script usando o ficheiro json modificado.

Cada um destes passos é discutido nas seguintes secções.

### <a name="download-service-tags-file"></a>Descarregar ficheiro de tags de serviço

Pode já ter um `ServiceTags.json` ficheiro na pasta onde descarregou o script. Caso contrário, pode descarregar o ficheiro de tags de serviço.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Alterar ficheiro de parâmetros

O primeiro passo seria modificar o `parameters-p2s.json` ficheiro e guardar as alterações. 

Para os recursos Azure que cria, irá fornecer os seguintes nomes:

|Nome do parâmetro  |Descrição  |
|---------|---------|
|virtualNetworks_vnet_name    | Nome da Rede Virtual Azure        |
|azureFirewalls_firewall_name     | Nome Azure Firewall        |
|routeTables_routetable_name     | Nome da tabela Azure Route        |
|publicIPAddresses_VNGW_public_ip_name     | Nome de endereço IP público para o seu gateway de rede Virtual       |
|virtualNetworkGateways_VNGW_name    | Nome do gateway Azure VPN (gateway de rede virtual)        |
|publicIPAddresses_firewall_public_ip_name     | Nome de endereço IP público para o seu Azure Firewall         |
|localização     |Esta é a região em que pretende criar a sua rede virtual. Selecione a mesma região que a associada ao seu dispositivo.         |
|RouteTables_routetable_onprem_name| Este é o nome da tabela de rotas adicional para ajudar os pacotes de rota de firewall de volta ao dispositivo Azure Stack Edge. O script cria duas rotas adicionais e associa *o padrão* e *firewallSubnet* com esta tabela de rotas.|

Forneça os seguintes endereços IP e espaços de endereço para os recursos Azure que são criados, incluindo a rede virtual e sub-redes associadas *(padrão,* *firewall,* *GatewaySubnet).*

|Nome do parâmetro  |Descrição  |
|---------|---------|
|VnetIPvv4AddressSpace    | Este é o espaço de endereço associado à sua rede virtual. Fornecer a gama IP Vnet como gama IP privada https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) (.     |
|DefaultssubnetiPv4AddressSpace    |Este é o espaço de endereço associado à `Default` sub-rede da sua rede virtual.         |
|FirewallSubnetiPv4AddressSpace    |Este é o espaço de endereço associado à `Firewall` sub-rede da sua rede virtual.          |
|GatewaySubnetiPv4AddressSpace    |Este é o espaço de endereço associado à `GatewaySubnet` sua rede virtual.          |
|GatewaySubnetipv4bgpPeeringAddress    | Este é o endereço IP que está reservado para comunicação BGP e é baseado no espaço de endereço associado à `GatewaySubnet` sua rede virtual.          |
|ClientAddressPool    | Este endereço IP é utilizado para o conjunto de endereços na configuração P2S no portal Azure.         |
|PublicCertData     | Os dados de certificados públicos são utilizados pela VPN Gateway para autenticar clientes P2S que o ligam. Para obter os dados do certificado, instale o certificado raiz. Certifique-se de que o certificado está codificado com uma extensão .cer. Abra este certificado e copie o texto no certificado entre ==BEGIN CERTIFICATE== e ==END CERTIFICATE== numa linha contínua.     |



### <a name="run-the-script"></a>Executar o script

Siga estes passos para utilizar o modificado `parameters-p2s.json` e executar o script para criar recursos Azure.

1. Executar PowerShell. Mude para o diretório onde o script está localizado.

3. Execute o script.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > Nesta versão, o script funciona apenas na localização dos EUA.

    Terá de inserir as seguintes informações quando executar o script:

    
    |Parâmetro  |Descrição  |
    |---------|---------|
    |Localização     |Esta é a região em que os recursos do Azure devem ser criados.         |
    |AzureAppRuleFilePath     | Este é o caminho do arquivo para `appRule.json` .       |
    |AzureIPRangesFilePath     |Este é o ficheiro Json do Service Tag que descarregou no passo anterior.         |
    |ResourceGroupName     | Este é o nome do grupo de recursos sob o qual todos os recursos Azure são criados.        |
    |AzureDeploymentName    |Este é o nome para a sua implantação do Azure.         |
    |Nome de redeRuleCollectionName            | Este é o nome para a recolha de todas as regras de rede que são criadas e adicionar ao seu Azure Firewall.             |
    |Prioridade            | Esta é a prioridade atribuída a todas as regras de rede e aplicação que são criadas.              |
    |Nome appruleCollection            |Este é o nome para a recolha de todas as regras de aplicação que são criadas e adicionadas ao seu Azure Firewall.                |


    É apresentada abaixo uma saída de exemplo.
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - O guião leva aproximadamente 90 minutos para ser executado. Certifique-se de iniciar sôs na sua rede antes do início do script.
    > - Se por qualquer motivo houver uma sessão falhada com o script, certifique-se de eliminar o grupo de recursos para eliminar todos os recursos criados sob o mesmo.
  
    
    Após a conclusão do script, um registo de implementação é gerado na mesma pasta onde reside o script.


## <a name="verify-the-azure-resources"></a>Verifique os recursos do Azure

Depois de executar o script com sucesso, verifique se todos os recursos foram criados em Azure. Vá ao grupo de recursos que criou. Deve ver os seguintes recursos:

![Recursos do Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>Baixar lista telefónica para perfil VPN

Neste passo, irá descarregar o perfil VPN para o seu dispositivo.

1. No portal Azure, vá ao grupo de recursos e, em seguida, selecione o gateway de rede virtual que criou no passo anterior.

    ![Gateway de rede virtual Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Aceda às **definições > configuração ponto-a-local**. Selecione **Baixar cliente VPN**.

    ![Ativar a configuração P2S 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. Guarde o perfil com fecho e extraia no seu cliente Windows.

    ![Ativar a configuração P2S 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. Vá à pasta *WindowsAmd64* e, em seguida, extraia o `.exe` : *VpnClientSetupAmd64.exe*.

    ![Ativar a configuração P2S 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. Criar um caminho temporário. Por exemplo:

    `C:\NewTemp\vnet\tmp`

4. Executar PowerShell e ir para o diretório onde o `.exe` está localizado. Para executar o `.exe` , tipo:

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. O caminho temporário terá novos ficheiros. Eis uma saída de exemplo:

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. O ficheiro *.pbk* é a lista telefónica para o perfil VPN. Vais usar isto na UI local.


## <a name="vpn-configuration-on-the-device"></a>Configuração VPN no dispositivo

Siga estes passos na UI local do seu dispositivo Azure Stack Edge.

1. Na UI local, vá à página **VPN.** Sob o estado VPN, selecione **Configure**.

    ![VPN configurar 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. Na lâmina **VPN configurada:**
    
    1. No ficheiro da lista telefónica Upload, aponte para o ficheiro .pbk que criou no passo anterior.
    2. No ficheiro configurar a lista IP do Upload, forneça o ficheiro JSON do alcance IP do Centro de Dados de Azure como entrada. Descarregou este ficheiro num passo anterior de: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
    3. **Selecione eastus** como região e selecione **Aplicar**.

    ![VPN configurar 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. Nos **intervalos de endereços IP a aceder apenas à secção VPN,** insira a gama Vnet IPv4 que escolheu para a sua Rede Virtual Azure.

    ![VPN configurar 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>Verifique a ligação do cliente

1. No portal Azure, vá ao portal VPN.
2. Aceda às **definições > configuração ponto-a-local**. Nos **endereços IP atribuídos,** o endereço IP do seu dispositivo Azure Stack Edge deve aparecer.

## <a name="validate-data-transfer-through-vpn"></a>Validar transferência de dados através da VPN

Para confirmar que a VPN está a funcionar, copie os dados para uma participação da SMB. Siga os passos no [Adicionar uma parte](azure-stack-edge-gpu-manage-shares.md#add-a-share) no seu dispositivo Azure Stack Edge. 

1. Copie um ficheiro, por exemplo, \data\pictures\waterfall.jpg para a parte SMB que montou no seu sistema de clientes. 
2. Para validar que os dados estão a passar pela VPN, enquanto os dados estão a ser copiados:

    1. Vá ao portal VPN no portal Azure. 

    2. Vá à **Monitorização > Métricas.**

    3. No painel direito, escolha o **Scope** como o seu gateway VPN, **métrica** como largura de banda Gateway P2S e **Agregação** como Avg.

    4. À medida que os dados estão a ser copiados, verá um aumento na utilização da largura de banda e quando a cópia de dados estiver completa, a utilização da largura de banda diminuirá.

        ![Métricas de Azure VPN](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. Verifique se este ficheiro aparece na sua conta de armazenamento na nuvem.
 
## <a name="debug-issues"></a>Problemas de depurar

Para depurar quaisquer problemas, utilize os seguintes comandos:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

A parte da amostra é mostrada abaixo:


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
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

[VPN configurar através da UI local no seu dispositivo Azure Stack Edge](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn).