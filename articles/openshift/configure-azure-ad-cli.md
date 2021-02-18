---
title: Azure Red Hat OpenShift executando OpenShift 4 - Configuração Autenticação do Diretório Ativo Azure utilizando a linha de comando
description: Saiba como configurar a autenticação do Azure Ative Directory para um cluster Azure Red Hat OpenShift que executa o OpenShift 4 utilizando a linha de comando
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, chapéu vermelho, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0e9e7c5fccf56077e138ffe24226344257bb3a13
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636293"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Configurar a autenticação do Diretório Ativo Azure para um cluster Azure Red Hat OpenShift 4 (CLI)

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest).

Recupere os seus URLs específicos do cluster que serão utilizados para configurar a aplicação do Diretório Ativo Azure.

Desaprote as variáveis para o grupo de recursos e o nome do cluster.

**\<resource_group>** Substitua-o pelo nome do seu grupo de recursos e **\<aro_cluster>** pelo nome do seu cluster.

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

Construa o URL de retorno de OAuth do cluster e guarde-o numa **variável oauthCallbackURL**. 

> [!NOTE]
> A `AAD` secção no URL de retorno de chamadas OAuth deve corresponder ao nome do fornecedor de identidade OAuth que irá configurar mais tarde.


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

O formato do oauthCallbackURL é ligeiramente diferente com domínios personalizados:

* Executar o seguinte comando se estiver a utilizar um domínio personalizado, por `contoso.com` exemplo. 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* Se não estiver a utilizar um domínio personalizado, o `$domain` serão uma cadeia alnum de oito caracteres e é estendido por `$location.aroapp.io` .

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> A `AAD` secção no URL de retorno de chamadas OAuth deve corresponder ao nome do fornecedor de identidade OAuth que irá configurar mais tarde.

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Criar um pedido de Diretório Ativo Azure para autenticação

Substitua **\<client_secret>** por uma senha segura para a aplicação.

```azurecli-interactive
client_secret=<client_secret>
```

Crie uma aplicação Azure Ative Directory e recupere o identificador de aplicações criado.

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

Recupere a identificação do inquilino da assinatura que detém a aplicação.

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Crie um ficheiro manifesto para definir as alegações opcionais a incluir no ID Token

Os desenvolvedores de aplicações podem usar [reclamações opcionais](../active-directory/develop/active-directory-optional-claims.md) nas suas aplicações AZure AD para especificar quais as alegações que pretendem em fichas enviadas para a sua aplicação.

Pode utilizar reclamações opcionais para:

- Selecione reclamações adicionais para incluir em fichas para a sua aplicação.
- Alterar o comportamento de certas alegações de que a Azure AD regressa em fichas.
- Adicione e aceda reclamações personalizadas para a sua aplicação.

Configuraremos o OpenShift para usar a `email` reclamação e recuar `upn` para definir o nome de utilizador preferido adicionando o como parte do `upn` token de ID devolvido pelo Azure Ative Directory.

Crie uma **manifest.jsem** ficheiro para configurar o pedido de Diretório Ativo Azure.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Atualizar as opções da aplicação Azure Ative Directory com um manifesto

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Atualizar as permissões de âmbito de aplicação do Azure Ative Directory

Para podermos ler as informações do utilizador a partir do Azure Ative Directory, precisamos definir os âmbitos adequados.

Adicione permissão para o **Azure Ative Directory Graph.User.Read** scope para permitir o sessão e ler o perfil do utilizador.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> Pode ignorar com segurança a mensagem para conceder o consentimento, a menos que seja autenticado como Administrador Global para este Diretório Ativo Azure. Os utilizadores de domínio padrão serão convidados a conceder o consentimento quando iniciarem o login no cluster usando as suas credenciais AAD.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Atribuir utilizadores e grupos ao cluster (opcional)

As aplicações registadas num inquilino do Azure Ative Directory (Azure AD) estão, por defeito, disponíveis para todos os utilizadores do arrendatário que autenticam com sucesso. A Azure AD permite que administradores e desenvolvedores de inquilinos restringam uma app a um conjunto específico de utilizadores ou grupos de segurança no inquilino.

Siga as instruções da documentação do Azure Ative Directory para [atribuir utilizadores e grupos à aplicação](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Configure a autenticação OpenShift OpenID

Recupere as `kubeadmin` credenciais. Executar o seguinte comando para encontrar a senha para o `kubeadmin` utilizador.

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

Inicie sessão no servidor API do cluster OpenShift utilizando o seguinte comando. 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

Crie um segredo OpenShift para armazenar o segredo da aplicação Azure Ative Directory.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

Crie um ficheiro **oidc.yaml** para configurar a autenticação OpenShift OpenID contra o Azure Ative Directory. 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

Aplique a configuração no cluster.

```azurecli-interactive
oc apply -f oidc.yaml
```

Receberá uma resposta semelhante à seguinte.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Verifique o login através do Azure Ative Directory

Se agora tiver o início da consola Web OpenShift e tentar fazer login novamente, será-lhe apresentada uma nova opção para iniciar sessão com **a AAD**. Talvez precise esperar por alguns minutos.

![Faça login no ecrã com opção Azure Ative Directory](media/aro4-login-2.png)
