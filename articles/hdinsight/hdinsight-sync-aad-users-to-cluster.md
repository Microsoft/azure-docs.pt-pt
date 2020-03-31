---
title: Synchronize utilizadores de Diretório Ativo Azure para cluster HDInsight
description: Sincronizar utilizadores autenticados do Diretório Ativo Azure para um cluster HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 299d242c38152db6a471159d1f3d2803598c1832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744857"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Sincronizar utilizadores do Azure Active Directory num cluster do HDInsight

Os [clusters HDInsight com pacote de segurança empresarial (ESP)](hdinsight-domain-joined-introduction.md) podem usar autenticação forte com utilizadores do Azure Ative Directory (Azure AD), bem como utilizar políticas *de controlo de acesso baseados em papéis* (RBAC). À medida que adiciona utilizadores e grupos ao Azure AD, pode sincronizar os utilizadores que precisam de acesso ao seu cluster.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não o fez, [crie um cluster HDInsight com pacote](hdinsight-domain-joined-configure.md)de segurança empresarial .

## <a name="add-new-azure-ad-users"></a>Adicione novos utilizadores de Anúncios Azure

Para ver os seus anfitriões, abra a Ambari Web UI. Cada nó será atualizado com novas definições de upgrade não atendidas.

1. A partir do [portal Azure,](https://portal.azure.com)navegue até ao diretório Azure AD associado ao seu cluster ESP.

2. **Selecione todos os utilizadores** do menu à esquerda e, em seguida, selecione Novo **utilizador**.

    ![Utilizadores e grupos do portal Azure](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Complete o novo formulário de utilizador. Selecione grupos criados para atribuir permissões baseadas em clusters. Neste exemplo, crie um grupo chamado "HiveUsers", ao qual pode atribuir novos utilizadores. As [instruções de exemplo](hdinsight-domain-joined-configure.md) para a criação `HiveUsers` `AAD DC Administrators`de um cluster ESP incluem a adição de dois grupos, e .

    ![Grupos selecionados pelo utilizador do portal Azure](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Selecione **Criar**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Utilize a API Apache Ambari REST para sincronizar os utilizadores

Os grupos de utilizadores especificados durante o processo de criação de clusters são sincronizados nesse momento. A sincronização de utilizadores ocorre automaticamente uma vez por hora. Para sincronizar imediatamente os utilizadores, ou para sincronizar um grupo diferente dos grupos especificados durante a criação do cluster, utilize a API Ambari REST.

O método seguinte utiliza post com a Ambari REST API. Para mais informações, consulte [Gerir os clusters HDInsight utilizando a API De Repouso Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Utilize [o comando ssh](hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Edite o comando `CLUSTERNAME` abaixo substituindo pelo nome do seu cluster e, em seguida, introduza o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Depois de autenticar, insira o seguinte comando:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    A resposta deve ser assim:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. Para ver o estado de sincronização, execute um novo `curl` comando:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    A resposta deve ser assim:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Este resultado mostra que o estado é **COMPLETO**, foi criado um novo utilizador e foi atribuído ao utilizador uma adesão. Neste exemplo, o utilizador é atribuído ao grupo LDAP sincronizado "HiveUsers", uma vez que o utilizador foi adicionado a esse mesmo grupo em Azure AD.

    > [!NOTE]  
    > O método anterior apenas sincroniza os grupos AD Azure especificados na propriedade do grupo de **utilizadores Access** das definições de domínio durante a criação do cluster. Para mais informações, consulte [criar um cluster HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Verifique o recém-adicionado utilizador da AD Azure

Abra a [Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) para verificar se o novo utilizador da AD Azure foi adicionado. Aceda ao Ambari Web UI navegando até **`https://CLUSTERNAME.azurehdinsight.net`**. Introduza o nome de utilizador e a palavra-passe do administrador do cluster.

1. A partir do painel ambari, selecione **Manage Ambari** sob o menu **de administração.**

    ![Painel Apache Ambari Gere ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Selecione **Utilizadores** no grupo de **menus User + Group Management** no lado esquerdo da página.

    ![Menu de utilizadores e grupos HDInsight](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. O novo utilizador deve ser listado na tabela Utilizadores. O Tipo está `LDAP` definido `Local`em vez de .

    ![Visão geral da página dos utilizadores de anúncios HDInsight](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Inicie sessão em Ambari como o novo utilizador

Quando o novo utilizador (ou qualquer outro utilizador de domínio) inicia sessão na Ambari, utilizam o nome completo do utilizador da AD Azure e credenciais de domínio.  Ambari exibe um pseudónimo de utilizador, que é o nome de exibição do utilizador em Azure AD.
O novo utilizador de `hiveuser3@contoso.com`exemplo tem o nome de utilizador . Em Ambari, este novo `hiveuser3` utilizador aparece como mas o `hiveuser3@contoso.com`utilizador entra em Ambari como .

## <a name="see-also"></a>Consulte também

* [Configure as políticas da Hive Apache no HDInsight com esp](hdinsight-domain-joined-run-hive.md)
* [Gerir clusters HDInsight com ESP](hdinsight-domain-joined-manage.md)
* [Autorizar utilizadores a Apache Ambari](hdinsight-authorize-users-to-ambari.md)
