---
title: Implantar a imagem de contêiner do registro de contêiner do Azure
description: Saiba como implantar contêineres em instâncias de contêiner do Azure usando imagens de contêiner em um registro de contêiner do Azure.
services: container-instances
ms.topic: article
ms.date: 12/30/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 823a25f388860fa55962a717b9dfed22f5d9c103
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770526"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Implantar em instâncias de contêiner do Azure do registro de contêiner do Azure

O [registro de contêiner do Azure](../container-registry/container-registry-intro.md) é um serviço de registro de contêiner gerenciado baseado no Azure usado para armazenar imagens de contêiner privadas do Docker. Este artigo descreve como implantar imagens de contêiner armazenadas em um registro de contêiner do Azure para instâncias de contêiner do Azure.

## <a name="prerequisites"></a>Pré-requisitos

**Registro de contêiner do Azure**: você precisa de um registro de contêiner do Azure – e pelo menos uma imagem de contêiner no registro – para concluir as etapas neste artigo. Se você precisar de um registro, consulte [criar um registro de contêiner usando o CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md).

**CLI do Azure**: os exemplos de linha de comando neste artigo usam o [CLI do Azure](/cli/azure/) e são formatados para o shell bash. Você pode [instalar o CLI do Azure](/cli/azure/install-azure-cli) localmente ou usar o [Azure cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Configurar a autenticação do registo

Em um cenário de produção em que você fornece acesso a aplicativos e serviços "sem periféricos", é recomendável configurar o acesso ao registro usando uma [entidade de serviço](../container-registry/container-registry-auth-service-principal.md). Uma entidade de serviço permite que você forneça [controle de acesso baseado em função](../container-registry/container-registry-roles.md) para suas imagens de contêiner. Por exemplo, pode configurar um principal de serviço com acesso a um registo apenas por pedido.

O registro de contêiner do Azure fornece [Opções de autenticação](../container-registry/container-registry-authentication.md)adicionais.

Na seção a seguir, você cria um cofre de chaves do Azure e uma entidade de serviço e armazena as credenciais da entidade de serviço no cofre. 

### <a name="create-key-vault"></a>Criar o cofre de chaves

Se ainda não tiver um cofre no [Azure Key Vault](../key-vault/key-vault-overview.md), crie um com a CLI do Azure através dos comandos seguintes.

Atualize a variável `RES_GROUP` com o nome de um grupo de recursos existente no qual criar o cofre de chaves e `ACR_NAME` com o nome do registro de contêiner. Para fins de brevidade, os comandos neste artigo pressupõem que o registro, o cofre de chaves e as instâncias de contêiner sejam todos criados no mesmo grupo de recursos.

 Especifique um nome para o novo cofre de chaves no `AKV_NAME`. O nome do cofre deve ser exclusivo no Azure e deve ter 3-24 caracteres alfanuméricos de comprimento, começar com uma letra, terminar com uma letra ou dígito e não pode conter hifens consecutivos.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Criar o principal de serviço e armazenar as credenciais

Agora, crie uma entidade de serviço e armazene suas credenciais no cofre de chaves.

O comando a seguir usa [AZ ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] para criar a entidade de serviço e [AZ keyvault segredo Set][az-keyvault-secret-set] para armazenar a **senha** da entidade de serviço no cofre.

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

O argumento `--role` no comando anterior configura a entidade de serviço com a função *acrpull* , que concede acesso somente pull ao registro. Para conceder acesso de push e pull, altere o argumento `--role` para *acrpush*.

Em seguida, armazene o *AppID* da entidade de serviço no cofre, que é o **nome de usuário** que você passa para o registro de contêiner do Azure para autenticação.

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

Agora que as credenciais da entidade de serviço são armazenadas em Azure Key Vault segredos, seus aplicativos e serviços podem usá-las para acessar seu registro privado.

Primeiro, obtenha o nome do servidor de logon do registro usando o comando [AZ ACR show][az-acr-show] . O nome do servidor de logon é todas as letras minúsculas e semelhantes a `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Execute o comando [AZ container Create][az-container-create] a seguir para implantar uma instância de contêiner. O comando usa as credenciais da entidade de serviço armazenadas em Azure Key Vault para se autenticar no registro de contêiner e pressupõe que você enviou anteriormente a imagem [ACI-HelloWorld](container-instances-quickstart.md) para o registro. Atualize o valor `--image` se desejar usar uma imagem diferente do registro.

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

O valor de `--dns-name-label` deve ser exclusivo no Azure, portanto, o comando anterior acrescenta um número aleatório ao rótulo de nome DNS do contêiner. O resultado do comando apresenta o nome de domínio completamente qualificado (FQDN) do contentor, por exemplo:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Depois que o contêiner for iniciado com êxito, você poderá navegar para seu FQDN no navegador para verificar se o aplicativo está sendo executado com êxito.

## <a name="deploy-with-azure-resource-manager-template"></a>Implantar com o modelo de Azure Resource Manager

Você pode especificar as propriedades do seu registro de contêiner do Azure em um modelo de Azure Resource Manager, incluindo a propriedade `imageRegistryCredentials` na definição do grupo de contêineres. Por exemplo, você pode especificar as credenciais de registro diretamente:

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

Para obter configurações completas do grupo de contêineres, consulte a [referência de modelo do Resource Manager](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups).    

Para obter detalhes sobre como referenciar segredos de Azure Key Vault em um modelo do Resource Manager, consulte [usar Azure Key Vault para passar o valor do parâmetro seguro durante a implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Implantar com portal do Azure

Se você mantiver imagens de contêiner em um registro de contêiner do Azure, poderá criar facilmente um contêiner em instâncias de contêiner do Azure usando o portal do Azure. Ao usar o portal para implantar uma instância de contêiner de um registro de contêiner, você deve habilitar a [conta de administrador](../container-registry/container-registry-authentication.md#admin-account)do registro. A conta de administrador é projetada para que um único usuário acesse o registro, principalmente para fins de teste. 

1. Na portal do Azure, navegue até o registro de contêiner.

1. Para confirmar se a conta de administrador está habilitada, selecione **chaves de acesso**e, em **usuário administrador** , selecione **habilitar**.

1. Selecione repositórios e, em **seguida, selecione**o repositório do qual você deseja implantar, clique com o botão direito do mouse na marca da imagem de contêiner que deseja implantar e selecione **executar instância**.

    !["Executar instância" no registro de contêiner do Azure no portal do Azure][acr-runinstance-contextmenu]

1. Insira um nome para o contêiner e um nome para o grupo de recursos. Você também pode alterar os valores padrão, se desejar.

    ![Criar menu para instâncias de contêiner do Azure][acr-create-deeplink]

1. Depois que a implantação for concluída, você poderá navegar até o grupo de contêineres no painel notificações para localizar seu endereço IP e outras propriedades.

    ![Exibição de detalhes para o grupo de contêineres de instâncias de contêiner do Azure][aci-detailsview]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a autenticação do registro de contêiner do Azure, consulte [autenticar com um registro de contêiner do Azure](../container-registry/container-registry-authentication.md).

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
