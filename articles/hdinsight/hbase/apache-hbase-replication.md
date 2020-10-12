---
title: Replicação do cluster HBase em redes virtuais - Azure HDInsight
description: Aprenda a configurar a replicação HBase de uma versão HDInsight para outra para equilibrar carga, alta disponibilidade, migração e atualizações de tempo zero e recuperação de desastres.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: cf080f2a6173651fce8f306619dba60347067e0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085617"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Configurar a replicação do cluster Apache HBase em redes virtuais Azure

Saiba como configurar a replicação [Apache HBase](https://hbase.apache.org/) dentro de uma rede virtual, ou entre duas redes virtuais em Azure.

A replicação do cluster utiliza uma metodologia de impulso de origem. Um cluster HBase pode ser uma fonte ou um destino, ou pode cumprir ambas as funções ao mesmo tempo. A replicação é assíncronea. O objetivo da replicação é a eventual consistência. Quando a fonte recebe uma edição para uma família de colunas quando a replicação está ativada, a edição é propagada a todos os clusters de destino. Quando os dados são replicados de um cluster para outro, o cluster de origem e todos os clusters que já consumiram os dados são rastreados, para evitar ciclos de replicação.

Neste artigo, configura-se uma replicação de destino de origem. Para outras topologias de cluster, consulte o [guia de referência Apache HBase](https://hbase.apache.org/book.html#_cluster_replication).

Seguem-se os casos de utilização da replicação HBase para uma única rede virtual:

* Balanceamento de carga. Por exemplo, você pode executar digitalizações ou trabalhos MapReduce no cluster de destino, e ingerir dados no cluster de origem.
* Adicionando alta disponibilidade.
* Dados migratórios de um cluster HBase para outro.
* Atualização de um cluster Azure HDInsight de uma versão para outra.

Seguem-se os casos de utilização de replicação HBase para duas redes virtuais:

* A preparar a recuperação de desastres.
* Carga de equilíbrio e divisão da aplicação.
* Adicionando alta disponibilidade.

Pode replicar clusters usando scripts de ação de [scripts](../hdinsight-hadoop-customize-cluster-linux.md) do [GitHub.](https://github.com/Azure/hbase-utils/tree/master/replication)

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar este artigo, deve ter uma assinatura Azure. Consulte [um julgamento gratuito do Azure.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

## <a name="set-up-the-environments"></a>Configurar os ambientes

Tem três opções de configuração:

- Dois aglomerados Apache HBase numa rede virtual Azure.
- Dois aglomerados Apache HBase em duas redes virtuais diferentes na mesma região.
- Dois aglomerados Apache HBase em duas redes virtuais diferentes em duas regiões diferentes (geo-replicação).

Este artigo cobre o cenário de geo-replicação.

Para ajudá-lo a configurar os ambientes, criámos alguns [modelos do Azure Resource Manager.](../../azure-resource-manager/management/overview.md) Se preferir configurar os ambientes utilizando outros métodos, consulte:

- [Criar clusters Apache Hadoop em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Criar clusters Apache HBase em Rede Virtual Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Criar duas redes virtuais em duas regiões diferentes

Para utilizar um modelo que cria duas redes virtuais em duas regiões diferentes e a ligação VPN entre as VNets, selecione o seguinte botão **Implementar para Azure.** A definição do modelo é armazenada num [armazenamento de bolhas públicas.](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json)

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Alguns dos valores codificados no modelo:

**VNet 1**

| Propriedade | Valor |
|----------|-------|
| Localização | E.U.A. Oeste |
| Nome VNet | &lt;ClusterNamePrevix>-vnet1 |
| Prefixo do espaço do endereço | 10.1.0.0/16 |
| Nome da sub-rede | sub-rede 1 |
| Prefixo de sub-rede | 10.1.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não pode ser alterado) |
| Prefixo de sub-rede (gateway) | 10.1.255.0/27 |
| Nome gateway | vnet1gw |
| Tipo de gateway | Vpn |
| Tipo gateway VPN | RouteBased |
| Gateway SKU | Básico |
| Gateway IP | vnet1gwip |

**VNet 2**

| Propriedade | Valor |
|----------|-------|
| Localização | E.U.A Leste |
| Nome VNet | &lt;ClusterNamePrevix>-vnet2 |
| Prefixo do espaço do endereço | 10.2.0.0/16 |
| Nome da sub-rede | sub-rede 1 |
| Prefixo de sub-rede | 10.2.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não pode ser alterado) |
| Prefixo de sub-rede (gateway) | 10.2.255.0/27 |
| Nome gateway | vnet2gw |
| Tipo de gateway | Vpn |
| Tipo gateway VPN | RouteBased |
| Gateway SKU | Básico |
| Gateway IP | vnet1gwip |

## <a name="setup-dns"></a>Configurar DNS

Na última secção, o modelo cria uma máquina virtual Ubuntu em cada uma das duas redes virtuais.  Nesta secção, instala-se o Bind nas duas máquinas virtuais DNS e, em seguida, configura o encaminhamento dns nas duas máquinas virtuais.

Para instalar o Bind, o yon precisa de encontrar o endereço IP público das duas máquinas virtuais DNS.

1. Abra o [portal do Azure](https://portal.azure.com).
2. Abra a máquina virtual DNS selecionando **grupos de Recursos > [nome do grupo de recursos] > [vnet1DNS]**.  O nome do grupo de recursos é o que se cria no último procedimento. Os nomes de máquinas virtuais DNS predefinidos são *vnet1DNS* e *vnet2NDS*.
3. Selecione **Propriedades** para abrir a página de propriedades da rede virtual.
4. Anota o **endereço IP público**e verifique também o endereço IP **privado**.  O endereço IP privado deve ser **10.1.0.4** para vnet1DNS e **10.2.0.4** para vnet2DNS.  
5. Altere os Servidores DNS para que ambas as redes virtuais utilizem servidores DNS predefinidos (fornecido por Azure) para permitir o acesso de entrada e saída a pacotes de descarregamento para instalar o Bind nos seguintes passos.

Para instalar o Bind, utilize o seguinte procedimento:

1. Utilize o SSH para ligar ao __endereço IP público__ da máquina virtual DNS. O exemplo a seguir liga-se a uma máquina virtual a 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    `sshuser`Substitua-a pela conta de utilizador SSH especificada ao criar a máquina virtual DNS.

    > [!NOTE]  
    > Há uma variedade de maneiras de obter a `ssh` utilidade. No Linux, Unix e macOS, é fornecido como parte do sistema operativo. Se estiver a utilizar o Windows, considere uma das seguintes opções:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash on Ubuntu no Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Para instalar o Bind, utilize os seguintes comandos da sessão SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Configure o Bind para encaminhar pedidos de resolução de nomes para o seu servidor DNS nas instalações. Para tal, utilize o seguinte texto como o conteúdo do `/etc/bind/named.conf.options` ficheiro:

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
    > Substitua os valores na `goodclients` secção pelo intervalo de endereços IP das duas redes virtuais. Esta secção define os endereços de que este servidor DNS aceita pedidos.

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
    vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    O `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` texto é o __sufixo DNS__ para esta rede virtual. Guarde este valor, porque vai ser utilizado mais tarde.

    Também tem de descobrir o sufixo DNS do outro servidor DNS. Precisa no próximo passo.

5. Para configurar o Bind para resolver os nomes dns para recursos dentro da rede virtual, utilize o seguinte texto como conteúdo do `/etc/bind/named.conf.local` ficheiro:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Deve substituir o `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` sufixo DNS da outra rede virtual. E o IP do reencaminhador é o endereço IP privado do servidor DNS na outra rede virtual.

    Para editar este ficheiro, utilize o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Para guardar o ficheiro, utilize __Ctrl+X,__ __Y__, e, em seguida, __Insira__.

6. Para iniciar o Bind, utilize o seguinte comando:

    ```bash
    sudo service bind9 restart
    ```

7. Para verificar se a ligação pode resolver os nomes dos recursos na outra rede virtual, utilize os seguintes comandos:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net`Substitua-a pelo nome de domínio totalmente qualificado (FQDN) da máquina virtual DNS na outra rede.
    >
    > `10.2.0.4`Substitua-o pelo __endereço IP interno__ do seu servidor DNS personalizado na outra rede virtual.

    A resposta parece semelhante ao seguinte texto:

    ```output
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Até agora, não é possível procurar o endereço IP da outra rede sem o endereço IP do servidor DNS especificado.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Configure a rede virtual para utilizar o servidor DNS personalizado

Para configurar a rede virtual para utilizar o servidor DNS personalizado em vez do Azure recursive resolver, utilize os seguintes passos:

1. No [portal Azure](https://portal.azure.com), selecione a rede virtual e, em seguida, selecione __Servidores DNS__.

2. Selecione __Custom__e insira o __endereço IP interno__ do servidor DNS personalizado. Por fim, __selecione Save__.

6. Abra a máquina virtual do servidor DNS em vnet1 e clique em **Reiniciar**.  Tem de reiniciar todas as máquinas virtuais da rede virtual para que a configuração do DNS produza efeitos.
7. Repita os passos configurar o servidor DNS personalizado para vnet2.

Para testar a configuração de DNS, pode ligar-se às duas máquinas virtuais DNS utilizando O SSH e pingar o servidor DNS da outra rede virtual utilizando o seu nome de anfitrião. Se não funcionar, utilize o seguinte comando para verificar o estado do DNS:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Criar clusters Apache HBase

Criar um cluster [Apache HBase](https://hbase.apache.org/) em cada uma das duas redes virtuais com a seguinte configuração:

- **Nome do grupo de**recursos : use o mesmo nome de grupo de recursos que criou as redes virtuais.
- **Tipo de cluster**: HBase
- **Versão**: HBase 1.1.2 (HDI 3.6)
- **Localização**: Utilize a mesma localização que a rede virtual.  Por defeito, vnet1 é *Oeste dos EUA,* e vnet2 é *Leste dos EUA.*
- **Armazenamento**: Criar uma nova conta de armazenamento para o cluster.
- **Rede virtual** (a partir de definições avançadas no portal): Selecione vnet1 que criou no último procedimento.
- **Sub-rede**: O nome predefinido utilizado no modelo é **sub-rede1**.

Para garantir que o ambiente está configurado corretamente, deve ser capaz de pingar o FQDN do headnode entre os dois clusters.

## <a name="load-test-data"></a>Carregar os dados de teste

Quando replica um cluster, tem de especificar as tabelas que pretende replicar. Nesta secção, você carrega alguns dados no cluster de origem. Na secção seguinte, permitirá a replicação entre os dois clusters.

Para criar uma tabela de Contactos e inserir **alguns dados** na tabela, siga as instruções no [tutorial Apache HBase: Começa a utilizar o Apache HBase em HDInsight](apache-hbase-tutorial-get-started-linux.md).

> [!NOTE]
> Se quiser replicar tabelas a partir de um espaço de nome personalizado, precisa garantir que os espaços de nome personalizados apropriados também são definidos no cluster de destino.
>

## <a name="enable-replication"></a>Ativar a replicação

Os seguintes passos descrevem como chamar o script de ação do script do portal Azure. Para obter informações sobre a execução de uma ação de script utilizando a Azure PowerShell e o Azure Classic CLI, consulte [os clusters HdInsight personalizos através da ação do script](../hdinsight-hadoop-customize-cluster-linux.md).

**Para ativar a replicação da HBase a partir do portal Azure**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster HBase de fonte.
3. No menu de cluster, selecione **Script Actions**.
4. No topo da página, **selecione Submeter Novo**.
5. Selecione ou introduza as seguintes informações:

   1. **Nome**: **Insira Ativar a replicação**.
   2. **URL do Roteiro de Bash**: Insira **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh** .
   3. **Cabeça**: Certifique-se de que esta está selecionada. Limpe os outros tipos de nós.
   4. **Parâmetros**: Os seguintes parâmetros da amostra permitem a replicação de todas as tabelas existentes e, em seguida, copiam todos os dados do cluster de origem para o cluster de destino:

    `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata`
    
      > [!NOTE]
      > Use o nome de hospedeiro em vez de FQDN para o nome DNS de origem e cluster de destino.
      >
      > Este walkthrough assume hn1 como headnode ativo. Verifique o seu cluster para identificar o nó de cabeça ativo.

6. Selecione **Criar**. O script pode demorar algum tempo a ser executado, especialmente quando se utiliza o argumento **de dados de cópia.**

Argumentos necessários:

|Nome|Descrição|
|----|-----------|
|-s, --src-cluster | Especifica o nome DNS do cluster HBase de origem. Por exemplo: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Especifica o nome DNS do cluster HBase de destino (réplica). Por exemplo: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Especifica a palavra-passe de administrador para Ambari no cluster HBase de origem. |
|-dp, --dst-ambari-password | Especifica a palavra-passe de administrador para Ambari no cluster HBase de destino.|

Argumentos opcionais:

|Nome|Descrição|
|----|-----------|
|-su, --src-ambari-user | Especifica o nome de utilizador administrativo para Ambari no cluster HBase de origem. O valor predefinido é **administrador.** |
|-du, --dst-ambari-user | Especifica o nome de utilizador administrativo para Ambari no cluster HBase de destino. O valor predefinido é **administrador.** |
|-t, --tabela-lista | Especifica as tabelas a replicar. Por exemplo: --table-list="table1;table2;table3". Se não especificar tabelas, todas as tabelas HBase existentes são replicadas.|
|-m, -máquina | Especifica o nó da cabeça onde a ação do script é executado. O valor deve ser escolhido com base no qual é o nó de cabeça ativo. Utilize esta opção quando estiver a executar o script de $0 como uma ação de script a partir do portal HDInsight ou Azure PowerShell.|
|-cp, -copydatas | Permite a migração de dados existentes nas tabelas onde a replicação está ativada. |
|-rpm, -replicar-phoenix-meta | Permite a replicação nas tabelas do sistema Phoenix. <br><br>*Utilize esta opção com cuidado.* Recomendamos que reumo as tabelas Phoenix em aglomerados de réplicas antes de utilizar este script. |
|-h, -- ajuda | Exibe informações de utilização. |

A `print_usage()` secção do [guião](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) tem uma explicação detalhada dos parâmetros.

Após a implementação da ação do script com sucesso, pode utilizar o SSH para ligar ao cluster HBase de destino e, em seguida, verificar se os dados foram replicados.

### <a name="replication-scenarios"></a>Cenários de replicação

A lista a seguir mostra-lhe alguns casos gerais de utilização e as definições dos seus parâmetros:

- **Ativar a replicação em todas as tabelas entre os dois agrupamentos.** Este cenário não requer a cópia ou migração dos dados existentes nas tabelas, e não utiliza tabelas Phoenix. Utilize os seguintes parâmetros:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>`

- **Ativar a replicação em tabelas específicas.** Para permitir a replicação no quadro1, no quadro2 e no quadro3, utilize os seguintes parâmetros:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"`

- **Ativar a replicação em tabelas específicas e copiar os dados existentes**. Para permitir a replicação no quadro1, no quadro2 e no quadro3, utilize os seguintes parâmetros:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata`

- **Ativar a replicação em todas as tabelas e replicar metadados Phoenix de origem para destino.** A replicação dos metadados phoenix não é perfeita. Use-o com cuidado. Utilize os seguintes parâmetros:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta`

## <a name="copy-and-migrate-data"></a>Copiar e migrar dados

Existem dois scripts de ação de scripts separados disponíveis para copiar ou migrar dados após a replicação ser ativado:

- [Script para mesas pequenas](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabelas que são alguns gigabytes de tamanho, e a cópia geral deve terminar em menos de uma hora)

- [Roteiro para mesas grandes](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabelas que devem demorar mais de uma hora a copiar)

Pode seguir o mesmo procedimento descrito em Ativar a [replicação](#enable-replication) para chamar a ação do script. Utilize os seguintes parâmetros:

`-m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]`

A `print_usage()` secção do [guião](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) tem uma descrição detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Copiar tabelas específicas (test1, test2 e test3) para todas as linhas editadas até agora (carimbo de tempo atual)**:

  `-m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

  Ou:

  `-m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

- **Copiar tabelas específicas com um intervalo de tempo especificado:**

  `-m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"`

## <a name="disable-replication"></a>Desativar a replicação

Para desativar a replicação, utilize outro script de ação do Script do [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Pode seguir o mesmo procedimento descrito em Ativar a [replicação](#enable-replication) para chamar a ação do script. Utilize os seguintes parâmetros:

`-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">`

A `print_usage()` secção do [guião](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) tem uma explicação detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Desativar a replicação em todas as tabelas:**

  `-m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all`

  ou

  `--src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>`

- **Desativar a replicação em tabelas especificadas (quadro1, quadro2 e quadro3)**:

  `-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"`

> [!NOTE]
> Se pretende eliminar o cluster de destino, certifique-se de que o remove da lista de pares do cluster de origem. Isto pode ser feito executando o comando remove_peer '1' na casca da base h no cluster de origem. Se não o conseguir, o cluster de fontes pode não funcionar corretamente.
>

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar a replicação Apache HBase dentro de uma rede virtual, ou entre duas redes virtuais. Para saber mais sobre HDInsight e Apache HBase, consulte estes artigos:

* [Começar com Apache HBase em HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Visão geral do HDInsight Apache HBase](./apache-hbase-overview.md)
* [Criar clusters Apache HBase em Rede Virtual Azure](./apache-hbase-provision-vnet.md)

