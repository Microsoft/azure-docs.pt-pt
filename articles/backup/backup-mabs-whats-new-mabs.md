---
title: What's new in Microsoft Azure Backup Server (Novidades no Microsoft Azure Backup Server)
description: O Microsoft Azure Backup Server oferece-lhe capacidades de backup melhoradas para proteger VMs, ficheiros e pastas, cargas de trabalho e muito mais. Saiba como instalar ou atualizar para O Azure Backup Server V3.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 358de6f2986036621accde90550e2afcc47f9135
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84247551"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>What's new in Microsoft Azure Backup Server (Novidades no Microsoft Azure Backup Server)

A versão 3 (MABS V3) do Microsoft Azure Backup Server é a mais recente atualização e inclui correções críticas de bugs, suporte ao Windows Server 2019, suporte ao SQL 2017 e outras funcionalidades e melhorias. Para visualizar a lista de erros fixados e as instruções de instalação do MABS V3, consulte o artigo KB [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

As seguintes funcionalidades estão incluídas no MABS V3:

## <a name="volume-to-volume-migration"></a>Volume para migração volume

Com o Modern Backup Storage (MBS) em MABS V2, anunciamos o armazenamento de workload aware, onde configura certas cargas de trabalho a serem apoiadas até armazenamento específico, com base em propriedades de armazenamento. No entanto, após a configuração, poderá encontrar a necessidade de mover cópias de segurança de determinadas fontes de dados para outros armazenamentos para utilização otimizada de recursos. O MABS V3 dá-lhe a capacidade de migrar as suas cópias de segurança e configurá-las para serem armazenadas num volume diferente em [três etapas](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

## <a name="prevent-unexpected-data-loss"></a>Prevenir perda de dados inesperados

Nas empresas, o MABS é gerido por uma equipa de administradores. Embora existam diretrizes sobre o armazenamento que devem ser utilizadas para cópias de segurança, um volume incorreto dado ao MABS como armazenamento de backup pode levar à perda de dados críticos. Com o MABS V3, pode evitar tais cenários configurando esses volumes como os que não estão disponíveis para armazenamento usando [estes cmdlets PowerShell](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Alocação de tamanho personalizado

O armazenamento de backup moderno (MBS) consome o armazenamento pouco, conforme e quando necessário. Para tal, o MABS calcula o tamanho dos dados que estão a ser apoiados quando são configurados para proteção. No entanto, se muitos ficheiros e pastas estiverem a ser apoiados em conjunto, como no caso de um servidor de ficheiros, o cálculo do tamanho pode demorar muito tempo. Com o MABS V3, pode configurar o MABS para aceitar o tamanho do volume como padrão, em vez de calcular o tamanho de cada ficheiro, o que poupa tempo.

## <a name="optimized-cc-for-rct-vms"></a>CC otimizado para VMs de RCT

O MABS utiliza o RCT (o rastreio de mudanças nativas em Hiper-V), o que diminui a necessidade de verificações de consistência demoradas em cenários à medida que o VM se despenha. O RCT proporciona uma melhor resiliência do que o controlo de alterações fornecido pelas cópias de segurança baseadas em instantâneos do VSS. O MABS V3 otimiza ainda mais o consumo de rede e armazenamento, transferindo apenas os dados alterados durante quaisquer verificações de consistência.

## <a name="support-to-tls-12"></a>Apoio ao TLS 1.2

TLS 1.2 é a forma segura de comunicação sugerida pela Microsoft com encriptação de classe mais alta. O MABS suporta agora a comunicação TLS 1.2 entre o MABS e os servidores protegidos, para autenticação baseada em certificados e para cópias de segurança na nuvem.

## <a name="vmware-vm-protection-support"></a>Suporte de proteção VMware VM

A cópia de segurança VMware VM está agora suportada para implementações de produção. O MABS V3 oferece o seguinte para a proteção VMware VM:

- Suporte para vCenter e ESXi 6.5, juntamente com suporte para 5.5 e 6.0.
- Proteção automática de VMware VMs para cloud. Se forem adicionados novos VMware VM a uma pasta protegida, são automaticamente protegidos ao disco e à nuvem.
- Melhorias na eficiência da recuperação da vMware para a recuperação de localização alternativa da VMware.

## <a name="sql-2017-support"></a>Suporte SQL 2017

O MABS V3 pode ser instalado com o SQL 2017 como base de dados MABS. Pode atualizar o servidor SQL de SQL 2016 para SQL 2017 ou instalá-lo recentemente. Também pode apoiar a carga de trabalho DO SQL 2017, tanto em ambiente agrupado como não agrupado com MABS V3.

## <a name="windows-server-2019-support"></a>Suporte ao Windows Server 2019

O MABS V3 pode ser instalado no Windows Server 2019. Para utilizar o MABS V3 com o WS2019, pode atualizar o seu SISTEMA para WS2019 antes de instalar/atualizar para o MABS V3 ou pode atualizar o seu post DE A/upgrade V3 em WS2016.

O MABS V3 é um desbloqueio completo e pode ser instalado diretamente no Windows Server 2016, Windows Server 2019 ou pode ser atualizado a partir do MABS V2. Antes de atualizar ou instalar backup Server V3, leia sobre os pré-requisitos de instalação.
Saiba mais informações sobre os passos de instalação/upgrade para MABS [aqui.](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package)

> [!NOTE]
>
> O MABS tem a mesma base de código que o Gestor de Proteção de Dados do Centro de Sistema. MABS v3 é equivalente ao Gestor de Proteção de Dados 1807.

## <a name="next-steps"></a>Passos seguintes

Saiba como preparar o seu servidor ou comece a proteger uma carga de trabalho:

- [Questões conhecidas no MABS V3](backup-mabs-release-notes-v3.md)
- [Preparar cargas de trabalho do servidor de backup](backup-azure-microsoft-azure-backup.md)
- [Utilize o Servidor de Backup para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Use o Servidor de Backup para fazer backup no SQL Server](backup-azure-sql-mabs.md)
- [Use armazenamento de backup moderno com servidor de backup](backup-mabs-add-storage.md)
