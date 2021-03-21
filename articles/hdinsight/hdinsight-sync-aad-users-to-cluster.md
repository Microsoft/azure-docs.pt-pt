---
title: Sincronizar os utilizadores do Azure Ative Directory para o cluster HDInsight
description: Sincronizar os utilizadores autenticados do Azure Ative Directory para um cluster HDInsight.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: a3307c5d677028c17717c0d591790f0373f131d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931634"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Sincronizar utilizadores do Azure Active Directory num cluster do HDInsight

[Os clusters HDInsight com Pacote de Segurança Empresarial (ESP)](./domain-joined/hdinsight-security-overview.md) podem usar autenticação forte com utilizadores do Azure Ative Directory (Azure AD), bem como usar políticas *de controlo de acesso baseado em funções Azure (Azure RBAC).* À medida que adiciona utilizadores e grupos ao Azure AD, pode sincronizar os utilizadores que precisam de acesso ao seu cluster.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não o fez, [crie um cluster HDInsight com o Pacote de Segurança Empresarial.](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

## <a name="add-new-azure-ad-users"></a>Adicionar novos utilizadores AD Azure

Para ver os seus anfitriões, abra a UI da Web Ambari. Cada nó será atualizado com novas definições de atualização não acompanhadas.

1. A partir do [portal Azure,](https://portal.azure.com)navegue até ao diretório AD Azure associado ao seu cluster ESP.

2. Selecione **Todos os utilizadores** do menu à esquerda e, em seguida, selecione **Novo utilizador**.

    ![Utilizadores e grupos do portal Azure](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Preencha o novo formulário de utilizador. Selecione os grupos que criou para atribuir permissões baseadas em clusters. Neste exemplo, crie um grupo chamado "HiveUsers", ao qual pode atribuir novos utilizadores. As [instruções](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) de exemplo para a criação de um cluster ESP incluem a adição de dois grupos `HiveUsers` e `AAD DC Administrators` .

    ![Painel de utilizadores do portal Azure seleciona grupos](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Selecione **Criar**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Utilize a API Apache Ambari REST para sincronizar os utilizadores

Os grupos de utilizadores especificados durante o processo de criação de clusters são sincronizados nesse momento. A sincronização de utilizadores ocorre automaticamente uma vez por hora. Para sincronizar imediatamente os utilizadores, ou para sincronizar um grupo diferente dos grupos especificados durante a criação do cluster, utilize a API Ambari REST.

O seguinte método utiliza POST com a API Ambari REST. Para obter mais informações, consulte [gerir os clusters HDInsight utilizando a API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Utilize [o comando ssh](hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo `CLUSTERNAME` pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Após autenticação, insira o seguinte comando:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    A resposta deverá ter o seguinte aspeto:

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

    A resposta deverá ter o seguinte aspeto:

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

1. Este resultado mostra que o estado é **COMPLETO**, um novo utilizador foi criado, e o utilizador foi atribuído a uma adesão. Neste exemplo, o utilizador é atribuído ao grupo LDAP sincronizado "HiveUsers", uma vez que o utilizador foi adicionado ao mesmo grupo em Azure AD.

    > [!NOTE]  
    > O método anterior apenas sincroniza os grupos AZure AD especificados na propriedade do grupo de **utilizadores Access** das definições de domínio durante a criação do cluster. Para obter mais informações, consulte  [criar um cluster HDInsight](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Verifique o recém-adicionado utilizador AZure AD

Abra o [Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) para verificar se o novo utilizador AD Azure foi adicionado. Aceda ao UI web Ambari navegando para **`https://CLUSTERNAME.azurehdinsight.net`** . Introduza o nome de utilizador e a palavra-passe do administrador do cluster.

1. A partir do painel Ambari, **selecione Manage Ambari** no menu **de administração.**

    ![Painel Apache Ambari Gerencie Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Selecione **Utilizadores** no grupo de **menus User + Group Management** no lado esquerdo da página.

    ![Menu de utilizadores e grupos HDInsight](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. O novo utilizador deve ser listado dentro da tabela Utilizadores. O Tipo está definido para `LDAP` e não  `Local` .

    ![Visão geral da página dos utilizadores do HDInsight aad](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Faça login em Ambari como o novo utilizador

Quando o novo utilizador (ou qualquer outro utilizador de domínio) entra em Ambari, utiliza o seu nome de utilizador completo Azure AD e credenciais de domínio.  Ambari exibe um pseudónimo do utilizador, que é o nome de exibição do utilizador em Azure AD.
O novo utilizador de exemplo tem o nome de `hiveuser3@contoso.com` utilizador. Em Ambari, este novo utilizador aparece como `hiveuser3` mas o utilizador entra em Ambari como `hiveuser3@contoso.com` .

## <a name="see-also"></a>Ver também

* [Configure as políticas da Colmeia Apache em HDInsight com ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Gerir clusters HDInsight com ESP](./domain-joined/apache-domain-joined-manage.md)
* [Autorizar utilizadores a Apache Ambari](hdinsight-authorize-users-to-ambari.md)