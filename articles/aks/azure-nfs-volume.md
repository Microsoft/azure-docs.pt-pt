---
title: Criar um servidor do Ubuntu NFS (Network File System) para utilização por pods de Azure Kubernetes Service (AKS)
description: Saiba como criar manualmente um volume de NFS Ubuntu Linux Server para utilização com pods no Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65468503"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Crie manualmente e usar um volume NFS (Network File System) Linux Server com o Azure Kubernetes Service (AKS)
Partilhar dados entre contentores, muitas vezes, é um componente necessário do baseadas em contentores de serviços e aplicações. Normalmente, tem vários pods que precisam de aceder às mesmas informações num volume persistente externo.    
Enquanto os ficheiros do Azure são uma opção, a criação de um servidor NFS numa VM do Azure é outra forma de armazenamento partilhado persistente. 

Este artigo irá mostrar como criar um servidor NFS numa máquina virtual Ubuntu. E também concede o acesso de contentores do AKS a este sistema de ficheiros partilhados.

## <a name="before-you-begin"></a>Antes de começar
Este artigo pressupõe que tem um Cluster do AKS existente. Se precisar de um Cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure] [ aks-quickstart-cli] ou [no portal do Azure][aks-quickstart-portal].

O Cluster do AKS tem de estar as redes virtuais no mesmas ou em modo de peering como o servidor NFS. O cluster tem de ser criado numa VNET existente, que pode ser a mesma VNET como sua VM.

Os passos para configurar com uma VNET existente são descritos na documentação: [criar o Cluster do AKS na VNET existente] [ aks-virtual-network] e [ligar redes virtuais com o peering de VNET][peer-virtual-networks]

Também parte do princípio que criou uma Máquina Virtual do Linux Ubuntu (por exemplo, 18.04 LTS). As definições e tamanho podem ser ao seu gosto e podem ser implementados através do Azure. Início rápido do Linux, veja [gerenciamento de VM do linux][linux-create].

Se implementar o Cluster do AKS em primeiro lugar, Azure irá automaticamente preencher o campo de rede virtual ao implementar a sua máquina de Ubuntu, tornando-os em direto na mesma VNET. Mas se quiser trabalhar com redes com peering em vez disso, consulte a documentação acima.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Implementar o servidor NFS numa máquina Virtual
Aqui está o script para configurar um servidor NFS na sua máquina virtual do Ubuntu:
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
O servidor será reiniciado (por causa de script) e pode montar o servidor de NFS para o AKS

>[!IMPORTANT]  
>Certifique-se substituir a **AKS_SUBNET** pelo correto do seu cluster ou outro "*" abrirá seu servidor de NFS para todas as portas e ligações.

Depois de criar a VM, copie o script acima para um ficheiro. Em seguida, pode movê-la a partir do seu computador local, ou onde quer que o script é, para a VM com: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Assim que o script estiver na sua VM, pode ssh na VM e executá-lo através do comando:
```console
sudo ./nfs-server-setup.sh
```
Se a sua execução falhar devido a um erro de permissão negada, defina a permissão de execução por meio do comando:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Cluster do AKS ao ligar ao servidor NFS
Podemos ligar o servidor NFS para nosso cluster através do aprovisionamento de um volume persistente e de uma afirmação de volume persistente que especifica como aceder ao volume.  
É necessário ligar os dois serviços na mesmas ou em modo de peering redes virtuais. Instruções sobre como configurar o cluster na mesma VNET está aqui: [criar Cluster do AKS na VNET existente][aks-virtual-network]

Assim que eles estão na mesma rede virtual (ou em modo de peering), terá de aprovisionar que um volume persistente e um volume persistente de afirmação no seu Cluster do AKS. Os contentores, em seguida, podem montar a unidade NFS para seu diretório local.

Esta é uma definição de kubernetes de exemplo para o volume persistente (esta definição assume que o cluster e a VM estão na mesma VNET):

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
Substitua **NFS_INTERNAL_IP**, **NFS_NAME** e **NFS_EXPORT_FILE_PATH** com informações do servidor NFS.

Também será necessário um ficheiro de afirmação de volume persistente. Eis um exemplo do que incluem:

>[!IMPORTANT]  
>**"storageClassName"** precisa permanecer vazio cadeia de caracteres ou a afirmação não funcionará.

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
Se não conseguir ligar ao servidor a partir de um cluster, um problema pode ser o diretório exportado, ou o respetivo elemento principal, não tem permissões suficientes para aceder ao servidor.

Certifique-se de que o seu diretório de exportação e o diretório principal tem 777 permissões.

Pode verificar as permissões ao executar o comando abaixo e os diretórios devem ter *'drwxrwxrwx'* permissões:
```console
ls -l
```

## <a name="more-information"></a>Mais informações
Para obter um passo a passo completa ou para ajudar a depurar seu programa de configuração do servidor NFS, este é um tutorial aprofundado:
  - [Tutorial NFS][nfs-tutorial]

## <a name="next-steps"></a>Passos Seguintes

Para as práticas recomendadas associadas, consulte [melhores práticas para o armazenamento e cópias de segurança no AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
