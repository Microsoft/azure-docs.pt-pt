---
title: Azure Red Hat OpenShift executando OpenShift 4 - Configure Azure Ative Directory autenticação usando o portal Azure e a consola web OpenShift
description: Saiba como configurar a autenticação do Diretório Ativo Azure para um cluster OpenShift da Chapéu Vermelha Azure que executa o OpenShift 4 utilizando o portal Azure e a consola web OpenShift
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, chapéu vermelho, cli
ms.custom: mvc
ms.openlocfilehash: 6b6248aac35c22b9ffd2cd95df41e84986356259
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205316"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Configure Azure Ative Diretório autenticação para um cluster OpenShift 4 do chapéu vermelho azure (Portal)

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.0.75 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Antes de começar

Construa o URL de **callback OAuth** do cluster e tome nota. Certifique-se de substituir **o aro-rg** pelo nome do seu grupo de recursos e o seu aglomerado de **aro-cluster** com o nome do seu cluster.

> [!NOTE]
> A `AAD` secção no URL de callback oAuth deve corresponder ao nome do fornecedor de identidade OAuth que irá configurar mais tarde.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Criar um pedido de Diretório Ativo Azure para autenticação

Faça login no portal Azure e navegue para a lâmina de registos da [App,](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)em seguida, clique em **Novo registo** para criar uma nova aplicação.

Forneça um nome para a aplicação, por exemplo **aro-azuread-auth,** e preencha o **Redirect URI** utilizando o valor do URL de callback OAuth que recuperou anteriormente.

![Novo registo de aplicação](media/aro4-ad-registerapp.png)

Navegue para **Certificados & segredos** e clique em **novo segredo do cliente** e preencha os detalhes. Tome nota do valor-chave, pois irá usá-lo numa fase posterior. Não poderá recuperá-lo de novo.

![Criar um segredo](media/aro4-ad-clientsecret.png)

Navegue para a **visão geral** e tome nota do ID de **Aplicação (cliente)** e **id de Diretório (inquilino).** Vai precisar deles numa fase posterior.

![Pedidos de Recuperação (cliente) e IDs de Diretório (inquilino)](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>Configurar afirmações opcionais

Os desenvolvedores de aplicações podem utilizar [reclamações opcionais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) nas suas aplicações Azure AD para especificar quais as reclamações que pretendem em fichas enviadas para a sua aplicação.

Pode utilizar reclamações opcionais para:

* Selecione reclamações adicionais para incluir em fichas para a sua aplicação.
* Mude o comportamento de certas alegações que a AD Azure devolve em fichas.
* Adicione e aceda a reclamações personalizadas para a sua aplicação.

Vamos configurar o OpenShift `email` para usar a `upn` reclamação e voltar a `upn` definir o nome de utilizador preferido, adicionando o como parte do token ID devolvido pelo Diretório Ativo Azure.

Navegar na **configuração token (pré-visualização)** e clicar em **Adicionar pedido opcional**. Selecione **ID,** em seguida, verifique o **e-mail** e as reclamações **upn.**

![Criar um segredo](media/aro4-ad-tokens.png)

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

Você pode encontrar o URL da consola cluster executando o seguinte comando, que será como`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

Lance o URL da consola num `kubeadmin` browser e faça login utilizando as credenciais.

Navegar para **a Administração,** clicar nas **Definições**de **OAuth**Cluster e, em seguida, selecionar o separador de **configuração global.**

Desloque-se para baixo para **selecionar** em **fornecedores** de identidade e selecione **OpenID Connect**.
![Selecione OpenID Connect a partir do dropdown dos Fornecedores de Identidade](media/aro4-oauth-idpdrop.png)

Preencha o nome de **AAD,** o ID do **Cliente** como ID de **Aplicação** e o Segredo do **Cliente.** O **URL emitente** é `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`formatado como tal: . Substitua o espaço reservado pela identificação do inquilino que recuperou anteriormente.

![Preencha os detalhes da OAuth](media/aro4-oauth-idp-1.png)

Desloque-se até à secção **Reivindicações** e atualize o Nome de **Utilizador Preferido** para utilizar o valor a partir da alegação **upn.**

![Preencha os detalhes das reclamações](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Verifique o login através do Diretório Ativo Azure

Se iniciar sessão na Consola Web OpenShift e tentar fazer login novamente, será-lhe apresentada uma nova opção de login com **AAD**. Talvez precise esperar alguns minutos.

![Tela de login com opção Diretório Ativo Azure](media/aro4-login-2.png)
