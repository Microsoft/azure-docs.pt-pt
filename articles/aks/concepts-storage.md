---
title: Conceitos-armazenamento nos serviços Kubernetess do Azure (AKS)
description: Saiba mais sobre o armazenamento no AKS (serviço kubernetes do Azure), incluindo volumes, volumes persistentes, classes de armazenamento e declarações
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: fb15063e41e83b4c9a9f2e01b6ad18c8afed7f5f
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68740994"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opções de armazenamento para aplicativos no serviço kubernetes do Azure (AKS)

Os aplicativos executados no serviço de kubernetes do Azure (AKS) podem precisar armazenar e recuperar dados. Para algumas cargas de trabalho de aplicativo, esse armazenamento de dados pode usar o armazenamento local e rápido no nó que não é mais necessário quando os pods são excluídos. Outras cargas de trabalho de aplicativo podem exigir armazenamento que persiste em volumes de dados mais comuns na plataforma do Azure. Vários pods podem precisar compartilhar os mesmos volumes de dados ou reanexar volumes de dados se o Pod for reagendado em um nó diferente. Por fim, talvez seja necessário injetar dados confidenciais ou informações de configuração do aplicativo em pods.

![Opções de armazenamento para aplicativos em um cluster do AKS (serviços Kubernetess do Azure)](media/concepts-storage/aks-storage-options.png)

Este artigo apresenta os principais conceitos que fornecem armazenamento para seus aplicativos no AKS:

- [Volumes](#volumes)
- [Volumes persistentes](#persistent-volumes)
- [Classes de armazenamento](#storage-classes)
- [Afirmações de volumes persistentes](#persistent-volume-claims)

## <a name="volumes"></a>Volumes

Os aplicativos geralmente precisam ser capazes de armazenar e recuperar dados. Como kubernetes normalmente trata os pods individuais como recursos efêmeros, descartáveis, diferentes abordagens estão disponíveis para que os aplicativos usem e persistam os dados conforme necessário. Um *volume* representa uma maneira de armazenar, recuperar e persistir dados em pods e por meio do ciclo de vida do aplicativo.

Os volumes tradicionais para armazenar e recuperar dados são criados como recursos de kubernetes com suporte do armazenamento do Azure. Você pode criar manualmente esses volumes de dados para serem atribuídos diretamente ao pods ou fazer com que o kubernetes os crie automaticamente. Esses volumes de dados podem usar os discos do Azure ou os arquivos do Azure:

- Os *discos do Azure* podem ser usados para criar um recurso de dataKubernetes de *disco* . Os discos podem usar o armazenamento Premium do Azure, apoiado por SSDs de alto desempenho ou armazenamento standard do Azure, apoiado por HDDs regulares. Para a maioria das cargas de trabalho de desenvolvimento e produção, use o armazenamento Premium. Os discos do Azure são montados como *ReadWriteOnce*, portanto, estão disponíveis apenas para um único nó. Para volumes de armazenamento que podem ser acessados por vários nós simultaneamente, use os arquivos do Azure.
- *Os arquivos do Azure* podem ser usados para montar um compartilhamento SMB 3,0 apoiado por uma conta de armazenamento do Azure para pods. Os arquivos permitem que você compartilhe dados em vários nós e pods. Os arquivos podem usar o armazenamento standard do Azure apoiado por HDDs regulares ou pelo armazenamento Premium do Azure, apoiado por SSDs de alto desempenho.
> [!NOTE] 
> Os arquivos do Azure dão suporte ao armazenamento Premium em clusters AKS que executam o kubernetes 1,13 ou superior.

No kubernetes, os volumes podem representar mais do que apenas um disco tradicional, onde as informações podem ser armazenadas e recuperadas. Os volumes kubernetes também podem ser usados como uma maneira de injetar dados em um pod para uso pelos contêineres. Os tipos de volume adicionais comuns no kubernetes incluem:

- *emptyDir* -esse volume é comumente usado como espaço temporário para um pod. Todos os contêineres dentro de um pod podem acessar os dados no volume. Os dados gravados nesse tipo de volume persistem apenas durante o ciclo de vida do pod – quando o pod é excluído, o volume é excluído. Esse volume normalmente usa o armazenamento em disco do nó local subjacente, embora também possa existir somente na memória do nó.
- *segredo* – esse volume é usado para injetar dados confidenciais em pods, como senhas. Primeiro, você cria um segredo usando a API kubernetes. Quando você define o Pod ou a implantação, um segredo específico pode ser solicitado. Os segredos são fornecidos apenas para nós que têm um pod agendado que o exige, e o segredo é armazenado em *tmpfs*, não gravado no disco. Quando o último pod em um nó que requer um segredo é excluído, o segredo é excluído do tmpfs do nó. Os segredos são armazenados em um namespace específico e só podem ser acessados por pods dentro do mesmo namespace.
- *configMap* -esse tipo de volume é usado para injetar Propriedades de pares chave-valor em pods, como informações de configuração do aplicativo. Em vez de definir informações de configuração de aplicativo em uma imagem de contêiner, você pode defini-la como um recurso kubernetes que pode ser facilmente atualizado e aplicado a novas instâncias de pods à medida que são implantadas. Como usar um segredo, você primeiro cria um ConfigMap usando a API kubernetes. Esse ConfigMap pode ser solicitado quando você define um pod ou uma implantação. ConfigMaps são armazenados em um namespace específico e só podem ser acessados por pods dentro do mesmo namespace.

## <a name="persistent-volumes"></a>Volumes persistentes

Os volumes que são definidos e criados como parte do ciclo de vida do pod só existem até que o Pod seja excluído. Os pods geralmente esperam que o armazenamento permaneça se um pod for reagendado em um host diferente durante um evento de manutenção, especialmente em StatefulSets. Um PV ( *volume persistente* ) é um recurso de armazenamento criado e gerenciado pela API kubernetes que pode existir além do tempo de vida de um pod individual.

Os discos ou arquivos do Azure são usados para fornecer o PersistentVolume. Conforme observado na seção anterior sobre volumes, a escolha de discos ou arquivos geralmente é determinada pela necessidade de acesso simultâneo aos dados ou ao nível de desempenho.

![Volumes persistentes em um cluster AKS (serviços Kubernetess do Azure)](media/concepts-storage/persistent-volumes.png)

Um PersistentVolume pode ser criado *estaticamente* por um administrador de cluster ou criado *dinamicamente* pelo servidor de API do kubernetes. Se um pod estiver agendado e solicitar armazenamento que não esteja disponível no momento, o kubernetes poderá criar o disco do Azure ou o armazenamento de arquivos subjacente e anexá-lo ao Pod. O provisionamento dinâmico usa um *StorageClass* para identificar o tipo de armazenamento do Azure que precisa ser criado.

## <a name="storage-classes"></a>Classes de armazenamento

Para definir diferentes camadas de armazenamento, como Premium e Standard, você pode criar um *StorageClass*. O StorageClass também define o *reclaimPolicy*. Este reclaimPolicy controla o comportamento do recurso de armazenamento do Azure subjacentes, quando o pod é eliminado e o volume persistente já não poderá ser necessário. O recurso de armazenamento subjacente pode ser eliminado ou retido para utilização com um pod futura.

Em AKS, dois StorageClasses iniciais são criados:

- *padrão* – usa o armazenamento standard do Azure para criar um disco gerenciado. A política de recuperação indica que o disco do Azure subjacente é excluído quando o Pod que o usava é excluído.
- *gerenciado-Premium* -usa o armazenamento Premium do Azure para criar um disco gerenciado. A política de recuperação novamente indica que o disco do Azure subjacente é excluído quando o Pod que o usava é excluído.

Se nenhum StorageClass for especificado para um volume persistente, o StorageClass padrão será usado. Tome cuidado ao solicitar volumes persistentes para que eles usem o armazenamento apropriado de que você precisa. Você pode criar um StorageClass para necessidades adicionais usando `kubectl`. O exemplo a seguir usa Managed Disks Premium e especifica que o disco subjacente do Azure deve ser *mantido* quando o pod é excluído:

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

## <a name="persistent-volume-claims"></a>Declarações de volume persistentes

Um PersistentVolumeClaim solicita o armazenamento de disco ou de arquivo de um StorageClass, modo de acesso e tamanho específicos. O servidor de API kubernetes pode provisionar dinamicamente o recurso de armazenamento subjacente no Azure se não houver nenhum recurso existente para atender à declaração com base no StorageClass definido. A definição de Pod inclui a montagem de volume após a conexão do volume com o pod.

![Afirmações de volume persistente num cluster de serviços de Kubernetes do Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

Um PersistentVolume é *associado* a um PersistentVolumeClaim quando um recurso de armazenamento disponível é atribuído ao Pod que o está solicitando. Há um mapeamento 1:1 de volumes persistentes para declarações.

O manifesto YAML de exemplo a seguir mostra uma declaração de volume persistente que usa o StorageClass *Premium gerenciado* e solicita um disco *5Gi* de tamanho:

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

Quando você cria uma definição de Pod, a declaração de volume persistente é especificada para solicitar o armazenamento desejado. Você também especifica o *volumeMount* para que seus aplicativos leiam e gravem dados. O manifesto YAML de exemplo a seguir mostra como a declaração de volume persistente anterior pode ser usada para montar um volume em */mnt/Azure*:

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

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para armazenamento e backups em AKs][operator-best-practices-storage].

Para ver como criar volumes dinâmicos e estáticos que usam discos do Azure ou arquivos do Azure, consulte os seguintes artigos de instruções:

- [Criar um volume estático usando discos do Azure][aks-static-disks]
- [Criar um volume estático usando os arquivos do Azure][aks-static-files]
- [Criar um volume dinâmico usando discos do Azure][aks-dynamic-disks]
- [Criar um volume dinâmico usando os arquivos do Azure][aks-dynamic-files]

Para obter informações adicionais sobre os principais conceitos de kubernetes e AKS, consulte os seguintes artigos:

- [Clusters e cargas de trabalho do kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identidade kubernetes/AKS][aks-concepts-identity]
- [Segurança do kubernetes/AKS][aks-concepts-security]
- [Redes virtuais kubernetes/AKS][aks-concepts-network]
- [Escala de kubernetes/AKS][aks-concepts-scale]

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
