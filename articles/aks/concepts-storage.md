---
title: Conceitos - Armazenamento em Serviços Azure Kubernetes (AKS)
description: Saiba mais sobre o armazenamento no Serviço Azure Kubernetes (AKS), incluindo volumes, volumes persistentes, aulas de armazenamento e reclamações
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a1f68b06c31597a1d2d044686274e86a79e6e9a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105787"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opções de armazenamento para aplicações no Serviço Azure Kubernetes (AKS)

As aplicações em execução no Serviço Azure Kubernetes (AKS) podem precisar de armazenar e recuperar dados. Enquanto algumas cargas de trabalho de aplicação podem usar armazenamento local e rápido em nós vazios e não necessários, outros requerem armazenamento que persiste em volumes de dados mais regulares dentro da plataforma Azure. 

Várias cápsulas podem precisar de:
* Partilhe os mesmos volumes de dados. 
* Recolocar os volumes de dados se a cápsula for reagendada num nó diferente. 

Finalmente, poderá ser necessário injetar informações sensíveis sobre dados ou configuração de aplicações em cápsulas.

Este artigo introduz os conceitos fundamentais que fornecem armazenamento às suas aplicações em AKS:

- [Volumes](#volumes)
- [Volumes persistentes](#persistent-volumes)
- [Aulas de armazenamento](#storage-classes)
- [Afirmações de volumes persistentes](#persistent-volume-claims)

![Opções de armazenamento para aplicações num cluster Azure Kubernetes Services (AKS)](media/concepts-storage/aks-storage-options.png)

## <a name="volumes"></a>Volumes

Kubernetes normalmente trata as cápsulas individuais como recursos efémeros e descartáveis. As aplicações têm diferentes abordagens à sua disposição para a utilização e persistência de dados. Um *volume* representa uma forma de armazenar, recuperar e persistir dados através de cápsulas e através do ciclo de vida da aplicação.

Os volumes tradicionais são criados como recursos Kubernetes apoiados pelo Azure Storage. Pode criar manualmente volumes de dados para serem atribuídos diretamente a cápsulas ou fazer com que kubernetes os criem automaticamente. Os volumes de dados podem utilizar Discos Azure ou Ficheiros Azure.

### <a name="azure-disks"></a>Discos do Azure

Utilize *discos Azure* para criar um recurso Kubernetes *DataDisk.* Os discos podem usar:
* Armazenamento Azure Premium, apoiado por SSDs de alto desempenho, ou 
* Armazenamento Azure Standard, apoiado por HDDs regulares. 

> [!TIP]
>Para a maioria das cargas de trabalho de produção e desenvolvimento, utilize o armazenamento Premium. 

Uma vez que os Discos Azure são montados como *ReadWriteOnce,* eles só estão disponíveis para uma única cápsula. Para volumes de armazenamento que podem ser acedidos simultaneamente por várias cápsulas, utilize ficheiros Azure.

### <a name="azure-files"></a>Ficheiros do Azure
Utilize *ficheiros Azure* para montar uma partilha SMB 3.0 apoiada por uma conta de Armazenamento Azure para cápsulas. Os ficheiros permitem-lhe partilhar dados através de vários nosdes e pods e pode utilizar:
* Armazenamento Azure Premium, apoiado por SSDs de alto desempenho, ou 
* Armazenamento Azure Standard apoiado por HDDs regulares.

### <a name="volume-types"></a>Tipos de volume
Os volumes de Kubernetes representam mais do que apenas um disco tradicional para armazenar e recuperar informações. Os volumes de Kubernetes também podem ser usados como forma de injetar dados numa cápsula para utilização pelos contentores. 

Os tipos de volume comuns em Kubernetes incluem:

#### <a name="emptydir"></a>emptyDir

Comumente usado como espaço temporário para uma cápsula. Todos os recipientes dentro de uma cápsula podem aceder aos dados sobre o volume. Os dados escritos a este tipo de volume persistem apenas durante o tempo de vida útil da cápsula. Uma vez eliminado o pod, o volume é apagado. Este volume normalmente utiliza o armazenamento do disco de nó local subjacente, embora também possa existir apenas na memória do nó.

#### <a name="secret"></a>segredo

Pode utilizar volumes *secretos* para injetar dados sensíveis em cápsulas, como palavras-passe. 
1. Crie um Segredo utilizando a API de Kubernetes. 
1. Defina a sua cápsula ou implantação e solicite um Segredo específico. 
    * Os segredos só são fornecidos aos nós com uma cápsula programada que os requer.
    * O Segredo está armazenado em *tmpfs,* não escrito em disco. 
1. Quando elimina a última cápsula num nó que requer um Segredo, o Segredo é apagado dos tmpfs do nó. 
   * Os segredos são armazenados dentro de um determinado espaço de nome e só podem ser acedidos por cápsulas dentro do mesmo espaço de nome.

#### <a name="configmap"></a>configMap

Pode utilizar *o configMap* para injetar propriedades de pares de valor-chave em cápsulas, como informações de configuração de aplicações. Defina as informações de configuração da aplicação como um recurso Kubernetes, facilmente atualizado e aplicado a novas instâncias de pods à medida que são implementadas. 

Como usar um segredo:
1. Crie um ConfigMap utilizando a API de Kubernetes. 
1. Solicite o ConfigMap quando definir uma cápsula ou implantação. 
   * ConfigMaps são armazenados dentro de um dado espaço de nome e só podem ser acedidos por cápsulas dentro do mesmo espaço de nome.

## <a name="persistent-volumes"></a>Volumes persistentes

Os volumes definidos e criados como parte do ciclo de vida do pod só existem até eliminar a cápsula. Os pods esperam que o seu armazenamento permaneça se uma cápsula for reagendada num anfitrião diferente durante um evento de manutenção, especialmente em StatefulSets. Um *volume persistente* (PV) é um recurso de armazenamento criado e gerido pela API Kubernetes que pode existir para além do tempo de vida de uma cápsula individual.

Pode utilizar Discos ou Ficheiros Azure para fornecer o PersistentVolume. Como indicado na secção [Volumes,](#volumes) a escolha de Discos ou Ficheiros é muitas vezes determinada pela necessidade de acesso simultâneo aos dados ou ao nível de desempenho.

![Volumes persistentes num cluster Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volumes.png)

Um PersistentVolume pode ser *criado estáticamente* por um administrador de cluster, ou criado *dinamicamente* pelo servidor API de Kubernetes. Se um pod estiver agendado e os pedidos de armazenamento atualmente indisponíveis, a Kubernetes pode criar o armazenamento subjacente do Disco Azure ou ficheiros e anexá-lo ao casulo. O fornecimento dinâmico utiliza uma *StorageClass* para identificar que tipo de armazenamento Azure precisa de ser criado.

## <a name="storage-classes"></a>Aulas de armazenamento

Para definir diferentes níveis de armazenamento, como Premium e Standard, pode criar uma *StorageClass*. 

O StorageClass também define a *recuperação da Política.* Quando elimina a cápsula e o volume persistente já não é necessário, a recuperaçãoPolicy controla o comportamento do recurso de armazenamento Azure subjacente. O recurso de armazenamento subjacente pode ser eliminado ou mantido para utilização com uma futura cápsula.

Em AKS, são criados quatro iniciais `StorageClasses` para o cluster utilizando os plugins de armazenamento na árvore:

| Permissão | Razão |
|---|---|
| `default` | Utiliza o armazenamento Azure StandardSSD para criar um Disco Gerido. A política de recuperação garante que o Disco Azure subjacente é eliminado quando o volume persistente que o utilizou for eliminado. |
| `managed-premium` | Utiliza o armazenamento Azure Premium para criar um Disco Gerido. A política de recuperação garante novamente que o Disco Azure subjacente é eliminado quando o volume persistente que o utilizou for eliminado. |
| `azurefile` | Utiliza o armazenamento Azure Standard para criar uma partilha de ficheiros Azure. A política de recuperação garante que a partilha de ficheiros Azure subjacente é eliminada quando o volume persistente que a usou for eliminado. |
| `azurefile-premium` | Utiliza o armazenamento Azure Premium para criar uma partilha de ficheiros Azure. A política de recuperação garante que a partilha de ficheiros Azure subjacente é eliminada quando o volume persistente que a usou for eliminado.|

Para os clusters que utilizam os novos plugins externos (CSI) da Interface de Armazenamento de Contentores (CSI), são criados os seguintes plugins `StorageClasses` extra:

| Permissão | Razão |
|---|---|
| `managed-csi` | Utiliza o armazenamento localmente redundante (LRS) da Azure StandardSSD para criar um Disco Gerido. A política de recuperação garante que o Disco Azure subjacente é eliminado quando o volume persistente que o utilizou for eliminado. A classe de armazenamento também configura os volumes persistentes para serem expansíveis, basta editar a reivindicação de volume persistente com o novo tamanho. |
| `managed-csi-premium` | Utiliza o armazenamento localmente redundante (LRS) do Azure Premium para criar um Disco Gerido. A política de recuperação garante novamente que o Disco Azure subjacente é eliminado quando o volume persistente que o utilizou for eliminado. Da mesma forma, esta classe de armazenamento permite expandir volumes persistentes. |
| `azurefile-csi` | Utiliza o armazenamento Azure Standard para criar uma partilha de ficheiros Azure. A política de recuperação garante que a partilha de ficheiros Azure subjacente é eliminada quando o volume persistente que a usou for eliminado. |
| `azurefile-csi-premium` | Utiliza o armazenamento Azure Premium para criar uma partilha de ficheiros Azure. A política de recuperação garante que a partilha de ficheiros Azure subjacente é eliminada quando o volume persistente que a usou for eliminado.|

A menos que especifique uma Classe de Armazenamento para um volume persistente, o StorageClass predefinido será utilizado. Certifique-se de que os volumes utilizam o armazenamento adequado de que necessita ao solicitar volumes persistentes. 

Pode criar uma Classe de Armazenamento para necessidades adicionais utilizando `kubectl` . O exemplo a seguir utiliza discos geridos premium e especifica que o disco Azure subjacente deve ser *mantido* quando eliminar o pod:

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

Um PersistenteVolumeClaim solicita o armazenamento de disco ou de ficheiro de uma determinada Classe de Armazenamento, modo de acesso e tamanho. O servidor API da Kubernetes pode providenciar dinamicamente o recurso de armazenamento Azure subjacente se nenhum recurso existente puder cumprir a reclamação com base na Classe de Armazenamento definida. 

A definição do casulo inclui o suporte de volume uma vez que o volume tenha sido ligado à cápsula.

![Reclamações de volume persistentes num cluster Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Uma vez atribuído um recurso de armazenamento disponível ao pod que solicita armazenamento, a PersistentVolume está *ligada* a um PersistentVolumeClaim. Os volumes persistentes são 1:1 mapeados para reclamações.

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

Quando cria uma definição de pod, também especifica:
* O volume persistente alega solicitar o armazenamento pretendido. 
* O *volumeMount* para as suas aplicações para ler e escrever dados. 

O manifesto yaml do exemplo que se segue mostra como a alegação de volume persistente anterior pode ser utilizada para montar um volume a */mnt/azure*:

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

Para obter mais informações sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

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
