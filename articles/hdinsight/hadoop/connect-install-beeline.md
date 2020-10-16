---
title: Conecte-se ou instale Apache Beeline - Azure HDInsight
description: Saiba como se conectar com o cliente Apache Beeline para executar consultas de Hive com Hadoop em HDInsight. Beeline é uma utilidade para trabalhar com a HiveServer2 em vez de JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperfq1
ms.date: 05/27/2020
ms.openlocfilehash: 5495e6c6392ba2e824a0a70717bd19747db9b754
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88754961"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>Conecte-se ao Apache Beeline no HDInsight ou instale-o localmente

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) é um cliente da Hive que está incluído nos nós da cabeça do seu cluster HDInsight. Este artigo descreve como ligar ao cliente Beeline instalado no seu cluster HDInsight em diferentes tipos de conexões. Também discute como [instalar o cliente Beeline localmente.](#install-beeline-client) 

## <a name="types-of-connections"></a>Tipos de ligações

### <a name="from-an-ssh-session"></a>De uma sessão de SSH

Ao ligar de uma sessão SSH a um headnode de cluster, pode ligar-se ao `headnodehost` endereço na `10001` porta:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Sobre uma rede virtual Azure

Ao ligar de um cliente ao HDInsight sobre uma Rede Virtual Azure, deve fornecer o nome de domínio totalmente qualificado (FQDN) de um nó de cabeça de cluster. Uma vez que esta ligação é feita diretamente aos nosmos de cluster, a ligação utiliza a `10001` porta:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

`<headnode-FQDN>`Substitua-o pelo nome de domínio totalmente qualificado de um headnode de cluster. Para encontrar o nome de domínio totalmente qualificado de um headnode, utilize as informações no [Manage HDInsight utilizando o documento Apache Ambari REST API.](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes)

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Para hdInsight Enterprise Security Package (ESP) cluster usando Kerberos

Ao ligar de um cliente a um cluster de Pacote de Segurança Empresarial (ESP) aderido ao Azure Ative Directory (AAD)-DS numa máquina no mesmo reino do cluster, deve também especificar o nome de domínio `<AAD-Domain>` e o nome de uma conta de utilizador de domínio com permissões de acesso ao `<username>` cluster:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

`<username>`Substitua-o pelo nome de uma conta no domínio por permissões de acesso ao cluster. `<AAD-DOMAIN>`Substitua-o pelo nome do Diretório Ativo Azure (AAD) a que o cluster se junta. Use uma corda maiúscula para o `<AAD-DOMAIN>` valor, caso contrário a credencial não será encontrada. Verifique `/etc/krb5.conf` se os nomes do reino, se necessário.

Para encontrar o URL JDBC de Ambari:

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` para, onde `CLUSTERNAME` está o nome do seu cluster. Certifique-se de que a HiveServer2 está a funcionar.

1. Utilize a prancheta para copiar o URL HiveServer2 JDBC.

### <a name="over-public-or-private-endpoints"></a>Sobre os pontos finais públicos ou privados

Ao ligar-se a um cluster utilizando os pontos finais públicos ou privados, deve fornecer o nome da conta de login do cluster (padrão) `admin` e a palavra-passe. Por exemplo, utilizar a Beeline a partir de um sistema de clientes para ligar ao `clustername.azurehdinsight.net` endereço. Esta ligação é feita sobre a porta `443` , e é encriptada usando TLS/SSL.

Substitua `clustername` pelo nome do seu cluster do HDInsight. `admin`Substitua-a pela conta de login do cluster para o seu cluster. Para os agrupamentos ESP, utilize a UPN completa (por exemplo, user@domain.com ). `password`Substitua-a pela palavra-passe para a conta de login do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

ou para o ponto final privado:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Os pontos finais privados apontam para um equilibrador de carga básico, que só pode ser acedido a partir dos VNETs espreitados na mesma região. Consulte [os constrangimentos no esparso global de VNet e nos balançadores de carga](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais informações. Pode utilizar o `curl` comando com `-v` opção de resolver problemas de conectividade com pontos finais públicos ou privados antes de utilizar a beeline.

### <a name="use-beeline-with-apache-spark"></a>Use Beeline com Faísca Apache

Apache Spark fornece a sua própria implementação do HiveServer2, que às vezes é referido como o servidor Spark Thrift. Este serviço utiliza o Spark SQL para resolver consultas em vez de Hive. E pode proporcionar um melhor desempenho dependendo da sua consulta.

#### <a name="through-public-or-private-endpoints"></a>Através de pontos finais públicos ou privados

A cadeia de ligação utilizada é ligeiramente diferente. Em vez de a `httpPath=/hive2` `httpPath/sparkhive2` conter, utiliza- se. Substitua `clustername` pelo nome do seu cluster do HDInsight. `admin`Substitua-a pela conta de login do cluster para o seu cluster. Para os agrupamentos ESP, utilize a UPN completa (por exemplo, user@domain.com ). `password`Substitua-a pela palavra-passe para a conta de login do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

ou para o ponto final privado:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Os pontos finais privados apontam para um equilibrador de carga básico, que só pode ser acedido a partir dos VNETs espreitados na mesma região. Consulte [os constrangimentos no esparso global de VNet e nos balançadores de carga](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para obter mais informações. Pode utilizar o `curl` comando com `-v` opção de resolver problemas de conectividade com pontos finais públicos ou privados antes de utilizar a beeline.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Da cabeça do cluster ou dentro da Rede Virtual Azure com a Faísca Apache

Ao ligar diretamente do nó da cabeça do cluster, ou de um recurso dentro da mesma Rede Virtual Azure que o cluster HDInsight, a porta deve ser utilizada para o `10002` servidor Spark Thrift em vez de `10001` . O exemplo a seguir mostra como ligar diretamente ao nó da cabeça:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Instalar cliente Beeline

Embora a Beeline esteja incluída nos nós da cabeça, é melhor instalá-la localmente.  Os passos de instalação de uma máquina local baseiam-se num [Subsistema Windows para o Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Atualizar listas de pacotes. Introduza o seguinte comando na sua concha de bash:

    ```bash
    sudo apt-get update
    ```

1. Instale Java se não estiver instalado. Pode verificar com o `which java` comando.

    1. Se não for instalado nenhum pacote java, insira o seguinte comando:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Abra o ficheiro bashrc (frequentemente encontrado em ~/.bashrc): `nano ~/.bashrc` .

    1. Alterar o ficheiro bashrc. Adicione a seguinte linha no final do ficheiro:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Em seguida, pressione **Ctrl+X,** em **seguida, Y**, e depois entre.

1. Descarregue os arquivos Hadoop e Beeline, insira os seguintes comandos:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Desembalar os arquivos, introduzir os seguintes comandos:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Alterar ainda mais o ficheiro bashrc. Tens de identificar o caminho para onde os arquivos foram desempacotados. Se utilizar o [Subsistema Windows para Linux](https://docs.microsoft.com/windows/wsl/install-win10), e seguisse exatamente os passos, o seu caminho seria `/mnt/c/Users/user/` , onde está o seu nome de `user` utilizador.

    1. Abra o ficheiro: `nano ~/.bashrc`

    1. Modifique os comandos abaixo com o caminho apropriado e, em seguida, insira-os no final do ficheiro bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Em seguida, pressione **Ctrl+X,** em **seguida, Y**, e depois entre.

1. Feche e, em seguida, reabra a sessão de festa.

1. Teste a sua ligação. Utilize o formato de ligação a partir de [pontos finais públicos ou privados,](#over-public-or-private-endpoints)acima.

## <a name="next-steps"></a>Passos seguintes

* Por exemplo, utilizando o cliente Beeline com a Colmeia Apache, consulte [Use Apache Beeline com Apache Hive](apache-hadoop-use-hive-beeline.md)
* Para obter informações mais gerais sobre a Colmeia em HDInsight, consulte [Use Apache Hive com Apache Hadoop em HDInsight](hdinsight-use-hive.md)
