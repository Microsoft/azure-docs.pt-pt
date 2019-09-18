---
title: Gerenciar clusters HDInsight com o Enterprise Security Enterprise-Azure
description: Saiba como gerenciar clusters do Azure HDInsight com o Enterprise Security Package.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: b98c62908885bc13cd5f473967cc70709af693d2
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034108"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Gerenciar clusters HDInsight com Enterprise Security Package
Aprenda os usuários e as funções no Enterprise Security Package do HDInsight (ESP) e como gerenciar clusters ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Utilizar o VSCode para ligar a um cluster associado a um domínio

Você pode vincular um cluster normal usando o nome de usuário gerenciado do Apache Ambari, além de vincular um cluster de Apache Hadoop de segurança usando o `user1@contoso.com`nome de usuário do domínio (como:).

1. Abra a paleta de comandos selecionando **Ctrl + Shift + P**e, em **seguida, insira HDInsight: Vincular um cluster**.

   ![paleta de comandos, vincular um cluster](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Insira a URL do cluster HDInsight-> entrada nome de usuário-> senha de entrada-> selecione tipo de cluster-> ele mostra informações de êxito se a verificação for aprovada.

   ![caixa de diálogo Vincular etapas do processo do cluster](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > O nome de usuário e a senha vinculados serão usados se o cluster fizer logon na assinatura do Azure e vinculado a um cluster.

3. Você pode ver um cluster vinculado usando o **cluster de lista**de comandos. Agora você pode enviar um script para esse cluster vinculado.

   ![listar verificação de saída de comando de cluster](./media/apache-domain-joined-manage/hdinsight-linked-cluster.png "cluster vinculado")

4. Você também pode desvincular um cluster ao inserir **o HDInsight: Desvincular um cluster** da paleta de comandos.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Utilizar o IntelliJ para ligar a um cluster associado a um domínio

Você pode vincular um cluster normal usando o nome de usuário gerenciado Ambari, também vincular um cluster Hadoop de segurança usando o nome de usuário `user1@contoso.com`do domínio (como:).

1. Clique em **vincular um cluster** do **Azure Explorer**.

   ![menu de contexto do cluster de link IntelliJ](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Insira o **nome do cluster**, o nome de **usuário** e a **senha**. Você precisa verificar o nome de usuário e a senha se obtiver a falha de autenticação. Opcionalmente, adicione conta de armazenamento, chave de armazenamento e, em seguida, selecione um contêiner do contêiner de armazenamento. As informações de armazenamento são para o Gerenciador de armazenamento na árvore à esquerda

   ![Caixa de diálogo do cluster de link do Azure Explorer IntelliJ](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > Usamos a chave de armazenamento vinculada, o nome de usuário e a senha se o cluster fizer logon na assinatura do Azure e vinculado a um cluster.
   > 
   > ![Conta de armazenamento do Azure Explorer no IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

3. Você poderá ver um cluster vinculado no nó do **HDInsight** se as informações de entrada estiverem corretas. Agora você pode enviar um aplicativo para esse cluster vinculado.

   ![IntelliJ de cluster vinculado do Azure Explorer](./media/apache-domain-joined-manage/linked-cluster-intellij.png "cluster vinculado IntelliJ]")

4. Você também pode desvincular um cluster do **Azure Explorer**.

   ![IntelliJ de cluster desvinculado do Azure Explorer](./media/apache-domain-joined-manage/hdinsight-unlink-cluster.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Utilizar o Eclipse para ligar a um cluster associado a um domínio

Você pode vincular um cluster normal usando o nome de usuário gerenciado Ambari, também vincular um cluster Hadoop de segurança usando o nome de usuário `user1@contoso.com`do domínio (como:).

1. Clique em **vincular um cluster** do **Azure Explorer**.

   ![menu de contexto do cluster de link Eclipse](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Insira o **nome do cluster**, o nome de **usuário** e a **senha**e clique no botão OK para vincular o cluster. Opcionalmente, insira a conta de armazenamento, a chave de armazenamento e, em seguida, selecione contêiner de armazenamento para o Gerenciador de armazenamento funcionar no modo de exibição de árvore à esquerda

   ![Caixa de diálogo Vincular cluster do Azure Explorer](./media/apache-domain-joined-manage/link-cluster-dialog1.png)

   > [!NOTE]  
   > Usamos a chave de armazenamento vinculada, o nome de usuário e a senha se o cluster fizer logon na assinatura do Azure e vinculado a um cluster.
   > 
   > ![Conta de armazenamento do Azure Explorer no Eclipse](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Você poderá ver um cluster vinculado no nó **HDInsight** depois de clicar no botão OK, se as informações de entrada estiverem corretas. Agora você pode enviar um aplicativo para esse cluster vinculado.

   ![Eclipse do cluster vinculado do Azure Explorer](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Você também pode desvincular um cluster do **Azure Explorer**.
   
   ![Eclipse do cluster desvinculado do Azure Explorer](./media/apache-domain-joined-manage/hdinsight-unlink-cluster.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Acesse os clusters com Enterprise Security Package.

Enterprise Security Package (anteriormente conhecido como HDInsight Premium) fornece acesso de vários usuários ao cluster, em que a autenticação é feita por Active Directory e autorização pelo Apache Ranger e ACLs de armazenamento (ACLs de ADLS). A autorização fornece limites seguros entre vários usuários e permite que apenas usuários com privilégios tenham acesso aos dados com base nas políticas de autorização.

Segurança e isolamento de usuário são importantes para um cluster HDInsight com Enterprise Security Package. Para atender a esses requisitos, o acesso do SSH ao cluster com Enterprise Security Package está bloqueado. A tabela a seguir mostra os métodos de acesso recomendados para cada tipo de cluster:

|Carga de trabalho|Cenário|Método de acesso|
|--------|--------|-------------|
|Apache Hadoop|Hive – trabalhos/consultas interativas  |<ul><li>[Beeline](#beeline)</li><li>[Exibição do hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Trabalhos/consultas interativos, PySpark Interactive|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin com Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Exibição do hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Cenários de lote – envio do Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Consulta interativa (LLAP)|Interativo|<ul><li>[Beeline](#beeline)</li><li>[Exibição do hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Any|Instalar aplicativo personalizado|<ul><li>[Ações de script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter não está instalado/tem suporte no Enterprise Security Package.

O uso das APIs padrão ajuda da perspectiva de segurança. Além disso, você obtém os seguintes benefícios:

- **Gerenciamento** – você pode gerenciar seu código e automatizar trabalhos usando APIs padrão – LIVY, HS2, etc.
- **Auditoria** – com SSH, não há nenhuma maneira de auditar, que os usuários SSH para o cluster. Esse não seria o caso quando os trabalhos são construídos por meio de pontos de extremidade padrão, pois eles seriam executados no contexto do usuário. 



### <a name="beeline"></a>Usar beeline 
Instale o beeline em seu computador e conecte-se pela Internet pública, use os seguintes parâmetros: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Se você tiver o beeline instalado localmente e se conectar por meio de uma rede virtual do Azure, use os seguintes parâmetros: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Para localizar o nome de domínio totalmente qualificado de um cabeçalho, use as informações no documento gerenciar o HDInsight usando a API REST do amAmbari.














## <a name="users-of-hdinsight-clusters-with-esp"></a>Usuários de clusters HDInsight com ESP
Um cluster HDInsight não ESP tem duas contas de usuário que são criadas durante a criação do cluster:

* **Administrador do Ambari**: Essa conta também é conhecida como *usuário do Hadoop* ou *usuário http*. Essa conta pode ser usada para entrar em Ambari em https://&lt;ClusterName >. azurehdinsight. net. Ele também pode ser usado para executar consultas em exibições do Ambari, executar trabalhos por meio de ferramentas externas (por exemplo, PowerShell, Templeton, Visual Studio) e autenticar com o driver ODBC do hive e as ferramentas de BI (por exemplo, Excel, Power BI ou tableau).

Um cluster HDInsight com ESP tem três novos usuários, além do administrador do Ambari.

* **Administrador do Ranger**:  Essa conta é a conta de administrador local do Apache Ranger. Ele não é um usuário de domínio do Active Directory. Essa conta pode ser usada para configurar políticas e fazer com que outros usuários administradores ou administradores delegados (para que esses usuários possam gerenciar políticas). Por padrão, o nome de usuário é *admin* e a senha é a mesma que a senha de administrador do Ambari. A senha pode ser atualizada na página configurações no Ranger.
* **Usuário do domínio do administrador do cluster**: Essa conta é um usuário de domínio do Active Directory designado como o administrador do cluster Hadoop, incluindo Ambari e ranger. Você deve fornecer as credenciais deste usuário durante a criação do cluster. Este usuário tem os seguintes privilégios:

  * Ingresse computadores no domínio e coloque-os dentro da UO que você especificar durante a criação do cluster.
  * Crie entidades de serviço dentro da UO que você especificar durante a criação do cluster.
  * Criar entradas DNS reversas.

    Observe que os outros usuários do AD também têm esses privilégios.

    Há alguns pontos de extremidade dentro do cluster (por exemplo, Templeton) que não são gerenciados pelo Ranger e, portanto, não são seguros. Esses pontos de extremidade são bloqueados para todos os usuários, exceto o usuário de domínio de administrador do cluster.
* **Regular**: Durante a criação do cluster, você pode fornecer vários grupos do Active Directory. Os usuários nesses grupos são sincronizados com o Ranger e o Ambari. Esses usuários são usuários de domínio e têm acesso somente a pontos de extremidade gerenciados pelo Ranger (por exemplo, Hiveserver2). Todas as políticas e auditoria RBAC serão aplicáveis a esses usuários.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Funções de clusters HDInsight com ESP
O HDInsight Enterprise Security Package tem as seguintes funções:

* Administrador de cluster
* Operador de cluster
* Administrador de Serviços
* Operador de serviço
* Usuário do cluster

**Para ver as permissões dessas funções**

1. Abra a interface do usuário de gerenciamento do Ambari.  Consulte [abrir a interface do usuário de gerenciamento do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **funções**.
3. Clique no ponto de interrogação azul para ver as permissões:

    ![Permissões de funções do do HDInsight de ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abrir a interface do usuário de gerenciamento do Ambari

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight.
3. Clique em **painel** no menu superior para abrir o Ambari.
4. Entre no Ambari usando o nome de usuário e a senha do domínio do administrador de cluster.
5. Clique no menu suspenso **admin** no canto superior direito e, em seguida, clique em **gerenciar Ambari**.

    ![Gerenciar Apache Ambari do ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A interface do usuário é semelhante a:

    ![Interface do usuário de gerenciamento do Apache HDInsight Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Listar os usuários de domínio sincronizados do seu Active Directory
1. Abra a interface do usuário de gerenciamento do Ambari.  Consulte [abrir a interface do usuário de gerenciamento do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **usuários**. Você deverá ver todos os usuários sincronizados do seu Active Directory para o cluster HDInsight.

    ![Interface do usuário de gerenciamento do Ambari do HDInsight para usuários da lista](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Listar os grupos de domínio sincronizados do seu Active Directory
1. Abra a interface do usuário de gerenciamento do Ambari.  Consulte [abrir a interface do usuário de gerenciamento do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **grupos**. Você deverá ver todos os grupos sincronizados de seu Active Directory para o cluster HDInsight.

    ![Grupos de lista de interface do usuário do gerenciamento Ambari do HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configurar permissões de exibições do hive
1. Abra a interface do usuário de gerenciamento do Ambari.  Consulte [abrir a interface do usuário de gerenciamento do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **exibições**.
3. Clique em **Hive** para mostrar os detalhes.

    ![Exibições do hive da interface do usuário do gerenciamento Ambari do HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Clique no link **exibição do hive** para configurar exibições do hive.
5. Role para baixo até a seção **permissões** .

    ![Exibições do hive da interface do usuário do gerenciamento Ambari do HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Clique em **Adicionar usuário** ou **Adicionar grupo**e, em seguida, especifique os usuários ou grupos que podem usar exibições do hive.

## <a name="configure-users-for-the-roles"></a>Configurar usuários para as funções
 Para ver uma lista de funções e suas permissões, consulte funções de clusters HDInsight com ESP.

1. Abra a interface do usuário de gerenciamento do Ambari.  Consulte [abrir a interface do usuário de gerenciamento do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, clique em **funções**.
3. Clique em **Adicionar usuário** ou **Adicionar grupo** para atribuir usuários e grupos a diferentes funções.

## <a name="next-steps"></a>Passos Seguintes
* Para configurar um cluster HDInsight com Enterprise Security Package, consulte [Configurar clusters hdinsight com ESP](apache-domain-joined-configure.md).
* Para configurar políticas do hive e executar consultas Hive, consulte [Configurar políticas de Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
