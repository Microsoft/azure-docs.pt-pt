---
title: Executar empregos Apache Sqoop com Azure HDInsight (Apache Hadoop)
description: Saiba como usar a Azure PowerShell a partir de uma estação de trabalho para executar a importação e exportação da Sqoop entre um cluster Hadoop e uma base de dados Azure SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: 165b7d00c3cf307e7996e84a35bb2a202f448cc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86076883"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Utilizar o Apache Sqoop com o Hadoop no HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o Apache Sqoop em HDInsight para importar e exportar dados entre um cluster HDInsight e Azure SQL Database.

Embora o Apache Hadoop seja uma escolha natural para o processamento de dados não estruturados e semi-estruturados, como registos e ficheiros, pode também haver necessidade de processar dados estruturados que são armazenados em bases de dados relacionais.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) é uma ferramenta projetada para transferir dados entre clusters Hadoop e bases de dados relacionais. Pode usá-lo para importar dados de um sistema de gestão de bases de dados relacional (RDBMS) como SQL Server, MySQL ou Oracle no sistema de ficheiros distribuídos hadoop (HDFS), transformar os dados em Hadoop com MapReduce ou Apache Hive, e depois exportar os dados de volta para um RDBMS. Neste artigo, está a utilizar a Base de Dados Azure SQL para a sua base de dados relacional.

> [!IMPORTANT]  
> Este artigo cria um ambiente de teste para realizar a transferência de dados. Em seguida, escolha um método de transferência de dados para este ambiente a partir de um dos métodos na secção [Run Sqoop jobs](#run-sqoop-jobs), mais abaixo.

Para versões Sqoop que são suportadas em clusters HDInsight, veja [quais as novidades nas versões de cluster fornecidas pela HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Compreender o cenário

O cluster HDInsight vem com alguns dados da amostra. Utiliza as seguintes duas amostras:

* Um ficheiro de log4j Apache Log4j, que está localizado em `/example/data/sample.log` . Os seguintes registos são extraídos do ficheiro:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Uma tabela de Colmeia chamada `hivesampletable` , que faz referência ao ficheiro de dados localizado em `/hive/warehouse/hivesampletable` . A tabela contém alguns dados do dispositivo móvel.
  
  | Campo | Tipo de dados |
  | --- | --- |
  | clientid |string |
  | consulta |string |
  | mercado |string |
  | placa de dispositivo |string |
  | fabricante de dispositivos |string |
  | modelo de dispositivo |string |
  | state |string |
  | país |string |
  | consultadwelltime |double |
  | sessionid |bigint |
  | ordem de visão de sessão |bigint |

Neste artigo, você usa estes dois conjuntos de dados para testar a importação e exportação da Sqoop.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Configurar ambiente de teste

O cluster, base de dados SQL e outros objetos são criados através do portal Azure utilizando um modelo de Gestor de Recursos Azure. O modelo pode ser encontrado em [modelos de arranque rápido Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). O modelo de Gestor de Recursos chama um pacote bacpac para implantar os esquemas de mesa para uma base de dados SQL.  O pacote bacpac está localizado num recipiente de bolhas públicas, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac . Se pretender utilizar um recipiente privado para os ficheiros bacpac, utilize os seguintes valores no modelo:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importar usando um modelo ou o portal Azure apenas suporta a importação de um ficheiro BACPAC a partir do armazenamento de blob Azure.

1. Selecione a seguinte imagem para abrir o modelo de Gestor de Recursos no portal Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Introduza as seguintes propriedades:

    |Campo |Valor |
    |---|---|
    |Subscrição |Selecione a sua subscrição Azure da lista de drop-down.|
    |Grupo de recursos |Selecione o seu grupo de recursos a partir da lista de drop-down, ou crie um novo|
    |Localização |Selecione uma região da lista de drop-down.|
    |Nome do Cluster |Introduza um nome para o cluster do Hadoop. Use apenas letras minúsculas.|
    |Nome de Utilizador de Início de Sessão do Cluster |Mantenha o valor pré-povoado. `admin`|
    |Palavra-passe de Início de Sessão do Cluster |Introduza uma senha.|
    |Nome do utilizador Ssh |Mantenha o valor pré-povoado. `sshuser`|
    |Ssh Password |Introduza uma senha.|
    |Sql Admin Login |Mantenha o valor pré-povoado. `sqluser`|
    |Senha de administrador sql |Introduza uma senha.|
    |Localização _artifacts | Utilize o valor predefinido a menos que pretenda utilizar o seu próprio ficheiro bacpac num local diferente.|
    |_artifacts Localização Sas Token |Deixe em branco.|
    |Nome do arquivo Bacpac |Utilize o valor predefinido a menos que pretenda utilizar o seu próprio ficheiro bacpac.|
    |Localização |Utilize o valor predefinido.|

    O nome [lógico do servidor SQL](../../azure-sql/database/logical-servers.md) será  `<ClusterName>dbserver` . O nome da base de dados `<ClusterName>db` será. O nome da conta de armazenamento predefinido será `e6qhezrh2pdqu` .

3. **Selecione Concordo com os termos e condições acima indicados.**

4. Selecione **Comprar**. Você vê um novo azulejo intitulado Submissão para implementação de modelo. A criação do cluster e da SQL Database demora cerca de 20 minutos.

## <a name="run-sqoop-jobs"></a>Executar empregos sqoop

HDInsight pode executar trabalhos sqoop usando uma variedade de métodos. Use a seguinte tabela para decidir qual o método certo para si e, em seguida, siga o link para uma passagem.

| **Use isto** se quiser... | ... uma concha **interativa** | ... processamento **de lotes** | ... deste **sistema operativo cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X ou Windows |
| [.NET SDK para Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Janelas (por enquanto) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Limitações

* Exportação a granel - Com o HDInsight baseado em Linux, o conector Sqoop utilizado para exportar dados para o Microsoft SQL Server ou SQL Database não suporta atualmente inserções a granel.
* Loteamento - Com o HDInsight baseado em Linux, quando utiliza o `-batch` interruptor ao efetuar inserções, a Sqoop executa múltiplas inserções em vez de lotar as operações de inserção.

## <a name="next-steps"></a>Passos seguintes

Agora aprendeste a usar o Sqoop. Para saber mais, veja:

* [Use a Colmeia Apache com HDInsight](../hdinsight-use-hive.md)
* [Faça upload de dados para HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para enviar dados para o armazenamento HDInsight/Azure Blob.
* [Utilizar o Apache Sqoop para importar e exportar dados entre o Apache Hadoop no HDInsight e a Base de Dados SQL](./apache-hadoop-use-sqoop-mac-linux.md)
