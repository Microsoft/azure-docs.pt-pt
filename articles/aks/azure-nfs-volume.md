---
title: Criar volume de servidor NFS Ubuntu Linux
titleSuffix: Azure Kubernetes Service
description: Saiba como criar manualmente um volume de servidor NFS Ubuntu Linux para utilização com cápsulas no Serviço Azure Kubernetes (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 4e817d572a98ffb8135adf58d13f50ccacbc8746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86251999"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Criar e utilizar manualmente um volume de servidor Linux (Sistema de Ficheiros de Rede) com serviço Azure Kubernetes (AKS)
A partilha de dados entre contentores é frequentemente um componente necessário dos serviços e aplicações à base de contentores. Você geralmente tem várias cápsulas que precisam de acesso à mesma informação em um volume persistente externo.    
Embora os ficheiros Azure sejam uma opção, a criação de um Servidor NFS num VM Azure é outra forma de armazenamento partilhado persistente. 

Este artigo irá mostrar-lhe como criar um Servidor NFS numa máquina virtual Ubuntu. E também dar aos seus contentores AKS acesso a este sistema de ficheiros partilhados.

## <a name="before-you-begin"></a>Antes de começar
Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um Cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

O seu Cluster AKS terá de viver nas mesmas redes virtuais ou espreitadas que o Servidor NFS. O cluster deve ser criado num VNET existente, que pode ser o mesmo VNET que o seu VM.

Os passos para configurar com um VNET existente são descritos na documentação: criar cluster [AKS no VNET existente][aks-virtual-network] e [ligar redes virtuais com o esprevamento VNET][peer-virtual-networks]

Também assume que criou uma Máquina Virtual Ubuntu Linux (por exemplo, 18.04 LTS). As configurações e o tamanho podem ser do seu agrado e podem ser implementados através do Azure. Para o arranque rápido do Linux, consulte a [gestão da Linux VM][linux-create].

Se implementar primeiro o seu Cluster AKS, o Azure preencherá automaticamente o campo de rede virtual ao implementar a sua máquina Ubuntu, fazendo-a viver dentro do mesmo VNET. Mas se quiser trabalhar com redes espreitadas, consulte a documentação acima.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Implantação do Servidor NFS numa Máquina Virtual
Aqui está o script para configurar um Servidor NFS dentro da sua máquina virtual Ubuntu:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
O servidor reiniciará (por causa do script) e poderá montar o Servidor NFS em AKS.

>[!IMPORTANT]  
>Certifique-se de que substitui o **AKS_SUBNET** pelo correto do seu cluster ou então "*" abrirá o seu Servidor NFS a todas as portas e ligações.

Depois de criar o seu VM, copie o script acima num ficheiro. Em seguida, pode movê-lo da sua máquina local, ou onde quer que o script esteja, para o VM usando: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Uma vez que o seu script esteja no seu VM, você pode ssh no VM e executá-lo através do comando:
```console
sudo ./nfs-server-setup.sh
```
Se a sua execução falhar devido a um erro negado por uma permissão, desembarate a permissão de execução através do comando:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Ligação do Cluster AKS ao Servidor NFS
Podemos ligar o Servidor NFS ao nosso cluster, fornecendo um volume persistente e uma reivindicação de volume persistente que especifica como aceder ao volume.

É necessário ligar os dois serviços nas mesmas redes virtuais ou espreitadas. As instruções para a configuração do cluster no mesmo VNET estão aqui: [Criando cluster AKS no VNET existente][aks-virtual-network]

Uma vez que estejam na mesma rede virtual (ou espreitadas), é necessário providenciar um volume persistente e uma reivindicação de volume persistente no seu Cluster AKS. Os contentores podem então montar a unidade NFS até ao seu diretório local.

Aqui está um exemplo de definição de Kubernetes para o volume persistente (esta definição pressupõe que o seu cluster e VM estão no mesmo VNET):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Substitua **NFS_INTERNAL_IP**, **NFS_NAME** e **NFS_EXPORT_FILE_PATH** por informações do NFS Server.

Também vai precisar de um ficheiro de reclamação de volume persistente. Aqui está um exemplo do que incluir:

>[!IMPORTANT]  
>**"StorageClassName"** tem de permanecer uma corda vazia ou a alegação não funciona.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Resolução de problemas
Se não conseguir ligar-se ao servidor a partir de um cluster, um problema pode ser o diretório exportado, ou o seu progenitor, não tem permissões suficientes para aceder ao servidor.

Verifique se tanto o seu diretório de exportação como o seu directório-mãe têm 777 permissões.

Pode verificar permissões executando o comando abaixo e os diretórios devem ter permissões *'drwxrwxrwx'*
```console
ls -l
```

## <a name="more-information"></a>Mais informações
Para obter uma passagem completa ou para ajudá-lo a depurar a configuração do seu Servidor NFS, aqui está um tutorial aprofundado:
  - [NFS Tutorial][nfs-tutorial]

## <a name="next-steps"></a>Passos seguintes

Para as melhores práticas associadas, consulte [as melhores práticas de armazenamento e backups em AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: ./configure-kubenet.md#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: ../virtual-network/tutorial-connect-virtual-networks-portal.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
