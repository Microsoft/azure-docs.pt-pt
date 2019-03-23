---
title: Migrar do HDInsight baseado em Windows para o HDInsight baseado em Linux - Azure
description: Saiba como migrar a partir de um cluster do HDInsight baseado em Windows a um cluster do HDInsight baseado em Linux.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: hrasheed
ms.openlocfilehash: 02f698d531555aa9b5498060918a2a361b28817e
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361256"
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrar de um cluster do HDInsight baseado em Windows para um cluster baseado em Linux

Este documento fornece detalhes sobre as diferenças entre o HDInsight no Windows e Linux. Também fornece orientações sobre como migrar cargas de trabalho existentes para um cluster baseado em Linux.

Enquanto o HDInsight baseado em Windows fornece uma forma fácil de utilizar o Apache Hadoop na cloud, terá de migrar para um cluster baseado em Linux. Por exemplo, para tirar partido de ferramentas baseadas em Linux e tecnologias que são necessárias para a sua solução. Muitas coisas no ecossistema do Hadoop são desenvolvidas em sistemas baseados em Linux e poderão não estar disponíveis para utilização com o HDInsight baseado em Windows. Muitos livros, vídeos e outros materiais de treinamento partem do princípio de que está a utilizar um sistema Linux ao trabalhar com o Hadoop.

> [!NOTE]  
> Clusters do HDInsight utilizam o suporte de longo prazo do Ubuntu (LTS) como o sistema operativo para os nós do cluster. Para obter informações sobre a versão do Ubuntu disponível com o HDInsight, juntamente com outras informações de controlo de versões de componente, consulte [versões de componente de HDInsight](hdinsight-component-versioning.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migration-tasks"></a>Tarefas de migração

Segue-se o fluxo de trabalho geral para a migração.

![Diagrama de fluxo de trabalho de migração](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Leia cada secção deste documento para compreender as alterações que possam ser necessárias durante a migração.

2. Crie um cluster baseado em Linux como um ambiente de garantia de qualidade/teste. Para obter mais informações sobre como criar um cluster baseado em Linux, consulte [baseado em Linux criar clusters no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Copie as tarefas existentes e Coletores de origens de dados para o novo ambiente.

4. Execute os testes de validação para se certificar de que as tarefas funcionam conforme esperado no novo cluster.

Assim que tiver verificado que tudo funciona conforme esperado, agende o período de indisponibilidade para a migração. Durante este período de indisponibilidade, execute as seguintes ações:

1. Fazer backup de todos os dados transitórios armazenados localmente em nós do cluster. Por exemplo, se tiver dados armazenados diretamente num nó principal.

2. Elimine o cluster baseado em Windows.

3. Crie um cluster baseado em Linux utilizar o mesmo arquivo de dados predefinida que o cluster baseado em Windows utilizado. O cluster baseado em Linux pode continuar a trabalhar em relação a seus dados de produção existente.

4. Importe qualquer cópia de segurança de dados transitórios.

5. Iniciar tarefas/continuam a utilizar o novo cluster de processamento.

### <a name="copy-data-to-the-test-environment"></a>Copiar dados para o ambiente de teste

Existem vários métodos para copiar os dados e tarefas, no entanto os dois abordados nesta seção são os métodos mais simples para diretamente move arquivos para um cluster de teste.

#### <a name="hdfs-copy"></a>Cópia do HDFS

Utilize os seguintes passos para copiar dados do cluster de produção para o cluster de teste. Estes passos utilizam o `hdfs dfs` utilitário que está incluído no HDInsight.

1. Localize as informações de contentor de armazenamento conta e padrão, para o seu cluster existente. O exemplo seguinte utiliza o PowerShell para obter estas informações:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Para criar um ambiente de teste, siga os passos nos clusters baseados em Linux criar documento de HDInsight. Pare antes de criar o cluster e, em vez disso, selecione **configuração opcional**.

3. Na secção de configuração opcionais, selecione **contas de armazenamento ligadas**.

4. Selecione **adicionar uma chave de armazenamento**e quando lhe for pedido, selecione a conta de armazenamento que foi devolvida pelo script do PowerShell no passo 1. Clique em **selecione** em cada secção. Por fim, crie o cluster.

5. Depois do cluster ter sido criado, ligá-la utilizando **SSH.** Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

6. A partir da sessão SSH, utilize o seguinte comando para copiar ficheiros de conta de armazenamento associada a essa conta de armazenamento predefinida. Substitua o contentor com as informações de contentor devolvidas pelo PowerShell. Substitua __conta__ com o nome da conta. Substitua o caminho para dados com o caminho para um ficheiro de dados.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]  
    > Se a estrutura do diretório que contém os dados não existe no ambiente de teste, pode criar usando o seguinte comando:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    O `-p` comutador permite a criação de todos os diretórios no caminho.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Cópia direta entre os blobs no armazenamento do Azure

Em alternativa, talvez queira usar o `Start-AzStorageBlobCopy` cmdlet do PowerShell do Azure para copiar blobs entre contas de armazenamento fora do HDInsight. Para obter mais informações, consulte como gerir a secção de Blobs do Azure de utilizar o Azure PowerShell com armazenamento do Azure.

## <a name="client-side-technologies"></a>Tecnologias do lado do cliente

Tecnologias do lado do cliente, como [cmdlets do Azure PowerShell](/powershell/azureps-cmdlets-docs), [CLI clássica do Azure](../cli-install-nodejs.md), ou o [SDK de .NET para o Apache Hadoop](https://hadoopsdk.codeplex.com/) continuar a trabalhar clusters baseados em Linux. Essas tecnologias se baseiam nas APIs REST, que são os mesmos em ambos os tipos de SO do cluster.

## <a name="server-side-technologies"></a>Tecnologias do lado do servidor

A tabela seguinte fornece orientações sobre migração componentes do lado do servidor que são específicas do Windows.

| Se estiver a utilizar esta tecnologia... | Executar esta ação... |
| --- | --- |
| **PowerShell** (scripts do lado do servidor, incluindo ações de Script utilizada durante a criação do cluster) |Reescrever como scripts de Bash. Para ações de Script, consulte [baseado em Linux personalizar o HDInsight com ações de Script](hdinsight-hadoop-customize-cluster-linux.md) e [Script o desenvolvimento de ação para o HDInsight baseado em Linux](hdinsight-hadoop-script-actions-linux.md). |
| **Azure CLI clássica** (scripts do lado do servidor) |Enquanto a CLI clássica do Azure está disponível no Linux, ele não vem pré-instalada sobre os nós principais do cluster de HDInsight. Para obter mais informações sobre como instalar a CLI clássica do Azure, consulte [introdução à CLI clássica do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **Componentes do .NET** |.NET é suportado no HDInsight baseado em Linux através de [Mono](https://mono-project.com). Para obter mais informações, consulte [soluções de .NET de migrar para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Componentes do Win32 ou outra tecnologia de só de Windows** |Documentação de orientação depende do componente ou tecnologia. Poderá encontrar uma versão compatível com o Linux. Caso contrário, tem de encontrar uma solução alternativa ou reescrever este componente. |

> [!IMPORTANT]  
> A gestão do HDInsight SDK não é totalmente compatível com o Mono. Não usá-lo como parte das soluções que são implementadas para o cluster do HDInsight.

## <a name="cluster-creation"></a>Criação do cluster

Esta seção fornece informações sobre as diferenças na criação do cluster.

### <a name="ssh-user"></a>SSH utilizador

Clusters do HDInsight baseado em Linux utilizar o **Secure Shell (SSH)** protocolo para fornecer acesso remoto para os nós do cluster. Ao contrário dos clusters baseados no ambiente de trabalho remoto para Windows, a maioria dos clientes SSH não fornecem uma experiência de utilizador gráfica. Em vez disso, os clientes SSH fornecem uma linha de comando que permite-lhe executar comandos no cluster. Alguns clientes (por exemplo, [MobaXterm](https://mobaxterm.mobatek.net/)) fornecem um browser de sistema de ficheiros gráfica, além de uma linha de comandos remota.

Durante a criação do cluster, tem de fornecer um utilizador SSH e um uma **palavra-passe** ou **certificado de chave pública** para autenticação.

Recomendamos que utilize o certificado de chave pública, pois é mais seguro que utilizar uma palavra-passe. Autenticação de certificado funciona ao gerar um par de chaves públicas/privadas assinado, em seguida, fornecer a chave pública ao criar o cluster. Ao ligar ao servidor através de SSH, a chave privada no cliente fornece autenticação para a ligação.

Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Personalização do cluster

**Ações de script** utilizada com clusters baseados em Linux têm de ser escritos no script de Bash. Clusters baseados em Linux podem utilizar ações de script durante ou após a criação do cluster. Para obter mais informações, consulte [baseado em Linux personalizar o HDInsight com ações de Script](hdinsight-hadoop-customize-cluster-linux.md) e [Script o desenvolvimento de ação para o HDInsight baseado em Linux](hdinsight-hadoop-script-actions-linux.md).

Outro recurso de personalização é **bootstrap**. Para os clusters do Windows, esta funcionalidade permite-lhe especificar a localização de bibliotecas adicionais para utilização com o Hive. Após a criação do cluster, essas bibliotecas estão automaticamente disponíveis para utilização com consultas do Hive, sem a necessidade de usar `ADD JAR`.

A funcionalidade de arranque de configuração para clusters baseados em Linux não fornece essa funcionalidade. Em alternativa, utilize a ação de script documentada em [bibliotecas de adicionar o Apache Hive durante a criação do cluster](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Redes Virtuais

Com base em Windows HDInsight clusters só funcionam com redes virtuais clássicas, enquanto os clusters de HDInsight baseado em Linux requerem redes do Resource Manager Virtual. Se tiver recursos numa rede Virtual clássica que tem de ligar ao cluster HDInsight do Linux, veja [ligar uma rede Virtual clássica a uma rede Virtual do Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Para obter mais informações sobre os requisitos de configuração, consulte a [capacidades de estender o HDInsight com uma rede Virtual](hdinsight-extend-hadoop-virtual-network.md) documento.

## <a name="management-and-monitoring"></a>Gestão e monitorização

Muitos dos web interfaces do usuário pode ter sido utilizada com o HDInsight baseado em Windows, como o histórico de tarefas ou a IU do Yarn, estão disponíveis através do Apache Ambari. Além disso, a vista do Ambari Hive fornece uma forma de executar consultas do Hive com o seu navegador da web. A interface do Usuário da Web de Ambari está disponível em clusters baseados em Linux no https://CLUSTERNAME.azurehdinsight.net.

Para obter mais informações sobre como trabalhar com o Ambari, consulte os seguintes documentos:

* [Apache Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [API de REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alertas do Ambari

O Ambari possui um sistema de alerta capaz de dizer de potenciais problemas com o cluster. Alertas são apresentados como entradas de amarelos ou vermelhas na IU da Web do Ambari, no entanto, também pode obtê-las através da API REST.

> [!IMPORTANT]  
> Alertas de Ambari indicam que existem *poderá* ser um problema, não que existem *é* um problema. Por exemplo, poderá receber um alerta que não pode ser acedido HiveServer2, mesmo que pode acessá-lo normalmente.
>
> Muitos alertas são implementadas como baseada no intervalo de consultas em relação a um serviço e esperam uma resposta num período de tempo específico. Para que o alerta não significa necessariamente que o serviço está desativado, mas não devolvem resultados no período de tempo esperado.

## <a name="file-system-locations"></a>Localizações de sistema de ficheiros

O sistema de ficheiros de cluster do Linux é disposto de forma diferente do que os clusters do HDInsight baseado em Windows. Utilize a seguinte tabela para localizar utilizado frequentemente ficheiros.

| Preciso de encontrar... | Ele está localizado... |
| --- | --- |
| Configuração |`/etc`. Por exemplo, `/etc/hadoop/conf/core-site.xml` |
| Ficheiros de registo |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Existem dois diretórios localizado aqui, que é a versão atual do HDP e `current`. O `current` diretório contém links simbólicos a arquivos e diretórios localizados no diretório de número de versão. O `current` diretório é fornecido como uma maneira conveniente de aceder a ficheiros HDP desde as alterações de número de versão como o HDP versão é atualizada. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Em geral, se souber o nome do ficheiro, pode utilizar o seguinte comando numa sessão SSH para localizar o caminho do ficheiro:

    find / -name FILENAME 2>/dev/null

Também pode utilizar carateres universais com o nome de ficheiro. Por exemplo, `find / -name *streaming*.jar 2>/dev/null` retorna o caminho para todos os ficheiros jar que contém a palavra "transmissão em fluxo" como parte do nome do ficheiro.

## <a name="apache-hive-apache-pig-and-mapreduce"></a>Apache Hive, Apache Pig e MapReduce

Cargas de trabalho PIg e MapReduce são semelhantes em clusters baseados em Linux. No entanto, os clusters do HDInsight baseado em Linux podem ser criados com versões mais recentes do Hadoop, Hive e Pig. Essas diferenças de versão podem introduzir alterações numa como a função de soluções existentes. Para obter mais informações sobre as versões dos componentes incluídos com o HDInsight, consulte [controlo de versões de componente de HDInsight](hdinsight-component-versioning.md).

HDInsight baseado em Linux não fornece a funcionalidade de ambiente de trabalho remota. Em vez disso, pode utilizar o SSH para ligar remotamente a nós principais do cluster. Para obter mais informações, consulte os seguintes documentos:

* [Utilizar o Hive do Apache com SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Utilizar o Apache Pig com SSH](hadoop/apache-hadoop-use-pig-ssh.md)
* [Utilizar o MapReduce com SSH](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]  
> Se usar um metastore Hive externo, deve copiar em segurança o metastore antes de o utilizar com o HDInsight baseado em Linux. HDInsight baseado em Linux está disponível com versões mais recentes do ramo de registo, o que pode ter incompatibilidades com metastores criados por versões anteriores.

A tabela a seguir fornece orientação sobre como migrar as cargas de trabalho do Hive.

| No baseado em Windows, utilizo... | No baseado em Linux... |
| --- | --- |
| **Editor do Hive** |[Apache Hive vista no Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` Para ativar o Tez |Apache Tez é o motor de execução padrão para os clusters baseados em Linux, pelo que a instrução set já não é necessária. |
| Funções c# definidas pelo utilizador | Para obter informações sobre a validar a componentes c# com o HDInsight baseado em Linux, consulte [soluções de .NET de migrar para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Ficheiros CMD ou scripts no servidor invocados como parte de uma tarefa do Hive |utilizar scripts de Bash |
| `hive` comando a partir da área de trabalho remota |Uso [Beeline de ramo de registo do Apache](hadoop/apache-hadoop-use-hive-beeline.md) ou [Apache Hive a partir de uma sessão SSH](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| No baseado em Windows, utilizo... | No baseado em Linux... |
| --- | --- |
| Funções c# definidas pelo utilizador | Para obter informações sobre a validar a componentes c# com o HDInsight baseado em Linux, consulte [soluções de .NET de migrar para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Ficheiros CMD ou scripts no servidor invocados como parte de uma tarefa Pig |utilizar scripts de Bash |

### <a name="mapreduce"></a>MapReduce

| No baseado em Windows, utilizo... | No baseado em Linux... |
| --- | --- |
| Mapeador de pontos e reducer componentes c# | Para obter informações sobre a validar a componentes c# com o HDInsight baseado em Linux, consulte [soluções de .NET de migrar para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Ficheiros CMD ou scripts no servidor invocados como parte de uma tarefa do Hive |utilizar scripts de Bash |

## <a name="apache-oozie"></a>Apache Oozie

> [!IMPORTANT]  
> Se usar um metastore Oozie externo, deve copiar em segurança o metastore antes de o utilizar com o HDInsight baseado em Linux. HDInsight baseado em Linux está disponível com versões mais recentes do Oozie, que pode ter incompatibilidades com metastores criados por versões anteriores.

Fluxos de trabalho do Oozie permitem ações de shell. Ações de shell utilizam a shell predefinida para o sistema operativo para executar comandos da linha de comandos. Se tiver Oozie fluxos de trabalho que se baseiam na shell do Windows, terá de reescrever os fluxos de trabalho contar com o ambiente do shell do Linux (Bash). Para obter mais informações sobre como utilizar as ações de shell com o Oozie, consulte [extensão de ação do shell de Oozie](https://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html).

Se tiver um fluxo de trabalho que utiliza uma aplicação c#, valide esses aplicativos num ambiente do Linux. Para obter mais informações, consulte [soluções de .NET de migrar para o HDInsight baseado em Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| No baseado em Windows, utilizo... | No baseado em Linux... |
| --- | --- |
| Dashboard do Storm |O Dashboard do Storm não está disponível. Ver [topologias de implementar e gerir o Apache Storm no HDInsight baseado em Linux](storm/apache-storm-deploy-monitor-topology-linux.md) formas de submeter as topologias |
| IU do Storm |A IU do Storm está disponível em https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio para criar, implementar e gerir topologias de c# ou híbrida |Visual Studio pode ser utilizado para criar, implementar e gerir c# (SCP.NET) ou topologias híbridas no Storm baseado em Linux no HDInsight. Só pode ser utilizada com clusters criados após 10/28/2016. |

## <a name="apache-hbase"></a>Apache HBase

Em clusters baseados em Linux, o pai de znode para o HBase é `/hbase-unsecure`. Defina este valor na configuração para qualquer cliente de Java de aplicações que utilizam a API nativa de Java de HBase.

Ver [criar uma aplicação Apache HBase baseado em Java](hdinsight-hbase-build-java-maven.md) para um cliente de exemplo que define esse valor.

## <a name="spark"></a>Spark

Os clusters do Spark estavam disponíveis em clusters do Windows durante a pré-visualização. Disponibilidade geral do Spark só está disponível com os clusters baseados em Linux. Não existe nenhum caminho de migração de um cluster do Spark baseado em Windows pré-visualização a um cluster do Spark baseado em Linux versão.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="azure-data-factory-custom-net-activities"></a>Atividades do Azure Data Factory personalizadas .NET

Atividades do Azure Data Factory personalizadas .NET não são atualmente suportadas nos clusters do HDInsight baseado em Linux. Em vez disso, deve usar um dos seguintes métodos para implementar atividades personalizadas como parte do seu pipeline do ADF.

* Execute atividades .NET no conjunto do Azure Batch. Ver a utilização do Azure Batch ligado a secção de serviço do [utilizar atividades personalizadas num pipeline do Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implemente a atividade como uma atividade de MapReduce. Para obter mais informações, consulte [invocar programas de MapReduce do Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Produzir de linha

Em geral, fins de linha em sistemas baseados em Windows utilizam CRLF, enquanto os sistemas baseados em Linux utilizam LF. Terá de modificar os produtores de dados existente e os consumidores para trabalhar com LF.

Por exemplo, o HDInsight num cluster baseado em Windows com o Azure PowerShell a consulta retorna dados com CRLF. A mesma consulta com um cluster baseado em Linux devolve LF. Teste para ver se o fim da linha faz com que um problema com a sua solução antes de migrar para um cluster baseado em Linux.

Utilize sempre LF como a linha final para scripts executados em nós do cluster. Se utilizar CRLF, poderá ver erros ao executar os scripts num cluster baseado em Linux.

Se os scripts não contêm cadeias de caracteres com caracteres de CR embedded, pode em massa alteração as fins de linha utilizando um dos seguintes métodos:

* **Antes de carregar para o cluster**: Utilize as seguintes declarações de PowerShell para alterar as fins de linha de CRLF para LF antes de carregar o script para o cluster.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **Depois de carregar para o cluster**: Utilize o seguinte comando numa sessão SSH para o cluster baseado em Linux para modificar o script.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Próximos Passos

* [Saiba como criar clusters do HDInsight baseado em Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Utilizar o SSH para ligar ao HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gerir um cluster baseado em Linux com o Apache Ambari](hdinsight-hadoop-manage-ambari.md)
