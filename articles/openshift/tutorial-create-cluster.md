---
title: Tutorial - Crie um cluster OpenShift 4 do chapéu vermelho azure
description: Saiba como criar um cluster OpenShift do Microsoft Azure Red Hat usando o Azure CLI
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d9b02c11c055b4b072c5f8a1ff47e44001ec4580
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509725"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: Criar um cluster OpenShift 4 do chapéu vermelho azure

Neste tutorial, parte um de três, você vai preparar o seu ambiente para criar um cluster OpenShift de chapéu vermelho azure executando OpenShift 4, e criar um cluster. Vai aprender a:
> [!div class="checklist"]
> * Configurar os pré-requisitos e criar a rede virtual e as subredes necessárias
> * Implementar um cluster

## <a name="before-you-begin"></a>Antes de começar

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.0.75 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="install-the-az-aro-extension"></a>Instalar `az aro` a extensão
A `az aro` extensão permite-lhe criar, aceder e eliminar os clusters OpenShift do Chapéu Vermelho Azure diretamente da linha de comando utilizando o Azure CLI.

Executar o seguinte comando `az aro` para instalar a extensão.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Se já tiver a extensão instalada, pode atualizar executando o seguinte comando.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

Em seguida, precisa `Microsoft.RedHatOpenShift` de registar o fornecedor de recursos na sua subscrição.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Verifique se a extensão está registada.

```azurecli-interactive
az -v
```

  Deve obter uma saída semelhante à seguinte.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Obtenha um segredo de pull de chapéu vermelho (opcional)

Um segredo de pull Red Hat permite ao seu cluster aceder aos registos de contentores do Red Hat juntamente com conteúdo adicional. Este passo é opcional, mas recomendado.

Obtenha o seu segredo https://cloud.redhat.com/openshift/install/azure/aro-provisioned de puxar navegando e clicando em *baixar segredo*de pull .

Terá de fazer login na sua conta Red Hat ou criar uma nova conta Red Hat com o seu email comercial e aceitar os termos e condições.

Mantenha o `pull-secret.txt` ficheiro guardado em algum lugar seguro - será usado em cada criação de cluster.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Criar uma rede virtual contendo duas subredes vazias

Em seguida, criará uma rede virtual contendo duas subredes vazias.

1. **Desconte as seguintes variáveis.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Criar um grupo de recursos**

    Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados do grupo de recursos são armazenados, é também onde os seus recursos funcionam em Azure se você não especificar outra região durante a criação de recursos. Crie um grupo de recursos utilizando o comando [az group criar][az-group-create].

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    A saída de exemplo seguinte mostra o grupo de recursos criado com sucesso:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Criar uma rede virtual.**

    Os clusters OpenShift do Chapéu Vermelho Azure que executam o OpenShift 4 requerem uma rede virtual com duas subredes vazias, para os nós mestre e operário.

    Crie uma nova rede virtual no mesmo grupo de recursos que criou anteriormente.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    A saída de exemplo a seguir mostra a rede virtual criada com sucesso:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Adicione uma sub-rede vazia para os narizes principais.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Adicione uma sub-rede vazia para os nódosos dos trabalhadores.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Desative as políticas de ponto final privado da sub-rede](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) na sub-rede principal.** Isto é necessário para ser capaz de ligar e gerir o cluster.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Criar o cluster

Executar o seguinte comando para criar um cluster. Opcionalmente, pode passar um segredo de pull que permite ao seu cluster aceder aos registos de contentores do Red Hat juntamente com conteúdo adicional. Aceda ao seu segredo de puxar navegando até ao [Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) e clicando em Copy Pull **Secret**.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret '$(< pull-secret.txt)' # [OPTIONAL]
```
>[!NOTE]
> Normalmente demora cerca de 35 minutos a criar um aglomerado.

>[!IMPORTANT]
> Se optar por especificar um domínio personalizado, por **exemplo, foo.example.com,** a `https://console-openshift-console.apps.foo.example.com`consola OpenShift estará disponível num URL como, em vez do domínio `https://console-openshift-console.apps.<random>.<location>.aroapp.io`incorporado.
>
> Por predefinição, a OpenShift utiliza certificados auto-assinados para todas as rotas criadas em `*.apps.<random>.<location>.aroapp.io`.  Se optar por utilizar DNS personalizados após a ligação ao cluster, terá de seguir a documentação OpenShift para [configurar um CA personalizado para o seu controlador](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) de entrada e um CA personalizado para o seu servidor [API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).
>

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Configurar os pré-requisitos e criar a rede virtual e as subredes necessárias
> * Implementar um cluster

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Ligue-se a um cluster OpenShift do chapéu vermelho azul](tutorial-connect-cluster.md)
