---
title: Excel & Apache Hadoop com controlador de conectividade de base de dados aberta (ODBC) - Azure HDInsight
description: Aprenda a configurar e a utilizar o controlador ODBC da Microsoft Hive para o Excel para consultar dados em clusters HDInsight do Microsoft Excel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: b5499a8c4fd6ee200ca0fac800c8c17c92a1cc0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087848"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Ligar o Excel ao Apache Hadoop no Azure HDInsight com o controlador ODBC Hive da Microsoft

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A solução Big Data da Microsoft integra componentes da Microsoft Business Intelligence (BI) com clusters Apache Hadoop implantados em HDInsight. Um exemplo é a capacidade de ligar o Excel ao armazém de dados da Hive de um cluster Hadoop. Conecte-se utilizando o controlador de conectividade da base de dados aberta da Microsoft Hive (ODBC).

Pode ligar os dados associados a um cluster HDInsight do Excel com o add-in da Microsoft Power Query para o Excel. Para obter mais informações, consulte [Connect Excel a HDInsight com Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, deve ter os seguintes itens:

* Um aglomerado de Hadoop HDInsight. Para criar um, consulte [Começar com Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Uma estação de trabalho com o Office 2010 Professional Plus ou mais tarde, ou Excel 2010 ou mais tarde.

## <a name="install-microsoft-hive-odbc-driver"></a>Instale o controlador ODBC da Microsoft Hive

Descarregue e [instale o Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886). Escolha a versão que corresponde à versão da aplicação onde utilizará o controlador ODBC.  Para este artigo, o motorista é utilizado para o Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Criar fonte de dados Apache Hive ODBC

Os passos seguintes mostram-lhe como criar uma Fonte de Dados Da Colmeia ODBC.

1. A partir do Windows, navegue para **iniciar > ferramentas administrativas do Windows > fontes de dados ODBC (32-bit)/(64-bit)**.  Esta ação abre a janela do **Administrador de Fonte de Dados ODBC.**

    ![Administrador de fonte de dados OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Configure um DSN usando o administrador de origem de dados da ODBC")

1. A partir do **separador DSN do utilizador,** selecione **Adicionar** para abrir a janela **Criar Nova Fonte de Dados.**

1. Selecione **Microsoft Hive ODBC Driver**e, em seguida, selecione **Finish** para abrir a janela **de configuração DSN do controlador do Microsoft Hive ODBC.**

1. Escreva ou selecione os seguintes valores:

   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da Origem de Dados |Atribua um nome para a sua origem de dados |
   |  Anfitriões ou anfitriões |Introduza `HDInsightClusterName.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net`. Nota: `HDInsightClusterName-int.azurehdinsight.net` é suportado desde que o VM cliente seja espreitado para a mesma rede virtual. |
   |  Porta |Utilize **443**. (Esta porta foi alterada de 563 para 443.) |
   |  Base de Dados |Utilizar **o padrão**. |
   |  Mecanismo |Selecione **Windows Azure HDInsight Service** |
   |  Nome de Utilizador |Introduza o nome de utilizador do grupo HDInsight HTTP. O nome de utilizador predefinido é **admin**. |
   |  Palavra-passe |Insira a palavra-passe do utilizador do cluster HDInsight. Selecione a caixa de verificação **Guardar palavra-passe (encriptada)**.|

1. Opcional: Selecione **Opções Avançadas...**  

   | Parâmetro | Descrição |
   | --- | --- |
   |  Utilizar consulta nativa |Quando é selecionado, o controlador ODBC NÃO tenta converter OSQL em HiveQL. Só o usará se tiver 100% de certeza de que está a enviar declarações puras da HiveQL. Ao ligar ao SQL Server ou à Base de Dados Azure SQL, deve deixá-lo desmarcado. |
   |  Linhas recolhidas por bloco |Ao obter um grande número de registos, pode ser necessário sintonizar este parâmetro para garantir um desempenho ótimo. |
   |  Comprimento da coluna de corda padrão, comprimento da coluna binária, escala de coluna decimal |Os comprimentos e precisões do tipo de dados podem afetar a forma como os dados são devolvidos. Fazem com que informações incorretas sejam devolvidas devido à perda de precisão e ou truncação. |

    ![Opções avançadas de configuração do DSN](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Opções avançadas de configuração do DSN")

1. Selecione **Teste** para testar a fonte de dados. Quando a fonte de dados está configurada corretamente, o resultado do teste mostra **SUCCESS!**

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a janela **de configuração DSN do controlador da Colmeia Microsoft.**  

1. Selecione **OK** para fechar a janela **do administrador de dados ODBC.**  

## <a name="import-data-into-excel-from-hdinsight"></a>Importe dados para o Excel a partir do HDInsight

Os seguintes passos descrevem a forma de importar dados de uma tabela de Colmeias num livro do Excel utilizando a fonte de dados ODBC que criou na secção anterior.

1. Abra um livro novo ou existente no Excel.

2. A partir do separador **Dados,** navegue para **obter**  >  **dados de outras fontes**  >  **da ODBC** para lançar a janela **ODBC.**

    ![Abra o assistente de ligação de dados do Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Abra o assistente de ligação de dados do Excel")

3. A partir da lista de drop-down, selecione o nome de origem de dados que criou na última secção e, em seguida, selecione **OK**.

4. Para a primeira utilização, abre-se um diálogo do **controlador ODBC.** Selecione **Windows** a partir do menu esquerdo. Em seguida, **selecione Connect** para abrir a janela **Do Navegador.**

5. A partir do **Navigator,** navegue até ao padrão **hive**  >  **default**  >  **hivesampletable**e, em seguida, selecione **Load**. Leva alguns momentos até que os dados são importados para o Excel.

    ![Navegador HDInsight Excel Hive ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "Navegador HDInsight Excel Hive ODBC")

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar o controlador ODBC da Microsoft Hive para recuperar dados do Serviço HDInsight para o Excel. Da mesma forma, pode obter dados do Serviço HDInsight para a Base de Dados SQL. Também é possível enviar dados para um Serviço HDInsight. Para saber mais, veja:

* [Visualizar os dados da Apache Hive com o Microsoft Power BI em Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados de hive de consulta interativa com Power BI em Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Ligue o Excel ao Apache Hadoop utilizando a Consulta de Potência](apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute consultas apache hive usando ferramentas data lake para estúdio visual.](apache-hadoop-visual-studio-tools-get-started.md)
