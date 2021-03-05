---
title: Integrar ficheiros Azure NetApp com o Serviço Azure Kubernetes
description: Saiba como integrar ficheiros Azure NetApp com o Serviço Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 1d5aa8232b5d0aaa68e6d7e3dcbb9a7d70d0e8f8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182150"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrar ficheiros Azure NetApp com o Serviço Azure Kubernetes

[Azure NetApp Files][anf] é um serviço de armazenamento de ficheiros de classe empresarial, de alto desempenho e medido em execução no Azure. Este artigo mostra-lhe como integrar ficheiros Azure NetApp com o Serviço Azure Kubernetes (AKS).

## <a name="before-you-begin"></a>Antes de começar
Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

> [!IMPORTANT]
> O seu cluster AKS também deve estar [numa região que suporta ficheiros Azure NetApp.][anf-regions]

Também precisa da versão Azure CLI 2.0.59 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

### <a name="limitations"></a>Limitações

Aplicam-se as seguintes limitações quando utiliza ficheiros Azure NetApp:

* O Azure NetApp Files só está disponível [nas regiões de Azure selecionadas.][anf-regions]
* Antes de poder utilizar ficheiros Azure NetApp, tem de ter acesso ao serviço Azure NetApp Files. Para se candidatar ao acesso, pode utilizar o formulário de submissão da [lista de espera Azure NetApp Files][anf-waitlist] ou ir para https://azure.microsoft.com/services/netapp/#getting-started . Não pode aceder ao serviço Azure NetApp Files até receber o e-mail oficial de confirmação da equipa do Azure NetApp Files.
* Após a implantação inicial de um cluster AKS, apenas é suportado o fornecimento estático para ficheiros Azure NetApp.
* Para utilizar o provisionamento dinâmico com os Ficheiros Azure NetApp, instale e configuure a versão 19.07 do [NetApp Trident](https://netapp-trident.readthedocs.io/) ou posterior.

## <a name="configure-azure-netapp-files"></a>Configurar ficheiros Azure NetApp

> [!IMPORTANT]
> Antes de poder registar o fornecedor de recursos  *Microsoft.NetApp,* tem de preencher o formulário de submissão da [lista de espera Azure NetApp Files][anf-waitlist] ou ir para a sua https://azure.microsoft.com/services/netapp/#getting-started subscrição. Não é possível registar o recurso até receber o e-mail oficial de confirmação da equipa do Azure NetApp Files.

Registe-se o fornecedor de recursos *Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Isto pode levar algum tempo para ser concluído.

Quando cria uma conta Azure NetApp para utilização com AKS, precisa de criar a conta no grupo de recursos **de nó.** Primeiro, obtenha o nome do grupo de recursos com o comando [az aks show][az-aks-show] e adicione o `--query nodeResourceGroup` parâmetro de consulta. O exemplo a seguir obtém o grupo de recursos de nó para o cluster AKS chamado *myAKSCluster* no nome do grupo de recursos *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Crie uma conta Azure NetApp Files no grupo de recursos **de nó** e na mesma região que o seu cluster AKS utilizando a [conta az netappfiles criar][az-netappfiles-account-create]. O exemplo a seguir cria uma conta denominada *myaccount1* no grupo de recursos *MC_myResourceGroup_myAKSCluster_eastus* e região *leste:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Crie um novo pool de capacidade utilizando [a az netappfiles pool create][az-netappfiles-pool-create]. O exemplo a seguir cria uma nova piscina de capacidade chamada *mypool1* com 4 TB em tamanho e nível de serviço *Premium:*

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Crie uma sub-rede para [delegar em Ficheiros Azure NetApp][anf-delegate-subnet] utilizando [a sub-rede de rede Az vnet create][az-network-vnet-subnet-create]. *Esta sub-rede deve estar na mesma rede virtual que o seu cluster AKS.*

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

Crie um volume utilizando [o volume de netappfiles az][az-netappfiles-volume-create].

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
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

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
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Criar o PersistenteVolume

Enusteça os detalhes do seu volume usando [a az netappfiles volume show][az-netappfiles-volume-show]

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

Crie um `pv-nfs.yaml` Conjunto Persistente. `path`Substitua-a pela *criaçãoToken* e `server` pelo *ipAddress* do comando anterior. Por exemplo:

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
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Atualize o *servidor* e *o caminho* para os valores do seu volume NFS (Sistema de Ficheiros de Rede) que criou no passo anterior. Crie o PersistenteVolume com o [comando de aplicação de kubectl:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Verifique o *estado* do PersistenteVolume *disponível utilizando* o comando [de descreveção kubectl:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Criar o PersistenteVolumeClaim

Crie um `pvc-nfs.yaml` Conjunto Persistente. Por exemplo:

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

Crie o comando PersistenteVolumeClaim com o [comando de aplicação de kubectl:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

Verifique o *estado* do PersistenteVolumeClaim está *ligado* utilizando o comando [de descreveção kubectl:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Monte com uma vagem

Crie um `nginx-nfs.yaml` pod que defina o PersistenteVolumeClaim. Por exemplo:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

Crie o pod com o [comando de aplicação de kubectl:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Verifique se a cápsula está *a correr* utilizando o comando de [descrever kubectl:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Verifique se o seu volume foi montado na cápsula utilizando um [executivo de kubectl][kubectl-exec] para ligar à cápsula e, em seguida, `df -h` para verificar se o volume está montado.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre ficheiros Azure NetApp, consulte [o que é Azure NetApp Files][anf]. Para obter mais informações sobre a utilização de NFS com AKS, consulte [manualmente criar e utilizar um volume de servidor Linux NFS (Sistema de Ficheiros de Rede) com o Serviço Azure Kubernetes (AKS)][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
