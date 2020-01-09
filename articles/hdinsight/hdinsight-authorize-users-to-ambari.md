---
title: Autorizar usuários para exibições Ambari-Azure HDInsight
description: Como gerenciar permissões de usuário e grupo Ambari para clusters HDInsight com ESP habilitado.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 3bb58334e96bf5378fb78b70125f9c7994a7c2fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435651"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autorizar utilizadores para as Vistas do Apache Ambari

Os [clusters HDInsight habilitados para Enterprise Security Package (ESP)](./domain-joined/hdinsight-security-overview.md) fornecem recursos de nível empresarial, incluindo a autenticação baseada em Azure Active Directory. Você pode [sincronizar novos usuários](hdinsight-sync-aad-users-to-cluster.md) adicionados aos grupos do Azure AD que receberam acesso ao cluster, permitindo que esses usuários específicos executem determinadas ações. O trabalho com usuários, grupos e permissões no [Apache Ambari](https://ambari.apache.org/) é compatível com clusters HDINSIGHT do ESP e clusters hdinsight padrão.

Active Directory os usuários podem entrar nos nós do cluster usando suas credenciais de domínio. Eles também podem usar suas credenciais de domínio para autenticar interações de cluster com outros pontos de extremidade aprovados, como [matiz](https://gethue.com/), Ambari views, ODBC, JDBC, PowerShell e APIs REST.

> [!WARNING]  
> Não altere a senha do Watchdog do Ambari (hdinsightwatchdog) em seu cluster HDInsight baseado em Linux. A alteração da senha interrompe a capacidade de usar ações de script ou executar operações de dimensionamento com o cluster.

Se você ainda não tiver feito isso, siga [estas instruções](./domain-joined/apache-domain-joined-configure.md) para provisionar um novo cluster ESP.

## <a name="access-the-ambari-management-page"></a>Acessar a página de gerenciamento do Ambari

Para acessar a **página de gerenciamento do Ambari** na [interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md), navegue até `https://CLUSTERNAME.azurehdinsight.net`. Insira o nome de usuário e a senha do administrador de cluster que você definiu ao criar o cluster. Em seguida, no painel do Ambari, selecione **gerenciar Ambari** abaixo do menu **admin** :

![Gerenciar o painel do Apache Ambari](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>Adicionar utilizadores

### <a name="add-users-through-the-portal"></a>Adicionar usuários por meio do portal

1. Na página Gerenciamento, selecione **usuários**.

    ![Usuários da página de gerenciamento do Apache Ambari](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. Selecione **+ criar usuário local**.

1. Forneça o **nome de usuário** e a **senha**. Selecione **salvar**.

### <a name="add-users-through-powershell"></a>Adicionar usuários por meio do PowerShell

Edite as variáveis abaixo, substituindo `CLUSTERNAME`, `NEWUSER`e `PASSWORD` pelos valores apropriados.

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>Adicionar usuários por meio da ondulação

Edite as variáveis abaixo, substituindo `CLUSTERNAME`, `ADMINPASSWORD`, `NEWUSER`e `USERPASSWORD` pelos valores apropriados. O script foi projetado para ser executado com o bash. Pequenas modificações seriam necessárias para um prompt de comando do Windows.

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>Conceder permissões para Apache Hive exibições

O Ambari vem com instâncias de exibição para [Apache Hive](https://hive.apache.org/) e [Apache tez](https://tez.apache.org/), entre outras. Para conceder acesso a uma ou mais instâncias de exibição do hive, vá para a **página de gerenciamento do Ambari**.

1. Na página Gerenciamento, selecione o link **exibições** no título do menu **exibições** à esquerda.

    ![Links de exibição de exibições do Apache Ambari](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. Na página exibições, expanda a linha do **Hive** . Há uma exibição padrão do hive que é criada quando o serviço do hive é adicionado ao cluster. Você também pode criar mais instâncias de exibição do hive, conforme necessário. Selecione uma exibição do hive:

    ![Exibições do HDInsight-exibição de Apache Hive](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Role para a parte inferior da página de exibição. Na seção *permissões* , você tem duas opções para conceder aos usuários de domínio suas permissões para a exibição:

**Conceder permissão a esses usuários** ![conceder permissão a esses usuários](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Conceda permissão a esses grupos** ![conceder permissão a esses grupos](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Para adicionar um usuário, selecione o botão **Adicionar usuário** .

   * Comece a digitar o nome de usuário e você verá uma lista suspensa de nomes definidos anteriormente.

     ![Preenchimento automático do usuário do Apache Ambari](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Selecione ou termine de digitar, o nome de usuário. Para adicionar esse nome de usuário como um novo usuário, selecione o botão **novo** .

   * Para salvar as alterações, marque a **caixa de seleção azul**.

     ![Permissões de usuário de concessão do Apache Ambari](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Para adicionar um grupo, selecione o botão **Adicionar grupo** .

   * Comece a digitar o nome do grupo. O processo de selecionar um nome de grupo existente ou adicionar um novo grupo é o mesmo para adicionar usuários.
   * Para salvar as alterações, marque a **caixa de seleção azul**.

     ![Permissões de concessão do Apache Ambari](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Adicionar usuários diretamente a uma exibição é útil quando você deseja atribuir permissões a um usuário para usar essa exibição, mas não deseja que eles sejam membros de um grupo que tenha permissões adicionais. Para reduzir a quantidade de sobrecarga administrativa, pode ser mais simples atribuir permissões a grupos.

## <a name="grant-permissions-to-apache-tez-views"></a>Conceder permissões para exibições do Apache TEZ

As instâncias de exibição do [Apache tez](https://tez.apache.org/) permitem que os usuários monitorem e depurem todos os trabalhos do tez, enviados por [Apache Hive](https://hive.apache.org/) consultas e scripts do [Apache Pig](https://pig.apache.org/) . Há uma instância de exibição de tez padrão que é criada quando o cluster é provisionado.

Para atribuir usuários e grupos a uma instância de exibição do tez, expanda a linha **tez** na página exibições, conforme descrito anteriormente.

![Exibições do HDInsight-exibição de Apache Tez](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Para adicionar usuários ou grupos, repita as etapas de 3-5 na seção anterior.

## <a name="assign-users-to-roles"></a>Atribuir usuários a funções

Há cinco funções de segurança para usuários e grupos, listadas em ordem de redução das permissões de acesso:

* Administrador de cluster
* Operador de cluster
* Administrador de Serviços
* Operador de serviço
* Usuário do cluster

Para gerenciar funções, vá para a **página de gerenciamento do Ambari**e, em seguida, selecione o link **funções** no grupo de menus *clusters* à esquerda.

![Links do menu de funções do Apache Ambari](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Para ver a lista de permissões dadas a cada função, clique no ponto de interrogação azul ao lado do cabeçalho da tabela **funções** na página funções.

![Permissões de link do menu funções do Apache Ambari](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Permissões de link do menu funções do Apache Ambari")

Nessa página, há duas exibições diferentes que você pode usar para gerenciar funções para usuários e grupos: bloquear e listar.

### <a name="block-view"></a>Exibição de bloco

O modo de exibição de bloco exibe cada função em sua própria linha e fornece as opções **atribuir funções a esses usuários** e **atribuir funções a essas grupos** , conforme descrito anteriormente.

![Exibição de bloco de funções do Apache Ambari](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>Vista de lista

A exibição de lista fornece recursos de edição rápida em duas categorias: usuários e grupos.

* A categoria usuários da exibição de lista exibe uma lista de todos os usuários, permitindo que você selecione uma função para cada usuário na lista suspensa.

    ![Exibição de lista de funções do Apache Ambari-usuários](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* A categoria grupos da exibição de lista exibe todos os grupos e a função atribuída a cada grupo. Em nosso exemplo, a lista de grupos é sincronizada a partir dos grupos do Azure AD especificados na propriedade **Access User Group** das configurações de domínio do cluster. Consulte [criar um cluster HDInsight com ESP habilitado](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).

    ![Exibição da lista de funções do Apache Ambari – grupos](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na imagem acima, o grupo "hiveusers" é atribuído à função de *usuário de cluster* . Essa é uma função somente leitura que permite que os usuários desse grupo exibam, mas não alterem as configurações de serviço e as métricas de cluster.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Faça logon no Ambari como um usuário somente de exibição

Atribuímos as permissões "hiveuser1" do usuário de domínio do Azure AD às exibições Hive e tez. Quando iniciamos a interface do usuário da Web do amAmbari e inserem as credenciais de domínio do usuário (nome de usuário do Azure AD no formato de email e senha), o usuário é redirecionado para a página de exibições do Ambari. A partir daqui, o usuário pode selecionar qualquer exibição acessível. O usuário não pode visitar nenhuma outra parte do site, incluindo o painel, os serviços, os hosts, os alertas ou as páginas de administrador.

![Usuário Apache Ambari somente com exibições](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Faça logon no Ambari como um usuário de cluster

Atribuímos o usuário de domínio do Azure AD "hiveuser2" à função de *usuário de cluster* . Essa função é capaz de acessar o painel e todos os itens de menu. Um usuário de cluster tem menos opções permitidas do que um administrador. Por exemplo, hiveuser2 pode exibir configurações para cada um dos serviços, mas não pode editá-los.

![Exibição do painel do Apache Ambari](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Passos seguintes

* [Configurar políticas de Apache Hive no HDInsight com ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Gerenciar clusters do HDInsight do ESP](./domain-joined/apache-domain-joined-manage.md)
* [Usar a exibição de Apache Hive com Apache Hadoop no HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Sincronizar os usuários do Azure AD com o cluster](hdinsight-sync-aad-users-to-cluster.md)
* [Gerenciar clusters HDInsight usando a API REST do Apache Ambari](./hdinsight-hadoop-manage-ambari-rest-api.md)
