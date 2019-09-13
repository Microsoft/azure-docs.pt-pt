---
title: Executar trabalhos do Apache Sqoop com o Azure HDInsight (Apache Hadoop)
description: Saiba como usar Azure PowerShell de uma estação de trabalho para executar a importação e exportação do Sqoop entre um cluster Hadoop e um banco de dados SQL do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 564bced9ae71213cb534393a7dcc45c929df3794
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917369"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Usar o Apache Sqoop com Hadoop no HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o Apache Sqoop no HDInsight para importar e exportar dados entre um cluster HDInsight e um banco de dado SQL do Azure.

Embora Apache Hadoop seja uma opção natural para o processamento de dados não estruturados e semiestruturados, como logs e arquivos, também pode haver a necessidade de processar dados estruturados armazenados em bancos de dados relacionais.

O [Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) é uma ferramenta projetada para transferir dados entre clusters Hadoop e bancos de dados relacionais. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL Server, MySQL ou Oracle no HDFS (sistema de arquivos distribuído do Hadoop), transformar os dados no Hadoop com MapReduce ou Apache Hive e, em seguida, exportar os dados de volta para um RDBMS . Neste artigo, você está usando um banco de dados SQL Server para seu banco de dados relacional.

> [!IMPORTANT]  
> Este artigo configura um ambiente de teste para executar a transferência de dados. Em seguida, escolha um método de transferência de dados para esse ambiente de um dos métodos na seção [executar trabalhos do Sqoop](#run-sqoop-jobs), mais adiante.

Para versões do Sqoop com suporte em clusters HDInsight, consulte [novidades nas versões do cluster fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Compreender o cenário

O cluster HDInsight vem com alguns dados de exemplo. Você usa os dois exemplos a seguir:

* Um arquivo de log do Apache Log4J, que está `/example/data/sample.log`localizado em. Os seguintes logs são extraídos do arquivo:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Uma tabela do hive `hivesampletable`denominada, que faz referência ao arquivo `/hive/warehouse/hivesampletable`de dados localizado em. A tabela contém alguns dados de dispositivo móvel.
  
  | Campo | Tipo de dados |
  | --- | --- |
  | ClientID |Cadeia de caracteres |
  | querytime |Cadeia de caracteres |
  | Anunciar |Cadeia de caracteres |
  | deviceplatform |Cadeia de caracteres |
  | devicemake |Cadeia de caracteres |
  | devicemodel |Cadeia de caracteres |
  | state |Cadeia de caracteres |
  | país |Cadeia de caracteres |
  | querydwelltime |double |
  | SessionID |bigint |
  | sessionpagevieworder |bigint |

Neste artigo, você usará esses dois conjuntos de valores para testar a importação e exportação do Sqoop.

## <a name="create-cluster-and-sql-database"></a>Configurar ambiente de teste
O cluster, o banco de dados SQL e outros objetos são criados por meio do portal do Azure usando um modelo de Azure Resource Manager. O modelo pode ser encontrado nos [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). O modelo do Resource Manager chama um pacote bacpac para implantar os esquemas de tabela em um banco de dados SQL.  O pacote bacpac está localizado em um contêiner de blob público https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac,. Se você quiser usar um contêiner privado para os arquivos bacpac, use os seguintes valores no modelo:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importar usando um modelo ou o portal do Azure só dá suporte à importação de um arquivo BACPAC do armazenamento de BLOBs do Azure.

1. Selecione a imagem a seguir para abrir o modelo do Resource Manager na portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>

2. Introduza as seguintes propriedades:

    |Campo |Value |
    |---|---|
    |Subscription |Selecione sua assinatura do Azure na lista suspensa.|
    |Resource group |Selecione o grupo de recursos na lista suspensa ou crie um novo|
    |Location |Selecione uma região na lista suspensa.|
    |Nome do Cluster |Introduza um nome para o cluster do Hadoop. Use somente letra minúscula.|
    |Nome de Utilizador de Início de Sessão do Cluster |Mantenha o valor `admin`preenchido previamente.|
    |Palavra-passe de Início de Sessão do Cluster |Insira uma senha.|
    |Nome de usuário SSH |Mantenha o valor `sshuser`preenchido previamente.|
    |Senha ssh |Insira uma senha.|
    |Logon de administrador do SQL |Mantenha o valor `sqluser`preenchido previamente.|
    |Senha de administrador do SQL |Insira uma senha.|
    |Local _artifacts | Use o valor padrão, a menos que você queira usar seu próprio arquivo bacpac em um local diferente.|
    |Token SAS do local _artifacts |Deixe em branco.|
    |Nome do arquivo Bacpac |Use o valor padrão, a menos que você queira usar seu próprio arquivo bacpac.|
    |Location |Utilize o valor predefinido.|

    O nome do SQL Server do Azure `<ClusterName>dbserver`será. O nome do banco de `<ClusterName>db`dados será. O nome da conta de armazenamento padrão `e6qhezrh2pdqu`será.

3. Selecione **concordo com os termos e condições declarados acima**.

4. Selecione **Comprar**. Você verá um novo bloco intitulado enviando implantação para Implantação de modelo. A criação do cluster e da SQL Database demora cerca de 20 minutos.

## <a name="run-sqoop-jobs"></a>Executar trabalhos do Sqoop

O HDInsight pode executar trabalhos do Sqoop usando uma variedade de métodos. Use a tabela a seguir para decidir qual método é ideal para você e, em seguida, siga o link para obter uma explicação.

| **Use esta** se desejar... | ... um shell **interativo** | ... processamento **em lotes** | ... deste **sistema operacional cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, UNIX, Mac OS X ou Windows |
| [.NET SDK para Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (por enquanto) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Limitações

* Exportação em massa-com o HDInsight baseado em Linux, o conector do Sqoop usado para exportar dados para o Microsoft SQL Server ou o Azure SQL Database não oferece suporte a inserções em massa no momento.
* Envio em lote com o HDInsight baseado em Linux, ao usar `-batch` a opção ao executar inserções, o Sqoop executa várias inserções em vez de realizar o envio em lote das operações de inserção.

## <a name="next-steps"></a>Passos seguintes
Agora você aprendeu a usar o Sqoop. Para saber mais, consulte:

* [Usar o Apache Hive com o HDInsight](../hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](../hdinsight-use-pig.md)
* [Carregar dados no HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para carregar dados no HDInsight/armazenamento de BLOBs do Azure.
