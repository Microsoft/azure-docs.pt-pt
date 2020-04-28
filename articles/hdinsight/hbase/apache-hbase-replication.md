---
title: Replicação de cluster HBase em redes virtuais - Azure HDInsight
description: Saiba como configurar a replicação hBase de uma versão HDInsight para outra para equilibrar a carga, alta disponibilidade, migração e atualizações de tempo zero e recuperação de desastres.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 1e6465584dd4e67f736b94d2939678c1a69163bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75435671"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Configurar a replicação do cluster Apache HBase em redes virtuais Azure

Saiba como configurar a replicação [Apache HBase](https://hbase.apache.org/) dentro de uma rede virtual, ou entre duas redes virtuais em Azure.

A replicação do cluster utiliza uma metodologia de impulso de origem. Um cluster HBase pode ser uma fonte ou um destino, ou pode cumprir ambas as funções de uma só vez. A replicação é assíncrona. O objetivo da replicação é a eventual consistência. Quando a fonte recebe uma edição para uma família de colunas quando a replicação está ativada, a edição é propagada a todos os clusters de destino. Quando os dados são replicados de um cluster para outro, o cluster de origem e todos os clusters que já consumiram os dados são rastreados, para evitar ciclos de replicação.

Neste artigo, cria-se uma réplica de destino-fonte. Para outras topoologias do cluster, consulte o guia de [referência Apache HBase](https://hbase.apache.org/book.html#_cluster_replication).

Seguem-se os casos de utilização da replicação hbase para uma única rede virtual:

* Balanceamento de carga. Por exemplo, pode executar digitalizações ou mapReduce empregos no cluster de destino, e ingerir dados sobre o cluster de origem.
* Adicionando alta disponibilidade.
* Dados migratórios de um cluster HBase para outro.
* Atualizar um cluster Azure HDInsight de uma versão para outra.

Seguem-se os casos de utilização da replicação de HBase para duas redes virtuais:

* A preparar a recuperação de desastres.
* Equilibre a carga e partindo a aplicação.
* Adicionando alta disponibilidade.

Pode replicar clusters utilizando scripts de ação de [script](../hdinsight-hadoop-customize-cluster-linux.md) do [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, deve ter uma assinatura Azure. Ver [Obter um teste gratuito Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Configurar os ambientes

Tem três opções de configuração:

- Dois clusters Apache HBase numa rede virtual Azure.
- Dois aglomerados Apache HBase em duas redes virtuais diferentes na mesma região.
- Dois clusters Apache HBase em duas redes virtuais diferentes em duas regiões diferentes (geo-replicação).

Este artigo cobre o cenário de geo-replicação.

Para ajudá-lo a configurar os ambientes, criamos [alguns modelos de Gestor de Recursos Azure.](../../azure-resource-manager/management/overview.md) Se preferir configurar os ambientes utilizando outros métodos, consulte:

- [Criar clusters Apache Hadoop em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Criar clusters Apache HBase na Rede Virtual Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Criar duas redes virtuais em duas regiões diferentes

Para utilizar um modelo que cria duas redes virtuais em duas regiões diferentes e a ligação VPN entre os VNets, selecione o seguinte **Botão Deploy para Azure.** A definição do modelo é armazenada num [armazenamento de bolhas públicas.](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json)

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Alguns dos valores codificados no modelo:

**VNet 1**

| Propriedade | Valor |
|----------|-------|
| Localização | E.U.A. Oeste |
| Nome VNet | &lt;ClusterNamePrevix>-vnet1 |
| Prefixo de espaço de endereço | 10.1.0.0/16 |
| Nome da sub-rede | subnet11 |
| Prefixo de sub-rede | 10.1.0.0/24 |
| Nome subnet (gateway) | GatewaySubnet (não pode ser alterado) |
| Prefixo subnet (gateway) | 10.1.255.0/27 |
| Nome do gateway | vnet1gw |
| Tipo de gateway | Vpn |
| Tipo VPN gateway | RouteBased |
| Gateway SKU | Básico |
| Gateway IP | vnet1gwip |

**VNet 2**

| Propriedade | Valor |
|----------|-------|
| Localização | E.U.A. Leste |
| Nome VNet | &lt;ClusterNamePrevix>-vnet2 |
| Prefixo de espaço de endereço | 10.2.0.0/16 |
| Nome da sub-rede | subnet11 |
| Prefixo de sub-rede | 10.2.0.0/24 |
| Nome subnet (gateway) | GatewaySubnet (não pode ser alterado) |
| Prefixo subnet (gateway) | 10.2.255.0/27 |
| Nome do gateway | vnet2gw |
| Tipo de gateway | Vpn |
| Tipo VPN gateway | RouteBased |
| Gateway SKU | Básico |
| Gateway IP | vnet1gwip |

## <a name="setup-dns"></a>Configuração DNS

Na última secção, o modelo cria uma máquina virtual Ubuntu em cada uma das duas redes virtuais.  Nesta secção, instala o Bind nas duas máquinas virtuais DNS e, em seguida, configura o DNS a encaminhar as duas máquinas virtuais.

Para instalar o Bind, o yon precisa de encontrar o endereço IP público das duas máquinas virtuais DNS.

1. Abra o [portal Azure.](https://portal.azure.com)
2. Abra a máquina virtual DNS selecionando **grupos de recursos > [nome do grupo de recursos] > [vnet1DNS]**.  O nome do grupo de recursos é o que se cria no último procedimento. Os nomes de máquinas virtuais DNS padrão são *vnet1DNS* e *vnet2NDS*.
3. Selecione **Propriedades** para abrir a página de propriedades da rede virtual.
4. Anote o **endereço IP público**e verifique também o endereço IP **privado**.  O endereço IP privado é **de 10.1.0.4** para vnet1DNS e **10.2.0.4** para vnet2DNS.  
5. Altere os Servidores DNS para ambas as redes virtuais utilizarem servidores DNS Padrão (Fornecidos pelo Azure) para permitir o acesso de entrada e saída aos pacotes de descarregamento para instalar o Bind nos seguintes passos.

Para instalar o Bind, utilize o seguinte procedimento:

1. Utilize o SSH para se ligar ao __endereço IP público__ da máquina virtual DNS. O seguinte exemplo liga-se a uma máquina virtual em 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Substitua-a `sshuser` pela conta de utilizador SSH especificada ao criar a máquina virtual DNS.

    > [!NOTE]  
    > Há uma variedade de maneiras de obter a `ssh` utilidade. No Linux, Unix e macOS, é fornecido como parte do sistema operativo. Se estiver a utilizar o Windows, considere uma das seguintes opções:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash em Ubuntu no Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [Openssh (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Para instalar o Bind, utilize os seguintes comandos da sessão SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Configure o Bind para retransmitir pedidos de resolução de nomes para o seu servidor DNS no local. Para tal, utilize o seguinte texto como `/etc/bind/named.conf.options` conteúdo do ficheiro:

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
    > Substitua os `goodclients` valores na secção pela gama de endereços IP das duas redes virtuais. Esta secção define os endereços de que este servidor DNS aceita pedidos.

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

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    O `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` texto é o __sufixo DNS__ para esta rede virtual. Guarde este valor, porque vai ser utilizado mais tarde.

    Também deve descobrir o sufixo DNS do outro servidor DNS. Precisa no próximo passo.

5. Para configurar o Bind para resolver os nomes de DNS para `/etc/bind/named.conf.local` recursos dentro da rede virtual, utilize o seguinte texto como conteúdo do ficheiro:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Deve substituir `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` o sufixo DNS da outra rede virtual. E o IP para a frente é o endereço IP privado do servidor DNS na outra rede virtual.

    Para editar este ficheiro, utilize o seguinte comando:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Para guardar o ficheiro, utilize __Ctrl+X,__ __Y,__ e depois __Enter__.

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
    > Substitua-a `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` com o nome de domínio totalmente qualificado (FQDN) da máquina virtual DNS na outra rede.
    >
    > Substitua-o `10.2.0.4` pelo __endereço IP interno__ do seu servidor DNS personalizado na outra rede virtual.

    A resposta parece semelhante ao seguinte texto:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Até agora, não é possível procurar o endereço IP da outra rede sem o endereço IP do servidor DNS especificado.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Configure a rede virtual para utilizar o servidor DNS personalizado

Para configurar a rede virtual para utilizar o servidor DNS personalizado em vez do resolver recursivo Azure, utilize os seguintes passos:

1. No [portal Azure,](https://portal.azure.com)selecione a rede virtual e, em seguida, selecione __Servidores DNS__.

2. Selecione __Custom__e __introduza__ o endereço IP interno do servidor DNS personalizado. Finalmente, selecione __Guardar__.

6. Abra a máquina virtual do servidor DNS em vnet1 e clique em **Reiniciar**.  Tem de reiniciar todas as máquinas virtuais da rede virtual para que a configuração dNS faça efeito.
7. Repita os passos configurar o servidor DNS personalizado para vnet2.

Para testar a configuração DNS, pode ligar-se às duas máquinas virtuais DNS utilizando SSH e pingar o servidor DNS da outra rede virtual utilizando o seu nome de anfitrião. Se não funcionar, utilize o seguinte comando para verificar o estado do DNS:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Criar clusters Apache HBase

Crie um cluster [Apache HBase](https://hbase.apache.org/) em cada uma das duas redes virtuais com a seguinte configuração:

- **Nome**do grupo de recursos : use o mesmo nome de grupo de recursos que criou as redes virtuais.
- **Tipo de cluster**: HBase
- **Versão**: HBase 1.1.2 (HDI 3.6)
- **Localização**: Utilize o mesmo local que a rede virtual.  Por padrão, vnet1 é *Oeste dos EUA*, e vnet2 é Leste dos *EUA*.
- **Armazenamento**: Criar uma nova conta de armazenamento para o cluster.
- **Rede virtual** (a partir de configurações avançadas no portal): Selecione vnet1 criada no último procedimento.
- **Sub-rede**: O nome predefinido utilizado no modelo é **subnet1**.

Para garantir que o ambiente está configurado corretamente, deve ser capaz de pingar o FQDN do cabeçada entre os dois clusters.

## <a name="load-test-data"></a>Carregar os dados de teste

Quando se replica um cluster, deve especificar as tabelas que pretende replicar. Nesta secção, você carrega alguns dados no cluster de origem. Na secção seguinte, permitirá a replicação entre os dois agrupamentos.

Para criar uma tabela de Contactos e inserir **alguns** dados na tabela, siga as instruções no [tutorial Apache HBase: Começar a usar Apache HBase no HDInsight](apache-hbase-tutorial-get-started-linux.md).

> [!NOTE]
> Se quiser replicar tabelas a partir de um espaço de nome personalizado, precisa de garantir que os espaços de nome personalizados adequados também estão definidos no cluster de destino.
>

## <a name="enable-replication"></a>Ativar a replicação

Os seguintes passos descrevem como chamar o script de ação do script do portal Azure. Para obter informações sobre a execução de uma ação de script utilizando o Azure PowerShell e o Azure Classic CLI, consulte customize [os clusters HDInsight utilizando a ação do script](../hdinsight-hadoop-customize-cluster-linux.md).

**Para permitir a replicação de HBase a partir do portal Azure**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster hbase de origem.
3. No menu de cluster, selecione **Script Actions**.
4. No topo da página, selecione **Submeter Novo**.
5. Selecione ou introduza as seguintes informações:

   1. **Nome**: Introduzir **a replicação ativa .**
   2. **URL do**script **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**de bash : Enter .
   3. **Cabeça**: Certifique-se de que este é selecionado. Limpe os outros tipos de nó.
   4. **Parâmetros**: Os seguintes parâmetros de amostra permitem a replicação de todas as tabelas existentes e, em seguida, copiar todos os dados do cluster de origem para o cluster de destino:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > Utilize o nome de anfitrião em vez de FQDN para o nome DNS do cluster de origem e destino.
      >
      > Esta passagem assume o hn1 como cabeçada ativa. Por favor, verifique o seu cluster para identificar o nó de cabeça ativa.

6. Selecione **Criar**. O script pode demorar algum tempo a ser executado, especialmente quando utiliza o argumento **-copydata.**

Argumentos necessários:

|Nome|Descrição|
|----|-----------|
|-s, --s-cluster src | Especifica o nome DNS do cluster HBase fonte. Por exemplo: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, -dst-cluster | Especifica o nome DNS do cluster HBase de destino (réplica). Por exemplo: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Especifica a palavra-passe de administrador para Ambari no cluster HBase fonte. |
|-dp, -dst-ambari-password | Especifica a palavra-passe de administrador para Ambari no cluster HBase de destino.|

Argumentos opcionais:

|Nome|Descrição|
|----|-----------|
|-su, --src-ambari-user | Especifica o nome de utilizador administrativo para Ambari no cluster HBase fonte. O valor predefinido é **a administração.** |
|-du, -dst-ambari-user | Especifica o nome de utilizador administrativo para Ambari no cluster HBase de destino. O valor predefinido é **a administração.** |
|-t, -lista de mesa | Especifica as tabelas a serem replicadas. Por exemplo: -tabela="table1;table2;quadro3". Se não especificar tabelas, todas as tabelas HBase existentes são replicadas.|
|-m, --máquina | Especifica o nó da cabeça onde a ação do guião corre. O valor deve ser escolhido com base no qual é o nó de cabeça ativo. Use esta opção quando estiver a executar o script de $0 como uma ação de script do portal HDInsight ou do Azure PowerShell.|
|-cp, -copydata | Permite a migração dos dados existentes nas tabelas onde a replicação está ativada. |
|-rpm, -replicate-phoenix-meta | Permite a replicação nas tabelas do sistema Phoenix. <br><br>*Utilize esta opção com cuidado.* Recomendamos que recrie as tabelas Phoenix em clusters de réplicas antes de usar este script. |
|-h, --ajuda | Exibe informações de utilização. |

A `print_usage()` secção do [guião](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) tem uma explicação detalhada dos parâmetros.

Após a implementação da ação do script com sucesso, pode utilizar o SSH para se ligar ao cluster HBase de destino e, em seguida, verificar se os dados foram replicados.

### <a name="replication-scenarios"></a>Cenários de replicação

A lista que se segue mostra alguns casos gerais de utilização e as definições dos parâmetros:

- **Ativar a replicação em todas as tabelas entre os dois clusters**. Este cenário não requer copiar ou migrar dados existentes nas tabelas, e não utiliza tabelas Phoenix. Utilize os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Ativar a replicação em tabelas específicas**. Para permitir a replicação no quadro 1, no quadro 2 e no quadro3, utilize os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Ativar a replicação em tabelas específicas e copiar os dados existentes**. Para permitir a replicação no quadro 1, no quadro 2 e no quadro3, utilize os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Ativar a replicação em todas as tabelas e replicar metadados Phoenix de origem para destino**. A replicação dos metadados Phoenix não é perfeita. Use-o com cuidado. Utilize os seguintes parâmetros:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copiar e migrar dados

Existem dois scripts de ação de script separados disponíveis para copiar ou migrar dados após a replicação:

- [Script para pequenas mesas](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabelas que são alguns gigabytes de tamanho, e a cópia geral é esperado para terminar em menos de uma hora)

- [Script para grandes tabelas](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabelas que se espera que demorem mais de uma hora a copiar)

Pode seguir o mesmo procedimento descrito na [replicação Enable](#enable-replication) para chamar a ação do script. Utilize os seguintes parâmetros:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

A `print_usage()` secção do [guião](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) tem uma descrição detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Copiar tabelas específicas (test1, test2 e test3) para todas as linhas editadas até agora (carimbo de tempo atual)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Ou:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copiar tabelas específicas com um intervalo de tempo especificado:**

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Desativar a replicação

Para desativar a replicação, utilize outro script de ação de script do [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Pode seguir o mesmo procedimento descrito na [replicação Enable](#enable-replication) para chamar a ação do script. Utilize os seguintes parâmetros:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

A `print_usage()` secção do [guião](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) tem uma explicação detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Desativar a replicação em todas as tabelas:**

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  ou

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Desativar a replicação em tabelas especificadas (quadro1, quadro2 e quadro3)**:

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

> [!NOTE]
> Se pretender eliminar o cluster de destino, certifique-se de que o remove da lista de pares do cluster de origem. Isto pode ser feito executando o comando remove_peer '1' na concha da base hbase no aglomerado de origem. Se não o aglomerado de origem funcionar corretamente, o cluster de origem pode não funcionar corretamente.
>

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar a replicação Apache HBase dentro de uma rede virtual, ou entre duas redes virtuais. Para saber mais sobre hDInsight e Apache HBase, consulte estes artigos:

* [Começar com Apache HBase em HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Visão geral hDInsight Apache HBase](./apache-hbase-overview.md)
* [Criar clusters Apache HBase na Rede Virtual Azure](./apache-hbase-provision-vnet.md)

