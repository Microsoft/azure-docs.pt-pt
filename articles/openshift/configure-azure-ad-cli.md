---
title: Azure Red Hat OpenShift executando OpenShift 4 - Configuração Autenticação do Diretório Ativo Azure utilizando a linha de comando
description: Saiba como configurar a autenticação do Azure Ative Directory para um cluster Azure Red Hat OpenShift que executa o OpenShift 4 utilizando a linha de comando
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, chapéu vermelho, cli
ms.custom: mvc
ms.openlocfilehash: fd6ea0749cce154ae20479bc54ef9b7374a69d0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89469427"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Configurar a autenticação do Diretório Ativo Azure para um cluster Azure Red Hat OpenShift 4 (CLI)

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest).

Recupere os seus URLs específicos do cluster que serão utilizados para configurar a aplicação do Diretório Ativo Azure.

Construa o URL de retorno de OAuth do cluster e guarde-o numa **variável oauthCallbackURL**. Certifique-se de que substitui **o aro-rg** pelo nome do seu grupo de recursos e **pelo aro-cluster** pelo nome do seu cluster.

> [!NOTE]
> A `AAD` secção no URL de retorno de chamadas OAuth deve corresponder ao nome do fornecedor de identidade OAuth que irá configurar mais tarde.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
apiServer=$(az aro show -g aro-rg -n aro-cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g aro-rg -n aro-cluster --query consoleProfile.url -o tsv)
oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Criar um pedido de Diretório Ativo Azure para autenticação

Crie uma aplicação Azure Ative Directory e recupere o identificador de aplicações criado. Substitua **\<ClientSecret>** por uma senha segura.

```azurecli-interactive
az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password '<ClientSecret>'
```

Devia ter de volta algo assim. Tome nota disso como este é o **AppId** que você precisará em passos posteriores.

```output
6a4cb4b2-f102-4125-b5f5-9ad6689f7224
```

Recupere a identificação do inquilino da assinatura que detém a aplicação.

```azure
az account show --query tenantId -o tsv
```

Devia ter de volta algo assim. Tome nota disso como este é o **TenantId** que você precisará em passos posteriores.

```output
72f999sx-8sk1-8snc-js82-2d7cj902db47
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

**\<AppID>** Substitua-o pela identificação que obteve mais cedo.

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id <AppId>
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Atualizar as permissões de âmbito de aplicação do Azure Ative Directory

Para podermos ler as informações do utilizador a partir do Azure Ative Directory, precisamos definir os âmbitos adequados.

**\<AppID>** Substitua-o pela identificação que obteve mais cedo.

Adicione permissão para o **Azure Ative Directory Graph.User.Read** scope para permitir o sessão e ler o perfil do utilizador.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id <AppId>
```

> [!NOTE]
> A menos que seja autenticado como Administrador Global para este Diretório Ativo Azure, pode ignorar a mensagem para conceder o consentimento, uma vez que lhe será solicitado que o faça assim que iniciar sessão na sua própria conta.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Atribuir utilizadores e grupos ao cluster (opcional)

As aplicações registadas num inquilino do Azure Ative Directory (Azure AD) estão, por defeito, disponíveis para todos os utilizadores do arrendatário que autenticam com sucesso. A Azure AD permite que administradores e desenvolvedores de inquilinos restringam uma app a um conjunto específico de utilizadores ou grupos de segurança no inquilino.

Siga as instruções da documentação do Azure Ative Directory para [atribuir utilizadores e grupos à aplicação](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Configure a autenticação OpenShift OpenID

Recupere as `kubeadmin` credenciais. Executar o seguinte comando para encontrar a senha para o `kubeadmin` utilizador.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

A saída de exemplo a seguir mostra que a palavra-passe estará em `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Inicie sessão no servidor API do cluster OpenShift utilizando o seguinte comando. A `$apiServer` variável foi definida [anteriormente.]() **\<kubeadmin password>** Substitua-a pela palavra-passe que recuperou.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

Crie um segredo OpenShift para armazenar o segredo da aplicação Azure Ative Directory, substituindo **\<ClientSecret>** o segredo que recuperou anteriormente.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=<ClientSecret>
```

Crie um ficheiro **oidc.yaml** para configurar a autenticação OpenShift OpenID contra o Azure Ative Directory. Substitua **\<AppID>** e **\<TenantId>** pelos valores recuperados anteriormente.

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
      clientID: <AppId>
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
      issuer: https://login.microsoftonline.com/<TenantId>
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