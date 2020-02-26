---
title: Conceitos - Armazenamento nos Serviços Azure Kubernetes (AKS)
description: Conheça o armazenamento no Serviço Azure Kubernetes (AKS), incluindo volumes, volumes persistentes, aulas de armazenamento e reclamações
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 4bb19d7da971a82aef9c0e1fc092cc648ac49c4c
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595999"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opções de armazenamento para aplicações no Serviço Azure Kubernetes (AKS)

As aplicações que funcionam no Serviço Azure Kubernetes (AKS) podem ter de armazenar e recuperar dados. Para algumas cargas de trabalho de aplicação, este armazenamento de dados pode usar armazenamento local e rápido no nó que já não é necessário quando as cápsulas são eliminadas. Outras cargas horárias de aplicação podem exigir armazenamento que persista em volumes de dados mais regulares dentro da plataforma Azure. Várias cápsulas podem ter de partilhar os mesmos volumes de dados ou recolocar volumes de dados se a cápsula for reagendada num nó diferente. Finalmente, poderá ter de injetar dados sensíveis ou informações de configuração de aplicações em cápsulas.

![Opções de armazenamento para aplicações num cluster azure Kubernetes Services (AKS)](media/concepts-storage/aks-storage-options.png)

Este artigo introduz os conceitos fundamentais que fornecem armazenamento às suas aplicações no AKS:

- [Volumes](#volumes)
- [Volumes persistentes](#persistent-volumes)
- [Aulas de armazenamento](#storage-classes)
- [Afirmações de volumes persistentes](#persistent-volume-claims)

## <a name="volumes"></a>Volumes

As aplicações precisam muitas vezes de ser capazes de armazenar e recuperar dados. Como kubernetes normalmente tratam as cápsulas individuais como recursos efémeros e descartáveis, diferentes abordagens estão disponíveis para aplicações para usar e persistir dados conforme necessário. Um *volume* representa uma forma de armazenar, recuperar e persistir dados em todas as cápsulas e através do ciclo de vida da aplicação.

Os volumes tradicionais para armazenar e recuperar dados são criados como recursos kubernetes apoiados pelo Armazenamento Azure. Pode criar manualmente estes volumes de dados a atribuir diretamente às cápsulas, ou ter kubernetes automaticamente a criá-los. Estes volumes de dados podem utilizar Discos Azure ou Ficheiros Azure:

- *Os Discos Azure* podem ser usados para criar um recurso Kubernetes *DataDisk.* Os discos podem utilizar o armazenamento Azure Premium, apoiado por SSDs de alto desempenho, ou armazenamento Azure Standard, apoiado por HDDs regulares. Para a maioria das cargas de trabalho de produção e desenvolvimento, utilize o armazenamento Premium. Os Discos Azure são montados como *ReadWriteOnce,* por isso só estão disponíveis para uma única cápsula. Para volumes de armazenamento que podem ser acedidos por várias cápsulas simultaneamente, utilize ficheiros Azure.
- *Os Ficheiros Azure* podem ser utilizados para montar uma quota SMB 3.0 apoiada por uma conta de Armazenamento Azure para cápsulas. Os ficheiros permitem-lhe partilhar dados em vários nódos e cápsulas. Os ficheiros podem utilizar o armazenamento Azure Standard apoiado por HDDs regulares, ou armazenamento Azure Premium, apoiado por SSDs de alto desempenho.
> [!NOTE] 
> Os Ficheiros Azure suportam o armazenamento premium em clusters AKS que executam Kubernetes 1.13 ou superior.

Em Kubernetes, os volumes podem representar mais do que apenas um disco tradicional onde a informação pode ser armazenada e recuperada. Os volumes kubernetes também podem ser usados como forma de injetar dados numa cápsula para utilização pelos recipientes. Os tipos comuns de volume adicional em Kubernetes incluem:

- *emptyDir* - Este volume é comumente usado como espaço temporário para uma vagem. Todos os recipientes dentro de uma cápsula podem aceder aos dados do volume. Os dados escritos a este tipo de volume persistem apenas durante o tempo de vida útil da cápsula - quando a cápsula é eliminada, o volume é eliminado. Este volume normalmente utiliza o armazenamento de disco de nó local subjacente, embora também possa existir apenas na memória do nó.
- *segredo* - Este volume é utilizado para injetar dados sensíveis em cápsulas, tais como senhas. Primeiro cria-se um Segredo usando a API kubernetes. Quando define a sua cápsula ou implantação, um segredo específico pode ser solicitado. Os segredos são fornecidos apenas a nós que tenham uma cápsula programada que o exija, e o Segredo é armazenado em *tmpfs,* não escrito em disco. Quando a última cápsula num nó que requer um segredo é apagada, o Segredo é apagado dos tmpfs do nó. Os segredos são armazenados dentro de um determinado espaço de nome e só podem ser acedidos por cápsulas dentro do mesmo espaço de nome.
- *configMap* - Este tipo de volume é usado para injetar propriedades de par de valor chave em cápsulas, tais como informações de configuração de aplicação. Em vez de definir informações de configuração de aplicação dentro de uma imagem de recipiente, pode defini-la como um recurso Kubernetes que pode ser facilmente atualizado e aplicado a novas instâncias de cápsulas à medida que são implantadas. Como usar um Segredo, cria-se primeiro um ConfigMap utilizando a API Kubernetes. Este ConfigMap pode então ser solicitado quando definir uma cápsula ou implementação. Os ConfigMaps são armazenados num determinado espaço de nome e só podem ser acedidos por cápsulas dentro do mesmo espaço de nome.

## <a name="persistent-volumes"></a>Volumes persistentes

Os volumes que são definidos e criados como parte do ciclo de vida da cápsula só existem até que a cápsula seja eliminada. As cápsulas esperam frequentemente que o seu armazenamento permaneça se uma cápsula for reagendada num hospedeiro diferente durante um evento de manutenção, especialmente em StatefulSets. Um *volume persistente* (PV) é um recurso de armazenamento criado e gerido pela API Kubernetes que pode existir para além da vida útil de uma cápsula individual.

Discos ou Ficheiros Azure são utilizados para fornecer o Volume Persistente. Como notado na secção anterior em Volumes, a escolha de Discos ou Ficheiros é muitas vezes determinada pela necessidade de acesso simultâneo aos dados ou ao nível de desempenho.

![Volumes persistentes num cluster azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volumes.png)

Um Volume Persistente pode ser *criado estáticamente* por um administrador de cluster, ou *criado dinamicamente* pelo servidor Kubernetes API. Se uma cápsula estiver programada e solicitar armazenamento que não esteja disponível atualmente, a Kubernetes pode criar o armazenamento de Discos Ou Ficheiros Azure subjacente e anexá-lo à cápsula. O fornecimento dinâmico utiliza uma *StorageClass* para identificar que tipo de armazenamento Azure precisa de ser criado.

## <a name="storage-classes"></a>Aulas de armazenamento

Para definir diferentes níveis de armazenamento, como Premium e Standard, pode criar uma *Classe de Armazenamento.* A Classe de Armazenamento também define a política de *recuperação.* Este reclaimPolicy controla o comportamento do recurso de armazenamento do Azure subjacentes, quando o pod é eliminado e o volume persistente já não poderá ser necessário. O recurso de armazenamento subjacente pode ser eliminado ou retido para utilização com um pod futura.

No AKS, são criadas duas Classes de Armazenamento iniciais:

- *predefinição* - Utiliza o armazenamento Azure Standard para criar um Disco Gerido. A política de recuperação indica que o Disco Azure subjacente é eliminado quando o volume persistente que o utilizou é eliminado.
- *managed-premium* - Utiliza o armazenamento Azure Premium para criar o Disco Gerido. A política de recuperação indica mais uma vez que o disco Azure subjacente é eliminado quando o volume persistente que o utilizou é eliminado.

Se não for especificada a StorageClass para um volume persistente, é utilizada a Classe de Armazenamento predefinida. Tenha cuidado ao solicitar volumes persistentes para que utilizem o armazenamento apropriado de que necessita. Pode criar uma StorageClass para necessidades adicionais utilizando `kubectl`. O exemplo seguinte utiliza discos geridos premium e especifica que o Disco Azure subjacente deve ser *mantido* quando o casulo é eliminado:

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

## <a name="persistent-volume-claims"></a>Reclamações persistentes de volume

Um PersistenteVolumeClaim solicita o armazenamento de discos ou ficheiros de uma determinada Classe de Armazenamento, modo de acesso e tamanho. O servidor Kubernetes API pode fornecer dinamicamente o recurso de armazenamento subjacente em Azure se não houver recurso existente para cumprir a reclamação com base na Classe de Armazenamento definida. A definição da cápsula inclui o volume de montagem uma vez que o volume tenha sido ligado à cápsula.

![Afirmações de volume persistente num cluster de serviços de Kubernetes do Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

Um Volume Persistente está *ligado* a um PersistenteVolumeClaim uma vez que um recurso de armazenamento disponível foi atribuído ao pod que o solicita. Há um mapeamento 1:1 de volumes persistentes para reclamações.

O seguinte exemplo De manifesto YAML mostra uma reivindicação de volume persistente que utiliza a Classe de Armazenamento *premium gerida* e solicita um Disco *5Gi* em tamanho:

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

Quando cria uma definição de casulo, a reivindicação de volume persistente é especificada para solicitar o armazenamento desejado. Em seguida, especifica também o *volumeMonte* para as suas aplicações para ler e escrever dados. O seguinte exemplo, o manifesto YAML mostra como a alegação de volume persistente anterior pode ser utilizada para montar um volume em */mnt/azure:*

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Passos seguintes

Para as melhores práticas associadas, consulte [as melhores práticas de armazenamento e backups em AKS][operator-best-practices-storage].

Para ver como criar volumes dinâmicos e estáticos que utilizem Discos Azure ou Ficheiros Azure, consulte os seguintes artigos:

- [Criar um volume estático usando discos Azure][aks-static-disks]
- [Criar um volume estático usando ficheiros Azure][aks-static-files]
- [Criar um volume dinâmico usando discos Azure][aks-dynamic-disks]
- [Criar um volume dinâmico usando ficheiros Azure][aks-dynamic-files]

Para obter informações adicionais sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Aglomerados kubernetes / AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Identidade Kubernetes / AKS][aks-concepts-identity]
- [Kubernetes / Segurança AKS][aks-concepts-security]
- [Redes virtuais Kubernetes / AKS][aks-concepts-network]
- [Escala kubernetes / AKS][aks-concepts-scale]

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
