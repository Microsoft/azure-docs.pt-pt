---
title: Dicas para usar Hadoop no HDInsight baseado em Linux - Azure
description: Obtenha dicas de implementação para usar clusters HDInsight (Hadoop) baseados em Linux em um ambiente linux familiar que funciona na nuvem Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 3d9dec0065bb62821fcedcbc4f6e5b578c061caf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386334"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informações sobre como utilizar o HDInsight no Linux

Os clusters Azure HDInsight fornecem Apache Hadoop num ambiente linux familiar, correndo na nuvem Azure. Para a maioria das coisas, deve funcionar exatamente como qualquer outra instalação Hadoop-on-Linux. Este documento chama a atenção para diferenças específicas que deve ter em conta.

## <a name="prerequisites"></a>Pré-requisitos

Muitas das etapas deste documento utilizam os seguintes utilitários, que podem ter de ser instalados no seu sistema.

* [cURL](https://curl.haxx.se/) - usado para comunicar com serviços baseados na web.
* **jq**, um processador JSON de linha de comando.  Veja [https://stedolan.github.io/jq/. ](https://stedolan.github.io/jq/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) - usado para gerir remotamente os serviços Azure.
* **Um cliente SSH**. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Utilizadores

A menos que [se junte ao domínio,](./domain-joined/hdinsight-security-overview.md)o HDInsight deve ser considerado um sistema **de utilizador único.** Uma única conta de utilizador SSH é criada com o cluster, com permissões de nível de administrador. Podem ser criadas contas SSH adicionais, mas também têm acesso ao cluster por parte dos administradores.

O HDInsight, filiado em domínios, suporta vários utilizadores e mais permissão granular e definições de funções. Para mais informações, consulte [Gerir os clusters HDInsight uniados ao Domínio](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Nomes de domínio

O nome de domínio totalmente qualificado (FQDN) para utilizar quando se conecta ao cluster a partir da internet é `CLUSTERNAME.azurehdinsight.net` ou `CLUSTERNAME-ssh.azurehdinsight.net` (apenas para SSH).

Internamente, cada nó no cluster tem um nome que é atribuído durante a configuração do cluster. Para encontrar os nomes do cluster, consulte a página **hosts** na Ambari Web UI. Também pode utilizar o seguinte para devolver uma lista de anfitriões da API AMBARI REST:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Substitua `CLUSTERNAME` pelo nome do cluster. Quando solicitado, introduza a palavra-passe para a conta de administração. Este comando devolve um documento JSON que contém uma lista dos anfitriões do cluster. [jq](https://stedolan.github.io/jq/) é usado para extrair o valor do elemento `host_name` para cada hospedeiro.

Se precisar de encontrar o nome do nó para um serviço específico, pode consultar Ambari por esse componente. Por exemplo, para encontrar os anfitriões para o nó de nome HDFS, utilize o seguinte comando:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Este comando devolve um documento JSON descrevendo o serviço, e então [jq](https://stedolan.github.io/jq/) retira apenas o valor `host_name` para os anfitriões.

## <a name="remote-access-to-services"></a>Acesso remoto a serviços

* **Ambari (web)**  - `https://CLUSTERNAME.azurehdinsight.net`

    Autenticar utilizando o utilizador e a palavra-passe do administrador do cluster e, em seguida, iniciar sessão na Ambari.

    A autenticação é texto simples - utilize sempre HTTPS para ajudar a garantir que a ligação está segura.

    > [!IMPORTANT]  
    > Alguns dos UIs web disponíveis através de nódos de acesso Ambari usando um nome de domínio interno. Os nomes internos de domínio não são acessíveis ao público através da internet. Pode receber erros de "servidor não encontrado" ao tentar aceder a algumas funcionalidades através da Internet.
    >
    > Para utilizar a funcionalidade completa da UI web ambari, utilize um túnel SSH para proxy tráfego web para o nó de cabeça do cluster. Consulte [O Túnel SSH para aceder a Apache Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie, e outros UIs web](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)**  - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Autenticar utilizando o utilizador do administrador do cluster e a palavra-passe.
    >
    > A autenticação é texto simples - utilize sempre HTTPS para ajudar a garantir que a ligação está segura.

* **WebHCat (Templeton)**  - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Autenticar utilizando o utilizador do administrador do cluster e a palavra-passe.
    >
    > A autenticação é texto simples - utilize sempre HTTPS para ajudar a garantir que a ligação está segura.

* **SSH** - CLUSTERNAME-ssh.azurehdinsight.net na porta 22 ou 23. A porta 22 é utilizada para ligar ao dinídeo primário, enquanto 23 é usada para ligar ao secundário. Para obter mais informações sobre os nós da cabeça, consulte [disponibilidade e fiabilidade dos clusters Apache Hadoop em HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Só é possível aceder aos nós da cabeça do cluster através do SSH a partir de uma máquina de cliente. Uma vez ligado, pode aceder aos nós dos trabalhadores utilizando SSH a partir de um nódeo.

Para mais informações, consulte as [Portas utilizadas pelos serviços Apache Hadoop no documento HDInsight.](hdinsight-hadoop-port-settings-for-services.md)

## <a name="file-locations"></a>Localizações de ficheiros

Os ficheiros relacionados com hadoop podem ser encontrados nos nós do cluster em `/usr/hdp`. Este diretório contém os seguintes subdiretórios:

* **2.6.5.3009-43**: O nome do diretório é a versão da plataforma Hadoop utilizada pela HDInsight. O número no seu cluster pode ser diferente daquele listado aqui.
* **atual**: Este diretório contém ligações a subdiretórios no âmbito do diretório **2.6.5.3009-43.** Este diretório existe para que não tenha supor o número da versão.

Os dados de exemplo e os ficheiros JAR podem ser encontrados no Sistema de Ficheiros Distribuídos Hadoop em `/example` e `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Armazenamento Azure e Armazenamento de Data Lake

Na maioria das distribuições hadoop, os dados são armazenados em HDFS, que é apoiado pelo armazenamento local nas máquinas do cluster. O uso do armazenamento local pode ser dispendioso para uma solução baseada na nuvem onde você é cobrado de hora em hora ou minuto para recursos de computação.

Ao utilizar o HDInsight, os ficheiros de dados são armazenados de forma escalável e resiliente na nuvem utilizando o Armazenamento De Azure Blob e opcionalmente o Armazenamento do Lago De dados Azure. Estes serviços proporcionam os seguintes benefícios:

* Armazenamento barato a longo prazo.
* Acessibilidade de serviços externos, tais como websites, serviços de upload/download de ficheiros, vários SDKs de idioma e navegadores web.
* Grande capacidade de arquivo e grande armazenamento escalável.

Para mais informações, consulte [Understanding blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) e [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

Ao utilizar o Armazenamento Azure ou o Armazenamento de Data Lake, não é necessário fazer nada de especial do HDInsight para aceder aos dados. Por exemplo, os seguintes ficheiros de comando listam ficheiros na pasta `/example/data`, independentemente de estar armazenado no Armazenamento Azure ou no Armazenamento de Data Lake:

    hdfs dfs -ls /example/data

No HDInsight, os recursos de armazenamento de dados (Armazenamento Azure Blob e Armazenamento de Lagos De Dados Azure) são dissociados dos recursos da computação. Por isso, pode criar clusters HDInsight para fazer a computação conforme necessário, e mais tarde eliminar o cluster quando o trabalho estiver concluído, mantendo os seus ficheiros de dados permanecidos em segurança no armazenamento em nuvem o tempo que precisar.

### <a name="URI-and-scheme"></a>URI e esquema

Alguns comandos podem exigir que especifique o esquema como parte do URI ao aceder a um ficheiro. Por exemplo, o componente Storm-HDFS requer que especifique o esquema. Ao utilizar armazenamento não predefinido (armazenamento adicionado como armazenamento "adicional" ao cluster), deve sempre utilizar o esquema como parte do URI.

Ao utilizar o [**Armazenamento Azure,** ](./hdinsight-hadoop-use-blob-storage.md)utilize um dos seguintes esquemas URI:

* `wasb:///`: Aceder ao armazenamento predefinido utilizando comunicações não encriptadas.

* `wasbs:///`: Aceder ao armazenamento predefinido utilizando comunicações encriptadas.  O esquema de wasbs é suportado apenas a partir da versão 3.6 da HDInsight.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Utilizado quando se comunica com uma conta de armazenamento não predefinida. Por exemplo, quando tem uma conta de armazenamento adicional ou ao aceder a dados armazenados numa conta de armazenamento acessível ao público.

Ao utilizar [**o Azure Data Lake Storage Gen2,** ](./hdinsight-hadoop-use-data-lake-storage-gen2.md)utilize o seguinte esquema URI:

* `abfs://`: Aceder ao armazenamento predefinido utilizando comunicações encriptadas.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Utilizado quando se comunica com uma conta de armazenamento não predefinida. Por exemplo, quando tem uma conta de armazenamento adicional ou ao aceder a dados armazenados numa conta de armazenamento acessível ao público.

Ao utilizar [**o Azure Data Lake Storage Gen1,** ](./hdinsight-hadoop-use-data-lake-store.md)utilize um dos seguintes esquemas URI:

* `adl:///`: Aceda ao armazenamento padrão do Lago de Dados para o cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Utilizado quando se comunica com um armazenamento não predefinido do Lago de Dados. Também usado para aceder a dados fora do diretório raiz do seu cluster HDInsight.

> [!IMPORTANT]  
> Ao utilizar o Armazenamento do Lago de Dados como loja padrão para o HDInsight, deve especificar um caminho dentro da loja para utilizar como raiz do armazenamento HDInsight. O caminho padrão é `/clusters/<cluster-name>/`.
>
> Ao utilizar `/` ou `adl:///` para aceder a dados, só é possível aceder a dados armazenados na raiz (por exemplo, `/clusters/<cluster-name>/`) do cluster. Para aceder aos dados em qualquer lugar da loja, utilize o formato `adl://<storage-name>.azuredatalakestore.net/`.

### <a name="what-storage-is-the-cluster-using"></a>Qual é o armazenamento usando

Pode utilizar ambari para recuperar a configuração de armazenamento predefinida para o cluster. Utilize o seguinte comando para recuperar informações de configuração HDFS utilizando caracóis e filtre-os utilizando [jq:](https://stedolan.github.io/jq/)

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Este comando devolve a primeira configuração aplicada ao servidor (`service_config_version=1`), que contém esta informação. Pode ter de listar todas as versões de configuração para encontrar a mais recente.

Este comando devolve um valor semelhante aos seguintes URIs:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` se utilizar uma conta de Armazenamento Azure.

    O nome da conta é o nome da conta Azure Storage. O nome do recipiente é o recipiente de bolhas que é a raiz do armazenamento do cluster.

* `adl://home` se utilizar o Armazenamento do Lago De Dados Azure. Para obter o nome data Lake Storage, use a seguinte chamada REST:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Este comando devolve o seguinte nome anfitrião: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Para obter o diretório dentro da loja que é a raiz para o HDInsight, use a seguinte chamada REST:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Este comando devolve um caminho semelhante ao seguinte caminho: `/clusters/<hdinsight-cluster-name>/`.

Também pode encontrar as informações de armazenamento utilizando o portal Azure utilizando os seguintes passos:

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster HDInsight.

2. Na secção **Propriedades,** selecione Contas de **Armazenamento**. As informações de armazenamento do cluster são apresentadas.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Como posso aceder a ficheiros de fora do HDInsight

Existem várias formas de aceder a dados de fora do cluster HDInsight. Seguem-se algumas ligações a utilitários e SDKs que podem ser utilizados para trabalhar com os seus dados:

Se utilizar o __Armazenamento Azure,__ consulte os seguintes links para obter formas de aceder aos seus dados:

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2): Comandos de interface de linha de comando para trabalhar com o Azure. Depois de instalar, utilize o comando `az storage` para obter ajuda na utilização do armazenamento ou `az storage blob` para comandos específicos da bolha.
* [blobxfer.py](https://github.com/Azure/blobxfer): Um roteiro python para trabalhar com bolhas no Armazenamento Azure.
* Vários SDKs:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [API REST de Armazenamento](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Se utilizar o Armazenamento do __Lago de Dados Azure,__ consulte os seguintes links para obter formas de aceder aos seus dados:

* [Navegador web](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [CLI do Azure](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Ferramentas do Lago de Dados para Estúdio Visual](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Escalando o seu cluster

A função de dimensionamento do cluster permite alterar dinamicamente o número de nós de dados utilizados por um cluster. Pode realizar operações de escala enquanto outros trabalhos ou processos estão em execução num cluster.  Ver também, [Clusters De Escala HDInsight](./hdinsight-scaling-best-practices.md)

Os diferentes tipos de cluster são afetados pela escala da seguinte forma:

* **Hadoop**: Ao reduzir o número de nós num cluster, alguns dos serviços do cluster são reiniciados. As operações de escala podem provocar postos de trabalho em funcionamento ou pendentes para falhar no final da operação de escala. Pode reenviar os trabalhos assim que a operação estiver concluída.
* **HBase**: Os servidores regionais são automaticamente equilibrados dentro de poucos minutos, uma vez concluída a operação de escala. Para equilibrar manualmente os servidores regionais, utilize os seguintes passos:

    1. Ligue-se ao cluster HDInsight utilizando O SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Utilize o seguinte para iniciar a concha HBase:

            hbase shell

    3. Uma vez carregada a concha HBase, utilize o seguinte para equilibrar manualmente os servidores regionais:

            balancer

* **Tempestade**: Deve reequilibrar as topoologias da tempestade em execução depois de ter sido realizada uma operação de escala. O reequilíbrio permite que a topologia reajuste as definições de paralelismo com base no novo número de nós no cluster. Para reequilibrar as topoologias de execução, utilize uma das seguintes opções:

    * **SSH:** Ligue-se ao servidor e utilize o seguinte comando para reequilibrar uma topologia:

            storm rebalance TOPOLOGYNAME

        Também pode especificar parâmetros para anular as dicas de paralelismo originalmente fornecidas pela topologia. Por exemplo, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` reconfigura a topologia a 5 processos de trabalhador, 3 executores para o componente de bico azul e 10 executores para o componente de parafusoamarelo.

    * **Tempestade UI:** Use os seguintes passos para reequilibrar uma topologia usando a Tempestade UI.

        1. Abra `https://CLUSTERNAME.azurehdinsight.net/stormui` no seu navegador web, onde `CLUSTERNAME` é o nome do seu cluster Storm. Se solicitado, introduza o nome e a palavra-passe do administrador do cluster HDInsight (administrador) especificado ao criar o cluster.
        2. Selecione a topologia que deseja reequilibrar e, em seguida, selecione o botão **Reequilibrar.** Introduza o atraso antes da operação de reequilíbrio ser executada.

* **Kafka**: Deve reequilibrar as réplicas da partição após operações de escala. Para mais informações, consulte a alta disponibilidade de dados com Apache Kafka no documento [HDInsight.](./kafka/apache-kafka-high-availability.md)

Para obter informações específicas sobre a escala do seu cluster HDInsight, consulte:

* [Gerir os clusters Apache Hadoop em HDInsight utilizando o portal Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gerir os clusters Apache Hadoop em HDInsight utilizando o Azure CLI](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Como instalar o Hue (ou outro componente Hadoop)?

HDInsight é um serviço gerido. Se o Azure detetar um problema com o cluster, pode eliminar o nó de falha e criar um nó para o substituir. Se instalar manualmente as coisas no cluster, não persistem quando esta operação ocorre. Em vez disso, utilize [as ações do script HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Uma ação de script pode ser usada para fazer as seguintes alterações:

* Instale e configure um serviço ou web site.
* Instale e configure um componente que exija alterações de configuração em vários nós do cluster.

As ações do guião são guiões bash. Os scripts são executados durante a criação do cluster, e são usados para instalar e configurar componentes adicionais. Para obter informações sobre o desenvolvimento das suas próprias Ações de Script, consulte [Desenvolvimento de Ações de Script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Ficheiros jar

Algumas tecnologias Hadoop são fornecidas em ficheiros de frascos autossuficientes que contêm funções usadas como parte de um trabalho MapReduce, ou de dentro de Porco ou Colmeia. Muitas vezes não requerem configuração, e podem ser enviados para o cluster após a criação e usados diretamente. Se pretender certificar-se de que o componente sobrevive à reimagem do cluster, pode armazenar o ficheiro do frasco no armazenamento predefinido para o seu cluster (WASB ou ADL).

Por exemplo, se quiser utilizar a versão mais recente do [Apache DataFu,](https://datafu.incubator.apache.org/)pode descarregar um frasco contendo o projeto e carregá-lo para o cluster HDInsight. Em seguida, siga a documentação DataFu sobre como usá-lo a partir de Porco ou Colmeia.

> [!IMPORTANT]  
> Alguns componentes que são ficheiros de frascos autónomos são fornecidos com HDInsight, mas não estão no caminho. Se estiver à procura de um componente específico, pode utilizar o seguinte para o procurar no seu cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Este comando devolve o caminho de quaisquer ficheiros de frascos correspondentes.

Para utilizar uma versão diferente de um componente, faça upload da versão de que necessita e use-a nos seus trabalhos.

> [!IMPORTANT]
> Os componentes fornecidos com o cluster HDInsight são totalmente suportados e o Microsoft Support ajuda a isolar e resolver problemas relacionados com estes componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. Isto pode resultar na resolução do problema ou pedir-lhe para envolver canais disponíveis para as tecnologias de código aberto onde se encontra uma profunda experiência para essa tecnologia. Por exemplo, existem muitos sites comunitários que podem ser usados, como: [Fórum MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Também os projetos Apache têm sites de projetos em [https://apache.org, ](https://apache.org)por exemplo: [Hadoop,](https://hadoop.apache.org/) [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Passos seguintes

* [Gerir os clusters HDInsight utilizando a API De REPOUSO Apache Ambari](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)
