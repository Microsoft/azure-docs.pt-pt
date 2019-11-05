---
title: Sincronizar Azure Active Directory usuários com o cluster HDInsight
description: Sincronizar usuários autenticados do Azure Active Directory para um cluster HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b6252e99e69f849e2e988819f38dcccc5a7a73e0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498147"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Sincronizar utilizadores do Azure Active Directory num cluster do HDInsight

Os [clusters HDInsight com Enterprise Security Package (ESP)](hdinsight-domain-joined-introduction.md) podem usar a autenticação forte com usuários do Azure Active Directory (AD do Azure), bem como usar políticas de RBAC ( *controle de acesso baseado em função* ). Ao adicionar usuários e grupos ao Azure AD, você pode sincronizar os usuários que precisam de acesso ao cluster.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tiver feito isso, [crie um cluster HDInsight com Enterprise Security Package](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Adicionar novos usuários do Azure AD

Para exibir seus hosts, abra a interface do usuário do amAmbari Web. Cada nó será atualizado com novas configurações de atualização autônoma.

1. Na [portal do Azure](https://portal.azure.com), navegue até o diretório do Azure ad associado ao seu cluster ESP.

2. Selecione **todos os usuários** no menu à esquerda e, em seguida, selecione **novo usuário**.

    ![portal do Azure todos os usuários e grupos](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Preencha o formulário novo usuário. Selecione os grupos que você criou para atribuir permissões baseadas em cluster. Neste exemplo, crie um grupo chamado "HiveUsers", ao qual você pode atribuir novos usuários. As [instruções de exemplo](hdinsight-domain-joined-configure.md) para a criação de um cluster ESP incluem a adição de dois grupos, `HiveUsers` e `AAD DC Administrators`.

    ![Painel do usuário do portal do Azure selecionar grupos](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Selecione **Criar**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Usar a API REST do Apache Ambari para sincronizar usuários

Os grupos de usuários especificados durante o processo de criação de cluster são sincronizados nesse momento. A sincronização de usuário ocorre automaticamente uma vez a cada hora. Para sincronizar os usuários imediatamente ou para sincronizar um grupo diferente dos grupos especificados durante a criação do cluster, use a API REST do amAmbari.

O método a seguir usa POST com a API REST do amAmbari. Para obter mais informações, consulte [gerenciar clusters HDInsight usando a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Conecte-se ao cluster com o ssh](hdinsight-hadoop-linux-use-ssh-unix.md). No painel Visão geral do cluster na portal do Azure, selecione o botão **Secure Shell (SSH)** .

    ![Ícone do Secure Shell do HDInsight (SSH)](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-secure-shell.png)

2. Copie o comando `ssh` exibido e cole-o em seu cliente SSH. Insira a senha de usuário do ssh quando solicitado.

3. Após a autenticação, digite o seguinte comando:

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    A resposta deve ser assim:

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. Para ver o status da sincronização, execute um novo comando `curl`:

    ```bash
    curl -u admin:<YOUR PASSWORD> https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```
    
    A resposta deve ser assim:
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
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

5. Esse resultado mostra que o status está **concluído**, um novo usuário foi criado e o usuário recebeu uma associação. Neste exemplo, o usuário é atribuído ao grupo de LDAP sincronizado "HiveUsers", já que o usuário foi adicionado ao mesmo grupo no Azure AD.

> [!NOTE]  
> O método anterior sincroniza apenas os grupos do Azure AD especificados na propriedade **Access User Group** das configurações de domínio durante a criação do cluster. Para obter mais informações, consulte [criar um cluster HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Verificar o usuário recém-adicionado do Azure AD

Abra a [interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md) para verificar se o novo usuário do Azure ad foi adicionado. Acesse a interface do usuário da Web do amAmbari navegando até **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Insira o nome de usuário e a senha do administrador de cluster.

1. No painel do Ambari, selecione **gerenciar Ambari** no menu **admin** .

    ![Painel do Apache Ambari gerenciar Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Selecione **usuários** no grupo de menu **usuário + gerenciamento de grupo** no lado esquerdo da página.

    ![Menu usuários e grupos do HDInsight](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. O novo usuário deve estar listado na tabela usuários. O tipo é definido como `LDAP` em vez de `Local`.

    ![Visão geral da página de usuários do HDInsight AAD](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Faça logon no Ambari como o novo usuário

Quando o novo usuário (ou qualquer outro usuário de domínio) fizer logon no Ambari, eles usarão seu nome de usuário e as credenciais de domínio completos do Azure AD.  Ambari exibe um alias de usuário, que é o nome de exibição do usuário no Azure AD. O novo usuário de exemplo tem o nome de usuário `hiveuser3@contoso.com`. No Ambari, esse novo usuário aparece como `hiveuser3`, mas o usuário faz logon no Ambari como `hiveuser3@contoso.com`.

## <a name="see-also"></a>Consultar também

* [Configurar políticas de Apache Hive no HDInsight com ESP](hdinsight-domain-joined-run-hive.md)
* [Gerenciar clusters HDInsight com o ESP](hdinsight-domain-joined-manage.md)
* [Autorizar usuários a Apache Ambari](hdinsight-authorize-users-to-ambari.md)
