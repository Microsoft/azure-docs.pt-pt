---
title: Políticas da Colmeia Apache em Apache Ranger - Azure HDInsight
description: Saiba como configurar as políticas do Apache Ranger para a Hive num serviço Azure HDInsight com pacote de segurança empresarial.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 90d7da9c8ddd8c9c595f2209dcc34e2f595acfd2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196931"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Configurar as políticas do Apache Hive no HDInsight com o Pacote de Segurança Enterprise

Aprenda a configurar as políticas do Apache Ranger para a Apache Hive. Neste artigo, irá criar duas políticas do Ranger para restringir o acesso a hivesampletable. O hivesampletable é fornecido com clusters do HDInsight. Depois de configurar as políticas, utiliza o condutor do Excel e da ODBC para se ligar às tabelas da Hive no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster HDInsight com pacote de segurança empresarial. Consulte [os clusters HDInsight de Configuração com ESP](apache-domain-joined-configure.md).
* Uma estação de trabalho com o Office 2016, o Office 2013 Professional Plus, o Office 365 Pro Plus, o Excel 2013 Standalone ou o Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Ligar à IU do Apache Ranger Admin
**Para ligar à IU do Ranger Admin**

1. A partir de um navegador, navegue até à Ranger Admin UI em `https://CLUSTERNAME.azurehdinsight.net/Ranger/` onde clusterNAME é o nome do seu cluster.

   > [!NOTE]  
   > Ranger usa credenciais diferentes do aglomerado Apache Hadoop. Para evitar que os navegadores utilizem credenciais Hadoop em cache, use uma nova janela de navegador InPrivate para se conectar ao Ranger Admin UI.

2. Inicie sessão com o nome de utilizador e a palavra-passe do domínio do administrador do cluster:

    ![Página inicial do HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Atualmente, o Ranger só funciona com o Yarn e o Hive.

## <a name="create-domain-users"></a>Criar Utilizadores de domínio

Consulte [Criar um cluster HDInsight com ESP,](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)para obter informações sobre como criar hiveruser1 e hiveuser2. Utiliza as duas contas de utilizador neste artigo.

## <a name="create-ranger-policies"></a>Criar políticas do Ranger

Nesta secção, cria-se duas políticas ranger para aceder à colmeia. Conceda permissão selecionada em diferentes conjuntos de colunas. Ambos os utilizadores foram criados usando [create a HDInsight cluster com ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). Na próxima secção, vais testar as duas políticas no Excel.

**Para criar políticas do Ranger**

1. Abra a IU do Ranger Admin. Consulte a Ligação à Apache Ranger Admin UI.
2. Selecione **CLUSTERNAME_Hive,** sob **a Colmeia.** Deverá ver duas políticas de pré-configuração.
3. Selecione **Adicionar Nova Política**e, em seguida, introduza os seguintes valores:

    |Propriedade |Valor |
    |---|---|
    |Nome da Política|leitura-hivesampletable-all|
    |Base de Dados da Colmeia|predefinição|
    |table|hivesampletable|
    |Coluna da Colmeia|*|
    |Selecionar Utilizador|hiveuser1|
    |Permissões|selecionar|

    ![HDInsight ESP Ranger Hive políticas configurar](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Se um utilizador de domínio não for povoado em Selecionar Utilizador, aguarde um momento enquanto o Ranger é sincronizado com o AAD.

4. Selecione **Adicionar** para salvar a apólice.

5. Repita os dois últimos passos para criar outra política com as seguintes propriedades:

    |Propriedade |Valor |
    |---|---|
    |Nome da Política|leitura-hivesampletable-dispositivo|
    |Base de Dados da Colmeia|predefinição|
    |table|hivesampletable|
    |Coluna da colmeia|clientid, dispositivomake|
    |Selecionar Utilizador|hiveuser2|
    |Permissões|selecionar|

## <a name="create-hive-odbc-data-source"></a>Criar a origem de dados do ODBC do Hive

As instruções podem ser encontradas em [Criar origem de dados do ODBC do Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Propriedade  |Descrição |
 | --- | --- |
 | Nome da Origem de Dados | Atribua um nome para a sua origem de dados |
 | Anfitrião | Entra CLUSTERNAME.azurehdinsight.net. Por exemplo, myHDICluster.azurehdinsight.net |
 | Porta | Utilize **443**. (Esta porta foi alterada de 563 para 443.) |
 | Base de Dados | Use **Predefinição**. |
 | Tipo de Servidor do Hive | Selecione **Servidor do Hive 2** |
 | Mecanismo | Selecione **Serviço do Azure HDInsight** |
 | Caminho HTTP | Deixe em branco. |
 | Nome de Utilizador | Introduza hiveuser1@contoso158.onmicrosoft.com. Atualize o nome de domínio se for diferente. |
 | Palavra-passe | Introduza a palavra-passe para hiveuser1. |

Certifique-se de que clica em **Teste**, antes de guardar a origem de dados.

## <a name="import-data-into-excel-from-hdinsight"></a>Importe dados para o Excel a partir do HDInsight

Na última secção, configuraste duas políticas.  O hiveuser1 tem a permissão de seleção em todas as colunas e o hiveuser2 tem a permissão de seleção em duas colunas. Nesta secção, representa os dois utilizadores para importar dados para o Excel.

1. Abra um livro novo ou existente no Excel.

1. A partir do separador **Dados,** navegue para **obter dados** > **de outras fontes** > da **ODBC** para lançar a janela **A partir da ODBC.**

    ![Assistente de ligação de dados aberto](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. A partir da lista de lançamentos, selecione o nome de origem de dados que criou na última secção e, em seguida, selecione **OK**.

1. Para a primeira utilização, abrir-se-á um diálogo do **condutor DaDBC.** Selecione **Windows** a partir do menu esquerdo. Em seguida, selecione **Ligar** para abrir a janela **Navigator.**

1. Aguarde que a caixa de diálogo **Selecionar Base de Dados e Tabela** se abra. Esta ação pode demorar alguns segundos.

1. Selecione **a tabela de amostras,** e, em seguida, selecione **Next**.

1. Selecione **Concluir**.

1. Na caixa de diálogo **Importar Dados**, pode alterar ou especificar a consulta. Para isso, selecione **Propriedades**. Esta ação pode demorar alguns segundos.

1. Selecione o separador **Definição.** O texto de comando é:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Através das políticas do Ranger que definiu, o hiveuser1 tem permissão de seleção em todas as colunas.  Portanto, esta consulta funciona com as credenciais do HIVEuser1, mas esta consulta não funciona com as credenciais do HIVEuser2.

1. Selecione **OK** para fechar o diálogo De Ligação Propriedades.

1. Selecione **OK** para fechar o diálogo **de dados de importação.**  

1. Reintroduza a palavra-passe para hiveuser1 e clique em **OK**. Demora alguns segundos antes de os dados serem importados para o Excel. Quando estiver feito, verá 11 colunas de dados.

Para testar a segunda política (read-hivesampletable-devicemake), criada na última secção

1. Adicione uma nova folha no Excel.
2. Siga o último procedimento para importar os dados.  A única mudança que fazes é usar as credenciais do HIVeuser2 em vez das do hiveuser1. Isto falha porque o hiveuser2 só tem permissão para ver duas colunas. Deve receber o erro seguinte:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
        
3. Siga o mesmo procedimento para importar dados. Desta vez, utilize as credenciais do hiveuser2 e modifique a instrução de seleção de:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    para:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Quando estiver feito, verá duas colunas de dados importadas.

## <a name="next-steps"></a>Passos seguintes

* Para configurar um cluster HDInsight com pacote de segurança empresarial, consulte [os clusters Configure HDInsight com ESP](apache-domain-joined-configure.md).
* Para gerir um cluster HDInsight com ESP, consulte [Gerir os clusters HDInsight com ESP](apache-domain-joined-manage.md).
* Para executar consultas de Hive utilizando clusters SSH em HDInsight com ESP, consulte [Use SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
* Para ligar a colmeia utilizando a Hive JDBC, consulte [Connect to Apache Hive on Azure HDInsight utilizando o condutor Hive JDBC](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Para ligar excel a Hadoop usando hive ODBC, consulte [Connect Excel a Apache Hadoop com a unidade Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Para ligar excel a Hadoop usando power query, consulte [Connect Excel a Apache Hadoop utilizando power query](../hadoop/apache-hadoop-connect-excel-power-query.md)
