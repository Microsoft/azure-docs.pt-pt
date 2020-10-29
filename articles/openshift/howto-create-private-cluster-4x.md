---
title: Crie um aglomerado privado Azure Red Hat OpenShift 4
description: Saiba como criar um cluster privado Azure Red Hat OpenShift com openshift 4
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: georgewallace
ms.author: gwallace
keywords: aro, openshift, az aro, chapéu vermelho, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3864d48399f00d5cfbdfa0a94939be0d88a73322
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928064"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Crie um aglomerado privado Azure Red Hat OpenShift 4

Neste artigo, você vai preparar o seu ambiente para criar clusters privados Azure Red Hat OpenShift executando OpenShift 4. Vai aprender a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e criar a rede virtual e sub-redes necessárias
> * Implementar um cluster com um ponto final privado do servidor API e um controlador de entrada privada

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Antes de começar

### <a name="register-the-resource-providers"></a>Registar os fornecedores de recursos

1. Se tiver várias subscrições do Azure, especifique o ID de subscrição relevante:

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registar o `Microsoft.RedHatOpenShift` fornecedor de recursos:

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```

1. Registar o `Microsoft.Compute` fornecedor de recursos:

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```

1. Registar o `Microsoft.Storage` fornecedor de recursos:

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Obtenha um segredo de puxar o chapéu vermelho (opcional)

Um segredo de puxar o chapéu vermelho permite ao seu cluster aceder aos registos de contentores do Red Hat juntamente com conteúdo adicional. Este passo é opcional, mas recomendado.

1. **[Vá ao seu portal de clusters Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) e faça login.**

   Terá de iniciar sessão na sua conta Red Hat ou criar uma nova conta Red Hat com o seu email de negócios e aceitar os termos e condições.

2. **Clique em Baixar O segredo de puxar.**

Mantenha o ficheiro guardado `pull-secret.txt` em algum lugar seguro - será usado em cada criação de cluster.

Ao executar o `az aro create` comando, pode fazer referência ao seu segredo de puxar utilizando o `--pull-secret @pull-secret.txt` parâmetro. Execute `az aro create` a partir do diretório onde guardou o seu `pull-secret.txt` ficheiro. Caso contrário, `@pull-secret.txt` `@<path-to-my-pull-secret-file` substitua-o por .

Se estiver a copiar o seu segredo de puxar ou a fazê-lo referenciar noutros scripts, o seu segredo de puxar deve ser formatado como uma cadeia JSON válida.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Criar uma rede virtual contendo duas sub-redes vazias

Em seguida, irá criar uma rede virtual contendo duas sub-redes vazias.

1. **Desa estarda as seguintes variáveis.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **Criar um grupo de recursos**

    Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados do grupo de recursos são armazenados, é também onde os seus recursos funcionam em Azure se você não especificar outra região durante a criação de recursos. Criar um grupo de recursos utilizando o comando [az-group-create].

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

5. **[Desative as políticas de ponto final privado da sub-rede](../private-link/disable-private-link-service-network-policy.md) na sub-rede principal.** Isto é necessário para ser capaz de conectar e gerir o cluster.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Criar o cluster

Executar o seguinte comando para criar um cluster. Opcionalmente, pode [passar o seu segredo de puxar o Chapéu Vermelho,](#get-a-red-hat-pull-secret-optional) o que permite ao seu cluster aceder aos registos de contentores do Red Hat juntamente com conteúdo adicional.

>[!NOTE]
> Se estiver a copiar/colar comandos e utilizar um dos parâmetros opcionais, certifique-se de que apaga as hashtags iniciais e o texto de comentário de fuga. Além disso, feche o argumento na linha anterior do comando com uma faixa de recuo.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Após a execução do `az aro create` comando, normalmente demora cerca de 35 minutos a criar um cluster.

>[!IMPORTANT]
> Se optar por especificar um domínio personalizado, por exemplo **foo.example.com,** a consola OpenShift estará disponível num URL como `https://console-openshift-console.apps.foo.example.com` , em vez do domínio incorporado `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .
>
> Por defeito, o OpenShift utiliza certificados auto-assinados para todas as rotas criadas em `*.apps.<random>.<location>.aroapp.io` .  Se escolher DNS personalizados, depois de se ligar ao cluster, terá de seguir a documentação OpenShift para [configurar um CA personalizado para o seu controlador](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) de entrada e CA personalizado para o seu servidor [API.](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)

## <a name="connect-to-the-private-cluster"></a>Conecte-se ao cluster privado

Pode iniciar sessão no cluster utilizando o `kubeadmin` utilizador.  Executar o seguinte comando para encontrar a senha para o `kubeadmin` utilizador.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
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
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Para se ligar a um cluster openShift de chapéu vermelho Azure privado, terá de executar o seguinte passo a partir de um anfitrião que esteja na Rede Virtual que criou ou numa Rede Virtual que é [espreitada](../virtual-network/virtual-network-peering-overview.md) com a Rede Virtual para a qual o cluster foi implantado.

Lance o URL da consola num browser e faça login usando as `kubeadmin` credenciais.

![Screenshot que mostra o ecrã de login Azure Red Hat OpenShift.](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Instale o CLI OpenShift

Uma vez iniciado sessão na Consola Web OpenShift, clique no **?** no topo direito e, em seguida, em **Ferramentas de Linha de Comando** . Descarregue o desbloqueio apropriado para a sua máquina.

![Ecrã de login Azure Red Hat OpenShift](media/aro4-download-cli.png)

Também pode descarregar a versão mais recente do CLI apropriada para a sua máquina a partir de <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> .

## <a name="connect-using-the-openshift-cli"></a>Conecte-se usando o CLI OpenShift

Recupere o endereço do servidor API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Para se ligar a um cluster openShift de chapéu vermelho Azure privado, terá de executar o seguinte passo a partir de um anfitrião que esteja na Rede Virtual que criou ou numa Rede Virtual que é [espreitada](../virtual-network/virtual-network-peering-overview.md) com a Rede Virtual para a qual o cluster foi implantado.

Faça login no servidor API do cluster OpenShift utilizando o seguinte comando. **\<kubeadmin password>** Substitua-a pela palavra-passe que acabou de recuperar.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, foi implantado um cluster Azure Red Hat OpenShift com openshift 4. Aprendeu a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e criar a rede virtual e sub-redes necessárias
> * Implementar um cluster
> * Conecte-se ao cluster usando o `kubeadmin` utilizador

Avance para o próximo artigo para aprender a configurar o cluster para autenticação utilizando o Azure Ative Directory.


* [Configure a autenticação com o Azure Ative Directy utilizando a linha de comando](configure-azure-ad-cli.md)


* [Configurar a autenticação com o Azure Ative Directory utilizando o portal Azure e a consola web OpenShift](configure-azure-ad-cli.md)
