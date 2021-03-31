---
title: What's new in Microsoft Azure Backup Server (Novidades no Microsoft Azure Backup Server)
description: O Microsoft Azure Backup Server oferece-lhe capacidades de backup melhoradas para proteger VMs, ficheiros e pastas, cargas de trabalho e muito mais.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 39050d0f658e29b82f270f1fe53026e2fb80bfa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91332768"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>Novidades no Microsoft Azure Backup Server (MABS)

## <a name="whats-new-in-mabs-v3-ur1"></a>Novidades no MABS V3 UR1

A versão 3 UR1 do Microsoft Azure Backup Server (MABS) é a mais recente atualização e inclui correções críticas de bugs e outras funcionalidades e melhorias. Para visualizar a lista de erros fixados e as instruções de instalação do MABS V3 UR1, consulte o artigo KB [4534062](https://support.microsoft.com/help/4534062).

>[!NOTE]
>O suporte para o agente de proteção de 32 bits é precotado com MABS v3 UR1. Ver [32 Bit de depreciação do agente de proteção](#32-bit-protection-agent-deprecation).

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>Backups mais rápidos com armazenamento hierárquico usando SSDs

O MABS V2 introduziu [o Modern Backup Storage](backup-mabs-add-storage.md) (MBS), melhorando a utilização e o desempenho do armazenamento. O MBS utiliza o ReFS como sistema de ficheiros subjacente e foi concebido para utilizar o armazenamento híbrido, como o armazenamento hierárquico.

Para alcançar a escala e o desempenho por MBS recomendamos a utilização de uma pequena percentagem (4% do armazenamento global) de armazenamento flash (SSD) com MABS V3 UR1 como um volume hierarquizado em combinação com o armazenamento DPM HDD. MABS V3 UR1 com armazenamento hierárquico oferece backups 50-70% mais rápidos. Consulte o artigo DPM [Configurar MBS com Armazenamento Tiered](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage) para obter etapas para configurar o armazenamento hierárquico.

### <a name="support-for-refs-volumes"></a>Suporte para volumes reFS

Com o MABS V3 UR1, pode fazer o back up dos volumes e cargas de trabalho do ReFS implantados no volume ReFS. Pode fazer o back up das seguintes cargas de trabalho implementadas nos volumes ReFS:

* Sistema operativo (64 bit): Windows Server 2019, 2016, 2012 R2, 2012.
* SQL Server: SQL Server 2019, SQL Server 2017, 2016.
* Troca: Troca 2019, 2016.
* SharePoint: SharePoint 2019, 2016 com o mais recente SP.

>[!NOTE]
> A cópia de segurança dos VMs Hiper-V armazenados num volume ReFS é suportada com MABS V3

>[IMPORTANTE] Identificamos alguns problemas com a cópia de segurança dos volumes deSemplicados do ReFS. Estamos a trabalhar na correção destes, e atualizaremos esta secção assim que tivermos uma correção disponível. Até lá, estamos a remover o suporte para o backup dos volumes de ReFS despromovedados do MABSv3 UR1.

### <a name="azure-vmware-solution-protection-support"></a>Suporte de proteção de solução Azure VMware

Com o MABS v3 UR1, pode agora proteger as máquinas virtuais implantadas na [Solução VMware Azure](../azure-vmware/index.yml).

### <a name="vmware-parallel-backups"></a>Backups paralelos VMware

Com o MABS V3 UR1, todas as suas cópias de segurança VMware VMs dentro de um único grupo de proteção serão paralelas, levando a 25% de backups VM mais rápidos.
Com versões anteriores do MABS, as cópias de segurança paralelas foram realizadas apenas em grupos de proteção. Com mABS V3 UR1, os trabalhos de replicação delta VMware funcionam em paralelo. Por defeito, o número de postos de trabalho a executar em paralelo é fixado para 8. Saiba mais sobre [backups paralelos VMware](backup-azure-backup-server-vmware.md#vmware-parallel-backups).

### <a name="disk-exclusion-for-vmware-vm-backup"></a>Exclusão do disco para backup VMware VM

Com o MABS V3 UR1, pode excluir discos específicos de uma cópia de segurança VMware VM. Saiba mais sobre [a exclusão dos discos da cópia de segurança VMware VM](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup).  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>Suporte para camada adicional de autenticação para eliminar backup on-line

Com o MABS V3 UR1, é adicionada uma camada adicional de autenticação para operações críticas. Será solicitado que introduza um PIN de segurança quando efetuar a Proteção stop com as operações de eliminação de **dados.**

### <a name="offline-backup-improvements"></a>Melhorias de backup offline

O MABS v3 UR1 melhora a experiência de backup offline com o Azure Import/Export Service. Para mais informações, consulte aqui os [passos](./backup-azure-backup-server-import-export.md)atualizados.

>[!NOTE]
>A atualização também traz a pré-visualização de Offline Backup usando Azure Data Box em MABS. Contato [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) para saber mais.

### <a name="new-cmdlet-parameter"></a>Novo parâmetro cmdlet

O MABS V3 UR1 inclui um novo parâmetro **[-CheckReplicaFragmentation]**. O novo parâmetro calcula a percentagem de fragmentação de uma réplica e está incluído no cmdlet **Copy-DPMDatasourceReplica.**

### <a name="32-bit-protection-agent-deprecation"></a>Depreciação do agente de proteção de 32 bits

Com o MABS v3 UR1, o suporte para o agente de proteção de 32 bits já não é suportado. Não poderá proteger cargas de trabalho de 32 bits depois de atualizar o servidor MABS v3 para UR1. Quaisquer agentes de proteção de 32 bits existentes estarão em estado desativado e as cópias de segurança agendadas falharão com o erro do **agente.** Se pretender reter dados de backup para estes agentes, pode parar a proteção com a opção de retenção de dados. Caso contrário, o agente de proteção pode ser removido.

>[!NOTE]
>Reveja a [matriz de proteção atualizada](./backup-mabs-protection-matrix.md) para aprender as cargas de trabalho suportadas para proteção com mABS UR 1.

## <a name="whats-new-in-mabs-v3-rtm"></a>Novidades no MABS V3 RTM

A versão 3 (MABS V3) do Microsoft Azure Backup Server inclui correções críticas de bugs, suporte ao Windows Server 2019, suporte ao SQL 2017 e outras funcionalidades e melhorias. Para visualizar a lista de erros fixados e as instruções de instalação do MABS V3, consulte o artigo KB [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

As seguintes funcionalidades estão incluídas no MABS V3:

### <a name="volume-to-volume-migration"></a>Volume para migração volume

Com o Modern Backup Storage (MBS) em MABS V2, anunciamos o armazenamento de workload aware, onde configura certas cargas de trabalho a serem apoiadas até armazenamento específico, com base em propriedades de armazenamento. No entanto, após a configuração, poderá encontrar a necessidade de mover cópias de segurança de determinadas fontes de dados para outros armazenamentos para utilização otimizada de recursos. O MABS V3 dá-lhe a capacidade de migrar as suas cópias de segurança e configurá-las para serem armazenadas num volume diferente em [três etapas](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

### <a name="prevent-unexpected-data-loss"></a>Prevenir perda de dados inesperados

Nas empresas, o MABS é gerido por uma equipa de administradores. Embora existam diretrizes sobre o armazenamento que devem ser utilizadas para cópias de segurança, um volume incorreto dado ao MABS como armazenamento de backup pode levar à perda de dados críticos. Com o MABS V3, pode evitar tais cenários configurando esses volumes como os que não estão disponíveis para armazenamento usando [estes cmdlets PowerShell](./backup-mabs-add-storage.md).

### <a name="custom-size-allocation"></a>Alocação de tamanho personalizado

O armazenamento de backup moderno (MBS) consome o armazenamento pouco, conforme e quando necessário. Para tal, o MABS calcula o tamanho dos dados que estão a ser apoiados quando são configurados para proteção. No entanto, se muitos ficheiros e pastas estiverem a ser apoiados em conjunto, como no caso de um servidor de ficheiros, o cálculo do tamanho pode demorar muito tempo. Com o MABS V3, pode configurar o MABS para aceitar o tamanho do volume como padrão, em vez de calcular o tamanho de cada ficheiro, o que poupa tempo.

### <a name="optimized-cc-for-rct-vms"></a>CC otimizado para VMs de RCT

O MABS utiliza o RCT (o rastreio de mudanças nativas em Hiper-V), o que diminui a necessidade de verificações de consistência demoradas em cenários à medida que o VM se despenha. O RCT proporciona uma melhor resiliência do que o controlo de alterações fornecido pelas cópias de segurança baseadas em instantâneos do VSS. O MABS V3 otimiza ainda mais o consumo de rede e armazenamento, transferindo apenas os dados alterados durante quaisquer verificações de consistência.

### <a name="support-to-tls-12"></a>Apoio ao TLS 1.2

TLS 1.2 é a forma segura de comunicação sugerida pela Microsoft com encriptação de classe mais alta. O MABS suporta agora a comunicação TLS 1.2 entre o MABS e os servidores protegidos, para autenticação baseada em certificados e para cópias de segurança na nuvem.

### <a name="vmware-vm-protection-support"></a>Suporte de proteção VMware VM

A cópia de segurança VMware VM está agora suportada para implementações de produção. O MABS V3 oferece o seguinte para a proteção VMware VM:

* Suporte para vCenter e ESXi 6.5, juntamente com suporte para 5.5 e 6.0.
* Proteção automática de VMware VMs para cloud. Se forem adicionados novos VMware vMware a uma pasta protegida, são automaticamente protegidos ao disco e à nuvem.
* Melhorias na eficiência da recuperação da vMware para a recuperação de localização alternativa da VMware.

### <a name="sql-2017-support"></a>Suporte SQL 2017

O MABS V3 pode ser instalado com o SQL 2017 como base de dados MABS. Pode atualizar o servidor SQL de SQL 2016 para SQL 2017 ou instalá-lo recentemente. Também pode apoiar a carga de trabalho DO SQL 2017, tanto em ambiente agrupado como não agrupado com MABS V3.

### <a name="windows-server-2019-support"></a>Suporte ao Windows Server 2019

O MABS V3 pode ser instalado no Windows Server 2019. Para utilizar o MABS V3 com o WS2019, pode atualizar o seu SISTEMA para WS2019 antes de instalar/atualizar para o MABS V3 ou pode atualizar o seu SO após a instalação/atualização do V3 no WS2016.

O MABS V3 é um desbloqueio completo e pode ser instalado diretamente no Windows Server 2016, Windows Server 2019 ou pode ser atualizado a partir do MABS V2. Antes de atualizar ou instalar backup Server V3, leia sobre os pré-requisitos de instalação.
Saiba mais informações sobre os passos de instalação/upgrade para MABS [aqui.](./backup-azure-microsoft-azure-backup.md#software-package)

> [!NOTE]
>
> O MABS tem a mesma base de código que o Gestor de Proteção de Dados do Centro de Sistema. MABS v3 é equivalente ao Gestor de Proteção de Dados 1807. MABS v3 UR1 é equivalente ao Gestor de Proteção de Dados 2019 UR1.

## <a name="next-steps"></a>Passos seguintes

Saiba como preparar o seu servidor ou comece a proteger uma carga de trabalho:

* [Preparar cargas de trabalho do servidor de backup](backup-azure-microsoft-azure-backup.md)
* [Utilizar o Backup Server para criar cópias de segurança de um servidor VMware](backup-azure-backup-server-vmware.md)
* [Utilizar o Backup Server para criar cópias de segurança do SQL Server](backup-azure-sql-mabs.md)
* [Use armazenamento de backup moderno com servidor de backup](backup-mabs-add-storage.md)
