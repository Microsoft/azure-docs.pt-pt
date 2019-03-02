---
title: Conceitos - armazenamento nos serviços de Kubernetes do Azure (AKS)
description: Saiba mais sobre o armazenamento no Azure Kubernetes Service (AKS), incluindo volumes, volumes persistentes, classes de armazenamento e afirmações
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: cce38eb12d803c0640d9ee774dbc6c98ab5db219
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243776"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opções de armazenamento para aplicações no Azure Kubernetes Service (AKS)

Aplicações que são executadas no Azure Kubernetes Service (AKS) poderão ter de armazenar e recuperar dados. Para algumas cargas de trabalho de aplicação, este armazenamento de dados pode utilizar o armazenamento rápido local no nó que já não é necessário quando os pods são eliminados. Outras cargas de trabalho do aplicativo podem exigir armazenamento persistir nos dados mais regulares volumes dentro da plataforma do Azure. Vários pods poderão ter de partilhar os mesmo volumes de dados ou voltar a ligá os volumes de dados, se o pod está reagendado num nó diferente. Por fim, terá de inserir dados confidenciais ou informações de configuração de aplicação em pods.

![Opções de armazenamento para aplicativos num cluster de serviços de Kubernetes do Azure (AKS)](media/concepts-storage/aks-storage-options.png)

Este artigo apresenta os conceitos principais que fornecem armazenamento às suas aplicações no AKS:

- [Volumes](#volumes)
- [Volumes persistentes](#persistent-volumes)
- [Classes de armazenamento](#storage-classes)
- [Afirmações de volume persistente](#persistent-volume-claims)

## <a name="volumes"></a>Volumes

Aplicativos muitas vezes necessitam de ser capaz de armazenar e recuperar dados. Como o Kubernetes, normalmente, trata pods individuais como recursos efémeros, descartáveis, abordagens diferentes estão disponíveis para aplicações para utilizar e manter os dados conforme necessário. R *volume* representa uma forma de armazenar, obter e manter os dados entre pods e através do ciclo de vida do aplicativo.

Volumes tradicionais para armazenar e recuperar os dados são criados como recursos do Kubernetes apoiados pelo armazenamento do Azure. Manualmente pode criar estes volumes de dados a ser atribuída ao pods diretamente, ou ter o Kubernetes criá-las automaticamente. Estes volumes de dados podem utilizar discos do Azure ou de ficheiros do Azure:

- *Discos do Azure* pode ser utilizado para criar um Kubernetes *DataDisk* recursos. Podem utilizar a discos de armazenamento Premium do Azure, apoiado por SSDs de alto desempenho, ou armazenamento padrão do Azure, protegido por HDDs regulares. Para a maioria dos e cargas de trabalho de desenvolvimento de produção, utilize o armazenamento Premium. Discos do Azure são instalados como *ReadWriteOnce*, portanto, só estão disponíveis para um único nó. Para volumes de armazenamento que podem ser acedidas por vários nós em simultâneo, utilize ficheiros do Azure.
- *Os ficheiros do Azure* pode ser usado para montar uma partilha de SMB 3.0 apoiada por uma conta de armazenamento do Azure para os pods. Ficheiros permitem-lhe partilhar dados entre vários nós e os pods. Atualmente, ficheiros apenas podem utilizar o armazenamento padrão do Azure, protegido por HDDs regulares.

No Kubernetes, volumes podem representar mais do que apenas um disco tradicional onde informações podem ser armazenadas e recuperadas. Volumes de Kubernetes também podem ser utilizados como uma forma de inserir dados num pod para utilização pelos contentores. Tipos de volume adicionais comuns no Kubernetes incluem:

- *emptyDir* -este volume é geralmente utilizado como o espaço temporário para um pod. Todos os contentores dentro de um pod podem acessar os dados no volume. Dados escritos para este tipo de volume persistir apenas para o tempo de vida de pod - quando é eliminado o pod, o volume é eliminado. Este volume normalmente utiliza o armazenamento de disco do nó local subjacente, mas também pode existir apenas na memória do nó.
- *segredo* -este volume é usado para inserir dados confidenciais no pods, como palavras-passe. Primeiro, vai criar um segredo com a API do Kubernetes. Quando define seu pod ou a implementação, pode ser pedido um segredo específico. Segredos são fornecidos apenas para nós que têm um pod agendado que precise dela, e o segredo é armazenado na *tmpfs*, não é escrito no disco. Quando o último pod num nó de que necessita de um segredo é eliminado, o segredo é eliminado do tmpfs do nó. Segredos são armazenados dentro de um determinado espaço de nomes e apenas podem ser acedidos por pods dentro do mesmo espaço de nomes.
- *configMap* -esse tipo de volume é usado para inserir propriedades de par chave-valor na pods, tais como informações de configuração de aplicação. Em vez de definir informações de configuração de aplicativo dentro de uma imagem de contentor, pode defini-lo como um recurso do Kubernetes que pode ser facilmente atualizado e aplicado a novas instâncias de pods que são implementados. Como usar um segredo, crie primeiro um ConfigMap usando a API do Kubernetes. Em seguida, pode ser solicitado este ConfigMap quando define um pod ou implementação. ConfigMaps são armazenadas dentro de um determinado espaço de nomes e apenas pode ser acedidos por pods dentro do mesmo espaço de nomes.

## <a name="persistent-volumes"></a>Volumes persistentes

Volumes que são definidos e criadas como parte do ciclo de vida de pod só existem até que o pod seja eliminado. Pods, muitas vezes, esperar que o respetivo armazenamento permaneça se um pod é reagendado num anfitrião diferente durante um evento de manutenção, especialmente em StatefulSets. R *volume persistente* (PV) é um recurso de armazenamento criados e geridos pela API do Kubernetes que podem existir supera o de um pod individual.

Discos do Azure ou os ficheiros são utilizados para fornecer o PersistentVolume. Conforme observado na seção anterior em Volumes, a opção de discos ou arquivos, muitas vezes, é determinada pela necessidade de acesso simultâneo para os dados ou o escalão de desempenho.

![Volumes persistentes num cluster de serviços de Kubernetes do Azure (AKS)](media/concepts-storage/persistent-volumes.png)

Pode ser um PersistentVolume *estaticamente* criado por um administrador de cluster, ou *dinamicamente* criada pelo servidor de API do Kubernetes. Se um pod está agendado e pedidos de armazenamento que não está atualmente disponível, o Kubernetes pode criar o armazenamento de disco do Azure ou de ficheiros subjacente e anexá-lo para o pod. Dinâmica de aprovisionamento utiliza um *StorageClass* para identificar o tipo de armazenamento do Azure tem de ser criada.

## <a name="storage-classes"></a>Classes de armazenamento

Para definir diferentes camadas de armazenamento, como Premium e Standard, pode criar uma *StorageClass*. O StorageClass também define a *reclaimPolicy*. Este reclaimPolicy controla o comportamento do recurso de armazenamento do Azure subjacentes, quando o pod é eliminado e o volume persistente já não poderá ser necessário. O recurso de armazenamento subjacente pode ser eliminado ou retido para utilização com um pod futura.

No AKS, são criados dois StorageClasses iniciais:

- *predefinição* -armazenamento utiliza o padrão do Azure para criar um disco gerido. A política de recuperar indica que o disco do Azure subjacente é eliminado quando o pod que o utilizaram é eliminado.
- *premium geridos* -armazenamento Premium do Azure utiliza para criar disco gerido. A política de recuperar novamente indica que o disco do Azure subjacente é eliminado quando o pod que o utilizaram é eliminado.

Se não for especificado nenhum StorageClass para um volume persistente, é utilizada a predefinição StorageClass. Tenha cuidado ao pedir volumes persistentes, para que utilizem o armazenamento de apropriado que precisa. Pode criar um StorageClass para as necessidades adicionais usando `kubectl`. O exemplo seguinte utiliza o Premium Managed Disks e especifica que o disco do Azure subjacente devem ser *retidos* quando é eliminado o pod:

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

## <a name="persistent-volume-claims"></a>Afirmações de volume persistente

Um PersistentVolumeClaim pedidos de armazenamento de disco ou arquivo de um determinado StorageClass, modo de acesso e tamanho. O servidor de API do Kubernetes dinamicamente pode aprovisionar o recurso de armazenamento subjacente no Azure, se não houver nenhum recurso existente para atender a afirmação com base no StorageClass definidos. A definição de pod inclui a montagem de volume, uma vez que o volume foi ligado ao pod.

![Afirmações de volume persistente num cluster de serviços de Kubernetes do Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

É um PersistentVolume *vinculado* para um PersistentVolumeClaim depois do pod a pedir-lhe foi atribuído um recurso de armazenamento disponível. Existe um mapeamento 1:1 de volumes persistentes para afirmações.

O manifesto YAML de exemplo seguinte mostra uma afirmação de volume persistente que utiliza a *premium geridos* StorageClass e pede um disco *5Gi* de tamanho:

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

Quando cria uma definição de pod, a declaração de volume persistente é especificada para pedir o armazenamento pretendido. Também, em seguida, especifique a *volumeMount* para as suas aplicações ler e escrever dados. O manifesto YAML de exemplo seguinte mostra como a afirmação de volume persistente anterior pode ser usada para montar um volume em */mnt/azure*:

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

## <a name="next-steps"></a>Passos Seguintes

Para as práticas recomendadas associadas, consulte [melhores práticas para o armazenamento e cópias de segurança no AKS][operator-best-practices-storage].

Para ver como criar volumes estáticos e dinâmicos que utilizam discos do Azure ou de ficheiros do Azure, consulte os seguintes artigos de procedimentos:

- [Criar um volume estático usando discos do Azure][aks-static-disks]
- [Criar um volume estático com ficheiros do Azure][aks-static-files]
- [Criar um volume dinâmico usando discos do Azure][aks-dynamic-disks]
- [Criar um volume dinâmico com ficheiros do Azure][aks-dynamic-files]

Para obter mais informações sobre principais Kubernetes e conceitos do AKS, consulte os artigos seguintes:

- [Kubernetes / clusters do AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / identidade do AKS][aks-concepts-identity]
- [Kubernetes / segurança AKS][aks-concepts-security]
- [Kubernetes / redes virtuais do AKS][aks-concepts-network]
- [Kubernetes / aumentar do AKS][aks-concepts-scale]

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
