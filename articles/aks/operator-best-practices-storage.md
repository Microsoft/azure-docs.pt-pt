---
title: Práticas recomendadas do operador – armazenamento dos serviços de Kubernetes no Azure (AKS)
description: Conheça as práticas recomendadas de operador de cluster de armazenamento, encriptação de dados e as cópias de segurança no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: iainfou
ms.openlocfilehash: 9231b3629c10043e72efad4231111e56fd54c626
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447152"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Melhores práticas para armazenamento e cópias de segurança no Azure Kubernetes Service (AKS)

Como criar e gerir clusters no Azure Kubernetes Service (AKS), as aplicações, muitas vezes, necessitam de armazenamento. É importante compreender as necessidades de desempenho e métodos para pods de acesso, para que pode fornecer o armazenamento adequado para aplicações. O tamanho de nó do AKS pode afetar essas opções de armazenamento. Também deve planejar formas de criar cópias de segurança e testar o processo de restauração para armazenamento de acesso.

Este artigo de melhores práticas concentra-se em considerações de armazenamento para os operadores de cluster. Neste artigo, vai aprender:

> [!div class="checklist"]
> * Que tipos de armazenamento estão disponíveis
> * Como dimensionar corretamente nós do AKS para desempenho de armazenamento
> * Diferenças entre estáticas e dinâmicas de aprovisionamento de volumes
> * Formas de criar cópias de segurança e proteger os seus volumes de dados

## <a name="choose-the-appropriate-storage-type"></a>Escolha o tipo de armazenamento adequado

**Melhores diretrizes de práticas** -compreender as necessidades da sua aplicação para escolher o armazenamento correta. Utilize armazenamento apoiadas por SSD para cargas de trabalho de produção de alto desempenho. Planear para armazenamento baseado em rede quando há uma necessidade de várias conexões simultâneas.

Aplicativos geralmente exigem a diferentes tipos e velocidades de armazenamento. As aplicações necessitam de armazenamento que se liga a pods individuais ou compartilhado entre vários pods? É o armazenamento para acesso só de leitura aos dados, ou escrever grandes quantidades de dados estruturados? Estes armazenamento tem de determinar o tipo mais adequado de armazenamento a utilizar.

A tabela seguinte descreve os tipos de armazenamento disponível e as respetivas funcionalidades:

| Caso de utilização | Plug-in do volume | Leitura/escrita uma vez | Muitos só de leitura | Leitura/escrita muitos | Suporte a contentores do Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Configuração partilhada       | Ficheiros do Azure   | Sim | Sim | Sim | Sim |
| Dados estruturados de aplicação        | Discos do Azure   | Sim | Não  | Não  | Sim |
| Dados não estruturados, operações de sistema de ficheiros | [BlobFuse (pré-visualização)][blobfuse] | Sim | Sim | Sim | Não |

Os dois principais tipos de armazenamento fornecida para os volumes de AKS são apoiados por discos do Azure ou de ficheiros do Azure. Para melhorar a segurança, ambos os tipos de armazenamento utilizam o Azure Storage Service Encryption (SSE) por padrão, que criptografa dados em repouso. Atualmente não não possível encriptar discos com o Azure Disk Encryption ao nível do nó do AKS.

Os ficheiros do Azure estão atualmente disponíveis no escalão padrão de desempenho. Discos do Azure estão disponíveis nos escalões de desempenho Standard e Premium:

- *Premium* discos são apoiados por discos de elevado desempenho Estado sólidos (SSDs). Os discos Premium são recomendados para todas as cargas de trabalho de produção.
- *Padrão* discos são apoiados por discos rotativos normais (HDDs) e são boas para dados de arquivos ou acedidos com pouca frequência.

Compreender as necessidades de desempenho do aplicativo e padrões para escolher a camada de armazenamento adequada de acesso. Para obter mais informações sobre os escalões de desempenho e tamanhos de discos geridos, consulte [descrição geral do Managed Disks do Azure][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Criar e usar as classes de armazenamento para definir as necessidades da aplicação

O tipo de armazenamento que utilizar é definido através do Kubernetes *classes de armazenamento*. A classe de armazenamento, em seguida, é referenciada na especificação pod ou implementação. Estas definições funcionam em conjunto para criar o armazenamento adequado e ligá-lo ao pods. Para obter mais informações, consulte [classes de armazenamento no AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Dimensionar os nós das necessidades de armazenamento

**Melhores diretrizes de práticas** -cada tamanho de nó suporta um número máximo de discos. Tamanhos de nó diferente também oferecem diferentes quantidades de largura de banda de rede e de armazenamento local. Planejar para suas necessidades de aplicações implementar um tamanho adequado de nós.

Nós do AKS são executados como VMs do Azure. Diferentes tipos e tamanhos de VM estão disponíveis. Cada tamanho de VM fornece uma quantidade diferente de recursos principais, tais como CPU e memória. Estes tamanhos VM tem um número máximo de discos que podem ser anexados. Desempenho de armazenamento também varia entre tamanhos de VM para o disco local e anexado o máximo IOPS (operações de entrada/saída por segundo).

Se as suas aplicações necessitam de discos do Azure como solução de armazenamento, planeie e escolha um tamanho VM de nó adequado. A quantidade de CPU e memória não é o único fator ao escolher um tamanho de VM. As capacidades de armazenamento também são importantes. Por exemplo, ambos os *Standard_B2ms* e *Standard_DS2_v2* tamanhos de VM incluem uma quantidade semelhante de recursos de CPU e memória. O desempenho de armazenamento possíveis é diferente, como mostrado na tabela a seguir:

| Tipo de nó e o tamanho | vCPU | Memória (GiB) | Discos de dados máximos | IOPS de disco eliminadas do cache máx. | Débito máximo do eliminadas do cache (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Aqui, o *Standard_DS2_v2* permite duplicar o número de discos ligados e fornece três a quatro vezes a quantidade de débito de disco e IOPS. Se apenas, examinamos os recursos de computação de núcleos e em comparação com os custos, pode escolher o *Standard_B2ms* VM redimensionar e ter um mau desempenho e armazenamento limitações. Trabalhar com a sua equipa de desenvolvimento de aplicativos para compreender suas necessidades de capacidade e desempenho de armazenamento. Escolha um tamanho VM adequado para os nós do AKS atingir ou exceder as suas necessidades de desempenho. Regularmente da linha de base aplicativos para ajustar o tamanho da VM conforme necessário.

Para obter mais informações sobre tamanhos VM disponíveis, consulte [tamanhos de máquinas de virtuais do Linux no Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dinamicamente os volumes de aprovisionamento

**Melhores diretrizes de práticas** - para reduzir a sobrecarga de gerenciamento e permitem que dimensione, não estaticamente criar e atribuir volumes persistentes. Utilize o aprovisionamento dinâmico. As classes de armazenamento, defina a política de recuperar adequada para minimizar os custos de armazenamento desnecessário, depois de pods são eliminados.

Quando precisar de ligar o armazenamento ao pods, se utilizar volumes persistentes. Estes volumes persistentes podem ser criados manualmente ou dinamicamente. Criação manual de volumes persistentes adiciona a sobrecarga de gerenciamento e limita a capacidade de dimensionar. Utilize o volume persistente dinâmico de aprovisionamento para simplificar a gestão de armazenamento e permitem às aplicações aumentar e dimensionar conforme necessário.

![Afirmações de volume persistente num cluster de serviços de Kubernetes do Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

Uma afirmação de volume persistente (PVC) permite-lhe criar dinamicamente o armazenamento, conforme necessário. Os discos do Azure subjacentes são criados como pods de pedido-los. Na definição do pod, solicita um volume para ser criados e anexados a um caminho de montagem projetado

Para os conceitos sobre como criar e utilizar volumes dinamicamente, consulte [persistente de Volumes de afirmações][aks-concepts-storage-pvcs].

Para ver estes volumes em ação, veja como criar e utilizar um volume persistente com dinamicamente [discos do Azure][dynamic-disks] or [Azure Files][dynamic-files].

Como parte das suas definições de classe de armazenamento, defina o adequado *reclaimPolicy*. Este reclaimPolicy controla o comportamento do recurso de armazenamento do Azure subjacentes, quando o pod é eliminado e o volume persistente já não poderá ser necessário. O recurso de armazenamento subjacente pode ser eliminado ou retido para utilização com um pod futura. O reclaimPolicy pode definido como *reter* ou *eliminar*. Compreender as necessidades da sua aplicação e implementar verificações regulares para o armazenamento que é mantido, para minimizar a quantidade de armazenamento não utilizado, que é utilizado e cobrada.

Para obter mais informações sobre as opções de classe de armazenamento, consulte [políticas de reclamar armazenamento][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Proteger e fazer backup dos dados

**Melhores diretrizes de práticas** - cópia de segurança os dados através de uma ferramenta adequada para seu tipo de armazenamento, como Velero ou Azure Site Recovery. Certifique-se de que a integridade e segurança, essas cópias de segurança.

Quando os aplicativos armazenem e utilizem os dados mantidos nos discos ou em arquivos, que precisa de efetuar cópias de segurança regulares ou instantâneos de dados. Discos do Azure podem utilizar tecnologias de instantâneo incorporado. Se pretender um gancho para as suas aplicações descarregar escreve no disco antes de efetuar a operação de instantâneo. [Velero][velero] can back up persistent volumes along with additional cluster resources and configurations. If you can't [remove state from your applications][remove-state], fazer backup dos dados dos volumes persistentes e testar regularmente as operações de restauro para verificar a integridade dos dados e os processos necessários.

Compreenda as limitações das abordagens diferentes para cópias de segurança de dados e se precisa silenciar dos dados antes de instantâneo. Cópias de segurança de dados não necessariamente lhe permite restaurar o ambiente de aplicação de implementação de cluster. Para obter mais informações sobre esses cenários, consulte [melhores práticas para a recuperação de desastre e continuidade de negócio no AKS][best-practices-multi-region].

## <a name="next-steps"></a>Passos Seguintes

Este artigo concentra-se no armazenamento de melhores práticas no AKS. Para obter mais informações sobre noções básicas do armazenamento no Kubernetes, consulte [conceitos de armazenamento para aplicações no AKS][aks-concepts-storage].

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
