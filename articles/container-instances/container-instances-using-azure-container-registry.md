---
title: Implementar no Azure Container Instances do registo de contentor do Azure
description: Saiba como implementar contentores no Azure Container Instances com imagens de contentor num Azure container registry.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b596af8ae9fbbaee6964622df44d316a11582cb9
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337928"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Implementar no Azure Container Instances do registo de contentor do Azure

[O Azure Container Registry](../container-registry/container-registry-intro.md) é um serviço de registo de contentor baseado no Azure, gerido utilizado para armazenar imagens privadas de contentor do Docker. Este artigo descreve como implementar imagens de contentor armazenadas num Azure container registry no Azure Container Instances.

## <a name="prerequisites"></a>Pré-requisitos

**Registo de contentor do Azure**: Precisa de um registo de contentor do Azure – e a imagem de contentor, pelo menos, um registro – para concluir os passos neste artigo. Se precisar de um registo, veja [criar um registo de contentor com a CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md).

**CLI do Azure**: Os exemplos da linha de comandos neste artigo utilizam o [CLI do Azure](/cli/azure/) e são formatados para o shell de Bash. Pode [instalar a CLI do Azure](/cli/azure/install-azure-cli) localmente, ou utilize o [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Configurar a autenticação do registo

Em qualquer cenário de produção, o acesso a um Azure container registry deve ser fornecido com o [principais de serviço](../container-registry/container-registry-auth-service-principal.md). Principais de serviço permitem que forneça [controlo de acesso baseado em funções](../container-registry/container-registry-roles.md) para as imagens de contentor. Por exemplo, pode configurar um principal de serviço com acesso a um registo apenas por pedido.

Na secção seguinte, criar um cofre de chave do Azure e um principal de serviço e armazenar credenciais do principal de serviço no cofre. 

### <a name="create-key-vault"></a>Criar o cofre de chaves

Se ainda não tiver um cofre no [Azure Key Vault](../key-vault/key-vault-overview.md), crie um com a CLI do Azure através dos comandos seguintes.

Atualização do `RES_GROUP` variável com o nome do grupo de recursos existente no qual pretende criar o Cofre de chaves, e `ACR_NAME` com o nome do seu registo de contentor. Especifique um nome para o novo cofre de chaves no `AKV_NAME`. O nome do cofre tem de ser exclusivo no Azure e tem de ser 3 e 24 carateres de alfanuméricos de comprimento, começar com uma letra, terminar com uma letra ou dígito e não pode conter hífenes consecutivos.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Criar o principal de serviço e armazenar as credenciais

Agora tem de criar um principal de serviço e armazenar as credenciais no cofre de chaves.

O seguinte comando utiliza [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] para criar o principal de serviço, e [conjunto segredo do keyvault az] [ az-keyvault-secret-set] para armazenar o principal de serviço **palavra-passe** no cofre.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
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

O `--role` argumento no comando anterior configura o principal de serviço com o *acrpull* função, que concede-pull-only acesso ao registo. Para conceder ambos enviar e extrair o acesso, altere a `--role` argumento *acrpush*.

Em seguida, armazenar o principal de serviço *appId* no cofre, que é o **username** passar para o Azure Container Registry para autenticação.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Acabou de criar um Azure Key Vault e de armazenar dois segredos nele:

* `$ACR_NAME-pull-usr`: O ID de principal de serviço, para utilização como o registo de contentor **nome de utilizador**.
* `$ACR_NAME-pull-pwd`: A senha de principal de serviço, para utilização como o registo de contentor **palavra-passe**.

Agora, pode referenciar estes segredos por nome quando você ou as suas aplicações e serviços solicitarem imagens do registo.

## <a name="deploy-container-with-azure-cli"></a>Implementar o contentor com a CLI do Azure

Agora que as credenciais do principal de serviço são armazenadas em segredos do Cofre de chaves do Azure, as aplicações e serviços podem utilizá-los para aceder ao seu registo privado.

Primeiro obtenha o nome de servidor de início de sessão do registo, utilizando o [show do az acr] [ az-acr-show] comando. O nome de servidor de início de sessão é todo em minúsculas e semelhante ao `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Execute o comando seguinte [az container create][az-container-create] para implementar uma instância de contentor. O comando utiliza credenciais do principal de serviço armazenadas no Azure Key Vault para autenticar para o seu registo de contentor e parte do princípio de que anteriormente enviou a [aci-helloworld](container-instances-quickstart.md) imagem para o seu registo. Atualização do `--image` valor se gostaria de utilizar uma imagem diferente a partir do registo.

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

O `--dns-name-label` valor tem de ser exclusivo no Azure, para que o comando anterior acrescenta um número aleatório, a etiqueta de nome DNS do contentor. O resultado do comando apresenta o nome de domínio completamente qualificado (FQDN) do contentor, por exemplo:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Depois do contentor foi iniciado com êxito, pode navegar para o seu FQDN no seu browser para verificar que a aplicação está em execução com êxito.

## <a name="deploy-with-azure-resource-manager-template"></a>Implementar com o modelo Azure Resource Manager

Pode especificar as propriedades do seu registo de contentor do Azure num modelo do Azure Resource Manager, incluindo o `imageRegistryCredentials` propriedade na definição de grupo do contentor:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Para obter detalhes sobre a referenciar segredos do Cofre de chaves do Azure num modelo do Resource Manager, consulte [do Azure com o Key Vault para transmitir o valor do parâmetro segura durante a implementação](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Implementar com o portal do Azure

Se mantenha as imagens de contentor num registo de contentor do Azure, pode facilmente criar um contentor no Azure Container Instances com o portal do Azure. Quando utilizar o portal para implementar uma instância de contentor a partir de um registo de contentor, tem de ativar o registo [conta de administrador](../container-registry/container-registry-authentication.md#admin-account). A conta de administrador foi concebida para um único utilizador aceder ao registo, principalmente para fins de teste. 

1. No portal do Azure, navegue para o seu registo de contentor.

1. Para confirmar que a conta de administrador está ativada, selecione **chaves de acesso**e, em **utilizador administrador** selecionar **ativar**.

1. Selecione **repositórios**, em seguida, selecione o repositório que pretende implementar a partir de, com o botão direito a etiqueta da imagem de contentor que pretende implementar e selecione **executar instância**.

    !["Executar instância" no Azure Container Registry no portal do Azure][acr-runinstance-contextmenu]

1. Introduza um nome para o contentor e um nome para o grupo de recursos. Também pode alterar os valores predefinidos se desejar.

    ![Criar o menu do Azure Container Instances][acr-create-deeplink]

1. Depois de concluída a implementação, pode navegar para o grupo de contentores a partir do painel de notificações para localizar o respetivo endereço IP e outras propriedades.

    ![Vista de detalhes para o grupo de contentores do Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a autenticação do Azure Container Registry, veja [autenticar com um Azure container registry](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
