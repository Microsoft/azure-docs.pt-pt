---
title: Tutorial - Criar um aglomerado Azure Red Hat OpenShift 4
description: Saiba como criar um cluster Microsoft Azure Red Hat OpenShift utilizando o Azure CLI
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 10/26/2020
ms.openlocfilehash: b690d3b3c29d2943e28a0992730d932b35c20734
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653045"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: Criar um aglomerado Azure Red Hat OpenShift 4

Neste tutorial, parte um de três, você vai preparar o seu ambiente para criar um cluster Azure Red Hat OpenShift executando OpenShift 4, e criar um cluster. Vai aprender a:
> [!div class="checklist"]
> * Configurar os pré-requisitos 
> * Criar a rede virtual e sub-redes necessárias
> * Implementar um cluster

## <a name="before-you-begin"></a>Antes de começar

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão 2.6.0 ou mais tarde do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest).

O Azure Red Hat OpenShift requer um mínimo de 40 núcleos para criar e executar um cluster OpenShift. A quota de recursos Azure por defeito para uma nova subscrição do Azure não satisfaz este requisito. Para solicitar um aumento do seu limite de recursos, consulte [quota standard: Aumentar os limites por série VM](../azure-portal/supportability/per-vm-quota-requests.md).

A ARO pull secret não altera o custo da licença RH OpenShift para a ARO.

### <a name="verify-your-permissions"></a>Verificar as permissões

Durante este tutorial, irá criar um grupo de recursos, que conterá a rede virtual para o cluster. Tem de ter permissões de Administrador de Acesso ao Utilizador ou ao Proprietário, quer diretamente na rede virtual, quer no grupo de recursos ou na subscrição que o contenha.

Também necessitará de permissões suficientes do Azure Ative Directory para a ferramenta criar um principal de aplicação e serviço em seu nome para o cluster.

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

1. [Navegue para o seu portal de clusters Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) e faça login.

   Terá de iniciar sessão na sua conta Red Hat ou criar uma nova conta Red Hat com o seu email de negócios e aceitar os termos e condições.

1. Clique **em Baixar O segredo de puxar** e baixar um segredo de puxar para ser usado com o seu cluster ARO.

    Mantenha o ficheiro guardado `pull-secret.txt` num lugar seguro. O ficheiro será usado em cada criação de cluster se precisar de criar um cluster que inclua amostras ou operadores para Chapéu Vermelho ou parceiros certificados.

    Ao executar o `az aro create` comando, pode fazer referência ao seu segredo de puxar utilizando o `--pull-secret @pull-secret.txt` parâmetro. Execute `az aro create` a partir do diretório onde guardou o seu `pull-secret.txt` ficheiro. Caso contrário, `@pull-secret.txt` `@/path/to/my/pull-secret.txt` substitua-o por .

    Se estiver a copiar o seu segredo de puxar ou a fazê-lo referenciar noutros scripts, o seu segredo de puxar deve ser formatado como uma cadeia JSON válida.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Prepare um domínio personalizado para o seu cluster (opcional)

Ao executar o `az aro create` comando, pode especificar um domínio personalizado para o seu cluster utilizando o `--domain foo.example.com` parâmetro.

Se fornecer um domínio personalizado para o seu cluster, note os seguintes pontos:

* Depois de criar o seu cluster, deve criar 2 registos DNS A no seu servidor DNS para o `--domain` especificado:
    * **api** - apontando para o endereço IP do servidor api
    * **\* .apps** - apontando para o endereço IP ingresss
    * Recupere estes valores executando o seguinte comando após a criação do cluster: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` .

* A consola OpenShift estará disponível num URL `https://console-openshift-console.apps.example.com` como, em vez do domínio `https://console-openshift-console.apps.<random>.<location>.aroapp.io` incorporado.

* Por predefinição, o OpenShift utiliza certificados auto-assinados para todas as rotas criadas em domínios `*.apps.example.com` personalizados.  Se optar por utilizar DNS personalizados após a ligação ao cluster, terá de seguir a documentação OpenShift para [configurar um CA personalizado para o seu controlador de entrada](https://docs.openshift.com/container-platform/4.6/security/certificates/replacing-default-ingress-certificate.html) e um CA personalizado para o seu servidor [API.](https://docs.openshift.com/container-platform/4.6/security/certificates/api-server.html)

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Criar uma rede virtual contendo duas sub-redes vazias

Em seguida, irá criar uma rede virtual contendo duas sub-redes vazias. Se tiver uma rede virtual existente que atenda às suas necessidades, pode saltar este passo.

1. **Desaprote as seguintes variáveis no ambiente da concha em que executará os `az` comandos.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

2. **Criar um grupo de recursos.**

   Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados do grupo de recursos são armazenados, e é também onde os seus recursos funcionam em Azure se você não especificar outra região durante a criação de recursos. Criar um grupo de recursos utilizando o [grupo az criar](/cli/azure/group?view=azure-cli-latest#az-group-create) comando.
    
   > [!NOTE] 
   > O Azure Red Hat OpenShift não está disponível em todas as regiões onde um grupo de recursos Azure pode ser criado. Consulte [as regiões disponíveis](https://azure.microsoft.com/en-gb/global-infrastructure/services/?products=openshift) para obter informações sobre o suporte do Azure Red Hat OpenShift.

   ```azurecli-interactive
   az group create \
     --name $RESOURCEGROUP \
     --location $LOCATION
   ```

   A saída de exemplo a seguir mostra o grupo de recursos criado com sucesso:

   ```json
   {
     "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
     "location": "eastus",
     "name": "aro-rg",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "type": "Microsoft.Resources/resourceGroups"
   }
   ```

2. **Criar uma rede virtual.**

   Os clusters Azure Red Hat OpenShift que executam o OpenShift 4 requerem uma rede virtual com duas sub-redes vazias, para os nós de mestre e trabalhador. Pode criar uma nova rede virtual para isso ou utilizar uma rede virtual existente.

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
       "dhcpOptions": {
         "dnsServers": []
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

5. **[Desative as políticas de ponto final privado da sub-rede](../private-link/disable-private-link-service-network-policy.md) na sub-rede principal.** Isto é necessário para que o serviço seja capaz de ligar e gerir o cluster.

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
