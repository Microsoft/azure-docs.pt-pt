---
title: Compreenda a gestão de armazenamento da Kubernetes no dispositivo Azure Stack Edge Pro Microsoft Docs
description: Descreve como a gestão de armazenamento de Kubernetes ocorre num dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: ff2a473ca008e9b283d03ebb05f35122473d778a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899270"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-pro-gpu-device"></a>Gestão de armazenamento kubernetes no seu dispositivo GPU Azure Stack Edge Pro

No seu dispositivo Azure Stack Edge Pro, é criado um cluster Kubernetes quando configura o papel de computação. Uma vez criado o cluster Kubernetes, as aplicações contentorizadas podem ser implantadas no cluster Kubernetes em casulos. Existem formas distintas de fornecer armazenamento a cápsulas no seu cluster Kubernetes. 

Este artigo descreve os métodos para o armazenamento num cluster Kubernetes em geral e especificamente no contexto do seu dispositivo Azure Stack Edge Pro. 

## <a name="storage-requirements-for-kubernetes-pods"></a>Requisitos de armazenamento para cápsulas Kubernetes

As cápsulas de Kubernetes são apátridas, mas as aplicações que executam são geralmente imponentes. Como as cápsulas podem ser de curta duração, e reiniciam, falham ou se movem entre os nós Kubernetes, os seguintes requisitos devem ser cumpridos para armazenamento associado à cápsula. 

O armazenamento deve:

- Viver fora da cápsula.
- Seja independente do ciclo de vida da vagem.
- Esteja acessível a partir de todos os nós kubernetes.

Para entender como o armazenamento é gerido para Kubernetes, é necessário entender dois recursos da API: 

- **PersistenteVolume (PV)**: Trata-se de um pedaço de armazenamento no cluster Kubernetes. O armazenamento de Kubernetes pode ser estaticamente a provisionado como `PersistentVolume` . Também pode ser dinamicamente a provisionado como  `StorageClass` .

- **PersistenteVolumeClaim (PVC)**: Trata-se de um pedido de armazenamento por parte de um utilizador. Os PVCs consomem recursos PV. Os PVCs podem solicitar o tamanho e os modos de acesso específicos. 

    Uma vez que os utilizadores precisam `PersistentVolumes` de propriedades variadas para diferentes problemas, por isso os administradores de cluster precisam de ser capazes de oferecer uma variedade `PersistentVolumes` disso diferir em mais maneiras do que apenas tamanho e modos de acesso. Para estas necessidades, precisa do `StorageClass` recurso.

O fornecimento de armazenamento pode ser estático ou dinâmico. Cada um dos tipos de provisionamento é discutido nas seguintes secções.

## <a name="staticprovisioning"></a>Provisão estática

Os administradores de clusters Kubernetes podem provisão estática do armazenamento. Para tal, podem utilizar backend de armazenamento com base em sistemas de ficheiros SMB/NFS ou utilizar discos iSCSI que se ligam localmente pela rede num ambiente no local, ou até mesmo utilizar Ficheiros Azure ou Discos Azure na nuvem. Este tipo de armazenamento não é a provisionado por defeito e os administradores de cluster têm de planear e gerir este provisionamento. 
 
Aqui está um diagrama que mostra como o armazenamento estático é consumido em Kubernetes: 

![Provisão estática através de PersistenteVolumes](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

Os seguintes passos ocorrem: 

1. **Armazenamento de provisões**: O administrador de agrupamento prevê o armazenamento. Neste exemplo, o administrador do cluster cria uma ou mais partilhas SMB que criam automaticamente objetos de volume persistentes no cluster Kubernetes correspondentes a estas ações. 

1. **Armazenamento de reclamações**: Você submete uma implementação de PVC que solicita o armazenamento. Esta reivindicação de armazenamento é a PersistentVolumeClaim (PVC). Se o tamanho e o modo de acesso do PV corresponderem ao do PVC, então o PVC está ligado ao PV. O mapa de PVC e PV um a um.

1. **Monte PVC no recipiente**: Uma vez que o PVC esteja ligado ao PV, pode montar este PVC num caminho no seu recipiente. Quando a lógica de aplicação no recipiente lê/escreve de/para este caminho, os dados são escritos no armazenamento SMB.
 

## <a name="dynamicprovisioning"></a>Provisionamento dinâmico

Aqui está um diagrama que mostra como o armazenamento estático é consumido em Kubernetes: 

![Provisionamento dinâmico via StorageClasses](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

Os seguintes passos ocorrem: 

1. **Definir classe de armazenamento**: O administrador de cluster define uma classe de armazenamento dependendo do ambiente de funcionamento do seu cluster Kubernetes. O administrador do cluster também implementa um provisionador, que é mais uma cápsula ou aplicação implantada no cluster Kubernetes. O provisionador tem todos os detalhes para a disponibilização das ações de forma dinâmica.  

1. **Armazenamento de reclamações**: Você submete um pedido que reclamaria o armazenamento. Uma vez que um PVC é criado com esta referência de classe de armazenamento, o provisionador é invocado. 

1. **Armazenamento de provisão dinamicamente**: O provisionador cria dinamicamente a parte associada ao armazenamento de discos locais. Uma vez criada a partilha, também cria um objeto PV automaticamente correspondente a esta partilha.

1. **Montagem em PVC para recipiente**: Uma vez que o PVC esteja ligado ao PV, pode montar o PVC no recipiente num caminho da mesma forma que o provisionamento estático e ler ou escrever na parte.


## <a name="storage-provisioning-on-azure-stack-edge-pro"></a>Fornecimento de armazenamento no Azure Stack Edge Pro

No dispositivo Azure Stack Edge Pro, o fornecimento estático `PersistentVolumes` é criado utilizando as capacidades de armazenamento do dispositivo. Quando disponibiliza uma ação e utiliza a partilha com a opção **de computação Edge,** esta ação cria um recurso PV automaticamente no cluster Kubernetes.

![Criação de ações locais no portal Azure para o provisionamento estático](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

Para utilizar o tiering de nuvem, pode criar uma partilha de nuvem Edge com a opção de computação Utilização com edge. Um PV é novamente criado automaticamente para esta partilha. Quaisquer dados de aplicação que escreva para a partilha Edge estão nivelados para a nuvem. 

![Criação de partilha de nuvem no portal Azure para provisão estática](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

Pode criar partilhas de SMB e NFS para provisões estáticas de PVs no dispositivo Azure Stack Edge Pro. Uma vez que o PV é a provisionado, você submeterá um PVC para reclamar este armazenamento. Aqui está um exemplo de uma implementação de PVC `yaml` que reclama o armazenamento e utiliza as ações que austei.


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

Para obter mais informações, consulte [Implementar uma aplicação imponente através de um provisionamento estático no seu Azure Stack Edge Pro via kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

O Azure Stack Edge Pro também tem um incorporado `StorageClass` chamado que utiliza um armazenamento de disco de `ase-node-local` dados ligado ao nó Kubernetes. Isto `StorageClass` suporta o fornecimento dinâmico. Pode fazer uma `StorageClass` referência nas aplicações do pod e um PV é automaticamente criado para si. Para obter mais informações, consulte o [painel de instrumentos de Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) para consultar `ase-node-local StorageClass` .

![Classe de armazenamento incorporado no painel de instrumentos de Kubernetes](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

Para obter mais informações, consulte [Implementar uma aplicação imponente através de um provisionamento dinâmico no seu Azure Stack Edge Pro via kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).

## <a name="choose-storage-type"></a>Escolha o tipo de armazenamento

Poderá ter de escolher o seu tipo de armazenamento dependendo da carga de trabalho que está a implementar. 

- Se quiser `ReadWriteMany` o modo de acesso para o seu local onde os `PersistentVolumes` volumes são montados como leitura-escrita por muitos nós que se implementam, utilize provisão estática para as ações SMB/NFS.

- Se as aplicações que está a implementar tiverem um requisito de conformidade POSIX, por exemplo, aplicações como MongoDB, PostgreSQL, MySQL ou Prometheus, utilize a StorageClass incorporada. Os modos de acesso são `ReadWriteOnce` ou o volume é montado como leitura-escrita por um único nó. 


Para obter mais informações sobre os modos de acesso, consulte [o modo de acesso aos volumes de Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes).


## <a name="next-steps"></a>Passos seguintes

Para entender como pode provisão estática de um `PersistentVolume` , consulte:

- [Implemente uma aplicação imponente através de provisões estáticas no seu Azure Stack Edge Pro via kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Para aprender como pode provisão dinamicamente a `StorageClass` , consulte:

- [Implemente uma aplicação imponente através de um provisionamento dinâmico no seu Azure Stack Edge Pro via kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).
