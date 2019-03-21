---
title: Autorizar utilizadores para vistas do Ambari - Azure HDInsight
description: Como gerir as permissões de utilizador e grupo Ambari para HDInsight clusters com ESP ativado.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: cf001d86356f4dd5fd3735803f0e329aa1e0940d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224027"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autorizar utilizadores para as Vistas do Apache Ambari

[Pacote de segurança da empresa (ESP) ativada clusters do HDInsight](./domain-joined/apache-domain-joined-introduction.md) fornecem capacidades de nível empresarial, incluindo autenticação baseada no Azure Active Directory. Pode [sincronizar novos utilizadores](hdinsight-sync-aad-users-to-cluster.md) adicionados a grupos do Azure AD que receberam acesso ao cluster, a permitir que esses utilizadores específicos executar determinadas ações. Trabalhar com os utilizadores, grupos e permissões no [Apache Ambari](https://ambari.apache.org/) é suportada para clusters do HDInsight ESP tanto clusters do HDInsight standard.

Active Directory utilizadores podem iniciar sessão para os nós do cluster através das respetivas credenciais de domínio. Também pode utilizar as credenciais de domínio para autenticar as interações de cluster com os outros pontos finais aprovados, como [Hue](https://gethue.com/), as vistas do Ambari, ODBC, JDBC, PowerShell e REST APIs.

> [!WARNING]  
> Não altere a palavra-passe de watchdog o Ambari (hdinsightwatchdog) no seu cluster do HDInsight baseado em Linux. Alterar a palavra-passe quebra a capacidade de utilizar as ações de script ou efetuar operações de dimensionamento com o seu cluster.

Se ainda não o fez, siga [estas instruções](./domain-joined/apache-domain-joined-configure.md) para aprovisionar um novo cluster de ESP.

## <a name="access-the-ambari-management-page"></a>Aceder à página de gestão do Ambari

Para obter o **página de gerenciamento do Ambari** no [Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md), navegue até **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Introduza o nome de utilizador de administrador de cluster e a palavra-passe que definiu quando criou o cluster. Em seguida, a partir do dashboard do Ambari, selecione **gerir Ambari** por baixo da **administrador** menu:

![Gerir Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Conceder permissões a vistas de Apache Hive

Ambari vem com instâncias de exibição para [Apache Hive](https://hive.apache.org/) e [Apache TEZ](https://tez.apache.org/), entre outros. Para conceder acesso a uma ou mais instâncias de vista do Hive, vá para o **página de gerenciamento do Ambari**.

1. Na página de gestão, selecione o **vistas** ligação sob o **vistas** título do menu à esquerda.

    ![Ligação de vistas](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. Na página de vistas, expanda o **HIVE** linha. Existe uma vista de ramo de registo predefinido que é criada quando o serviço de Hive é adicionado ao cluster. Também pode criar mais instâncias de vista do Hive conforme necessário. Selecione uma vista do Hive:

    ![Vistas - vista do Hive](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Desloque-se na direção da parte inferior da página de exibição. Sob o *permissões* secção, tem duas opções para a concessão as respetivas permissões para a vista de utilizadores de domínio:

**Conceder permissão a estes utilizadores** ![conceder permissão a estes utilizadores](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Conceder permissão a estes grupos** ![conceder permissão a estes grupos](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

1. Para adicionar um utilizador, selecione o **adicionar utilizador** botão.

   * Começar a escrever o nome de utilizador e verá uma lista suspensa de nomes definidos anteriormente.

     ![Autocompletes de utilizador](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

   * Selecione ou terminar de escrever o nome de utilizador. Para adicionar este nome de utilizador como um novo utilizador, selecione o **New** botão.

   * Para guardar as alterações, selecione o **caixa de seleção azul**.

     ![Utilizador introduzida](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

1. Para adicionar um grupo, selecione o **adicionar grupo** botão.

   * Comece a escrever o nome do grupo. O processo de selecionar um nome de grupo existente ou adicionar um novo grupo, é igual à adição de utilizadores.
   * Para guardar as alterações, selecione o **caixa de seleção azul**.

     ![Grupo introduzido](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Adicionar utilizadores diretamente para uma vista é útil quando deseja atribuir permissões a um utilizador para utilizar essa exibição, mas não pretende ser um membro de um grupo que tenha permissões adicionais. Para reduzir a quantidade de sobrecarga administrativa, talvez seja mais simples atribuir permissões a grupos.

## <a name="grant-permissions-to-apache-tez-views"></a>Conceder permissões a vistas de Apache TEZ

O [Apache TEZ](https://tez.apache.org/) ver instâncias permitirá aos utilizadores monitorizar e depurar todas as tarefas do Tez, submetidas pelo [Apache Hive](https://hive.apache.org/) consultas e [Apache Pig](https://pig.apache.org/) scripts. Existe um padrão de instância de vista do Tez que foi criado quando o cluster for aprovisionado.

Para atribuir utilizadores e grupos a uma instância de vista do Tez, expanda o **TEZ** linha na página de modos de exibição, conforme descrito anteriormente.

![Vistas - modo de exibição do Tez](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Para adicionar utilizadores ou grupos, repita os passos 3 a 5, na secção anterior.

## <a name="assign-users-to-roles"></a>Atribuir utilizadores a funções

Existem cinco funções de segurança para utilizadores e grupos, listados por ordem de diminuir as permissões de acesso:

* Administrador de cluster
* Operador de cluster
* Administrador de Serviços
* Operador de serviço
* Utilizador do cluster

Para gerir funções, vá para o **página de gerenciamento do Ambari**, em seguida, selecione a **funções** ligação dentro o *Clusters* grupo de menu à esquerda.

![Ligação de menu de funções](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Para ver a lista de permissões concedidas a cada função, clique no ponto de interrogação azul junto a **funções** cabeçalho de tabela na página de funções.

![Ligação de menu de funções](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Nesta página, existem dois modos de exibição diferentes, que pode utilizar para gerir funções de utilizadores e grupos: Bloco e de lista.

### <a name="block-view"></a>Vista de bloco

A vista de bloco apresenta cada função na sua própria linha e fornece a **atribuir funções a esses usuários** e **atribuir funções a estes grupos** opções conforme descrito anteriormente.

![Funções de bloquear a vista](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Vista de lista

A vista de lista fornece capacidades de edição rápidas em duas categorias: Os utilizadores e grupos.

* A categoria de utilizadores da exibição de lista apresenta uma lista de todos os utilizadores, permitindo-lhe selecionar uma função para cada utilizador na lista pendente.

    ![Vista - os utilizadores de lista de funções](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  A categoria de grupos da exibição de lista apresenta todos os grupos e a função atribuída a cada grupo. No nosso exemplo, a lista de grupos é sincronizada a partir de grupos do Azure AD especificados na **grupo de utilizadores de acesso** propriedade das definições de domínio do cluster. Ver [criar um cluster do HDInsight com ESP ativado](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Vista - grupos de lista de funções](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na imagem acima, o grupo de "hiveusers" é atribuído a *utilizador de Cluster* função. Essa é uma função só de leitura que permite que os utilizadores desse grupo para ver, mas não alterar configurações de serviço e métricas de cluster.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Inicie sessão no Ambari como um utilizador só de visualização

Atribuído nosso utilizador de domínio do Azure AD "hiveuser1" permissões às vistas do Hive e Tez. Quando iniciar a IU Web do Ambari e introduza as credenciais de domínio deste utilizador (nome de utilizador do Azure AD no formato de correio eletrónico e palavra-passe), o utilizador é redirecionado para a página de vistas do Ambari. Aqui, o utilizador pode selecionar qualquer vista acessível. O utilizador não é possível visitar qualquer outra parte do site, incluindo as páginas de dashboard, serviços, anfitriões, alertas ou administrador.

![Utilizador com apenas vistas](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Inicie sessão no Ambari como um utilizador do cluster

Atribuído nosso utilizador de domínio do Azure AD "hiveuser2" para o *utilizador de Cluster* função. Esta função é capaz de acessar o dashboard e todos os itens de menu. Um utilizador do cluster tem menos opções permitidas que um administrador. Por exemplo, hiveuser2 pode ver as configurações para cada um dos serviços, mas não é possível editá-los.

![Utilizador com função de utilizador do Cluster](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Passos Seguintes

* [Configurar políticas de Apache Hive no HDInsight com ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Gerir clusters do HDInsight ESP](./domain-joined/apache-domain-joined-manage.md)
* [Utilizar o modo de exibição do Apache Hive com o Apache Hadoop no HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Sincronizar utilizadores do Azure AD para o cluster](hdinsight-sync-aad-users-to-cluster.md)
