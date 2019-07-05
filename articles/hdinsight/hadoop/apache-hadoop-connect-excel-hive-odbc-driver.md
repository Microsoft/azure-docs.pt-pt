---
title: Ligar o Excel para Apache Hadoop com o controlador ODBC do Hive - Azure HDInsight
description: Saiba como configurar e utilizar o controlador Microsoft Hive ODBC para o Excel para consultar dados em clusters do HDInsight a partir do Microsoft Excel.
keywords: hadoop do excel, o hive do excel, do hive odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: 01e7ca184f2be755f23cd402e0694ea282e3b85e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441386"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Ligar o Excel para Apache Hadoop no HDInsight do Azure com o controlador Microsoft Hive ODBC

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Solução de macrodados da Microsoft integra-se os componentes do Microsoft Business Intelligence (BI) com clusters do Apache Hadoop que foram implementados no Azure HDInsight. Um exemplo desta integração é a capacidade de ligar o Excel para o armazém de dados do Hive de um cluster de Hadoop no HDInsight com o controlador Microsoft Hive Open Database Connectivity (ODBC).

Também é possível ligar os dados associados um cluster do HDInsight e outras origens de dados, incluindo outros clusters do Hadoop (não HDInsight), do Excel usando o suplemento do Microsoft Power Query para Excel. Para obter informações sobre como instalar e utilizar o Power Query, consulte [ligar o Excel ao HDInsight com o Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter os seguintes itens:

* Um cluster de Hadoop do HDInsight. Para criar um, veja [introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho com o Office 2010 Professional Plus ou posterior, ou o Excel 2010 ou posterior.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalar o controlador Microsoft Hive ODBC
Transferir e instalar o [Microsoft Hive ODBC Driver](https://go.microsoft.com/fwlink/?LinkID=286698) versão que corresponde à versão do aplicativo em que irá utilizar o controlador ODBC.  Neste artigo, o driver é utilizado para o Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Criar origem de dados de ODBC do Hive do Apache
Os passos seguintes mostram como criar uma origem de dados de ODBC do Hive.

1. A partir do Windows, navegue para o início > Ferramentas administrativas do Windows > origens de dados ODBC (32-bit)/(64-bit).  Esta ação abre o **administrador de fonte de dados de ODBC** janela.

    ![Administrador de fonte de dados OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "configurar um DSN com o administrador de fonte de dados de ODBC")

1. Do **utilizador DSN** separador, selecione **Add** para abrir o **criar nova origem de dados** janela.

1. Selecione **Microsoft Hive ODBC Driver**e, em seguida, selecione **concluir** para abrir o **Microsoft Hive ODBC Driver de DSN Setup** janela.

1. Escreva ou selecione os seguintes valores:

   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da Origem de Dados |Atribua um nome para a sua origem de dados |
   |  Host(s) |Introduza `HDInsightClusterName.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net` |
   |  Port |Utilize **443**. (Esta porta foi alterada de 563 para 443.) |
   |  Base de Dados |Uso **predefinição**. |
   |  Mecanismo |Selecione **serviço do Windows Azure HDInsight** |
   |  Nome de utilizador |Introduza o nome de utilizador do utilizador do HDInsight cluster HTTP. O nome de utilizador predefinido é **admin**. |
   |  Palavra-passe |Introduza a palavra-passe do utilizador de cluster do HDInsight. Selecione a caixa de verificação **guardar palavra-passe (encriptado)** .|

1. Opcional: Selecione **opções avançadas...**  

   | Parâmetro | Descrição |
   | --- | --- |
   |  Utilizar a consulta nativa |Quando estiver selecionada, o controlador ODBC não tentará converter TSQL em HiveQL. Deve usá-lo apenas se for 100%-se de que está a enviar instruções de HiveQL puras. Ao ligar ao SQL Server ou base de dados do Azure SQL, deve deixar desmarcada. |
   |  Linhas obtidas por bloco |Quando a obter um grande número de registos, a otimização este parâmetro pode ser necessário para garantir que os desempenhos ideal. |
   |  Comprimento de coluna de cadeia de caracteres padrão, o comprimento de coluna binária, escala da coluna Decimal |O tipo de dados comprimentos e precisions pode afetar a forma como os dados são retornados. Estes provocarem informações incorretas a serem retornados devido a perda de precisão e/ou o truncamento. |

    ![Opções Avançadas](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "opções de configuração do Advanced DSN")

1. Selecione **testar** para testar a origem de dados. Quando a origem de dados está configurada corretamente, o resultado do teste mostra **SUCESSO!** .  

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a **Microsoft Hive ODBC Driver de DSN Setup** janela.  

1. Selecione **OK** para fechar a **administrador de fonte de dados de ODBC** janela.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importe dados para o Excel a partir do HDInsight

Os passos seguintes descrevem a forma de importar dados de uma tabela do Hive para um livro do Excel com a origem de dados ODBC que criou na secção anterior.

1. Abra um livro novo ou existente no Excel.

2. Do **dados** separador, navegue até **obter dados** > **de outras origens** > **do ODBC** para iniciar o **De ODBC** janela.

    ![Assistente de ligação de dados abertos](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Assistente de ligação de dados abertos")

3. Na lista pendente, selecione o nome da origem de dados que criou na última secção e, em seguida, selecione **OK**.

4. Para a primeira utilização, uma **controlador ODBC** abrirá a caixa de diálogo. Selecione **Windows** no menu à esquerda. Em seguida, selecione **Connect** para abrir o **navegador** janela.

5. Partir **navegador**, navegue até à **HIVE** > **padrão** > **hivesampletable**e, em seguida, selecione  **Carga**. Demora alguns minutos antes de dados serem importados para o Excel.

    ![Navegador de ODBC do Hive do HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Assistente de ligação de dados abertos")

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a utilizar o controlador Microsoft Hive ODBC para recuperar dados do serviço HDInsight para o Excel. Da mesma forma, pode recuperar dados do serviço HDInsight na base de dados SQL. Também é possível carregar dados para um serviço do HDInsight. Para saber mais, consulte:

* [Visualize os dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados de consulta interativa do Hive com o Power BI no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Utilizar Apache Zeppelin para executar consultas do Apache Hive no Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Ligar o Excel para Apache Hadoop com o Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ligar ao Azure HDInsight e executar consultas do Apache Hive com o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utilize a ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados para o HDInsight](./../hdinsight-upload-data.md).