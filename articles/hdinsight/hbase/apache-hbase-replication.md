---
title: Replicação de cluster HBase em redes virtuais – Azure HDInsight
description: Saiba como configurar a replicação do HBase de uma versão do HDInsight para outra para balanceamento de carga, alta disponibilidade, migração e atualizações sem tempo de inatividade e recuperação de desastres.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 1e6465584dd4e67f736b94d2939678c1a69163bf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435671"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Configurar a replicação de cluster do Apache HBase em redes virtuais do Azure

Saiba como configurar a replicação do [Apache HBase](https://hbase.apache.org/) em uma rede virtual ou entre duas redes virtuais no Azure.

A replicação de cluster usa uma metodologia de envio por push de origem. Um cluster HBase pode ser uma origem ou um destino ou pode atender às duas funções ao mesmo tempo. A replicação é assíncrona. O objetivo da replicação é a consistência eventual. Quando a origem recebe uma edição para uma família de colunas quando a replicação é habilitada, a edição é propagada para todos os clusters de destino. Quando os dados são replicados de um cluster para outro, o cluster de origem e todos os clusters que já consumiram os dados são rastreados para evitar loops de replicação.

Neste artigo, você configura uma replicação de origem/destino. Para outras topologias de cluster, consulte o [Guia de referência do Apache HBase](https://hbase.apache.org/book.html#_cluster_replication).

Estes são os casos de uso de replicação do HBase para uma única rede virtual:

* Balanceamento de carga. Por exemplo, você pode executar verificações ou trabalhos MapReduce no cluster de destino e ingerir dados no cluster de origem.
* Adicionando alta disponibilidade.
* Migrar dados de um cluster HBase para outro.
* Atualizando um cluster HDInsight do Azure de uma versão para outra.

Veja a seguir os casos de uso de replicação do HBase para duas redes virtuais:

* Configurando a recuperação de desastre.
* Balanceamento de carga e particionamento do aplicativo.
* Adicionando alta disponibilidade.

Você pode replicar clusters usando scripts de [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) do [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter uma assinatura do Azure. Consulte [obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Configurar os ambientes

Você tem três opções de configuração:

- Dois clusters Apache HBase em uma rede virtual do Azure.
- Dois clusters Apache HBase em duas redes virtuais diferentes na mesma região.
- Dois clusters do Apache HBase em duas redes virtuais diferentes em duas regiões diferentes (replicação geográfica).

Este artigo aborda o cenário de replicação geográfica.

Para ajudá-lo a configurar os ambientes, criamos alguns [modelos de Azure Resource Manager](../../azure-resource-manager/management/overview.md). Se você preferir configurar os ambientes usando outros métodos, consulte:

- [Criar clusters Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Criar clusters do Apache HBase na rede virtual do Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Configurar duas redes virtuais em duas regiões diferentes

Para usar um modelo que cria duas redes virtuais em duas regiões diferentes e a conexão VPN entre o VNets, selecione o botão **implantar no Azure** a seguir. A definição do modelo é armazenada em um [armazenamento de BLOBs público](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Alguns dos valores embutidos em código no modelo:

**VNet 1**

| Propriedade | Valor |
|----------|-------|
| Localização | E.U.A. Oeste |
| Nome da VNet | &lt;ClusterNamePrevix>-vnet1 |
| Prefixo de espaço de endereço | 10.1.0.0/16 |
| Nome da sub-rede | sub-rede 1 |
| Prefixo de sub-rede | 10.1.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não pode ser alterado) |
| Prefixo de sub-rede (gateway) | 10.1.255.0/27 |
| Nome do gateway | vnet1gw |
| Tipo de gateway | Vpn |
| Tipo de VPN do gateway | RouteBased |
| SKU do gateway | Basic |
| IP do gateway | vnet1gwip |

**VNet 2**

| Propriedade | Valor |
|----------|-------|
| Localização | E.U.A. Leste |
| Nome da VNet | &lt;ClusterNamePrevix>-vnet2 |
| Prefixo de espaço de endereço | 10.2.0.0/16 |
| Nome da sub-rede | sub-rede 1 |
| Prefixo de sub-rede | 10.2.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não pode ser alterado) |
| Prefixo de sub-rede (gateway) | 10.2.255.0/27 |
| Nome do gateway | vnet2gw |
| Tipo de gateway | Vpn |
| Tipo de VPN do gateway | RouteBased |
| SKU do gateway | Basic |
| IP do gateway | vnet1gwip |

## <a name="setup-dns"></a>Configurar DNS

Na última seção, o modelo cria uma máquina virtual Ubuntu em cada uma das duas redes virtuais.  Nesta seção, você instalará o BIND nas duas máquinas virtuais de DNS e, em seguida, configurará o encaminhamento de DNS nas duas máquinas virtuais.

Para instalar o BIND, o Yon precisa localizar o endereço IP público das duas máquinas virtuais de DNS.

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Abra a máquina virtual DNS selecionando **recursos grupos > [nome do grupo de recursos] > [vnet1DNS]** .  O nome do grupo de recursos é aquele que você cria no último procedimento. Os nomes de máquina virtual DNS padrão são *vnet1DNS* e *vnet2NDS*.
3. Selecione **Propriedades** para abrir a página Propriedades da rede virtual.
4. Anote o **endereço IP público**e verifique também o **endereço IP privado**.  O endereço IP privado deve ser **10.1.0.4** para vnet1DNS e **10.2.0.4** para vnet2DNS.  
5. Altere os servidores DNS para que ambas as redes virtuais usem servidores DNS padrão (fornecidos pelo Azure) para permitir o acesso de entrada e saída para baixar pacotes para instalar o BIND nas etapas a seguir.

Para instalar o BIND, use o seguinte procedimento:

1. Use o SSH para se conectar ao __endereço IP público__ da máquina virtual DNS. O exemplo a seguir se conecta a uma máquina virtual em 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Substitua `sshuser` pela conta de usuário SSH que você especificou ao criar a máquina virtual DNS.

    > [!NOTE]  
    > Há várias maneiras de obter o utilitário `ssh`. No Linux, UNIX e macOS, ele é fornecido como parte do sistema operacional. Se você estiver usando o Windows, considere uma das seguintes opções:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash no Ubuntu no Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Para instalar o BIND, use os seguintes comandos da sessão SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Configure o BIND para encaminhar solicitações de resolução de nomes para o servidor DNS local. Para fazer isso, use o seguinte texto como o conteúdo do arquivo de `/etc/bind/named.conf.options`:

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
    > Substitua os valores na seção `goodclients` pelo intervalo de endereços IP das duas redes virtuais. Esta seção define os endereços dos quais esse servidor DNS aceita solicitações.

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

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    O texto de `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` é o __sufixo DNS__ para essa rede virtual. Guarde este valor, porque vai ser utilizado mais tarde.

    Você também deve descobrir o sufixo DNS do outro servidor DNS. Você precisará dela na próxima etapa.

5. Para configurar o BIND para resolver nomes DNS para recursos na rede virtual, use o seguinte texto como o conteúdo do arquivo de `/etc/bind/named.conf.local`:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Você deve substituir o `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` pelo sufixo DNS da outra rede virtual. E o IP do encaminhador é o endereço IP privado do servidor DNS na outra rede virtual.

    Para editar esse arquivo, use o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Para salvar o arquivo, use __Ctrl + X__, __Y__e, em seguida, __Enter__.

6. Para iniciar a ligação, use o seguinte comando:

    ```bash
    sudo service bind9 restart
    ```

7. Para verificar se o BIND pode resolver os nomes dos recursos na outra rede virtual, use os seguintes comandos:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Substitua `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` pelo FQDN (nome de domínio totalmente qualificado) da máquina virtual DNS na outra rede.
    >
    > Substitua `10.2.0.4` pelo __endereço IP interno__ do seu servidor DNS personalizado na outra rede virtual.

    A resposta é semelhante ao seguinte texto:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Até agora, você não pode pesquisar o endereço IP da outra rede sem o endereço IP do servidor DNS especificado.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Configurar a rede virtual para usar o servidor DNS personalizado

Para configurar a rede virtual para usar o servidor DNS personalizado em vez do resolvedor recursivo do Azure, use as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), selecione a rede virtual e, em seguida, selecione __servidores DNS__.

2. Selecione __personalizado__e insira o __endereço IP interno__ do servidor DNS personalizado. Por fim, selecione __salvar__.

6. Abra a máquina virtual do servidor DNS em vnet1 e clique em **reiniciar**.  Você deve reiniciar todas as máquinas virtuais na rede virtual para que a configuração de DNS entre em vigor.
7. Repita as etapas configurar o servidor DNS personalizado para vnet2.

Para testar a configuração de DNS, você pode se conectar às duas máquinas virtuais DNS usando SSH e executar ping no servidor DNS da outra rede virtual usando seu nome de host. Se não funcionar, use o seguinte comando para verificar o status do DNS:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Criar clusters do Apache HBase

Crie um cluster do [Apache HBase](https://hbase.apache.org/) em cada uma das duas redes virtuais com a seguinte configuração:

- **Nome do grupo de recursos**: Use o mesmo nome de grupo de recursos que você criou as redes virtuais.
- **Tipo de cluster**: HBase
- **Versão**: HBase 1.1.2 (HDI 3,6)
- **Local**: Use o mesmo local que a rede virtual.  Por padrão, vnet1 é *oeste dos EUA*e Vnet2 é *leste dos EUA*.
- **Armazenamento**: Crie uma nova conta de armazenamento para o cluster.
- **Rede virtual** (de configurações avançadas no Portal): selecione vnet1 que você criou no último procedimento.
- **Sub-rede**: o nome padrão usado no modelo é **subnet1**.

Para garantir que o ambiente esteja configurado corretamente, você deve ser capaz de executar ping no FQDN do cabeçalho entre os dois clusters.

## <a name="load-test-data"></a>Carregar os dados de teste

Ao replicar um cluster, você deve especificar as tabelas que deseja replicar. Nesta seção, você carrega alguns dados no cluster de origem. Na próxima seção, você habilitará a replicação entre os dois clusters.

Para criar uma tabela de **contatos** e inserir alguns dados na tabela, siga as instruções em [tutorial do Apache HBase: introdução ao uso do Apache HBase no HDInsight](apache-hbase-tutorial-get-started-linux.md).

> [!NOTE]
> Se você quiser replicar tabelas de um namespace personalizado, será necessário garantir que os namespaces personalizados apropriados também sejam definidos no cluster de destino.
>

## <a name="enable-replication"></a>Ativar a replicação

As etapas a seguir descrevem como chamar o script de ação de script do portal do Azure. Para obter informações sobre como executar uma ação de script usando Azure PowerShell e a CLI clássica do Azure, consulte [Personalizar clusters HDInsight usando a ação de script](../hdinsight-hadoop-customize-cluster-linux.md).

**Para habilitar a replicação do HBase no portal do Azure**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HBase de origem.
3. No menu do cluster, selecione **ações de script**.
4. Na parte superior da página, selecione **Enviar novo**.
5. Selecione ou insira as seguintes informações:

   1. **Nome**: insira **habilitar replicação**.
   2. **URL do script bash**: insira **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh** .
   3. **Cabeçalho**: Verifique se isso está selecionado. Desmarque os outros tipos de nó.
   4. **Parâmetros**: os seguintes parâmetros de exemplo habilitam a replicação para todas as tabelas existentes e, em seguida, copiam todos os dados do cluster de origem para o cluster de destino:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > Use hostname em vez de FQDN para o nome DNS do cluster de origem e de destino.
      >
      > Este tutorial pressupõe o hn1 como ativo cabeçalho. Verifique o cluster para identificar o nó principal ativo.

6. Selecione **Criar**. O script pode levar algum tempo para ser executado, especialmente quando você usa o argumento **-CopyData** .

Argumentos necessários:

|Nome|Descrição|
|----|-----------|
|-s,--src-cluster | Especifica o nome DNS do cluster HBase de origem. Por exemplo:-s hbsrccluster,--src-cluster = hbsrccluster |
|-d,--DST-cluster | Especifica o nome DNS do cluster HBase de destino (réplica). Por exemplo:-s dsthbcluster,--src-cluster = dsthbcluster |
|-SP,--src-ambari-Password | Especifica a senha de administrador para Ambari no cluster HBase de origem. |
|-DP,--DST-ambari-Password | Especifica a senha de administrador para Ambari no cluster HBase de destino.|

Argumentos opcionais:

|Nome|Descrição|
|----|-----------|
|-Su,--src-ambari-usuário | Especifica o nome de usuário administrador para Ambari no cluster HBase de origem. O valor padrão é **admin**. |
|-du,--DST-ambari-User | Especifica o nome de usuário administrador para Ambari no cluster HBase de destino. O valor padrão é **admin**. |
|-t,--tabela-lista | Especifica as tabelas a serem replicadas. Por exemplo:--Table-List = "Table1; Table2; Table3". Se você não especificar tabelas, todas as tabelas HBase existentes serão replicadas.|
|-m,--máquina | Especifica o nó de cabeçalho onde a ação de script é executada. O valor deve ser escolhido com base em qual é o nó principal ativo. Use esta opção quando estiver executando o script $0 como uma ação de script no portal do HDInsight ou Azure PowerShell.|
|-cp, -copydata | Habilita a migração de dados existentes nas tabelas em que a replicação está habilitada. |
|-rpm,-replicate-Phoenix-meta | Habilita a replicação em tabelas do sistema Phoenix. <br><br>*Use essa opção com cuidado.* Recomendamos que você recrie tabelas Phoenix em clusters de réplicas antes de usar esse script. |
|-h,--ajuda | Exibe informações de uso. |

A seção `print_usage()` do [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) tem uma explicação detalhada dos parâmetros.

Depois que a ação de script for implantada com êxito, você poderá usar o SSH para se conectar ao cluster HBase de destino e, em seguida, verificar se os dados foram replicados.

### <a name="replication-scenarios"></a>Cenários de replicação

A lista a seguir mostra alguns casos de uso geral e suas configurações de parâmetro:

- **Habilite a replicação em todas as tabelas entre os dois clusters**. Esse cenário não requer a cópia ou a migração de dados existentes nas tabelas e não usa tabelas Phoenix. Use os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Habilite a replicação em tabelas específicas**. Para habilitar a replicação em Table1, Table2 e Table3, use os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Habilite a replicação em tabelas específicas e copie os dados existentes**. Para habilitar a replicação em Table1, Table2 e Table3, use os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Habilite a replicação em todas as tabelas e replique metadados de Phoenix da origem para o destino**. A replicação de metadados Phoenix não é perfeita. Use-o com cuidado. Use os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copiar e migrar dados

Há dois scripts de ação de script separados disponíveis para copiar ou migrar dados após a replicação ser habilitada:

- [Script para tabelas pequenas](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabelas que têm alguns gigabytes de tamanho e espera-se que a cópia geral seja concluída em menos de uma hora)

- [Script para tabelas grandes](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabelas que devem levar mais de uma hora para serem copiadas)

Você pode seguir o mesmo procedimento descrito em habilitar a [replicação](#enable-replication) para chamar a ação de script. Use os seguintes parâmetros:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

A seção `print_usage()` do [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) tem uma descrição detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Copiar tabelas específicas (Test1, test2 e test3) para todas as linhas editadas até agora (carimbo de data/hora atual)** :

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Or

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copiar tabelas específicas com um intervalo de tempo especificado**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Desativar a replicação

Para desabilitar a replicação, use outro script de ação de script do [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Você pode seguir o mesmo procedimento descrito em habilitar a [replicação](#enable-replication) para chamar a ação de script. Use os seguintes parâmetros:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

A seção `print_usage()` do [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) tem uma explicação detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Desabilitar a replicação em todas as tabelas**:

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  ou

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Desabilitar a replicação nas tabelas especificadas (Table1, Table2 e Table3)** :

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

> [!NOTE]
> Se você pretende excluir o cluster de destino, certifique-se de removê-lo da lista de pares do cluster de origem. Isso pode ser feito executando o comando remove_peer ' 1 ' no Shell do HBase no cluster de origem. Com falha, o cluster de origem pode não funcionar corretamente.
>

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a configurar a replicação do Apache HBase em uma rede virtual ou entre duas redes virtuais. Para saber mais sobre o HDInsight e o Apache HBase, consulte estes artigos:

* [Introdução ao Apache HBase no HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Visão geral do HDInsight Apache HBase](./apache-hbase-overview.md)
* [Criar clusters do Apache HBase na rede virtual do Azure](./apache-hbase-provision-vnet.md)

