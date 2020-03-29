---
title: Gerir clusters de pacotes de pacotes de segurança empresarial - Azure HDInsight
description: Saiba como gerir os clusters Azure HDInsight com o Enterprise Security Package.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 93880269edd72477f3aa85b2dbdc9d9f3ec8ef25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435897"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Gerir clusters HDInsight com pacote de segurança empresarial

Conheça os utilizadores e as funções no HDInsight Enterprise Security Package (ESP) e como gerir os clusters ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Utilizar o VSCode para ligar a um cluster associado a um domínio

Pode ligar um cluster normal utilizando o nome de utilizador gerido por Apache Ambari, ligando também `user1@contoso.com`um cluster De segurança Apache Hadoop utilizando o nome de utilizador de domínio (como: ).

1. Código de [estúdio visual](https://code.visualstudio.com/)aberto . Certifique-se de que a extensão [Spark & Hive Tools](../hdinsight-for-vscode.md) está instalada.

1. Siga os passos de [Link um cluster](../hdinsight-for-vscode.md#link-a-cluster) para Visual Studio Code.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Utilizar o IntelliJ para ligar a um cluster associado a um domínio

Pode ligar um cluster normal utilizando o nome de utilizador gerido por Ambari, ligando também `user1@contoso.com`um cluster de hadoop de segurança utilizando o nome de utilizador do domínio (como: ).

1. Abra o IntelliJ IDEA. Certifique-se de que todos os [pré-requisitos são cumpridos.](../spark/apache-spark-intellij-tool-plugin.md#prerequisites)

1. Siga os passos de [Link um cluster](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) para IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Utilizar o Eclipse para ligar a um cluster associado a um domínio

Pode ligar um cluster normal utilizando o nome de utilizador gerido por Ambari, ligando também `user1@contoso.com`um cluster de hadoop de segurança utilizando o nome de utilizador do domínio (como: ).

1. Abra o Eclipse. Certifique-se de que todos os [pré-requisitos são cumpridos.](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites)

1. Siga os passos de [Link um cluster](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) para Eclipse.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Aceda aos clusters com pacote de segurança empresarial

O Pacote de Segurança Empresarial (anteriormente conhecido como HDInsight Premium) fornece acesso multiutilizador ao cluster, onde a autenticação é feita por Ative Directory e autorização por Apache Ranger e Storage ACLs (ADLS ACLs). A autorização fornece limites seguros entre vários utilizadores e permite que apenas utilizadores privilegiados tenham acesso aos dados com base nas políticas de autorização.

A segurança e o isolamento do utilizador são importantes para um cluster HDInsight com pacote de segurança empresarial. Para satisfazer estes requisitos, o acesso sSH ao cluster com pacote de segurança empresarial está bloqueado. O quadro seguinte mostra os métodos de acesso recomendados para cada tipo de cluster:

|Carga de trabalho|Cenário|Método de Acesso|
|--------|--------|-------------|
|Apache Hadoop|Colmeia – Empregos Interativos/Consultas  |<ul><li>[Beeline](#beeline)</li><li>[Vista do Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas de estúdio visual](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Empregos Interativos/Consultas, PySpark interativo|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin com Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Vista do Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas de estúdio visual](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Cenários de Lote – Spark submit, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Consulta Interativa (LLAP)|Interativo|<ul><li>[Beeline](#beeline)</li><li>[Vista do Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas de estúdio visual](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Qualquer|Instalar aplicação personalizada|<ul><li>[Ações de Script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter não é instalado/suportado no Pacote de Segurança Empresarial.

A utilização das APIs padrão ajuda do ponto de vista da segurança. Obtém também os seguintes benefícios:

- **Gestão** – Pode gerir o seu código e automatizar trabalhos utilizando APIs padrão – Livy, HS2 etc.
- **Auditoria** – Com o SSH, não há forma de auditoria, que os utilizadores sSH'd para o cluster. Isto não aconteceria quando os postos de trabalho fossem construídos através de pontos finais padrão, uma vez que seriam executados em contexto de utilizador.

### <a name="use-beeline"></a><a name="beeline"></a>Utilizar Beeline

Instale beeline na sua máquina e ligue-se através da internet pública, utilize os seguintes parâmetros:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Se tiver a Beeline instalada localmente e ligar-se a uma Rede Virtual Azure, utilize os seguintes parâmetros:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Para encontrar o nome de domínio totalmente qualificado de um headnode, utilize a informação no Manage HDInsight utilizando o documento Ambari REST API.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Utilizadores de clusters HDInsight com ESP

Um cluster HDInsight não ESP tem duas contas de utilizador que são criadas durante a criação do cluster:

- **Administração Ambari**: Esta conta também é conhecida como *utilizador Hadoop* ou *utilizador HTTP*. Esta conta pode ser usada para iniciar `https://CLUSTERNAME.azurehdinsight.net`sessão em Ambari em . Também pode ser usado para executar consultas em vistas de Ambari, executar trabalhos através de ferramentas externas (por exemplo, PowerShell, Templeton, Visual Studio), e autenticar com o condutor da Hive ODBC e ferramentas BI (por exemplo, Excel, Power BI ou Tableau).

Um cluster HDInsight com ESP tem três novos utilizadores, além da Ambari Admin.

- **Administrador a ranger:** Esta conta é a conta de administração local do Apache Ranger. Não é um utilizador de domínio de diretório ativo. Esta conta pode ser usada para configurar políticas e fazer com que outros utilizadores adminsem ou delegem administradores (para que esses utilizadores possam gerir as políticas). Por predefinição, o nome de utilizador é *administrador* e a palavra-passe é a mesma que a palavra-passe da Administração Ambari. A palavra-passe pode ser atualizada a partir da página Definições no Ranger.

- Utilizador de **domínio de administração**cluster : Esta conta é um utilizador de domínio de diretório ativo designado como administrador do cluster Hadoop, incluindo Ambari e Ranger. Deve fornecer as credenciais deste utilizador durante a criação do cluster. Este utilizador tem os seguintes privilégios:
    - Junte as máquinas ao domínio e coloque-as dentro da Ou que especifique durante a criação do cluster.
    - Crie diretores de serviço dentro da Ou que especifique durante a criação de clusters.
    - Crie entradas DNS inversas.

    Note que os outros utilizadores de AD também têm estes privilégios.

    Existem alguns pontos finais dentro do aglomerado (por exemplo, Templeton) que não são geridos pelo Ranger, e portanto não são seguros. Estes pontos finais são bloqueados para todos os utilizadores, exceto o utilizador do domínio de administração do cluster.

- **Regular**: Durante a criação do cluster, pode fornecer vários grupos de diretórios ativos. Os utilizadores destes grupos estão sincronizados com Ranger e Ambari. Estes utilizadores são utilizadores de domínio e têm acesso apenas a pontos finais geridos pelo Ranger (por exemplo, Hiveserver2). Todas as políticas e auditorias rBAC serão aplicáveis a estes utilizadores.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Funções de clusters HDInsight com ESP

O Pacote de Segurança Empresarial HDInsight tem as seguintes funções:

- Administrador de Cluster
- Operador de Cluster
- Administrador de Serviços
- Operador de Serviço
- Utilizador de Cluster

**Para ver as permissões destes papéis**

1. Abra a Ambari Management UI.  Ver [Aberto a Ambari Management UI](#open-the-ambari-management-ui).
2. A partir do menu esquerdo, selecione **Funções**.
3. Selecione o ponto de interrogação azul para ver as permissões:

    ![Permissões de funções ESP HDInsight](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abra a Ambari Management UI

1. Navegue `https://CLUSTERNAME.azurehdinsight.net/` até onde clusterNAME é o nome do seu cluster.
1. Inscreva-se na Ambari utilizando o nome de utilizador do domínio do administrador do cluster e a palavra-passe.
1. Selecione o menu de entrega de **administrador** do canto superior direito e, em seguida, selecione **Manage Ambari**.

    ![ESP HDInsight gere Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A UI parece:

    ![ESP HDInsight Apache Ambari management UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Liste os utilizadores de domínio sincronizados a partir do seu Diretório Ativo

1. Abra a Ambari Management UI.  Ver [Aberto a Ambari Management UI](#open-the-ambari-management-ui).
2. A partir do menu esquerdo, selecione **Utilizadores**. Verá todos os utilizadores sincronizados desde o seu Diretório Ativo até ao cluster HDInsight.

    ![Esp HDInsight Ambari gestoru ui lista utilizadores](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Enumerar os grupos de domínio sincronizados a partir do seu Diretório Ativo

1. Abra a Ambari Management UI.  Ver [Aberto a Ambari Management UI](#open-the-ambari-management-ui).
2. A partir do menu esquerdo, selecione **Grupos**. Verá todos os grupos sincronizados desde o seu Diretório Ativo até ao cluster HDInsight.

    ![ESP HDInsight Ambari grupos de lista de gestão de UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configure as permissões de vistas da colmeia

1. Abra a Ambari Management UI.  Ver [Aberto a Ambari Management UI](#open-the-ambari-management-ui).
2. A partir do menu esquerdo, selecione **Views**.
3. **Selecione HIVE** para mostrar os detalhes.

    ![ESP HDInsight Ambari gestão UI Hive Views](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Selecione o link **Hive View** para configurar pontos de vista para a Colmeia.
5. Desça até a secção **de Permissões.**

    ![ESP HDInsight Ambari gestão UI Hive Views configuram permissões](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Selecione **Adicionar utilizador** ou **adicionar grupo,** e, em seguida, especificar os utilizadores ou grupos que podem usar pontos de vista da Colmeia.

## <a name="configure-users-for-the-roles"></a>Configure os utilizadores para as funções

 Para ver uma lista de papéis e suas permissões, consulte Roles of HDInsight clusters com ESP.

1. Abra a Ambari Management UI.  Ver [Aberto a Ambari Management UI](#open-the-ambari-management-ui).
2. A partir do menu esquerdo, selecione **Funções**.
3. Selecione **Adicionar User** ou **Adicionar Grupo** para atribuir utilizadores e grupos a diferentes funções.

## <a name="next-steps"></a>Passos seguintes

- Para configurar um cluster HDInsight com pacote de segurança empresarial, consulte [os clusters Configure HDInsight com ESP](apache-domain-joined-configure.md).
- Para configurar as políticas da Colmeia e executar consultas de Hive, consulte as políticas de [Configure Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
