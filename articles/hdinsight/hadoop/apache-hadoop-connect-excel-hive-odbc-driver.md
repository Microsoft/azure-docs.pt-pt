---
title: Excel & Apache Hadoop com ODBC Driver - Azure HDInsight
description: Aprenda a configurar e utilizar o controlador Microsoft Hive ODBC para o Excel consultar dados em clusters HDInsight do Microsoft Excel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/02/2020
ms.openlocfilehash: f356009197c0446efa2ea2d7f0e90040229df47b
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251073"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Ligue excel a Apache Hadoop em Azure HDInsight com o condutor da Microsoft Hive ODBC

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A solução Big Data da Microsoft integra componentes da Microsoft Business Intelligence (BI) com clusters Apache Hadoop que foram implantados no Azure HDInsight. Um exemplo desta integração é a capacidade de ligar o Excel ao armazém de dados da Hive de um cluster Hadoop em HDInsight utilizando o Controlador de Conectividade de Base de Dados Aberta microsoft Hive (ODBC).

Também é possível ligar os dados associados a um cluster HDInsight e outras fontes de dados, incluindo outros clusters hadoop (não HDInsight), do Excel usando o add-in Microsoft Power Query para o Excel. Para obter informações sobre a instalação e utilização da Consulta de Energia, consulte [O Connect Excel para o HDInsight com a Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, deve ter os seguintes itens:

* Um aglomerado de Hadoop HDInsight. Para criar um, consulte Iniciar com [Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho com o Office 2010 Professional Plus ou mais tarde, ou Excel 2010 ou mais tarde.

## <a name="install-microsoft-hive-odbc-driver"></a>Instale o controlador Microsoft Hive ODBC

Descarregue e instale a versão [Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886) que corresponde à versão da aplicação onde estará a utilizar o controlador ODBC.  Para este artigo, o motorista é usado para o Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Criar fonte de dados apache Hive ODBC

Os seguintes passos mostram-lhe como criar uma Hive ODBC Data Source.

1. Do Windows, navegue para Start > Windows Administrative Tools > ODBC Data Sources (32 bits)/(64-bit).  Isto abre a janela do Administrador de Fonte de **Dados da ODBC.**

    ![Administrador de fonte de dados da OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Configure um DSN usando o Administrador de Fonte de Dados da ODBC")

1. A partir do separador **DSN** do utilizador, selecione **Adicionar** para abrir a janela Criar Nova Fonte de **Dados.**

1. Selecione **Microsoft Hive ODBC Driver**, e, em seguida, selecione **Finish** para abrir a janela de **configuração DSN do condutor da Microsoft Hive ODBC.**

1. Escreva ou selecione os seguintes valores:

   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da Origem de Dados |Atribua um nome para a sua origem de dados |
   |  Hospedeiro(s) |Introduza `HDInsightClusterName.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net`. Nota: `HDInsightClusterName-int.azurehdinsight.net` é suportado desde que o cliente VM seja espreitado para a mesma rede virtual. |
   |  Porta |Utilize **443**. (Esta porta foi alterada de 563 para 443.) |
   |  Base de Dados |Utilize o **padrão**. |
   |  Mecanismo |Selecione **serviço HDInsight Windows Azure** |
   |  Nome de Utilizador |Introduza o nome de utilizador do utilizador http do cluster HDInsight. O nome de utilizador predefinido é **admin**. |
   |  Palavra-passe |Introduza a palavra-passe do utilizador do cluster HDInsight. Selecione a caixa de verificação **Guardar palavra-passe (encriptada)** .|

1. Opcional: Selecione **Opções Avançadas...**  

   | Parâmetro | Descrição |
   | --- | --- |
   |  Usar consulta nativa |Quando é selecionado, o condutor da ODBC NÃO tenta converter o TSQL em HiveQL. Só o utilizará se tiver 100% de certeza de que está a apresentar declarações puras da HiveQL. Ao ligar-se ao SQL Server ou à Base de Dados SQL Azure, deve deixá-lo descontrolado. |
   |  Filas recolhidas por bloco |Ao recolher um grande número de registos, pode ser necessário sintonizar este parâmetro para garantir desempenhos ideais. |
   |  Comprimento da coluna de cadeia padrão, comprimento da coluna binária, escala de coluna decimal |Os comprimentos e precisões do tipo de dados podem afetar a forma como os dados são devolvidos. Provocam a derrocausa de informações incorretas devido à perda de precisão e/ou truncação. |

    ![Opções avançadas de configuração dSN](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Opções avançadas de configuração dSN")

1. Selecione **Teste** para testar a fonte de dados. Quando a fonte de dados está corretamente configurada, o resultado do teste mostra **SUCESSO!**  

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a janela de **configuração DSN do condutor da Microsoft Hive ODBC.**  

1. Selecione **OK** para fechar a janela do Administrador de Fonte de **Dados da ODBC.**  

## <a name="import-data-into-excel-from-hdinsight"></a>Importe dados para o Excel a partir do HDInsight

Os seguintes passos descrevem a forma de importar dados de uma tabela da Hive para um livro do Excel utilizando a fonte de dados da ODBC que criou na secção anterior.

1. Abra um livro novo ou existente no Excel.

2. A partir do separador **Dados,** navegue para **obter dados** > **de outras fontes** > da **ODBC** para lançar a janela **A partir da ODBC.**

    ![Assistente de ligação de dados Open Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Assistente de ligação de dados Open Excel")

3. A partir da lista de lançamentos, selecione o nome de origem de dados que criou na última secção e, em seguida, selecione **OK**.

4. Para a primeira utilização, abrir-se-á um diálogo do **condutor DaDBC.** Selecione **Windows** a partir do menu esquerdo. Em seguida, selecione **Ligar** para abrir a janela **Navigator.**

5. Do **Navigator,** navegue até à **HIVE** >  ** > ** **colmeia,** e, em seguida, selecione **Load**. Leva alguns momentos até que os dados sejam importados para o Excel.

    ![Navegador HDInsight Excel Hive ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "Navegador HDInsight Excel Hive ODBC")

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar o controlador Microsoft Hive ODBC para recuperar dados do Serviço HDInsight para o Excel. Da mesma forma, pode recuperar dados do Serviço HDInsight para a Base de Dados SQL. Também é possível fazer o upload de dados para um serviço HDInsight. Para saber mais, consulte:

* [Visualizar os dados da Hive Apache com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados de Colmeia de Consulta Interativa com Power BI em Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use Apache Zeppelin para executar consultas apache hive em Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Ligue excel a Apache Hadoop utilizando](apache-hadoop-connect-excel-power-query.md)power query .
* [Ligue-se ao Azure HDInsight e execute consultas apache hive usando ferramentas de data lake para estúdio visual](apache-hadoop-visual-studio-tools-get-started.md).
* [Utilize a ferramenta Azure HDInsight para o Código](../hdinsight-for-vscode.md)do Estúdio Visual .
* [Faça upload de dados para HDInsight](./../hdinsight-upload-data.md).
