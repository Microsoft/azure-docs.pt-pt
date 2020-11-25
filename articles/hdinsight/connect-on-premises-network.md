---
title: Ligue o Azure HDInsight à sua rede no local
description: Aprenda a criar um cluster HDInsight numa Rede Virtual Azure e, em seguida, conecte-o à sua rede no local. Saiba como configurar a resolução de nomes entre o HDInsight e a sua rede no local utilizando um servidor DNS personalizado.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 71ef902e909e552ade5174196f291630bc242ca0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005372"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Ligar o HDInsight à sua rede no local

Saiba como ligar o HDInsight à sua rede no local utilizando redes virtuais Azure e uma porta de entrada VPN. Este documento fornece informações sobre:

* Utilização de HDInsight numa Rede Virtual Azure que se conecta à sua rede no local.
* Configurar a resolução de nomes DNS entre a rede virtual e a sua rede no local.
* Configurar grupos de segurança de rede para restringir o acesso à Internet ao HDInsight.
* Portas fornecidas por HDInsight na rede virtual.

## <a name="overview"></a>Descrição geral

Para permitir que o HDInsight e os recursos da rede unida se comuniquem pelo nome, deve realizar as seguintes ações:

1. Criar Rede Virtual Azure.
1. Crie um servidor DNS personalizado na Rede Virtual Azure.
1. Configure a rede virtual para utilizar o servidor DNS personalizado em vez do Azure Recursive Resolver padrão.
1. Configurar o encaminhamento entre o servidor DNS personalizado e o servidor DNS no local.

Estas configurações permitem o seguinte comportamento:

* Os pedidos de nomes de domínio totalmente qualificados que tenham o sufixo DNS __para a rede virtual__ são reencaminhados para o servidor DNS personalizado. O servidor DNS personalizado reencaminha estes pedidos para o Azure Recursive Resolver, que devolve o endereço IP.
* Todos os outros pedidos são reencaminhados para o servidor DNS no local. Mesmo os pedidos de recursos públicos da Internet, como microsoft.com são encaminhados para o servidor DNS no local para resolução de nomes.

No diagrama seguinte, as linhas verdes são pedidos de recursos que terminam no sufixo DNS da rede virtual. As linhas azuis são pedidos de recursos na rede de acesso ou na internet pública.

![Diagrama de como os pedidos de DNS são resolvidos na configuração](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Se utilizar o PowerShell, necessitará do [Módulo AZ](/powershell/azure/).
* Se quiser utilizar o Azure CLI e ainda não o instalou, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Criar configuração de rede virtual

Utilize os seguintes documentos para aprender a criar uma Rede Virtual Azure que esteja ligada à sua rede no local:

* [Com o Portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Utilizar o Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Utilizar a CLI do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Crie servidor DNS personalizado

> [!IMPORTANT]  
> Tem de criar e configurar o servidor DNS antes de instalar o HDInsight na rede virtual.

Estes passos utilizam o [portal Azure](https://portal.azure.com) para criar uma Máquina Virtual Azure. Para outras formas de criar uma máquina virtual, consulte [Create VM - Azure CLI](../virtual-machines/linux/quick-create-cli.md) e [Create VM - Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Para criar um Linux VM que utilize o software [Bind](https://www.isc.org/downloads/bind/) DNS, utilize os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
  
1. A partir do menu superior, selecione **+ Crie um recurso.**

    ![Criar uma máquina virtual Ubuntu](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. Selecione a máquina Virtual **compute**  >  **Virtual machine** para ir à página de máquina **virtual Criar.**

1. A partir do separador __Básicos,__ introduza as seguintes informações:  
  
    | Campo | Valor |
    | --- | --- |
    |Subscrição |Selecione a sua subscrição apropriada.|
    |Grupo de recursos |Selecione o grupo de recursos que contém a rede virtual criada anteriormente.|
    |Nome da máquina virtual | Introduza um nome amigável que identifique esta máquina virtual. Este exemplo utiliza **DNSProxy**.|
    |Region | Selecione a mesma região que a rede virtual criada anteriormente.  Nem todos os tamanhos de VM estão disponíveis em todas as regiões.  |
    |Opções de disponibilidade |  Selecione o nível de disponibilidade desejado.  O Azure oferece um leque de opções para gerir a disponibilidade e a resiliência das suas aplicações.  Architecte a sua solução para utilizar VMs replicados em Zonas de Disponibilidade ou Conjuntos de Disponibilidade para proteger as suas apps e dados contra interrupções de datacenter e eventos de manutenção. Este exemplo utiliza **não é necessário um despedimento de infraestruturas.** |
    |Imagem | Deixe no **Ubuntu Server 18.04 LTS**. |
    |Tipo de autenticação | __Senha__ ou __chave pública SSH__: O método de autenticação para a conta SSH. Recomendamos usar chaves públicas, já que são mais seguras. Este exemplo utiliza **a Palavra-passe.**  Para obter mais informações, consulte as [teclas Criar e utilizar as teclas SSH para o documento Linux VMs.](../virtual-machines/linux/mac-create-ssh-keys.md)|
    |Nome de utilizador |Introduza o nome de utilizador do administrador para o VM.  Este exemplo usa **sshuser**.|
    |Senha ou chave pública SSH | O campo disponível é determinado pela sua escolha para **tipo de autenticação**.  Insira o valor apropriado.|
    |Portas de entrada públicas|Selecione **Deixe as portas selecionadas.** Em seguida, selecione **SSH (22)** da lista de descida das portas de **entrada Select.**|

    ![Configuração básica da máquina virtual](./media/connect-on-premises-network/virtual-machine-basics.png)

    Deixe outras entradas nos valores predefinidos e, em seguida, selecione o **separador 'Rede'.**

4. A partir do **separador Networking,** introduza as seguintes informações:

    | Campo | Valor |
    | --- | --- |
    |Rede virtual | Selecione a rede virtual que criou anteriormente.|
    |Sub-rede | Selecione a sub-rede predefinitiva para a rede virtual que criou anteriormente. __Não__ selecione a sub-rede utilizada pelo gateway VPN.|
    |IP público | Utilize o valor autopovoado.  |

    ![Definições de rede virtual HDInsight](./media/connect-on-premises-network/virtual-network-settings.png)

    Deixe outras entradas nos valores predefinidos e, em seguida, selecione o **Review + create**.

5. A partir do separador **'Rever + criar',** selecione **Criar** para criar a máquina virtual.

### <a name="review-ip-addresses"></a>Rever endereços IP

Uma vez criada a máquina virtual, receberá uma notificação **conseguida da Implementação** com um botão **de recurso Go para recurso.**  Selecione **Vá para o recurso** para ir para a sua nova máquina virtual.  A partir da vista padrão para a sua nova máquina virtual, siga estes passos para identificar os endereços IP associados:

1. A partir **de Definições,** selecione **Propriedades**.

2. Note os valores **para ENDEREÇO IP PÚBLICO/ETIQUETA DE DNS** E **ENDEREÇO IP PRIVADO** para utilização posterior.

   ![Endereços IP públicos e privados](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalar e configurar o Bind (software DNS)

1. Utilize o SSH para ligar ao __endereço IP público__ da máquina virtual. `sshuser`Substitua-a pela conta de utilizador SSH especificada ao criar o VM. O exemplo a seguir liga-se a uma máquina virtual a 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Para instalar o Bind, utilize os seguintes comandos da sessão SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Para configurar o Bind para enviar pedidos de resolução de nomes para o seu servidor DNS nas instalações, utilize o seguinte texto como o conteúdo do `/etc/bind/named.conf.options` ficheiro:

    ```DNS Zone file
    acl goodclients {
        10.0.0.0/16; # Replace with the IP address range of the virtual network
        10.1.0.0/16; # Replace with the IP address range of the on-premises network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            192.168.0.1; # Replace with the IP address of the on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

    > [!IMPORTANT]  
    > Substitua os valores na `goodclients` secção pela gama de endereços IP da rede virtual e da rede no local. Esta secção define os endereços de que este servidor DNS aceita pedidos.
    >
    > Substitua a `192.168.0.1` entrada na secção pelo endereço IP do servidor `forwarders` DNS nas instalações. Esta rota de entrada DNS solicita ao seu servidor DNS no local para resolução.

    Para editar este ficheiro, utilize o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Para guardar o ficheiro, utilize __Ctrl+X,__ __Y__, e, em seguida, __Insira__.

4. A partir da sessão SSH, utilize o seguinte comando:

    ```bash
    hostname -f
    ```

    Este comando devolve um valor semelhante ao seguinte texto:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    O `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` texto é o __sufixo DNS__ para esta rede virtual. Guarde este valor, como é usado mais tarde.

5. Para configurar o Bind para resolver os nomes dns para recursos dentro da rede virtual, utilize o seguinte texto como conteúdo do `/etc/bind/named.conf.local` ficheiro:

    ```DNS Zone file
    // Replace the following with the DNS suffix for your virtual network
    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Deve substituir o `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` sufixo DNS que recuperou anteriormente.

    Para editar este ficheiro, utilize o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Para guardar o ficheiro, utilize __Ctrl+X,__ __Y__, e, em seguida, __Insira__.

6. Para iniciar o Bind, utilize o seguinte comando:

    ```bash
    sudo service bind9 restart
    ```

7. Para verificar se a ligação pode resolver os nomes dos recursos na sua rede no local, utilize os seguintes comandos:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Substitua `dns.mynetwork.net` pelo nome de domínio totalmente qualificado (FQDN) de um recurso na sua rede no local.
    >
    > `10.0.0.4`Substitua-o pelo __endereço IP interno__ do seu servidor DNS personalizado na rede virtual.

    A resposta parece semelhante ao seguinte texto:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Configure a rede virtual para utilizar o servidor DNS personalizado

Para configurar a rede virtual para utilizar o servidor DNS personalizado em vez do Azure recursive resolver, utilize os seguintes passos a partir do [portal Azure](https://portal.azure.com):

1. A partir do menu esquerdo, navegue para todas as redes virtuais de rede de **serviços.**  >  **Networking**  >  **Virtual networks**

2. Selecione a sua rede virtual a partir da lista, que abrirá a vista padrão para a sua rede virtual.  

3. A partir da vista padrão, em **Definições**, selecione **servidores DNS**.  

4. Selecione __Custom__, e insira o **ENDEREÇO IP PRIVADO** do servidor DNS personalizado.

5. Selecione __Guardar__.  <br />  

    ![Desa esta medida o servidor DNS personalizado para a rede](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Configurar no local servidor DNS

Na secção anterior, configura o servidor DNS personalizado para encaminhar pedidos para o servidor DNS no local. Em seguida, tem de configurar o servidor DNS no local para encaminhar pedidos para o servidor DNS personalizado.

Para obter etapas específicas sobre como configurar o seu servidor DNS, consulte a documentação do seu software de servidor DNS. Procure os passos sobre como configurar um __reencaminhador condicional__.

Um avançado condicional apenas remete os pedidos para um sufixo de DNS específico. Neste caso, deve configurar um reencaminhador para o sufixo DNS da rede virtual. Os pedidos deste sufixo devem ser reencaminhados para o endereço IP do servidor DNS personalizado. 

O texto a seguir é um exemplo de uma configuração de reencaminhamento condicional para o software **Bind** DNS:

```DNS Zone file
zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
    type forward;
    forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
};
```

Para obter informações sobre a utilização de DNS no **Windows Server 2016,** consulte a documentação [Add-DnsServerConditionalForwarderZone...](/powershell/module/dnsserver/add-dnsserverconditionalforwarderzone)

Uma vez configurado o servidor DNS no local, pode utilizar `nslookup` a partir da rede no local para verificar se pode resolver nomes na rede virtual. O exemplo a seguir 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Este exemplo utiliza o servidor DNS no local em 196.168.0.4 para resolver o nome do servidor DNS personalizado. Substitua o endereço IP pelo do servidor DNS no local. Substitua o `dnsproxy` endereço pelo nome de domínio totalmente qualificado do servidor DNS personalizado.

## <a name="optional-control-network-traffic"></a>Opcional: Tráfego de rede de controlo

Pode utilizar grupos de segurança de rede (NSG) ou rotas definidas pelo utilizador (UDR) para controlar o tráfego da rede. Os NSGs permitem filtrar o tráfego de entrada e saída e permitir ou negar o tráfego. As UDRs permitem-lhe controlar como o tráfego flui entre os recursos na rede virtual, na internet e na rede no local.

> [!WARNING]  
> O HDInsight requer acesso à entrada a partir de endereços IP específicos na nuvem Azure e acesso de saída sem restrições. Ao utilizar NSGs ou UDRs para controlar o tráfego, deve executar os seguintes passos:

1. Encontre os endereços IP para a localização que contém a sua rede virtual. Para obter uma lista de IPs necessários por localização, consulte [os endereços IP necessários](./hdinsight-management-ip-addresses.md).

2. Para os endereços IP identificados no passo 1, permita o tráfego de entrada a partir desse endereço IP.

   * Se estiver a utilizar __o NSG:__ Permita o tráfego __de entrada na__ porta __443__ para os endereços IP.
   * Se estiver a utilizar __o UDR:__ Desaça o tipo __de Next Hop__ da rota para a __Internet__ para os endereços IP.

Para um exemplo de utilização do Azure PowerShell ou do CLI Azure para criar NSGs, consulte o [documento Extend HDInsight com redes virtuais Azure.](hdinsight-create-virtual-network.md#hdinsight-nsg)

## <a name="create-the-hdinsight-cluster"></a>Crie o cluster HDInsight

> [!WARNING]  
> Tem de configurar o servidor DNS personalizado antes de instalar o HDInsight na rede virtual.

Utilize os passos no [cluster Criar um cluster HDInsight utilizando o documento do portal Azure](./hdinsight-hadoop-create-linux-clusters-portal.md) para criar um cluster HDInsight.

> [!WARNING]  
> * Durante a criação do cluster, deve escolher o local que contém a sua rede virtual.
> * Nas __definições Avançadas__ parte da configuração, deve selecionar a rede virtual e a sub-rede que criou anteriormente.

## <a name="connecting-to-hdinsight"></a>Ligação ao HDInsight

A maioria da documentação no HDInsight assume que você tem acesso ao cluster através da internet. Por exemplo, que pode ligar ao cluster em `https://CLUSTERNAME.azurehdinsight.net`. Este endereço utiliza o gateway público, que não está disponível se tiver usado NSGs ou UDRs para restringir o acesso a partir da internet.

Alguma documentação também faz `headnodehost` referências ao ligar-se ao cluster a partir de uma sessão de SSH. Este endereço só está disponível a partir de nós dentro de um cluster, e não é utilizável em clientes ligados através da rede virtual.

Para ligar diretamente ao HDInsight através da rede virtual, utilize os seguintes passos:

1. Para descobrir os nomes de domínio internos totalmente qualificados dos nódos de cluster HDInsight, utilize um dos seguintes métodos:

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

2. Para determinar a porta onde um serviço está disponível, consulte os [Ports utilizados pelos serviços Apache Hadoop no documento HDInsight.](./hdinsight-hadoop-port-settings-for-services.md)

    > [!IMPORTANT]  
    > Alguns serviços alojados nos nós da cabeça só estão ativos num nó de cada vez. Se tentar aceder a um serviço num nó de cabeça e falhar, mude para o outro nó de cabeça.
    >
    > Por exemplo, Apache Ambari só está ativo num nó de cada vez. Se tentar aceder a Ambari num nó de cabeça e retornar um erro de 404, então está a correr no outro nó da cabeça.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a utilização do HDInsight numa rede virtual, consulte [Plan uma implementação de rede virtual para clusters Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

* Para obter mais informações sobre as redes virtuais Azure, consulte a [visão geral da Rede Virtual Azure](../virtual-network/virtual-networks-overview.md).

* Para obter mais informações sobre grupos de segurança de rede, consulte [os grupos de segurança da rede](../virtual-network/network-security-groups-overview.md).

* Para obter mais informações sobre as rotas definidas pelo utilizador, consulte [as rotas definidas pelo Utilizador e o encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).