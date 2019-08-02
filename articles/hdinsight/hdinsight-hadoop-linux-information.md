---
title: Dicas para usar o Hadoop no HDInsight baseado em Linux – Azure
description: Obtenha dicas de implementação para usar clusters HDInsight baseados em Linux (Hadoop) em um ambiente Linux familiar em execução na nuvem do Azure.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1904ab07a188e4e877a4fb2f2b7682d923c08fb2
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441998"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informações sobre como utilizar o HDInsight no Linux

Os clusters do Azure HDInsight fornecem Apache Hadoop em um ambiente Linux familiar, em execução na nuvem do Azure. Para a maioria das coisas, ele deve funcionar exatamente como qualquer outra instalação do Hadoop no Linux. Este documento chama diferenças específicas que você deve conhecer.

## <a name="prerequisites"></a>Pré-requisitos

Muitas das etapas neste documento usam os seguintes utilitários, que talvez precisem ser instalados em seu sistema.

* [ondulação](https://curl.haxx.se/) – usada para se comunicar com serviços baseados na Web.
* **JQ**, um processador JSON de linha de comando.  Consulte [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) -usado para gerenciar remotamente os serviços do Azure.
* **Um cliente SSH**. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Utilizadores

A menos que [ingressado no domínio](./domain-joined/hdinsight-security-overview.md), o HDInsight deve ser considerado um sistema **de usuário único** . Uma única conta de usuário SSH é criada com o cluster, com permissões de nível de administrador. Contas SSH adicionais podem ser criadas, mas também têm acesso de administrador ao cluster.

O HDInsight ingressado no domínio dá suporte a vários usuários e a permissões e configurações de função mais granulares. Para obter mais informações, consulte [gerenciar clusters HDInsight ingressados no domínio](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Nomes de domínio

O FQDN (nome de domínio totalmente qualificado) a ser usado ao conectar-se ao cluster da `CLUSTERNAME.azurehdinsight.net` Internet `CLUSTERNAME-ssh.azurehdinsight.net` é ou (somente para SSH).

Internamente, cada nó no cluster tem um nome atribuído durante a configuração do cluster. Para localizar os nomes de cluster, consulte  a página hosts na interface do usuário do amAmbari Web. Você também pode usar o seguinte para retornar uma lista de hosts da API REST do amAmbari:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Substitua `CLUSTERNAME` pelo nome do cluster. Quando solicitado, insira a senha para a conta de administrador. Esse comando retorna um documento JSON que contém uma lista dos hosts no cluster. [JQ](https://stedolan.github.io/jq/) é usado para extrair o `host_name` valor do elemento para cada host.

Se você precisar localizar o nome do nó para um serviço específico, poderá consultar Ambari para esse componente. Por exemplo, para localizar os hosts para o nó de nome HDFS, use o seguinte comando:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Esse comando retorna um documento JSON que descreve o serviço e, em seguida, [JQ](https://stedolan.github.io/jq/) extrai apenas `host_name` o valor para os hosts.

## <a name="remote-access-to-services"></a>Acesso remoto a serviços

* **Ambari (Web)**  - https://CLUSTERNAME.azurehdinsight.net

    Autentique usando o usuário e a senha do administrador de cluster e, em seguida, entre no Ambari.

    A autenticação é de texto sem formatação – sempre use HTTPS para ajudar a garantir que a conexão seja segura.

    > [!IMPORTANT]  
    > Algumas das UIs da Web disponíveis por meio de nós de acesso Ambari usando um nome de domínio interno. Os nomes de domínio internos não são acessíveis publicamente pela Internet. Você pode receber erros "servidor não encontrado" ao tentar acessar alguns recursos pela Internet.
    >
    > Para usar a funcionalidade completa da interface do usuário da Web do amAmbari, use um túnel SSH para o tráfego da Web de proxy para o nó principal do cluster. Consulte [usar o túnel SSH para acessar a IU da Web do Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces do usuário da Web](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)**  - https://CLUSTERNAME.azurehdinsight.net/ambari

    > [!NOTE]  
    > Autentique usando o usuário e a senha do administrador de cluster.
    >
    > A autenticação é de texto sem formatação – sempre use HTTPS para ajudar a garantir que a conexão seja segura.

* **WebHCat (Templeton)**  - https://CLUSTERNAME.azurehdinsight.net/templeton

    > [!NOTE]  
    > Autentique usando o usuário e a senha do administrador de cluster.
    >
    > A autenticação é de texto sem formatação – sempre use HTTPS para ajudar a garantir que a conexão seja segura.

* **SSH** -CLUSTERNAME-SSH.azurehdinsight.net na porta 22 ou 23. A porta 22 é usada para se conectar ao cabeçalho primário, enquanto 23 é usado para se conectar ao secundário. Para obter mais informações sobre os nós de cabeçalho, consulte [disponibilidade e confiabilidade de clusters de Apache Hadoop no HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Você só pode acessar os nós de cabeçalho do cluster por meio de SSH em um computador cliente. Uma vez conectado, você pode acessar os nós de trabalho usando o SSH de um cabeçalho.

Para obter mais informações, consulte o documento [portas usadas pelo Apache Hadoop Services no HDInsight](hdinsight-hadoop-port-settings-for-services.md) .

## <a name="file-locations"></a>Localizações de ficheiros

Os arquivos relacionados ao Hadoop podem ser encontrados nos nós de cluster `/usr/hdp`em. Esse diretório contém os seguintes subdiretórios:

* **2.6.5.3006-29**: O nome do diretório é a versão da plataforma de dados Hortonworks usada pelo HDInsight. O número no seu cluster pode ser diferente daquele listado aqui.
* **current**: Esse diretório contém links para subdiretórios no diretório **2.6.5.3006-29** . Esse diretório existe para que você não precise se lembrar do número de versão.

Os dados de exemplo e os arquivos jar podem ser encontrados em `/example` sistema de arquivos distribuído `/HdiSamples`Hadoop em e.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, armazenamento do Azure e Data Lake Storage

Na maioria das distribuições do Hadoop, os dados são armazenados no HDFS, que é apoiado pelo armazenamento local nos computadores do cluster. O uso do armazenamento local pode ser dispendioso para uma solução baseada em nuvem, na qual você é cobrado por hora ou por minuto para recursos de computação.

Ao usar o HDInsight, os arquivos de dados são armazenados de maneira escalonável e resiliente na nuvem usando o armazenamento de BLOBs do Azure e, opcionalmente, Azure Data Lake Storage. Esses serviços oferecem os seguintes benefícios:

* Armazenamento barato de longo prazo.
* Acessibilidade de serviços externos, como sites, utilitários de upload/download de arquivos, vários SDKs de linguagem e navegadores da Web.
* Capacidade de arquivo grande e armazenamento escalonável grande.

Para obter mais informações, [](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) consulte Understanding BLOBs and [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

Ao usar o armazenamento do Azure ou o Data Lake Storage, você não precisa fazer nada especial do HDInsight para acessar os dados. Por exemplo, o comando a seguir lista os arquivos `/example/data` na pasta, independentemente de estarem armazenados no armazenamento do Azure ou data Lake Storage:

    hdfs dfs -ls /example/data

No HDInsight, os recursos de armazenamento de dados (armazenamento de BLOBs do Azure e Azure Data Lake Storage) são dissociados dos recursos de computação. Portanto, você pode criar clusters HDInsight para fazer a computação conforme necessário e, posteriormente, excluir o cluster quando o trabalho for concluído, enquanto mantém seus arquivos de dados persistentes com segurança no armazenamento em nuvem, contanto que seja necessário.


### <a name="URI-and-scheme"></a>URI e esquema

Alguns comandos podem exigir que você especifique o esquema como parte do URI ao acessar um arquivo. Por exemplo, o componente Storm-HDFS exige que você especifique o esquema. Ao usar o armazenamento não padrão (armazenamento adicionado como armazenamento "adicional" ao cluster), você sempre deve usar o esquema como parte do URI.

Ao usar o __armazenamento do Azure__, use um dos seguintes esquemas de URI:

* `wasb:///`: Acesse o armazenamento padrão usando comunicação não criptografada.

* `wasbs:///`: Acesse o armazenamento padrão usando a comunicação criptografada.  O esquema wasbs tem suporte apenas do HDInsight versão 3,6 em diante.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Usado ao se comunicar com uma conta de armazenamento não padrão. Por exemplo, quando você tem uma conta de armazenamento adicional ou ao acessar dados armazenados em uma conta de armazenamento publicamente acessível.

Ao usar __Azure data Lake Storage Gen2__, use um dos seguintes esquemas de URI:

* `abfs:///`: Acesse o armazenamento padrão usando comunicação não criptografada.

* `abfss:///`: Acesse o armazenamento padrão usando a comunicação criptografada.  O esquema abfss tem suporte apenas do HDInsight versão 3,6 em diante.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Usado ao se comunicar com uma conta de armazenamento não padrão. Por exemplo, quando você tem uma conta de armazenamento adicional ou ao acessar dados armazenados em uma conta de armazenamento publicamente acessível.

Ao usar __Azure data Lake Storage Gen1__, use um dos seguintes esquemas de URI:

* `adl:///`: Acesse o Data Lake Storage padrão para o cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Usado ao se comunicar com um Data Lake Storage não padrão. Também usado para acessar dados fora do diretório raiz do cluster HDInsight.

> [!IMPORTANT]  
> Ao usar Data Lake Storage como o armazenamento padrão para o HDInsight, você deve especificar um caminho dentro da loja para usar como a raiz do armazenamento do HDInsight. O caminho padrão é `/clusters/<cluster-name>/`.
>
> Ao usar `/` ou `adl:///` para acessar dados, você só pode acessar os dados armazenados na raiz (por exemplo, `/clusters/<cluster-name>/`) do cluster. Para acessar dados em qualquer lugar da loja, use `adl://<storage-name>.azuredatalakestore.net/` o formato.

### <a name="what-storage-is-the-cluster-using"></a>Qual armazenamento é o cluster usando

Você pode usar Ambari para recuperar a configuração de armazenamento padrão para o cluster. Use o comando a seguir para recuperar as informações de configuração do HDFS usando a ondulação e filtrá-las usando [JQ](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Esse comando retorna a primeira configuração aplicada ao servidor (`service_config_version=1`), que contém essas informações. Talvez seja necessário listar todas as versões de configuração para encontrar a versão mais recente.

Esse comando retorna um valor semelhante aos seguintes URIs:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`Se estiver usando uma conta de armazenamento do Azure.

    O nome da conta é o nome da conta de armazenamento do Azure. O nome do contêiner é o contêiner de BLOB que é a raiz do armazenamento de cluster.

* `adl://home`Se estiver usando Azure Data Lake Storage. Para obter o nome do Data Lake Storage, use a seguinte chamada REST:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Esse comando retorna o seguinte nome de host `<data-lake-store-account-name>.azuredatalakestore.net`:.

    Para obter o diretório na loja que é a raiz do HDInsight, use a seguinte chamada REST:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Esse comando retorna um caminho semelhante ao seguinte caminho: `/clusters/<hdinsight-cluster-name>/`.

Você também pode encontrar as informações de armazenamento usando o portal do Azure usando as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), selecione o cluster HDInsight.

2. Na seção **Propriedades** , selecione **contas de armazenamento**. As informações de armazenamento para o cluster são exibidas.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Como fazer acessar arquivos de fora do HDInsight

Há várias maneiras de acessar dados de fora do cluster HDInsight. A seguir estão alguns links para utilitários e SDKs que podem ser usados para trabalhar com seus dados:

Se estiver usando o __armazenamento do Azure__, consulte os links a seguir para obter maneiras de acessar seus dados:

* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2): Comandos da interface de linha de comando para trabalhar com o Azure. Depois de instalar o, `az storage` use o comando para obter ajuda sobre como `az storage blob` usar o armazenamento ou para comandos específicos de BLOB.
* [blobxfer.py](https://github.com/Azure/blobxfer): Um script Python para trabalhar com BLOBs no armazenamento do Azure.
* Vários SDKs:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [API REST de Armazenamento](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Se estiver usando __Azure data Lake Storage__, consulte os links a seguir para obter maneiras de acessar seus dados:

* [Navegador da Web](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [CLI do Azure](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [API REST do WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Ferramentas do Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Dimensionando o cluster

O recurso de dimensionamento de cluster permite que você altere dinamicamente o número de nós de dados usados por um cluster. Você pode executar operações de dimensionamento enquanto outros trabalhos ou processos estão em execução em um cluster.  Consulte também, [dimensionar clusters HDInsight](./hdinsight-scaling-best-practices.md)

Os diferentes tipos de cluster são afetados pelo dimensionamento da seguinte maneira:

* **Hadoop**: Ao reduzir verticalmente o número de nós em um cluster, alguns dos serviços no cluster são reiniciados. As operações de dimensionamento podem fazer com que os trabalhos em execução ou pendentes falhem na conclusão da operação de dimensionamento. Você pode reenviar os trabalhos quando a operação for concluída.
* **HBase**: Os servidores regionais são balanceados automaticamente em alguns minutos, após a conclusão da operação de dimensionamento. Para balancear manualmente os servidores regionais, use as seguintes etapas:

    1. Conecte-se ao cluster HDInsight usando SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Use o seguinte para iniciar o Shell do HBase:

            hbase shell

    3. Depois que o Shell do HBase tiver sido carregado, use o seguinte para balancear manualmente os servidores regionais:

            balancer

* **Storm**: Você deve reequilibrar todas as topologias Storm em execução após a execução de uma operação de dimensionamento. O rebalanceamento permite que a topologia reajuste as configurações de paralelismo com base no novo número de nós no cluster. Para reequilibrar topologias em execução, use uma das seguintes opções:

    * **SSH**: Conecte-se ao servidor e use o seguinte comando para reequilibrar uma topologia:

            storm rebalance TOPOLOGYNAME

        Você também pode especificar parâmetros para substituir as dicas de paralelismo originalmente fornecidas pela topologia. Por exemplo, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` reconfigura a topologia para cinco processos de trabalho, 3 executores para o componente Blue-Spout e 10 executores para o componente de raio amarelo.

    * **Interface do usuário do Storm**: Use as etapas a seguir para reequilibrar uma topologia usando a interface do usuário do Storm.

        1. Abra `https://CLUSTERNAME.azurehdinsight.net/stormui` o no navegador da Web, `CLUSTERNAME` em que é o nome do cluster Storm. Se solicitado, insira o nome do administrador do cluster HDInsight (admin) e a senha que você especificou ao criar o cluster.
        2. Selecione a topologia que você deseja reequilibrar e, em  seguida, selecione o botão rebalancear. Insira o atraso antes que a operação de rebalanceamento seja executada.

* **Kafka**: Você deve reequilibrar réplicas de partição após as operações de dimensionamento. Para obter mais informações, consulte a [alta disponibilidade de dados com o Apache Kafka no documento HDInsight](./kafka/apache-kafka-high-availability.md) .

Para obter informações específicas sobre como dimensionar seu cluster HDInsight, consulte:

* [Gerenciar clusters de Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gerenciar clusters de Apache Hadoop no HDInsight usando CLI do Azure](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Como fazer instalar o matiz (ou outro componente do Hadoop)?

O HDInsight é um serviço gerenciado. Se o Azure detectar um problema com o cluster, ele poderá excluir o nó com falha e criar um nó para substituí-lo. Se você instalar manualmente as coisas no cluster, elas não serão persistidas quando essa operação ocorrer. Em vez disso, use as [ações de script do HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Uma ação de script pode ser usada para fazer as seguintes alterações:

* Instalar e configurar um serviço ou site.
* Instale e configure um componente que exija alterações de configuração em vários nós no cluster.

As ações de script são scripts bash. Os scripts são executados durante a criação do cluster e são usados para instalar e configurar componentes adicionais. Os scripts de exemplo são fornecidos para a instalação dos seguintes componentes:

* [Apache O giraph](hdinsight-hadoop-giraph-install-linux.md)

Para obter informações sobre o desenvolvimento das suas próprias Ações de Script, consulte [Desenvolvimento de Ações de Script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Arquivos jar

Algumas tecnologias do Hadoop são fornecidas em arquivos jar independentes que contêm funções usadas como parte de um trabalho MapReduce ou de dentro de Pig ou Hive. Geralmente, eles não exigem nenhuma configuração e podem ser carregados no cluster após a criação e usados diretamente. Se você quiser ter certeza de que o componente sobreviver à recriação de imagens do cluster, poderá armazenar o arquivo JAR no armazenamento padrão do cluster (WASB ou ADL).

Por exemplo, se você quiser usar a versão mais recente do [Apache DataFu](https://datafu.incubator.apache.org/), poderá baixar um jar contendo o projeto e carregá-lo no cluster HDInsight. Em seguida, siga a documentação do DataFu sobre como usá-lo do Pig ou Hive.

> [!IMPORTANT]  
> Alguns componentes que são arquivos jar autônomos são fornecidos com o HDInsight, mas não estão no caminho. Se você estiver procurando um componente específico, poderá usar o seguinte para procurá-lo em seu cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Esse comando retorna o caminho de qualquer arquivo JAR correspondente.

Para usar uma versão diferente de um componente, carregue a versão necessária e use-a em seus trabalhos.

> [!IMPORTANT]
> Os componentes fornecidos com o cluster HDInsight são totalmente suportados e Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de que você envolva canais disponíveis para as tecnologias de software livre em que a profunda experiência para essa tecnologia é encontrada. Por exemplo, há muitos sites de comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Além disso, os projetos do Apache [https://apache.org](https://apache.org)têm sites de projeto em, por exemplo: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Passos Seguintes

* [Gerenciar clusters HDInsight usando a API REST do Apache Ambari](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)
