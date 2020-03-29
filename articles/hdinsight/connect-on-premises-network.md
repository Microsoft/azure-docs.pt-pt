---
title: Ligue o Azure HDInsight à sua rede no local
description: Aprenda a criar um cluster HDInsight numa Rede Virtual Azure e, em seguida, conecte-o à sua rede no local. Saiba como configurar a resolução de nomes entre o HDInsight e a sua rede no local utilizando um servidor DNS personalizado.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 2ed7a5b9c81d1b50f80f379a88688b69c49ed382
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897922"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Ligar o HDInsight à sua rede no local

Saiba como ligar o HDInsight à sua rede no local utilizando redes virtuais Azure e um gateway VPN. Este documento fornece informações de planeamento sobre:

* Utilizando o HDInsight numa Rede Virtual Azure que se conecta à sua rede no local.
* Configurar a resolução de nomes DNS entre a rede virtual e a sua rede no local.
* Configurar grupos de segurança de rede para restringir o acesso à Internet ao HDInsight.
* Portas fornecidas pela HDInsight na rede virtual.

## <a name="overview"></a>Descrição geral

Para permitir que o HDInsight e os recursos na rede unida se comuniquem pelo nome, deve realizar as seguintes ações:

1. Criar rede virtual Azure.
1. Crie um servidor DNS personalizado na Rede Virtual Azure.
1. Configure a rede virtual para utilizar o servidor DNS personalizado em vez do Resolver Recursivo Desdefinido.
1. Configure o reencaminhamento entre o servidor DNS personalizado e o servidor DNS no local.

Estas configurações permitem o seguinte comportamento:

* Os pedidos de nomes de domínio totalmente qualificados que tenham o sufixo DNS __para a rede virtual__ são encaminhados para o servidor DNS personalizado. O servidor DNS personalizado reencaminha então estes pedidos para o Azure Recursive Resolver, que devolve o endereço IP.
* Todos os outros pedidos são encaminhados para o servidor DNS no local. Mesmo os pedidos de recursos públicos da internet, como microsoft.com são encaminhados para o servidor DNS no local para resolução de nomes.

No diagrama seguinte, as linhas verdes são pedidos de recursos que terminam no sufixo DNS da rede virtual. As linhas azuis são pedidos de recursos na rede no local ou na internet pública.

![Diagrama de como os pedidos de DNS são resolvidos na configuração](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Se utilizar o PowerShell, necessitará do [Módulo AZ](https://docs.microsoft.com/powershell/azure/overview).
* Se pretender utilizar o Azure CLI e ainda não o tiver instalado, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Criar configuração de rede virtual

Utilize os seguintes documentos para aprender a criar uma Rede Virtual Azure que esteja ligada à sua rede no local:

* [Usando o portal Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Utilizar o Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Utilizar a CLI do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Criar servidor DNS personalizado

> [!IMPORTANT]  
> Tem de criar e configurar o servidor DNS antes de instalar o HDInsight na rede virtual.

Estes passos utilizam o [portal Azure](https://portal.azure.com) para criar uma Máquina Virtual Azure. Para outras formas de criar uma máquina virtual, consulte [Create VM - Azure CLI](../virtual-machines/linux/quick-create-cli.md) e [Create VM - Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Para criar um VM Linux que utilize o software [Bind](https://www.isc.org/downloads/bind/) DNS, utilize os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
  
1. A partir do menu superior, selecione **+ Criar um recurso**.

    ![Criar uma máquina virtual Ubuntu](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. Selecione **Máquina Compute** > **Virtual** para ir à página Criar uma **máquina virtual.**

1. No separador __Basics,__ introduza as seguintes informações:  
  
    | Campo | Valor |
    | --- | --- |
    |Subscrição |Selecione a sua subscrição apropriada.|
    |Grupo de recursos |Selecione o grupo de recursos que contém a rede virtual criada anteriormente.|
    |Nome da máquina virtual | Introduza um nome amigável que identifique esta máquina virtual. Este exemplo utiliza **o DNSProxy**.|
    |Região | Selecione a mesma região que a rede virtual criada anteriormente.  Nem todos os tamanhos vm estão disponíveis em todas as regiões.  |
    |Opções de disponibilidade |  Selecione o nível de disponibilidade desejado.  O Azure oferece um leque de opções para gerir a disponibilidade e resiliência para as suas aplicações.  Arquiteto a sua solução para usar VMs replicados em Zonas de Disponibilidade ou Conjuntos de Disponibilidade para proteger as suas aplicações e dados de falhas de datacenter e eventos de manutenção. Este exemplo não utiliza **qualquer redundância de infraestrutura necessária.** |
    |Imagem | Deixe no **Ubuntu Server 18.04 LTS**. |
    |Tipo de autenticação | __Palavra-passe__ ou __chave pública SSH__: O método de autenticação da conta SSH. Recomendamos usar chaves públicas, já que são mais seguras. Este exemplo utiliza **palavra-passe**.  Para mais informações, consulte as [teclas Create e use as chaves SSH para o documento De VMs Linux.](../virtual-machines/linux/mac-create-ssh-keys.md)|
    |Nome de utilizador |Introduza o nome de utilizador do administrador para o VM.  Este exemplo utiliza **sshuser**.|
    |Senha ou chave pública SSH | O campo disponível é determinado pela sua escolha para **o tipo de autenticação**.  Introduza o valor apropriado.|
    |Portos de entrada pública|**Selecione Permitir portas selecionadas**. Em seguida, selecione **SSH (22)** da lista de descarregamento das **portas de entrada Select.**|

    ![Configuração básica da máquina virtual](./media/connect-on-premises-network/virtual-machine-basics.png)

    Deixe outras entradas nos valores predefinidos e, em seguida, selecione o separador **De Rede.**

4. A partir do separador **Networking,** introduza as seguintes informações:

    | Campo | Valor |
    | --- | --- |
    |Rede virtual | Selecione a rede virtual que criou anteriormente.|
    |Subrede | Selecione a sub-rede predefinida para a rede virtual que criou anteriormente. Não __not__ selecione a sub-rede utilizada pelo gateway VPN.|
    |IP público | Use o valor autopovoado.  |

    ![Definições de rede virtual HDInsight](./media/connect-on-premises-network/virtual-network-settings.png)

    Deixe outras entradas nos valores predefinidos e, em seguida, selecione o **Review + criar**.

5. A partir do separador **Review + criar,** selecione **Criar** para criar a máquina virtual.

### <a name="review-ip-addresses"></a>Rever endereços IP

Uma vez criada a máquina virtual, receberá uma notificação **de Implementação bem sucedida** com um botão De recurso **Go.**  Selecione **Recorrer** para ir à sua nova máquina virtual.  A partir da vista padrão para a sua nova máquina virtual, siga estes passos para identificar os endereços IP associados:

1. A partir de **Definições,** selecione **Propriedades**.

2. Note os valores para endereço **seletivo público/dNS NAME LABEL** e **ENDEREÇO IP PRIVADO** para posterior utilização.

   ![Endereços IP públicos e privados](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalar e configurar Bind (software DNS)

1. Utilize o SSH para se ligar ao __endereço IP público__ da máquina virtual. Substitua-a `sshuser` pela conta de utilizador SSH especificada ao criar o VM. O seguinte exemplo liga-se a uma máquina virtual em 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Para instalar o Bind, utilize os seguintes comandos da sessão SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Para configurar o Bind para enviar pedidos de resolução de nomes para o `/etc/bind/named.conf.options` seu servidor DNS no local, utilize o seguinte texto como conteúdo do ficheiro:

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
    > Substitua os `goodclients` valores na secção pela gama de endereços IP da rede virtual e da rede no local. Esta secção define os endereços de que este servidor DNS aceita pedidos.
    >
    > Substitua `192.168.0.1` a `forwarders` entrada na secção pelo endereço IP do seu servidor DNS no local. Esta entrada encaminha os pedidos do DNS para o seu servidor DNS no local para resolução.

    Para editar este ficheiro, utilize o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Para guardar o ficheiro, utilize __Ctrl+X,__ __Y,__ e depois __Enter__.

4. A partir da sessão SSH, utilize o seguinte comando:

    ```bash
    hostname -f
    ```

    Este comando devolve um valor semelhante ao seguinte texto:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    O `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` texto é o __sufixo DNS__ para esta rede virtual. Poupe este valor, como é usado mais tarde.

5. Para configurar o Bind para resolver os nomes de DNS para `/etc/bind/named.conf.local` recursos dentro da rede virtual, utilize o seguinte texto como conteúdo do ficheiro:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > Tem de `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` substituir o sufixo DNS que recuperou anteriormente.

    Para editar este ficheiro, utilize o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Para guardar o ficheiro, utilize __Ctrl+X,__ __Y,__ e depois __Enter__.

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
    > Substitua-a `dns.mynetwork.net` com o nome de domínio totalmente qualificado (FQDN) de um recurso na sua rede no local.
    >
    > Substitua-o `10.0.0.4` pelo __endereço IP interno__ do seu servidor DNS personalizado na rede virtual.

    A resposta parece semelhante ao seguinte texto:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Configure a rede virtual para utilizar o servidor DNS personalizado

Para configurar a rede virtual para utilizar o servidor DNS personalizado em vez do resolver recursivo Azure, utilize os seguintes passos do [portal Azure:](https://portal.azure.com)

1. Do menu esquerdo, navegue para **todos os serviços** > Redes**Virtuais****de** > Rede.

2. Selecione a sua rede virtual da lista, que abrirá a vista padrão para a sua rede virtual.  

3. A partir da vista predefinida, em **Definições,** selecione **servidores DNS**.  

4. Selecione __Custom__e introduza o **ENDEREÇO IP PRIVADO** do servidor DNS personalizado.

5. Selecione __Guardar__.  <br />  

    ![Desloque o servidor DNS personalizado para a rede](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Configurar no local o servidor DNS

Na secção anterior, configurou o servidor DNS personalizado para encaminhar pedidos para o servidor DNS no local. Em seguida, deve configurar o servidor DNS no local para encaminhar pedidos para o servidor DNS personalizado.

Para obter passos específicos sobre como configurar o seu servidor DNS, consulte a documentação para o seu software de servidor DNS. Procure os passos sobre como configurar um __avançado condicional__.

Um avançado condicional apenas reencaminha pedidos para um sufixo DNS específico. Neste caso, deve configurar um reencaminhador para o sufixo DNS da rede virtual. Os pedidos para este sufixo devem ser encaminhados para o endereço IP do servidor DNS personalizado. 

O seguinte texto é um exemplo de uma configuração de um forwardr condicional para o software **Bind** DNS:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Para obter informações sobre a utilização de DNS no **Windows Server 2016,** consulte a documentação [Add-DnsServerConditionalForwarderZone...](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone)

Depois de configurar o servidor DNS no local, `nslookup` pode utilizar a partir da rede no local para verificar se pode resolver nomes na rede virtual. O seguinte exemplo 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Este exemplo utiliza o servidor DNS no local em 196.168.0.4 para resolver o nome do servidor DNS personalizado. Substitua o endereço IP pelo do servidor DNS no local. Substitua `dnsproxy` o endereço pelo nome de domínio totalmente qualificado do servidor DNS personalizado.

## <a name="optional-control-network-traffic"></a>Opcional: Controlar o tráfego da rede

Pode utilizar grupos de segurança de rede (NSG) ou rotas definidas pelo utilizador (UDR) para controlar o tráfego da rede. Os NSGs permitem filtrar o tráfego de entrada e saída e permitir ou negar o tráfego. Os UDRs permitem controlar como o tráfego flui entre os recursos na rede virtual, na internet e na rede no local.

> [!WARNING]  
> O HDInsight requer acesso de entrada a partir de endereços IP específicos na nuvem Azure e acesso de saída sem restrições. Ao utilizar NSGs ou UDRs para controlar o tráfego, deve executar os seguintes passos:

1. Encontre os endereços IP para a localização que contém a sua rede virtual. Para obter uma lista dos IPs necessários por localização, consulte [os endereços IP necessários](./hdinsight-management-ip-addresses.md).

2. Para os endereços IP identificados na etapa 1, permita o tráfego de entrada a partir desse endereço IP.

   * Se estiver a utilizar o __NSG__: Permita o tráfego __de entrada__ na porta __443__ para os endereços IP.
   * Se estiver a utilizar __uDR__: Desloque o tipo __seguinte hop__ da rota para a __Internet__ para os endereços IP.

Para um exemplo de utilização do Azure PowerShell ou do Azure CLI para criar NSGs, consulte o [Documento Extend HDInsight com redes virtuais Azure.](hdinsight-create-virtual-network.md#hdinsight-nsg)

## <a name="create-the-hdinsight-cluster"></a>Criar o cluster HDInsight

> [!WARNING]  
> Tem de configurar o servidor DNS personalizado antes de instalar o HDInsight na rede virtual.

Utilize os passos no [cluster Create a HDInsight utilizando o](./hdinsight-hadoop-create-linux-clusters-portal.md) documento do portal Azure para criar um cluster HDInsight.

> [!WARNING]  
> * Durante a criação do cluster, você deve escolher o local que contém a sua rede virtual.
> * Nas __configurações avançadas,__ a parte da configuração, deve selecionar a rede virtual e a sub-rede que criou anteriormente.

## <a name="connecting-to-hdinsight"></a>Ligação ao HDInsight

A maioria da documentação no HDInsight assume que você tem acesso ao cluster através da internet. Por exemplo, que pode ligar ao cluster em `https://CLUSTERNAME.azurehdinsight.net`. Este endereço utiliza o portal público, que não está disponível se tiver usado NSGs ou UDRs para restringir o acesso a partir da internet.

Alguma documentação `headnodehost` também refere-se ao ligar-se ao cluster a partir de uma sessão de SSH. Este endereço só está disponível a partir de nós dentro de um cluster, e não é utilizável em clientes conectados através da rede virtual.

Para ligar diretamente ao HDInsight através da rede virtual, utilize os seguintes passos:

1. Para descobrir os nomes internos de domínio totalmente qualificados dos nós de cluster HDInsight, utilize um dos seguintes métodos:

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

2. Para determinar a porta em que está disponível um serviço, consulte as [Portas utilizadas pelos serviços Apache Hadoop no documento HDInsight.](./hdinsight-hadoop-port-settings-for-services.md)

    > [!IMPORTANT]  
    > Alguns serviços hospedados nos nóóis de cabeça só estão ativos num nó de cada vez. Se tentar aceder a um serviço num nó de cabeça e falhar, mude para o outro nó da cabeça.
    >
    > Por exemplo, Apache Ambari só está ativo num nó de cabeça de cada vez. Se tentar aceder a Ambari num nó de cabeça e devolver um erro de 404, então está a correr no outro nó da cabeça.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a utilização do HDInsight numa rede virtual, consulte Plan uma implementação de [rede virtual para clusters Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

* Para obter mais informações sobre as redes virtuais do Azure, consulte a [visão geral da Rede Virtual Azure.](../virtual-network/virtual-networks-overview.md)

* Para obter mais informações sobre grupos de segurança da rede, consulte [grupos](../virtual-network/security-overview.md)de segurança da rede .

* Para obter mais informações sobre as rotas definidas pelo utilizador, consulte as rotas definidas pelo utilizador e o [reencaminhamento ip](../virtual-network/virtual-networks-udr-overview.md).
