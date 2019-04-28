---
title: Executar tarefas de Apache Sqoop com o Azure HDInsight (Apache Hadoop)
description: Saiba como utilizar o Azure PowerShell a partir de uma estação de trabalho para executar o Sqoop import e export entre um cluster do Hadoop e uma base de dados SQL do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 6764d8d812789c9f54fa59e10b2a3e416e583a9c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129404"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Utilizar o Apache Sqoop com o Hadoop no HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Apache Sqoop para importar e exportar dados entre um cluster do HDInsight e uma base de dados SQL do Azure no HDInsight.

Embora o Apache Hadoop é uma opção natural para processar os dados não estruturados e semiestruturados, tais como registos e ficheiros, também pode haver a necessidade de processar dados estruturados que são armazenados em bases de dados relacionais.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) é uma ferramenta projetada para transferir dados entre clusters do Hadoop e bases de dados relacionais. Pode usá-lo para importar dados de um sistema de gerenciamento de banco de dados relacional (RDBMS), como SQL Server, MySQL ou Oracle para o sistema de ficheiros distribuído Hadoop (HDFS), transformar os dados no Hadoop com o MapReduce ou o Apache Hive e, em seguida, exportar os dados de volta para um RDBMS . Neste artigo, está a utilizar uma base de dados do SQL Server da base de dados relacional.

> [!IMPORTANT]  
> Este artigo define um ambiente de teste para efetuar a transferência de dados. Em seguida, escolha um método de transferência de dados para este ambiente a partir de um dos métodos na secção [tarefas de executar o Sqoop](#run-sqoop-jobs), mais abaixo.

Para versões de Sqoop que são suportadas nos clusters do HDInsight, consulte [quais são as novidades nas versões do cluster fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Compreender o cenário

Cluster de HDInsight vem com alguns dados de exemplo. Utilize os seguintes dois exemplos:

* Um ficheiro de registo Apache Log4j, que está localizado em `/example/data/sample.log`. Os seguintes registos são extraídos a partir do ficheiro:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Uma tabela do Hive com o nome `hivesampletable`, que faz referência ao arquivo de dados localizado em `/hive/warehouse/hivesampletable`. A tabela contém alguns dados de dispositivos móveis.
  
  | Campo | Tipo de dados |
  | --- | --- |
  | ID de cliente |string |
  | querytime |string |
  | mercado |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | País |string |
  | querydwelltime |double |
  | SessionID |bigint |
  | sessionpagevieworder |bigint |

Neste artigo, vai utilizar esses dois conjuntos de dados para testar o Sqoop import e exportar.

## <a name="create-cluster-and-sql-database"></a>Configurar o ambiente de teste
O cluster, base de dados SQL e outros objetos são criados através do portal do Azure com um modelo Azure Resource Manager. O modelo pode ser encontrado na [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). O modelo do Resource Manager chama um pacote de bacpac para implementar os esquemas de tabela para uma base de dados SQL.  O pacote de bacpac está localizado no contentor de blob público, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Se pretender utilizar um contentor privado para os ficheiros bacpac, utilize os seguintes valores no modelo:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importar com um modelo ou do portal do Azure só suporta a importação de um ficheiro BACPAC do armazenamento de Blobs do Azure.

1. Selecione a imagem seguinte para abrir o modelo do Resource Manager no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Introduza as seguintes propriedades:

    |Campo |Value |
    |---|---|
    |Subscrição |Selecione a sua subscrição do Azure na lista pendente.|
    |Grupo de recursos |Selecione o grupo de recursos na lista pendente ou criar um novo|
    |Location |Selecione uma região na lista pendente.|
    |Nome do Cluster |Introduza um nome para o cluster do Hadoop. Utilize apenas a letra minúscula.|
    |Nome de Utilizador de Início de Sessão do Cluster |Mantenha o valor pré-preenchidos `admin`.|
    |Palavra-passe de Início de Sessão do Cluster |Introduza uma palavra-passe.|
    |Nome de utilizador SSH |Mantenha o valor pré-preenchidos `sshuser`.|
    |SSH palavra-passe |Introduza uma palavra-passe.|
    |Início de sessão de administrador de SQL |Mantenha o valor pré-preenchidos `sqluser`.|
    |Palavra-passe de administrador do SQL |Introduza uma palavra-passe.|
    |localização de _artifacts | Utilize o valor predefinido, a menos que desejar usar seu próprio ficheiro bacpac numa localização diferente.|
    |Token de Sas de localização de _artifacts |Deixe em branco.|
    |Nome de ficheiro Bacpac |Utilize o valor predefinido, a menos que queira utilizar o seu próprio ficheiro bacpac.|
    |Location |Utilize o valor predefinido.|

    O nome do servidor SQL do Azure será `<ClusterName>dbserver`. O nome de base de dados será `<ClusterName>db`. O nome de conta do storage predefinida será `e6qhezrh2pdqu`.

3. Selecione **concordo com os termos e condições indicados acima**.

4. Selecione **Comprar**. Verá um novo mosaico intitulado submeter a implementação para a implementação do modelo. A criação do cluster e da SQL Database demora cerca de 20 minutos.

## <a name="run-sqoop-jobs"></a>Executar tarefas de Sqoop

HDInsight pode executar o Sqoop tarefas utilizando uma variedade de métodos. Utilize a tabela seguinte para decidir qual é o método adequado para si, em seguida, siga a ligação para obter instruções.

| **Utilize esta opção** se pretender que... | ... .an **interativo** shell | ... **batch** processamento | ... .from isso **sistema operativo do cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X ou Windows |
| [.NET SDK para Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (por agora) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Limitações

* Exportação em massa - baseado em Linux com o HDInsight, o conector do Sqoop utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta atualmente inserções em massa.
* Criação de batches - com o HDInsight baseado em Linux, ao utilizar o `-batch` mude ao realizar inserções, Sqoop realiza várias inserções em vez das operações de inserção de criação de batches.

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu a utilizar o Sqoop. Para saber mais, consulte:

* [Utilizar o Apache Hive com o HDInsight](../hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](../hdinsight-use-pig.md)
* [Carregar dados para o HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para carregar dados para o armazenamento de Blobs do HDInsight/do Azure.
