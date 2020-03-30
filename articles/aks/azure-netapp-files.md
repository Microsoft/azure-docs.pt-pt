---
title: Integrar ficheiros Azure NetApp com o Serviço Azure Kubernetes
description: Saiba como integrar ficheiros Azure NetApp com o Serviço Azure Kubernetes
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273756"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrar ficheiros Azure NetApp com o Serviço Azure Kubernetes

[O Azure NetApp Files][anf] é um serviço de armazenamento de ficheiros medido de classe empresarial, de alto desempenho, que funciona no Azure. Este artigo mostra-lhe como integrar ficheiros Azure NetApp com o Serviço Azure Kubernetes (AKS).

## <a name="before-you-begin"></a>Antes de começar
Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

> [!IMPORTANT]
> O seu cluster AKS também deve estar [numa região que suporta ficheiros Azure NetApp][anf-regions].

Também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

### <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se quando utiliza ficheiros Azure NetApp:

* Os Ficheiros Azure NetApp só estão disponíveis [em regiões azure selecionadas.][anf-regions]
* Antes de poder utilizar ficheiros Azure NetApp, deve ter acesso ao serviço Deficheiros Azure NetApp. Para se candidatar ao acesso, pode utilizar o formulário de submissão da lista de espera do [Azure NetApp Files][anf-waitlist]. Não pode aceder ao serviço Deficheiros Azure NetApp até receber o e-mail oficial de confirmação da equipa de Ficheiros Azure NetApp.
* O seu serviço Azure NetApp Files deve ser criado na mesma rede virtual que o seu cluster AKS.
* Após a implementação inicial de um cluster AKS, apenas é suportado o fornecimento estático para ficheiros Azure NetApp.
* Para utilizar o fornecimento dinâmico com ficheiros Azure NetApp, instale e configure a versão 19.07 do [NetApp Trident](https://netapp-trident.readthedocs.io/) ou posterior.

## <a name="configure-azure-netapp-files"></a>Configure Ficheiros NetApp Azure

> [!IMPORTANT]
> Antes de poder registar o fornecedor de recursos *Microsoft.NetApp,* tem de preencher o formulário de submissão da lista de espera do [Azure NetApp Files][anf-waitlist] para a sua subscrição. Não pode registar o recurso providenciado até receber o e-mail oficial de confirmação da equipa de Ficheiros Azure NetApp.

Registe o fornecedor de recursos *Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Isto pode levar algum tempo a ser concluído.

Quando cria uma conta Azure NetApp para uso com AKS, precisa de criar a conta no grupo de recursos do **nó.** Primeiro, obtenha o nome do grupo de recursos com `--query nodeResourceGroup` o comando [az aks mostrar][az-aks-show] e adicionar o parâmetro de consulta. O exemplo seguinte obtém o grupo de recursos do nó para o cluster AKS chamado *myAKSCluster* no nome do grupo de recursos *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Crie uma conta Azure NetApp Files no grupo de recursos do **nó** e na mesma região que o seu cluster AKS utilizando a [conta az netappfiles criar][az-netappfiles-account-create]. O exemplo seguinte cria uma conta chamada *myaccount1* no grupo de recursos *MC_myResourceGroup_myAKSCluster_eastus* e região *oriental:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Criar um novo conjunto de capacidades utilizando a criação de pool [de az netappfiles][az-netappfiles-pool-create]. O exemplo seguinte cria uma nova piscina de capacidade chamada *mypool1* com 4 TB em tamanho e nível de serviço *Premium:*

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Crie uma subnet para [delegar ficheiros Azure NetApp][anf-delegate-subnet] utilizando a [rede az vnet criar][az-network-vnet-subnet-create]. *Esta sub-rede deve estar na mesma rede virtual que o seu cluster AKS.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Criar um volume utilizando o volume de [ficheiros az netappfiles criar][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Criar o Volume Persistente

Enumera os detalhes do seu volume utilizando [o volume de ficheiros az netappfiles][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Crie `pv-nfs.yaml` um Volume Persistente que defina. Substitua-a `path` pela `server` *criaçãoToken* e pelo *ipAddress* do comando anterior. Por exemplo:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Atualize o *servidor* e *o caminho* para os valores do volume nFS (Sistema de Ficheiros de Rede) que criou no passo anterior. Criar o PersistenteVolume com o [kubectl aplicar][kubectl-apply] comando:

```console
kubectl apply -f pv-nfs.yaml
```

Verifique se o *Estado* do Volume Persistente está *disponível* utilizando o comando [de descrever kubectl:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Criar a Reclamação persistente de volume

Crie `pvc-nfs.yaml` um Volume Persistente que defina. Por exemplo:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Criar o PersistenteVolumeClaim com o [kubectl aplicar][kubectl-apply] comando:

```console
kubectl apply -f pvc-nfs.yaml
```

Verifique se o *Estado* da Reclamação persistente é *ligado* utilizando o comando [de descrever kubectl:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Monte com uma vagem

Crie `nginx-nfs.yaml` uma cápsula que defina a utilização do PersistentVolumeClaim. Por exemplo:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Crie a cápsula com o [kubectl aplique][kubectl-apply] o comando:

```console
kubectl apply -f nginx-nfs.yaml
```

Verifique se a cápsula está *a funcionar* utilizando o comando de [descrever kubectl:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Verifique se o seu volume foi montado na cápsula utilizando o executivo `df -h` [kubectl][kubectl-exec] para ligar à cápsula e, em seguida, para verificar se o volume está montado.

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os Ficheiros Azure NetApp, consulte [o que é o Azure NetApp Files][anf]. Para obter mais informações sobre a utilização de NFS com AKS, consulte [Manualmente criar e utilizar um volume de servidor De NFS (Sistema de Ficheiros de Rede) Linux com o Serviço Azure Kubernetes (AKS)][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
