---
title: Ligar ou instalar Apache Beeline - Azure HDInsight
description: Aprenda a ligar-se ao cliente Apache Beeline para executar consultas da Hive com Hadoop no HDInsight. Beeline é um utilitário para trabalhar com hiveServer2 sobre JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 05/27/2020
ms.openlocfilehash: fcd1c20ea2ecb5cbeb87fb8f703593453b5f85bb
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122396"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>Ligue-se à Apache Beeline no HDInsight ou instale-o localmente

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) é um cliente da Colmeia que está incluído nos nós da cabeça do seu cluster HDInsight. Este artigo descreve como se conectar ao cliente Beeline instalado no seu cluster HDInsight em diferentes tipos de ligações. Também discute como [instalar o cliente Beeline localmente.](#install-beeline-client) 

## <a name="types-of-connections"></a>Tipos de ligações

### <a name="from-an-ssh-session"></a>A partir de uma sessão de SSH

Ao ligar de uma sessão SSH a um nído de cluster, pode então ligar-se ao `headnodehost` endereço na `10001` porta:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Sobre uma Rede Virtual Azure

Ao ligar-se de um cliente ao HDInsight sobre uma Rede Virtual Azure, deve fornecer o nome de domínio totalmente qualificado (FQDN) de um nó de cabeça de cluster. Uma vez que esta ligação é feita diretamente aos nós do cluster, a ligação utiliza a `10001` porta:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

`<headnode-FQDN>`Substitua-a com o nome de domínio totalmente qualificado de um headnode de cluster. Para encontrar o nome de domínio totalmente qualificado de um headnode, utilize a informação no [Manage HDInsight utilizando o documento Apache Ambari REST API.](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes)

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Para o cluster HDInsight Enterprise Security Package (ESP) utilizando kerberos

Ao ligar-se de um cliente a um cluster do Enterprise Security Package (ESP) ligado ao Azure Ative Directory (AAD)-DS numa máquina no mesmo domínio do cluster, deve também especificar o nome de domínio e o nome de uma conta de `<AAD-Domain>` utilizador de domínio com permissões para aceder ao `<username>` cluster:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

`<username>`Substitua-o pelo nome de uma conta no domínio com permissões de acesso ao cluster. Substitua `<AAD-DOMAIN>` pelo nome do Diretório Ativo Azure (AAD) a que o cluster está unido. Use uma corda maiúscula para o `<AAD-DOMAIN>` valor, caso contrário a credencial não será encontrada. Verifique `/etc/krb5.conf` se é necessário o nome do reino.

Para encontrar o URL JDBC de Ambari:

1. De um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` até, onde `CLUSTERNAME` está o nome do seu cluster. Certifique-se de que o HiveServer2 está a funcionar.

1. Utilize a área de sobre-diagnóstico para copiar o URL HiveServer2 JDBC.

### <a name="over-public-or-private-endpoints"></a>Sobre pontos finais públicos ou privados

Ao ligar-se a um cluster utilizando os pontos finais públicos ou privados, deve fornecer o nome da conta de login do cluster (predefinido) `admin` e a palavra-passe. Por exemplo, usar a Beeline a partir de um sistema de cliente para ligar ao `clustername.azurehdinsight.net` endereço. Esta ligação é feita sobre a porta `443` , e é encriptada usando TLS/SSL.

Substitua `clustername` pelo nome do seu cluster do HDInsight. Substitua-a com a conta de `admin` login do cluster para o seu cluster. Para os clusters ESP, utilize a UPN completa (por exemplo, user@domain.com ). `password`Substitua-a com a palavra-passe para a conta de login do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

ou para fins finais privados:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Os pontos finais privados apontam para um equilíbrio de carga básico, que só pode ser acedido a partir dos VNETs espreitados na mesma região. Consulte [constrangimentos no vnet global e equilibradores](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) de carga para mais informações. Pode utilizar o `curl` comando com `-v` opção para resolver problemas de conectividade com pontos finais públicos ou privados antes de utilizar a linha de apição.

### <a name="use-beeline-with-apache-spark"></a>Use beeline com Apache Spark

A Apache Spark fornece a sua própria implementação do HiveServer2, que às vezes é referido como o servidor Spark Thrift. Este serviço utiliza o Spark SQL para resolver consultas em vez da Colmeia. E pode proporcionar um melhor desempenho dependendo da sua consulta.

#### <a name="through-public-or-private-endpoints"></a>Através de pontos finais públicos ou privados

A corda de ligação utilizada é ligeiramente diferente. Em vez de `httpPath=/hive2` contê-lo `httpPath/sparkhive2` utiliza. Substitua `clustername` pelo nome do seu cluster do HDInsight. Substitua-a com a conta de `admin` login do cluster para o seu cluster. Para os clusters ESP, utilize a UPN completa (por exemplo, user@domain.com ). `password`Substitua-a com a palavra-passe para a conta de login do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

ou para fins finais privados:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Os pontos finais privados apontam para um equilíbrio de carga básico, que só pode ser acedido a partir dos VNETs espreitados na mesma região. Consulte [constrangimentos no vnet global e equilibradores](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) de carga para mais informações. Pode utilizar o `curl` comando com `-v` opção para resolver problemas de conectividade com pontos finais públicos ou privados antes de utilizar a linha de apição.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Da cabeça de cluster ou dentro da Rede Virtual Azure com Apache Spark

Ao ligar diretamente a partir do nó da cabeça do cluster, ou a partir de um recurso dentro da mesma Rede Virtual Azure que o cluster HDInsight, a porta deve ser utilizada para o `10002` servidor Spark Thrift em vez de `10001` . O exemplo que se segue mostra como ligar diretamente ao nó da cabeça:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Instalar cliente Beeline

Embora a Beeline esteja incluída nos nós da cabeça, pode querer instalá-la localmente.  Os passos de instalação de uma máquina local baseiam-se num [subsistema windows para Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Atualizar listas de pacotes. Introduza o seguinte comando na sua concha de bash:

    ```bash
    sudo apt-get update
    ```

1. Instale java se não estiver instalado. Pode verificar com o `which java` comando.

    1. Se não for instalado nenhum pacote java, introduza o seguinte comando:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Abra o ficheiro bashrc (muitas vezes encontrado em ~/.bashrc): `nano ~/.bashrc` .

    1. Altere o ficheiro bashrc. Adicione a seguinte linha no final do ficheiro:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Em seguida, prima **Ctrl+X**, em **seguida, Y**, em seguida, entrar.

1. Baixe os arquivos Hadoop e Beeline, insira os seguintes comandos:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Desfaça as malas, introduza os seguintes comandos:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Altere ainda mais o ficheiro bashrc. Tens de identificar o caminho para onde os arquivos foram desembalados. Se utilizar o [Subsistema Windows para o Linux](https://docs.microsoft.com/windows/wsl/install-win10), e seguir os passos exatamente, o seu caminho seria , onde está o `/mnt/c/Users/user/` seu nome de `user` utilizador.

    1. Abra o ficheiro:`nano ~/.bashrc`

    1. Modifique os comandos abaixo com o caminho adequado e, em seguida, introduza-os no final do ficheiro bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Em seguida, prima **Ctrl+X**, em **seguida, Y**, em seguida, entrar.

1. Feche e reabra a sessão de bash.

1. Teste a sua ligação. Utilize o formato de ligação de [pontos finais públicos ou privados,](#over-public-or-private-endpoints)acima.

## <a name="next-steps"></a>Próximos passos

* Por exemplo, usando o cliente Beeline com Apache Hive, consulte [Use Apache Beeline com Apache Hive](apache-hadoop-use-hive-beeline.md)
* Para obter informações mais gerais sobre a Hive em HDInsight, consulte [Use Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)