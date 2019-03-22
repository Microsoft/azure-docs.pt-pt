---
title: O que há de novo no Microsoft Azure Backup Server
description: Servidor do Microsoft Azure Backup dá-lhe recursos avançados de cópia de segurança para proteger as VMs, ficheiros e pastas, cargas de trabalho e muito mais. Saiba como instalar ou atualizar para o Azure Backup Server V3.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 5718064994a80266c216ae6040746be29194adc9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108921"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>O que há de novo no Microsoft Azure Backup Server

Microsoft Azure Backup Server versão 3 (MABS V3) é a atualização mais recente e inclui correções de erros críticas, suporte de 2019 do Windows Server, suporte de 2017 do SQL e outras funcionalidades e melhoramentos. Para ver a lista de erros corrigidos e as instruções de instalação de MABS V3, consulte o artigo BDC [4457852](https://support.microsoft.com/en-us/help/4457852/microsoft-azure-backup-server-v3).

As seguintes funcionalidades estão incluídas na MABS V3:

## <a name="volume-to-volume-migration"></a>Migração de volume de Volume
Com modernas cópia de segurança do armazenamento (MB) no MABS V2, anunciámos o armazenamento com deteção de carga de trabalho, quando configurar determinadas cargas de trabalho a cópia de segurança para armazenamento específico, com base nas propriedades de armazenamento. No entanto, após a configuração, pode achar a necessidade de mover as cópias de segurança de determinadas origens de dados para outro armazenamento de utilização de recursos otimizados. MABS V3 dá-lhe a capacidade de migrar as cópias de segurança e configurá-los para serem armazenados para um volume diferente no [3 passos](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/).

## <a name="prevent-unexpected-data-loss"></a>Evitar a perda de dados inesperado
Das empresas, o MABS é gerida por uma equipa de administradores. Embora haja diretrizes no armazenamento que deve ser utilizado para cópias de segurança, um volume incorreto para o MABS como armazenamento de cópia de segurança pode levar à perda de dados críticos. Com o MABS V3, pode impedir esses cenários de através da configuração desses volumes como aqueles que não estão disponíveis para a utilização de armazenamento [estes cmdlets do PowerShell](https://docs.microsoft.com/system-center/dpm/add-storage#volume-exclusion).

## <a name="custom-size-allocation"></a>Alocação de tamanho personalizado
Armazenamento de cópia de segurança Moderno (MBS) consome armazenamento dinâmico, como e quando for necessário. Para fazer isso, o MABS calcula o tamanho dos dados a cópia de segurança quando está configurado para proteção. No entanto, se o número de ficheiros e pastas são a cópia de segurança em conjunto, como no caso de um servidor de ficheiros, o cálculo do tamanho pode demorar muito tempo. Com o MABS V3, pode configurar MABS para aceitar o tamanho do volume como padrão, em vez de calcular o tamanho de cada arquivo, por conseguinte, economizando tempo.

## <a name="optimized-cc-for-rct-vms"></a>CC otimizado para as VMs do RCT
MABS utiliza RCT (o controlo de alterações nativo no Hyper-V), que diminui a necessidade de consistência morosas verifica em cenários como a VM falha. O RCT proporciona uma melhor resiliência ao registo de alterações fornecida pelas cópias de segurança de baseadas em instantâneos VSS. MABS V3 otimiza ainda mais consumo de rede e armazenamento ao transferir apenas os dados alterados durante qualquer verificação de consistência.

## <a name="support-to-tls-12"></a>Suporte para TLS 1.2
TLS 1.2 é a forma segura de comunicação sugerida pela Microsoft com a encriptação da melhor. MABS suporta agora a comunicação de TLS 1.2 entre MABS e servidores protegidos, para autenticação baseada em certificado e para cópias de segurança na cloud.

## <a name="vmware-vm-protection-support"></a>Suporte para proteção de VM de VMware
Cópia de segurança de VM do VMware é agora suportada para implementações de produção. MABS V3 oferece as seguintes opções para proteção de VMS de VMware:

-   Suporte para o vCenter e ESXi 6.5, juntamente com suporte para o 5.5 e 6.0.
- Proteção automática de VMs de VMware para a cloud. Se as novas VMs VMware são adicionadas a uma pasta protegida, são automaticamente protegidos no disco e na cloud.
- Melhoramentos de eficiência de recuperação para a recuperação de localização alternativa de VMware.

## <a name="sql-2017-support"></a>Suporte de SQL 2017
MABS V3 pode ser instalado com o SQL 2017 como a base de dados do MABS. Pode atualizar o SQL server do SQL 2016 para o SQL 2017 ou instalá-lo recentemente. Pode também fazer cópias de segurança SQL 2017 carga de trabalho tanto no ambiente em cluster e não-agrupado com o MABS V3.

## <a name="windows-server-2019-support"></a>Suporte do Windows Server 2019
MABS V3 pode ser instalado no Windows Server 2019. Para usar MABS V3 com WS2019, pode atualizar seu sistema operacional para WS2019 antes de instalar/atualizar para o MABS V3 ou pode atualizar o seu sistema operacional publicar V3 em utilizar o WS2016 instalar/atualizar.

MABS V3 é uma versão de lançamento e pode ser instalado diretamente no Windows Server 2016, Windows Server 2019 ou podem ser atualizados a partir do MABS V2. Antes de atualizar para ou instalar V3 de servidor de cópia de segurança, leia sobre os pré-requisitos de instalação.
Encontrar mais informações sobre a instalação/atualização os passos para o MABS [aqui](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).


> [!NOTE]
> 
> MABS tem o mesmo código base como o System Center Data Protection Manager. MABS v3 é equivalente a 1807 de Gestor de proteção de dados.

## <a name="next-steps"></a>Passos Seguintes

Saiba como preparar o servidor ou começar a proteger uma carga de trabalho:
- [Problemas conhecidos no MABS V3](backup-mabs-release-notes-v3.md)
- [Preparar as cargas de trabalho do servidor de cópia de segurança](backup-azure-microsoft-azure-backup.md)
- [Utilizar o servidor de cópia de segurança para fazer backup de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Utilizar o servidor de cópia de segurança para criar cópias de segurança do SQL Server](backup-azure-sql-mabs.md)
- [Utilizar o armazenamento de cópia de segurança moderno com o servidor de cópia de segurança](backup-mabs-add-storage.md)
