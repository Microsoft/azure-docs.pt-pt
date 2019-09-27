---
title: Integrar Azure NetApp Files com o serviço kubernetes do Azure
description: Saiba como integrar o Azure NetApp Files com o serviço kubernetes do Azure
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 84192a831e3b1f24e20eb07a6c8695516c28970f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329334"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrar Azure NetApp Files com o serviço kubernetes do Azure

[Azure NetApp files][anf] é um serviço de armazenamento de arquivos medido de alto desempenho e de classe empresarial em execução no Azure. Este artigo mostra como integrar Azure NetApp Files com o AKS (serviço kubernetes do Azure).

## <a name="before-you-begin"></a>Antes de começar
Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

> [!IMPORTANT]
> O cluster AKS também deve estar [em uma região com suporte a Azure NetApp files][anf-regions].

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você usa Azure NetApp Files:

* Azure NetApp Files só está disponível [em regiões do Azure selecionadas][anf-regions].
* Para poder usar Azure NetApp Files, você deve ter acesso ao serviço Azure NetApp Files. Para se candidatar ao acesso, você pode usar o [formulário de envio Azure NetApp files Waitlist][anf-waitlist]. Você não pode acessar o serviço de Azure NetApp Files até receber o email de confirmação oficial da equipe de Azure NetApp Files.
* Seu serviço de Azure NetApp Files deve ser criado na mesma rede virtual que o cluster AKS.
* Somente o provisionamento estático para Azure NetApp Files tem suporte em AKS.

## <a name="configure-azure-netapp-files"></a>Configurar Azure NetApp Files

> [!IMPORTANT]
> Antes de poder registrar o provedor de recursos *Microsoft. NetApp* , você deve concluir o [formulário de envio Azure NetApp files Waitlist][anf-waitlist] para sua assinatura. Você não pode registrar o recurso fornecer até receber o email de confirmação oficial da equipe de Azure NetApp Files.

Registre o provedor de recursos *Microsoft. NetApp* :

```azure-cli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Isso pode levar algum tempo para ser concluído.

Ao criar uma conta do Azure NetApp para uso com o AKS, você precisa criar a conta no grupo de recursos do **nó** . Primeiro, obtenha o nome do grupo de recursos com o comando [AZ AKs show][az-aks-show] e adicione o parâmetro de consulta `--query nodeResourceGroup`. O exemplo a seguir obtém o grupo de recursos de nó para o cluster AKS chamado *myAKSCluster* no nome do grupo de recursos *MyResource*Group:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Crie uma conta de Azure NetApp Files no grupo de recursos do **nó** e a mesma região que o cluster AKs usando [AZ netappfiles Account Create][az-netappfiles-account-create]. O exemplo a seguir cria uma conta chamada *myaccount1* no grupo de recursos *MC_myResourceGroup_myAKSCluster_eastus* e na região *eastus* :

```azure-cli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Crie um novo pool de capacidade usando [AZ netappfiles pool Create][az-netappfiles-pool-create]. O exemplo a seguir cria um novo pool de capacidade chamado *mypool1* com 4 TB de tamanho e nível de serviço *Premium* :

```azure-cli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Crie uma sub-rede para [delegar a Azure NetApp files][anf-delegate-subnet] usando [AZ Network vnet subnet Create][az-network-vnet-subnet-create]. *Essa sub-rede deve estar na mesma rede virtual que o cluster AKS.*

```azure-cli
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

Crie um volume usando [AZ netappfiles volume Create][az-netappfiles-volume-create].

```azure-cli
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

## <a name="create-the-persistentvolume"></a>Criar o PersistentVolume

Listar os detalhes do seu volume usando [AZ netappfiles volume show][az-netappfiles-volume-show]
```azure-cli
$ az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"

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

Crie um `pv-nfs.yaml` definindo um PersistentVolume. Substitua `path` por *creationToken* e `server` por *ipAddress* do comando anterior. Por exemplo:

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

Atualize o *servidor* e o *caminho* para os valores do volume NFS (sistema de arquivos de rede) criado na etapa anterior. Crie o PersistentVolume com o comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pv-nfs.yaml
```

Verifique se o *status* do PersistentVolume está *disponível* usando o comando [kubectl de descrição][kubectl-describe] :

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Criar o PersistentVolumeClaim

Crie um `pvc-nfs.yaml` definindo um PersistentVolume. Por exemplo:

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

Crie o PersistentVolumeClaim com o comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pvc-nfs.yaml
```

Verifique se o *status* do PersistentVolumeClaim está *associado* usando o comando [kubectl de descrição][kubectl-describe] :

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Montar com um pod

Crie um `nginx-nfs.yaml` definindo um pod que usa o PersistentVolumeClaim. Por exemplo:

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

Crie o Pod com o comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f nginx-nfs.yaml
```

Verifique se o Pod está *em execução* usando o comando [kubectl de descrição][kubectl-describe] :

```console
kubectl describe pod nginx-nfs
```

Verifique se o volume foi montado no pod usando [kubectl exec][kubectl-exec] para se conectar ao pod e, em seguida, `df -h` para verificar se o volume está montado.

```console
$ kubectl exec -it nginx-nfs -- bash

root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre Azure NetApp Files, consulte [o que é Azure NetApp files][anf]. Para obter mais informações sobre como usar o NFS com o AKS, consulte [criar e usar manualmente um volume de servidor NFS (Network File System) do Linux com o AKs (serviço kubernetes do Azure)][aks-nfs].


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
