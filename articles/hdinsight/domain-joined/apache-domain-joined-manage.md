---
title: Gerenciar clusters de Enterprise Security Package-Azure HDInsight
description: Saiba como gerenciar clusters do Azure HDInsight com o Enterprise Security Package.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 93880269edd72477f3aa85b2dbdc9d9f3ec8ef25
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435897"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Gerenciar clusters HDInsight com Enterprise Security Package

Aprenda os usuários e as funções no Enterprise Security Package do HDInsight (ESP) e como gerenciar clusters ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Utilizar o VSCode para ligar a um cluster associado a um domínio

Você pode vincular um cluster normal usando o nome de usuário gerenciado do Apache Ambari, além de vincular um cluster de Apache Hadoop de segurança usando o nome de usuário do domínio (como: `user1@contoso.com`).

1. Abra [Visual Studio Code](https://code.visualstudio.com/). Verifique se a extensão de [Ferramentas do hive do Spark &](../hdinsight-for-vscode.md) está instalada.

1. Siga as etapas em [vincular um cluster](../hdinsight-for-vscode.md#link-a-cluster) para Visual Studio Code.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Utilizar o IntelliJ para ligar a um cluster associado a um domínio

Você pode vincular um cluster normal usando o nome de usuário gerenciado Ambari, também vincular um cluster Hadoop de segurança usando o nome de usuário do domínio (como: `user1@contoso.com`).

1. Abra o IntelliJ IDEA. Verifique se todos os [pré-requisitos](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) foram atendidos.

1. Siga as etapas em [vincular um cluster](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) para IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Utilizar o Eclipse para ligar a um cluster associado a um domínio

Você pode vincular um cluster normal usando o nome de usuário gerenciado Ambari, também vincular um cluster Hadoop de segurança usando o nome de usuário do domínio (como: `user1@contoso.com`).

1. Abra o Eclipse. Verifique se todos os [pré-requisitos](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) foram atendidos.

1. Siga as etapas em [vincular um cluster para o](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) eclipse.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Acessar os clusters com Enterprise Security Package

Enterprise Security Package (anteriormente conhecido como HDInsight Premium) fornece acesso de vários usuários ao cluster, em que a autenticação é feita por Active Directory e autorização pelo Apache Ranger e ACLs de armazenamento (ACLs de ADLS). A autorização fornece limites seguros entre vários usuários e permite que apenas usuários com privilégios tenham acesso aos dados com base nas políticas de autorização.

Segurança e isolamento de usuário são importantes para um cluster HDInsight com Enterprise Security Package. Para atender a esses requisitos, o acesso do SSH ao cluster com Enterprise Security Package está bloqueado. A tabela a seguir mostra os métodos de acesso recomendados para cada tipo de cluster:

|Carga de trabalho|Cenário|Método de Acesso|
|--------|--------|-------------|
|Apache Hadoop|Hive – trabalhos/consultas interativas  |<ul><li>[Beeline](#beeline)</li><li>[Exibição do hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Trabalhos/consultas interativos, PySpark Interactive|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin com Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Exibição do hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Cenários de lote – envio do Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Consulta interativa (LLAP)|Interativo|<ul><li>[Beeline](#beeline)</li><li>[Exibição do hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Qualquer|Instalar aplicativo personalizado|<ul><li>[Ações de script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter não está instalado/tem suporte no Enterprise Security Package.

O uso das APIs padrão ajuda da perspectiva de segurança. Você também obtém os seguintes benefícios:

- **Gerenciamento** – você pode gerenciar seu código e automatizar trabalhos usando APIs padrão – LIVY, HS2, etc.
- **Auditoria** – com o ssh, não há como auditar, que os usuários SSH para o cluster. Esse não seria o caso quando os trabalhos são construídos por meio de pontos de extremidade padrão, pois eles seriam executados no contexto do usuário.

### <a name="beeline"></a>Usar beeline

Instale o beeline em seu computador e conecte-se pela Internet pública, use os seguintes parâmetros:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Se você tiver o beeline instalado localmente e se conectar por meio de uma rede virtual do Azure, use os seguintes parâmetros:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Para localizar o nome de domínio totalmente qualificado de um cabeçalho, use as informações no documento gerenciar o HDInsight usando a API REST do amAmbari.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Usuários de clusters HDInsight com ESP

Um cluster HDInsight não ESP tem duas contas de usuário que são criadas durante a criação do cluster:

- **Administrador do Ambari**: essa conta também é conhecida como *usuário do Hadoop* ou *usuário http*. Essa conta pode ser usada para entrar no Ambari em `https://CLUSTERNAME.azurehdinsight.net`. Ele também pode ser usado para executar consultas em exibições do Ambari, executar trabalhos por meio de ferramentas externas (por exemplo, PowerShell, Templeton, Visual Studio) e autenticar com o driver ODBC do hive e as ferramentas de BI (por exemplo, Excel, Power BI ou tableau).

Um cluster HDInsight com ESP tem três novos usuários, além do administrador do Ambari.

- **Administrador do Ranger**: essa conta é a conta de administrador local do Apache Ranger. Não é um usuário de domínio do Active Directory. Essa conta pode ser usada para configurar políticas e fazer com que outros usuários administradores ou administradores delegados (para que esses usuários possam gerenciar políticas). Por padrão, o nome de usuário é *admin* e a senha é a mesma que a senha de administrador do Ambari. A senha pode ser atualizada na página configurações no Ranger.

- **Usuário de domínio do administrador do cluster**: essa conta é um usuário de domínio do Active Directory designado como o administrador do cluster Hadoop, incluindo Ambari e ranger. Você deve fornecer as credenciais deste usuário durante a criação do cluster. Este usuário tem os seguintes privilégios:
    - Ingresse computadores no domínio e coloque-os dentro da UO que você especificar durante a criação do cluster.
    - Crie entidades de serviço dentro da UO que você especificar durante a criação do cluster.
    - Criar entradas DNS reversas.

    Observe que os outros usuários do AD também têm esses privilégios.

    Há alguns pontos de extremidade dentro do cluster (por exemplo, Templeton) que não são gerenciados pelo Ranger e, portanto, não são seguros. Esses pontos de extremidade são bloqueados para todos os usuários, exceto o usuário de domínio de administrador do cluster.

- **Regular**: durante a criação do cluster, você pode fornecer vários grupos do Active Directory. Os usuários nesses grupos são sincronizados com o Ranger e o Ambari. Esses usuários são usuários de domínio e têm acesso somente a pontos de extremidade gerenciados pelo Ranger (por exemplo, Hiveserver2). Todas as políticas e auditoria RBAC serão aplicáveis a esses usuários.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Funções de clusters HDInsight com ESP

O HDInsight Enterprise Security Package tem as seguintes funções:

- Administrador de cluster
- Operador de cluster
- Administrador de Serviços
- Operador de serviço
- Usuário do cluster

**Para ver as permissões dessas funções**

1. Abra a interface do usuário de gerenciamento do Ambari.  Consulte [abrir a interface do usuário de gerenciamento do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, selecione **funções**.
3. Selecione o ponto de interrogação azul para ver as permissões:

    ![Permissões de funções do do HDInsight de ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abrir a interface do usuário de gerenciamento do Ambari

1. Navegue até `https://CLUSTERNAME.azurehdinsight.net/` em que CLUSTERname é o nome do cluster.
1. Entre no Ambari usando o nome de usuário e a senha do domínio do administrador de cluster.
1. Selecione o menu suspenso **admin** no canto superior direito e, em seguida, selecione **gerenciar Ambari**.

    ![Gerenciar Apache Ambari do ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A interface do usuário é semelhante a:

    ![Interface do usuário de gerenciamento do Apache HDInsight Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Listar os usuários de domínio sincronizados do seu Active Directory

1. Abra a interface do usuário de gerenciamento do Ambari.  Consulte [abrir a interface do usuário de gerenciamento do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, selecione **usuários**. Você deverá ver todos os usuários sincronizados do seu Active Directory para o cluster HDInsight.

    ![Interface do usuário de gerenciamento do Ambari do HDInsight para usuários da lista](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Listar os grupos de domínio sincronizados do seu Active Directory

1. Abra a interface do usuário de gerenciamento do Ambari.  Consulte [abrir a interface do usuário de gerenciamento do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, selecione **grupos**. Você deverá ver todos os grupos sincronizados de seu Active Directory para o cluster HDInsight.

    ![Grupos de lista de interface do usuário do gerenciamento Ambari do HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configurar permissões de exibições do hive

1. Abra a interface do usuário de gerenciamento do Ambari.  Consulte [abrir a interface do usuário de gerenciamento do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, selecione **exibições**.
3. Selecione **Hive** para mostrar os detalhes.

    ![Exibições do hive da interface do usuário do gerenciamento Ambari do HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Selecione o link **exibição do hive** para configurar exibições do hive.
5. Role para baixo até a seção **permissões** .

    ![Exibições do hive da interface do usuário do gerenciamento Ambari do HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Selecione **Adicionar usuário** ou **Adicionar grupo**e, em seguida, especifique os usuários ou grupos que podem usar exibições do hive.

## <a name="configure-users-for-the-roles"></a>Configurar usuários para as funções

 Para ver uma lista de funções e suas permissões, consulte funções de clusters HDInsight com ESP.

1. Abra a interface do usuário de gerenciamento do Ambari.  Consulte [abrir a interface do usuário de gerenciamento do Ambari](#open-the-ambari-management-ui).
2. No menu à esquerda, selecione **funções**.
3. Selecione **Adicionar usuário** ou **Adicionar grupo** para atribuir usuários e grupos a diferentes funções.

## <a name="next-steps"></a>Passos seguintes

- Para configurar um cluster HDInsight com Enterprise Security Package, consulte [Configurar clusters hdinsight com ESP](apache-domain-joined-configure.md).
- Para configurar políticas do hive e executar consultas Hive, consulte [Configurar políticas de Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
