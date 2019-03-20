---
title: Dicas para usar o Hadoop no HDInsight baseado em Linux - Azure
description: Obtenha dicas de implementação para a utilização de clusters do HDInsight baseado em Linux (Hadoop) num ambiente Linux familiar em execução na cloud do Azure.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/09/2018
ms.openlocfilehash: 43b672569b398f636b2e02172428cf072febb156
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202457"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informações sobre como utilizar o HDInsight no Linux

Os clusters de HDInsight do Azure fornecem Apache Hadoop num ambiente Linux familiar, em execução na cloud do Azure. Para a maioria da coisas, deve funcionar exatamente como qualquer outra instalação do Hadoop no Linux. Este documento chama diferenças específicas que deve estar atento.

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="prerequisites"></a>Pré-requisitos

Muitos dos passos neste documento utilizam os utilitários a seguir, que poderão ter de ser instalado no seu sistema.

* [cURL](https://curl.haxx.se/) - utilizado para comunicar com serviços baseados na web.
* [jq](https://stedolan.github.io/jq/) - utilizado para analisar documentos JSON.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2) - utilizado para gerir remotamente serviços do Azure.

## <a name="users"></a>Utilizadores

A menos que [associados a um domínio](./domain-joined/apache-domain-joined-introduction.md), HDInsight deve ser considerado um **único usuário** system. Uma única conta de utilizador SSH é criada com o cluster, com permissões de nível de administrador. Contas SSH adicionais podem ser criadas, mas também têm acesso de administrador para o cluster.

HDInsight associado a um domínio oferece suporte a vários utilizadores e definições de permissão e a função mais granulares. Para obter mais informações, consulte [clusters do HDInsight associados a um domínio gerir](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Nomes de domínio

É o nome de domínio completamente qualificado (FQDN) a utilizar quando ligar ao cluster a partir da internet  **&lt;clustername >. azurehdinsight.net** ou (para SSH)  **&lt;clustername-ssh >. azurehdinsight.NET**.

Internamente, cada nó do cluster tem um nome que é atribuído durante a configuração de cluster. Para localizar os nomes de cluster, consulte a **anfitriões** página na IU da Web do Ambari. Também pode utilizar o seguinte para devolver uma lista de anfitriões a partir da API de REST do Ambari:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Substitua **CLUSTERNAME** pelo nome do cluster. Quando lhe for pedido, introduza a palavra-passe para a conta de administrador. Este comando devolve um documento JSON que contém uma lista de anfitriões no cluster. Jq é utilizado para extrair o `host_name` valor do elemento para cada anfitrião.

Se precisar de encontrar o nome do nó para um serviço específico, pode consultar o Ambari desse componente. Por exemplo, para localizar os anfitriões para o nó de nome HDFS, utilize o seguinte comando:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Este comando devolve um documento JSON que descreve o serviço e, em seguida, jq extrai apenas o `host_name` valor para os anfitriões.

## <a name="remote-access-to-services"></a>Acesso remoto aos serviços

* **(Web) do Ambari** - https://&lt;clustername >. azurehdinsight.net

    Autenticar com o utilizador de administrador de cluster e a palavra-passe e, em seguida, inicie sessão no Ambari.

    A autenticação é texto sem formatação – utilize sempre HTTPS para ajudar a garantir que a ligação é segura.

    > [!IMPORTANT]  
    > Algumas das interfaces do usuário disponíveis através do Ambari web aceder a nós com um nome de domínio interno. Nomes de domínio interno não são acessíveis publicamente através da internet. Poderá receber erros de "servidor não encontrado" ao tentar acessar alguns recursos através da Internet.
    >
    > Para utilizar a funcionalidade completa da IU web do Ambari, utilize um túnel SSH para o tráfego de web de proxy para o nó principal do cluster. Consulte [utilizar túnel SSH para aceder à IU web do Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces do usuário da web](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;clustername >.azurehdinsight.net/ambari

    > [!NOTE]  
    > Autenticar com o utilizador de administrador de cluster e a palavra-passe.
    >
    > A autenticação é texto sem formatação – utilize sempre HTTPS para ajudar a garantir que a ligação é segura.

* **(Templeton) de WebHCat** - https://&lt;clustername >.azurehdinsight.net/templeton

    > [!NOTE]  
    > Autenticar com o utilizador de administrador de cluster e a palavra-passe.
    >
    > A autenticação é texto sem formatação – utilize sempre HTTPS para ajudar a garantir que a ligação é segura.

* **SSH** - &lt;clustername >-ssh.azurehdinsight.net na porta 22 ou 23. A porta 22 é utilizada para ligar ao nó principal primário, embora 23 é utilizada para ligar para o secundário. Para obter mais informações sobre os nós principais, consulte [disponibilidade e fiabilidade do Apache Hadoop clusters no HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Só pode aceder a nós principais do cluster através de SSH a partir de uma máquina de cliente. Assim que estiver ligado, em seguida, pode aceder os nós de trabalho ao utilizar o SSH a partir de um nó principal.

Para obter mais informações, consulte a [portas utilizadas pelos serviços do Apache Hadoop no HDInsight](hdinsight-hadoop-port-settings-for-services.md) documento.

## <a name="file-locations"></a>Localizações de ficheiros

Ficheiros relacionados com o Hadoop podem ser encontrados em nós de cluster em `/usr/hdp`. Esse diretório contém os subdiretórios do seguintes:

* **2.2.4.9-1**: O nome do diretório é a versão da plataforma de dados Hortonworks utilizado pelo HDInsight. O número do seu cluster pode ser diferente dos listados aqui.
* **current**: Este diretório contém links para os subdiretórios abaixo a **2.2.4.9-1** diretório. Este diretório existe para que não precisa se lembrar o número de versão.

Dados de exemplo e ficheiros JAR podem ser encontrados no Hadoop sistema de ficheiros distribuído no `/example` e `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, o armazenamento do Azure e o armazenamento do Data Lake

Na maioria das distribuições de Hadoop, os dados são armazenados no HDFS, o que é suportado por armazenamento local nas máquinas no cluster. Utilizar o armazenamento local pode ser dispendiosa para uma solução baseada na cloud em que é cobrado por hora ou por minuto para recursos de computação.

Ao utilizar o HDInsight, os ficheiros de dados são armazenados numa forma escalável e flexível na cloud com o armazenamento de Blobs do Azure e, opcionalmente, o armazenamento do Azure Data Lake. Estes serviços fornecem as seguintes vantagens:

* Armazenamento de longa duração barato.
* Acessibilidade de serviços externos, como Web sites, utilitários de carregamento/transferência de ficheiro, vários SDKs de idioma e navegadores da web.
* Capacidade de ficheiros grandes e grande armazenamento escalável.

Para obter mais informações, consulte [blobs compreensão](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) e [armazenamento do Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/).

Ao utilizar o armazenamento do Azure ou o armazenamento do Data Lake, não precisa de fazer nada de especial do HDInsight para aceder aos dados. Por exemplo, o comando seguinte lista os ficheiros no `/example/data` pasta, independentemente de se ele é armazenado no armazenamento do Azure ou no armazenamento do Data Lake:

    hdfs dfs -ls /example/data

No HDInsight, os recursos de armazenamento de dados (armazenamento de Blobs do Azure e armazenamento do Azure Data Lake) estão desassociados dos recursos de computação. Portanto, pode criar clusters do HDInsight para fazer a computação à medida que precisa e posteriormente elimina o cluster quando o trabalho estiver concluído, ao mesmo tempo, manter os ficheiros de dados que permanecem em segurança no armazenamento na cloud, desde que precisa.


### <a name="URI-and-scheme"></a>URI e o esquema

Alguns comandos podem exigir que especifique o esquema como parte do URI, ao aceder a um ficheiro. Por exemplo, o componente de Storm-HDFS exige que especifique o esquema. Quando utiliza o armazenamento de não-padrão (adicionado como armazenamento de "adicional" para o cluster de armazenamento), tem de utilizar sempre o esquema como parte do URI.

Ao usar __armazenamento do Azure__, utilize um dos esquemas URI seguintes:

* `wasb:///`: Aceder ao armazenamento de predefinido com comunicação desencriptada.

* `wasbs:///`: Armazenamento de padrão de acesso através da comunicação criptografada.  O esquema wasbs é suportado apenas a partir do HDInsight versão 3.6 ou posterior.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Utilizado quando estão a comunicar com uma conta de armazenamento não predefinido. Por exemplo, se tiver uma conta de armazenamento adicional ou quando aceder aos dados armazenados numa conta de armazenamento acessível publicamente.

Ao usar __geração 2 de armazenamento do Azure Data Lake__, utilize um dos esquemas URI seguintes:

* `abfs:///`: Aceder ao armazenamento de predefinido com comunicação desencriptada.

* `abfss:///`: Armazenamento de padrão de acesso através da comunicação criptografada.  O esquema de abfss é suportado apenas a partir do HDInsight versão 3.6 ou posterior.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Utilizado quando estão a comunicar com uma conta de armazenamento não predefinido. Por exemplo, se tiver uma conta de armazenamento adicional ou quando aceder aos dados armazenados numa conta de armazenamento acessível publicamente.

Ao usar __Gen1 de armazenamento do Azure Data Lake__, utilize um dos esquemas URI seguintes:

* `adl:///`: Acesso a predefinição de armazenamento do Data Lake para o cluster.

* `adl://<storage-name>.azuredatalakestore.net/`: Utilizado quando estão a comunicar com um armazenamento do Data Lake não predefinido. Também é utilizado para aceder aos dados fora do diretório de raiz do cluster do HDInsight.

> [!IMPORTANT]  
> Quando utiliza o armazenamento do Data Lake como o arquivo predefinido do HDInsight, tem de especificar um caminho no arquivo para utilizar como a raiz de armazenamento do HDInsight. O caminho predefinido é `/clusters/<cluster-name>/`.
>
> Ao usar `/` ou `adl:///` para aceder aos dados, só pode aceder a dados armazenados na raiz (por exemplo, `/clusters/<cluster-name>/`) do cluster. Para aceder aos dados em qualquer lugar da loja, utilize o `adl://<storage-name>.azuredatalakestore.net/` formato.

### <a name="what-storage-is-the-cluster-using"></a>Qual armazenamento é o cluster com

Pode utilizar Ambari para obter a configuração de armazenamento padrão para o cluster. Utilize o seguinte comando para obter informações de configuração de HDFS com o curl e filtrá-la usando [jq](https://stedolan.github.io/jq/):

```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]  
> Este comando devolve a primeira configuração aplicada ao servidor (`service_config_version=1`), que contém essas informações. Terá de listar todas as versões de configuração para encontrar a mais recente.

Este comando devolve um valor semelhante para os URIs seguintes:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` Se utilizar uma conta de armazenamento do Azure.

    O nome da conta é o nome da conta do Storage do Azure. O nome do contentor é o contentor de BLOBs que é a raiz do armazenamento de cluster.

* `adl://home` Se utilizar o armazenamento do Azure Data Lake. Para obter o nome de armazenamento do Data Lake, utilize a seguinte chamada REST:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    Este comando devolve o seguinte nome de anfitrião: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Para obter o diretório no arquivo que é a raiz para o HDInsight, utilize a seguinte chamada REST:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    Este comando devolve um caminho semelhante para o seguinte caminho: `/clusters/<hdinsight-cluster-name>/`.

Também pode encontrar as informações de armazenamento utilizando o portal do Azure, utilizando os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), selecione o cluster do HDInsight.

2. Partir do **propriedades** secção, selecione **contas de armazenamento**. As informações de armazenamento para o cluster são apresentadas.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Como posso aceder a ficheiros do HDInsight externo

Há uma várias formas de aceder a dados a partir de fora do cluster do HDInsight. Seguem-se algumas ligações para utilitários e SDKs que podem ser usados para trabalhar com os seus dados:

Se utilizar __armazenamento do Azure__, consulte as seguintes ligações para formas que pode aceder aos seus dados:

* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2): Comandos de interface de linha de comandos para trabalhar com o Azure. Depois de instalar, utilizar o `az storage` comando para obter ajuda sobre como utilizar o armazenamento, ou `az storage blob` para comandos específicos de Blobs.
* [blobxfer.py](https://github.com/Azure/blobxfer): Um script de python para trabalhar com blobs no armazenamento do Azure.
* Vários SDKs:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [API REST de Armazenamento](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Se utilizar __armazenamento do Azure Data Lake__, consulte as seguintes ligações para formas que pode aceder aos seus dados:

* [Navegador da Web](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [CLI do Azure](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [API de REST do WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Ferramentas do Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Dimensionamento do seu cluster

O funcionalidade de dimensionamento do cluster permite alterar dinamicamente o número de nós de dados utilizado por um cluster. Pode efetuar operações de dimensionamento enquanto outras tarefas ou processos em execução num cluster.

Os tipos de cluster diferentes são afetados pelo dimensionamento da seguinte forma:

* **Hadoop**: Ao reduzir verticalmente o número de nós num cluster, alguns dos serviços do cluster são reiniciados. Operações de dimensionamento podem fazer com tarefas em execução ou pendente a falhar após a conclusão da operação de dimensionamento. Pode submeter novamente as tarefas depois de concluída a operação.
* **HBase**: Servidores regionais são balanceadas automaticamente dentro de alguns minutos, uma vez concluída a operação de dimensionamento. Manualmente balancear servidores regionais, utilize os seguintes passos:

    1. Ligue ao cluster do HDInsight através de SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Utilize o seguinte para iniciar o shell de HBase:

            hbase shell

    3. Assim que a shell de HBase for carregada, utilize o seguinte para equilibrar manualmente os servidores regionais:

            balancer

* **Storm**: Deve reequilibrar qualquer topologias Storm em execução depois de uma operação de dimensionamento foi realizada. Reequilibrar permite que a topologia de reajustar os definições de paralelismo com base no número de nós no cluster novo. Reequilibrar as topologias em execução, utilize uma das seguintes opções:

    * **SSH**: Ligar ao servidor e utilize o seguinte comando para reequilibrar uma topologia:

            storm rebalance TOPOLOGYNAME

        Também pode especificar parâmetros para substituir as sugestões de paralelismo originalmente fornecidas pela topologia. Por exemplo, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` reconfigura a topologia de 5 processos de trabalho, 3 executores para o componente de spout de azul e 10 executores para o componente de amarelo-bolt.

    * **IU do Storm**: Utilize os seguintes passos para reequilibrar uma topologia na IU do Storm.

        1. Open **https://CLUSTERNAME.azurehdinsight.net/stormui** no seu navegador da web, em que CLUSTERNAME é o nome do cluster do Storm. Se lhe for pedido, introduza o nome de administrador (administrador) de cluster do HDInsight e a palavra-passe que especificou ao criar o cluster.
        2. Selecionar a topologia de que pretende reequilibrar, em seguida, selecione o **reequilibrar** botão. Introduza o atraso antes da operação de reequilíbrio é executada.

* **Kafka**: Deve reequilibrar as réplicas de partições após operações de dimensionamento. Para obter mais informações, consulte a [elevada disponibilidade dos dados com o Apache Kafka no HDInsight](./kafka/apache-kafka-high-availability.md) documento.

Para obter informações específicas sobre como aumentar o seu cluster do HDInsight, consulte:

* [Gerir clusters do Apache Hadoop no HDInsight com o portal do Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gerir clusters do Apache Hadoop no HDInsight com o Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Como posso instalar o Hue (ou outros componentes do Hadoop)?

HDInsight é um serviço gerido. Se o Azure detetar um problema com o cluster, pode eliminar o nó com falha e criar um nó para substituí-lo. Se instalar manualmente as coisas no cluster, estas não são mantidas quando esta operação ocorre. Em alternativa, utilize [ações de Script do HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Uma ação de script pode ser utilizada para efetuar as seguintes alterações:

* Instalar e configurar um serviço ou o web site.
* Instale e configure um componente que requer alterações de configuração em vários nós no cluster.

Ações de script são scripts de Bash. Os scripts são executados durante a criação do cluster e são utilizados para instalar e configurar componentes adicionais. Scripts de exemplo são fornecidos para instalar os seguintes componentes:

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)

Para obter informações sobre o desenvolvimento das suas próprias Ações de Script, consulte [Desenvolvimento de Ações de Script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Ficheiros JAR

Algumas tecnologias Hadoop são fornecidas nos ficheiros jar autónomo que contêm funções usadas como parte de uma tarefa de MapReduce ou a partir de dentro de Pig ou do Hive. Muitas vezes, não requerem nenhuma configuração e podem ser carregados no cluster após a criação e usados diretamente. Se pretender certificar-se de que o componente sobrevive a recriação da imagem do cluster, pode armazenar o ficheiro jar no armazenamento padrão para o seu cluster (WASB ou ADL).

Por exemplo, se desejar usar a versão mais recente do [Apache DataFu](https://datafu.incubator.apache.org/), pode transferir um jar que contém o projeto e carregue-o para o cluster do HDInsight. Em seguida, siga a documentação de DataFu sobre como usá-lo a partir do Pig ou do Hive.

> [!IMPORTANT]  
> Alguns componentes que são arquivos de jar autónomo são fornecidos com o HDInsight, mas não estão no caminho. Se estiver procurando por um componente específico, pode utilizar a seguir para pesquisá-la no seu cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Este comando devolve o caminho de todos os ficheiros jar correspondente.

Para utilizar uma versão diferente de um componente, carregue a versão que precisa e utilizá-lo nas suas tarefas.

> [!WARNING]
> Componentes fornecidos com o cluster do HDInsight são totalmente suportadas e Support da Microsoft ajuda a isolar e resolver problemas relacionados com esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a resolver o problema. Isso pode resultar em resolver o problema ou pedir-lhe para interagir com os canais disponíveis para as tecnologias de código-fonte aberto, onde os conhecimentos aprofundados para essa tecnologia é encontrado. Por exemplo, há muitos sites de Comunidade que podem ser utilizados, como: [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Também projetos Apache tem sites de projeto no [ https://apache.org ](https://apache.org), por exemplo: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Passos Seguintes

* [Migrar do HDInsight baseado em Windows para baseado em Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Utilizar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)
