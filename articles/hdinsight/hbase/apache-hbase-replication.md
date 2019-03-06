---
title: Configurar a replicação de cluster HBase em redes virtuais do Azure - Azure HDInsight
description: Saiba como configurar a replicação do HBase de uma versão do HDInsight para outro para balanceamento de carga, elevada disponibilidade, sem período de indisponibilidade migração e as atualizações e recuperação após desastre.
services: hdinsight,virtual-network
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: 52b52cce1e93e55563cf695f06bd7821ebcfc585
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444910"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Configurar a replicação de cluster do Apache HBase em redes virtuais do Azure

Saiba como configurar [Apache HBase](https://hbase.apache.org/) replicação numa rede virtual, ou entre duas redes virtuais no Azure.

A replicação de cluster utiliza uma metodologia de push de origem. Um cluster do HBase pode ser uma origem ou destino ou possa preencher ambas as funções de uma só vez. A replicação é assíncrona. O objetivo da replicação é a consistência eventual. Quando a origem recebe uma edição de uma família de colunas, quando a replicação é ativada, a edição será propagada a todos os clusters de destino. Quando os dados são replicados de um cluster para outro, o cluster de origem e de todos os clusters que já tem consumido os dados são controlados, para impedir que os ciclos de replicação.

Neste tutorial, configurou uma replicação de origem-destino. Para outras topologias de cluster, consulte a [guia de referência Apache HBase](https://hbase.apache.org/book.html#_cluster_replication).

Seguem-se casos de utilização de replicação de HBase para uma única rede virtual:

* Balanceamento de carga. Por exemplo, pode executar análises ou tarefas de MapReduce no cluster de destino e ingestão de dados no cluster de origem.
* Adição de elevada disponibilidade.
* Migrar dados de um cluster do HBase para outro.
* Atualizar um cluster de HDInsight do Azure de uma versão para outra.

Seguem-se casos de utilização de replicação de HBase para duas redes virtuais:

* Configuração da recuperação após desastre.
* Balanceamento de carga e o aplicativo de criação de partições.
* Adição de elevada disponibilidade.

Pode replicar clusters utilizando [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) scripts a partir de [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter uma subscrição do Azure. Ver [obtenha uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Configurar os ambientes

Tem três opções de configuração:

- Dois clusters do Apache HBase numa rede virtual do Azure.
- Dois clusters do Apache HBase em duas redes virtuais diferentes na mesma região.
- Dois clusters do Apache HBase em duas redes virtuais diferentes em duas regiões diferentes (georreplicação).

Este artigo aborda o cenário de georreplicação.

Para ajudá-lo a configurar os ambientes, criámos algumas [modelos Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Se preferir configurar os ambientes com outros métodos, consulte:

- [Criar clusters do Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Criar clusters do Apache HBase numa rede Virtual do Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Configurar as duas redes virtuais em duas regiões diferentes

Para utilizar um modelo que cria duas redes virtuais em duas regiões diferentes e a ligação VPN entre as VNets, selecione as seguintes **implementar no Azure** botão. A definição do modelo é armazenada num [armazenamento de BLOBs público](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Alguns dos valores codificados no modelo:

**VNet 1**

| Propriedade | Value |
|----------|-------|
| Localização | EUA Oeste |
| Nome da VNet | &lt;ClusterNamePrevix>-vnet1 |
| Prefixo de espaço de endereço | 10.1.0.0/16 |
| Nome da sub-rede | sub-rede 1 |
| Prefixo de sub-rede | 10.1.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não é possível alterar) |
| Prefixo de sub-rede (gateway) | 10.1.255.0/27 |
| Nome do gateway | vnet1gw |
| Tipo de gateway | Vpn |
| Tipo de gateway de VPN | RouteBased |
| SKU de gateway | Básica |
| Gateway IP | vnet1gwip |

**VNet 2**

| Propriedade | Value |
|----------|-------|
| Localização | EUA Leste |
| Nome da VNet | &lt;ClusterNamePrevix>-vnet2 |
| Prefixo de espaço de endereço | 10.2.0.0/16 |
| Nome da sub-rede | sub-rede 1 |
| Prefixo de sub-rede | 10.2.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não é possível alterar) |
| Prefixo de sub-rede (gateway) | 10.2.255.0/27 |
| Nome do gateway | vnet2gw |
| Tipo de gateway | Vpn |
| Tipo de gateway de VPN | RouteBased |
| SKU de gateway | Básica |
| Gateway IP | vnet1gwip |

## <a name="setup-dns"></a>Configuração do DNS

Na última seção, o modelo cria uma máquina virtual do Ubuntu em cada uma das duas redes virtuais.  Nesta secção, instalar o enlace a duas máquinas de virtuais DNS e, em seguida, configure o encaminhamento de DNS nas duas máquinas virtuais.

Para instalar o enlace, yon tem de encontrar o endereço IP público de duas máquinas de virtuais DNS.

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Abra a máquina virtual DNS, selecionando **grupos de recursos > [nome do grupo de recursos] > [vnet1DNS]**.  O nome do grupo de recursos é criado no último procedimento. Os nomes de máquina virtual DNS padrão são *vnet1DNS* e *vnet2NDS*.
3. Selecione **propriedades** para abrir a página de propriedades da rede virtual.
4. Anote o **endereço IP público**e também verificar se o **endereço IP privado**.  Deve ser o endereço IP privado **10.1.0.4** para vnet1DNS e **10.2.0.4** para vnet2DNS.  
5. Altere os servidores de DNS para ambas as redes virtuais para utilizar servidores DNS de predefinição (fornecida pelo Azure) para permitir o acesso de entrada e saído transferir pacotes a serem instalados Bind nos passos seguintes.

Para instalar o enlace, utilize o seguinte procedimento:

1. Utilize SSH para ligar para o __endereço IP público__ da máquina de virtual DNS. O exemplo seguinte liga a uma máquina virtual em 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Substitua `sshuser` com a conta de utilizador SSH que especificou ao criar a máquina virtual DNS.

    > [!NOTE]  
    > Existem diversas formas de obter o `ssh` utilitário. No Linux, Unix e macOS, ele é fornecido como parte do sistema operativo. Se estiver a utilizar o Windows, considere uma das seguintes opções:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash no Ubuntu no Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Para instalar o enlace, utilize os seguintes comandos a partir da sessão SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Configure o enlace para reencaminhar pedidos de resolução de nome ao seu servidor DNS no local. Para tal, utilize o seguinte texto como conteúdo do `/etc/bind/named.conf.options` ficheiro:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > Substitua os valores no `goodclients` secção com o intervalo de endereços IP de duas redes virtuais. Esta secção define os endereços que aceita os pedidos deste servidor DNS.

    Para editar este ficheiro, utilize o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Para guardar o ficheiro, utilize __Ctrl + X__, __Y__e, em seguida __Enter__.

4. A partir da sessão SSH, utilize o seguinte comando:

    ```bash
    hostname -f
    ```

    Este comando devolve um valor semelhante ao seguinte texto:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    O `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` texto é o __sufixo DNS__ para esta rede virtual. Guarde este valor, porque vai ser utilizado mais tarde.

    Também deve descobrir o sufixo DNS do servidor DNS. Precisa no próximo passo.

5. Para configurar o enlace para resolver os nomes DNS para recursos dentro da rede virtual, utilize o seguinte texto como conteúdo do `/etc/bind/named.conf.local` ficheiro:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Tem de substituir o `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` com o sufixo DNS da rede virtual. E o IP de reencaminhador é o endereço IP privado do servidor DNS na rede virtual.

    Para editar este ficheiro, utilize o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Para guardar o ficheiro, utilize __Ctrl + X__, __Y__e, em seguida __Enter__.

6. Para iniciar a ligação, utilize o seguinte comando:

    ```bash
    sudo service bind9 restart
    ```

7. Para verificar essa associação pode resolver os nomes de recursos na rede virtual, utilize os seguintes comandos:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Substitua `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` com o nome de domínio completamente qualificado (FQDN) da máquina virtual DNS na rede de outro.
    >
    > Substitua `10.2.0.4` com o __endereço IP interno__ do seu servidor DNS personalizado na rede virtual.

    A resposta parece ser semelhante ao seguinte texto:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Até agora, não é possível verificar o endereço IP da rede sem o endereço IP do servidor DNS especificado.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Configurar a rede virtual para utilizar o servidor DNS personalizado

Para configurar a rede virtual para utilizar o servidor DNS personalizado em vez do resolvedor recursivo do Azure, utilize os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com), selecione a rede virtual e, em seguida, selecione __servidores DNS__.

2. Selecione __personalizados__e introduza o __endereço IP interno__ do servidor DNS personalizado. Por fim, selecione __guardar__.

6. Abra a máquina de virtual de servidor DNS na vnet1 e clique em **reiniciar**.  Tem de reiniciar todas as máquinas virtuais na rede virtual para que a configuração de DNS entrem em vigor.
7. Repita os passos de configurar o servidor DNS personalizado para a vnet2.

Para testar a configuração de DNS, pode ligar a duas máquinas de virtuais DNS, através de SSH e executar ping no servidor DNS da rede virtual com o respetivo nome de anfitrião. Se não funcionar, utilize o seguinte comando para verificar o estado DNS:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Criar clusters do Apache HBase

Criar uma [Apache HBase](https://hbase.apache.org/) cluster em cada uma das duas redes virtuais com a seguinte configuração:

- **Nome do grupo de recursos**: utilizar o mesmo nome de grupo de recursos à medida que criou as redes virtuais.
- **Tipo de cluster**: HBase
- **Versão**: HBase 1.1.2 (HDI 3.6)
- **Localização**: Utilize a mesma localização que a rede virtual.  Por predefinição, é vnet1 *E.U.A. oeste*, e é vnet2 *E.U.A. Leste*.
- **Armazenamento**: Crie uma nova conta de armazenamento para o cluster.
- **Rede virtual** (a partir de definições avançadas no portal): Selecione vnet1 que criou no último procedimento.
- **Sub-rede**: O nome predefinido utilizado no modelo é **subnet1**.

Para garantir que o ambiente está configurado corretamente, tem de ser capaz de executar ping FQDN do nó principal entre dois clusters.

## <a name="load-test-data"></a>Carregar os dados de teste

Quando replicar um cluster, tem de especificar as tabelas que pretende replicar. Nesta seção, carrega alguns dados para o cluster de origem. Na secção seguinte, irá ativar a replicação entre dois clusters.

Para criar uma **contactos** da tabela e inserir alguns dados na tabela, siga as instruções em [tutorial do Apache HBase: Introdução ao Apache HBase no HDInsight](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Ativar a replicação

Os passos seguintes descrevem como chamar o script de ação de script a partir do portal do Azure. Para obter informações sobre como executar uma ação de script com o Azure PowerShell e CLI clássica do Azure, consulte [HDInsight personalizar clusters com ação de script](../hdinsight-hadoop-customize-cluster-linux.md).

**Para ativar a replicação do HBase do portal do Azure**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster do HBase de origem.
3. No menu do cluster, selecione **ações de Script**.
4. Na parte superior da página, selecione **submeter nova**.
5. Selecione ou introduza as seguintes informações:

  1. **Nome**: Introduza **ativar a replicação**.
  2. **URL de Script de bash**: Enter **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **HEAD**: Certifique-se de que esta opção está selecionada. Desmarque os outros tipos de nós.
  4. **Parâmetros**: Os parâmetros de exemplo seguinte ativa a replicação para todas as tabelas existentes e, em seguida, copiar todos os dados do cluster de origem para o cluster de destino:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    > [!NOTE]
    > Utilize o nome de anfitrião em vez do FQDN para o nome DNS de cluster de origem e de destino.

6. Selecione **Criar**. O script pode demorar algum tempo para executar, especialmente quando utiliza a **- copydata** argumento.

Argumentos necessários:

|Name|Descrição|
|----|-----------|
|-s, – src-cluster | Especifica o nome DNS do cluster do HBase de origem. Por exemplo: hbsrccluster -s, de cluster – src = hbsrccluster |
|-d, de cluster de – o horário de Verão | Especifica o nome DNS do cluster de HBase (réplica) de destino. Por exemplo: dsthbcluster -s, de cluster – src = dsthbcluster |
|-sp, --src-ambari-password | Especifica a palavra-passe de administrador para Ambari no cluster do HBase de origem. |
|-dp, --dst-ambari-password | Especifica a palavra-passe de administrador para Ambari no cluster do HBase de destino.|

Argumentos opcionais:

|Name|Descrição|
|----|-----------|
|-su, – src-ambari-utilizador | Especifica o nome de utilizador de administrador para Ambari no cluster do HBase de origem. O valor predefinido é **administrador**. |
|-du, – o horário de Verão-ambari-utilizador | Especifica o nome de utilizador de administrador para Ambari no cluster do HBase de destino. O valor predefinido é **administrador**. |
|-t, – lista de tabelas | Especifica as tabelas a serem replicados. Por exemplo: – lista de tabelas = "tabela1; table2; table3". Se não especificar tabelas, todas as tabelas HBase existentes são replicadas.|
|-m, --machine | Especifica o nó principal em que a ação de script é executado. O valor é **hn0** ou **hn1** e deve ser escolhida com base em que é o nó principal do Active Directory. Utilize esta opção se de que está a executar o script de US $0 como uma ação de script do portal do HDInsight ou o Azure PowerShell.|
|-cp, -copydata | Permite a migração de dados existentes nas tabelas de onde os replicação está ativada. |
|-rpm, - replicate-phoenix-meta | Permite a replicação em tabelas de sistema de Phoenix. <br><br>*Utilize esta opção com cuidado.* Recomendamos que voltar a criar tabelas de Phoenix em clusters de réplica antes de utilizar este script. |
|-h, --help | Mostra informações de utilização. |

O `print_usage()` secção do [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) tem uma explicação detalhada dos parâmetros.

Depois da ação de script é implementada com êxito, pode utilizar o SSH para ligar para o cluster do HBase de destino e, em seguida, certifique-se de que os dados foram replicados.

### <a name="replication-scenarios"></a>Cenários de replicação

A lista seguinte mostra alguns casos de utilização geral e suas configurações de parâmetro:

- **Ativar a replicação em todas as tabelas entre dois clusters**. Este cenário não é necessário copiar ou migração de dados existentes nas tabelas e não utiliza as tabelas de Phoenix. Utilize os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Ativar a replicação em tabelas específicas**. Para ativar a replicação em table1, table2 e table3, utilize os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Ativar a replicação em tabelas específicas e copie os dados existentes**. Para ativar a replicação em table1, table2 e table3, utilize os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Ativar a replicação em todas as tabelas e replicar Phoenix metadados da origem para destino**. Replicação de metadados de Phoenix não é perfeita. Utilize-o com cuidado. Utilize os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copie e migrar dados

Dois scripts de ação de script separada estão disponíveis para copiar ou a migrar os dados depois de ativar a replicação:

- [Script para tabelas pequenas](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (as tabelas que são poucos gigabytes na cópia de tamanho e geral é esperado termina em menos de uma hora)

- [Script para tabelas grandes](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabelas que devem demorar mais de uma hora para copiar)

Pode seguir o mesmo procedimento descrito [ativar a replicação](#enable-replication) para chamar a ação de script. Utilize os seguintes parâmetros:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

O `print_usage()` secção do [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) tem uma descrição detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Copiar tabelas específicas (test1, test2 e test3) para todas as linhas editadas até agora (carimbo de hora atual)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Ou:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copiar tabelas específicas com um intervalo de tempo especificado**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Desativar a replicação

Para desativar a replicação, utilize outro script de ação de script da [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Pode seguir o mesmo procedimento descrito [ativar a replicação](#enable-replication) para chamar a ação de script. Utilize os seguintes parâmetros:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

O `print_usage()` secção do [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) tem uma explicação detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Desative a replicação em todas as tabelas**:

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  ou

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Desative a replicação em tabelas especificadas (table1, table2 e table3)**:

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a configurar a replicação de Apache HBase numa rede virtual, ou entre duas redes virtuais. Para saber mais sobre o HDInsight e o Apache HBase, veja estes artigos:

* [Introdução ao Apache HBase no HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Descrição geral do Apache HBase do HDInsight](./apache-hbase-overview.md)
* [Criar clusters do Apache HBase numa rede Virtual do Azure](./apache-hbase-provision-vnet.md)

