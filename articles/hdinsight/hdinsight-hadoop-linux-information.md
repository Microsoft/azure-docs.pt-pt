---
title: Dicas para usar Hadoop em HDInsight - Azure baseado em Linux
description: Obtenha dicas de implementação para usar clusters HDInsight (Hadoop) baseados em Linux num ambiente linux familiar que funciona na nuvem Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 1b3c694b4d6134f30d04ba8bafee9a6ffabdd959
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488117"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informações sobre como utilizar o HDInsight no Linux

Os clusters Azure HDInsight fornecem Apache Hadoop num ambiente linux familiar, correndo na nuvem Azure. Para a maioria das coisas, deve funcionar exatamente como qualquer outra instalação Hadoop-on-Linux. Este documento chama a atenção para diferenças específicas que deve estar ciente.

## <a name="prerequisites"></a>Pré-requisitos

Muitos dos passos deste documento utilizam os seguintes utilitários, que podem ter de ser instalados no seu sistema.

* [cURL](https://curl.haxx.se/) - usado para comunicar com serviços baseados na web.
* **jq**, um processador JSON de linha de comando.  [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)Ver.
* [Azure CLI](/cli/azure/install-azure-cli) - usado para gerir remotamente os serviços Azure.
* **Um cliente SSH**. Para obter mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Utilizadores

A menos que [o domínio se junte a](./domain-joined/hdinsight-security-overview.md)domínio, o HDInsight deve ser considerado um sistema de utilizador **único.** Uma única conta de utilizador SSH é criada com o cluster, com permissões de nível de administrador. Podem ser criadas contas SSH adicionais, mas também têm acesso ao cluster.

O HDInsight, acompanhado pelo domínio, suporta vários utilizadores e mais permissões granulares e definições de função. Para obter mais informações, consulte [os clusters HDInsight unidos pelo Domínio.](./domain-joined/apache-domain-joined-manage.md)

## <a name="domain-names"></a>Nomes de domínio

O nome de domínio totalmente qualificado (FQDN) para utilizar ao ligar ao cluster da internet é `CLUSTERNAME.azurehdinsight.net` ou `CLUSTERNAME-ssh.azurehdinsight.net` (apenas para SSH).

Internamente, cada nó no cluster tem um nome que é atribuído durante a configuração do cluster. Para encontrar os nomes do cluster, consulte a página **hosts** na UI web Ambari. Também pode usar o seguinte para devolver uma lista de anfitriões da AMBAri REST API:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'
```

Substitua `CLUSTERNAME` pelo nome do cluster. Quando solicitado, introduza a palavra-passe para a conta de administração. Este comando devolve um documento JSON que contém uma lista dos anfitriões no cluster. [jq](https://stedolan.github.io/jq/) é usado para extrair o valor do `host_name` elemento para cada hospedeiro.

Se precisar de encontrar o nome do nó para um serviço específico, pode consultar a Ambari para esse componente. Por exemplo, para encontrar os anfitriões para o nó de nome HDFS, utilize o seguinte comando:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'
```

Este comando devolve um documento JSON descrevendo o serviço, e então [jq](https://stedolan.github.io/jq/) retira apenas o `host_name` valor para os anfitriões.

## <a name="remote-access-to-services"></a>Acesso remoto aos serviços

* **Ambari (web)** - `https://CLUSTERNAME.azurehdinsight.net`

    Autenticar utilizando o utilizador e a palavra-passe do administrador do cluster e, em seguida, iniciar súpido no Ambari.

    A autenticação é texto simples - utilize sempre HTTPS para ajudar a garantir que a ligação está segura.

    > [!IMPORTANT]  
    > Algumas das UIs web disponíveis através de nós de acesso Ambari usando um nome de domínio interno. Os nomes de domínio interno não são acessíveis ao público através da internet. Pode receber erros de "servidor não encontrado" ao tentar aceder a algumas funcionalidades através da Internet.
    >
    > Para utilizar a funcionalidade completa da UI web Ambari, use um túnel SSH para proxy tráfego web para o nó de cabeça de cluster. Consulte [o Use SSH Tunneling para aceder à Web UI apache Ambari, ResourceManager, JobHistory, NameNode, Oozie e outros UIs web](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Autenticar utilizando o utilizador e a palavra-passe do administrador de cluster.
    >
    > A autenticação é texto simples - utilize sempre HTTPS para ajudar a garantir que a ligação está segura.

* **WebHCat (Templeton)** - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Autenticar utilizando o utilizador e a palavra-passe do administrador de cluster.
    >
    > A autenticação é texto simples - utilize sempre HTTPS para ajudar a garantir que a ligação está segura.

* **SSH** - CLUSTERNAME-ssh.azurehdinsight.net na porta 22 ou 23. A porta 22 é utilizada para ligar ao cabeçano primário, enquanto 23 é usada para ligar ao secundário. Para obter mais informações sobre os nós da cabeça, consulte [disponibilidade e fiabilidade dos clusters Apache Hadoop em HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Só é possível aceder aos nós da cabeça do cluster através do SSH a partir de uma máquina cliente. Uma vez ligados, pode então aceder aos nós do trabalhador utilizando SSH a partir de um headnode.

Para mais informações, consulte os [Ports utilizados pelos serviços Apache Hadoop no documento HDInsight.](hdinsight-hadoop-port-settings-for-services.md)

## <a name="file-locations"></a>Localizações dos ficheiros

Os ficheiros relacionados com hadoop podem ser encontrados nos nós de cluster em `/usr/hdp` . Este diretório contém as seguintes subdireções:

* **2.6.5.3009-43**: O nome do diretório é a versão da plataforma Hadoop utilizada pela HDInsight. O número do seu aglomerado pode ser diferente do listado aqui.
* **corrente**: Este diretório contém ligações a subdireções ao abrigo do diretório **2.6.5.3009-43.** Este diretório existe para que não tenha que se lembrar do número da versão.

Os dados de exemplo e os ficheiros JAR podem ser encontrados no Sistema de Ficheiros Distribuídos Hadoop em `/example` e `/HdiSamples` .

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Armazenamento Azure e Armazenamento do Lago de Dados

Na maioria das distribuições de Hadoop, os dados são armazenados em HDFS. O HDFS é apoiado pelo armazenamento local nas máquinas do cluster. A utilização de armazenamento local pode ser dispendiosa para uma solução baseada na nuvem, onde é cobrado de hora a hora ou por minuto para obter recursos de computação.

Ao utilizar o HDInsight, os ficheiros de dados são armazenados de forma adaptável e resiliente na nuvem utilizando o Azure Blob Storage e opcionalmente Azure Data Lake Storage Gen1/Gen2. Estes serviços proporcionam os seguintes benefícios:

* Armazenamento barato a longo prazo.
* Acessibilidade de serviços externos, tais como websites, serviços de upload de ficheiros/descarregamento, vários SDKs de idioma e navegadores web.
* Grande capacidade de ficheiro e grande armazenamento adaptável.

Para mais informações, consulte [o armazenamento Azure Blob,](../storage/common/storage-introduction.md) [Azure Data Lake Storage Gen1,](../data-lake-store/data-lake-store-overview.md)ou [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Ao utilizar o armazenamento Azure Blob ou o Data Lake Storage Gen1/Gen2, não tem de fazer nada de especial desde o HDInsight para aceder aos dados. Por exemplo, os seguintes ficheiros de comando na `/example/data` pasta, quer sejam armazenados no Armazenamento Azure ou no Armazenamento do Lago de Dados:

```console
hdfs dfs -ls /example/data
```

No HDInsight, os recursos de armazenamento de dados (Azure Blob Storage e Azure Data Lake Storage) são dissociados dos recursos computativos. Pode criar clusters HDInsight para fazer a computação conforme precisar e, mais tarde, eliminar o cluster quando o trabalho estiver terminado. Entretanto, manter os seus ficheiros de dados persistiu em segurança no armazenamento em nuvem o tempo que for necessário.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>URI e esquema

Alguns comandos podem exigir que especifique o esquema como parte do URI ao aceder a um ficheiro. Por exemplo, o componente Storm-HDFS requer que especifique o esquema. Ao utilizar o armazenamento não padrão (armazenamento adicionado como armazenamento "adicional" ao cluster), deve utilizar sempre o esquema como parte do URI.

Ao utilizar [**o Azure Storage,**](./hdinsight-hadoop-use-blob-storage.md)utilize um dos seguintes esquemas URI:

* `wasb:///`: Aceder ao armazenamento predefinido utilizando uma comunicação não encriptada.

* `wasbs:///`: Aceder ao armazenamento predefinido utilizando comunicação encriptada.  O esquema de wasbs é suportado apenas a partir da versão 3.6 do HDInsight.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Utilizado na comunicação com uma conta de armazenamento não padrão. Por exemplo, quando tem uma conta de armazenamento adicional ou ao aceder aos dados armazenados numa conta de armazenamento acessível ao público.

Ao utilizar [**a Azure Data Lake Storage Gen2,**](./hdinsight-hadoop-use-data-lake-storage-gen2.md)utilize o seguinte esquema URI:

* `abfs://`: Aceder ao armazenamento predefinido utilizando comunicação encriptada.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Utilizado na comunicação com uma conta de armazenamento não padrão. Por exemplo, quando tem uma conta de armazenamento adicional ou ao aceder aos dados armazenados numa conta de armazenamento acessível ao público.

Ao utilizar [**a Azure Data Lake Storage Gen1,**](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)utilize um dos seguintes esquemas URI:

* `adl:///`: Aceda ao armazenamento predefinido do Data Lake para o cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Utilizado ao comunicar com um armazenamento não padrão do Lago de Dados. Também utilizado para aceder a dados fora do diretório de raiz do seu cluster HDInsight.

> [!IMPORTANT]  
> Ao utilizar o Data Lake Storage como loja predefinitiva para HDInsight, deve especificar um caminho dentro da loja para utilizar como raiz do armazenamento HDInsight. O caminho padrão é `/clusters/<cluster-name>/` .
>
> Quando se utiliza `/` ou `adl:///` acede aos dados, só é possível aceder aos dados armazenados na raiz (por `/clusters/<cluster-name>/` exemplo,) do cluster. Para aceder a dados em qualquer lugar da loja, utilize o `adl://<storage-name>.azuredatalakestore.net/` formato.

### <a name="what-storage-is-the-cluster-using"></a>Que armazenamento é o cluster usando

Pode utilizar ambari para recuperar a configuração de armazenamento padrão para o cluster. Utilize o seguinte comando para recuperar informações de configuração HDFS utilizando caracóis e filtre-as utilizando [jq](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Este comando devolve a primeira configuração aplicada ao servidor ( `service_config_version=1` ), que contém esta informação. Pode ser necessário listar todas as versões de configuração para encontrar a mais recente.

Este comando devolve um valor semelhante aos seguintes URIs:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` se utilizar uma conta de Armazenamento Azure.

    O nome da conta é o nome da conta Azure Storage. O nome do recipiente é o recipiente blob que é a raiz do armazenamento do cluster.

* `adl://home` se utilizar o Azure Data Lake Storage. Para obter o nome de armazenamento do Data Lake, use a seguinte chamada REST:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Este comando devolve o seguinte nome de anfitrião: `<data-lake-store-account-name>.azuredatalakestore.net` .

    Para obter o diretório dentro da loja que é a raiz para HDInsight, use a seguinte chamada REST:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Este comando devolve um caminho semelhante ao seguinte caminho: `/clusters/<hdinsight-cluster-name>/` .

Também pode encontrar as informações de armazenamento utilizando o portal Azure utilizando os seguintes passos:

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster HDInsight.

2. Na secção **Propriedades,** selecione **Contas de Armazenamento.** As informações de armazenamento do cluster são apresentadas.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Como posso aceder a ficheiros de fora do HDInsight

Existem várias formas de aceder a dados de fora do cluster HDInsight. Seguem-se algumas ligações a utilitários e SDKs que podem ser utilizados para trabalhar com os seus dados:

Se utilizar __o armazenamento Azure Blob,__ consulte os seguintes links para obter formas de aceder aos seus dados:

* [Azure CLI](/cli/azure/install-az-cli2): comandos de interface Command-Line para trabalhar com o Azure. Depois de instalar, utilize o `az storage` comando para obter ajuda na utilização do armazenamento ou para `az storage blob` comandos específicos da bolha.
* [blobxfer.py](https://github.com/Azure/blobxfer): Um roteiro python para trabalhar com bolhas no Azure Storage.
* Vários SDKs:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [API REST de Armazenamento](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Se utilizar __a Azure Data Lake Storage Gen1,__ consulte os seguintes links para obter formas de aceder aos seus dados:

* [Navegador web](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [CLI do Azure](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Ferramentas do Lago de Dados para Estúdio Visual](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>Escalar o seu cluster

A funcionalidade de escala de cluster permite alterar dinamicamente o número de nós de dados utilizados por um cluster. Você pode fazer operações de escala enquanto outros trabalhos ou processos estão funcionando em um cluster.  Ver [clusters hdinsight escala](./hdinsight-scaling-best-practices.md)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Como posso instalar o Hue (ou outro componente Hadoop)?

HDInsight é um serviço gerido. Se o Azure detetar um problema com o cluster, pode apagar o nó de falha e criar um nó para o substituir. Quando instala manualmente coisas no cluster, elas não persistem quando esta operação ocorre. Em vez disso, utilize [ações de script HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Uma ação de script pode ser usada para fazer as seguintes alterações:

* Instale e configuure um serviço ou web site.
* Instale e configuure um componente que exija alterações de configuração em múltiplos nós no cluster.

As ações do script são guiões bash. Os scripts são executados durante a criação do cluster, e são usados para instalar e configurar componentes adicionais. Para obter informações sobre o desenvolvimento das suas próprias Ações de Script, consulte [Desenvolvimento de Ações de Script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Ficheiros de frascos

Algumas tecnologias Hadoop fornecem ficheiros de frascos independentes. Estes ficheiros contêm funções usadas como parte de um trabalho mapReduce, ou de dentro de Porco ou Colmeia. Muitas vezes não requerem qualquer configuração, e podem ser enviados para o cluster após a criação e usados diretamente. Se quiser certificar-se de que o componente sobrevive à reimagem do cluster, guarde o ficheiro do frasco no armazenamento predefinido do cluster.

Por exemplo, se quiser utilizar a versão mais recente do [Apache DataFu,](https://datafu.incubator.apache.org/)pode descarregar um frasco que contém o projeto e carregá-lo para o cluster HDInsight. Em seguida, siga a documentação dataFu sobre como usá-lo a partir de Porco ou Colmeia.

> [!IMPORTANT]  
> Alguns componentes que são ficheiros de frascos autónomos são fornecidos com HDInsight, mas não estão no caminho. Se estiver à procura de um componente específico, pode utilizar o seguinte para o procurar no seu cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Este comando devolve o caminho de quaisquer ficheiros de frascos correspondentes.

Para utilizar uma versão diferente de um componente, faça o upload da versão de que necessita e use-a nos seus trabalhos.

> [!IMPORTANT]
> Os componentes fornecidos com o cluster HDInsight são totalmente suportados e o Microsoft Support ajuda a isolar e resolver problemas relacionados com estes componentes.
>
> Os componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. Isto pode resultar na resolução do problema ou pedir-lhe para envolver canais disponíveis para as tecnologias de código aberto onde se encontram conhecimentos profundos para essa tecnologia. Por exemplo, existem muitos sites comunitários que podem ser usados, como: [Microsoft Q&Uma página de perguntas para HDInsight](https://docs.microsoft.com/answers/topics/azure-hdinsight.html), [https://stackoverflow.com](https://stackoverflow.com) . Também os projetos Apache têm sites de projeto em [https://apache.org](https://apache.org) , por exemplo: [Hadoop,](https://hadoop.apache.org/) [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Passos seguintes

* [Gerir os clusters HDInsight utilizando a API Apache Ambari REST](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)
