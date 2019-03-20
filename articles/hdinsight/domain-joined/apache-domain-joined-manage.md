---
title: Gerir clusters do HDInsight com o Enterprise Security Enterprise - Azure
description: Saiba como gerir clusters do HDInsight com o Enterprise Security Package.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: 0fec9173eb94aa60c3689089e4eacd2d9d47724a
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200026"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Gerir clusters do HDInsight com o Enterprise Security Package
Saiba os utilizadores e as funções no pacote de segurança de Enterprise do HDInsight (ESP) e como gerir clusters do ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Utilizar o VSCode para ligar a um cluster associado a um domínio

Pode ligar um cluster do normal com o nome de utilizador do Apache ambari destina gerida, também ligar um cluster do Apache Hadoop de segurança com o nome de utilizador de domínio (como: user1@contoso.com).
1. Abra a paleta de comandos, selecionando **CTRL + SHIFT + P**e, em seguida, introduza **HDInsight: Ligar um cluster**.

   ![comando de cluster de ligação](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Introduza o HDInsight URL do cluster -> entrada nome de utilizador -> entrada de palavra-passe -> selecione o tipo de cluster ->-mostra informações de sucesso se transmitido de verificação.
   
   ![caixa de diálogo de cluster de ligação](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > O nome de utilizador ligado e a palavra-passe são utilizados se o cluster tanto registado na subscrição do Azure e ligadas um cluster. 
   
3. Pode ver um cluster de ligado, utilizando o comando **cluster de lista**. Agora pode submeter um script para este cluster ligado.

   ![cluster ligado](./media/apache-domain-joined-manage/linked-cluster.png)

4. Também pode desassociar um cluster ao inserir **HDInsight: Desassociar um cluster** da paleta de comandos.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Utilizar o IntelliJ para ligar a um cluster associado a um domínio

Pode ligar um cluster do normal com o nome de utilizador do Ambari gerida, também ligar um cluster de hadoop de segurança com o nome de utilizador de domínio (como: user1@contoso.com). 
1. Clique em **ligar um cluster** partir **Explorador do Azure**.

   ![menu de contexto do cluster de ligação](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Introduza **nome do Cluster**, **nome de utilizador** e **palavra-passe**. Precisa verificar o nome de utilizador e palavra-passe se a falha de autenticação. Opcionalmente, adicione a conta de armazenamento, a chave de armazenamento, em seguida, selecione um contentor do contentor de armazenamento. Informações de armazenamento são para o Explorador de armazenamento na árvore à esquerda
   
   ![caixa de diálogo de cluster de ligação](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > Usamos a chave de armazenamento ligada, o nome de utilizador e a palavra-passe se o cluster tanto registado na subscrição do Azure e ligadas um cluster.
   > ![Explorador de armazenamento no IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. Pode ver um cluster de ligado no **HDInsight** nó se as informações de entrada são adequadas. Agora pode enviar uma aplicação a este cluster ligado.

   ![cluster ligado](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Também pode desassociar um cluster a partir **Explorador do Azure**.
   
   ![Desassociar de cluster](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Utilizar o Eclipse para ligar a um cluster associado a um domínio

Pode ligar um cluster do normal com o nome de utilizador do Ambari gerida, também ligar um cluster de hadoop de segurança com o nome de utilizador de domínio (como: user1@contoso.com).
1. Clique em **ligar um cluster** partir **Explorador do Azure**.

   ![menu de contexto do cluster de ligação](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Introduza **nome do Cluster**, **nome de utilizador** e **palavra-passe**, em seguida, clique no botão OK para ligar o cluster. Opcionalmente, introduza a conta de armazenamento, a chave de armazenamento e, em seguida, selecione o contentor de armazenamento para o Explorador de armazenamento trabalhar na vista de árvore à esquerda
   
   ![caixa de diálogo de cluster de ligação](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Usamos a chave de armazenamento ligada, o nome de utilizador e a palavra-passe se o cluster tanto registado na subscrição do Azure e ligadas um cluster.
   > ![Explorador de armazenamento no Eclipse](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Pode ver um cluster de ligado no **HDInsight** nó depois de clicar no botão OK, se as informações de entrada são adequadas. Agora pode enviar uma aplicação a este cluster ligado.

   ![cluster ligado](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Também pode desassociar um cluster a partir **Explorador do Azure**.
   
   ![Desassociar de cluster](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Acessar os clusters com o Enterprise Security Package.

Enterprise Security Package (anteriormente conhecido como o HDInsight Premium) fornece acesso de vários utilizador para o cluster, onde a autenticação é realizada pelo Active Directory e a autorização ao Apache Ranger e ACLs de armazenamento (ACLs do ADLS). Autorização fornece limites seguros entre vários utilizadores e permite que apenas com privilégios que os utilizadores tenham acesso aos dados com base nas políticas de autorização.

Isolamento de segurança e de utilizador são importantes para um cluster do HDInsight com o Enterprise Security Package. Para cumprir estes requisitos, o acesso SSH ao cluster com o Enterprise Security Package é bloqueado. A tabela seguinte mostra os métodos de acesso recomendada para cada tipo de cluster:

|Carga de trabalho|Cenário|Método de acesso|
|--------|--------|-------------|
|Apache Hadoop|Hive – tarefas/consultas interativas  |<ul><li>[Beeline](#beeline)</li><li>[Vista do Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Consultas interativas do tarefas /, interativo do PySpark|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin com o Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Vista do Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Cenários do batch – envio de Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interactive Query (LLAP)|Interativo|<ul><li>[Beeline](#beeline)</li><li>[Vista do Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Qualquer|Instalar aplicação personalizada|<ul><li>[Ações de script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter não está instalado/suporte no Enterprise Security Package.

Com as APIs padrão ajuda a partir da perspectiva de segurança. Além disso, obtém os seguintes benefícios:

1.  **Gestão** – pode gerir o seu código e automatizar tarefas usando APIs padrão – Livy, o HS2 etc.
2.  **Auditar** – com o SSH, não é possível auditar, os utilizadores que SSH tinha no cluster. Isso seria o caso quando as tarefas são construídas por meio de pontos finais padrão como iria ser executados no contexto do utilizador. 



### <a name="beeline"></a>Utilizar Beeline 
Instalar o Beeline no seu computador e ligar através da internet pública, utilize os seguintes parâmetros: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Se tiver o Beeline instalado localmente e ligar através de uma rede Virtual do Azure, utilize os seguintes parâmetros: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Para localizar o nome de domínio completamente qualificado de um nó principal, utilize as informações do HDInsight gerir com o documento da API REST do Ambari.














## <a name="users-of-hdinsight-clusters-with-esp"></a>Utilizadores de clusters do HDInsight com ESP
Um cluster do HDInsight não ESP tem duas contas de utilizador que são criadas durante a criação de cluster:

* **Administrador de Ambari**: Esta conta também é conhecido como *utilizador do Hadoop* ou *utilizador HTTP*. Esta conta pode ser utilizada para iniciar sessão no Ambari em https://&lt;clustername >. azurehdinsight.net. Ele também pode ser usado para executar consultas em vistas do Ambari, executar tarefas através de ferramentas externas (por exemplo, PowerShell, Templeton, Visual Studio) e autenticar com o controlador ODBC do Hive e as ferramentas de BI (por exemplo, Excel, Power BI ou Tableau).

Um cluster do HDInsight com ESP tem três novos usuários, além do administrador do Ambari.

* **Ranger admin**:  Esta conta é a conta de administrador local do Apache Ranger. Não é um utilizador de domínio do Active Directory. Esta conta pode ser utilizada para configurar políticas e fazer a outros utilizadores administradores ou administradores delegados (para que os utilizadores podem gerir políticas). Por predefinição, é o nome de utilizador *administrador* e a palavra-passe é o mesmo que a palavra-passe de administrador do Ambari. A palavra-passe pode ser atualizada a partir da página de definições no Ranger.
* **Utilizador de domínio de administrador de cluster**: Esta conta é um utilizador de domínio do Active Directory designado como o administrador de cluster Hadoop, incluindo Ambari e Ranger. Tem de fornecer credenciais deste utilizador durante a criação do cluster. Este utilizador tem os seguintes privilégios:

  * Associar máquinas ao domínio e colocá-las na UO que especificou durante a criação do cluster.
  * Crie principais de serviço na UO que especificou durante a criação do cluster.
  * Crie entradas de DNS reverso.

    Tenha em atenção de que os outros utilizadores do AD também tem estes privilégios.

    Existem alguns pontos finais dentro do cluster (por exemplo, Templeton) que não são geridos pelo Ranger e, por conseguinte, não são seguras. Estes pontos finais estão bloqueados para todos os utilizadores, exceto o utilizador de domínio de administrador de cluster.
* **Regular**: Durante a criação do cluster, pode fornecer vários grupos do Active Directory. Os utilizadores nestes grupos são sincronizados para o Ranger e Ambari. Esses usuários são usuários de domínio e tem acesso a apenas gerido Ranger pontos finais (por exemplo, Hiveserver2). Todas as políticas RBAC e auditoria vão ser aplicáveis a estes utilizadores.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Funções de clusters do HDInsight com ESP
HDInsight Enterprise Security Package tem as seguintes funções:

* Administrador de cluster
* Operador de cluster
* Administrador de Serviços
* Operador de serviço
* Utilizador do cluster

**Para ver as permissões destas funções**

1. Abra a gestão de Ambari da interface do Usuário.  Ver [abrir o Ambari de gerenciamento da interface do Usuário](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **funções**.
3. Clique no ponto de interrogação azul para ver as permissões:

    ![Permissões de funções de HDInsight ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abra a IU de gestão do Ambari

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o seu cluster do HDInsight.
3. Clique em **Dashboard** no menu superior para abrir o Ambari.
4. Inicie sessão com o nome de utilizador de domínio de administrador de cluster e a palavra-passe do Ambari.
5. Clique nas **administrador** menu suspenso da parte superior com o botão direito canto e, em seguida, clique em **Ambari gerir**.

    ![Gerir o HDInsight ESP Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A interface do Usuário é semelhante a:

    ![IU de gestão do ESP HDInsight Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Listar os utilizadores de domínio sincronizados a partir do Active Directory
1. Abra a gestão de Ambari da interface do Usuário.  Ver [abrir o Ambari de gerenciamento da interface do Usuário](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **utilizadores**. Deverá ver todos os utilizadores sincronizados a partir do Active Directory para o cluster do HDInsight.

    ![Utilizadores de lista de interface do Usuário de gestão de ESP HDInsight Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Listar os grupos de domínio sincronizados a partir do Active Directory
1. Abra a gestão de Ambari da interface do Usuário.  Ver [abrir o Ambari de gerenciamento da interface do Usuário](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **grupos**. Deverá ver todos os grupos sincronizados a partir do Active Directory para o cluster do HDInsight.

    ![Grupos de lista de interface do Usuário de gestão de ESP HDInsight Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configurar permissões de vistas do Hive
1. Abra a gestão de Ambari da interface do Usuário.  Ver [abrir o Ambari de gerenciamento da interface do Usuário](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **vistas**.
3. Clique em **HIVE** para mostrar os detalhes.

    ![Gestão de ESP HDInsight Ambari vistas do Hive de interface do Usuário](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Clique nas **vista do Hive** ligação para configurar as vistas do Hive.
5. Desloque para baixo para o **permissões** secção.

    ![Configurar o ESP HDInsight Ambari da interface do Usuário do Hive vistas de gestão de permissões](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Clique em **adicionar utilizador** ou **adicionar grupo**e, em seguida, especifique os utilizadores ou grupos que podem utilizar as vistas do Hive.

## <a name="configure-users-for-the-roles"></a>Configurar utilizadores para as funções
 Para ver uma lista de funções e as respetivas permissões, consulte os clusters de funções do HDInsight com o ESP.

1. Abra a gestão de Ambari da interface do Usuário.  Ver [abrir o Ambari de gerenciamento da interface do Usuário](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **funções**.
3. Clique em **adicionar utilizador** ou **adicionar grupo** para atribuir utilizadores e grupos a funções diferentes.

## <a name="next-steps"></a>Passos Seguintes
* Para configurar um cluster do HDInsight com o Enterprise Security Package, veja [clusters do HDInsight configurar com ESP](apache-domain-joined-configure.md).
* Para configurar políticas do Hive e executar consultas do Hive, veja [clusters do Apache Hive configurar políticas para o HDInsight com ESP](apache-domain-joined-run-hive.md).
