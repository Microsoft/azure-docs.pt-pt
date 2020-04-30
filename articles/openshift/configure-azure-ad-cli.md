---
title: Azure Red Hat OpenShift executando OpenShift 4 - Configure Azure Ative Diretório autenticação usando a linha de comando
description: Saiba como configurar a autenticação do Diretório Ativo Azure para um cluster OpenShift de chapéu vermelho azure que executa o OpenShift 4 usando a linha de comando
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, chapéu vermelho, cli
ms.custom: mvc
ms.openlocfilehash: 45da3034891e5a82fb8423adb6bcd5e867f9d4e2
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205004"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Configure Autenticação de Diretório Ativo Do Azure para um cluster OpenShift 4 do chapéu vermelho azure (CLI)

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão Azure CLI 2.0.75 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Recupere os seus URLs específicos do cluster que serão usados para configurar a aplicação Azure Ative Directory.

Construa o URL de callback OAuth do cluster e guarde-o num **oauthCallbackURL**variável . Certifique-se de substituir **o aro-rg** pelo nome do seu grupo de recursos e o seu aglomerado de **aro-cluster** com o nome do seu cluster.

> [!NOTE]
> A `AAD` secção no URL de callback oAuth deve corresponder ao nome do fornecedor de identidade OAuth que irá configurar mais tarde.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
apiServer=$(az aro show -g aro-rg -n aro-cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g aro-rg -n aro-cluster --query consoleProfile.url -o tsv)
oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Criar um pedido de Diretório Ativo Azure para autenticação

Crie uma aplicação Azure Ative Directory e recupere o identificador de aplicação criado. Substitua o ** \<ClienteSecret>** por uma senha segura.

```azurecli-interactive
az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password '<ClientSecret>'
```

Devia sacá-lo algo assim. Tome nota, pois este é o **AppId** que necessitará em etapas posteriores.

```output
6a4cb4b2-f102-4125-b5f5-9ad6689f7224
```

Recupere a identificação do inquilino da subscrição que detém a aplicação.

```azure
az account show --query tenantId -o tsv
```

Devia sacá-lo algo assim. Tome nota, pois este é o **TenantId** que você precisará em passos posteriores.

```output
72f999sx-8sk1-8snc-js82-2d7cj902db47
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Criar um ficheiro manifesto para definir as alegações opcionais a incluir no Id Token

Os desenvolvedores de aplicações podem utilizar [reclamações opcionais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) nas suas aplicações Azure AD para especificar quais as reclamações que pretendem em fichas enviadas para a sua aplicação.

Pode utilizar reclamações opcionais para:

- Selecione reclamações adicionais para incluir em fichas para a sua aplicação.
- Mude o comportamento de certas alegações que a AD Azure devolve em fichas.
- Adicione e aceda a reclamações personalizadas para a sua aplicação.

Vamos configurar o OpenShift `email` para usar a `upn` reclamação e voltar a `upn` definir o nome de utilizador preferido, adicionando o como parte do token ID devolvido pelo Diretório Ativo Azure.

Crie um ficheiro **manifesto.json** para configurar a aplicação Azure Ative Directory.

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

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Atualizar as opções opcionais da aplicação Azure Ative Directory com um manifesto

Substitua o ** \<>AppID** com a identificação que obteve anteriormente.

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id <AppId>
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Atualizar as permissões de âmbito de aplicação do Diretório Ativo do Azure

Para podermos ler as informações dos utilizadores a partir do Azure Ative Directory, precisamos de definir os âmbitos adequados.

Substitua o ** \<>AppID** com a identificação que obteve anteriormente.

Adicione permissão para o **Gráfico de Diretório Ativo Azure.User.Read** scope para ativar o sessão e ler o perfil do utilizador.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id <AppId>
```

> [!NOTE]
> A menos que seja autenticado como Administrador Global deste Diretório Ativo Azure, pode ignorar a mensagem para conceder o consentimento, uma vez que lhe será pedido que o faça assim que iniciar sessão na sua própria conta.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Atribuir utilizadores e grupos ao cluster (opcional)

As candidaturas registadas num inquilino do Azure Ative Directory (Azure AD) estão, por defeito, disponíveis para todos os utilizadores do arrendatário que autenticam com sucesso. A Azure AD permite que administradores e desenvolvedores de inquilinos restrinjam uma app a um conjunto específico de utilizadores ou grupos de segurança no inquilino.

Siga as instruções da documentação do Diretório Ativo Do Azure para [atribuir utilizadores e grupos à aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Configure a autenticação OpenShift OpenID

Recupere `kubeadmin` as credenciais. Executar o seguinte comando para `kubeadmin` encontrar a palavra-passe para o utilizador.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

A saída de exemplo seguinte `kubeadminPassword`mostra que a palavra-passe estará dentro .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Inicie sessão no servidor API do cluster OpenShift utilizando o seguinte comando. A `$apiServer` variável foi definida [mais cedo.]() Substitua a ** \<palavra-passe de kubeadmin>** pela palavra-passe que recuperou.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

Crie um segredo OpenShift para armazenar o segredo ** \<** da aplicação Azure Ative Directory, substituindo o ClienteSecret>pelo segredo que recuperou anteriormente.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=<ClientSecret>
```    

Crie um ficheiro **oidc.yaml** para configurar a autenticação OpenShift OpenID contra o Diretório Ativo Azure. Substitua ** \<o AppID>** e ** \<o TenantId>** os valores que recuperou anteriormente.

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

## <a name="verify-login-through-azure-active-directory"></a>Verifique o login através do Diretório Ativo Azure

Se iniciar sessão na Consola Web OpenShift e tentar fazer login novamente, será-lhe apresentada uma nova opção para iniciar sessão com **a AAD**. Talvez precise esperar alguns minutos.

![Inicie sessão no ecrã com opção Azure Ative Directory](media/aro4-login-2.png)
