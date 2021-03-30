---
title: Melhores práticas para armazenamento e backup
titleSuffix: Azure Kubernetes Service
description: Aprenda as melhores práticas do operador do cluster para armazenamento, encriptação de dados e backups no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 722fe393ad7637be20360463a4c3b6234224a036
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88653975"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Melhores práticas para armazenamento e backups no Serviço Azure Kubernetes (AKS)

À medida que cria e gere clusters no Serviço Azure Kubernetes (AKS), as suas aplicações muitas vezes precisam de armazenamento. É importante entender as necessidades de desempenho e os métodos de acesso para as cápsulas para que possa fornecer o armazenamento adequado às aplicações. O tamanho do nó AKS pode ter impacto nestas escolhas de armazenamento. Também deve planear formas de fazer o back up e testar o processo de restauro para armazenamento anexado.

Este artigo de boas práticas centra-se nas considerações de armazenamento para os operadores de cluster. Neste artigo, aprende-se:

> [!div class="checklist"]
> * Que tipos de armazenamento estão disponíveis
> * Como dimensionar corretamente os nóns AKS para o desempenho do armazenamento
> * Diferenças entre o fornecimento dinâmico e estático dos volumes
> * Formas de fazer o back up e proteger os seus volumes de dados

## <a name="choose-the-appropriate-storage-type"></a>Escolha o tipo de armazenamento apropriado

**Orientação para as melhores práticas** - Compreenda as necessidades da sua aplicação para escolher o armazenamento certo. Utilize armazenamento de alto desempenho, apoiado por SSD para cargas de trabalho de produção. Plano para armazenamento em rede quando há necessidade de múltiplas ligações simultâneas.

As aplicações requerem frequentemente diferentes tipos e velocidades de armazenamento. As suas aplicações precisam de armazenamento que se conecte a cápsulas individuais ou partilhadas em várias cápsulas? O armazenamento é apenas para acesso de leitura aos dados, ou para escrever grandes quantidades de dados estruturados? Estas necessidades de armazenamento determinam o tipo de armazenamento mais adequado a utilizar.

A tabela que se segue descreve os tipos de armazenamento disponíveis e as suas capacidades:

| Caso de utilização | Plugin de volume | Ler/escrever uma vez | Ler apenas muitos | Ler/escrever muitos | Suporte ao servidor do Windows |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Configuração partilhada       | Ficheiros do Azure   | Yes | Yes | Yes | Yes |
| Dados estruturados da aplicação        | Discos do Azure   | Yes | No  | No  | Yes |
| Dados não estruturados, operações do sistema de ficheiros | [BlobFuse][blobfuse] | Yes | Yes | Yes | No |

Os dois tipos primários de armazenamento fornecidos para volumes em AKS são apoiados por Discos Azure ou Ficheiros Azure. Para melhorar a segurança, ambos os tipos de armazenamento utilizam encriptação do serviço de armazenamento Azure (SSE) por padrão que encripta os dados em repouso. Os discos não podem ser encriptados utilizando a encriptação do disco Azure ao nível do nó AKS.

Tanto os Ficheiros Azure como os Discos Azure estão disponíveis nos níveis de desempenho Standard e Premium:

- *Os* discos premium são apoiados por discos de estado sólido de alto desempenho (SSDs). Os discos premium são recomendados para todas as cargas de trabalho de produção.
- *Os* discos standard são apoiados por discos de fiação regulares (HDDs), e são bons para dados de arquivo ou de acesso pouco frequente.

Compreenda as necessidades de desempenho da aplicação e os padrões de acesso para escolher o nível de armazenamento apropriado. Para obter mais informações sobre os tamanhos e níveis de desempenho dos Discos Geridos Geridos, consulte [a visão geral dos Discos Geridos Azure][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Criar e utilizar aulas de armazenamento para definir necessidades de aplicação

O tipo de armazenamento que utiliza é definido usando *as aulas de armazenamento* Kubernetes. A classe de armazenamento é então referenciada na pod ou especificação de implantação. Estas definições trabalham em conjunto para criar o armazenamento apropriado e conectá-lo a cápsulas. Para obter mais informações, consulte [as aulas de Armazenamento em AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Dimensione os nó de nó para necessidades de armazenamento

**Orientação para as melhores práticas** - Cada tamanho do nó suporta um número máximo de discos. Diferentes tamanhos de nó também fornecem diferentes quantidades de armazenamento local e largura de banda de rede. O plano para a sua aplicação exige implantar o tamanho adequado dos nós.

Os nós AKS funcionam como VMs Azure. Diferentes tipos e tamanhos de VM estão disponíveis. Cada tamanho VM fornece uma quantidade diferente de recursos fundamentais, tais como CPU e memória. Estes tamanhos VM têm um número máximo de discos que podem ser ligados. O desempenho do armazenamento também varia entre os tamanhos VM para o máximo de IOPS de disco local e anexado (operações de entrada/saída por segundo).

Se as suas aplicações necessitarem de Discos Azure como solução de armazenamento, planeie e escolha um tamanho VM de nó apropriado. A quantidade de CPU e memória não é o único fator quando se escolhe um tamanho VM. As capacidades de armazenamento também são importantes. Por exemplo, tanto os tamanhos *de Standard_B2ms* como *Standard_DS2_v2* VM incluem uma quantidade semelhante de CPU e recursos de memória. O seu desempenho potencial de armazenamento é diferente, como mostra a tabela seguinte:

| Tipo e tamanho do nó | vCPU | Memória (GiB) | Discos de dados máximos | Max disco sem cócega IOPS | Produção max sem arrochados (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Aqui, o *Standard_DS2_v2* permite duplicar o número de discos anexos, e fornece três a quatro vezes a quantidade de IOPS e produção de disco. Se olhar apenas para os recursos de computação nuclear e custos comparados, pode escolher o *tamanho Standard_B2ms* VM e ter um fraco desempenho e limitações de armazenamento. Trabalhe com a sua equipa de desenvolvimento de aplicações para compreender a sua capacidade de armazenamento e necessidades de desempenho. Escolha o tamanho VM adequado para que os nós AKS satisfaçam ou excedam as suas necessidades de desempenho. Aplicações de base regulares para ajustar o tamanho de VM conforme necessário.

Para obter mais informações sobre os tamanhos VM disponíveis, consulte [tamanhos para máquinas virtuais Linux em Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Volumes de provisão dinâmica

**Orientação de boas práticas** - Para reduzir a sobrecarga de gestão e deixar escalar, não crie e atribua volumes persistentes. Use o provisionamento dinâmico. Nas suas aulas de armazenamento, defina a política de recuperação adequada para minimizar os custos de armazenamento não necessários uma vez que as cápsulas são eliminadas.

Quando é necessário anexar o armazenamento às cápsulas, utiliza volumes persistentes. Estes volumes persistentes podem ser criados manualmente ou dinamicamente. A criação manual de volumes persistentes adiciona a sobrecarga de gestão e limita a sua capacidade de escala. Utilize um fornecimento dinâmico de volume persistente para simplificar a gestão do armazenamento e permitir que as suas aplicações cresçam e aumentem conforme necessário.

![Reclamações de volume persistentes num cluster Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Uma reivindicação de volume persistente (PVC) permite-lhe criar dinamicamente o armazenamento conforme necessário. Os discos Azure subjacentes são criados à medida que as cápsulas os solicitam. Na definição da vagem, solicita-se que seja criado um volume e ligado a um caminho de montagem designado.

Para os conceitos sobre como criar e utilizar dinâmicamente volumes, consulte [As Reivindicações persistentes de Volumes.][aks-concepts-storage-pvcs]

Para ver estes volumes em ação, veja como criar e utilizar dinamicamente um volume persistente com [Discos Azure][dynamic-disks] ou [Ficheiros Azure][dynamic-files].

Como parte das definições da sua classe de armazenamento, desaproprie a *recuperação adequadaPolicy*. Esta recuperação A Política controla o comportamento do recurso de armazenamento Azure subjacente quando a cápsula é eliminada e o volume persistente pode deixar de ser necessário. O recurso de armazenamento subjacente pode ser eliminado ou retido para utilização com uma futura cápsula. A recuperação A Política pode definir para *reter* ou *eliminar*. Compreenda as necessidades da sua aplicação e implemente verificações regulares de armazenamento que são retidas para minimizar a quantidade de armazenamento não utilizado que é usado e faturado.

Para obter mais informações sobre as opções de classe de armazenamento, consulte [as políticas de recuperação de armazenamento][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Proteja e apoie os seus dados

**Orientação para as melhores práticas** - Faça o backup dos seus dados utilizando uma ferramenta apropriada para o seu tipo de armazenamento, como Velero ou Azure Backup. Verifique a integridade e segurança dos reforços.

Quando as suas aplicações armazenam e consomem dados persistentes em discos ou em ficheiros, é necessário fazer cópias de segurança ou instantâneos regulares desses dados. Os Discos Azure podem utilizar tecnologias instantâneas incorporadas. Pode ser necessário procurar as suas aplicações para lavar as gravações no disco antes de efetuar a operação instantânea. [Velero][velero] pode apoiar volumes persistentes juntamente com recursos e configurações adicionais de cluster. Se não conseguir remover o [estado das suas aplicações,][remove-state]faça o check-up dos dados de volumes persistentes e teste regularmente as operações de restauro para verificar a integridade dos dados e os processos necessários.

Compreenda as limitações das diferentes abordagens às cópias de segurança de dados e se precisar de questionar os seus dados antes do instantâneo. As cópias de segurança de dados não permitem necessariamente restaurar o ambiente de aplicação da implantação do cluster. Para obter mais informações sobre estes cenários, consulte [as melhores práticas para a continuidade do negócio e recuperação de desastres na AKS.][best-practices-multi-region]

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se nas melhores práticas de armazenamento em AKS. Para obter mais informações sobre o básico de armazenamento em Kubernetes, consulte [conceitos de Armazenamento para aplicações em AKS][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
