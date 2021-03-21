---
title: Join an Azure-SSIS integration runtime to a virtual network (Associar um runtime de integração do Azure-SSIS a uma rede virtual)
description: Aprenda a juntar-se a um tempo de integração Azure-SSIS a uma rede virtual Azure.
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/02/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 9a82b305adec1385bf659987ea39df6bb953cd70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370976"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Join an Azure-SSIS integration runtime to a virtual network (Associar um runtime de integração do Azure-SSIS a uma rede virtual)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ao utilizar os Serviços de Integração de Servidores SQL (SSIS) na Azure Data Factory, deverá aderir ao seu tempo de integração Azure-SSIS (IR) a uma rede virtual Azure nos seguintes cenários:

- Pretende ligar-se às lojas de dados no local a partir de pacotes SSIS que funcionam no seu Azure-SSIS IR sem configurar ou gerir um IR auto-hospedado como procuração. 

- Pretende hospedar a base de dados do catálogo SSIS (SSISDB) na Base de Dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou em SQL Managed Instance com ponto final privado. 

- Pretende ligar-se aos recursos Azure configurados com pontos finais de serviço de rede virtual a partir de pacotes SSIS que funcionam no seu Azure-SSIS IR.

- Pretende ligar-se a lojas de dados/recursos configurados com as regras de firewall IP a partir de pacotes SSIS que funcionam no seu Azure-SSIS IR.

A Data Factory permite-lhe juntar o seu Azure-SSIS IR a uma rede virtual criada através do modelo de implementação clássico ou do modelo de implementação do Azure Resource Manager.

> [!IMPORTANT]
> A rede virtual clássica está a ser depreciada, por isso use a rede virtual Azure Resource Manager.  Se já utilizar a rede virtual clássica, mude para a rede virtual Azure Resource Manager o mais rapidamente possível.

A [configuração de um tempo de integração de servidores Azure-SQL (SSIS) para aderir a um](tutorial-deploy-ssis-virtual-network.md) tutorial de rede virtual mostra os passos mínimos através do portal Azure. Este artigo expande-se no tutorial e descreve todas as tarefas opcionais:

- Se estiver a utilizar a rede virtual (clássica).
- Se trouxer os seus próprios endereços IP públicos para o Azure-SSIS IR.
- Se utilizar o seu próprio servidor Do Sistema de Nome de Domínio (DNS).
- Se utilizar um grupo de segurança de rede (NSG) na sub-rede.
- Se utilizar o Azure ExpressRoute ou uma rota definida pelo utilizador (UDR).
- Se utilizar Azure-SSIS IR personalizado.
- Se utilizar o provisionamento da Azure Powershell.

## <a name="access-to-on-premises-data-stores"></a>Acesso a lojas de dados no local

Se os seus pacotes SSIS acederem a lojas de dados no local, pode juntar-se ao seu Azure-SSIS IR a uma rede virtual que está ligada à rede no local. Ou pode configurar e gerir um IR auto-hospedado como procuração para o seu Azure-SSIS IR. Para obter mais informações, consulte [Configure um IR auto-hospedado como um representante para um Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). 

Ao juntar o seu Azure-SSIS IR a uma rede virtual, lembre-se destes pontos importantes: 

- Se nenhuma rede virtual estiver ligada à sua rede no local, crie primeiro uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para a sua Azure-SSIS IR aderir. Em seguida, configuure uma ligação de [gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) local ou ligação [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) dessa rede virtual para a sua rede no local. 

- Se uma rede virtual Azure Resource Manager já estiver ligada à sua rede no local no mesmo local que o seu Azure-SSIS IR, pode juntar-se ao IR a essa rede virtual. 

- Se uma rede virtual clássica já estiver ligada à sua rede no local numa localização diferente da sua Azure-SSIS IR, pode criar uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para a sua Azure-SSIS IR aderir. Em seguida, configurar uma ligação [de rede virtual classic-to-Azure Resource Manager.](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 
 
- Se uma rede virtual Azure Resource Manager já estiver ligada à sua rede no local numa localização diferente da sua Azure-SSIS IR, pode primeiro criar uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para a sua Azure-SSIS IR aderir. Em seguida, configurar uma ligação de rede virtual Azure Resource Manager-to-Azure Resource Manager. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hospedar o catálogo SSIS na Base de Dados SQL

Se hospedar o seu catálogo SSIS numa Base de Dados Azure SQL com pontos finais de serviço de rede virtual, certifique-se de que se junta ao seu Azure-SSIS IR à mesma rede virtual e sub-rede.

Se hospedar o seu catálogo SSIS em SQL Managed Instance com ponto final privado, certifique-se de que se junta ao seu Azure-SSIS IR à mesma rede virtual, mas numa sub-rede diferente da instância gerida. Para aderir ao seu Azure-SSIS IR a uma rede virtual diferente da SQL Managed Instance, recomendamos que o espreitamento de rede virtual (que se limita à mesma região) ou uma ligação da rede virtual à rede virtual. Para obter mais informações, consulte [Ligar a sua aplicação a Azure SQL Managed Instance](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

Se os seus pacotes SSIS acederem a recursos Azure que suportam [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço de rede virtual e pretender garantir o acesso a esses recursos a partir do Azure-SSIS IR, pode juntar-se ao seu Azure-SSIS IR a uma sub-rede virtual configurada para pontos finais de serviço de rede virtual e, em seguida, adicionar uma regra de rede virtual aos recursos Azure relevantes para permitir o acesso a partir da mesma sub-rede.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Acesso a fontes de dados protegidas pela regra de firewall IP

Se os seus pacotes SSIS acederem a lojas/recursos de dados que permitem apenas endereços IP públicos estáticos específicos e pretender garantir o acesso a esses recursos a partir da Azure-SSIS IR, pode associar [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md) ao Azure-SSIS IR, juntando-o a uma rede virtual e, em seguida, adicionar uma regra de firewall IP aos recursos relevantes para permitir o acesso a partir desses endereços IP. Há duas formas alternativas de o fazer: 

- Ao criar O Azure-SSIS IR, pode trazer os seus próprios endereços IP públicos e especirá-los através [da Data Factory UI ou SDK](#join-the-azure-ssis-ir-to-a-virtual-network). Apenas a conectividade de saída da Internet da Azure-SSIS IR utilizará os seus endereços IP públicos fornecidos e outros dispositivos na sub-rede não os utilizarão.
- Também pode configurar [o Virtual Network NAT](../virtual-network/nat-overview.md) para a sub-rede que o Azure-SSIS IR irá aderir e toda a conectividade de saída nesta sub-rede utilizará os seus endereços IP públicos especificados.

Em todos os casos, a rede virtual só pode ser implantada através do modelo de implementação do Gestor de Recursos Azure.

As seguintes secções fornecem mais detalhes. 

## <a name="virtual-network-configuration"></a>Configuração de rede virtual

Crie a sua rede virtual para satisfazer estes requisitos: 

- Certifique-se de que `Microsoft.Batch` se trata de um fornecedor registado sob a subscrição da sua sub-rede de rede virtual que acolhe o Azure-SSIS IR. Se utilizar uma rede virtual clássica, junte-se também `MicrosoftAzureBatch` ao papel de Contribuinte Clássico de Máquina Virtual para essa rede virtual. 

- Certifique-se de que tem as permissões necessárias. Para obter mais informações, consulte [configurar permissões.](#perms)

- Selecione a sub-rede adequada para hospedar o Azure-SSIS IR. Para obter mais informações, consulte [Selecione a sub-rede.](#subnet) 

- Se você trouxer os seus próprios endereços IP públicos para o Azure-SSIS IR, consulte [Selecione os endereços IP públicos estáticos](#publicIP)

- Se utilizar o seu próprio servidor Do Sistema de Nome de Domínio (DNS) na rede virtual, consulte [Configurar o servidor DNS](#dns_server). 

- Se utilizar um grupo de segurança de rede (NSG) na sub-rede, consulte [Configurar um NSG](#nsg). 

- Se utilizar o Azure ExpressRoute ou uma rota definida pelo utilizador (UDR), consulte [Use Azure ExpressRoute ou um UDR](#route). 

- Certifique-se de que o grupo de recursos da rede virtual (ou o grupo de recursos dos endereços IP públicos se trouxer os seus próprios endereços IP públicos) pode criar e eliminar certos recursos da rede Azure. Para obter mais informações, consulte [Configurar o grupo de recursos.](#resource-group) 

- Se personalizar o seu Azure-SSIS IR como descrito na [configuração personalizada para Azure-SSIS IR,](./how-to-configure-azure-ssis-ir-custom-setup.md)o nosso processo interno de gestão dos seus nós consumirá endereços IP privados de uma gama pré-fina de 172.16.0.0 a 172.31.255.255. Consequentemente, certifique-se de que as gamas privadas de endereços IP das suas redes virtuais ou no local não colidem com esta gama.

Este diagrama mostra as ligações necessárias para o seu Azure-SSIS IR:

![Diagrama que mostra as ligações necessárias para o seu Azure-SSIS IR.](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a> Configurar permissões

O utilizador que criar o Azure-SSIS IR deve ter as seguintes permissões:

- Se estiver a juntar o seu SSIS IR a uma rede virtual Azure Resource Manager, tem duas opções:

  - Utilize a função de contribuinte de rede incorporada. Esta função vem com a permissão _Microsoft.Network/ \*_ que tem um âmbito muito maior do que o necessário.

  - Crie uma função personalizada que inclua apenas a necessária _microsoft.network/virtualNetworks/ \* /join/action_ permission. Se também pretender trazer os seus próprios endereços IP públicos para Azure-SSIS IR ao juntar-se a uma rede virtual Azure Resource Manager, por favor inclua também _a Microsoft.Network/publicIPAddresses/*/join/action_ permission in the role.

- Se estiver a juntar-se ao seu SSIS IR a uma rede virtual clássica, recomendamos que utilize o papel de Contribuinte Clássico de Máquina Virtual. Caso contrário, tem de definir um papel personalizado que inclua a permissão para aderir à rede virtual.

### <a name="select-the-subnet"></a><a name="subnet"></a> Selecione a sub-rede

Ao escolher uma sub-rede: 

- Não selecione a GatewaySubnet para implementar um Azure-SSIS IR. É dedicado a portais de rede virtuais. 

- Certifique-se de que a sub-rede selecionada tem espaço de endereço disponível suficiente para o Azure-SSIS IR utilizar. Deixe os endereços IP disponíveis pelo menos duas vezes o número do nó IV. O Azure reserva alguns endereços IP dentro de cada sub-rede. Estes endereços não podem ser usados. Os primeiros e últimos endereços IP das sub-redes estão reservados para a conformidade do protocolo, e mais três endereços são utilizados para os serviços Azure. Para mais informações, consulte [existem restrições à utilização de endereços IP dentro destas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Não utilize uma sub-rede exclusivamente ocupada por outros serviços Azure (por exemplo, SQL Database SQL Managed Instance, App Service, e assim por diante). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Selecione os endereços IP públicos estáticos

Se pretender trazer os seus próprios endereços IP públicos estáticos para o Azure-SSIS IR ao aderir a uma rede virtual, certifique-se de que cumprem os seguintes requisitos:

- Devem ser fornecidos exatamente dois que não estão já associados a outros recursos Azure. O extra será usado quando atualizarmos periodicamente o seu Azure-SSIS IR. Note que um endereço IP público não pode ser partilhado entre os seus IRs Azure-SSIS ativos.

- Ambos devem ser estáticos de tipo padrão. Consulte [as SKUs do Endereço IP Público](../virtual-network/public-ip-addresses.md#sku) para mais informações.

- Ambos deviam ter um nome DNS. Se não tiver fornecido um nome DNS ao criá-los, pode fazê-lo no portal Azure.

![Runtime de integração Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Eles e a rede virtual devem estar sob a mesma subscrição e na mesma região.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a> Configurar o servidor DNS 
Se precisar de utilizar o seu próprio servidor DNS numa rede virtual a que se juntou o seu Azure-SSIS IR para resolver o seu nome de anfitrião privado, certifique-se de que também pode resolver os nomes globais dos anfitriões do Azure (por exemplo, uma bolha de armazenamento Azure `<your storage account>.blob.core.windows.net` chamada). 

Uma abordagem recomendada é a seguir: 

-   Configure o DNS personalizado para encaminhar pedidos para Azure DNS. Pode encaminhar registos DNS não resolvidos para o endereço IP do Azure resoluçs recursivos (168.63.129.16) no seu próprio servidor DNS. 

Para obter mais informações, consulte [a resolução Nome que utiliza o seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Utilize um nome de domínio totalmente qualificado (FQDN) para o seu nome de anfitrião privado (por exemplo, use `<your_private_server>.contoso.com` em vez de `<your_private_server>` ). Em alternativa, pode utilizar uma configuração personalizada padrão no seu Azure-SSIS IR para anexar automaticamente o seu próprio sufixo DNS (por exemplo) a qualquer nome de `contoso.com` domínio de rótulo único não qualificado e transformá-lo num FQDN antes de o utilizar em consultas DNS, consulte [amostras de configuração personalizadas padrão.](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples) 

### <a name="set-up-an-nsg"></a><a name="nsg"></a> Configurar um NSG
Se precisar de implementar um NSG para a sub-rede utilizada pelo seu Azure-SSIS IR, permita o tráfego de entrada e saída através das seguintes portas: 

-   **Requisito de entrada da Azure-SSIS IR**

| Direção | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
|---|---|---|---|---|---|---|
| Entrada | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (se aderir ao IR a uma rede virtual do Gestor de Recursos) <br/><br/>10100, 20100, 30100 (se juntar o IR a uma rede virtual clássica)| O serviço Data Factory utiliza estas portas para comunicar com os nós do seu Azure-SSIS IR na rede virtual. <br/><br/> Quer crie ou não um NSG de nível sub-rede, a Data Factory configura sempre um NSG ao nível dos cartões de interface de rede (NICs) ligados às máquinas virtuais que acolhem o Azure-SSIS IR. Apenas o tráfego de entrada a partir de endereços IP da Data Factory nas portas especificadas é permitido por esse NSG de nível NIC. Mesmo que abra estas portas ao tráfego de internet ao nível da sub-rede, o tráfego de endereços IP que não são endereços IP da Data Factory está bloqueado ao nível do NIC. |
| Entrada | TCP | Serra CorpNet | * | VirtualNetwork | 3389 | (Opcional) Esta regra só é necessária quando o apoiante da Microsoft pede ao cliente para abrir para uma resolução avançada de problemas, e pode ser fechada logo após a resolução de problemas. A tag de serviço **CorpNetSaw** permite apenas estações de trabalho de acesso segura na rede corporativa da Microsoft para utilizar o ambiente de trabalho remoto. E esta etiqueta de serviço não pode ser selecionada a partir do portal e só está disponível através do Azure PowerShell ou do Azure CLI. <br/><br/> No nível NIC NSG, a porta 3389 está aberta por defeito e permitimos que controle a porta 3389 no nível de sub-rede NSG, enquanto a Azure-SSIS IR não permitiu a saída da porta 3389 por defeito na regra de firewall do Windows firewall em cada nó DE IDS para proteção. |
||||||||

-   **Requisito de saída da Azure-SSIS IR**

| Direção | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
|---|---|---|---|---|---|---|
| Saída | TCP | VirtualNetwork | * | AzureCloud | 443 | Os nós do seu Azure-SSIS IR na rede virtual utilizam esta porta para aceder aos serviços Azure, como o Azure Storage e o Azure Event Hubs. |
| Saída | TCP | VirtualNetwork | * | Internet | 80 | (Opcional) Os nós do seu Azure-SSIS IR na rede virtual utilizam esta porta para descarregar uma lista de revogação de certificados da internet. Se bloquear este tráfego, poderá experimentar uma degradação de desempenho quando iniciar o IR e perder capacidade de verificar a lista de revogação de certificados para utilização do certificado. Se quiser reduzir ainda mais o destino a certos FQDNs, consulte a secção **Use Azure ExpressRoute ou UDR**|
| Saída | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | (Opcional) Esta regra só é exigida quando os nós do seu Azure-SSIS IR na rede virtual acedem a um SSISDB hospedado pelo seu servidor. Se a sua política de ligação do servidor estiver definida para **Proxy** em vez de **Redirecionar**, apenas é necessária a porta 1433. <br/><br/> Esta regra de segurança de saída não é aplicável a um SSISDB hospedado pela sua SQL Managed Instance na rede virtual ou base de dados SQL configurada com ponto final privado. |
| Saída | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (Opcional) Esta regra só é exigida quando os nós do seu Azure-SSIS IR na rede virtual acedem a um SSISDB hospedado pela sua SQL Managed Instance na rede virtual ou sql Database configurado com ponto final privado. Se a sua política de ligação do servidor estiver definida para **Proxy** em vez de **Redirecionar**, apenas é necessária a porta 1433. |
| Saída | TCP | VirtualNetwork | * | Armazenamento | 445 | (Opcional) Esta regra só é necessária quando pretender executar o pacote SSIS armazenado em Ficheiros Azure. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Use Azure ExpressRoute ou UDR
Se pretender inspecionar o tráfego de saída da Azure-SSIS IR, pode encaminhar o tráfego iniciado da Azure-SSIS IR para o aparelho de firewall no local através do túnel da força [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) (publicidade de uma rota BGP, 0.0.0.0/0, para a rede virtual) ou para o Sistema Virtual de Rede (NVA) como firewall ou [Azure Firewall](../firewall/index.yml) via [UDRs](../virtual-network/virtual-networks-udr-overview.md). 

![Cenário NVA para Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Tens de fazer coisas abaixo para que todo o cenário funcione.
   -   O tráfego de entrada entre os serviços de gestão do Azure Batch e o Azure-SSIS IR não pode ser encaminhado através de um aparelho de firewall.
   -   O aparelho de firewall deve permitir o tráfego de saída exigido pela Azure-SSIS IR.

O tráfego de entrada entre os serviços de gestão do Azure Batch e o Azure-SSIS IR não pode ser encaminhado para o aparelho de firewall, caso contrário o tráfego será interrompido devido a um problema de encaminhamento assimétrico. As vias devem ser definidas para o tráfego de entrada, de modo a que o tráfego possa responder da mesma forma que entrou. Pode definir UDRs específicos para encaminhar o tráfego entre os serviços de gestão do Azure Batch e o Azure-SSIS IR com o próximo tipo de lúpulo como **Internet.**

Por exemplo, se o seu Azure-SSIS IR estiver localizado `UK South` e pretender inspecionar o tráfego de saída através do Azure Firewall, obterá em primeiro lugar uma lista de identificação de serviço da gama IP `BatchNodeManagement.UKSouth` a partir do link de descarregamento de gama IP de [tags](https://www.microsoft.com/download/details.aspx?id=56519) de serviço ou através da [API de Deteção de Identificação](../virtual-network/service-tags-overview.md#service-tags-on-premises)de Serviço. Em seguida, aplique os seguintes UDRs de rotas de gama IP relacionadas com o próximo tipo de lúpulo como **Internet,** juntamente com a rota 0.0.0.0/0 com o próximo tipo de lúpulo como **aparelho virtual**.

![Definições de UDR de lote de Azure](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Esta abordagem incorre num custo adicional de manutenção. Verifique regularmente a gama IP e adicione novas gamas IP no seu UDR para evitar quebrar o Azure-SSIS IR. Recomendamos verificar mensalmente a gama IP porque quando o novo IP aparecer na etiqueta de serviço, o IP entrará em vigor mais um mês. 

Para facilitar a configuração das regras da UDR, pode executar o seguinte script powershell para adicionar regras UDR para serviços de gestão do Azure Batch:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Para o aparelho de firewall permitir o tráfego de saída, é necessário permitir que as portas de saída para baixos, iguais às regras de saída da NSG.
-   Porto 443 com destino como serviços Azure Cloud.

    Se utilizar o Azure Firewall, pode especificar a regra da rede com a Etiqueta de Serviço AzureCloud. Para firewall dos outros tipos, você pode simplesmente permitir o destino como todos para a porta 443 ou permitir abaixo de FQDNs com base no tipo do seu ambiente Azure:

    | Ambiente Azure | Pontos Finais                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Público      | <ul><li><b>Fábrica de Dados Azure (Gestão)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Armazenamento Azure (Gestão)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Registo do contentor Azure (Configuração personalizada)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>Centro de Eventos (Registo)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Serviço de Registo microsoft (utilização interna)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Fábrica de Dados Azure (Gestão)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Armazenamento Azure (Gestão)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Registo do contentor Azure (Configuração personalizada)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>Centro de Eventos (Registo)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Serviço de Registo microsoft (utilização interna)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Fábrica de Dados Azure (Gestão)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Armazenamento Azure (Gestão)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Registo do contentor Azure (Configuração personalizada)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>Centro de Eventos (Registo)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>Serviço de Registo microsoft (utilização interna)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Quanto às FQDNs de Azure Storage, Azure Container Registry e Event Hub, também pode optar por ativar os seguintes pontos finais de serviço para a sua rede virtual, de modo a que o tráfego de rede para estes pontos finais passe pela rede de espinha dorsal Azure em vez de ser encaminhado para o seu aparelho de firewall:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   Porto 80 com destino como sites de descarregamento CRL.

    Você deve permitir abaixo fQDNs que são usados como CRL (Lista de Revogação de Certificados) sites de certificados para fins de gestão Azure-SSIS IR:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Se estiver a utilizar certificados com CRL diferente, é-lhe sugerido que os inclua também. Pode ler isto para saber mais na [Lista de Revogação de Certificados.](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)

    Se não permitir este tráfego, poderá experimentar uma degradação de desempenho quando iniciar o Azure-SSIS IR e perder capacidade para verificar a lista de revogação de certificados para utilização do certificado, o que não é recomendado do ponto de vista de segurança.

-   Porta 1433, 11000-11999 com destino como Base de Dados Azure SQL (só é necessário quando os nódoas do seu Azure-SSIS IR na rede virtual acedam a um SSISDB hospedado pelo seu servidor).

    Se utilizar o Azure Firewall, pode especificar a regra da rede com a Etiqueta de Serviço Azure SQL, caso contrário poderá permitir o destino como url de azure sql específico no aparelho de firewall.

-   Porta 445 com destino como Azure Storage (só é necessário quando executa o pacote SSIS armazenado em Ficheiros Azure).

    Se utilizar o Azure Firewall, pode especificar a regra da rede com a Etiqueta de Serviço de Armazenamento, caso contrário poderá permitir o destino como url de armazenamento de ficheiros azure específico no aparelho de firewall.

> [!NOTE]
> Para o Azure SQL e Armazenamento, se configurar os pontos finais do serviço de Rede Virtual na sua sub-rede, então o tráfego entre a Azure-SSIS IR e a Azure SQL na mesma região \ O Armazenamento Azure na mesma região ou região emparelhada será encaminhado diretamente para a rede de espinha dorsal do Microsoft Azure em vez do seu aparelho de firewall.

Se não precisar de capacidade de inspecionar o tráfego de saída do Azure-SSIS IR, pode simplesmente aplicar uma rota para forçar todo o tráfego para a próxima **Internet** do tipo hop:

-   Num cenário Azure ExpressRoute, pode aplicar uma rota de 0.0.0.0/0 com o próximo tipo de lúpulo como **Internet** na sub-rede que acolhe o Azure-SSIS IR. 
-   Num cenário de NVA, pode modificar a rota 0.0.0.0/0 existente aplicada na sub-rede que acolhe o Azure-SSIS IR do próximo tipo de lúpulo como **aparelho virtual** para a **Internet.**

![Adicionar uma rota](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Especificar a rota com o próximo tipo de lúpulo **Internet** não significa que todo o tráfego irá passar pela Internet. Enquanto o endereço de destino for para um dos serviços da Azure, a Azure encaminha o tráfego diretamente para o serviço através da rede de espinha dorsal da Azure, em vez de encaminhar o tráfego para a Internet.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a> Criar o grupo de recursos

O Azure-SSIS IR precisa de criar certos recursos de rede no mesmo grupo de recursos que a rede virtual. Estes recursos incluem:
- Um equilibrador de carga Azure, com o nome *\<Guid> -azurebatch-cloudserviceloadanceancer*.
- Um endereço IP público Azure, com o nome *\<Guid> -azurebatch-cloudservicepublicip*.
- Um grupo de segurança de trabalho de rede, com o nome *\<Guid> -azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Pode agora trazer os seus próprios endereços IP públicos estáticos para a Azure-SSIS IR. Neste cenário, criaremos apenas o equilibrador de carga Azure e o grupo de segurança da rede sob o mesmo grupo de recursos que os seus endereços IP públicos estáticos em vez da rede virtual.

Esses recursos serão criados quando o seu IR Azure-SSIS começar. Serão apagados quando o seu Azure-SSIS IR parar. Se trouxer os seus próprios endereços IP públicos estáticos para O Azure-SSIS IR, os seus próprios endereços IP públicos estáticos não serão apagados quando o seu Azure-SSIS IR parar. Para evitar que o seu Azure-SSIS IR pare, não reutilize estes recursos de rede nos seus outros recursos.

Certifique-se de que não tem nenhum bloqueio de recursos no grupo de recursos/subscrição a que pertencem os endereços IP públicos da rede virtual/da sua estática. Se configurar um bloqueio apenas de leitura/apagar, iniciar e parar o seu Azure-SSIS IR falhará, ou deixará de responder.

Certifique-se de que não tem uma atribuição da Política Azure que impeça a criação dos seguintes recursos no âmbito do grupo de recursos/subscrição a que pertencem os endereços IP públicos da rede virtual/dos seus endereços IP públicos estáticos: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

Certifique-se de que a quota de recursos da sua subscrição é suficiente para os três recursos de rede acima. Especificamente, para cada Azure-SSIS IR criado em rede virtual, é necessário reservar duas quotas gratuitas para cada um dos três recursos de rede acima referidos. A quota extra será usada quando atualizarmos periodicamente o seu Azure-SSIS IR.

### <a name="faq"></a><a name="faq"></a> Perguntas Frequentes

- Como posso proteger o endereço IP público exposto no meu Azure-SSIS IR para ligação à entrada? É possível remover o endereço IP público?
 
  Neste momento, um endereço IP público será automaticamente criado quando o seu Azure-SSIS IR se juntar a uma rede virtual. Dispomos de um NSG de nível NIC que permite que apenas os serviços de gestão do Azure Batch se conectem ao seu Azure-SSIS IR. Também pode especificar um NSG de nível de sub-rede para proteção de entrada.

  Se não quiser que nenhum endereço IP público seja exposto, considere [configurar um IR auto-hospedado como procuração para o seu Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md) em vez de se juntar ao seu Azure-SSIS IR a uma rede virtual, se isso se aplicar ao seu cenário.
 
- Posso adicionar o endereço IP público do meu Azure-SSIS IR à lista de autorizações da firewall para as minhas fontes de dados?

  Pode agora trazer os seus próprios endereços IP públicos estáticos para a Azure-SSIS IR. Neste caso, pode adicionar os seus endereços IP à lista de autorizações da firewall para as suas fontes de dados. Também pode considerar outras opções abaixo para garantir o acesso aos dados a partir do seu Azure-SSIS IR dependendo do seu cenário:

  - Se a sua fonte de dados estiver no local, depois de ligar uma rede virtual à sua rede no local e de se juntar ao seu Azure-SSIS IR à sub-rede de rede virtual, pode então adicionar o intervalo de endereço IP privado dessa sub-rede à lista de permitis da firewall para a sua fonte de dados.
  - Se a sua fonte de dados for um serviço Azure que suporta pontos finais de serviço de rede virtual, pode configurar um ponto final de serviço de rede virtual na sua sub-rede de rede virtual e juntar-se ao seu Azure-SSIS IR a essa sub-rede. Em seguida, pode adicionar uma regra de rede virtual com essa sub-rede à firewall para a sua fonte de dados.
  - Se a sua fonte de dados for um serviço de nuvem não-Azure, pode utilizar um UDR para encaminhar o tráfego de saída do seu Azure-SSIS IR para uma Firewall NVA/Azure através de um endereço IP público estático. Em seguida, pode adicionar o endereço IP público estático do seu NVA/Azure Firewall à lista de autorizações da firewall para a sua fonte de dados.
  - Se nenhuma das opções acima corresponde às suas necessidades, considere [configurar um IR auto-hospedado como procuração para o seu Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). Em seguida, pode adicionar o endereço IP público estático da máquina que hospeda o seu IR auto-hospedado na lista de autorizações da firewall para a sua fonte de dados.

- Por que preciso de fornecer duas moradas públicas estáticas se quero trazer a minha própria para a Azure-SSIS IR?

  O Azure-SSIS IR é atualizado automaticamente regularmente. Novos nós são criados durante a atualização e os antigos serão eliminados. No entanto, para evitar tempo de inatividade, os nós antigos não serão apagados até que os novos estejam prontos. Assim, o seu primeiro endereço IP público estático utilizado pelos nós antigos não pode ser lançado imediatamente e precisamos do seu segundo endereço IP público estático para criar os novos nós.

- Trouxe os meus próprios endereços IP públicos estáticos para o Azure-SSIS IR, mas porque é que ainda não consegue aceder às minhas fontes de dados?

  - Confirme que os dois endereços IP públicos estáticos são adicionados à lista de autorizações da firewall para as suas fontes de dados. Cada vez que o seu Azure-SSIS IR é atualizado, o seu endereço IP público estático é alternado entre os dois trazidos por si. Se adicionar apenas um deles à lista de admissões, o acesso de dados para o seu Azure-SSIS IR será quebrado após a sua atualização.
  - Se a sua fonte de dados for um serviço Azure, verifique se o configura com pontos finais de serviço de rede virtual. Se for esse o caso, o tráfego da Azure-SSIS IR para a sua fonte de dados irá mudar para utilizar os endereços IP privados geridos pelos serviços Azure e adicionar os seus próprios endereços IP públicos estáticos à lista de autorizações da firewall para a sua fonte de dados não produzirá efeito.

## <a name="azure-portal-data-factory-ui"></a>Azure portal (Data Factory UI)

Esta secção mostra-lhe como se juntar a um Azure-SSIS IR existente a uma rede virtual (classic ou Azure Resource Manager) utilizando o portal Azure e a Data Factory UI. 

Antes de se juntar ao seu Azure-SSIS IR à rede virtual, é necessário configurar adequadamente a rede virtual. Siga os passos na secção que se aplica ao seu tipo de rede virtual (classic ou Azure Resource Manager). Em seguida, siga os passos na terceira secção para se juntar ao seu Azure-SSIS IR à rede virtual. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Configurar uma rede virtual Azure Resource Manager

Utilize o portal para configurar uma rede virtual Azure Resource Manager antes de tentar aderir a um Azure-SSIS IR para o mesmo.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas estes navegadores da Web suportam a UI da Data Factory. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Selecione **Mais serviços**. Filtrar e selecionar **redes virtuais.** 

1. Filtre e selecione a sua rede virtual na lista. 

1. Na página **de rede Virtual,** selecione **Propriedades.** 

1. Selecione o botão de cópia para **O ID do RECURSO** para copiar o ID do recurso para a rede virtual para a área de transferência. Guarde o ID da área de transferência no OneNote ou num ficheiro. 

1. No menu esquerdo, selecione **Subnets**. Certifique-se de que o número de endereços disponíveis é maior do que os nós do seu Azure-SSIS IR. 

1. Verifique se o fornecedor Azure Batch está registado na subscrição do Azure que possui a rede virtual. Ou registe o fornecedor Azure Batch. Se já tem uma conta Azure Batch na sua subscrição, a sua subscrição está registada no Azure Batch. (Se criar o Azure-SSIS IR no portal Data Factory, o fornecedor Azure Batch está automaticamente registado para si.) 

   1. No portal Azure, no menu esquerdo, selecione **Subscrições**. 

   1. Selecione a sua subscrição. 

   1. À esquerda, selecione **Fornecedores de Recursos**, e confirme que **Microsoft.Batch** é um fornecedor registado. 

   ![Confirmação do estado "Registado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se não vir **Microsoft.Batch** na lista, para registá-lo, [crie uma conta Azure Batch vazia](../batch/batch-account-create-portal.md) na sua subscrição. Pode apagá-lo mais tarde. 

### <a name="configure-a-classic-virtual-network"></a>Configurar uma rede virtual clássica

Utilize o portal para configurar uma rede virtual clássica antes de tentar aderir a um Azure-SSIS IR. 

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas estes navegadores da Web suportam a UI da Data Factory. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Selecione **Mais serviços**. Filtrar e selecionar **redes virtuais (clássicas)**. 

1. Filtre e selecione a sua rede virtual na lista. 

1. Na página **de rede Virtual (clássica),** selecione **Propriedades.** 

   ![ID de recurso de rede virtual clássico](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Selecione o botão de cópia para **O ID recurso** para copiar o ID do recurso para a rede clássica para a área de transferência. Guarde o ID da área de transferência no OneNote ou num ficheiro. 

1. No menu esquerdo, selecione **Subnets**. Certifique-se de que o número de endereços disponíveis é maior do que os nós do seu Azure-SSIS IR. 

   ![Número de endereços disponíveis na rede virtual](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Junte-se ao **MicrosoftAzureBatch** ao papel **de Contribuinte Clássico de Máquina Virtual** para a rede virtual. 

   1. No menu esquerdo, selecione **Access control (IAM)** e selecione o **separador Fun assignments.** 

       ![Botões "Controlo de acesso" e "Adicionar"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Selecione **Adicionar atribuição de função**.

   1. Na página **de atribuição de funções Add,** para **Role**, selecione Classic Virtual **Machine Contributor**. Na caixa **Select,** pasta **ddbf3205-c6bd-46ae-8127-60eb93363864**, e, em seguida, selecione **Microsoft Azure Batch** na lista de resultados de pesquisa. 

       ![Resultados da pesquisa na página "Adicionar atribuição de funções"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. **Selecione Guardar** para guardar as definições e fechar a página. 

       ![Guardar definições de acesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Confirme que vê o **Microsoft Azure Batch** na lista de contribuintes. 

       ![Confirme o acesso ao Lote Azure](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verifique se o fornecedor Azure Batch está registado na subscrição do Azure que possui a rede virtual. Ou registe o fornecedor Azure Batch. Se já tem uma conta Azure Batch na sua subscrição, a sua subscrição está registada no Azure Batch. (Se criar o Azure-SSIS IR no portal Data Factory, o fornecedor Azure Batch está automaticamente registado para si.) 

   1. No portal Azure, no menu esquerdo, selecione **Subscrições**. 

   1. Selecione a sua subscrição. 

   1. À esquerda, selecione **Fornecedores de Recursos**, e confirme que **Microsoft.Batch** é um fornecedor registado. 

   ![Confirmação do estado "Registado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se não vir **Microsoft.Batch** na lista, para registá-lo, [crie uma conta Azure Batch vazia](../batch/batch-account-create-portal.md) na sua subscrição. Pode apagá-lo mais tarde. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Junte-se ao Azure-SSIS IR a uma rede virtual

Depois de configurar a sua rede virtual Azure Resource Manager ou rede virtual clássica, pode juntar-se ao Azure-SSIS IR à rede virtual:

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas estes navegadores da Web suportam a UI da Data Factory. 

1. No [portal Azure,](https://portal.azure.com)no menu esquerdo, selecione **Fábricas de Dados.** Se não vir **fábricas de Dados** no menu, selecione **Mais serviços** e, em seguida, na secção **INTELLIGENCE + ANALYTICS,** selecione **Fábricas de Dados**. 

   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecione a sua fábrica de dados com o Azure-SSIS IR na lista. Vê a página inicial da sua fábrica de dados. Selecione o **azulejo do Monitor &** autor. Vê a UI da Data Factory num separador. 

   ![Home page da fábrica de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Na UI da Fábrica de Dados, mude para o **separador Editar,** selecione **Connections** e mude para o separador **Tempos de Execução de Integração.** 

   ![Separador "Tempos de execução de integração"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se o seu Azure-SSIS IR estiver em execução, na lista **de Tempos de Execução de Integração,** na coluna **Ações,** selecione o botão **Stop** para o seu Azure-SSIS IR. Não pode editar o seu Azure-SSIS IR até que o pare. 

   ![Parar o IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na lista **de Tempos de Execução de Integração,** na coluna **Ações,** selecione o botão **Editar** para o seu Azure-SSIS IR. 

   ![Editar o tempo de integração](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. No painel de configuração do tempo de execução da integração, avance através das **definições gerais** e **das definições SQL** selecionando o botão **Seguinte.** 

1. Na secção **Definições Avançadas:** 

   1. Selecione o **VNet para o seu tempo de execução de integração Azure-SSIS para aderir, permita que a ADF crie certos recursos de rede e, opcionalmente, traga a sua própria caixa de verificação de endereços IP públicos estáticos.** 

   1. Para **Subscrição**, selecione a subscrição Azure que tem a sua rede virtual.

   1. Para **a localização,** é selecionado o mesmo local do seu tempo de execução de integração.

   1. Para **tipo,** selecione o tipo da sua rede virtual: classic ou Azure Resource Manager. Recomendamos que selecione uma rede virtual Azure Resource Manager, porque redes virtuais clássicas serão depreciadas em breve.

   1. Para **O Nome VNet,** selecione o nome da sua rede virtual. Deve ser o mesmo usado para a Base de Dados SQL com pontos finais de serviço de rede virtual ou SQL Managed Instance com ponto final privado para hospedar O SSISDB. Ou deve ser o mesmo ligado à sua rede no local. Caso contrário, pode ser qualquer rede virtual para trazer os seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

   1. Para **nome da sub-rede,** selecione o nome da sub-rede para a sua rede virtual. Deve ser o mesmo usado para a Base de Dados SQL com pontos finais de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da utilizada para a SQL Managed Instance com ponto final privado para hospedar o SSISDB. Caso contrário, pode ser qualquer sub-rede para trazer os seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

   1. Selecione os **endereços IP públicos estáticos para a sua caixa de verificação de tempo de execução de integração Azure-SSIS** para escolher se pretende trazer os seus próprios endereços IP públicos estáticos para Azure-SSIS IR, para que possa permitir que fiquem na firewall para as suas fontes de dados.

      Se selecionar a caixa de verificação, complete os seguintes passos.

      1. Para **o primeiro endereço IP público estático**, selecione o primeiro endereço IP público estático que satisfaz os [requisitos](#publicIP) para o seu Azure-SSIS IR. Se não tiver nenhum, clique em **Criar uma nova** ligação para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que possa selecioná-los.
      
      1. Para **o segundo endereço IP público estático**, selecione o segundo endereço IP público estático que satisfaz os [requisitos](#publicIP) para o seu Azure-SSIS IR. Se não tiver nenhum, clique em **Criar uma nova** ligação para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que possa selecioná-los.

   1. Selecione **validação VNet**. Se a validação for bem sucedida, **selecione Continue**. 

   ![Definições avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Na secção **Resumo,** reveja todas as definições para o seu Azure-SSIS IR. Em seguida, selecione **Update**.

1. Inicie o seu Azure-SSIS IR selecionando o botão **Iniciar** na coluna **Ações** para o seu Azure-SSIS IR. Leva cerca de 20 a 30 minutos para iniciar o Azure-SSIS IR que une uma rede virtual. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Definir as variáveis

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Antes de se juntar ao seu Azure-SSIS IR a uma rede virtual, tem de configurar a rede virtual. Para configurar automaticamente permissões e configurações de rede virtuais para o seu Azure-SSIS IR para se juntar à rede virtual, adicione o seguinte script:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Crie um Azure-SSIS IR e junte-se a uma rede virtual

Pode criar um Azure-SSIS IR e juni-o a uma rede virtual ao mesmo tempo. Para obter o roteiro e instruções completos, consulte [Criar um Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Junte-se a um Azure-SSIS IR existente a uma rede virtual

O artigo [Create a Azure-SSIS IR mostra-lhe](create-azure-ssis-integration-runtime.md) como criar um Azure-SSIS IR e junê-lo a uma rede virtual no mesmo script. Se já tem um Azure-SSIS IR, siga estes passos para se juntar à rede virtual: 
1. Pare o Azure-SSIS IR. 
1. Configure o Azure-SSIS IR para aderir à rede virtual. 
1. Inicie o Azure-SSIS IR. 

### <a name="stop-the-azure-ssis-ir"></a>Pare o Azure-SSIS IR

Tem de parar o Azure-SSIS IR antes de poder aderir a uma rede virtual. Este comando liberta todos os seus nós e para a faturação:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurar definições de rede virtual para a Azure-SSIS IR aderir

Para configurar as definições para a rede virtual que o Azure-SSIS irá aderir, utilize este script: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configure o Azure-SSIS IR

Para se juntar ao seu Azure-SSIS IR a uma rede virtual, execute o `Set-AzDataFactoryV2IntegrationRuntime` comando: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Inicie o Azure-SSIS IR

Para iniciar o Azure-SSIS IR, executar o seguinte comando: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Este comando leva 20 a 30 minutos para terminar.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure-SSIS IR, consulte os seguintes artigos: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceptuais gerais sobre as IRs, incluindo a Azure-SSIS IR. 
- [Tutorial: Implementar pacotes SSIS para Azure](./tutorial-deploy-ssis-packages-azure.md). Este tutorial fornece instruções passo a passo para criar o seu Azure-SSIS IR. Utiliza a Base de Dados Azure SQL para acolher o catálogo SSIS. 
- [Criar um Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Este artigo expande-se sobre o tutorial. Fornece instruções sobre a utilização da Base de Dados Azure SQL com pontos finais de serviço de rede virtual ou SQL Managed Instance numa rede virtual para hospedar o catálogo SSIS. Mostra como juntar o seu Azure-SSIS IR a uma rede virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra-lhe como obter informações sobre o seu Azure-SSIS IR. Fornece descrições de estado para as informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou eliminar o seu Azure-SSIS IR. Também mostra como escalar o seu Azure-SSIS IR adicionando nós.
