---
title: O que há de novo no Backup do Microsoft Azure Server
description: O Backup do Microsoft Azure Server oferece recursos de backup aprimorados para proteger VMs, arquivos e pastas, cargas de trabalho e muito mais. Saiba como instalar ou atualizar para o Servidor de Backup do Azure v3.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 086856865efba2d1b5e1b8e359ab1e4616aabec9
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091556"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>O que há de novo no Backup do Microsoft Azure Server

O Backup do Microsoft Azure Server versão 3 (MABS v3) é a atualização mais recente e inclui correções críticas de bugs, suporte ao Windows Server 2019, suporte a SQL 2017 e outros recursos e aprimoramentos. Para exibir a lista de bugs corrigidos e as instruções de instalação do MABS v3, consulte o artigo [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)do KB.

Os seguintes recursos estão incluídos no MABS V3:

## <a name="volume-to-volume-migration"></a>Migração de volume para volume

Com o MBS (Armazenamento de Backup Moderno) no MABS v2, anunciamos o armazenamento com reconhecimento de carga de trabalho, no qual você configura determinadas cargas de trabalho para fazer backup em um armazenamento específico, com base nas propriedades de armazenamento. No entanto, após a configuração, talvez você encontre a necessidade de mover backups de determinadas fontes de dados para outro armazenamento para a utilização otimizada de recursos. O MABS v3 oferece a capacidade de migrar seus backups e configurá-los para serem armazenados em um volume diferente em [três etapas](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/).

## <a name="prevent-unexpected-data-loss"></a>Evitar perda de dados inesperada

Nas empresas, o MABS é gerenciado por uma equipe de administradores. Embora haja diretrizes no armazenamento que devem ser usadas para backups, um volume incorreto fornecido ao MABS como armazenamento de backup pode levar à perda de dados críticos. Com o MABS v3, você pode impedir esses cenários Configurando esses volumes como aqueles que não estão disponíveis para armazenamento usando [esses cmdlets do PowerShell](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Alocação de tamanho personalizado

O Armazenamento de Backup Moderno (MBS) consome o armazenamento de forma fina, como e quando necessário. Para fazer isso, o MABS calcula o tamanho dos dados cujo backup está sendo feito quando ele está configurado para proteção. No entanto, se o backup de vários arquivos e pastas for feito juntos, como no caso de um servidor de arquivos, o cálculo de tamanho poderá levar muito tempo. Com o MABS v3, você pode configurar o MABS para aceitar o tamanho do volume como padrão, em vez de calcular o tamanho de cada arquivo, economizando tempo.

## <a name="optimized-cc-for-rct-vms"></a>CC otimizado para VMs RCT

O MABS usa RCT (o controle de alterações nativo no Hyper-V), que diminui a necessidade de verificações de consistência demoradas em cenários à medida que a VM falha. O RCT fornece maior resiliência do que o controle de alterações fornecido pelos backups baseados em instantâneo do VSS. O MABS v3 otimiza ainda mais o consumo de rede e armazenamento Transferindo apenas os dados alterados durante as verificações de consistência.

## <a name="support-to-tls-12"></a>Suporte a TLS 1,2

O TLS 1,2 é a maneira segura de comunicação sugerida pela Microsoft com a melhor criptografia de classe. O MABS agora dá suporte à comunicação TLS 1,2 entre MABS e os servidores protegidos, para autenticação baseada em certificado e para backups na nuvem.

## <a name="vmware-vm-protection-support"></a>Suporte à proteção de VM VMware

O backup de VM VMware agora tem suporte para implantações de produção. O MABS v3 oferece o seguinte para a proteção de VM do VMware:

- Suporte para vCenter e ESXi 6,5, junto com o suporte para 5,5 e 6,0.
- Proteção automática de VMs do VMware para a nuvem. Se novas VMs VMware forem adicionadas a uma pasta protegida, elas serão automaticamente protegidas em disco e nuvem.
- Melhorias de eficiência de recuperação para recuperação de local alternativo do VMware.

## <a name="sql-2017-support"></a>Suporte do SQL 2017

O MABS v3 pode ser instalado com o SQL 2017 como o banco de dados MABS. Você pode atualizar o SQL Server do SQL 2016 para o SQL 2017 ou instalá-lo de novo. Você também pode fazer backup da carga de trabalho do SQL 2017 no ambiente clusterizado e não clusterizado com o MABS v3.

## <a name="windows-server-2019-support"></a>Suporte do Windows Server 2019

O MABS v3 pode ser instalado no Windows Server 2019. Para usar o MABS v3 com o WS2019, você pode atualizar seu sistema operacional para o WS2019 antes de instalar/atualizar para MABS V3 ou pode atualizar o seu sistema operacional após a instalação/atualização v3 no WS2016.

O MABS v3 é uma versão completa e pode ser instalado diretamente no Windows Server 2016, no Windows Server 2019 ou pode ser atualizado do MABS v2. Antes de atualizar para o ou instalar o servidor de backup v3, leia sobre os pré-requisitos de instalação.
Encontre mais informações sobre as etapas de instalação/atualização para MABS [aqui](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).

> [!NOTE]
>
> MABS tem a mesma base de código que o System Center Data Protection Manager. MABS v3 é equivalente a Data Protection Manager 1807.

## <a name="next-steps"></a>Passos seguintes

Saiba como preparar seu servidor ou começar a proteger uma carga de trabalho:

- [Problemas conhecidos no MABS v3](backup-mabs-release-notes-v3.md)
- [Preparar cargas de trabalho do servidor de backup](backup-azure-microsoft-azure-backup.md)
- [Usar o servidor de backup para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Usar o servidor de backup para fazer backup de SQL Server](backup-azure-sql-mabs.md)
- [Usar Armazenamento de Backup Moderno com o servidor de backup](backup-mabs-add-storage.md)
