---
title: Políticas de Apache Hive no Apache Ranger – Azure HDInsight
description: Saiba como configurar políticas do Apache Ranger para o hive em um serviço do Azure HDInsight com o Enterprise Security Package.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: e49e2d103fd9c91824c8e8a1603cddddf16366e1
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044863"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Configurar as políticas do Apache Hive no HDInsight com o Pacote de Segurança Enterprise
Saiba como configurar políticas do Apache Ranger para Apache Hive. Neste artigo, irá criar duas políticas do Ranger para restringir o acesso a hivesampletable. O hivesampletable é fornecido com clusters do HDInsight. Após ter configurado as políticas, utilize o Excel e o controlador ODBC para estabelecer uma ligação a tabelas do Hive no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos
* Um cluster HDInsight com Enterprise Security Package. Consulte [Configurar clusters HDInsight com ESP](apache-domain-joined-configure.md).
* Uma estação de trabalho com o Office 2016, o Office 2013 Professional Plus, o Office 365 Pro Plus, o Excel 2013 Standalone ou o Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Ligar à IU do Apache Ranger Admin
**Para ligar à IU do Ranger Admin**

1. Num browser, ligue-se à IU do Ranger Admin. O URL é https://&lt;ClusterName>.azurehdinsight.net/Ranger/.

   > [!NOTE]  
   > O Ranger usa credenciais diferentes do que Apache Hadoop cluster. Para evitar que os navegadores usem credenciais do Hadoop em cache, use a nova janela do navegador InPrivate para se conectar à interface do usuário do administrador do Ranger.

2. Inicie sessão com o nome de utilizador e a palavra-passe do domínio do administrador do cluster:

    ![home page de Ranger do HDInsight para](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Atualmente, o Ranger só funciona com o Yarn e o Hive.

## <a name="create-domain-users"></a>Criar Utilizadores de domínio
Consulte [criar um cluster HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)para obter informações sobre como criar hiveruser1 e hiveuser2. Você usa as duas contas de usuário neste artigo.

## <a name="create-ranger-policies"></a>Criar políticas do Ranger
Nesta seção, você criará duas políticas do Ranger para acessar o hivesampletable. Conceda permissão selecionada em diferentes conjuntos de colunas. Ambos os usuários foram criados usando [criar um cluster HDInsight com o ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp). Na secção seguinte, irá testar as duas políticas no Excel.

**Para criar políticas do Ranger**

1. Abra a IU do Ranger Admin. Consulte conectar-se à interface do usuário do administrador do Apache Ranger.
2. Clique em **&lt;ClusterName >_hive**, em **Hive**. Deverá ver duas políticas de pré-configuração.
3. Clique em **Adicionar Nova Política**, e, em seguida, introduza os seguintes valores:

   * Nome da política: read-hivesampletable-all
   * Base de dados do Hive: predefinido
   * tabela: hivesampletable
   * Coluna do Hive: *
   * Selecione o Utilizador: hiveuser1
   * Permissões: selecionar

     ![Configuração de política de hive do HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]  
     > Se um utilizador de domínio não for povoado em Selecionar Utilizador, aguarde um momento enquanto o Ranger é sincronizado com o AAD.
     >
     >
4. Clique em **Adicionar** para guardar a política.
5. Repita os dois últimos passos para criar outra política com as seguintes propriedades:

   * Nome da política: read-hivesampletable-devicemake
   * Base de dados do Hive: predefinido
   * tabela: hivesampletable
   * Coluna do Hive: clientid, devicemake
   * Selecione o Utilizador: hiveuser2
   * Permissões: selecionar

## <a name="create-hive-odbc-data-source"></a>Criar a origem de dados do ODBC do Hive
As instruções podem ser encontradas em [Criar origem de dados do ODBC do Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Propriedade  |Descrição |
 | --- | --- |
 | Nome da Origem de Dados | Atribua um nome para a sua origem de dados |
 | Host | Introduza &lt;HDInsightClusterName>.azurehdinsight.net. Por exemplo, myHDICluster.azurehdinsight.net |
 | Porta | Utilize **443**. (Esta porta foi alterada de 563 para 443.) |
 | Base de Dados | Use **Predefinição**. |
 | Tipo de Servidor do Hive | Selecione **Servidor do Hive 2** |
 | Mecanismo | Selecione **Serviço do Azure HDInsight** |
 | Caminho HTTP | Deixe em branco. |
 | Nome de Utilizador | Introduza hiveuser1@contoso158.onmicrosoft.com. Atualize o nome de domínio se ele for diferente. |
 | Palavra-passe | Introduza a palavra-passe para hiveuser1. |

Certifique-se de que clica em **Teste**, antes de guardar a origem de dados.

## <a name="import-data-into-excel-from-hdinsight"></a>Importe dados para o Excel a partir do HDInsight
Na última secção, configurou duas políticas.  O hiveuser1 tem a permissão de seleção em todas as colunas e o hiveuser2 tem a permissão de seleção em duas colunas. Nesta secção, representa os dois utilizadores para importar dados para o Excel.

1. Abra um livro novo ou existente no Excel.
2. No separador **Dados**, clique em **De Outras Origens de Dados** e, em seguida, clique em **Do Assistente de Ligação de Dados** para iniciar o **Assistente de Ligação de Dados**.

    ![Abrir assistente de ligação de dados][img-hdi-simbahiveodbc.excel.dataconnection]
3. Selecione **ODBC DSN** como a origem de dados e, em seguida, clique em **Seguinte**.
4. Nas origens de dados do ODBC, selecione o nome da origem de dados que criou no passo anterior e, em seguida, clique em **Seguinte**.
5. Insira a senha novamente para o cluster no assistente e clique em **OK**. Aguarde que a caixa de diálogo **Selecionar Base de Dados e Tabela** se abra. Esta ação pode demorar alguns segundos.
6. Selecione **hivesampletable** e, em seguida, clique em **Seguinte**.
7. Clique em **Concluir**.
8. Na caixa de diálogo **Importar Dados**, pode alterar ou especificar a consulta. Para o fazer, clique em **Propriedades**. Esta ação pode demorar alguns segundos.
9. Clique na guia **definição** . O texto do comando é:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Através das políticas do Ranger que definiu, o hiveuser1 tem permissão de seleção em todas as colunas.  Portanto, essa consulta funciona com as credenciais do hiveuser1, mas essa consulta não funciona com as credenciais do hiveuser2.

   ![Propriedades da Ligação][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Clique em **OK** para fechar a caixa de diálogo Propriedades da Ligação.
11. Clique em **OK** para fechar a caixa de diálogo **Importar Dados**.  
12. Reintroduza a palavra-passe para hiveuser1 e clique em **OK**. Demora alguns segundos antes de os dados serem importados para o Excel. Quando estiver concluído, deverá ver 11 colunas de dados.

Para testar a segunda política (Read-hivesampletable-devicemake), você criou na última seção

1. Adicione uma nova folha no Excel.
2. Siga o último procedimento para importar os dados.  A única alteração feita é usar as credenciais do hiveuser2 em vez de hiveuser1. Isso falha porque hiveuser2 só tem permissão para ver duas colunas. Deve receber o erro seguinte:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Siga o mesmo procedimento para importar dados. Desta vez, utilize as credenciais do hiveuser2 e modifique a instrução de seleção de:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    para:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Quando estiver concluído, deverá ver duas colunas de dados importados.

## <a name="next-steps"></a>Passos seguintes
* Para configurar um cluster HDInsight com Enterprise Security Package, consulte [Configurar clusters hdinsight com ESP](apache-domain-joined-configure.md).
* Para gerenciar um cluster HDInsight com o ESP, consulte [gerenciar clusters hdinsight com o ESP](apache-domain-joined-manage.md).
* Para executar consultas de Hive usando SSH em clusters HDInsight com ESP, consulte [usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Para conectar o hive usando o hive JDBC, consulte [conectar-se ao Apache Hive no Azure HDInsight usando o driver JDBC do hive](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Para conectar o Excel ao Hadoop usando o ODBC do hive, consulte [conectar o Excel ao Apache Hadoop com a unidade ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Para conectar o Excel ao Hadoop usando o Power Query, consulte [conectar o Excel ao Apache Hadoop usando Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
