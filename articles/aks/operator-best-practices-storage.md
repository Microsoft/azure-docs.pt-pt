---
title: Práticas recomendadas do operador – armazenamento dos serviços de Kubernetes no Azure (AKS)
description: Conheça as práticas recomendadas de operador de cluster de armazenamento, encriptação de dados e as cópias de segurança no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 6521655ded45f0a1d15c3ec40a44993d757b8854
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594673"
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

**Orientação** de boas práticas - Compreenda as necessidades da sua aplicação para escolher o armazenamento certo. Utilize armazenamento apoiadas por SSD para cargas de trabalho de produção de alto desempenho. Planear para armazenamento baseado em rede quando há uma necessidade de várias conexões simultâneas.

Aplicativos geralmente exigem a diferentes tipos e velocidades de armazenamento. As aplicações necessitam de armazenamento que se liga a pods individuais ou compartilhado entre vários pods? É o armazenamento para acesso só de leitura aos dados, ou escrever grandes quantidades de dados estruturados? Estes armazenamento tem de determinar o tipo mais adequado de armazenamento a utilizar.

A tabela seguinte descreve os tipos de armazenamento disponível e as respetivas funcionalidades:

| Caso de utilização | Plug-in do volume | Leitura/escrita uma vez | Muitos só de leitura | Leitura/escrita muitos | Suporte para recipientes do Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Configuração partilhada       | Ficheiros do Azure   | Sim | Sim | Sim | Sim |
| Dados estruturados de aplicação        | Discos do Azure   | Sim | Não  | Não  | Sim |
| Dados não estruturados, operações de sistema de ficheiros | [BlobFuse (pré-visualização)][blobfuse] | Sim | Sim | Sim | Não |

Os dois principais tipos de armazenamento fornecida para os volumes de AKS são apoiados por discos do Azure ou de ficheiros do Azure. Para melhorar a segurança, ambos os tipos de armazenamento utilizam o Azure Storage Service Encryption (SSE) por padrão, que criptografa dados em repouso. Atualmente não não possível encriptar discos com o Azure Disk Encryption ao nível do nó do AKS.

Os ficheiros do Azure estão atualmente disponíveis no escalão padrão de desempenho. Discos do Azure estão disponíveis nos escalões de desempenho Standard e Premium:

- *Os* discos premium são apoiados por discos de estado sólido de alto desempenho (SSDs). Os discos Premium são recomendados para todas as cargas de trabalho de produção.
- *Os* discos standard são apoiados por discos de fiação regulares (HDDs), e são bons para dados de arquivo ou pouco frequentemente acedidos.

Compreender as necessidades de desempenho do aplicativo e padrões para escolher a camada de armazenamento adequada de acesso. Para obter mais informações sobre tamanhos de discos geridos e níveis de desempenho, consulte a [visão geral dos Discos Geridos do Azure][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Criar e usar as classes de armazenamento para definir as necessidades da aplicação

O tipo de armazenamento que utiliza é definido utilizando *as classes*de armazenamento kubernetes. A classe de armazenamento, em seguida, é referenciada na especificação pod ou implementação. Estas definições funcionam em conjunto para criar o armazenamento adequado e ligá-lo ao pods. Para mais informações, consulte as aulas de [armazenamento em AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Dimensionar os nós das necessidades de armazenamento

**Orientação de boas práticas** - Cada tamanho do nó suporta um número máximo de discos. Tamanhos de nó diferente também oferecem diferentes quantidades de largura de banda de rede e de armazenamento local. Planejar para suas necessidades de aplicações implementar um tamanho adequado de nós.

Nós do AKS são executados como VMs do Azure. Diferentes tipos e tamanhos de VM estão disponíveis. Cada tamanho de VM fornece uma quantidade diferente de recursos principais, tais como CPU e memória. Estes tamanhos VM tem um número máximo de discos que podem ser anexados. Desempenho de armazenamento também varia entre tamanhos de VM para o disco local e anexado o máximo IOPS (operações de entrada/saída por segundo).

Se as suas aplicações necessitam de discos do Azure como solução de armazenamento, planeie e escolha um tamanho VM de nó adequado. A quantidade de CPU e memória não é o único fator ao escolher um tamanho de VM. As capacidades de armazenamento também são importantes. Por exemplo, tanto os tamanhos *de Standard_B2ms* como *Standard_DS2_v2* VM incluem uma quantidade semelhante de CPU e recursos de memória. O desempenho de armazenamento possíveis é diferente, como mostrado na tabela a seguir:

| Tipo de nó e o tamanho | vCPU | Memória (GiB) | Discos de dados máximos | IOPS de disco eliminadas do cache máx. | Débito máximo do eliminadas do cache (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Aqui, o *Standard_DS2_v2* permite o dobro do número de discos anexados, e fornece três a quatro vezes a quantidade de IOPS e entrada de disco. Se olhar apenas para os recursos de base da computação e para os custos comparados, pode escolher o *tamanho Standard_B2ms* VM e ter um fraco desempenho e limitações de armazenamento. Trabalhar com a sua equipa de desenvolvimento de aplicativos para compreender suas necessidades de capacidade e desempenho de armazenamento. Escolha um tamanho VM adequado para os nós do AKS atingir ou exceder as suas necessidades de desempenho. Regularmente da linha de base aplicativos para ajustar o tamanho da VM conforme necessário.

Para obter mais informações sobre os tamanhos vm disponíveis, consulte [Sizes para máquinas virtuais Linux em Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dinamicamente os volumes de aprovisionamento

**Orientação de boas práticas** - Para reduzir a gestão e deixar-lhe escalar, não crie e atribua volumes persistentes. Utilize o aprovisionamento dinâmico. As classes de armazenamento, defina a política de recuperar adequada para minimizar os custos de armazenamento desnecessário, depois de pods são eliminados.

Quando precisar de ligar o armazenamento ao pods, se utilizar volumes persistentes. Estes volumes persistentes podem ser criados manualmente ou dinamicamente. Criação manual de volumes persistentes adiciona a sobrecarga de gerenciamento e limita a capacidade de dimensionar. Utilize o volume persistente dinâmico de aprovisionamento para simplificar a gestão de armazenamento e permitem às aplicações aumentar e dimensionar conforme necessário.

![Afirmações de volume persistente num cluster de serviços de Kubernetes do Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

Uma afirmação de volume persistente (PVC) permite-lhe criar dinamicamente o armazenamento, conforme necessário. Os discos do Azure subjacentes são criados como pods de pedido-los. Na definição do pod, solicita um volume para ser criados e anexados a um caminho de montagem projetado

Para os conceitos sobre como criar e utilizar volumes de forma dinâmica, consulte Reivindicações de [Volumes Persistentes][aks-concepts-storage-pvcs].

Para ver estes volumes em ação, veja como criar e utilizar um volume persistente com [Discos Azure][dynamic-disks] ou [Ficheiros Azure][dynamic-files].

Como parte das definições da sua classe de armazenamento, delineie a política de *recuperação*adequada . Este reclaimPolicy controla o comportamento do recurso de armazenamento do Azure subjacentes, quando o pod é eliminado e o volume persistente já não poderá ser necessário. O recurso de armazenamento subjacente pode ser eliminado ou retido para utilização com um pod futura. A política de recuperação pode definir para *reter* ou *eliminar*. Compreender as necessidades da sua aplicação e implementar verificações regulares para o armazenamento que é mantido, para minimizar a quantidade de armazenamento não utilizado, que é utilizado e cobrada.

Para obter mais informações sobre as opções da classe de armazenamento, consulte as políticas de recuperação de [armazenamento.][reclaim-policy]

## <a name="secure-and-back-up-your-data"></a>Proteger e fazer backup dos dados

**Orientação das melhores práticas** - Recue os seus dados utilizando uma ferramenta apropriada para o seu tipo de armazenamento, como velero ou recuperação do site Azure. Certifique-se de que a integridade e segurança, essas cópias de segurança.

Quando os aplicativos armazenem e utilizem os dados mantidos nos discos ou em arquivos, que precisa de efetuar cópias de segurança regulares ou instantâneos de dados. Discos do Azure podem utilizar tecnologias de instantâneo incorporado. Pode ser necessário procurar as suas aplicações para descarregar escritos para o disco antes de executar a operação instantânea. [Velero][velero] pode fazer o apoio a volumes persistentes juntamente com recursos e configurações adicionais de cluster. Se não conseguir remover o [estado das suas aplicações,][remove-state]faça o check-up dos dados de volumes persistentes e teste regularmente as operações de restauro para verificar a integridade dos dados e os processos necessários.

Compreenda as limitações das abordagens diferentes para cópias de segurança de dados e se precisa silenciar dos dados antes de instantâneo. Cópias de segurança de dados não necessariamente lhe permite restaurar o ambiente de aplicação de implementação de cluster. Para obter mais informações sobre esses cenários, consulte [as melhores práticas para a continuidade do negócio e recuperação de desastres na AKS][best-practices-multi-region].

## <a name="next-steps"></a>Passos seguintes

Este artigo concentra-se no armazenamento de melhores práticas no AKS. Para obter mais informações sobre os fundamentos de armazenamento em Kubernetes, consulte conceitos de [armazenamento para aplicações em AKS][aks-concepts-storage].

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
