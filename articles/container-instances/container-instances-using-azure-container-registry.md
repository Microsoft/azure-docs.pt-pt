---
title: Implantar imagem de contentor a partir do registo do contentor de Azure
description: Saiba como implantar contentores em instâncias de contentores Azure, retirando imagens de contentores de um registo de contentores Azure.
services: container-instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc
ms.openlocfilehash: d5ba56271950c2d14c7fbf0b9154afb371bcbabc
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173644"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Implementar no Azure Container Instances a partir do Azure Container Registry

[O Registo de Contentores Azure](../container-registry/container-registry-intro.md) é um serviço de registo de contentores gerido à base de Azure, utilizado para armazenar imagens privadas de contentores do Docker. Este artigo descreve como retirar imagens de contentores armazenadas num registo de contentores Azure ao ser implantado em Instâncias de Contentores Azure. Uma forma recomendada de configurar o acesso ao registo é criar um serviço de diretório Azure Ative e senha, e armazenar as credenciais de login num cofre de chaves Azure.

## <a name="prerequisites"></a>Pré-requisitos

**Registo do contentor Azure**: É necessário um registo de contentores Azure e pelo menos uma imagem de contentor no registo para completar as etapas deste artigo. Se precisar de um registo, consulte [criar um registo de contentores utilizando o Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: Os exemplos da linha de comando neste artigo utilizam o [Azure CLI](/cli/azure/) e são formatados para a casca bash. Pode [instalar o Azure CLI](/cli/azure/install-azure-cli) localmente ou utilizar a [Azure Cloud Shell][cloud-shell-bash].

## <a name="limitations"></a>Limitações

* Não é possível autenticar o Registo de Contentores Azure para retirar imagens durante a colocação do grupo de contentores utilizando uma [identidade gerida](container-instances-managed-identity.md) configurada no mesmo grupo de contentores.
* Não é possível extrair imagens do Registo de [Contentores Azure](../container-registry/container-registry-vnet.md) implantados numa Rede Virtual Azure neste momento.

## <a name="configure-registry-authentication"></a>Configurar a autenticação do registo

Num cenário de produção em que se fornece acesso a serviços e aplicações "sem cabeça", recomenda-se configurar o acesso ao registo através de um [principal serviço.](../container-registry/container-registry-auth-service-principal.md) Um chefe de serviço [permite-lhe fornecer o controlo de acesso baseado em funções (Azure RBAC)](../container-registry/container-registry-roles.md) às imagens do seu contentor. Por exemplo, pode configurar um principal de serviço com acesso a um registo apenas por pedido.

O Registo de Contentores Azure oferece opções de [autenticação](../container-registry/container-registry-authentication.md)adicionais.

Na secção seguinte, você cria um cofre de chaves Azure e um diretor de serviço, e armazena as credenciais do diretor de serviço no cofre.

### <a name="create-key-vault"></a>Criar o cofre de chaves

Se ainda não tiver um cofre no [Azure Key Vault](../key-vault/general/overview.md), crie um com a CLI do Azure através dos comandos seguintes.

Atualize a `RES_GROUP` variável com o nome de um grupo de recursos existente para criar o cofre-chave, e `ACR_NAME` com o nome do seu registo de contentores. Para a brevidade, os comandos neste artigo assumem que o seu registo, cofre chave e instâncias de contentores são todos criados no mesmo grupo de recursos.

 Especifique um nome para o seu novo cofre de chaves em `AKV_NAME` . O nome do cofre deve ser único dentro de Azure e deve ter 3-24 caracteres alfanuméricos de comprimento, começar com uma letra, terminar com uma letra ou dígito, e não pode conter hífenes consecutivos.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Criar o principal de serviço e armazenar as credenciais

Agora crie um diretor de serviço e guarde as suas credenciais no seu cofre chave.

O seguinte comando usa [a az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar o principal serviço, e [az keyvault conjunto secreto][az-keyvault-secret-set] para armazenar a **senha** do diretor de serviço no cofre.

```azurecli
# Create service principal, store its password in vault (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

O `--role` argumento no comando anterior configura o diretor de serviço com a função *acrpull,* que lhe concede acesso apenas ao registo. Para conceder tanto o impulso como o acesso, mude o `--role` argumento para *acrpush*.

Em seguida, guarde o *appId* do diretor de serviço no cofre, que é o **nome de utilizador** que você passa para O Registo de Contentores Azure para autenticação.

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Acabou de criar um cofre de chaves do Azure e de armazenar dois segredos nele:

* `$ACR_NAME-pull-usr`: o ID do principal de serviço, para utilização como o **nome de utilizador** do registo de contentor.
* `$ACR_NAME-pull-pwd`: a palavra-passe do principal de serviço, para utilização como a **palavra-passe** do registo de contentor.

Agora, pode referenciar estes segredos por nome quando você ou as suas aplicações e serviços solicitarem imagens do registo.

## <a name="deploy-container-with-azure-cli"></a>Implementar o contentor com a CLI do Azure

Agora que as principais credenciais do serviço estão armazenadas nos segredos do Azure Key Vault, as suas aplicações e serviços podem usá-las para aceder ao seu registo privado.

Primeiro obtenha o nome do servidor de login do registo usando o comando [de show az acr.][az-acr-show] O nome do servidor de login é tudo minúsculo e semelhante a `myregistry.azurecr.io` .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Execute o comando seguinte [az container create][az-container-create] para implementar uma instância de contentor. O comando utiliza as credenciais do diretor de serviço armazenadas no Cofre da Chave Azure para autenticar o registo do seu contentor, e assume que já empurrou a imagem [aci-helloworld](container-instances-quickstart.md) para o seu registo. Atualize o `--image` valor se quiser utilizar uma imagem diferente do seu registo.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

O `--dns-name-label` valor deve ser único dentro do Azure, pelo que o comando anterior anexa um número aleatório à etiqueta de nome DNS do recipiente. O resultado do comando apresenta o nome de domínio completamente qualificado (FQDN) do contentor, por exemplo:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Uma vez que o recipiente tenha começado com sucesso, você pode navegar para o seu FQDN no seu navegador para verificar se a aplicação está funcionando com sucesso.

## <a name="deploy-with-azure-resource-manager-template"></a>Implementar com o modelo de gestor de recursos Azure

Pode especificar as propriedades do seu registo de contentores Azure num modelo de Gestor de Recursos Azure, incluindo a `imageRegistryCredentials` propriedade na definição do grupo de contentores. Por exemplo, pode especificar diretamente as credenciais de registo:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

Para obter definições completas do grupo de contentores, consulte a referência do [modelo do Gestor de Recursos](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups).    

Para obter detalhes sobre a referência aos segredos do Cofre de Chaves Azure num modelo de Gestor de Recursos, consulte [use a anteure Key Vault para passar o valor do parâmetro seguro durante a implementação](../azure-resource-manager/templates/key-vault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Implementar com portal Azure

Se mantiver imagens de contentores num registo de contentores Azure, pode facilmente criar um recipiente em Instâncias de Contentores Azure utilizando o portal Azure. Ao utilizar o portal para implantar uma instância de contentor a partir de um registo de contentores, deve ativar a conta de [administração](../container-registry/container-registry-authentication.md#admin-account)do registo . A conta de administração foi concebida para que um único utilizador aceda ao registo, principalmente para fins de teste. 

1. No portal Azure, navegue para o seu registo de contentores.

1. Para confirmar que a conta de administração está ativada, selecione **as teclas de acesso**e no utilizador de **Administração** selecione **Enable**.

1. Selecione **Repositórios**, em seguida, selecione o repositório a partir do quais pretende implantar, clique à direita na etiqueta para a imagem do recipiente que pretende implantar e selecione **a instância de execução**.

    !["Instância de execução" no Registo de Contentores Azure no portal Azure][acr-runinstance-contextmenu]

1. Insira um nome para o recipiente e um nome para o grupo de recursos. Também pode alterar os valores predefinidos se desejar.

    ![Criar menu para instâncias de contentores Azure][acr-create-deeplink]

1. Uma vez concluída a implementação, pode navegar para o grupo de contentores a partir do painel de notificações para encontrar o seu endereço IP e outras propriedades.

    ![Vista de detalhes para o grupo de contentores Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a autenticação do Registo do Contentor Azure, consulte [Authenticate com um registo de contentores Azure](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
