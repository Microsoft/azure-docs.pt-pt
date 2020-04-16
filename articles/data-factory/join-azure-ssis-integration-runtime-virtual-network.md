---
title: Join an Azure-SSIS integration runtime to a virtual network (Associar um runtime de integração do Azure-SSIS a uma rede virtual)
description: Aprenda a aderir a um tempo de funcionação de integração Azure-SSIS para uma rede virtual Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: cf13dbe17738ca1ae658c73bb0092a219b4823d1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415907"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Join an Azure-SSIS integration runtime to a virtual network (Associar um runtime de integração do Azure-SSIS a uma rede virtual)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ao utilizar os Serviços de Integração de Servidores SQL (SSIS) na Azure Data Factory, deverá juntar-se ao seu tempo de execução de integração Azure-SSIS (IR) a uma rede virtual Azure nos seguintes cenários:

- Pretende ligar-se às lojas de dados no local a partir de pacotes SSIS que funcionam no seu IR Azure-SSIS sem configurar ou gerir um IR auto-hospedado como procuração. 

- Deseja hospedar a base de dados de catálogo SSIS (SSISDB) numa Base de Dados Azure SQL com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerida com ponto final privado. 

- Pretende ligar-se aos recursos do Azure configurados com pontos finais de serviço de rede virtual a partir de pacotes SSIS que funcionam no seu IR Azure-SSIS.

- Pretende ligar-se a lojas de dados/recursos configurados com regras de firewall IP a partir de pacotes SSIS que funcionam no seu IR Azure-SSIS.

Data Factory permite-lhe aderir ao seu IR Azure-SSIS a uma rede virtual criada através do modelo clássico de implementação ou do modelo de implementação do Gestor de Recursos Azure.

> [!IMPORTANT]
> A rede virtual clássica está a ser depreciada, por isso use a rede virtual Do Gestor de Recursos Azure.  Se já utilizar a rede virtual clássica, mude para a rede virtual Do Gestor de Recursos Azure o mais rapidamente possível.

A configuração de um tempo de integração do Serviço de Integração de [Servidores Azure-SQL (SSIS) para aderir a um](tutorial-deploy-ssis-virtual-network.md) tutorial de rede virtual mostra os passos mínimos através do portal Azure. Este artigo expande-se no tutorial e descreve todas as tarefas opcionais:

- Se estiver a utilizar a rede virtual (clássica).
- Se trouxer os seus próprios endereços IP públicos para o IR Azure-SSIS.
- Se utilizar o seu próprio servidor do Sistema de Nomede Domínio (DNS).
- Se utilizar um grupo de segurança de rede (NSG) na sub-rede.
- Se utilizar o Azure ExpressRoute ou uma rota definida pelo utilizador (UDR).
- Se utilizar o Azure-SSIS IR personalizado.
- Se utilizar o fornecimento Azure Powershell.

## <a name="access-to-on-premises-data-stores"></a>Acesso a lojas de dados no local

Se o seu SSIS aceder às lojas de dados no local, pode juntar-se ao seu IR Azure-SSIS a uma rede virtual que esteja ligada à rede no local. Ou pode configurar e gerir um IR auto-hospedado como procuração para o seu IR Azure-SSIS. Para mais informações, consulte [configure um IR auto-hospedado como um proxy para um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Ao juntar o seu IR Azure-SSIS a uma rede virtual, lembre-se destes pontos importantes: 

- Se nenhuma rede virtual estiver ligada à sua rede no local, crie primeiro uma rede virtual do Gestor de [Recursos Azure](../virtual-network/quick-create-portal.md#create-a-virtual-network) para o seu IR Azure-SSIS se juntar. Em seguida, configure uma ligação de [gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) site-to-site ou [ligação ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) dessa rede virtual para a sua rede no local. 

- Se uma rede virtual do Gestor de Recursos Azure já estiver ligada à sua rede no local no mesmo local que o seu IR Azure-SSIS, pode juntar-se ao IR a essa rede virtual. 

- Se uma rede virtual clássica já estiver ligada à sua rede no local num local diferente do seu IR Azure-SSIS, pode criar uma [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para o seu IR Azure-SSIS se juntar. Em seguida, configure uma ligação [de rede virtual classic-to-Azure Resource Manager.](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 
 
- Se uma rede virtual do Gestor de Recursos Azure já estiver ligada à sua rede no local num local diferente do seu IR Azure-SSIS, pode primeiro criar uma rede virtual do [Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para o seu IR Azure-SSIS para aderir. Em seguida, configure uma ligação virtual de rede de rede de rede de recursos Azure Resource Manager-to-Azure. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hospedagem do catálogo SSIS na Base de Dados SQL

Se hospedar o seu catálogo SSIS numa Base de Dados Azure SQL com pontos finais de serviço de rede virtual, certifique-se de que se junta ao seu IR Azure-SSIS à mesma rede virtual e subnet.

Se hospedar o seu catálogo SSIS numa instância gerida com ponto final privado, certifique-se de que se junta ao seu IR Azure-SSIS para a mesma rede virtual, mas numa subrede diferente da instância gerida. Para aderir ao seu IR Azure-SSIS a uma rede virtual diferente da instância gerida, recomendamos o peering de rede virtual (que está limitado à mesma região) ou uma ligação da rede virtual à rede virtual. Para mais informações, consulte [Ligue a sua aplicação à base de dados Azure SQL gerida](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

Se os seus pacotes SSIS acederem aos recursos Do Azure que suportam [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço de rede virtual e pretender garantir o acesso a esses recursos do Ir Azure-SSIS, pode juntar-se ao seu IR Azure-SSIS a uma rede virtual configurada para pontos finais de serviço de rede virtual e, em seguida, adicionar uma regra de rede virtual aos recursos do Azure relevantes para permitir o acesso a partir da mesma subnet.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Acesso a fontes de dados protegidas pela regra da firewall IP

Se os seus pacotes SSIS acederem a lojas/recursos de dados que permitem apenas endereços IP públicos estáticos específicos e pretender garantir o acesso a esses recursos a partir do Ir Azure-SSIS, pode trazer os seus [próprios endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) para o IR Azure-SSIS, ao mesmo tempo que o une a uma rede virtual e, em seguida, adicionar uma regra de firewall IP aos recursos relevantes para permitir o acesso desses endereços IP.

Em todos os casos, a rede virtual só pode ser implantada através do modelo de implementação do Gestor de Recursos Azure.

As seguintes secções fornecem mais detalhes. 

## <a name="virtual-network-configuration"></a>Configuração da rede virtual

Instale a sua rede virtual para satisfazer estes requisitos: 

- Certifique-se `Microsoft.Batch` de que se trata de um fornecedor registado no âmbito da subscrição da sua subnet de rede virtual que acolhe o IR Azure-SSIS. Se utilizar uma rede virtual `MicrosoftAzureBatch` clássica, junte-se também ao papel clássico de Colaborador de Máquina virtual para essa rede virtual. 

- Certifique-se de que tem as permissões necessárias. Para mais informações, consulte [Configurar permissões](#perms).

- Selecione a sub-rede adequada para acolher o IR Azure-SSIS. Para mais informações, consulte [Selecione a sub-rede](#subnet). 

- Se trouxer os seus próprios endereços IP públicos para o IR Azure-SSIS, consulte [Selecione os endereços IP públicos estáticos](#publicIP)

- Se utilizar o seu próprio servidor do Sistema de Nomede Domínio (DNS) na rede virtual, consulte a [configuração do servidor DNS](#dns_server). 

- Se utilizar um grupo de segurança de rede (NSG) na subnet, consulte [A Configuração de um NSG](#nsg). 

- Se utilizar o Azure ExpressRoute ou uma rota definida pelo utilizador (UDR), consulte [use Azure ExpressRoute ou um UDR](#route). 

- Certifique-se de que o grupo de recursos da rede virtual (ou o grupo público de recursos ip addresss se trouxer os seus próprios endereços IP públicos) pode criar e eliminar certos recursos da rede Azure. Para mais informações, consulte [Configurar o grupo de recursos](#resource-group). 

- Se personalizar o seu IR Azure-SSIS conforme descrito na [configuração personalizada para o Ir Azure-SSIS,](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)os seus nós de IR Azure-SSIS receberão endereços IP privados de uma gama pré-definida de 172.16.0.0 a 172.31.255.255. Por isso, certifique-se de que as gamas de endereços IP privados das suas redes virtuais ou no local não colidem com esta gama.

Este diagrama mostra as ligações necessárias para o seu IR Azure-SSIS:

![Runtime de integração Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a>Configurar permissões

O utilizador que criar o IR Azure-SSIS deve ter as seguintes permissões:

- Se estiver a aderir ao seu SSIS IR a uma rede virtual do Gestor de Recursos Azure, tem duas opções:

  - Utilize a função de Colaborador de Rede incorporada. Este papel vem com a permissão _Microsoft.Network/,\* _ que tem um âmbito muito maior do que o necessário.

  - Crie uma função personalizada que inclua apenas a necessária _Microsoft.Network/virtualNetworks/\*/join/action_ permission. Se também pretende trazer os seus próprios endereços IP públicos para o Ir Azure-SSIS ao mesmo tempo que o junta a uma rede virtual do Gestor de Recursos Do Azure, por favor, inclua também a _Microsoft.Network/publicIPAddresss/*/join/action_ permission no papel.

- Se você está se juntando ao seu SSIS IR para uma rede virtual clássica, recomendamos que use a função de Colaborador de Máquina Virtual Clássica incorporada. Caso contrário, tem de definir um papel personalizado que inclua a permissão para aderir à rede virtual.

### <a name="select-the-subnet"></a><a name="subnet"></a>Selecione a sub-rede

À medida que escolhe uma sub-rede: 

- Não selecione a GatewaySubnet para implantar um IR Azure-SSIS. É dedicado a portais de rede virtuais. 

- Certifique-se de que a sub-rede selecionada tem espaço de endereço disponível suficiente para a utilização do IR Azure-SSIS. Deixe os endereços IP disponíveis durante pelo menos duas vezes o número do nó IR. O Azure reserva alguns endereços IP dentro de cada subnet. Estes endereços não podem ser usados. Os primeiros e últimos endereços IP das subnets estão reservados para conformidade com o protocolo, e mais três endereços são utilizados para os serviços Azure. Para mais informações, consulte [Existem restrições à utilização de endereços IP dentro destas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Não utilize uma sub-rede que seja exclusivamente ocupada por outros serviços Azure (por exemplo, sqL Database gerido por exemplo, App Service, e assim por diante). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Selecione os endereços IP públicos estáticos

Se pretender levar os seus próprios endereços IP públicos estáticos para o Ir Azure-SSIS ao juntar-se a uma rede virtual, certifique-se de que satisfazem os seguintes requisitos:

- Devem ser fornecidos exatamente dois não utilizados que ainda não estejam associados a outros recursos do Azure. O extra será usado quando atualizarmos periodicamente o seu IR Azure-SSIS. Note que um endereço IP público não pode ser partilhado entre os seus IRs Ativos Azure-SSIS.

- Ambos devem ser estáticos do tipo padrão. Consulte as [SKUs do Endereço IP Público](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) para mais detalhes.

- Ambos deviam ter um nome DNS. Se não forneceu um nome DNS ao criá-los, pode fazê-lo no portal Azure.

![Runtime de integração Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Eles e a rede virtual devem estar sob a mesma subscrição e na mesma região.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a>Configurar o servidor DNS 
Se necessitar de utilizar o seu próprio servidor DNS numa rede virtual a que o seu Ir Azure-SSIS se encontra a resolver o seu `<your storage account>.blob.core.windows.net`nome de anfitrião privado, certifique-se de que também pode resolver nomes globais de anfitriões azure (por exemplo, uma bolha de Armazenamento Azure chamada). 

Uma abordagem recomendada está abaixo: 

-   Configure o DNS personalizado para encaminhar pedidos para o DNS Azure. Pode encaminhar os registos DNS não resolvidos para o endereço IP dos resolvers recursivos Do Azure (168.63.129.16) no seu próprio servidor DNS. 

Para mais informações, consulte a [resolução de nomes que utiliza o seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Utilize um nome de domínio totalmente qualificado (FQDN) para o `<your_private_server>.contoso.com` seu `<your_private_server>`nome de anfitrião privado, por exemplo, utilização em vez de , uma vez que o Azure-SSIS IR não anexaautomaticamente o seu próprio sufixo DNS.

### <a name="set-up-an-nsg"></a><a name="nsg"></a>Criar um NSG
Se necessitar de implementar um NSG para a sub-rede utilizada pelo seu IR Azure-SSIS, permita o tráfego de entrada e saída através das seguintes portas: 

-   **Requisito de entrada do IR Azure-SSIS**

| Direção | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
|---|---|---|---|---|---|---|
| Entrada | TCP | Gestão de BatchNode | * | VirtualNetwork | 29876, 29877 (se aderir ao IR a uma rede virtual do Gestor de Recursos) <br/><br/>10100, 20100, 30100 (se aderir ao IR a uma rede virtual clássica)| O serviço Data Factory utiliza estas portas para comunicar com os nódosos do seu Ir Azure-SSIS na rede virtual. <br/><br/> Quer crie ou não um NSG de nível de sub-rede, a Data Factory configura sempre um NSG ao nível dos cartões de interface de rede (NICs) ligados às máquinas virtuais que acolhem o IR Azure-SSIS. Apenas o tráfego de entrada a partir de endereços IP da Fábrica de Dados nas portas especificadas é permitido por esse NSG de nível NIC. Mesmo que abra estas portas ao tráfego de internet ao nível da subnet, o tráfego a partir de endereços IP que não são endereços IP da Fábrica de Dados está bloqueado ao nível nic. |
| Entrada | TCP | Corpnetsaw | * | VirtualNetwork | 3389 | (Opcional) Esta regra só é necessária quando o apoiante da Microsoft pede ao cliente para abrir para resolução avançada de problemas, e pode ser fechado logo após resolução de problemas. A etiqueta de serviço **CorpNetSaw** permite apenas postos de trabalho de acesso seguros na rede corporativa da Microsoft para utilizar o ambiente de trabalho remoto. E esta etiqueta de serviço não pode ser selecionada a partir do portal e só está disponível via Azure PowerShell ou Azure CLI. <br/><br/> Ao nível NIC NSG, a porta 3389 está aberta por padrão e permitimos controlar a porta 3389 ao nível da sub-rede NSG, enquanto o Azure-SSIS IR desautorizado é a saída por defeito na regra da firewall das janelas em cada nó de IR para proteção. |
||||||||

-   **Requisito de saída do IR Azure-SSIS**

| Direção | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Intervalo de portas de destino | Comentários |
|---|---|---|---|---|---|---|
| Saída | TCP | VirtualNetwork | * | AzureCloud | 443 | Os nós do seu Ir Azure-SSIS na rede virtual utilizam este porto para aceder aos serviços Azure, como o Azure Storage e o Azure Event Hubs. |
| Saída | TCP | VirtualNetwork | * | Internet | 80 | (Opcional) Os nós do seu Ir Azure-SSIS na rede virtual utilizam esta porta para descarregar uma lista de revogação de certificados a partir da internet. Se bloquear este tráfego, poderá sentir uma desvalorização de desempenho quando iniciar o IR e perder capacidade para verificar a lista de revogação do certificado para uso do certificado. Se quiser reduzir ainda mais o destino a certas FQDNs, consulte a **secção Use Azure ExpressRoute ou UDR**|
| Saída | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | (Opcional) Esta regra só é exigida quando os nós do seu IR Azure-SSIS na rede virtual acedem a um SSISDB hospedado pelo seu servidor de base de dados SQL. Se a sua política de ligação ao servidor sQL Database estiver definida para **Proxy** em vez de **Redirecionar,** apenas é necessária a porta 1433. <br/><br/> Esta regra de segurança de saída não é aplicável a um SSISDB hospedado pela sua instância gerida na rede virtual ou servidor de Base de Dados Azure configurado com ponto final privado. |
| Saída | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (Opcional) Esta regra só é exigida quando os nós do seu IR Azure-SSIS na rede virtual acedem a um SSISDB hospedado pela sua instância gerida na rede virtual ou servidor de base de dados Azure configurado com ponto final privado. Se a sua política de ligação ao servidor sQL Database estiver definida para **Proxy** em vez de **Redirecionar,** apenas é necessária a porta 1433. |
| Saída | TCP | VirtualNetwork | * | Armazenamento | 445 | (Opcional) Esta regra só é necessária quando pretender executar o pacote SSIS armazenado em Ficheiros Azure. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a>Utilize azure ExpressRoute ou UDR
Se pretender inspecionar o tráfego de saída do Azure-SSIS IR, pode encaminhar o tráfego iniciado do Azure-SSIS IR para o aparelho firewall no local através do túnel de força [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) (publicidade a uma rota BGP, 0.0.0.0/0, para a rede virtual) ou para a Rede Virtual Appliance (NVA) como firewall ou [Azure Firewall](https://docs.microsoft.com/azure/firewall/) via [UDRs](../virtual-network/virtual-networks-udr-overview.md). 

![Cenário nVA para O IR Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Tens de fazer abaixo das coisas para que todo o cenário funcione.
   -   O tráfego de entrada entre os serviços de gestão do Lote Azure e o IR Azure-SSIS não pode ser encaminhado através de um aparelho de firewall.
   -   O aparelho de firewall deve permitir o tráfego de saída exigido pelo IR Azure-SSIS.

O tráfego de entrada entre os serviços de gestão do Lote Azure e o IR Azure-SSIS não pode ser encaminhado para o aparelho de firewall, caso contrário o tráfego será interrompido devido a um problema de encaminhamento assimétrico. As rotas devem ser definidas para o tráfego de entrada, de modo a que o tráfego possa responder da mesma forma que entrou. Você pode definir UDRs específicos para direcionar o tráfego entre os serviços de gestão do Lote Azure e o Ir Azure-SSIS com o próximo tipo de lúpulo como **Internet**.

Por exemplo, se o seu IR `UK South` Azure-SSIS estiver localizado e pretender inspecionar o tráfego de saída através `BatchNodeManagement.UKSouth` do Azure Firewall, obterá primeiro uma lista de etiquetas de serviço IP a partir do link de [descarregamento](https://www.microsoft.com/download/details.aspx?id=56519) de gama IP das etiquetas de serviço ou através da [API de identificação](https://aka.ms/discoveryapi)de etiquetas de serviço . Em seguida, aplique os seguintes UDRs de rotas de gama IP relacionadas com o próximo tipo de lúpulo como **Internet,** juntamente com a rota 0.0.0.0/0 com o próximo tipo de lúpulo como **aparelho virtual**.

![Definições uDR do lote azure](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Esta abordagem incorre num custo adicional de manutenção. Verifique regularmente a gama IP e adicione novas gamas ip no seu UDR para evitar quebrar o IR Azure-SSIS. Recomendamos verificar mensalmente a gama IP porque quando o novo IP aparecer na etiqueta de serviço, o IP levará mais um mês a entrar em vigor. 

Para facilitar a configuração das regras uDR, pode executar o seguinte script powershell para adicionar regras uDR para serviços de gestão de Lotes Azure:
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

Para que o aparelho de firewall permita o tráfego de saída, é necessário permitir a saída para as portas abaixo, tal como o requisito nas regras de saída do NSG.
-   Porto 443 com destino como serviços Azure Cloud.

    Se utilizar o Firewall Azure, pode especificar a regra da rede com a Etiqueta de Serviço AzureCloud. Para firewall dos outros tipos, você pode simplesmente permitir o destino como todo para a porta 443 ou permitir abaixo fQDNs com base no tipo do seu ambiente Azure:

    | Ambiente Azure | Pontos Finais                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Público      | <ul><li><b>Fábrica de Dados Azure (Gestão)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Armazenamento Azure (Gestão)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Registo de contentores azure (configuração personalizada)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>Centro de Eventos (Madeireiro)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Serviço de registo da Microsoft (Utilização Interna)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Fábrica de Dados Azure (Gestão)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Armazenamento Azure (Gestão)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Registo de contentores azure (configuração personalizada)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>Centro de Eventos (Madeireiro)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Serviço de registo da Microsoft (Utilização Interna)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Fábrica de Dados Azure (Gestão)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Armazenamento Azure (Gestão)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Registo de contentores azure (configuração personalizada)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>Centro de Eventos (Madeireiro)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>Serviço de registo da Microsoft (Utilização Interna)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Quanto às FQDNs de Armazenamento Azure, Registo de Contentores Azure e Centro de Eventos, também pode optar por ativar os seguintes pontos finais de serviço para a sua rede virtual, de modo a que o tráfego de rede para estes pontos finais passe pela rede de espinha dorsal Azure em vez de ser encaminhado para o seu aparelho de firewall:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   Porta 80 com destino como sites de descarregamento CRL.

    Você deve permitir abaixo fQDNs que são usados como CRL (Lista de Revogação de Certificados) sites de descarregamento de certificados para finalidade de gestão do IR Azure-SSIS:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Se estiver a usar certificados com CRL diferente, é sugerido que os inclua também. Pode ler isto para entender mais sobre a Lista de Revogação de [Certificados](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Se você não permitir este tráfego, você pode experimentar uma desvalorização de desempenho quando iniciar O IR Azure-SSIS e perder capacidade para verificar a lista de revogação do certificado para uso do certificado, o que não é recomendado do ponto de vista de segurança.

-   Porta 1433, 11000-11999 com destino como Azure SQL (apenas necessário quando os nós do seu IR Azure-SSIS na rede virtual acedem a um SSISDB hospedado pelo seu servidor de base de dados SQL).

    Se utilizar o Firewall Azure, pode especificar a regra da rede com a Etiqueta de Serviço Azure SQL, caso contrário poderá permitir o destino como url sql azul específico no aparelho de firewall.

-   Porta 445 com destino como Armazenamento Azure (apenas necessário quando executa pacote SSIS armazenado em Ficheiros Azure).

    Se utilizar o Firewall Azure, pode especificar a regra da rede com a Etiqueta do Serviço de Armazenamento, caso contrário poderá permitir o destino como url específico de armazenamento de ficheiros azure no aparelho firewall.

> [!NOTE]
> Para o Azure SQL e armazenamento, se configurar pontos finais de serviço da Rede Virtual na sua subnet, o tráfego entre o Azure-SSIS IR e o Azure SQL na mesma região \ O Armazenamento Azure na mesma região ou região emparelhada será encaminhado diretamente para a rede de espinhas do Microsoft Azure em vez do seu aparelho de firewall.

Se não precisar de capacidade para inspecionar o tráfego de saída do IR Azure-SSIS, pode simplesmente aplicar a rota para forçar todo o tráfego para o próximo tipo de **internet**do tipo hop:

-   Num cenário Azure ExpressRoute, pode aplicar uma rota de 0.0.0.0/0 com o próximo tipo de lúpulo como **Internet** na subnet que acolhe o IR Azure-SSIS. 
-   Num cenário de NVA, pode modificar a rota de 0.0.0.0/0 existente aplicada na subnet que acolhe o IR Azure-SSIS do próximo tipo de lúpulo como **aparelho virtual** para a **Internet**.

![Adicione uma rota](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Especificar rota com o próximo tipo de **internet** do tipo hop não significa que todo o tráfego passará pela Internet. Enquanto o endereço de destino for para um dos serviços do Azure, o Azure encaminha o tráfego diretamente para o serviço sobre a rede de espinha dorsal do Azure, em vez de encaminhar o tráfego para a Internet.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a>Criar o grupo de recursos

O IR Azure-SSIS necessita de criar certos recursos de rede no mesmo grupo de recursos que a rede virtual. Estes recursos incluem:
- Um equilibrador de carga Azure, com o nome * \<Guid>-azurebatch-cloudserviceloadbalancer*.
- Um endereço IP público azure, com o nome * \<Guid>-azurebatch-cloudservicepublicip*.
- Um grupo de segurança de trabalho de rede, com o nome * \<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Agora pode trazer os seus próprios endereços IP públicos estáticos para o IR Azure-SSIS. Neste cenário, criaremos apenas o balanceador de carga Azure e o grupo de segurança da rede sob o mesmo grupo de recursos que os seus endereços IP públicos estáticos em vez da rede virtual.

Esses recursos serão criados quando o seu IR Azure-SSIS começar. Serão apagados quando o seu IR Azure-SSIS parar. Se trouxer os seus próprios endereços IP públicos estáticos para o Ir Azure-SSIS, os seus próprios endereços IP públicos estáticos não serão eliminados quando o seu IR Azure-SSIS parar. Para evitar que o seu IR Azure-SSIS pare, não reutilize estes recursos de rede nos seus outros recursos.

Certifique-se de que não tem nenhum bloqueio de recursos no grupo/subscrição de recursos a que pertencem os endereços IP públicos estáticos/rede virtual. Se configurar um bloqueio de leitura/exclusão, iniciar e parar o seu IR Azure-SSIS falhará, ou deixará de responder.

Certifique-se de que não tem uma política Azure que impeça a criação dos seguintes recursos ao abrigo do grupo/subscrição de recursos a que pertencem os endereços IP públicos estáticos da rede virtual/seus endereços IP públicos estáticos: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresss 

Certifique-se de que a quota de recursos da sua subscrição é suficiente para os três recursos de rede acima. Especificamente, para cada IR Azure-SSIS criado em rede virtual, você precisa reservar duas quotas gratuitas para cada um dos três recursos de rede acima. A quota extra será utilizada quando atualizarmos periodicamente o seu IR Azure-SSIS.

### <a name="faq"></a><a name="faq"></a>Perguntas Frequentes

- Como posso proteger o endereço IP público exposto no meu IR Azure-SSIS para ligação de entrada? É possível remover o endereço IP público?
 
  Neste momento, será criado automaticamente um endereço IP público quando o seu Ir Azure-SSIS se juntar a uma rede virtual. Temos um NSG de nível NIC para permitir que apenas os serviços de gestão do Lote Azure se liguem ao seu IR Azure-SSIS. Também pode especificar um NSG de nível subnet para proteção de entrada.

  Se não quiser que nenhum endereço IP público seja exposto, considere [configurar um IR auto-hospedado como procuração para o seu IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) em vez de se juntar ao seu IR Azure-SSIS a uma rede virtual, se isso se aplicar ao seu cenário.
 
- Posso adicionar o endereço IP público do meu IR Azure-SSIS à lista de autorizações da firewall para as minhas fontes de dados?

  Agora pode trazer os seus próprios endereços IP públicos estáticos para o IR Azure-SSIS. Neste caso, pode adicionar os seus endereços IP à lista de autorizações da firewall para obter as suas fontes de dados. Também pode considerar outras opções abaixo para garantir o acesso de dados do seu IR Azure-SSIS dependendo do seu cenário:

  - Se a sua fonte de dados estiver no local, depois de ligar uma rede virtual à sua rede no local e de se juntar ao seu IR Azure-SSIS à subnet de rede virtual, pode adicionar a gama de endereços IP privado dessa sub-rede à lista de suporte da firewall para a sua fonte de dados.
  - Se a sua fonte de dados for um serviço Azure que suporta pontos finais de serviço de rede virtual, pode configurar um ponto final de serviço de rede virtual na sua subnet de rede virtual e juntar-se ao seu IR Azure-SSIS a essa subnet. Em seguida, pode adicionar uma regra de rede virtual com essa sub-rede à firewall para a sua fonte de dados.
  - Se a sua fonte de dados for um serviço de nuvem não Azure, pode utilizar um UDR para direcionar o tráfego de saída do seu Azure-SSIS IR para um Firewall NVA/Azure através de um endereço IP público estático. Em seguida, pode adicionar o endereço IP público estático da sua Firewall NVA/Azure à lista de autorizações da firewall para a sua fonte de dados.
  - Se nenhuma das opções acima atende às suas necessidades, considere [configurar um IR auto-hospedado como procuração para o seu IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Em seguida, pode adicionar o endereço IP público estático da máquina que acolhe o seu IV auto-hospedado na lista de autorizações da firewall para a sua fonte de dados.

- Por que preciso fornecer dois endereços públicos estáticos se quero trazer o meu para o IR Azure-SSIS?

  O IR Azure-SSIS é automaticamente atualizado regularmente. Novos nódosos são criados durante a atualização e os antigos serão eliminados. No entanto, para evitar o tempo de inatividade, os nódosos antigos não serão apagados até que os novos estejam prontos. Assim, o seu primeiro endereço IP público estático utilizado pelos nódosos antigos não pode ser lançado imediatamente e precisamos do seu segundo endereço IP público estático para criar os novos nós.

- Trouxe os meus próprios endereços IP públicos estáticos para o IR Azure-SSIS, mas porque é que ainda não consegue aceder às minhas fontes de dados?

  - Confirme que os dois endereços IP públicos estáticos são adicionados à lista de autorizações da firewall para as suas fontes de dados. Cada vez que o seu IR Azure-SSIS é atualizado, o seu endereço IP público estático é trocado entre os dois trazidos por si. Se adicionar apenas um deles à lista de admissões, o acesso aos dados do seu IR Azure-SSIS será quebrado após a sua atualização.
  - Se a sua fonte de dados for um serviço Azure, verifique se os configurou com pontos finais de serviço de rede virtual. Se for esse o caso, o tráfego do Ir Azure-SSIS para a sua fonte de dados passará a utilizar os endereços IP privados geridos pelos serviços Azure e adicionar os seus próprios endereços IP públicos estáticos à lista de autorizações da firewall para a sua fonte de dados não produzirá efeito.

## <a name="azure-portal-data-factory-ui"></a>Azure portal (Data Factory UI)

Esta secção mostra-lhe como se juntar a um IR Azure-SSIS existente a uma rede virtual (classic ou Azure Resource Manager) utilizando o portal Azure e data Factory UI. 

Antes de juntar o seu IR Azure-SSIS à rede virtual, precisa de configurar corretamente a rede virtual. Siga os passos na secção que se aplica ao seu tipo de rede virtual (classic ou Azure Resource Manager). Em seguida, siga os passos na terceira secção para se juntar ao seu IR Azure-SSIS à rede virtual. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Configure uma rede virtual do Gestor de Recursos Azure

Utilize o portal para configurar uma rede virtual do Gestor de Recursos Azure antes de tentar aderir a um IR Azure-SSIS.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas estes navegadores web suportam a UI da Fábrica de Dados. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Selecione **Mais serviços**. Filtrar e selecionar **redes virtuais**. 

1. Filtre e selecione a sua rede virtual na lista. 

1. Na página de **rede Virtual,** selecione **Propriedades**. 

1. Selecione o botão de cópia para **ID DE RECURSOS** para copiar o ID de recurso para a rede virtual para a área de reparação. Guarde o ID da pasta no OneNote ou num ficheiro. 

1. No menu esquerdo, selecione **Subnets**. Certifique-se de que o número de endereços disponíveis é maior do que os nós do seu IR Azure-SSIS. 

1. Verifique se o fornecedor de Lote Azure está registado na subscrição Azure que possui a rede virtual. Ou registe o fornecedor do Lote Azure. Se já tem uma conta Azure Batch na sua subscrição, a sua subscrição está registada para o Lote Azure. (Se criar o IR Azure-SSIS no portal data Factory, o fornecedor de Lotes Azure está automaticamente registado para si.) 

   1. No portal Azure, no menu esquerdo, selecione **Subscrições**. 

   1. Selecione a sua subscrição. 

   1. À esquerda, selecione **fornecedores**de Recursos , e confirme que a **Microsoft.Batch** é um fornecedor registado. 

   ![Confirmação do estado "Registado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se não vir o **Microsoft.Batch** na lista, para o registar, [crie uma conta Azure Batch vazia](../batch/batch-account-create-portal.md) na sua subscrição. Pode apagá-lo mais tarde. 

### <a name="configure-a-classic-virtual-network"></a>Configure uma rede virtual clássica

Utilize o portal para configurar uma rede virtual clássica antes de tentar aderir a um IR Azure-SSIS. 

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas estes navegadores web suportam a UI da Fábrica de Dados. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Selecione **Mais serviços**. Filtrar e selecionar **redes virtuais (clássica)**. 

1. Filtre e selecione a sua rede virtual na lista. 

1. Na página da **rede Virtual (clássica),** selecione **Propriedades**. 

   ![ID de recursos de rede virtual clássico](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Selecione o botão de cópia para **ID DE RECURSOS** para copiar o ID de recurso para a rede clássica para a área de reparação. Guarde o ID da pasta no OneNote ou num ficheiro. 

1. No menu esquerdo, selecione **Subnets**. Certifique-se de que o número de endereços disponíveis é maior do que os nós do seu IR Azure-SSIS. 

   ![Número de endereços disponíveis na rede virtual](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Junte-se ao **MicrosoftAzureBatch** ao papel clássico de colaborador de **máquinavirtual** para a rede virtual. 

   1. No menu esquerdo, selecione **o controlo de acesso (IAM)** e selecione o separador de **tarefas role.** 

       ![Botões "Controle de acesso" e "Adicionar"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Selecione **Adicionar atribuição de função**.

   1. Na página de atribuição de **funções Add,** para **Role,** selecione **Classic Virtual Machine Contributor**. Na caixa **Select,** pasta **ddbf3205-c6bd-46ae-8127-60eb93363864**, e, em seguida, selecione **Microsoft Azure Batch** na lista de resultados de pesquisa. 

       ![Resultados da pesquisa na página "Adicionar atribuição de papéis"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Selecione **Guardar** para guardar as definições e fechar a página. 

       ![Guardar as definições de acesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Confirme que vê o **Microsoft Azure Batch** na lista de contribuintes. 

       ![Confirme acesso ao Lote Azure](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verifique se o fornecedor de Lote Azure está registado na subscrição Azure que possui a rede virtual. Ou registe o fornecedor do Lote Azure. Se já tem uma conta Azure Batch na sua subscrição, a sua subscrição está registada para o Lote Azure. (Se criar o IR Azure-SSIS no portal data Factory, o fornecedor de Lotes Azure está automaticamente registado para si.) 

   1. No portal Azure, no menu esquerdo, selecione **Subscrições**. 

   1. Selecione a sua subscrição. 

   1. À esquerda, selecione **fornecedores**de Recursos , e confirme que a **Microsoft.Batch** é um fornecedor registado. 

   ![Confirmação do estado "Registado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se não vir o **Microsoft.Batch** na lista, para o registar, [crie uma conta Azure Batch vazia](../batch/batch-account-create-portal.md) na sua subscrição. Pode apagá-lo mais tarde. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Junte-se ao IR Azure-SSIS a uma rede virtual

Depois de configurar a sua rede virtual Do Gestor de Recursos Azure ou a rede virtual clássica, pode juntar-se ao IR Azure-SSIS à rede virtual:

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas estes navegadores web suportam a UI da Fábrica de Dados. 

1. No [portal Azure,](https://portal.azure.com)no menu esquerdo, selecione fábricas de **dados.** Se não vir **as fábricas de dados** no menu, selecione Mais **serviços,** e depois na secção **INTELLIGENCE + ANALYTICS,** selecione **Fábricas**de Dados . 

   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecione a sua fábrica de dados com o IR Azure-SSIS na lista. Veja a página inicial da sua fábrica de dados. Selecione o azulejo **monitor de & autor.** Vê a UI da Fábrica de Dados num separador. 

   ![Home page da fábrica de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Na UI da Fábrica de Dados, mude para o separador **Editar,** selecione **Ligações**e mude para o separador Tempos de **Integração.** 

   ![Separador "Tempos de integração"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se o seu IR Azure-SSIS estiver em execução, na lista de Tempos de **Integração,** na coluna **Ações,** selecione o botão **Stop** para o seu IR Azure-SSIS. Não pode editar o seu IR Azure-SSIS até o parar. 

   ![Parar o IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na lista **De Integração Runtimes,** na coluna **Ações,** selecione o botão **Editar** para o seu IR Azure-SSIS. 

   ![Editar o tempo de execução da integração](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. No painel de configuração do tempo de integração, avance através das **definições gerais** e das **definições SQL** selecionando o botão **Seguinte.** 

1. Na secção **Definições Avançadas:** 

   1. Selecione o Select a VNet para o seu Tempo de **Integração Azure-SSIS para aderir, permitir que a ADF crie certos recursos de rede e, opcionalmente, traga** a sua própria caixa de verificação de endereços IP públicos estáticos. 

   1. Para **Subscrição**, selecione a subscrição Azure que tem a sua rede virtual.

   1. Para **localização**, é selecionada a mesma localização do seu tempo de execução de integração.

   1. Para **Tipo**, selecione o tipo da sua rede virtual: classic ou Gestor de Recursos Azure. Recomendamos que selecione uma rede virtual do Gestor de Recursos Azure, porque as redes virtuais clássicas serão depreciadas em breve.

   1. Para **Nome VNet,** selecione o nome da sua rede virtual. Deve ser o mesmo utilizado para o seu servidor de base de dados Azure SQL com pontos finais de serviço de rede virtual ou instância gerida com ponto final privado para hospedar o SSISDB. Ou deve ser a mesma ligada à sua rede no local. Caso contrário, pode ser qualquer rede virtual para trazer os seus próprios endereços IP públicos estáticos para o Ir Azure-SSIS.

   1. Para **nome sub-rede,** selecione o nome da subnet para a sua rede virtual. Deve ser o mesmo utilizado para o seu servidor de base de dados Azure SQL com pontos finais de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da usada para a sua instância gerida com ponto final privado para hospedar o SSISDB. Caso contrário, pode ser qualquer subrede para trazer os seus próprios endereços IP públicos estáticos para o Ir Azure-SSIS.

   1. Selecione os endereços IP públicos estáticos para a sua caixa de verificação de tempo de **integração Azure-SSIS** para escolher se pretende trazer os seus próprios endereços IP públicos estáticos para o Ir Azure-SSIS, para que possa permitir na firewall as suas fontes de dados.

      Se selecionar a caixa de verificação, complete os seguintes passos.

      1. Para **primeiro endereço IP público estático,** selecione o primeiro endereço IP público estático que satisfaz os [requisitos](#publicIP) para o seu IR Azure-SSIS. Se não tiver nenhum, clique em **Criar um novo** link para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que possa selecioná-los.
      
      1. Para **o segundo endereço IP público estático,** selecione o segundo endereço IP público estático que [satisfaça os requisitos](#publicIP) para o seu IR Azure-SSIS. Se não tiver nenhum, clique em **Criar um novo** link para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que possa selecioná-los.

   1. Selecione **validação VNet**. Se a validação for bem sucedida, selecione **Continue**. 

   ![Definições avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Na secção **Resumo,** reveja todas as definições para o seu IR Azure-SSIS. Em seguida, selecione **Atualizar**.

1. Inicie o seu IR Azure-SSIS selecionando o botão **Iniciar** na coluna **Ações** para o seu IR Azure-SSIS. Leva cerca de 20 a 30 minutos para iniciar o IR Azure-SSIS que se junta a uma rede virtual. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Definir as variáveis

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Antes de poder aderir ao seu IR Azure-SSIS a uma rede virtual, precisa de configurar a rede virtual. Para configurar automaticamente permissões e configurações de rede virtual para que o seu IR Azure-SSIS se junte à rede virtual, adicione o seguinte script:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Crie um IR Azure-SSIS e junte-o a uma rede virtual

Pode criar um IR Azure-SSIS e juntá-lo a uma rede virtual ao mesmo tempo. Para obter o script completo e instruções, consulte [Criar um IR Azure-SSIS](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Junte-se a um IR Azure-SSIS existente para uma rede virtual

O artigo [Create a Azure-SSIS IR](create-azure-ssis-integration-runtime.md) mostra-lhe como criar um IR Azure-SSIS e juntá-lo a uma rede virtual no mesmo script. Se já tem um IR Azure-SSIS, siga estes passos para o juntar à rede virtual: 
1. Pare o IR Azure-SSIS. 
1. Configure o IR Azure-SSIS para aderir à rede virtual. 
1. Inicie o IR Azure-SSIS. 

### <a name="stop-the-azure-ssis-ir"></a>Parar o IR Azure-SSIS

Tem de parar o IR Azure-SSIS antes de poder aderir a uma rede virtual. Este comando liberta todos os seus nós e para de faturar:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configure as definições de rede virtual para o IR Azure-SSIS aderir

Para configurar as definições para a rede virtual a que o Azure-SSIS irá aderir, utilize este script: 

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

### <a name="configure-the-azure-ssis-ir"></a>Configure o IR Azure-SSIS

Para se juntar ao seu IR Azure-SSIS a uma rede virtual, execute o `Set-AzDataFactoryV2IntegrationRuntime` comando: 

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

### <a name="start-the-azure-ssis-ir"></a>Inicie o IR Azure-SSIS

Para iniciar o IR Azure-SSIS, executar o seguinte comando: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Este comando leva 20 a 30 minutos para terminar.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Ir Azure-SSIS, consulte os seguintes artigos: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceptuais gerais sobre IRs, incluindo o Ir Azure-SSIS. 
- [Tutorial: Implementar pacotes SSIS para o Azure](tutorial-create-azure-ssis-runtime-portal.md). Este tutorial fornece instruções passo a passo para criar o seu IR Azure-SSIS. Utiliza a Base de Dados Azure SQL para acolher o catálogo SSIS. 
- [Criar um IR Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande-se no tutorial. Fornece instruções sobre a utilização da Base de Dados Azure SQL com pontos finais de serviço de rede virtual ou instância gerida numa rede virtual para acolher o catálogo SSIS. Mostra como se juntar ao seu IR Azure-SSIS a uma rede virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra-lhe como obter informações sobre o seu IR Azure-SSIS. Fornece descrições de estado para as informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, começar ou apagar o seu IR Azure-SSIS. Também mostra como escalar o seu IR Azure-SSIS adicionando nódoos.