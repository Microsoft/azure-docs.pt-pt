---
title: Executar trabalhos Apache Sqoop com Azure HDInsight (Apache Hadoop)
description: Aprenda a usar o Azure PowerShell a partir de uma estação de trabalho para executar a importação e exportação de Sqoop entre um cluster Hadoop e uma base de dados Azure SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 091ce1cc0b2540a02e62e1e85c5515f6aa62b93c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84018842"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Utilizar o Apache Sqoop com o Hadoop no HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Apache Sqoop no HDInsight para importar e exportar dados entre um cluster HDInsight e a Base de Dados Azure SQL.

Embora o Apache Hadoop seja uma escolha natural para o processamento de dados não estruturados e semi-estruturados, como registos e ficheiros, também pode haver necessidade de processar dados estruturados que são armazenados em bases de dados relacionais.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) é uma ferramenta projetada para transferir dados entre clusters Hadoop e bases de dados relacionais. Pode usá-lo para importar dados de um sistema de gestão relacional de bases de dados (RDBMS) como O Servidor SQL, MySQL ou Oracle para o sistema de ficheiros distribuídos hadoop (HDFS), transformar os dados em Hadoop com MapReduce ou Hive, e depois exportar os dados de volta para um RDBMS. Neste artigo, está a utilizar a Base de Dados Azure SQL para a sua base de dados relacional.

> [!IMPORTANT]  
> Este artigo cria um ambiente de teste para realizar a transferência de dados. Em seguida, escolha um método de transferência de dados para este ambiente a partir de um dos métodos da secção [Run Sqoop jobs](#run-sqoop-jobs), mais abaixo.

Para versões Sqoop que são suportadas em clusters HDInsight, veja [quais as novidades nas versões de cluster fornecidas pela HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Compreender o cenário

O cluster HDInsight vem com alguns dados da amostra. Utiliza as seguintes duas amostras:

* Um ficheiro de registo Apache Log4j, que está localizado a `/example/data/sample.log` . Os seguintes registos são extraídos do ficheiro:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Uma tabela Da Colmeia denominada `hivesampletable` , que faz referência ao ficheiro de dados localizado em `/hive/warehouse/hivesampletable` . A tabela contém alguns dados do dispositivo móvel.
  
  | Campo | Tipo de dados |
  | --- | --- |
  | clientid |string |
  | hora da consulta |string |
  | mercado |string |
  | plataforma de dispositivos |string |
  | dispositivofazer |string |
  | modelo de dispositivo |string |
  | state |string |
  | país |string |
  | consultadwelltime |double |
  | sessionid |bigint |
  | sessãopagevieworder |bigint |

Neste artigo, utiliza estes dois conjuntos de dados para testar a importação e exportação de Sqoop.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Configurar o ambiente de teste

O cluster, a base de dados SQL e outros objetos são criados através do portal Azure usando um modelo de Gestor de Recursos Azure. O modelo pode ser encontrado em [modelos de arranque rápido de Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). O modelo do Gestor de Recursos chama um pacote bacpac para implantar os schemas de mesa para uma base de dados SQL.  O pacote bacpac está localizado num recipiente de bolhas https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac públicos, . Se pretender utilizar um recipiente privado para os ficheiros bacpac, utilize os seguintes valores no modelo:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importar utilizando um modelo ou o portal Azure apenas suporta a importação de um ficheiro BACPAC do armazenamento de blob Azure.

1. Selecione a seguinte imagem para abrir o modelo de Gestor de Recursos no portal Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Introduza as seguintes propriedades:

    |Campo |Valor |
    |---|---|
    |Subscrição |Selecione a sua subscrição Azure na lista de drop-down.|
    |Grupo de recursos |Selecione o seu grupo de recursos a partir da lista de drop-down, ou crie um novo|
    |Localização |Selecione uma região da lista de abandono.|
    |Nome do Cluster |Introduza um nome para o cluster do Hadoop. Use apenas letras minúsculas.|
    |Nome de Utilizador de Início de Sessão do Cluster |Mantenha o valor pré-povoado. `admin`|
    |Palavra-passe de Início de Sessão do Cluster |Introduza uma senha.|
    |Nome do utilizador SSH |Mantenha o valor pré-povoado. `sshuser`|
    |Palavra-passe ssh |Introduza uma senha.|
    |Sql Admin Login |Mantenha o valor pré-povoado. `sqluser`|
    |Senha de Administrador Sql |Introduza uma senha.|
    |localização _artifacts | Utilize o valor predefinido a menos que pretenda utilizar o seu próprio ficheiro bacpac num local diferente.|
    |localização _artifacts Sas Token |Deixe em branco.|
    |Nome do ficheiro Bacpac |Utilize o valor predefinido a menos que pretenda utilizar o seu próprio ficheiro bacpac.|
    |Localização |Utilize o valor predefinido.|

    O nome lógico do [servidor SQL](../../azure-sql/database/logical-servers.md) será `<ClusterName>dbserver` . O nome da base de dados `<ClusterName>db` será. O nome da conta de armazenamento por defeito será `e6qhezrh2pdqu` .

3. Selecione **Concordo com os termos e condições acima indicados**.

4. Selecione **Comprar**. Você vê um novo azulejo intitulado Submissão para implementação de modelo. A criação do cluster e da SQL Database demora cerca de 20 minutos.

## <a name="run-sqoop-jobs"></a>Executar empregos em Sqoop

O HDInsight pode executar trabalhos de Sqoop utilizando uma variedade de métodos. Use a tabela seguinte para decidir qual o método certo para si e, em seguida, siga o link para uma passagem.

| **Use isto** se quiser... | ... uma concha **interativa** | ... processamento de **lotes** | ... deste **sistema operativo cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X ou Windows |
| [.NET SDK para Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Janelas (por enquanto) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Limitações

* Exportação a granel - Com o HDInsight baseado em Linux, o conector Sqoop utilizado para exportar dados para o Microsoft SQL Server ou SQL Database não suporta atualmente inserções a granel.
* Loteamento - Com hDInsight baseado em Linux, ao utilizar o `-batch` interruptor ao executar inserções, o Sqoop executa várias inserções em vez de encher as operações de inserção.

## <a name="next-steps"></a>Próximos passos

Agora aprendeste a usar o Sqoop. Para saber mais, consulte:

* [Use a Colmeia Apache com HDInsight](../hdinsight-use-hive.md)
* [Upload de dados para HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para o upload de dados para armazenamento HDInsight/Azure Blob.
* [Utilizar o Apache Sqoop para importar e exportar dados entre o Apache Hadoop no HDInsight e a Base de Dados SQL](./apache-hadoop-use-sqoop-mac-linux.md)