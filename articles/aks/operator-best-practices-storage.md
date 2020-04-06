---
title: Boas práticas para armazenamento e backup
titleSuffix: Azure Kubernetes Service
description: Conheça as melhores práticas do operador de cluster para armazenamento, encriptação de dados e backups no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 90abf2e36fd46c707904d87f00362091fe931743
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668106"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Boas práticas para armazenamento e backups no Serviço Azure Kubernetes (AKS)

À medida que cria e gere clusters no Serviço Azure Kubernetes (AKS), as suas aplicações muitas vezes precisam de armazenamento. É importante compreender as necessidades de desempenho e os métodos de acesso para as cápsulas para que possa fornecer o armazenamento adequado às aplicações. O tamanho do nó AKS pode afetar estas escolhas de armazenamento. Também deve planear formas de fazer o backback e testar o processo de restauro para armazenamento anexado.

Este artigo de boas práticas centra-se nas considerações de armazenamento para os operadores de cluster. Neste artigo, aprende-se:

> [!div class="checklist"]
> * Que tipos de armazenamento estão disponíveis
> * Como dimensionar corretamente os nódosos AKS para o desempenho do armazenamento
> * Diferenças entre o fornecimento dinâmico e estático dos volumes
> * Formas de fazer o back up e proteger os seus volumes de dados

## <a name="choose-the-appropriate-storage-type"></a>Escolha o tipo de armazenamento apropriado

**Orientação** de boas práticas - Compreenda as necessidades da sua aplicação para escolher o armazenamento certo. Utilize armazenamento de alto desempenho, apoiado por SSD para cargas de trabalho de produção. Planeie armazenamento baseado em rede quando houver necessidade de múltiplas ligações simultâneas.

As aplicações requerem frequentemente diferentes tipos e velocidades de armazenamento. As suas aplicações precisam de armazenamento que se conectem a cápsulas individuais ou partilhadas em várias cápsulas? O armazenamento para acesso apenas a leitura a dados, ou para escrever grandes quantidades de dados estruturados? Estas necessidades de armazenamento determinam o tipo de armazenamento mais adequado a utilizar.

A tabela que se segue descreve os tipos de armazenamento disponíveis e as suas capacidades:

| Caso de utilização | Plugin de volume | Ler/escrever uma vez | Ler apenas muitos | Ler/escrever muitos | Suporte para recipientes do Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Configuração partilhada       | Ficheiros do Azure   | Sim | Sim | Sim | Sim |
| Dados estruturados da aplicação        | Discos do Azure   | Sim | Não  | Não  | Sim |
| Dados não estruturados, operações do sistema de ficheiros | [BlobFuse][blobfuse] | Sim | Sim | Sim | Não |

Os dois principais tipos de armazenamento fornecidos para volumes em AKS são apoiados por Discos Azure ou Ficheiros Azure. Para melhorar a segurança, ambos os tipos de armazenamento utilizam a Encriptação do Serviço de Armazenamento Azure (SSE) por padrão que encripta os dados em repouso. Os discos não podem atualmente ser encriptados utilizando encriptação de disco azure ao nível do nó AKS.

Os Ficheiros Azure estão atualmente disponíveis no nível de desempenho Standard. Os Discos Azure estão disponíveis nos níveis de desempenho Standard e Premium:

- *Os* discos premium são apoiados por discos de estado sólido de alto desempenho (SSDs). São recomendados discos premium para todas as cargas de trabalho de produção.
- *Os* discos standard são apoiados por discos de fiação regulares (HDDs), e são bons para dados de arquivo ou pouco frequentemente acedidos.

Compreenda as necessidades de desempenho da aplicação e os padrões de acesso para escolher o nível de armazenamento apropriado. Para obter mais informações sobre tamanhos de discos geridos e níveis de desempenho, consulte a [visão geral dos Discos Geridos do Azure][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Criar e usar aulas de armazenamento para definir necessidades de aplicação

O tipo de armazenamento que utiliza é definido utilizando *as classes*de armazenamento kubernetes. A classe de armazenamento é então referenciada na cápsula ou especificação de implementação. Estas definições trabalham em conjunto para criar o armazenamento apropriado e ligá-lo a casulos. Para mais informações, consulte as aulas de [armazenamento em AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Tamanho dos nódosos para necessidades de armazenamento

**Orientação de boas práticas** - Cada tamanho do nó suporta um número máximo de discos. Diferentes tamanhos de nó também fornecem diferentes quantidades de armazenamento local e largura de banda da rede. Planeie as exigências da sua aplicação para implantar o tamanho adequado dos nódosos.

Os narizes da AKS funcionam como VMs Azure. Diferentes tipos e tamanhos de VM estão disponíveis. Cada tamanho VM fornece uma quantidade diferente de recursos fundamentais, tais como CPU e memória. Estes tamanhos vm têm um número máximo de discos que podem ser anexados. O desempenho do armazenamento também varia entre os tamanhos vm para o iOPS máximo do disco local e anexado (operações de entrada/saída por segundo).

Se as suas aplicações exigirem que os Discos Azure sejam a sua solução de armazenamento, planeie e escolha um tamanho VM de nó apropriado. A quantidade de CPU e memória não é o único fator quando se escolhe um tamanho VM. As capacidades de armazenamento também são importantes. Por exemplo, tanto os tamanhos *de Standard_B2ms* como *Standard_DS2_v2* VM incluem uma quantidade semelhante de CPU e recursos de memória. O seu potencial desempenho de armazenamento é diferente, como mostra a tabela seguinte:

| Tipo de nó e tamanho | vCPU | Memória (GiB) | Discos de dados máximos | IOPS de disco não cached Max | Entrada max uncached (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Aqui, o *Standard_DS2_v2* permite o dobro do número de discos anexados, e fornece três a quatro vezes a quantidade de IOPS e entrada de disco. Se olhar apenas para os recursos de base da computação e para os custos comparados, pode escolher o *tamanho Standard_B2ms* VM e ter um fraco desempenho e limitações de armazenamento. Trabalhe com a sua equipa de desenvolvimento de aplicações para compreender a sua capacidade de armazenamento e necessidades de desempenho. Escolha o tamanho de VM apropriado para os nódosos AKS satisfazerem ou excederem as suas necessidades de desempenho. Aplicações de base regularpara ajustar o tamanho do VM conforme necessário.

Para obter mais informações sobre os tamanhos vm disponíveis, consulte [Sizes para máquinas virtuais Linux em Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Volumes de provisão dinamicamente

**Orientação de boas práticas** - Para reduzir a gestão e deixar-lhe escalar, não crie e atribua volumes persistentes. Use o provisionamento dinâmico. Nas suas aulas de armazenamento, defina a política de recuperação adequada para minimizar os custos de armazenamento desnecessários uma vez que as cápsulas são eliminadas.

Quando precisa de anexar o armazenamento a cápsulas, utiliza volumes persistentes. Estes volumes persistentes podem ser criados manual ou dinamicamente. A criação manual de volumes persistentes adiciona a gestão aérea e limita a sua capacidade de escala. Utilize um fornecimento dinâmico de volume persistente para simplificar a gestão do armazenamento e permitir que as suas aplicações cresçam e escaldam conforme necessário.

![Reclamações de volume persistentes num cluster azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Uma reivindicação persistente de volume (PVC) permite criar dinamicamente o armazenamento conforme necessário. Os discos Azure subjacentes são criados à medida que as cápsulas os solicitam. Na definição da cápsula, solicita a criação e a fixação de um volume a um caminho de montagem designado.

Para os conceitos sobre como criar e utilizar volumes de forma dinâmica, consulte Reivindicações de [Volumes Persistentes][aks-concepts-storage-pvcs].

Para ver estes volumes em ação, veja como criar e utilizar um volume persistente com [Discos Azure][dynamic-disks] ou [Ficheiros Azure][dynamic-files].

Como parte das definições da sua classe de armazenamento, delineie a política de *recuperação*adequada . Esta recuperaçãoPolicy controla o comportamento do recurso de armazenamento Azure subjacente quando a cápsula é eliminada e o volume persistente pode deixar de ser necessário. O recurso de armazenamento subjacente pode ser eliminado ou retido para utilização com uma futura cápsula. A política de recuperação pode definir para *reter* ou *eliminar*. Compreenda as necessidades da sua aplicação e implemente controlos regulares para armazenamento que seja retido para minimizar a quantidade de armazenamento não utilizado que é usado e faturado.

Para obter mais informações sobre as opções da classe de armazenamento, consulte as políticas de recuperação de [armazenamento.][reclaim-policy]

## <a name="secure-and-back-up-your-data"></a>Proteja e recue os seus dados

**Orientação das melhores práticas** - Recue os seus dados utilizando uma ferramenta apropriada para o seu tipo de armazenamento, como velero ou recuperação do site Azure. Verifique a integridade e a segurança dos reforços.

Quando as suas aplicações armazenam e consomem dados persistidos em discos ou em ficheiros, é necessário obter cópias de segurança ou fotografias regulares desses dados. Os Discos Azure podem usar tecnologias de instantâneo incorporadas. Pode ser necessário procurar as suas aplicações para descarregar escritos para o disco antes de executar a operação instantânea. [Velero][velero] pode fazer o apoio a volumes persistentes juntamente com recursos e configurações adicionais de cluster. Se não conseguir remover o [estado das suas aplicações,][remove-state]faça o check-up dos dados de volumes persistentes e teste regularmente as operações de restauro para verificar a integridade dos dados e os processos necessários.

Compreenda as limitações das diferentes abordagens às cópias de segurança de dados e se precisar de quiesces os seus dados antes do instantâneo. As cópias de segurança de dados não permitem necessariamente restaurar o ambiente de aplicação da implementação do cluster. Para obter mais informações sobre esses cenários, consulte [as melhores práticas para a continuidade do negócio e recuperação de desastres na AKS][best-practices-multi-region].

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se nas melhores práticas de armazenamento no AKS. Para obter mais informações sobre os fundamentos de armazenamento em Kubernetes, consulte conceitos de [armazenamento para aplicações em AKS][aks-concepts-storage].

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
