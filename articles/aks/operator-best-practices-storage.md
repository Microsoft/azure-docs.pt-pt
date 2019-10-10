---
title: Práticas recomendadas do operador – armazenamento nos serviços Kubernetess do Azure (AKS)
description: Conheça as práticas recomendadas do operador de cluster para armazenamento, criptografia de dados e backups no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: mlearned
ms.openlocfilehash: 8e5f394987de06feaeb9a635face643eecc97cb9
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174226"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para armazenamento e backups no serviço de kubernetes do Azure (AKS)

Conforme você cria e gerencia clusters no AKS (serviço kubernetes do Azure), seus aplicativos geralmente precisam de armazenamento. É importante entender as necessidades de desempenho e os métodos de acesso para pods para que você possa fornecer o armazenamento apropriado aos aplicativos. O tamanho do nó AKS pode afetar essas opções de armazenamento. Você também deve planejar maneiras de fazer backup e testar o processo de restauração para armazenamento anexado.

Este artigo de práticas recomendadas se concentra em considerações de armazenamento para operadores de cluster. Neste artigo, você aprende:

> [!div class="checklist"]
> * Quais tipos de armazenamento estão disponíveis
> * Como dimensionar corretamente nós AKS para desempenho de armazenamento
> * Diferenças entre o provisionamento dinâmico e estático de volumes
> * Maneiras de fazer backup e proteger seus volumes de dados

## <a name="choose-the-appropriate-storage-type"></a>Escolha o tipo de armazenamento apropriado

**Diretrizes de práticas recomendadas** -entenda as necessidades do seu aplicativo para escolher o armazenamento certo. Use o armazenamento com suporte de SSD e alto desempenho para cargas de trabalho de produção. Planeje o armazenamento baseado em rede quando houver necessidade de várias conexões simultâneas.

Os aplicativos geralmente exigem tipos e velocidades diferentes de armazenamento. Seus aplicativos precisam de armazenamento que se conecta a pods individuais ou compartilhados entre vários pods? O armazenamento é para acesso somente leitura a dados ou para gravar grandes quantidades de dados estruturados? Essas necessidades de armazenamento determinam o tipo mais apropriado de armazenamento a ser usado.

A tabela a seguir descreve os tipos de armazenamento disponíveis e seus recursos:

| Caso de utilização | Plug-in de volume | Ler/gravar uma vez | Somente leitura | Ler/gravar muitos | Suporte a contêiner do Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Configuração compartilhada       | Ficheiros do Azure   | Sim | Sim | Sim | Sim |
| Dados de aplicativo estruturados        | Discos do Azure   | Sim | Não  | Não  | Sim |
| Dados não estruturados, operações do sistema de arquivos | [BlobFuse (visualização)][blobfuse] | Sim | Sim | Sim | Não |

Os dois tipos primários de armazenamento fornecidos para volumes em AKS são apoiados por discos do Azure ou arquivos do Azure. Para melhorar a segurança, os dois tipos de armazenamento usam o SSE (Criptografia do Serviço de Armazenamento do Azure), por padrão, que criptografa os dados em repouso. Atualmente, os discos não podem ser criptografados usando Azure Disk Encryption no nível de nó AKS.

Os arquivos do Azure estão disponíveis no momento no nível de desempenho padrão. Os discos do Azure estão disponíveis nos níveis de desempenho Standard e Premium:

- Os discos *Premium* são apoiados por SSDs (discos de estado sólido) de alto desempenho. Os discos Premium são recomendados para todas as cargas de trabalho de produção.
- Os discos *Standard* são apoiados por discos de rotação regular (HDDs) e são bons para dados arquivados ou raramente acessados.

Entenda as necessidades de desempenho do aplicativo e os padrões de acesso para escolher a camada de armazenamento apropriada. Para obter mais informações sobre tamanhos de Managed Disks e níveis de desempenho, consulte [visão geral do Azure Managed disks][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Criar e usar classes de armazenamento para definir as necessidades do aplicativo

O tipo de armazenamento que você usa é definido usando *classes de armazenamento*kubernetes. A classe de armazenamento é referenciada no Pod ou na especificação de implantação. Essas definições funcionam em conjunto para criar o armazenamento apropriado e conectá-lo a pods. Para obter mais informações, consulte [classes de armazenamento em AKs][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Dimensionar os nós para necessidades de armazenamento

**Diretrizes de práticas recomendadas** -cada tamanho de nó dá suporte a um número máximo de discos. Diferentes tamanhos de nó também fornecem quantidades diferentes de armazenamento local e largura de banda de rede. Planeje as demandas do seu aplicativo para implantar o tamanho apropriado dos nós.

Os nós AKS são executados como VMs do Azure. Tipos e tamanhos diferentes de VM estão disponíveis. Cada tamanho de VM fornece uma quantidade diferente de recursos principais, como CPU e memória. Esses tamanhos de VM têm um número máximo de discos que podem ser anexados. O desempenho do armazenamento também varia entre os tamanhos de VM para o máximo de IOPS de disco local e anexado (operações de entrada/saída por segundo).

Se seus aplicativos exigirem discos do Azure como sua solução de armazenamento, planeje e escolha um tamanho de VM de nó apropriado. A quantidade de CPU e memória não é o único fator quando você escolhe um tamanho de VM. Os recursos de armazenamento também são importantes. Por exemplo, os tamanhos de VM *Standard_B2ms* e *Standard_DS2_v2* incluem uma quantidade semelhante de recursos de CPU e memória. Seu desempenho de armazenamento potencial é diferente, conforme mostrado na tabela a seguir:

| Tipo e tamanho de nó | vCPU | Memória (GiB) | Máximo de discos de dados | IOPS de disco máximo não armazenado em cache | Taxa de transferência máxima não armazenada em cache (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1\.920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6\.400                  | 96                             |

Aqui, o *Standard_DS2_v2* permite o dobro do número de discos anexados e fornece de três a quatro vezes a quantidade de IOPS e a taxa de transferência do disco. Se você só examinou os principais recursos de computação e os custos em comparação, poderá escolher o tamanho da VM *Standard_B2ms* e ter um desempenho e limitações de armazenamento insatisfatórios. Trabalhe com sua equipe de desenvolvimento de aplicativos para entender suas necessidades de desempenho e capacidade de armazenamento. Escolha o tamanho de VM apropriado para os nós AKS atender ou exceder suas necessidades de desempenho. Aplicativos de linha de base regularmente para ajustar o tamanho da VM, conforme necessário.

Para obter mais informações sobre tamanhos de VM disponíveis, consulte [tamanhos de máquinas virtuais do Linux no Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Provisionar volumes dinamicamente

**Diretrizes de práticas recomendadas** -para reduzir a sobrecarga de gerenciamento e permitir a escala, não crie estaticamente e atribua volumes persistentes. Use o provisionamento dinâmico. Em suas classes de armazenamento, defina a política de recuperação apropriada para minimizar os custos de armazenamento desnecessários depois que os pods forem excluídos.

Quando você precisar anexar o armazenamento a pods, use volumes persistentes. Esses volumes persistentes podem ser criados de forma manual ou dinâmica. A criação manual de volumes persistentes adiciona sobrecarga de gerenciamento e limita sua capacidade de dimensionamento. Use o provisionamento de volume persistente dinâmico para simplificar o gerenciamento de armazenamento e permitir que seus aplicativos cresçam e dimensionem conforme a necessidade.

![Declarações de volume persistentes em um cluster AKS (serviços Kubernetess do Azure)](media/concepts-storage/persistent-volume-claims.png)

Uma declaração de volume persistente (PVC) permite que você crie dinamicamente o armazenamento conforme necessário. Os discos subjacentes do Azure são criados como os pods os solicitam. Na definição de Pod, você solicita que um volume seja criado e anexado a um caminho de montagem projetado

Para obter os conceitos sobre como criar e usar volumes dinamicamente, confira [declarações de volumes persistentes][aks-concepts-storage-pvcs].

Para ver esses volumes em ação, consulte como criar e usar dinamicamente um volume persistente com [discos do Azure][dynamic-disks] ou [arquivos do Azure][dynamic-files].

Como parte de suas definições de classe de armazenamento, defina o *reclaimPolicy*apropriado. Esse reclaimPolicy controla o comportamento do recurso de armazenamento do Azure subjacente quando o pod é excluído e o volume persistente pode não ser mais necessário. O recurso de armazenamento subjacente pode ser excluído ou retido para uso com um pod futuro. O reclaimPolicy pode ser definido como *reter* ou *excluir*. Entenda suas necessidades de aplicativo e implemente verificações regulares de armazenamento que são mantidas para minimizar a quantidade de armazenamento não utilizado que é usada e cobrada.

Para obter mais informações sobre opções de classe de armazenamento, consulte [políticas de redeclaração de armazenamento][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Proteja e faça backup de seus dados

**Diretrizes de práticas recomendadas** -faça backup dos dados usando uma ferramenta apropriada para seu tipo de armazenamento, como Velero ou Azure site Recovery. Verifique a integridade e a segurança desses backups.

Quando seus aplicativos armazenam e consomem dados persistentes em discos ou em arquivos, você precisa fazer backups regulares ou instantâneos desses dados. Os discos do Azure podem usar tecnologias de instantâneo internas. Talvez seja necessário procurar seus aplicativos para liberar gravações no disco antes de executar a operação de instantâneo. O [Velero][velero] pode fazer backup de volumes persistentes juntamente com configurações e recursos de cluster adicionais. Se você não puder [remover o estado de seus aplicativos][remove-state], faça backup dos dados de volumes persistentes e teste regularmente as operações de restauração para verificar a integridade dos dados e os processos necessários.

Entenda as limitações das diferentes abordagens para backups de dados e se você precisar desativar seus dados antes do instantâneo. Os backups de dados não permitem, necessariamente, restaurar o ambiente de aplicativo da implantação de cluster. Para obter mais informações sobre esses cenários, consulte [práticas recomendadas para continuidade dos negócios e recuperação de desastres no AKs][best-practices-multi-region].

## <a name="next-steps"></a>Passos seguintes

Este artigo se concentrou nas práticas recomendadas de armazenamento no AKS. Para obter mais informações sobre noções básicas de armazenamento no kubernetes, consulte [conceitos de armazenamento para aplicativos no AKs][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/linux/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
