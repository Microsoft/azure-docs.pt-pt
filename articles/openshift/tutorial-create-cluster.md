---
title: Tutorial - Criar um aglomerado Azure Red Hat OpenShift 4
description: Saiba como criar um cluster Microsoft Azure Red Hat OpenShift utilizando o Azure CLI
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 9a393e29c4b5b2faa48cbcd273c7bc7a46169ba3
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904195"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: Criar um aglomerado Azure Red Hat OpenShift 4

Neste tutorial, parte um de três, você vai preparar o seu ambiente para criar um cluster Azure Red Hat OpenShift executando OpenShift 4, e criar um cluster. Vai aprender a:
> [!div class="checklist"]
> * Configurar os pré-requisitos e criar a rede virtual e sub-redes necessárias
> * Implementar um cluster

## <a name="before-you-begin"></a>Before you begin

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.0.75 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="verify-your-permissions"></a>Verificar as permissões

Para criar um cluster Azure Red Hat OpenShift, verifique as seguintes permissões na sua subscrição Azure, utilizador do Azure Ative Directory ou principal de serviço:

|Permissões|Grupo de Recursos que contém o VNet|Execução do utilizador`az aro create`|Diretor de Serviço passou como`–client-id`|
|----|:----:|:----:|:----:|
|**Administrador de Acesso do Utilizador**|X|X| |
|**Contribuinte**|X|X|X|

### <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

Em seguida, tem de registar o `Microsoft.RedHatOpenShift` fornecedor de recursos na sua subscrição.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Obtenha um segredo de puxar o chapéu vermelho (opcional)

Um segredo de puxar o chapéu vermelho permite ao seu cluster aceder aos registos de contentores do Red Hat juntamente com conteúdo adicional. Este passo é opcional, mas recomendado.

1. **[Navegue para o seu portal de clusters Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) e faça login.**

   Terá de iniciar sessão na sua conta Red Hat ou criar uma nova conta Red Hat com o seu email de negócios e aceitar os termos e condições.

2. Vá à página de [**produto OpenShift**](https://developers.redhat.com/products/codeready-containers) se esta for a sua primeira vez a criar um cluster. Após a inscrição, dirija-se à [**página red hat openshift cluster Manager**](https://cloud.redhat.com/openshift/), onde pode clicar em Baixar o **segredo** e baixar um segredo de puxar para ser usado com o seu cluster ARO.

Mantenha o ficheiro guardado `pull-secret.txt` num lugar seguro. O ficheiro será usado em cada criação de cluster se precisar de criar um cluster que inclua amostras ou operadores para Chapéu Vermelho ou parceiros certificados.

Ao executar o `az aro create` comando, pode fazer referência ao seu segredo de puxar utilizando o `--pull-secret @pull-secret.txt` parâmetro. Execute `az aro create` a partir do diretório onde guardou o seu `pull-secret.txt` ficheiro. Caso contrário, `@pull-secret.txt` `@<path-to-my-pull-secret-file>` substitua-o por .

Se estiver a copiar o seu segredo de puxar ou a fazê-lo referenciar noutros scripts, o seu segredo de puxar deve ser formatado como uma cadeia JSON válida.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Prepare um domínio personalizado para o seu cluster (opcional)

Ao executar o `az aro create` comando, pode especificar um domínio personalizado para o seu cluster utilizando o `--domain foo.example.com` parâmetro.

Se fornecer um domínio personalizado para o seu cluster, note os seguintes pontos:

* Depois de criar o seu cluster, deve criar 2 registos DNS A no seu servidor DNS para o `--domain` especificado:
    * **api** - apontando para o servidor api
    * ** \* .apps** - apontando para a entrada
    * Recupere estes valores executando o seguinte comando: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` .

* A consola OpenShift estará disponível num URL `https://console-openshift-console.apps.foo.example.com` como, em vez do domínio `https://console-openshift-console.apps.<random>.<location>.aroapp.io` incorporado.

* Por predefinição, o OpenShift utiliza certificados auto-assinados para todas as rotas criadas em `*.apps.<random>.<location>.aroapp.io` .  Se optar por utilizar DNS personalizados após a ligação ao cluster, terá de seguir a documentação OpenShift para [configurar um CA personalizado para o seu controlador de entrada](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) e um CA personalizado para o seu servidor [API.](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Criar uma rede virtual contendo duas sub-redes vazias

Em seguida, irá criar uma rede virtual contendo duas sub-redes vazias.

1. **Desaprote as seguintes variáveis no ambiente da concha em que executará os `az` comandos.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Criar um grupo de recursos.**

    Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados do grupo de recursos são armazenados, é também onde os seus recursos funcionam em Azure se você não especificar outra região durante a criação de recursos. Criar um grupo de recursos utilizando o [grupo az criar](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) comando.

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    A saída de exemplo a seguir mostra o grupo de recursos criado com sucesso:

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

    Os clusters Azure Red Hat OpenShift que executam o OpenShift 4 requerem uma rede virtual com duas sub-redes vazias, para os nós de mestre e trabalhador.

    Crie uma nova rede virtual no mesmo grupo de recursos que criou anteriormente:

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

3. **Adicione uma sub-rede vazia para os nós mestres.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Adicione uma sub-rede vazia para os nós dos trabalhadores.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Desative as políticas de ponto final privado da sub-rede](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) na sub-rede principal.** Isto é necessário para ser capaz de conectar e gerir o cluster.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Criar o cluster

Executar o seguinte comando para criar um cluster. Se optar por utilizar qualquer uma das seguintes opções, modifique o comando em conformidade:
* Opcionalmente, pode [passar o seu segredo de puxar o Chapéu Vermelho,](#get-a-red-hat-pull-secret-optional) o que permite ao seu cluster aceder aos registos de contentores do Red Hat juntamente com conteúdo adicional. Adicione o `--pull-secret @pull-secret.txt` argumento ao seu comando.
* Opcionalmente, pode [utilizar um domínio personalizado.](#prepare-a-custom-domain-for-your-cluster-optional) Adicione o `--domain foo.example.com` argumento ao seu comando, substituindo-o `foo.example.com` pelo seu próprio domínio personalizado.

> [!NOTE]
> Se adicionar argumentos opcionais ao seu comando, certifique-se de encerrar o argumento na linha anterior do comando com uma faixa de retrocesso.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

Após a execução do `az aro create` comando, normalmente demora cerca de 35 minutos a criar um cluster.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Configurar os pré-requisitos e criar a rede virtual e sub-redes necessárias
> * Implementar um cluster

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Ligue-se a um cluster Azure Red Hat OpenShift](tutorial-connect-cluster.md)
