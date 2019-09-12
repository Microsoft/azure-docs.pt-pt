---
title: Conectar o Excel ao Apache Hadoop com o driver ODBC do hive – Azure HDInsight
description: Saiba como configurar e usar o driver ODBC do Microsoft Hive para Excel para consultar dados em clusters HDInsight do Microsoft Excel.
keywords: Hadoop Excel, Hive Excel, Hive ODBC
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: b1231a406ac20512e9ce92032379d697c3346af3
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882803"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Conectar o Excel ao Apache Hadoop no Azure HDInsight com o driver ODBC do Microsoft Hive

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A solução de Big data da Microsoft integra componentes de BI (Business Intelligence) da Microsoft com clusters Apache Hadoop que foram implantados no Azure HDInsight. Um exemplo dessa integração é a capacidade de conectar o Excel ao hive data warehouse de um cluster Hadoop no HDInsight usando o driver ODBC (Open Database Connectivity) do Microsoft Hive.

Também é possível conectar os dados associados a um cluster HDInsight e outras fontes de dados, incluindo outros clusters Hadoop (não HDInsight), do Excel usando o suplemento Microsoft Power Query para Excel. Para obter informações sobre como instalar e usar Power Query, consulte [conectar o Excel ao HDInsight com o Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter os seguintes itens:

* HDInsight An cluster Hadoop. Para criar um, consulte Introdução [ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho com o Office 2010 Professional Plus ou posterior ou Excel 2010 ou posterior.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalar o driver ODBC do Microsoft Hive
Baixe e instale a versão [driver ODBC do Microsoft Hive](https://go.microsoft.com/fwlink/?LinkID=286698) que corresponde à versão do aplicativo em que você usará o driver ODBC.  Para este artigo, o driver é usado para o Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Criar Apache Hive fonte de dados ODBC
As etapas a seguir mostram como criar uma fonte de dados ODBC do hive.

1. No Windows, navegue até Iniciar > Ferramentas administrativas do Windows > fontes de dados ODBC (32 bits)/(64 bits).  Isso abre a janela **administrador de fonte de dados ODBC** .

    ![Administrador de fonte de dados ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Configurar um DSN usando o administrador de fonte de dados ODBC")

1. Na guia **DSN de usuário** , selecione **Adicionar** para abrir a janela **criar nova fonte de dados** .

1. Selecione **driver ODBC do Microsoft Hive**e, em seguida, selecione **concluir** para abrir a janela **instalação do driver ODBC do Microsoft Hive DSN** .

1. Escreva ou selecione os seguintes valores:

   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da Origem de Dados |Atribua um nome para a sua origem de dados |
   |  Host (s) |Introduza `HDInsightClusterName.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net` |
   |  Port |Utilize **443**. (Esta porta foi alterada de 563 para 443.) |
   |  Base de Dados |Usar **padrão**. |
   |  Mecanismo |Selecione o **serviço HDInsight do Windows Azure** |
   |  Nome de utilizador |Insira o nome de usuário HTTP do cluster HDInsight. O nome de utilizador predefinido é **admin**. |
   |  Palavra-passe |Insira a senha de usuário do cluster HDInsight. Marque a caixa de seleção **salvar senha (criptografada)** .|

1. Opcional: Selecione **Opções avançadas...**  

   | Parâmetro | Descrição |
   | --- | --- |
   |  Usar consulta nativa |Quando selecionado, o driver ODBC não tenta converter TSQL em HiveQL. Você deve usá-lo somente se você for 100% certeza de que está enviando instruções HiveQL puras. Ao conectar-se ao SQL Server ou ao banco de dados SQL do Azure, deixe-o desmarcado. |
   |  Linhas buscadas por bloco |Ao buscar um grande número de registros, o ajuste desse parâmetro pode ser necessário para garantir o desempenho ideal. |
   |  Comprimento da coluna de cadeia de caracteres padrão, comprimento de coluna binária, escala de coluna decimal |Os comprimentos e as precisão do tipo de dados podem afetar a forma como os dados são retornados. Eles fazem com que informações incorretas sejam retornadas devido à perda de precisão e/ou truncamento. |

    ![Opções avançadas](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Opções de configuração de DSN avançadas")

1. Selecione **testar** para testar a fonte de dados. Quando a fonte de dados estiver configurada corretamente, o resultado do teste mostrará **êxito!** .  

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a janela **instalação do driver ODBC do Microsoft Hive DSN** .  

1. Selecione **OK** para fechar a janela **administrador de fonte de dados ODBC** .  

## <a name="import-data-into-excel-from-hdinsight"></a>Importe dados para o Excel a partir do HDInsight

As etapas a seguir descrevem a maneira de importar dados de uma tabela do hive para uma pasta de trabalho do Excel usando a fonte de dados ODBC que você criou na seção anterior.

1. Abra um livro novo ou existente no Excel.

2. Na guia **dados** , navegue até **obter dados** > **de outras fontes** > **do ODBC** para iniciar a janela **do ODBC** .

    ![Abrir assistente de conexão de dados](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Abrir assistente de conexão de dados")

3. Na lista suspensa, selecione o nome da fonte de dados que você criou na última seção e, em seguida, selecione **OK**.

4. Para o primeiro uso, uma caixa de diálogo do **driver ODBC** será aberta. Selecione **Windows** no menu à esquerda. Em seguida, selecione **conectar** para abrir a janela **navegador** .

5. No **navegador**, navegue até **Hive** > **Default** > **hivesampletable**e, em seguida, selecione **Load**. Leva alguns minutos para que os dados sejam importados para o Excel.

    ![Navegador ODBC do hive do HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "Abrir assistente de conexão de dados")

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, você aprendeu a usar o driver ODBC do Microsoft Hive para recuperar dados do serviço HDInsight para o Excel. Da mesma forma, você pode recuperar dados do serviço HDInsight no banco de dados SQL. Também é possível carregar dados em um serviço do HDInsight. Para saber mais, consulte:

* [Visualize Apache Hive dados com o Microsoft Power bi no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize dados de hive de consulta interativa com Power bi no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use o Apache Zeppelin para executar Apache Hive consultas no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conecte o Excel ao Apache Hadoop usando Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute Apache Hive consultas usando o data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Use a ferramenta Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados no HDInsight](./../hdinsight-upload-data.md).