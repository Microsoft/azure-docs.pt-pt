---
title: Desloque a imagem do contentor do Registo de Contentores de Azure
description: Aprenda a colocar contentores em Instâncias de Contentores Azure, retirando imagens de contentores de um registo de contentores Azure.
services: container-instances
ms.topic: article
ms.date: 02/18/2020
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 212624b857d65297830995018603c2627f83369b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453528"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Desdobre para as instâncias de contentores azure do registo de contentores de Azure

O Registo de [Contentores Azure](../container-registry/container-registry-intro.md) é um serviço de registo de contentores gerido com base em Azure, utilizado para armazenar imagens privadas de contentores Docker. Este artigo descreve como puxar imagens de contentores armazenadas num registo de contentores Azure ao ser implantado em Instâncias de Contentores Azure. Uma forma recomendada de configurar o acesso ao registo é criar um diretor e senha de serviço Azure Ative Directory e armazenar as credenciais de login num cofre chave Azure.

## <a name="prerequisites"></a>Pré-requisitos

**Registo de contentores azure**: É necessário um registo de contentores Azure e pelo menos uma imagem de contentor no registo - para completar as etapas deste artigo. Se precisar de um registo, consulte Criar um registo de [contentores utilizando o Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: Os exemplos da linha de comando neste artigo utilizam o [Azure CLI](/cli/azure/) e são formatados para a concha bash. Pode [instalar o Azure CLI](/cli/azure/install-azure-cli) localmente ou utilizar a [Casca de Nuvem Azure][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Configurar a autenticação do registo

Num cenário de produção em que fornece acesso a serviços e aplicações "sem cabeça", é aconselhável configurar o acesso ao registo utilizando um diretor de [serviço.](../container-registry/container-registry-auth-service-principal.md) Um diretor de serviço [permite-lhe](../container-registry/container-registry-roles.md) fornecer um controlo de acesso baseado em funções nas imagens do seu contentor. Por exemplo, pode configurar um principal de serviço com acesso a um registo apenas por pedido.

O Registo de Contentores Azure fornece opções adicionais de [autenticação.](../container-registry/container-registry-authentication.md)

> [!NOTE]
> Não é possível autenticar o Registo de Contentores Azure para puxar imagens durante a implantação do grupo de contentores utilizando uma [identidade gerida](container-instances-managed-identity.md) configurada no mesmo grupo de contentores.

Na secção seguinte, cria-se um cofre chave Azure e um diretor de serviço, e armazena as credenciais do diretor de serviço no cofre. 

### <a name="create-key-vault"></a>Criar o cofre de chaves

Se ainda não tiver um cofre no [Azure Key Vault](../key-vault/general/overview.md), crie um com a CLI do Azure através dos comandos seguintes.

Atualize `RES_GROUP` a variável com o nome de um grupo de `ACR_NAME` recursos existente para criar o cofre chave, e com o nome do seu registo de contentores. Para a brevidade, os comandos neste artigo assumem que o seu registo, cofre chave e instâncias de contentores são todos criados no mesmo grupo de recursos.

 Especifique um nome `AKV_NAME`para o seu novo cofre em . O nome do cofre deve ser único dentro de Azure e deve ter 3-24 caracteres alfanuméricos de comprimento, começar com uma letra, terminar com uma letra ou um dígito, e não pode conter hífenes consecutivos.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Criar o principal de serviço e armazenar as credenciais

Agora crie um diretor de serviço e guarde as suas credenciais no seu cofre chave.

O comando seguinte usa [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar o diretor de serviço, e [az keyvault secret definido][az-keyvault-secret-set] para armazenar a **senha** do diretor de serviço no cofre.

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

O `--role` argumento no comando anterior confunde o diretor de serviço com a função *acrpull,* que lhe concede acesso apenas ao registo. Para conceder tanto empurrar e `--role` puxar o acesso, mude o argumento para *acrpush*.

Em seguida, guarde a *appId* do diretor de serviço no cofre, que é o nome de **utilizador** que passa para o Registo de Contentores Azure para autenticação.

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

Agora que as credenciais principais do serviço estão armazenadas em segredos do Cofre chave azure, as suas aplicações e serviços podem usá-los para aceder ao seu registo privado.

Primeiro obtenha o nome do servidor de login do registo usando o comando de [show az acr.][az-acr-show] O nome do servidor de login `myregistry.azurecr.io`é todo minúsculo e semelhante a .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Execute o comando seguinte [az container create][az-container-create] para implementar uma instância de contentor. O comando utiliza as credenciais do diretor de serviço armazenadas no Cofre chave azure para autenticar o seu registo de contentores, e assume que já empurrou a imagem [aci-helloworld](container-instances-quickstart.md) para o seu registo. Atualize `--image` o valor se quiser utilizar uma imagem diferente do seu registo.

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

O `--dns-name-label` valor deve ser único dentro do Azure, pelo que o comando anterior anexa um número aleatório à etiqueta de nome DNS do contentor. O resultado do comando apresenta o nome de domínio completamente qualificado (FQDN) do contentor, por exemplo:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Uma vez que o recipiente tenha começado com sucesso, pode navegar para o fQDN no seu navegador para verificar se a aplicação está a funcionar com sucesso.

## <a name="deploy-with-azure-resource-manager-template"></a>Implementar com o modelo de Gestor de Recursos Azure

Pode especificar as propriedades do seu registo de contentores Azure `imageRegistryCredentials` num modelo de Gestor de Recursos Azure, incluindo a propriedade na definição de grupo de contentores. Por exemplo, pode especificar as credenciais de registo diretamente:

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

Para configurações completas do grupo de contentores, consulte a referência do [modelo do Gestor](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups)de Recursos .    

Para mais detalhes sobre a referência aos segredos do Cofre de Chaves Azure num modelo de Gestor de Recursos, consulte [o Cofre chave Azure para passar](../azure-resource-manager/templates/key-vault-parameter.md)o valor seguro do parâmetro durante a implementação .

## <a name="deploy-with-azure-portal"></a>Implementar com portal Azure

Se mantiver imagens de contentores num registo de contentores Azure, pode facilmente criar um contentor em Instâncias de Contentores Azure utilizando o portal Azure. Ao utilizar o portal para implantar uma instância de contentores a partir de um registo de contentores, deve ativar a [conta de administração](../container-registry/container-registry-authentication.md#admin-account)do registo . A conta de administração destina-se a um único utilizador a aceder ao registo, principalmente para efeitos de teste. 

1. No portal Azure, navegue para o seu registo de contentores.

1. Para confirmar que a conta de administração está ativada, selecione **teclas de Acesso**, e sob a seção de **utilizador da Admin** **Enable**.

1. Selecione **Repositórios**, em seguida, selecione o repositório a que pretende implementar, clique na etiqueta para a imagem do recipiente que pretende implementar e selecione **Executar exemplo**.

    !["Exemplo de execução" no Registo de Contentores de Azure no portal Azure][acr-runinstance-contextmenu]

1. Introduza um nome para o recipiente e um nome para o grupo de recursos. Também pode alterar os valores predefinidos se desejar.

    ![Criar menu para instâncias de contentores azure][acr-create-deeplink]

1. Uma vez concluída a implementação, pode navegar para o grupo de contentores a partir do painel de notificações para encontrar o seu endereço IP e outras propriedades.

    ![Vista de detalhes para o grupo de contentores De casos de contentores Azure][aci-detailsview]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a autenticação do Registo de Contentores Azure, consulte [Authenticate com um registo de contentores Azure](../container-registry/container-registry-authentication.md).

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
