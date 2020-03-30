---
title: What's new in Microsoft Azure Backup Server (Novidades no Microsoft Azure Backup Server)
description: O Microsoft Azure Backup Server oferece-lhe capacidades de backup melhoradas para proteger VMs, ficheiros e pastas, cargas de trabalho e muito mais. Saiba como instalar ou atualizar para o Servidor de Backup Azure V3.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 61430ce06d3e441fcfe0443eaaf5de3755b04624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582811"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>What's new in Microsoft Azure Backup Server (Novidades no Microsoft Azure Backup Server)

A versão 3 do Microsoft Azure Backup Server (MABS V3) é a mais recente atualização, e inclui correções críticas de bugs, suporte windows Server 2019, suporte SQL 2017 e outras funcionalidades e melhorias. Para visualizar a lista de bugs corrigidos e as instruções de instalação para MABS V3, consulte o artigo [KB 4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

As seguintes características estão incluídas no MABS V3:

## <a name="volume-to-volume-migration"></a>Migração volume-volume

Com o Armazenamento de Backup Moderno (MBS) em MABS V2, anunciamos o armazenamento consciente da Carga de Trabalho, onde configura certas cargas de trabalho para serem apoiadas em armazenamento específico, com base em propriedades de armazenamento. No entanto, após a configuração, poderá encontrar a necessidade de mover cópias de segurança de determinadas fontes de dados para outros armazenamentos para utilização otimizada de recursos. O MABS V3 dá-lhe a capacidade de migrar as suas cópias de segurança e configurá-las para serem armazenadas num volume diferente em [três passos](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

## <a name="prevent-unexpected-data-loss"></a>Prevenir perda inesperada de dados

Nas empresas, o MABS é gerido por uma equipa de administradores. Embora existam diretrizes sobre o armazenamento que devem ser utilizadas para cópias de segurança, um volume incorreto dado ao MABS como armazenamento de cópia de segurança pode levar à perda de dados críticos. Com o MABS V3, pode prevenir tais cenários configurando esses volumes como os que não estão disponíveis para armazenamento utilizando [estes cmdlets PowerShell](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Alocação de tamanho personalizado

O armazenamento de backup moderno (MBS) consome o armazenamento de forma fina, conforme e quando necessário. Para isso, o MABS calcula o tamanho dos dados que estão a ser apoiados quando estão configurados para proteção. No entanto, se muitos ficheiros e pastas estiverem a ser apoiados em conjunto, como no caso de um servidor de ficheiros, o cálculo do tamanho pode demorar muito tempo. Com o MABS V3, pode configurar o MABS para aceitar o tamanho do volume como padrão, em vez de calcular o tamanho de cada ficheiro, poupando assim tempo.

## <a name="optimized-cc-for-rct-vms"></a>CC otimizado para VMs RCT

O MABS usa RCT (o rastreio de mudança nativa em Hyper-V), o que diminui a necessidade de verificações de consistência demoradas nos cenários à medida que o VM se despenha. O RCT proporciona uma melhor resiliência do que o controlo de alterações fornecido pelas cópias de segurança baseadas em instantâneos do VSS. O MABS V3 otimiza ainda mais o consumo de rede e armazenamento, transferindo apenas os dados alterados durante quaisquer verificações de consistência.

## <a name="support-to-tls-12"></a>Suporte a TLS 1.2

TLS 1.2 é a forma segura de comunicação sugerida pela Microsoft com encriptação de classe de melhor em classe. O MABS suporta agora a comunicação TLS 1.2 entre o MABS e os servidores protegidos, para autenticação baseada em certificados e para backups em nuvem.

## <a name="vmware-vm-protection-support"></a>Suporte de proteção VMware VM

A cópia de segurança VMware VM é agora suportada para implementações de produção. O MABS V3 oferece o seguinte para a proteção VMware VM:

- Suporte para vCenter e ESXi 6.5, juntamente com suporte para 5.5 e 6.0.
- Auto-protecção dos VMware VMs para a nuvem. Se os novos VMware forem adicionados a uma pasta protegida, são automaticamente protegidos para o disco e a nuvem.
- Melhorias na eficiência de recuperação para a recuperação alternativa de localização da VMware.

## <a name="sql-2017-support"></a>Suporte SQL 2017

O MABS V3 pode ser instalado com o SQL 2017 como base de dados MABS. Pode atualizar o servidor SQL de SQL 2016 para SQL 2017 ou instalá-lo recentemente. Também pode apoiar a carga de trabalho SQL 2017 tanto em ambiente agrupado como não agrupado com MABS V3.

## <a name="windows-server-2019-support"></a>Suporte windows Server 2019

O MABS V3 pode ser instalado no Windows Server 2019. Para utilizar o MABS V3 com o WS2019, pode atualizar o seu OS para WS2019 antes de instalar/atualizar para MABS V3 ou pode atualizar o seu post os de instalação/atualização v3 no WS2016.

O MABS V3 é um lançamento completo e pode ser instalado diretamente no Windows Server 2016, Windows Server 2019, ou pode ser atualizado a partir de MABS V2. Antes de atualizar ou instalar o Backup Server V3, leia sobre os pré-requisitos de instalação.
Encontre mais informações sobre os passos de instalação/atualização para MABS [aqui](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).

> [!NOTE]
>
> O MABS tem a mesma base de código que o System Center Data Protection Manager. O MABS v3 é equivalente ao Gestor de Proteção de Dados 1807.

## <a name="next-steps"></a>Passos seguintes

Aprenda a preparar o seu servidor ou comece a proteger uma carga de trabalho:

- [Questões conhecidas no MABS V3](backup-mabs-release-notes-v3.md)
- [Preparar cargas de trabalho do Servidor de Backup](backup-azure-microsoft-azure-backup.md)
- [Utilize o Servidor de Backup para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Utilize o Servidor de Backup para fazer backup do Servidor SQL](backup-azure-sql-mabs.md)
- [Utilize o armazenamento de backup moderno com o servidor de backup](backup-mabs-add-storage.md)
