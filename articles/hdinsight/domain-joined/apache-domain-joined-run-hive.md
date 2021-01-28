---
title: Apache Hive políticas em Apache Ranger - Azure HDInsight
description: Saiba como configurar as políticas apache ranger para a Colmeia num serviço Azure HDInsight com o Pacote de Segurança Empresarial.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 8ebc03d0847414730c51b899be4cf6586d064696
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932239"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Configurar as políticas do Apache Hive no HDInsight com o Pacote de Segurança Enterprise

Saiba como configurar as políticas apache ranger para a Colmeia Apache. Neste artigo, irá criar duas políticas do Ranger para restringir o acesso a hivesampletable. O hivesampletable é fornecido com clusters do HDInsight. Depois de configurar as políticas, utilize o controlador Excel e ODBC para ligar às tabelas hive em HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster HDInsight com pacote de segurança empresarial. Consulte [os clusters Configure HDInsight com ESP](./apache-domain-joined-configure-using-azure-adds.md).
* Uma estação de trabalho com aplicativos Microsoft 365 para empresas, Office 2016, Office 2013 Professional Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Ligar à IU do Apache Ranger Admin
**Para ligar à IU do Ranger Admin**

1. A partir de um navegador, navegue para o Ranger Admin UI onde `https://CLUSTERNAME.azurehdinsight.net/Ranger/` CLUSTERNAME é o nome do seu cluster.

   > [!NOTE]  
   > Ranger usa credenciais diferentes do aglomerado apache Hadoop. Para evitar que os navegadores utilizem credenciais de Hadoop em cache, utilize uma nova janela do navegador InPrivate para ligar ao Ranger Admin UI.

2. Inicie sessão com o nome de utilizador e a palavra-passe do domínio do administrador do cluster:

    ![Página inicial do HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Atualmente, o Ranger só funciona com o Yarn e o Hive.

## <a name="create-domain-users"></a>Criar Utilizadores de domínio

Consulte [Criar um cluster HDInsight com ESP,](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)para obter informações sobre como criar hiver1 e hiveuser2. Utilize as duas contas de utilizador neste artigo.

## <a name="create-ranger-policies"></a>Criar políticas do Ranger

Nesta secção, cria-se duas políticas ranger para aceder ao hivesmpletable. Conceda permissão selecionada em diferentes conjuntos de colunas. Ambos os utilizadores foram criados usando [criar um cluster HDInsight com ESP.](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp) Na próxima secção, vai testar as duas políticas no Excel.

**Para criar políticas do Ranger**

1. Abra a IU do Ranger Admin. Consulte Ligar à IU do Apache Ranger Admin.
2. Selecione **CLUSTERNAME_Hive,** em **Hive**. Deverá ver duas políticas de pré-configuração.
3. **Selecione Adicionar Nova Política** e, em seguida, insira os seguintes valores:

    |Propriedade |Valor |
    |---|---|
    |Nome da Política|read-hivesampletable-all|
    |Base de Dados da Colmeia|predefinição|
    |table|hivesmpletable|
    |Coluna da Colmeia|*|
    |Selecionar Utilizador|hiveuser1|
    |Permissões|selecionar|

    ![HdInsight ESP Ranger Hive configurar políticas de configuração](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Se um utilizador de domínio não for povoado em Selecionar Utilizador, aguarde um momento enquanto o Ranger é sincronizado com o AAD.

4. **Selecione Adicionar** para guardar a apólice.

5. Repita os dois últimos passos para criar outra política com as seguintes propriedades:

    |Propriedade |Valor |
    |---|---|
    |Nome da Política|read-hivesampletable-devicemake|
    |Base de Dados da Colmeia|predefinição|
    |table|hivesmpletable|
    |Coluna de colmeia|cliente, fabricante de dispositivos|
    |Selecionar Utilizador|hiveuser2|
    |Permissões|selecionar|

## <a name="create-hive-odbc-data-source"></a>Criar a origem de dados do ODBC do Hive

As instruções podem ser encontradas em [Criar origem de dados do ODBC do Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Propriedade  |Descrição |
 | --- | --- |
 | Nome da Origem de Dados | Atribua um nome para a sua origem de dados |
 | Anfitrião | Introduza CLUSTERNAME.azurehdinsight.net. Por exemplo, myHDICluster.azurehdinsight.net |
 | Porta | Utilize **443**. (Esta porta foi alterada de 563 para 443.) |
 | Base de Dados | Utilizar **predefinido**. |
 | Tipo de Servidor do Hive | Selecione **Servidor do Hive 2** |
 | Mecanismo | Selecione **Serviço do Azure HDInsight** |
 | Caminho HTTP | Deixe em branco. |
 | Nome de Utilizador | Introduza hiveuser1@contoso158.onmicrosoft.com. Atualize o nome de domínio se for diferente. |
 | Palavra-passe | Introduza a palavra-passe para hiveuser1. |

Certifique-se de que clica em **Teste**, antes de guardar a origem de dados.

## <a name="import-data-into-excel-from-hdinsight"></a>Importe dados para o Excel a partir do HDInsight

Na última secção, configuraste duas apólices.  O hiveuser1 tem a permissão de seleção em todas as colunas e o hiveuser2 tem a permissão de seleção em duas colunas. Nesta secção, representa os dois utilizadores para importar dados para o Excel.

1. Abra um livro novo ou existente no Excel.

1. A partir do separador **Dados,** navegue para **obter**  >  **dados de outras fontes**  >  **da ODBC** para lançar a janela **ODBC.**

    ![Abrir o assistente de ligação de dados](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. A partir da lista de drop-down, selecione o nome de origem de dados que criou na última secção e, em seguida, selecione **OK**.

1. Para a primeira utilização, abre-se um diálogo do **controlador ODBC.** Selecione **Windows** a partir do menu esquerdo. Em seguida, **selecione Connect** para abrir a janela **Do Navegador.**

1. Aguarde que a caixa de diálogo **Selecionar Base de Dados e Tabela** se abra. Esta ação pode demorar alguns segundos.

1. Selecione **hivesmpletable** e, em seguida, selecione **Seguinte**.

1. Selecione **Concluir**.

1. Na caixa de diálogo **Importar Dados**, pode alterar ou especificar a consulta. Para tal, selecione **Propriedades.** Esta ação pode demorar alguns segundos.

1. Selecione o **separador Definição.** O texto de comando é:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"`
    ```

   Através das políticas do Ranger que definiu, o hiveuser1 tem permissão de seleção em todas as colunas.  Portanto, esta consulta funciona com as credenciais da Hiveuser1, mas esta consulta não funciona com as credenciais da Hiveuser2.

1. Selecione **OK** para fechar o diálogo Connection Properties.

1. Selecione **OK** para fechar o diálogo **de dados** de importação.  

1. Reintroduza a palavra-passe para hiveuser1 e clique em **OK**. Demora alguns segundos antes de os dados serem importados para o Excel. Quando estiver feito, verá 11 colunas de dados.

Para testar a segunda política (read-hivesampletable-devicemake), criou na última secção

1. Adicione uma nova folha no Excel.
2. Siga o último procedimento para importar os dados.  A única mudança que se faz é usar as credenciais do hiveuser2 em vez de hiveuser1. Isto falha porque a hiveuser2 só tem permissão para ver duas colunas. Deve receber o erro seguinte:

    ```output
    [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
    ```

3. Siga o mesmo procedimento para importar dados. Desta vez, utilize as credenciais do hiveuser2 e modifique a instrução de seleção de:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"
    ```

    para:

    ```sql
    SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"
    ```

    Quando estiver feito, verá duas colunas de dados importadas.

## <a name="next-steps"></a>Próximos passos

* Para configurar um cluster HDInsight com pacote de segurança empresarial, consulte [clusters Configure HDInsight com ESP](./apache-domain-joined-configure-using-azure-adds.md).
* Para gerir um cluster HDInsight com ESP, consulte [Gerir clusters HDInsight com ESP](apache-domain-joined-manage.md).
* Para executar consultas de Hive utilizando SSH em clusters HDInsight com ESP, consulte [Use SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
* Para ligar a colmeia utilizando a Colmeia JDBC, consulte [Connect to Apache Hive on Azure HDInsight usando o controlador Hive JDBC](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Para ligar o Excel ao Hadoop utilizando o Hive ODBC, consulte [Connect Excel a Apache Hadoop com a unidade Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Para ligar o Excel ao Hadoop utilizando a Consulta de Energia, consulte [Connect Excel a Apache Hadoop utilizando a Consulta de Energia](../hadoop/apache-hadoop-connect-excel-power-query.md)