---
title: Azure Red Hat OpenShift executando OpenShift 4 - Configuração Azure Ative Directory autenticação usando o portal Azure e a consola web OpenShift
description: Saiba como configurar a autenticação do Azure Ative Directory para um cluster Azure Red Hat OpenShift que executa o OpenShift 4 utilizando o portal Azure e a consola web OpenShift
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, chapéu vermelho, cli
ms.custom: mvc
ms.openlocfilehash: 4eab701d22f579a816aa95bd43a74fd9ea07d9e4
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490242"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Configurar a autenticação do Diretório Ativo Azure para um cluster Azure Red Hat OpenShift 4 (Portal)

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Antes de começar

Construa o URL de **retorno de OAuth** do cluster e tome nota dele. Certifique-se de que substitui **o aro-rg** pelo nome do seu grupo de recursos e **pelo aro-cluster** pelo nome do seu cluster.

> [!NOTE]
> A `AAD` secção no URL de retorno de chamadas OAuth deve corresponder ao nome do fornecedor de identidade OAuth que irá configurar mais tarde.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Criar um pedido de Diretório Ativo Azure para autenticação

Faça login no portal Azure e navegue para a [lâmina de registos da App,](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)clique em **Novo Registo** para criar uma nova aplicação.

Forneça um nome para a aplicação, por exemplo **aro-azuread-auth**, e preencha o **Redirect URI** usando o valor do URL de retorno de OAuth que recuperou anteriormente.

![Novo registo de aplicação](media/aro4-ad-registerapp.png)

Navegue para **Certificados & segredos** e clique em **Novo segredo de cliente** e preencha os detalhes. Tome nota do valor da chave, pois irá usá-lo numa fase posterior. Não vai conseguir recuperá-la de novo.

![Criar um segredo](media/aro4-ad-clientsecret.png)

Navegue para a **Visão Geral** e tome nota do ID de **Aplicação (cliente)** e **Diretório (inquilino).** Vai precisar deles numa fase posterior.

![IDs de Pedido de Recuperação (cliente) e Diretório (inquilino)](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>Configurar afirmações opcionais

Os desenvolvedores de aplicações podem usar [reclamações opcionais](../active-directory/develop/active-directory-optional-claims.md) nas suas aplicações AZure AD para especificar quais as alegações que pretendem em fichas enviadas para a sua aplicação.

Pode utilizar reclamações opcionais para:

* Selecione reclamações adicionais para incluir em fichas para a sua aplicação.
* Alterar o comportamento de certas alegações de que a Azure AD regressa em fichas.
* Adicione e aceda reclamações personalizadas para a sua aplicação.

Configuraremos o OpenShift para usar a `email` reclamação e recuar `upn` para definir o nome de utilizador preferido adicionando o como parte do `upn` token de ID devolvido pelo Azure Ative Directory.

Navegue para **a configuração token (pré-visualização)** e clique em **Adicionar reivindicação opcional**. Selecione **ID** em seguida, verifique o **e-mail** e **upn** reclamações.

![Screenshot que mostra o e-mail e upn alegações que foram adicionadas.](media/aro4-ad-tokens.png)

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

Você pode encontrar o URL da consola de cluster executando o seguinte comando, que será semelhante `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

Lance o URL da consola num browser e faça login usando as `kubeadmin` credenciais.

Navegue para **administração**, clique em **Definições de Cluster**e, em seguida, selecione o **separador Configuração Global.** Percorra para selecionar **OAuth**.

Desloque-se para baixo para selecionar **Adicionar** aos **Fornecedores de Identidade** e selecione **OpenID Connect**.
![Selecione OpenID Connect a partir do dropdown dos Fornecedores de Identidade](media/aro4-oauth-idpdrop.png)

Preencha o nome como **AAD,** o **ID** do Cliente como ID de **aplicação** e o Segredo do **Cliente.** O **URL do Emitente** é formatado como tal: `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . Substitua o espaço reservado pelo ID do inquilino que recuperou anteriormente.

![Preencha detalhes da OAuth](media/aro4-oauth-idp-1.png)

Percorra a secção **'Reclamações'** e atualize o **nome de utilizador preferido** para utilizar o valor da reclamação **upn.**

![Preencha detalhes de reclamações](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Verifique o login através do Azure Ative Directory

Se agora tiver o início da consola Web OpenShift e tentar iniciar sessão novamente, será apresentada uma nova opção para iniciar sessão com **a AAD**. Talvez precise esperar por alguns minutos.

![Ecrã de login com opção Azure Ative Directory](media/aro4-login-2.png)