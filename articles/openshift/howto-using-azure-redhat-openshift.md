---
title: Criar um OpenShift 4.3 do chapéu vermelho azul [ Cluster 4.3 ] Microsoft Docs
description: Crie um cluster com o Azure Red Hat OpenShift 3.11
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, chapéu vermelho, cli
ms.openlocfilehash: 81f8edb42be1f73692062d36440890ef5a1e7c9a
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899311"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Criar, aceder e gerir um Cluster OpenShift 4.3 do Chapéu Vermelho Azure

> [!IMPORTANT]
> O Azure Red Hat OpenShift (ARO) 4.3 é oferecido em pré-visualização. As funcionalidades de pré-visualização são autosserviço e são fornecidas como estão disponíveis e estão excluídas do acordo de nível de serviço (SLA) e da garantia limitada. Portanto, as características não são destinadas ao uso da produção.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para criar um cluster Azure Red Hat OpenShift 4.3:

- Versão Azure CLI 2.0.72 ou superior
  
- A extensão 'az aro'

- Uma rede virtual contendo duas subredes vazias, cada uma sem grupo de segurança de rede anexado.  O seu aglomerado será implantado nestas subredes.

- Uma aplicação aAD de cluster (ID do cliente e secreto) e diretor de serviço, ou permissões AAD suficientes para `az aro create` criar uma aplicação e diretor de serviço AAD para si automaticamente.

- O diretor de serviço rp e o diretor de serviço de cluster devem ter cada um a função de Contribuinte na rede virtual cluster.  Se tiver a função de "Administrador de Acesso ao Utilizador" na rede virtual, `az aro create` irá configurar automaticamente as atribuições de funções para si.

### <a name="install-the-az-aro-extension"></a>Instale a extensão 'az aro'
A extensão `az aro` permite criar, aceder e eliminar os clusters OpenShift do Chapéu Vermelho Azure diretamente da linha de comando utilizando o Azure CLI.

> [!Note] 
> A extensão `az aro` está atualmente em pré-visualização. Pode ser alterado ou removido numa futura versão.
> Para optar pela pré-visualização de extensão `az aro`, é necessário registar o fornecedor de recursos `Microsoft.RedHatOpenShift`.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Iniciar sessão no Azure.

   ```console
   az login
   ```

2. Executar o seguinte comando para instalar a extensão `az aro`:

   ```console
   az extension add --source https://arosvc.blob.core.windows.net/az-preview/aro-0.1.0-py2.py3-none-any.whl
   ```

3. Verifique se a extensão ARO está registada.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Criar uma rede virtual contendo duas subredes vazias

Siga estes passos para criar uma rede virtual contendo duas subredes vazias.

1. Desconte as seguintes variáveis.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Crie um grupo de recursos para o seu cluster.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Criar a rede virtual.

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Adicione duas subredes vazias à sua rede virtual.

   ```console
    for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Desative as políticas de rede para o Private Link Service na sua rede virtual e subnets. Esta é uma exigência para que o serviço ARO aceda e gere o cluster.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Criar um cluster

Executar o seguinte comando para criar um cluster.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker"
```

>[!NOTE]
> Normalmente demora cerca de 35 minutos a criar um aglomerado.

## <a name="access-the-cluster-console"></a>Aceda à consola de cluster

Pode encontrar o URL da consola de cluster (do formulário `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) sob o recurso de cluster OpenShift 4.3 do Chapéu Vermelho Azure. Executar o seguinte comando para visualizar o recurso:

```console
az aro list -o table
```

Pode iniciar sessão no cluster utilizando o utilizador `kubeadmin`.  Executar o seguinte comando para encontrar a palavra-passe para o utilizador `kubeadmin`:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Eliminar um cluster

Executar o seguinte comando para apagar um cluster.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```