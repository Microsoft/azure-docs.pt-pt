---
title: Melhores práticas para armazenamento e backup
titleSuffix: Azure Kubernetes Service
description: Aprenda as melhores práticas do operador do cluster para armazenamento, encriptação de dados e backups no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 9b3ee6fd7eea958a573743b21bf8940458e2a965
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104920"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Melhores práticas para armazenamento e backups no Serviço Azure Kubernetes (AKS)

À medida que cria e gere clusters no Serviço Azure Kubernetes (AKS), as suas aplicações muitas vezes precisam de armazenamento. Certifique-se de que compreende as necessidades de desempenho do pod e os métodos de acesso para que possa selecionar o melhor armazenamento para a sua aplicação. O tamanho do nó AKS pode ter impacto nas suas escolhas de armazenamento. Planeie formas de fazer o back up e testar o processo de restauro para armazenamento anexado.

Este artigo de boas práticas centra-se nas considerações de armazenamento para os operadores de cluster. Neste artigo, aprende-se:

> [!div class="checklist"]
> * Que tipos de armazenamento estão disponíveis.
> * Como dimensionar corretamente os nós AKS para o desempenho do armazenamento.
> * Diferenças entre o fornecimento dinâmico e estático dos volumes.
> * Formas de fazer o back up e proteger os seus volumes de dados.

## <a name="choose-the-appropriate-storage-type"></a>Escolha o tipo de armazenamento apropriado

> **Orientação de melhor prática**
> 
> Compreenda as necessidades da sua aplicação para escolher o armazenamento certo. Utilize armazenamento de alto desempenho, apoiado por SSD para cargas de trabalho de produção. Planeie o armazenamento em rede quando necessitar de várias ligações simultâneas.

As aplicações requerem frequentemente diferentes tipos e velocidades de armazenamento. Determine o tipo de armazenamento mais adequado fazendo as seguintes perguntas. 
* As suas aplicações precisam de armazenamento que se ligue a cápsulas individuais?
* As suas aplicações precisam de armazenamento partilhado em várias cápsulas? 
* O armazenamento é apenas para leitura de acesso a dados?
* O armazenamento será utilizado para escrever grandes quantidades de dados estruturados? 

A tabela que se segue descreve os tipos de armazenamento disponíveis e as suas capacidades:

| Caso de utilização | Plugin de volume | Ler/escrever uma vez | Ler apenas muitos | Ler/escrever muitos | Suporte ao servidor do Windows |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Configuração partilhada       | Ficheiros do Azure   | Yes | Yes | Yes | Yes |
| Dados estruturados da aplicação        | Discos do Azure   | Yes | No  | No  | Yes |
| Dados não estruturados, operações do sistema de ficheiros | [BlobFuse][blobfuse] | Yes | Yes | Yes | No |

A AKS fornece dois tipos primários de armazenamento seguro para volumes apoiados por Discos Azure ou Ficheiros Azure. Ambos utilizam a encriptação padrão do serviço de armazenamento Azure (SSE) que encripta os dados em repouso. Os discos não podem ser encriptados utilizando a encriptação do disco Azure ao nível do nó AKS.

Tanto os Ficheiros Azure como os Discos Azure estão disponíveis nos níveis de desempenho Standard e Premium:

- *Discos* premium
    - Apoiados por discos de estado sólido de alto desempenho (SSDs). 
    - Recomendado para todas as cargas de trabalho de produção.
- *Discos standard*
    - Apoiados por discos de fiação regulares (HDDs).
    - Bom para dados de arquivo ou pouco frequentemente acedidos.

Compreenda as necessidades de desempenho da aplicação e os padrões de acesso para escolher o nível de armazenamento apropriado. Para obter mais informações sobre os tamanhos e níveis de desempenho dos Discos Geridos Geridos, consulte [a visão geral dos Discos Geridos Azure][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Criar e utilizar aulas de armazenamento para definir necessidades de aplicação

Defina o tipo de armazenamento que pretende utilizar *as aulas de armazenamento* kubernetes. A classe de armazenamento é então referenciada na pod ou especificação de implantação. As definições da classe de armazenamento funcionam em conjunto para criar o armazenamento apropriado e conectá-lo a cápsulas. 

Para obter mais informações, consulte [as aulas de Armazenamento em AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Dimensione os nó de nó para necessidades de armazenamento

> **Orientação de melhor prática**
> 
> Cada tamanho do nó suporta um número máximo de discos. Diferentes tamanhos de nó também fornecem diferentes quantidades de armazenamento local e largura de banda de rede. Planeie adequadamente para a sua aplicação exige implementar o tamanho certo dos nós.

Os nós AKS funcionam como vários tipos e tamanhos Azure VM. Cada tamanho VM fornece:
* Uma quantidade diferente de recursos fundamentais, como CPU e memória. 
* Um número máximo de discos que podem ser ligados. 

O desempenho do armazenamento também varia entre os tamanhos VM para o máximo de IOPS de disco local e anexado (operações de entrada/saída por segundo).

Se as suas aplicações necessitarem de Discos Azure como solução de armazenamento, planeifique um tamanho VM de nó apropriado. As capacidades de armazenamento e os valores de CPU e memória desempenham um papel importante na decisão sobre um tamanho VM. 

Por exemplo, embora tanto os tamanhos *de Standard_B2ms* como *Standard_DS2_v2* VM incluam uma quantidade semelhante de CPU e recursos de memória, o seu desempenho potencial de armazenamento é diferente:

| Tipo e tamanho do nó | vCPU | Memória (GiB) | Discos de dados máximos | Max disco sem cócega IOPS | Produção max sem arrochados (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Neste exemplo, o *Standard_DS2_v2* oferece o dobro dos discos anexados e três a quatro vezes a quantidade de IOPS e de produção de disco. Se comparasse apenas os recursos de computação nuclear e os custos comparados, poderia ter escolhido o *tamanho Standard_B2ms* VM com fraco desempenho de armazenamento e limitações. 

Trabalhe com a sua equipa de desenvolvimento de aplicações para compreender a sua capacidade de armazenamento e necessidades de desempenho. Escolha o tamanho VM adequado para que os nós AKS satisfaçam ou excedam as suas necessidades de desempenho. Aplicações de base regulares para ajustar o tamanho de VM conforme necessário.

Para obter mais informações sobre os tamanhos VM disponíveis, consulte [tamanhos para máquinas virtuais Linux em Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Volumes de provisão dinâmica

> **Orientação de melhor prática** 
>
> Para reduzir a sobrecarga de gestão e permitir a escala, evite criar estáticamente e atribuir volumes persistentes. Use o provisionamento dinâmico. Nas suas aulas de armazenamento, defina a política de recuperação adequada para minimizar os custos de armazenamento não necessários uma vez que as cápsulas são eliminadas.

Para fixar o armazenamento em cápsulas, utilize volumes persistentes. Volumes persistentes podem ser criados manualmente ou dinamicamente. Criar volumes persistentes adiciona manualmente sobrecarga de gestão e limita a sua capacidade de escala. Em vez disso, provisões de volume persistente dinamicamente para simplificar a gestão do armazenamento e permitir que as suas aplicações cresçam e aumentem conforme necessário.

![Reclamações de volume persistentes num cluster Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Uma reivindicação de volume persistente (PVC) permite-lhe criar dinamicamente o armazenamento conforme necessário. Os discos Azure subjacentes são criados à medida que as cápsulas os solicitam. Na definição da vagem, solicite um volume a ser criado e ligado a um caminho de montagem designado.

Para os conceitos sobre como criar e utilizar dinâmicamente volumes, consulte [As Reivindicações persistentes de Volumes.][aks-concepts-storage-pvcs]

Para ver estes volumes em ação, veja como criar e utilizar dinamicamente um volume persistente com [Discos Azure][dynamic-disks] ou [Ficheiros Azure][dynamic-files].

Como parte das definições da sua classe de armazenamento, desaproprie a *recuperação adequadaPolicy*. Esta recuperaçãoPolicy controla o comportamento do recurso de armazenamento Azure subjacente quando a cápsula é eliminada. O recurso de armazenamento subjacente pode ser eliminado ou retido para futura utilização do pod. Desementar a Política de Reprodução para *reter* ou *apagar*. 

Compreenda as necessidades da sua aplicação e implemente verificações regulares para armazenamento retido para minimizar a quantidade de armazenamento não reutilizado e faturado.

Para obter mais informações sobre as opções de classe de armazenamento, consulte [as políticas de recuperação de armazenamento][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Proteja e apoie os seus dados

> **Orientação de melhor prática** 
> 
> Faça o backup dos seus dados utilizando uma ferramenta apropriada para o seu tipo de armazenamento, como Velero ou Azure Backup. Verifique a integridade e segurança dos reforços.

Quando as suas aplicações armazenam e consomem dados persistentes em discos ou em ficheiros, é necessário fazer cópias de segurança ou instantâneos regulares desses dados. Os Discos Azure podem utilizar tecnologias instantâneas incorporadas. As suas aplicações podem ter de descarregar as gravações para o disco antes de efetuar a operação instantânea. [Velero][velero] pode apoiar volumes persistentes juntamente com recursos e configurações adicionais de cluster. Se não conseguir remover o [estado das suas aplicações,][remove-state]faça o check-up dos dados de volumes persistentes e teste regularmente as operações de restauro para verificar a integridade dos dados e os processos necessários.

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
