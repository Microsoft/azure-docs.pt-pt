---
title: Guia de migração de armazenamento Azure
description: Guia geral de migração de armazenamento descreve orientações básicas para migração de armazenamento
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: f6f00075c7c66679281d776f9472ec4a1a590d76
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231591"
---
# <a name="azure-storage-migration-overview"></a>Visão geral da migração do Azure Storage

Este artigo centra-se nas migrações de armazenamento para Azure e fornece orientações sobre os seguintes cenários de migração de armazenamento:

- Migração de dados não estruturados, tais como ficheiros e objetos
- Migração de dispositivos baseados em blocos, tais como discos e redes de área de armazenamento (SANs)

## <a name="migration-of-unstructured-data"></a>Migração de dados não estruturados

A migração de dados não estruturados inclui os seguintes cenários:

- Migração de ficheiros do armazenamento anexado à rede (NAS) para uma das ofertas de ficheiros Azure:
  - [Ficheiros do Azure](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [soluções independentes de fornecedor de software (ISV).](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview)
- Migração de objetos de soluções de armazenamento de objetos para a plataforma de armazenamento de objetos Azure:
  - [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

### <a name="migration-phases"></a>Fases de migração

Uma migração completa consiste em várias fases diferentes: descoberta, avaliação e migração.

![Diagrama que mostra as fases de descoberta, avaliação e migração da migração](./media/storage-migration-overview/migration-phases.png)

#### <a name="discovery-phase"></a>Fase de descoberta

Na fase de descoberta, você determina todas as fontes que precisam de ser migradas como ações SMB, exportações de NFS ou espaços de nome de objetos. Pode fazer esta fase manualmente ou utilizar ferramentas automatizadas.

#### <a name="assessment-phase"></a>Fase de avaliação

A fase de avaliação é fundamental para compreender as opções disponíveis para a migração. Para reduzir o risco durante a migração, e para evitar armadilhas comuns, siga estes três passos:

| Fase de avaliação                     | Opções                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **Escolha um serviço de armazenamento alvo**            | - Armazenamento de Blob Azure e Armazenamento de Lago de Dados<br>- Ficheiros Azure<br>- Ficheiros Azure NetApp<br>- Soluções ISV |
| **Selecione um método de migração**                  | - Online<br>- Offline<br> - Combinação de ambos                                  |
| **Escolha a melhor ferramenta de migração para o trabalho** | - Ferramentas comerciais (Azure e ISV)<br> - Fonte aberta                             


Existem várias ferramentas comerciais (ISV) que podem ajudar na fase de avaliação. Veja a [matriz de comparação.](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)

##### <a name="choose-a-target-storage-service"></a>Escolha um serviço de armazenamento alvo

A escolha de um serviço de armazenamento de destino depende da aplicação ou dos utilizadores que acedam aos dados. A escolha correta depende tanto dos aspetos técnicos como financeiros. Em primeiro lugar, fazer uma avaliação técnica para avaliar os possíveis objetivos e determinar quais os serviços que satisfazem os requisitos. Em seguida, faça uma avaliação financeira para determinar a melhor escolha.

Para ajudar a selecionar o serviço de armazenamento-alvo para a migração, avalie os seguintes aspetos de cada serviço:

- Suporte de protocolo
- Características de desempenho
- Limites do serviço de armazenamento alvo

O diagrama seguinte é uma árvore de decisão simplificada que ajuda a guiá-lo para o serviço de ficheiros Azure recomendado. Se os serviços nativos da Azure não satisfizerem os requisitos, uma variedade de [soluções independentes de fornecedor de software (ISV)](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview) irão.

Depois de terminar a avaliação técnica e selecionar o alvo adequado, faça uma avaliação de custos para determinar a opção mais rentável.

![Árvore de decisão básica na escolha do serviço de ficheiros correto](./media/storage-migration-overview/files-decision-tree.png)

Para manter a árvore de decisão simples, os limites do serviço de armazenamento alvo não são incorporados no diagrama. Para saber mais sobre os limites atuais, e para determinar se precisa modificar as suas escolhas com base neles, consulte:

- [Limites da conta de armazenamento](/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)
- [Limites de armazenamento de bolhas](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-blob-storage-limits)
- [Metas de escalabilidade e desempenho de Ficheiros do Azure](/azure/storage/files/storage-files-scale-targets)
- [Limites de recursos do Azure NetApp Files](/azure/azure-netapp-files/azure-netapp-files-resource-limits)

Se algum dos limites representar um bloqueador para a utilização de um serviço, o Azure suporta vários fornecedores de armazenamento que oferecem as suas soluções no Azure Marketplace. Para obter informações sobre parceiros ISV validados que fornecem serviços de ficheiros, consulte [os parceiros de Armazenamento Azure para armazenamento primário e secundário.](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview)

##### <a name="select-the-migration-method"></a>Selecione o método de migração

Existem dois métodos básicos de migração para as migrações de armazenamento.

- **Online.** O método online utiliza a rede para a migração de dados. A internet pública ou o [Azure ExpressRoute](/azure/expressroute/expressroute-introduction) podem ser utilizados. Se o serviço não tiver um ponto final público, deve usar uma VPN com internet pública.
- **Offline.** O método offline utiliza um dos dispositivos [Azure Data Box.](https://azure.microsoft.com/services/databox/)

A decisão de utilizar um método online versus um método offline depende da largura de banda de rede disponível. O método on-line é preferido nos casos em que há largura de banda de rede suficiente para realizar uma migração dentro da linha temporal necessária.

É possível usar uma combinação de ambos os métodos, método offline para a migração a granel inicial e um método on-line para migração incremental de mudanças. A utilização de ambos os métodos requer simultaneamente um elevado nível de coordenação e não é recomendado por esta razão. Se optar por utilizar ambos os métodos, isole os conjuntos de dados que são migrados online dos conjuntos de dados que são migrados offline.

Para obter mais informações sobre os diferentes métodos e orientações de migração, consulte [Escolha uma solução Azure para transferência de dados](/azure/storage/common/storage-choose-data-transfer-solution) e [Migrar para ações de ficheiros Azure](/azure/storage/files/storage-files-migration-overview).

##### <a name="choose-the-best-migration-tool-for-the-job"></a>Escolha a melhor ferramenta de migração para o trabalho

Existem várias ferramentas de migração que pode usar para realizar a migração. Alguns são de código aberto como AzCopy, robocopia, xcopia e rsync enquanto outros são comerciais. A lista de ferramentas comerciais disponíveis e comparação entre elas está disponível na nossa [matriz de comparação.](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)

As ferramentas de código aberto são adequadas para migrações em pequena escala. Para a migração dos servidores de ficheiros do Windows para ficheiros Azure, a Microsoft recomenda começar com a capacidade nativa do Azure Files e utilizar [o Azure File Sync](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-file-sync). Para migrações mais complexas, compostas por diferentes fontes, grande capacidade ou requisitos especiais como estrangulamento ou relatórios detalhados com capacidades de auditoria, as ferramentas comerciais são a melhor escolha. Estas ferramentas facilitam a migração e reduzem significativamente o risco. A maioria das ferramentas comerciais também pode realizar a descoberta, que fornece uma entrada valiosa para a avaliação.

#### <a name="migration-phase"></a>Fase de migração

A fase de migração é o passo final de migração que faz movimento de dados e migração. Normalmente, você vai passar pela fase de migração várias vezes para realizar uma transição mais fácil. A fase de migração consiste nos seguintes passos:

1. **Migração inicial.** O passo de migração inicial migra todos os dados da fonte para o alvo. Este passo migra a maior parte dos dados que precisam de ser migrados.
2. **O Resync.** Uma operação de ressínca migra todos os dados que foram alterados após o passo inicial de migração. Pode repetir este passo várias vezes se houver inúmeras alterações. O objetivo de executar várias operações de resync é reduzir o tempo que leva para o passo final. Para dados inativos e para dados que não têm alterações (como dados de backup ou arquivo), pode saltar este passo.
3. **Transição final**. O passo final de transição muda a utilização ativa dos dados da fonte para o alvo e retira a fonte.

A duração da migração para dados não estruturados depende de vários aspetos. Fora do método escolhido, os fatores mais críticos são o tamanho total dos dados e a distribuição do tamanho do ficheiro. Quanto maior for o conjunto total de dados, mais tempo o tempo de migração. Quanto menor o tamanho médio do ficheiro, mais tempo o tempo de migração. Se tiver um grande número de pequenos ficheiros considere arquivá-los em ficheiros maiores (como um ficheiro .tar ou .zip), se aplicável, para reduzir o tempo total de migração.

## <a name="migration-of-block-based-devices"></a>Migração de dispositivos baseados em blocos

A migração de dispositivos baseados em blocos é normalmente feita como parte da migração de máquinas virtuais ou hospedeiros físicos. É um equívoco comum adiar as decisões de armazenamento do bloco até depois da migração. Tomar estas decisões com antecedência com considerações adequadas para os requisitos de carga de trabalho leva a uma migração mais suave para a nuvem.

Para explorar cargas de trabalho para migrar e aproximar-se para tomar, consulte a documentação de [armazenamento do disco Azure,](/azure/virtual-machines/disks-types)e os recursos na página do [produto de armazenamento de disco.](https://azure.microsoft.com/services/storage/disks/#resources) Pode aprender quais os discos que se adequam às suas necessidades e as mais recentes capacidades, como [a explosão do disco.](/azure/virtual-machines/disk-bursting) Para obter informações sobre como migrar as máquinas virtuais juntamente com os dispositivos subjacentes baseados em blocos, consulte a documentação do [Azure Migrate.](/azure/migrate/)

## <a name="see-also"></a>Ver também

- [Escolher uma solução do Azure para a transferência de dados](/azure/storage/common/storage-choose-data-transfer-solution)
- [Comparação de ferramentas de migração comercial](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Migrar para partilhas de ficheiros do Azure](/azure/storage/files/storage-files-migration-overview)
- [Migrar para o armazenamento do Lago de Dados com a plataforma WANdisco LiveData para o Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Copiar ou mover dados para o Azure Storage com a AzCopy](https://aka.ms/azcopy)
- [Migrar grandes conjuntos de dados para o armazenamento de blob Azure com AzReplicate](https://github.com/Azure/AzReplicate/tree/master/)