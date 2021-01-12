---
title: Conceitos - Armazenamento em Serviços Azure Kubernetes (AKS)
description: Saiba mais sobre o armazenamento no Serviço Azure Kubernetes (AKS), incluindo volumes, volumes persistentes, aulas de armazenamento e reclamações
services: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: bf910c66694a62505f259c0a95a88f7dfed05d19
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127962"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opções de armazenamento para aplicações no Serviço Azure Kubernetes (AKS)

As aplicações que funcionam no Serviço Azure Kubernetes (AKS) podem precisar de armazenar e recuperar dados. Para algumas cargas de trabalho de aplicação, este armazenamento de dados pode usar armazenamento local e rápido no nó que já não é necessário quando as cápsulas são eliminadas. Outras cargas de trabalho de aplicação podem exigir armazenamento que persista em volumes de dados mais regulares dentro da plataforma Azure. Várias cápsulas podem precisar de partilhar os mesmos volumes de dados ou religar volumes de dados se a cápsula for reagendada num nó diferente. Finalmente, poderá ser necessário injetar informações sensíveis sobre dados ou configuração de aplicações em cápsulas.

![Opções de armazenamento para aplicações num cluster Azure Kubernetes Services (AKS)](media/concepts-storage/aks-storage-options.png)

Este artigo introduz os conceitos fundamentais que fornecem armazenamento às suas aplicações em AKS:

- [Volumes](#volumes)
- [Volumes persistentes](#persistent-volumes)
- [Aulas de armazenamento](#storage-classes)
- [Afirmações de volumes persistentes](#persistent-volume-claims)

## <a name="volumes"></a>Volumes

As aplicações precisam muitas vezes de ser capazes de armazenar e recuperar dados. Como kubernetes normalmente tratam as cápsulas individuais como recursos efémeros e descartáveis, diferentes abordagens estão disponíveis para aplicações para usar e persistir dados conforme necessário. Um *volume* representa uma forma de armazenar, recuperar e persistir dados através de cápsulas e através do ciclo de vida da aplicação.

Os volumes tradicionais para armazenar e recuperar dados são criados como recursos kubernetes apoiados pelo Azure Storage. Pode criar manualmente estes volumes de dados para serem atribuídos diretamente a cápsulas, ou fazer com que o Kubernetes os crie automaticamente. Estes volumes de dados podem utilizar Discos Azure ou Ficheiros Azure:

- *Os Discos Azure* podem ser usados para criar um recurso Kubernetes *DataDisk.* Os discos podem utilizar o armazenamento Azure Premium, apoiado por SSDs de alto desempenho, ou armazenamento Azure Standard, apoiado por HDDs regulares. Para a maioria das cargas de trabalho de produção e desenvolvimento, utilize o armazenamento Premium. Os Discos Azure são montados como *ReadWriteOnce,* por isso só estão disponíveis para uma única cápsula. Para volumes de armazenamento que podem ser acedidos simultaneamente por várias cápsulas, utilize ficheiros Azure.
- *Os Ficheiros Azure* podem ser utilizados para montar uma partilha SMB 3.0 apoiada por uma conta de Armazenamento Azure para cápsulas. Os ficheiros permitem-lhe partilhar dados através de vários nós e cápsulas. Os ficheiros podem utilizar o armazenamento Azure Standard apoiado por HDDs regulares, ou armazenamento Azure Premium, apoiado por SSDs de alto desempenho.

Em Kubernetes, os volumes podem representar mais do que apenas um disco tradicional onde a informação pode ser armazenada e recuperada. Os volumes de Kubernetes também podem ser usados como forma de injetar dados numa cápsula para utilização pelos contentores. Os tipos de volume adicionais comuns em Kubernetes incluem:

- *emptyDir* - Este volume é comumente usado como espaço temporário para uma vagem. Todos os recipientes dentro de uma cápsula podem aceder aos dados sobre o volume. Os dados escritos a este tipo de volume persistem apenas durante o tempo de vida útil da cápsula - quando a cápsula é eliminada, o volume é eliminado. Este volume normalmente utiliza o armazenamento do disco de nó local subjacente, embora também possa existir apenas na memória do nó.
- *segredo* - Este volume é usado para injetar dados sensíveis em cápsulas, como palavras-passe. Primeiro cria-se um Segredo usando a API de Kubernetes. Quando define o seu casulo ou implantação, pode ser solicitado um Segredo específico. Os segredos são fornecidos apenas a nós que têm uma cápsula programada que o requer, e o Segredo é armazenado em *tmpfs*, não escritos para o disco. Quando a última cápsula de um nó que requer um Segredo é apagada, o Segredo é apagado dos tmpfs do nó. Os segredos são armazenados dentro de um determinado espaço de nome e só podem ser acedidos por cápsulas dentro do mesmo espaço de nome.
- *configMap* - Este tipo de volume é usado para injetar propriedades de pares de valor-chave em cápsulas, tais como informações de configuração de aplicações. Em vez de definir informações de configuração de aplicações dentro de uma imagem de recipiente, pode defini-la como um recurso Kubernetes que pode ser facilmente atualizado e aplicado a novas instâncias de pods à medida que são implementadas. Tal como usar um Segredo, cria-se primeiro um ConfigMap utilizando a API de Kubernetes. Este ConfigMap pode então ser solicitado quando define uma cápsula ou implantação. ConfigMaps são armazenados dentro de um dado espaço de nome e só podem ser acedidos por cápsulas dentro do mesmo espaço de nome.

## <a name="persistent-volumes"></a>Volumes persistentes

Os volumes que são definidos e criados como parte do ciclo de vida do pod só existem até que a cápsula seja eliminada. Os pods esperam que o seu armazenamento permaneça se uma cápsula for reagendada num anfitrião diferente durante um evento de manutenção, especialmente em StatefulSets. Um *volume persistente* (PV) é um recurso de armazenamento criado e gerido pela API Kubernetes que pode existir para além do tempo de vida de uma cápsula individual.

Os Discos ou Ficheiros Azure são utilizados para fornecer o PersistentVolume. Como indicado na secção anterior em Volumes, a escolha de Discos ou Ficheiros é muitas vezes determinada pela necessidade de acesso simultâneo aos dados ou ao nível de desempenho.

![Volumes persistentes num cluster Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volumes.png)

Um PersistentVolume pode ser *criado estáticamente* por um administrador de cluster, ou criado *dinamicamente* pelo servidor API de Kubernetes. Se um casulo estiver agendado e solicitar o armazenamento que não está atualmente disponível, a Kubernetes pode criar o armazenamento de Disco ou Ficheiros Azure subjacente e anexá-lo à cápsula. O fornecimento dinâmico utiliza uma *StorageClass* para identificar que tipo de armazenamento Azure precisa de ser criado.

## <a name="storage-classes"></a>Aulas de armazenamento

Para definir diferentes níveis de armazenamento, como Premium e Standard, pode criar uma *StorageClass*. O StorageClass também define a *recuperação da Política.* Esta recuperação A Política controla o comportamento do recurso de armazenamento Azure subjacente quando a cápsula é eliminada e o volume persistente pode deixar de ser necessário. O recurso de armazenamento subjacente pode ser eliminado ou retido para utilização com uma futura cápsula.

Em AKS, são criados quatro iniciais `StorageClasses` para o cluster utilizando os plugins de armazenamento na árvore:

- `default` - Utiliza o armazenamento Azure StandardSSD para criar um Disco Gerido. A política de recuperação garante que o Disco Azure subjacente é eliminado quando o volume persistente que o utilizou for eliminado.
- `managed-premium` - Utiliza o armazenamento Azure Premium para criar um Disco Gerido. A política de recuperação garante novamente que o Disco Azure subjacente é eliminado quando o volume persistente que o utilizou for eliminado.
- `azurefile` - Utiliza o armazenamento Azure Standard para criar uma partilha de ficheiros Azure. A política de recuperação garante que a partilha de ficheiros Azure subjacente é eliminada quando o volume persistente que a usou for eliminado.
- `azurefile-premium` - Utiliza o armazenamento Azure Premium para criar uma partilha de ficheiros Azure. A política de recuperação garante que a partilha de ficheiros Azure subjacente é eliminada quando o volume persistente que a usou for eliminado.

Para os clusters que utilizam os novos plugins externos (CSI) da Interface de Armazenamento de Contentores (CSI), são criados os seguintes `StorageClasses` :
- `managed-csi` - Utiliza o armazenamento localmente redundante (LRS) da Azure StandardSSD para criar um Disco Gerido. A política de recuperação garante que o Disco Azure subjacente é eliminado quando o volume persistente que o utilizou for eliminado. A classe de armazenamento também configura os volumes persistentes para serem expansíveis, basta editar a reivindicação de volume persistente com o novo tamanho.
- `managed-csi-premium` - Utiliza o armazenamento localmente redundante (LRS) do Azure Premium para criar um Disco Gerido. A política de recuperação garante novamente que o Disco Azure subjacente é eliminado quando o volume persistente que o utilizou for eliminado. Da mesma forma, esta classe de armazenamento permite expandir volumes persistentes.
- `azurefile-csi` - Utiliza o armazenamento Azure Standard para criar uma partilha de ficheiros Azure. A política de recuperação garante que a partilha de ficheiros Azure subjacente é eliminada quando o volume persistente que a usou for eliminado.
- `azurefile-csi-premium` - Utiliza o armazenamento Azure Premium para criar uma partilha de ficheiros Azure. A política de recuperação garante que a partilha de ficheiros Azure subjacente é eliminada quando o volume persistente que a usou for eliminado.

Se não for especificado o StorageClass para um volume persistente, utiliza-se a Classe de Armazenamento predefinida. Tenha cuidado ao solicitar volumes persistentes para que utilizem o armazenamento apropriado. Pode criar uma Classe de Armazenamento para necessidades adicionais utilizando `kubectl` . O exemplo a seguir utiliza discos geridos premium e especifica que o disco Azure subjacente deve ser *mantido* quando a cápsula for eliminada:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> A AKS concilia as classes de armazenamento predefinidos e substituirá quaisquer alterações que fizer para essas aulas de armazenamento.

## <a name="persistent-volume-claims"></a>Afirmações de volumes persistentes

Um PersistenteVolumeClaim solicita o armazenamento de disco ou de ficheiro de uma determinada Classe de Armazenamento, modo de acesso e tamanho. O servidor API da Kubernetes pode providenciar dinamicamente o recurso de armazenamento subjacente no Azure se não existir um recurso existente para cumprir a reclamação com base na Classe de Armazenamento definida. A definição do casulo inclui o suporte de volume uma vez que o volume tenha sido ligado à cápsula.

![Reclamações de volume persistentes num cluster Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Um PersistentVolume está *ligado* a um PersistentVolumeClaim uma vez que um recurso de armazenamento disponível tenha sido atribuído ao pod que o solicita. Há um mapeamento de 1:1 de volumes persistentes para reclamações.

O manifesto yaml do exemplo a seguir mostra uma alegação de volume persistente que utiliza a Classe de Armazenamento *premium gerida* e solicita um Disco *5Gi* em tamanho:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Quando se cria uma definição de pod, a reivindicação de volume persistente é especificada para solicitar o armazenamento pretendido. Em seguida, especifica também o *volumeMount* para as suas aplicações para ler e escrever dados. O manifesto yaml do exemplo que se segue mostra como a alegação de volume persistente anterior pode ser utilizada para montar um volume a */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Para a montagem de um volume num recipiente Windows, especifique a letra de acionamento e o caminho. Por exemplo:

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>Passos seguintes

Para as melhores práticas associadas, consulte [as melhores práticas de armazenamento e backups em AKS][operator-best-practices-storage].

Para ver como criar volumes dinâmicos e estáticos que utilizam Discos Azure ou Ficheiros Azure, consulte os seguintes artigos de como fazer:

- [Criar um volume estático usando discos Azure][aks-static-disks]
- [Criar um volume estático usando ficheiros Azure][aks-static-files]
- [Criar um volume dinâmico usando Discos Azure][aks-dynamic-disks]
- [Criar um volume dinâmico utilizando ficheiros Azure][aks-dynamic-files]

Para obter informações adicionais sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Aglomerados de Kubernetes / AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Identidade de Kubernetes / AKS][aks-concepts-identity]
- [Segurança Kubernetes / AKS][aks-concepts-security]
- [Redes virtuais Kubernetes / AKS][aks-concepts-network]
- [Escala de Kubernetes / AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
