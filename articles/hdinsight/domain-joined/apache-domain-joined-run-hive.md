---
title: Políticas de Apache Hive no Apache Ranger – Azure HDInsight
description: Saiba como configurar políticas do Apache Ranger para o hive em um serviço do Azure HDInsight com o Enterprise Security Package.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: ff612c43a058fce02bd801e15632c27979f22d17
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435860"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Configurar as políticas do Apache Hive no HDInsight com o Pacote de Segurança Enterprise

Saiba como configurar políticas do Apache Ranger para Apache Hive. Neste artigo, irá criar duas políticas do Ranger para restringir o acesso a hivesampletable. O hivesampletable é fornecido com clusters do HDInsight. Depois de configurar as políticas, use o Excel e o driver ODBC para se conectar a tabelas Hive no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster HDInsight com Enterprise Security Package. Consulte [Configurar clusters HDInsight com ESP](apache-domain-joined-configure.md).
* Uma estação de trabalho com o Office 2016, o Office 2013 Professional Plus, o Office 365 Pro Plus, o Excel 2013 Standalone ou o Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Ligar à IU do Apache Ranger Admin
**Para ligar à IU do Ranger Admin**

1. Em um navegador, navegue até a interface do usuário do administrador do Ranger em `https://CLUSTERNAME.azurehdinsight.net/Ranger/` em que CLUSTERname é o nome do cluster.

   > [!NOTE]  
   > O Ranger usa credenciais diferentes do que Apache Hadoop cluster. Para evitar que os navegadores usem credenciais do Hadoop em cache, use a nova janela do navegador InPrivate para se conectar à interface do usuário do administrador do Ranger.

2. Inicie sessão com o nome de utilizador e a palavra-passe do domínio do administrador do cluster:

    ![home page de Ranger do HDInsight para](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Atualmente, o Ranger só funciona com o Yarn e o Hive.

## <a name="create-domain-users"></a>Criar Utilizadores de domínio

Consulte [criar um cluster HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)para obter informações sobre como criar hiveruser1 e hiveuser2. Você usa as duas contas de usuário neste artigo.

## <a name="create-ranger-policies"></a>Criar políticas do Ranger

Nesta seção, você criará duas políticas do Ranger para acessar o hivesampletable. Conceda permissão selecionada em diferentes conjuntos de colunas. Ambos os usuários foram criados usando [criar um cluster HDInsight com o ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). Na próxima seção, você testará as duas políticas no Excel.

**Para criar políticas do Ranger**

1. Abra a IU do Ranger Admin. Consulte conectar-se à interface do usuário do administrador do Apache Ranger.
2. Selecione **CLUSTERNAME_Hive**, em **Hive**. Deverá ver duas políticas de pré-configuração.
3. Selecione **Adicionar nova política**e, em seguida, insira os seguintes valores:

    |Propriedade |Valor |
    |---|---|
    |Nome da Política|Read-hivesampletable-All|
    |Banco de dados do hive|predefinição|
    |table|hivesampletable|
    |Coluna do hive|*|
    |Selecionar Utilizador|hiveuser1|
    |Permissões|selecione|

    ![Configuração de políticas de hive do HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Se um utilizador de domínio não for povoado em Selecionar Utilizador, aguarde um momento enquanto o Ranger é sincronizado com o AAD.

4. Selecione **Adicionar** para salvar a política.

5. Repita os dois últimos passos para criar outra política com as seguintes propriedades:

    |Propriedade |Valor |
    |---|---|
    |Nome da Política|Leia-hivesampletable-devicemake|
    |Banco de dados do hive|predefinição|
    |table|hivesampletable|
    |Coluna do hive|ClientID, devicemake|
    |Selecionar Utilizador|hiveuser2|
    |Permissões|selecione|

## <a name="create-hive-odbc-data-source"></a>Criar a origem de dados do ODBC do Hive

As instruções podem ser encontradas em [Criar origem de dados do ODBC do Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Propriedade  |Descrição |
 | --- | --- |
 | Nome da Origem de Dados | Atribua um nome para a sua origem de dados |
 | Host | Insira CLUSTERNAME.azurehdinsight.net. Por exemplo, myHDICluster.azurehdinsight.net |
 | Porta | Utilize **443**. (Esta porta foi alterada de 563 para 443.) |
 | Base de Dados | Use **Predefinição**. |
 | Tipo de Servidor do Hive | Selecione **Servidor do Hive 2** |
 | Mecanismo | Selecione **Serviço do Azure HDInsight** |
 | Caminho HTTP | Deixe em branco. |
 | Nome de Utilizador | Introduza hiveuser1@contoso158.onmicrosoft.com. Atualize o nome de domínio se ele for diferente. |
 | Palavra-passe | Introduza a palavra-passe para hiveuser1. |

Certifique-se de que clica em **Teste**, antes de guardar a origem de dados.

## <a name="import-data-into-excel-from-hdinsight"></a>Importe dados para o Excel a partir do HDInsight

Na última seção, você configurou duas políticas.  O hiveuser1 tem a permissão de seleção em todas as colunas e o hiveuser2 tem a permissão de seleção em duas colunas. Nesta secção, representa os dois utilizadores para importar dados para o Excel.

1. Abra um livro novo ou existente no Excel.

1. Na guia **dados** , navegue até **obter dados** > **de outras fontes** > **do ODBC** para iniciar a janela **do ODBC** .

    ![Abrir assistente de conexão de dados](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. Na lista suspensa, selecione o nome da fonte de dados que você criou na última seção e, em seguida, selecione **OK**.

1. Para o primeiro uso, uma caixa de diálogo do **driver ODBC** será aberta. Selecione **Windows** no menu à esquerda. Em seguida, selecione **conectar** para abrir a janela **navegador** .

1. Aguarde que a caixa de diálogo **Selecionar Base de Dados e Tabela** se abra. Esta ação pode demorar alguns segundos.

1. Selecione **hivesampletable**e, em seguida, selecione **Avançar**.

1. Selecione **Concluir**.

1. Na caixa de diálogo **Importar Dados**, pode alterar ou especificar a consulta. Para fazer isso, selecione **Propriedades**. Esta ação pode demorar alguns segundos.

1. Selecione a guia **definição** . O texto do comando é:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Através das políticas do Ranger que definiu, o hiveuser1 tem permissão de seleção em todas as colunas.  Portanto, essa consulta funciona com as credenciais do hiveuser1, mas essa consulta não funciona com as credenciais do hiveuser2.

1. Selecione **OK** para fechar a caixa de diálogo Propriedades da conexão.

1. Selecione **OK** para fechar a caixa de diálogo **importar dados** .  

1. Reintroduza a palavra-passe para hiveuser1 e clique em **OK**. Demora alguns segundos antes de os dados serem importados para o Excel. Quando terminar, você deverá ver 11 colunas de dados.

Para testar a segunda política (Read-hivesampletable-devicemake), você criou na última seção

1. Adicione uma nova folha no Excel.
2. Siga o último procedimento para importar os dados.  A única alteração feita é usar as credenciais do hiveuser2 em vez de hiveuser1. Isso falha porque hiveuser2 só tem permissão para ver duas colunas. Deve receber o erro seguinte:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
        
3. Siga o mesmo procedimento para importar dados. Desta vez, utilize as credenciais do hiveuser2 e modifique a instrução de seleção de:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    para:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Quando terminar, você deverá ver duas colunas de dados importadas.

## <a name="next-steps"></a>Passos seguintes

* Para configurar um cluster HDInsight com Enterprise Security Package, consulte [Configurar clusters hdinsight com ESP](apache-domain-joined-configure.md).
* Para gerenciar um cluster HDInsight com o ESP, consulte [gerenciar clusters hdinsight com o ESP](apache-domain-joined-manage.md).
* Para executar consultas de Hive usando SSH em clusters HDInsight com ESP, consulte [usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Para conectar o hive usando o hive JDBC, consulte [conectar-se ao Apache Hive no Azure HDInsight usando o driver JDBC do hive](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Para conectar o Excel ao Hadoop usando o ODBC do hive, consulte [conectar o Excel ao Apache Hadoop com a unidade ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Para conectar o Excel ao Hadoop usando o Power Query, consulte [conectar o Excel ao Apache Hadoop usando Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
