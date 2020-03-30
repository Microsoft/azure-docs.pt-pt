---
title: Autorizar utilizadores de Ambari Views - Azure HDInsight
description: Como gerir as permissões de utilizador e grupo Ambari para clusters HDInsight com ESP ativado.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 3bb58334e96bf5378fb78b70125f9c7994a7c2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435651"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autorizar utilizadores para as Vistas do Apache Ambari

[Os clusters HDInsight ativados do Pacote de Segurança Empresarial (ESP) fornecem](./domain-joined/hdinsight-security-overview.md) capacidades de nível empresarial, incluindo a autenticação baseada no Diretório Ativo azure. Pode [sincronizar novos utilizadores](hdinsight-sync-aad-users-to-cluster.md) adicionados aos grupos AD Azure que tiveram acesso ao cluster, permitindo que esses utilizadores específicos realizem determinadas ações. Trabalhar com utilizadores, grupos e permissões em [Apache Ambari](https://ambari.apache.org/) é suportado tanto para clusters ESP HDInsight como para clusters HDInsight padrão.

Os utilizadores de Diretório ativo podem iniciar sessão nos nós do cluster utilizando as suas credenciais de domínio. Também podem usar as suas credenciais de domínio para autenticar interações de cluster com outros pontos finais aprovados como [Hue](https://gethue.com/), Ambari Views, ODBC, JDBC, PowerShell e REST APIs.

> [!WARNING]  
> Não altere a palavra-passe do cão de guarda Ambari (hdinsightwatchdog) no seu cluster HDInsight baseado em Linux. Alterar a palavra-passe quebra a capacidade de utilizar ações de script ou realizar operações de escala com o seu cluster.

Se ainda não o fez, siga [estas instruções](./domain-joined/apache-domain-joined-configure.md) para fornecer um novo cluster ESP.

## <a name="access-the-ambari-management-page"></a>Aceda à página de gestão de Ambari

Para chegar à página de gestão de **Ambari** no Apache `https://CLUSTERNAME.azurehdinsight.net` [Ambari Web UI,](hdinsight-hadoop-manage-ambari.md)navegue até . Introduza o nome de utilizador do administrador do cluster e a palavra-passe que definiu ao criar o cluster. Em seguida, a partir do painel ambari, **selecione Manage Ambari** por baixo do menu **de administração:**

![Gestão do dashboard Apache Ambari](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>Adicionar utilizadores

### <a name="add-users-through-the-portal"></a>Adicione utilizadores através do portal

1. A partir da página de gestão, selecione **Utilizadores**.

    ![Utilizadores da página de gestão Apache Ambari](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. Selecione **+ Criar o Utilizador Local**.

1. Fornecer **username** e **password**. Selecione **guardar**.

### <a name="add-users-through-powershell"></a>Adicionar utilizadores através do PowerShell

Editar as variáveis abaixo `CLUSTERNAME` `NEWUSER`substituindo, e `PASSWORD` com os valores apropriados.

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

### <a name="add-users-through-curl"></a>Adicione utilizadores através do Curl

Editar as variáveis abaixo `CLUSTERNAME` `ADMINPASSWORD`substituindo, e `NEWUSER` `USERPASSWORD` com os valores apropriados. O guião foi concebido para ser executado com uma festa. Seriam necessárias ligeiras modificações para um pedido de comando windows.

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

## <a name="grant-permissions-to-apache-hive-views"></a>Conceda permissões às vistas da Colmeia Apache

Ambari vem com exemplos de vista para [Apache Hive](https://hive.apache.org/) e [Apache TEZ,](https://tez.apache.org/)entre outros. Para dar acesso a uma ou mais instâncias de visão da Colmeia, aceda à página de gestão de **Ambari.**

1. Na página de gestão, selecione o link **Views** no menu **Views** em direção à esquerda.

    ![Apache Ambari vê links](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. Na página Views, expanda a linha **HIVE.** Há uma visão padrão da Colmeia que é criada quando o serviço hive é adicionado ao cluster. Você também pode criar mais casos de vista da Colmeia conforme necessário. Selecione uma vista para a Colmeia:

    ![Vistas HDInsight - Vista Apache Hive](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Percorra para a parte inferior da página Ver. De acordo com a secção *Permissões,* tem duas opções para conceder aos utilizadores de domínio as suas permissões à vista:

**Conceda permissão a estes utilizadores** ![Conceder permissão a estes utilizadores](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Conceda permissão a estes grupos** ![Conceder permissão a estes grupos](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Para adicionar um utilizador, selecione o botão **Adicionar Utilizador.**

   * Comece a digitar o nome do utilizador e verá uma lista de nomes previamente definidos.

     ![Apache Ambari utilizador auto completa](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Selecione ou termine a dactilografia, o nome do utilizador. Para adicionar este nome de utilizador como um novo utilizador, selecione o botão **Novo.**

   * Para guardar as suas alterações, selecione a **caixa de verificação azul**.

     ![Apache Ambari concede permissões ao utilizador](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Para adicionar um grupo, selecione o botão **Adicionar Grupo.**

   * Comece a escrever o nome do grupo. O processo de seleção de um nome de grupo existente, ou de adicionar um novo grupo, é o mesmo que para adicionar utilizadores.
   * Para guardar as suas alterações, selecione a **caixa de verificação azul**.

     ![Apache Ambari concede permissões](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Adicionar utilizadores diretamente a uma vista é útil quando pretende atribuir permissões a um utilizador para usar essa visão, mas não quer que seja um membro de um grupo que tenha permissões adicionais. Para reduzir a quantidade de despesas administrativas, pode ser mais simples atribuir permissões a grupos.

## <a name="grant-permissions-to-apache-tez-views"></a>Conceder permissões às vistas do Apache TEZ

As instâncias de visão [Apache TEZ](https://tez.apache.org/) permitem que os utilizadores monitorizem e depuram todos os empregos tez, apresentados por consultas [apache hive](https://hive.apache.org/) e scripts [Apache Pig.](https://pig.apache.org/) Há uma instância de visualização predefinida da Tez que é criada quando o cluster é provisionado.

Para atribuir utilizadores e grupos a uma instância de visualização Tez, expanda a linha **TEZ** na página Views, como descrito anteriormente.

![Vistas HDInsight - Vista Apache Tez](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Para adicionar utilizadores ou grupos, repita os passos 3 - 5 na secção anterior.

## <a name="assign-users-to-roles"></a>Atribuir utilizadores a funções

Existem cinco funções de segurança para utilizadores e grupos, listadas por ordem de diminuição das permissões de acesso:

* Administrador de Cluster
* Operador de Cluster
* Administrador de Serviços
* Operador de Serviço
* Utilizador de Cluster

Para gerir funções, vá à página de **gestão ambari**e, em seguida, selecione o link **Roles** dentro do grupo de menu *clusters* à esquerda.

![Links de menu de papéis Apache Ambari](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Para ver a lista de permissões dadas a cada função, clique no ponto de interrogação azul ao lado do cabeçalho da tabela **Roles** na página Roles.

![Apache Ambari roles menu link permissions](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Apache Ambari roles menu link permissions")

Nesta página, existem duas visões diferentes que pode utilizar para gerir funções para utilizadores e grupos: Bloquear e Lista.

### <a name="block-view"></a>Vista de bloco

A visão do Bloco exibe cada papel na sua própria linha, e fornece as **funções de Atribuição a estes utilizadores** e atribui **funções a estes grupos,** conforme descrito anteriormente.

![Apache Ambari papéis vista de bloco](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>Vista de lista

A visão da Lista fornece capacidades de edição rápida em duas categorias: Utilizadores e Grupos.

* A categoria Utilizadores da visão Lista apresenta uma lista de todos os utilizadores, permitindo-lhe selecionar uma função para cada utilizador na lista de dropdown.

    ![Visão da lista de papéis Apache Ambari - utilizadores](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* A categoria grupos da lista mostra todos os grupos, e o papel atribuído a cada grupo. No nosso exemplo, a lista de grupos é sincronizada a partir dos grupos AD Azure especificados na propriedade do grupo de **utilizadores Access** das definições de Domínio do cluster. Consulte [Criar um cluster HDInsight com ESP ativado](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).

    ![Apache Ambari vista lista de papéis - grupos](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na imagem acima, o grupo "hiveusers" é atribuído à função De Utilizador do *Cluster.* Esta é uma função apenas para leitura que permite aos utilizadores desse grupo visualizar, mas não alterar configurações de serviço e métricas de cluster.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Inicie sessão em Ambari como um utilizador só para visualização

Atribuímos permissões ao nosso utilizador de domínio Azure AD "hiveuser1" para vistas para a Hive e Tez. Quando lançamos o Ambari Web UI e introduzimos as credenciais de domínio deste utilizador (nome de utilizador DaA Azure em formato de e-mail e palavra-passe), o utilizador é redirecionado para a página Ambari Views. A partir daqui, o utilizador pode selecionar qualquer vista acessível. O utilizador não pode visitar qualquer outra parte do site, incluindo o dashboard, serviços, anfitriões, alertas ou páginas de administração.

![Utilizador Apache Ambari apenas com vistas](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Inicie sessão em Ambari como utilizador de cluster

Atribuímos o nosso utilizador de domínio Azure AD "hiveuser2" à função *de Utilizador* cluster. Esta função é capaz de aceder ao painel de instrumentos e a todos os itens de menu. Um utilizador de cluster tem menos opções permitidas do que um administrador. Por exemplo, o hiveuser2 pode ver configurações para cada um dos serviços, mas não pode editá-los.

![Ecrã do painel apache Ambari](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Passos seguintes

* [Configure as políticas da Hive Apache no HDInsight com esp](./domain-joined/apache-domain-joined-run-hive.md)
* [Gerir clusters ESP HDInsight](./domain-joined/apache-domain-joined-manage.md)
* [Use a vista da colmeia Apache com Apache Hadoop em HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchronize utilizadores de AD Azure para o cluster](hdinsight-sync-aad-users-to-cluster.md)
* [Gerir os clusters HDInsight utilizando a API De REPOUSO Apache Ambari](./hdinsight-hadoop-manage-ambari-rest-api.md)
