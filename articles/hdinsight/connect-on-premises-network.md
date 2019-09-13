---
title: Conectar o Azure HDInsight à sua rede local
description: Saiba como criar um cluster HDInsight em uma rede virtual do Azure e, em seguida, conecte-o à sua rede local. Saiba como configurar a resolução de nomes entre o HDInsight e sua rede local usando um servidor DNS personalizado.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 1ed722ad68280226387b98b3fefb77647f5cd825
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918552"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Ligar o HDInsight à sua rede no local

Saiba como conectar o HDInsight à sua rede local usando redes virtuais do Azure e um gateway de VPN. Este documento fornece informações de planejamento sobre:

* Usando o HDInsight em uma rede virtual do Azure que se conecta à sua rede local.
* Configurar a resolução de nomes DNS entre a rede virtual e a rede local.
* Configurar grupos de segurança de rede para restringir o acesso à Internet para o HDInsight.
* Portas fornecidas pelo HDInsight na rede virtual.

## <a name="overview"></a>Descrição geral

Para permitir que o HDInsight e os recursos na rede unida se comuniquem por nome, você deve executar as seguintes ações:

* Crie uma rede virtual do Azure.
* Crie um servidor DNS personalizado na rede virtual do Azure.
* Configure a rede virtual para usar o servidor DNS personalizado em vez do resolvedor recursivo padrão do Azure.
* Configure o encaminhamento entre o servidor DNS personalizado e o servidor DNS local.

Essa configuração habilita o seguinte comportamento:

* As solicitações de nomes de domínio totalmente qualificados que têm o sufixo DNS __para a rede virtual__ são encaminhadas para o servidor DNS personalizado. Em seguida, o servidor DNS personalizado encaminha essas solicitações para o resolvedor recursivo do Azure, que retorna o endereço IP.
* Todas as outras solicitações são encaminhadas para o servidor DNS local. Até mesmo solicitações de recursos públicos da Internet, como microsoft.com, são encaminhadas para o servidor DNS local para resolução de nomes.

No diagrama a seguir, as linhas verdes são solicitações de recursos que terminam no sufixo DNS da rede virtual. As linhas azuis são solicitações de recursos na rede local ou na Internet pública.

![Diagrama de como as solicitações DNS são resolvidas na configuração usada neste documento](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Se estiver usando o PowerShell, você precisará do [módulo AZ](https://docs.microsoft.com/powershell/azure/overview).
* Se desejar usar CLI do Azure e você ainda não o tiver instalado, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Criar configuração de rede virtual

Use os documentos a seguir para saber como criar uma rede virtual do Azure que está conectada à sua rede local:

* [Com o Portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Utilizar o Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Utilizar a CLI do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Criar servidor DNS personalizado

> [!IMPORTANT]  
> Você deve criar e configurar o servidor DNS antes de instalar o HDInsight na rede virtual.

Essas etapas usam o [portal do Azure](https://portal.azure.com) para criar uma máquina virtual do Azure. Para outras maneiras de criar uma máquina virtual, consulte [criar VM-CLI do Azure](../virtual-machines/linux/quick-create-cli.md) e [criar VM-Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Para criar uma VM do Linux que usa o software DNS de [ligação](https://www.isc.org/downloads/bind/) , use as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
  
2. No menu à esquerda, navegue até **+ criar um recurso** > **computação** > **Ubuntu Server 18, 4 LTS**.

    ![Criar uma máquina virtual do Ubuntu](./media/connect-on-premises-network/create-ubuntu-virtual-machine.png)

3. Na guia __noções básicas__ , insira as seguintes informações:  
  
    | Campo | Value |
    | --- | --- |
    |Subscription |Selecione sua assinatura apropriada.|
    |Resource group |Selecione o grupo de recursos que contém a rede virtual criada anteriormente.|
    |Nome da máquina virtual | Insira um nome amigável que identifique essa máquina virtual. Este exemplo usa **DNSProxy**.|
    |Região | Selecione a mesma região que a rede virtual criada anteriormente.  Nem todos os tamanhos de VM estão disponíveis em todas as regiões.  |
    |Opções de disponibilidade |  Selecione o nível de disponibilidade desejado.  O Azure oferece uma variedade de opções para gerenciar a disponibilidade e a resiliência para seus aplicativos.  Projete sua solução para usar VMs replicadas em conjuntos de Zonas de Disponibilidade ou de disponibilidade para proteger seus aplicativos e dados de eventos de manutenção e interrupções do datacenter. Este exemplo **não usa nenhuma redundância de infraestrutura necessária**. |
    |Image | Deixe no **Ubuntu Server 18, 4 LTS**. |
    |Tipo de autenticação | __Senha__ ou __chave pública SSH__: O método de autenticação para a conta SSH. É recomendável usar chaves públicas, pois elas são mais seguras. Este exemplo usa **senha**.  Para obter mais informações, consulte o documento [criar e usar chaves SSH para VMs do Linux](../virtual-machines/linux/mac-create-ssh-keys.md) .|
    |Nome de utilizador |Insira o nome de usuário do administrador para a VM.  Este exemplo usa **sshuser**.|
    |Senha ou chave pública SSH | O campo disponível é determinado por sua escolha para o **tipo de autenticação**.  Insira o valor apropriado.|
    |Portas de entrada públicas|Selecione **permitir portas selecionadas**. Em seguida, selecione **SSH (22)** na lista suspensa **selecionar portas de entrada** .|

    ![Configuração básica da máquina virtual](./media/connect-on-premises-network/virtual-machine-basics.png)

    Deixe outras entradas com os valores padrão e, em seguida, selecione a guia **rede** .

4. Na guia **rede** , insira as seguintes informações:

    | Campo | Value |
    | --- | --- |
    |Rede virtual | Selecione a rede virtual que você criou anteriormente.|
    |Subnet | Selecione a sub-rede padrão para a rede virtual que você criou anteriormente. __Não__ selecione a sub-rede usada pelo gateway de VPN.|
    |IP Público | Use o valor preenchido automaticamente.  |

    ![Configurações de rede virtual](./media/connect-on-premises-network/virtual-network-settings.png)

    Deixe outras entradas com os valores padrão e, em seguida, selecione a **revisão + criar**.

5. Na guia **revisar + criar** , selecione **criar** para criar a máquina virtual.

### <a name="review-ip-addresses"></a>Examinar endereços IP
Depois que a máquina virtual tiver sido criada, você receberá uma notificação de **implantação bem-sucedida** com um botão **ir para recurso** .  Selecione **ir para o recurso** para ir para a nova máquina virtual.  Do modo de exibição padrão para sua nova máquina virtual, siga estas etapas para identificar os endereços IP associados:

1. Em **configurações**, selecione **Propriedades**.

2. Observe os valores para **endereço IP público/rótulo de nome DNS** e **endereço IP privado** para uso posterior.

   ![Endereços IP públicos e privados](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalar e configurar o BIND (software DNS)

1. Use o SSH para se conectar ao __endereço IP público__ da máquina virtual. Substitua `sshuser` pela conta de usuário SSH que você especificou ao criar a VM. O exemplo a seguir se conecta a uma máquina virtual em 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Para instalar o BIND, use os seguintes comandos da sessão SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Para configurar o BIND para encaminhar solicitações de resolução de nomes para o servidor DNS local, use o seguinte texto como o `/etc/bind/named.conf.options` conteúdo do arquivo:

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

    > [!IMPORTANT]  
    > Substitua os valores na `goodclients` seção pelo intervalo de endereços IP da rede virtual e da rede local. Esta seção define os endereços dos quais esse servidor DNS aceita solicitações.
    >
    > Substitua a `192.168.0.1` entrada `forwarders` na seção pelo endereço IP do seu servidor DNS local. Essa entrada roteia as solicitações DNS para o servidor DNS local para resolução.

    Para editar esse arquivo, use o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Para salvar o arquivo, use __Ctrl + X__, __Y__e, em seguida, __Enter__.

4. Na sessão SSH, use o seguinte comando:

    ```bash
    hostname -f
    ```

    Esse comando retorna um valor semelhante ao seguinte texto:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    O `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` texto é o __sufixo DNS__ para esta rede virtual. Guarde este valor, porque vai ser utilizado mais tarde.

5. Para configurar o BIND para resolver nomes DNS para recursos na rede virtual, use o seguinte texto como o conteúdo do `/etc/bind/named.conf.local` arquivo:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > Você deve substituir o `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` pelo sufixo DNS que você recuperou anteriormente.

    Para editar esse arquivo, use o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Para salvar o arquivo, use __Ctrl + X__, __Y__e, em seguida, __Enter__.

6. Para iniciar a ligação, use o seguinte comando:

    ```bash
    sudo service bind9 restart
    ```

7. Para verificar se o BIND pode resolver os nomes dos recursos em sua rede local, use os seguintes comandos:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Substitua `dns.mynetwork.net` pelo FQDN (nome de domínio totalmente qualificado) de um recurso em sua rede local.
    >
    > Substitua `10.0.0.4` pelo __endereço IP interno__ do seu servidor DNS personalizado na rede virtual.

    A resposta é semelhante ao seguinte texto:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Configurar a rede virtual para usar o servidor DNS personalizado

Para configurar a rede virtual para usar o servidor DNS personalizado em vez do resolvedor recursivo do Azure, use as seguintes etapas do [portal do Azure](https://portal.azure.com):

1. No menu à esquerda, navegue até **todos os serviços** > **rede** > **redes virtuais**.

2. Selecione sua rede virtual na lista, que abrirá a exibição padrão para sua rede virtual.  

3. Na exibição padrão, em **configurações**, selecione **servidores DNS**.  

4. Selecione __personalizado__e insira o **endereço IP privado** do servidor DNS personalizado.   

5. Selecione __Guardar__.  <br />  

    ![Definir o servidor DNS personalizado para a rede](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Configurar servidor DNS local

Na seção anterior, você configurou o servidor DNS personalizado para encaminhar solicitações para o servidor DNS local. Em seguida, você deve configurar o servidor DNS local para encaminhar solicitações para o servidor DNS personalizado.

Para obter etapas específicas sobre como configurar o servidor DNS, consulte a documentação do software do servidor DNS. Procure as etapas de como configurar um __encaminhador condicional__.

Um encaminhamento condicional apenas encaminha solicitações para um sufixo DNS específico. Nesse caso, você deve configurar um encaminhador para o sufixo DNS da rede virtual. As solicitações para esse sufixo devem ser encaminhadas para o endereço IP do servidor DNS personalizado. 

O texto a seguir é um exemplo de uma configuração de encaminhador condicional para o software DNS de **ligação** :

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Para obter informações sobre como usar o DNS no **Windows Server 2016**, consulte a documentação do [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) ...

Depois de configurar o servidor DNS local, você pode usar `nslookup` a partir da rede local para verificar se você pode resolver nomes na rede virtual. O exemplo a seguir 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Este exemplo usa o servidor DNS local em 196.168.0.4 para resolver o nome do servidor DNS personalizado. Substitua o endereço IP por um para o servidor DNS local. Substitua o `dnsproxy` endereço pelo nome de domínio totalmente qualificado do servidor DNS personalizado.

## <a name="optional-control-network-traffic"></a>Opcional: Controlar o tráfego de rede

Você pode usar NSG (grupos de segurança de rede) ou UDR (rotas definidas pelo usuário) para controlar o tráfego de rede. O NSGs permite filtrar o tráfego de entrada e de saída e permitir ou negar o tráfego. O UDRs permite controlar como o tráfego flui entre os recursos na rede virtual, a Internet e a rede local.

> [!WARNING]  
> O HDInsight requer acesso de entrada de endereços IP específicos na nuvem do Azure e acesso irrestrito de saída. Ao usar NSGs ou UDRs para controlar o tráfego, você deve executar as seguintes etapas:

1. Localize os endereços IP para o local que contém sua rede virtual. Para obter uma lista de IPs necessários por local, consulte [endereços IP necessários](./hdinsight-management-ip-addresses.md).

2. Para os endereços IP identificados na etapa 1, permita o tráfego de entrada dos endereços IP.

   * Se você estiver usando __NSG__: Permita o tráfego de __entrada__ na porta __443__ para os endereços IP.
   * Se você estiver usando __UDR__: Defina o tipo do __próximo salto__ da rota para a __Internet__ para os endereços IP.

Para obter um exemplo de como usar Azure PowerShell ou o CLI do Azure para criar NSGs, consulte o documento [estender o HDInsight com redes virtuais do Azure](hdinsight-create-virtual-network.md#hdinsight-nsg) .

## <a name="create-the-hdinsight-cluster"></a>Criar o cluster HDInsight

> [!WARNING]  
> Você deve configurar o servidor DNS personalizado antes de instalar o HDInsight na rede virtual.

Use as etapas no documento [criar um cluster hdinsight usando o portal do Azure](./hdinsight-hadoop-create-linux-clusters-portal.md) para criar um cluster hdinsight.

> [!WARNING]  
> * Durante a criação do cluster, você deve escolher o local que contém sua rede virtual.
> * Na parte __Configurações avançadas__ da configuração, você deve selecionar a rede virtual e a sub-rede que você criou anteriormente.

## <a name="connecting-to-hdinsight"></a>Conectando-se ao HDInsight

A maioria das documentações no HDInsight pressupõe que você tenha acesso ao cluster pela Internet. Por exemplo, que pode ligar ao cluster em `https://CLUSTERNAME.azurehdinsight.net`. Esse endereço usa o gateway público, que não estará disponível se você tiver usado NSGs ou UDRs para restringir o acesso da Internet.

Algumas documentações também `headnodehost` fazem referência ao se conectar ao cluster de uma sessão SSH. Esse endereço só está disponível em nós em um cluster e não pode ser usado em clientes conectados pela rede virtual.

Para se conectar diretamente ao HDInsight por meio da rede virtual, use as seguintes etapas:

1. Para descobrir os nomes de domínio totalmente qualificados internos dos nós do cluster HDInsight, use um dos seguintes métodos:

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

2. Para determinar a porta em que um serviço está disponível, consulte o documento [portas usadas pelo Apache Hadoop Services no HDInsight](./hdinsight-hadoop-port-settings-for-services.md) .

    > [!IMPORTANT]  
    > Alguns serviços hospedados nos nós de cabeçalho estão ativos apenas em um nó por vez. Se você tentar acessar um serviço em um nó de cabeçalho e ele falhar, alterne para o outro nó de cabeçalho.
    >
    > Por exemplo, o Apache Ambari está ativo apenas em um nó de cabeçalho por vez. Se você tentar acessar o Ambari em um nó de cabeçalho e ele retornar um erro 404, ele será executado no outro nó de cabeçalho.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como usar o HDInsight em uma rede virtual, consulte [planejar uma implantação de rede virtual para clusters do Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

* Para obter mais informações sobre redes virtuais do Azure, consulte [visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md).

* Para obter mais informações sobre grupos de segurança de rede, consulte [grupos de segurança de rede](../virtual-network/security-overview.md).

* Para obter mais informações sobre rotas definidas pelo usuário, consulte [rotas definidas pelo usuário e encaminhamento de IP](../virtual-network/virtual-networks-udr-overview.md).
