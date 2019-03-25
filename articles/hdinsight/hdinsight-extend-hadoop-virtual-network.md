---
title: Expandir o HDInsight com a rede Virtual - Azure
description: Saiba como utilizar a rede Virtual do Azure para ligar o HDInsight para outros recursos de nuvem ou recursos no seu datacenter
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: e7d264e5d5d289117dbc30b71e317506006e1f30
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401589"
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Expandir HDInsight do Azure com uma rede Virtual do Azure

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

> [!IMPORTANT]  
> Depois de 28 de Fevereiro de 2019, os recursos de rede (por exemplo, NICs, LBs, etc.) nos clusters novos criados numa VNET serão aprovisionados no mesmo grupo de recursos de cluster de HDInsight. Anteriormente, esses recursos tenham sido aprovisionados no grupo de recursos VNET. Não há nenhuma alteração para os clusters em execução atuais e os clusters criados sem uma VNET.

Saiba como utilizar o HDInsight com um [rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). Utilizar uma rede Virtual do Azure permite que os seguintes cenários:

* Ligar ao HDInsight diretamente a partir de uma rede no local.

* A ligar o HDInsight aos dados armazena numa rede Virtual do Azure.

* Acesse diretamente [Apache Hadoop](https://hadoop.apache.org/) serviços que não estão disponíveis publicamente na Internet. Por exemplo, [Apache Kafka](https://kafka.apache.org/) APIs ou o [Apache HBase](https://hbase.apache.org/) API de Java.

> [!WARNING]  
> As informações neste documento requerem uma compreensão das redes de TCP/IP. Se não estiver familiarizado com a rede de TCP/IP, deve de parceiros com alguém que é antes de efetuar as modificações a redes de produção.

> [!IMPORTANT]  
> Se estiver procurando por orientações passo a passo sobre como ligar o HDInsight para o seu local de rede com uma rede Virtual do Azure, consulte a [ligar o HDInsight à sua rede no local](connect-on-premises-network.md) documento.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="planning"></a>Planeamento

Seguem-se as perguntas que tem de responder quando planear instalar HDInsight numa rede virtual:

* Precisa instalar HDInsight numa rede virtual existente? Ou está a criar uma nova rede?

    Se estiver a utilizar uma rede virtual existente, terá de modificar a configuração de rede antes de instalar o HDInsight. Para obter mais informações, consulte a [adicionar HDInsight para uma rede virtual existente](#existingvnet) secção.

* Que pretende ligar a rede virtual que contém o HDInsight para outra rede virtual ou a sua rede no local?

    Trabalhar facilmente com os recursos através de redes, terá de criar um DNS personalizado e configurar o reencaminhamento de DNS. Para obter mais informações, consulte a [ligar várias redes](#multinet) secção.

* Deseja restringir/redirecionar tráfego de entrada ou saído para HDInsight?

    HDInsight tem de ter a sem restrições a comunicação com endereços IP específicos no Centro de dados do Azure. Também há várias portas que têm de ser permitidas através de firewalls para comunicação do cliente. Para obter mais informações, consulte a [controlar o tráfego da rede](#networktraffic) secção.

## <a id="existingvnet"></a>Adicionar o HDInsight para uma rede virtual existente

Utilize os passos nesta secção para saber como adicionar um novo HDInsight para uma rede Virtual do Azure existente.

> [!NOTE]  
> Não é possível adicionar um cluster do HDInsight existente numa rede virtual.

1. Está a utilizar um clássico ou modelo de implementação do Resource Manager para a rede virtual?

    HDInsight 3.4 e maior requer uma rede virtual do Resource Manager. Versões anteriores do HDInsight necessária uma rede virtual clássica.

    Se a sua rede existente é uma rede virtual clássica, tem de criar uma rede virtual do Resource Manager e, em seguida, ligar os dois. [Ligar VNets clássicas a novas VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Depois de combinado, instalado na rede de Gestor de recursos do HDInsight pode interagir com os recursos na rede clássica.

2. Utiliza o protocolo de túnel forçado? O túnel forçado é uma definição de sub-rede que força o tráfego de Internet de saída para um dispositivo para inspeção e registo. HDInsight não suporta o protocolo de túnel forçado. Remova o túnel forçado antes de implementar o HDInsight numa sub-rede existente, ou criar uma nova sub-rede com nenhuma túnel forçado para HDInsight.

3. Utilizar grupos de segurança de rede, as rotas definidas pelo utilizador ou aplicações virtuais de rede para restringir o tráfego para dentro ou fora da rede virtual?

    Como um serviço gerido, o HDInsight requer acesso sem restrições para vários endereços IP no Centro de dados do Azure. Para permitir a comunicação com estes endereços IP, atualize quaisquer grupos de segurança de rede existentes ou rotas definidas pelo utilizador.

    HDInsight hospeda vários serviços, utilizam uma variedade de portas. Bloqueia o tráfego para estas portas. Para obter uma lista de portas para permitir que através de firewalls de aplicação virtual, consulte a secção de segurança.

    Para localizar a sua configuração de segurança existente, utilize os seguintes comandos do Azure PowerShell ou CLI clássica do Azure:

    * Grupos de segurança de rede

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-Aznetworksecuritygroup -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network nsg list --resource-group $RESOURCEGROUP
        ```

        Para obter mais informações, consulte a [resolver problemas dos grupos de segurança de rede](../virtual-network/diagnose-network-traffic-filter-problem.md) documento.

        > [!IMPORTANT]  
        > Regras do grupo de segurança de rede são aplicadas por ordem, com base na prioridade da regra. A primeira regra que corresponde ao padrão de tráfego é aplicada, e não existem outros são aplicados para que o tráfego. Regras de ordem do mais permissivo ao menos permissiva. Para obter mais informações, consulte a [filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/security-overview.md) documento.

    * Rotas definidas pelo utilizador

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-Azroutetable -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network route-table list --resource-group $RESOURCEGROUP
        ```

        Para obter mais informações, consulte a [resolver problemas de rotas](../virtual-network/diagnose-network-routing-problem.md) documento.

4. Criar um cluster do HDInsight e selecione a rede Virtual do Azure durante a configuração. Utilize os passos nos seguintes documentos para compreender o processo de criação do cluster:

    * [Create HDInsight using the Azure portal (Criar o HDInsight com o portal do Azure)](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Create HDInsight using Azure PowerShell (Criar o HDInsight com o Azure PowerShell)](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Criar o HDInsight com a CLI clássica do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Criar o HDInsight com um modelo Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > A adição de HDInsight a uma rede virtual é um passo de configuração opcional. Certifique-se de que selecione a rede virtual, quando configurar o cluster.

## <a id="multinet"></a>Ligar a várias redes

O maior desafio com uma configuração de rede multi é a resolução de nomes entre as redes.

O Azure fornece resolução de nomes para os serviços do Azure que estão instalados numa rede virtual. Esta resolução de nomes interna permite que o HDInsight ligar para os seguintes recursos com um nome de domínio completamente qualificado (FQDN):

* Qualquer recurso que está disponível na internet. Por exemplo, microsoft.com, windowsupdate.com.

* Qualquer recurso que está na mesma rede Virtual do Azure, utilizando o __nome DNS interno__ do recurso. Por exemplo, ao utilizar a resolução de nome predefinido, seguem-se nomes DNS internos exemplo atribuídos a nós de trabalho do HDInsight:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Ambos estes nós podem comunicar diretamente entre si e com outros nós no HDInsight, utilizando nomes DNS internos.

A resolução de nomes padrão faz __não__ permitir que o HDInsight resolver os nomes de recursos em redes que estão associados à rede virtual. Por exemplo, é comum para associar a sua rede no local para a rede virtual. Com apenas a resolução de nome predefinido, HDInsight não é possível aceder a recursos na rede no local por nome. O oposto também se aplica, recursos na sua rede no local não é possível aceder a recursos na rede virtual por nome.

> [!WARNING]  
> Tem de criar o servidor DNS personalizado e configurar a rede virtual para utilizá-lo antes de criar o cluster do HDInsight.

Para habilitar a resolução de nome entre a rede virtual e os recursos em redes associados a um, tem de efetuar as seguintes ações:

1. Crie um servidor DNS personalizado na rede Virtual do Azure onde planeia instalar HDInsight.

2. Configure a rede virtual para utilizar o servidor DNS personalizado.

3. Encontre que o Azure atribuído o sufixo DNS para a rede virtual. Este valor é semelhante ao `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Para obter informações sobre como encontrar o sufixo DNS, consulte o [exemplo: DNS personalizado](#example-dns) secção.

4. Configure o reencaminhamento entre os servidores DNS. A configuração depende do tipo de rede remota.

   * Se a rede remota for uma rede no local, configure o DNS da seguinte forma:
        
     * __DNS personalizado__ (na rede virtual):

         * Encaminhar pedidos para o sufixo DNS da rede virtual para o resolvedor recursivo do Azure (168.63.129.16). O Azure trata solicitações de recursos na rede virtual

         * Reencaminhe todos os outros pedidos para o servidor DNS no local. O DNS no local processa todos os outros pedidos de resolução de nomes, até mesmo solicitações de recursos de internet, tais como o Microsoft.com.

     * __DNS locais__: Encaminhar pedidos para o sufixo DNS de rede virtual para o servidor DNS personalizado. O servidor DNS personalizado, em seguida, encaminha para o resolvedor recursivo do Azure.

       Este solicitações de configuração de rotas para completamente qualificado do nomes de domínio que contêm o sufixo DNS da rede virtual para o servidor DNS personalizado. Todos os outros pedidos (mesmo para endereços públicos da internet) são processados pelo servidor DNS no local.

   * Se a rede remota é outra rede Virtual do Azure, configure o DNS da seguinte forma:

     * __DNS personalizado__ (em cada rede virtual):

         * As solicitações para o sufixo DNS das redes virtuais são encaminhadas para os servidores DNS personalizados. O DNS em cada rede virtual é responsável por resolver recursos dentro de sua rede.

         * Reencaminhe todos os outros pedidos para o resolvedor recursivo do Azure. O resolvedor recursivo é responsável por resolver local e recursos da internet.

       O servidor DNS para cada rede reencaminha os pedidos para o outro, com base no sufixo DNS. Outras solicitações são resolvidas de utilizar o resolvedor recursivo do Azure.

     Para obter um exemplo de cada configuração, consulte o [exemplo: DNS personalizado](#example-dns) secção.

Para obter mais informações, consulte a [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) documento.

## <a name="directly-connect-to-apache-hadoop-services"></a>Ligar-se diretamente aos serviços do Apache Hadoop

Pode ligar ao cluster em https://CLUSTERNAME.azurehdinsight.net. Este endereço utiliza um IP público, que pode não estar acessível se tiver utilizado os NSGs para restringir o tráfego de entrada a partir da internet. Além disso, ao implementar o cluster numa VNet pode acessá-la usando o ponto final privado https://CLUSTERNAME-int.azurehdinsight.net. Este ponto final é resolvido para um IP privado na VNet para o acesso de cluster.

Para ligar ao Apache Ambari e outras páginas da web através da rede virtual, utilize os seguintes passos:

1. Para descobrir os nomes de domínio completamente qualificado interno (FQDN) de nós de cluster do HDInsight, utilize um dos seguintes métodos:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Na lista de nós devolvido, localizar o FQDN para os nós principais e utilizar os FQDNs para ligar a Ambari e outros serviços da web. Por exemplo, utilizar `http://<headnode-fqdn>:8080` para acessar o Ambari.

    > [!IMPORTANT]  
    > Alguns serviços alojados nos nós principais são apenas o Active Directory num nó de cada vez. Se tentar acessar um serviço num nó principal e retorna um erro 404, mude para o outro nó principal.

2. Para determinar o nó e a porta que um serviço está disponível em, veja a [portas utilizadas pelo serviços do Hadoop no HDInsight](./hdinsight-hadoop-port-settings-for-services.md) documento.

## <a id="networktraffic"></a> Controlar o tráfego de rede

Tráfego de rede num redes virtuais do Azure pode ser controlado através dos seguintes métodos:

* **Grupos de segurança de rede** (NSG) permitem-lhe filtrar o tráfego de entrada e saído para a rede. Para obter mais informações, consulte a [filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/security-overview.md) documento.

    > [!WARNING]  
    > HDInsight não suporta a restringir o tráfego de saída. Todo o tráfego de saída deve ser permitido.

* **Rotas definidas pelo utilizador** (UDR) definem a forma como o tráfego flui entre os recursos na rede. Para obter mais informações, consulte a [rotas definidas pelo utilizador e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md) documento.

* **Aplicações virtuais de rede** replicar a funcionalidade dos dispositivos, tais como firewalls e roteadores. Para obter mais informações, consulte a [aplicações de rede](https://azure.microsoft.com/solutions/network-appliances) documento.

Como um serviço gerido, HDInsight requer acesso sem restrições para o estado de funcionamento do HDinsight e dos serviços de gestão para o tráfego de entrada e saído da VNET. Ao utilizar NSGs e as UDRs, certifique-se de que esses serviços ainda consegue comunicar com o cluster do HDInsight.

### <a id="hdinsight-ip"></a> HDInsight com grupos de segurança de rede e as rotas definidas pelo utilizador

Se pretender utilizar **grupos de segurança de rede** ou **rotas definidas pelo utilizador** para controlar o tráfego de rede, execute as seguintes ações antes de instalar o HDInsight:

1. Identifica a região do Azure que pretende utilizar para o HDInsight.

2. Identifica os endereços IP necessários pelo HDInsight. Para obter mais informações, consulte a [endereços IP necessários pelo HDInsight](#hdinsight-ip) secção.

3. Criar ou modificar os grupos de segurança de rede ou rotas definidas pelo utilizador para a sub-rede que planeia instalar HDInsight numa.

    * __Grupos de segurança de rede__: permitir __entrada__ tráfego na porta __443__ de IP endereços. Isto irá garantir que os serviços de gestão de HDI contactar o cluster de VNET fora.
    * __Rotas definidas pelo utilizador__: Se planeia utilizar UDRs, criar uma rota para cada endereço IP e definir o __tipo de próximo salto__ ao __Internet__. Também deve permitir que qualquer outro tráfego de saída da VNET sem restrição. Por exemplo, pode encaminhar todos os outros tráfegos à sua do Azure rede ou firewall de aplicação virtual (alojado no Azure) para efeitos de monitorização, mas o tráfego de saída não deve ser bloqueado.

Para obter mais informações sobre grupos de segurança de rede ou rotas definidas pelo utilizador, consulte a seguinte documentação:

* [Grupo de segurança de rede](../virtual-network/security-overview.md)

* [Rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling-to-on-premise"></a>O túnel forçado para no local

O túnel forçado é uma configuração de encaminhamento definido pelo utilizador em que todo o tráfego de uma sub-rede é forçado a uma rede específica ou local, como a sua rede no local. HDInsight faz __não__ suporte o túnel forçado para as redes no local. Se estiver a utilizar a Firewall do Azure ou uma aplicação virtual de rede alojadas no Azure, pode utilizar as UDRs para encaminhar o tráfego para o mesmo para efeitos de monitorização e permitir todo tráfego de saída.

## <a id="hdinsight-ip"></a> Endereços IP necessários

> [!IMPORTANT]  
> Os serviços de estado de funcionamento e gestão do Azure tem de ser capazes de comunicar com o HDInsight. Se utilizar rotas definidas pelo utilizador ou grupos de segurança de rede, permita o tráfego dos endereços IP destes serviços alcançar o HDInsight.
>
> Se não utilizar rotas definidas pelo utilizador ou grupos de segurança de rede para controlar o tráfego, pode ignorar esta secção.

Se utilizar grupos de segurança de rede, tem de permitir o tráfego dos serviços do Azure de estado de funcionamento e gestão para chegar a clusters do HDInsight na porta 443. Também tem de permitir tráfego entre VMs dentro da sub-rede. Utilize os seguintes passos para encontrar os endereços IP que têm de ser permitidos:

1. Sempre têm de permitir tráfego a partir os seguintes endereços IP:

    | Endereço IP de origem | Porta de destino | Direção |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Entrada |
    | 23.99.5.239 | 443 | Entrada |
    | 168.61.48.131 | 443 | Entrada |
    | 138.91.141.162 | 443 | Entrada |

2. Se o seu cluster do HDInsight está das seguintes regiões, tem de permitir tráfego a partir de endereços IP listados para a região:

    > [!IMPORTANT]  
    > Se a região do Azure que está a utilizar não estiver listada, em seguida, apenas use os quatro endereços IP do passo 1.

    | País | Região | Endereços IP de origem permitidos | Permissão de porta de destino | Direção |
    | ---- | ---- | ---- | ---- | ----- |
    | Ásia | Ásia Oriental | 23.102.235.122</br>52.175.38.134 | 443 | Entrada |
    | &nbsp; | Sudeste Asiático | 13.76.245.160</br>13.76.136.249 | 443 | Entrada |
    | Austrália | Leste da Austrália | 104.210.84.115</br>13.75.152.195 | 443 | Entrada |
    | &nbsp; | Sudeste da Austrália | 13.77.2.56</br>13.77.2.94 | 443 | Entrada |
    | Brasil | Sul do Brasil | 191.235.84.104</br>191.235.87.113 | 443 | Entrada |
    | Canadá | Leste do Canadá | 52.229.127.96</br>52.229.123.172 | 443 | Entrada |
    | &nbsp; | Canadá Central | 52.228.37.66</br>52.228.45.222 | 443 | Entrada |
    | China | China Norte | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | 443 | Entrada |
    | &nbsp; | Leste da China | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | 443 | Entrada |
    | &nbsp; | Norte da China 2 | 40.73.37.141</br>40.73.38.172 | 443 | Entrada |
    | Europa | Europa do Norte | 52.164.210.96</br>13.74.153.132 | 443 | Entrada |
    | &nbsp; | Europa Ocidental| 52.166.243.90</br>52.174.36.244 | 443 | Entrada |
    | França | França Central| 20.188.39.64</br>40.89.157.135 | 443 | Entrada |
    | Alemanha | Alemanha Central | 51.4.146.68</br>51.4.146.80 | 443 | Entrada |
    | &nbsp; | Alemanha Nordeste | 51.5.150.132</br>51.5.144.101 | 443 | Entrada |
    | Índia | Índia Central | 52.172.153.209</br>52.172.152.49 | 443 | Entrada |
    | &nbsp; | Sul da Índia | 104.211.223.67<br/>104.211.216.210 | 443 | Entrada |
    | Japão | Leste do Japão | 13.78.125.90</br>13.78.89.60 | 443 | Entrada |
    | &nbsp; | Oeste do Japão | 40.74.125.69</br>138.91.29.150 | 443 | Entrada |
    | Coreia | Coreia do Sul Central | 52.231.39.142</br>52.231.36.209 | 433 | Entrada |
    | &nbsp; | Coreia do Sul | 52.231.203.16</br>52.231.205.214 | 443 | Entrada
    | Reino Unido | Reino Unido Oeste | 51.141.13.110</br>51.141.7.20 | 443 | Entrada |
    | &nbsp; | Reino Unido Sul | 51.140.47.39</br>51.140.52.16 | 443 | Entrada |
    | Estados Unidos | EUA Central | 13.67.223.215</br>40.86.83.253 | 443 | Entrada |
    | &nbsp; | EUA Leste | 13.82.225.233</br>40.71.175.99 | 443 | Entrada |
    | &nbsp; | EUA Centro-Norte | 157.56.8.38</br>157.55.213.99 | 443 | Entrada |
    | &nbsp; | EUA Centro-Oeste | 52.161.23.15</br>52.161.10.167 | 443 | Entrada |
    | &nbsp; | EUA Oeste | 13.64.254.98</br>23.101.196.19 | 443 | Entrada |
    | &nbsp; | EUA Oeste 2 | 52.175.211.210</br>52.175.222.222 | 443 | Entrada |

    Para obter informações sobre os endereços IP a utilizar para o Azure Government, consulte a [do Azure Government inteligência + análise](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) documento.

3. Também tem de permitir acesso a partir __168.63.129.16__. Este endereço é o resolvedor recursivo do Azure. Para obter mais informações, consulte a [resolução de nomes para VMs e a função instâncias](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) documento.

Para obter mais informações, consulte a [controlar o tráfego da rede](#networktraffic) secção.

Se estiver a utilizar routes(UDRs) definidas pelo utilizador, deve especificar uma rota e permitir o tráfego de saída da VNET para os IPs acima com o próximo salto definido como "Internet".
    
## <a id="hdinsight-ports"></a> Portas necessárias

Se pretender utilizar um **firewall** e aceder ao cluster a partir de fora em determinadas portas, poderá ser necessário permitir o tráfego dessas portas necessárias para o seu cenário. Por predefinição, nenhuma lista de permissões especial de portas é necessárias, desde que o tráfego de gestão do azure explicado na seção anterior recebe autorização para acessar o cluster na porta 443.

Para obter uma lista de portas para serviços específicos, consulte a [portas utilizadas pelos serviços do Apache Hadoop no HDInsight](hdinsight-hadoop-port-settings-for-services.md) documento.

Para obter mais informações sobre regras de firewall para aplicações virtuais, consulte a [cenário de aplicação virtual](../virtual-network/virtual-network-scenario-udr-gw-nva.md) documento.

## <a id="hdinsight-nsg"></a>Exemplo: grupos de segurança de rede com o HDInsight

Os exemplos nesta secção demonstram como criar regras de grupo que permitem que o HDInsight comunicar com os serviços de gestão do Azure de segurança de rede. Antes de utilizar os exemplos, ajuste os endereços IP para corresponder aos perfis para a região do Azure que está a utilizar. Pode encontrar estas informações no [HDInsight com grupos de segurança de rede e as rotas definidas pelo utilizador](#hdinsight-ip) secção.

### <a name="azure-resource-management-template"></a>Modelo de gestão de recursos do Azure

O modelo de gestão de recursos seguinte cria uma rede virtual que restringe o tráfego de entrada, mas permite que o tráfego dos endereços IP necessários pelo HDInsight. Este modelo também cria um cluster do HDInsight na rede virtual.

* [Implementar uma rede Virtual protegida do Azure e um cluster de Hadoop do HDInsight](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]  
> Altere os endereços IP utilizados neste exemplo, de acordo com a região do Azure que está a utilizar. Pode encontrar estas informações no [HDInsight com grupos de segurança de rede e as rotas definidas pelo utilizador](#hdinsight-ip) secção.

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o seguinte script do PowerShell para criar uma rede virtual que restringe o tráfego de entrada e permita o tráfego dos endereços IP para a região Europa do Norte.

> [!IMPORTANT]  
> Altere os endereços IP utilizados neste exemplo, de acordo com a região do Azure que está a utilizar. Pode encontrar estas informações no [HDInsight com grupos de segurança de rede e as rotas definidas pelo utilizador](#hdinsight-ip) secção.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `
# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

> [!IMPORTANT]  
> Este exemplo demonstra como adicionar regras para permitir o tráfego de entrada nos endereços IP necessários. Não contém uma regra para restringir o acesso de entrada de outras origens.
>
> O exemplo seguinte demonstra como ativar o acesso SSH da Internet:
>
> ```powershell
> Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-classic-cli"></a>CLI clássica do Azure

Utilize os seguintes passos para criar uma rede virtual que restringe o tráfego de entrada, mas permite que o tráfego dos endereços IP necessários pelo HDInsight.

1. Utilize o seguinte comando para criar um novo grupo de segurança de rede com o nome `hdisecure`. Substitua **RESOURCEGROUPNAME** com o grupo de recursos que contém a rede Virtual do Azure. Substitua **localização** com a localização (região) que o grupo foi criado em.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Quando o grupo tiver sido criado, receber informações sobre o novo grupo.

2. Utilize o seguinte para adicionar regras para o novo grupo de segurança de rede que permitem a comunicação de entrada na porta 443 do serviço de estado de funcionamento e gestão do Azure HDInsight. Substitua **RESOURCEGROUPNAME** com o nome do grupo de recursos que contém a rede Virtual do Azure.

    > [!IMPORTANT]  
    > Altere os endereços IP utilizados neste exemplo, de acordo com a região do Azure que está a utilizar. Pode encontrar estas informações no [HDInsight com grupos de segurança de rede e as rotas definidas pelo utilizador](#hdinsight-ip) secção.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Para obter o identificador exclusivo para este grupo de segurança de rede, utilize o seguinte comando:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Este comando devolve um valor semelhante ao seguinte texto:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Utilize aspas duplas em torno do id no comando, se não receber os resultados esperados.

4. Utilize o seguinte comando para aplicar o grupo de segurança de rede a uma sub-rede. Substitua a __GUID__ e __RESOURCEGROUPNAME__ valores por aqueles que o retornado do passo anterior. Substitua __VNETNAME__ e __SUBNETNAME__ com o nome de rede virtual e o nome da sub-rede que pretende criar.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Assim que este comando for concluído, pode instalar HDInsight numa rede Virtual.

> [!IMPORTANT]  
> Estes passos apenas abrir o acesso ao serviço de estado de funcionamento e gestão de HDInsight na cloud do Azure. Qualquer outro acesso para o cluster de HDInsight a partir de fora da rede Virtual está bloqueado. Para ativar o acesso a partir de fora da rede virtual, tem de adicionar regras de grupo de segurança de rede adicionais.
>
> O exemplo seguinte demonstra como ativar o acesso SSH da Internet:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> Exemplo: Configuração do DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Resolução de nomes entre uma rede virtual e uma rede no local ligado

Este exemplo faz as seguintes suposições:

* Tem uma rede Virtual do Azure que está ligada a uma rede no local através de um gateway VPN.

* O servidor DNS personalizado na rede virtual está a executar Linux ou Unix como o sistema operativo.

* [Vincular](https://www.isc.org/downloads/bind/) está instalado no servidor DNS personalizado.

No servidor DNS personalizado na rede virtual:

1. Utilize o Azure PowerShell ou CLI clássica do Azure para encontrar o sufixo DNS da rede virtual:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. No servidor DNS personalizado para a rede virtual, utilize o seguinte texto como conteúdo do `/etc/bind/named.conf.local` ficheiro:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Substitua o `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` valor com o sufixo DNS da sua rede virtual.

    Esta configuração encaminha todos os pedidos DNS para o sufixo DNS da rede virtual para o resolvedor recursivo do Azure.

2. No servidor DNS personalizado para a rede virtual, utilize o seguinte texto como conteúdo do `/etc/bind/named.conf.options` ficheiro:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Substitua o `10.0.0.0/16` valor com o intervalo de endereços IP da sua rede virtual. Esta entrada permite que os endereços de pedidos de resolução de nomes dentro deste intervalo.

    * Adicionar o intervalo de endereços IP da rede no local para o `acl goodclients { ... }` secção.  entrada permite que os pedidos de resolução de nomes de recursos na rede no local.
    
    * Substitua o valor `192.168.0.1` com o endereço IP do servidor DNS no local. Esta entrada encaminha todos os outros pedidos DNS para o servidor DNS no local.

3. Para utilizar a configuração, reinicie o enlace. Por exemplo, `sudo service bind9 restart`.

4. Adicione um reencaminhador condicional para o servidor DNS no local. Configure o reencaminhador condicional para enviar pedidos para o sufixo DNS do passo 1 para o servidor DNS personalizado.

    > [!NOTE]  
    > Consulte a documentação do seu software DNS para obter informações específicas sobre como adicionar um reencaminhador condicional.

Depois de concluir estes passos, pode ligar a recursos em qualquer rede através de nomes de domínio completamente qualificado (FQDN). Agora, pode instalar HDInsight numa rede virtual.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Resolução de nomes entre duas redes virtuais ligadas

Este exemplo faz as seguintes suposições:

* Tem duas redes virtuais do Azure que estão ligadas através de um gateway VPN ou o peering.

* O servidor DNS personalizado em ambas as redes está a executar Linux ou Unix como o sistema operativo.

* [Vincular](https://www.isc.org/downloads/bind/) está instalado nos servidores DNS personalizados.

1. Utilize o Azure PowerShell ou CLI clássica do Azure para encontrar o sufixo DNS de ambas as redes virtuais:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Utilize o seguinte texto como conteúdo do `/etc/bind/named.config.local` ficheiro no servidor DNS personalizado. Efetue esta alteração no servidor DNS personalizado em ambas as redes virtuais.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Substitua a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` valor com o sufixo DNS do __outros__ rede virtual. Esta entrada encaminha os pedidos para o sufixo DNS da rede remoto para o DNS personalizado nessa rede.

3. Nos servidores DNS personalizados em ambas as redes virtuais, utilize o seguinte texto como conteúdo do `/etc/bind/named.conf.options` ficheiro:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
   * Substitua a `10.0.0.0/16` e `10.1.0.0/16` intervalos de suas redes virtuais de endereços de valores com o IP. Esta entrada permite que os recursos em cada rede para fazer pedidos dos servidores DNS.

     Todos os pedidos que não são para os sufixos DNS das redes virtuais (por exemplo, microsoft.com) é manipulada pelo resolvedor recursivo do Azure.

4. Para utilizar a configuração, reinicie o enlace. Por exemplo, `sudo service bind9 restart` em ambos os servidores DNS.

Depois de concluir estes passos, pode ligar a recursos na rede virtual através de nomes de domínio completamente qualificado (FQDN). Agora, pode instalar HDInsight numa rede virtual.

## <a name="next-steps"></a>Passos Seguintes

* Para obter um exemplo de ponto a ponto da configuração do HDInsight para ligar a uma rede no local, consulte [HDInsight ligar a uma rede no local](./connect-on-premises-network.md).
* Para configurar clusters do Apache Hbase em redes virtuais do Azure, veja [Apache HBase criar clusters no HDInsight na rede Virtual do Azure](hbase/apache-hbase-provision-vnet.md).
* Para configurar a georreplicação do HBase do Apache, veja [configurar a replicação de cluster do Apache HBase em redes virtuais do Azure](hbase/apache-hbase-replication.md).
* Para obter mais informações sobre redes virtuais do Azure, consulte a [descrição geral da rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

* Para obter mais informações sobre grupos de segurança de rede, consulte [grupos de segurança de rede](../virtual-network/security-overview.md).

* Para obter mais informações sobre as rotas definidas pelo utilizador, consulte [rotas definidas pelo utilizador e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).
