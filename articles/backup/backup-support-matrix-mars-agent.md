---
title: Matriz de suporte para cópia de segurança de máquinas que executem o agente dos serviços de recuperação do Azure (MARS) da Microsoft com cópia de segurança do Azure
description: Este artigo resume o suporte de cópia de segurança do Azure, quando cria cópias de segurança de máquinas que executem o agente dos serviços de recuperação do Azure (MARS) da Microsoft.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 3e2c6a550a9358656fd0870c7e785d131c5b6380
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894398"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matriz de suporte para cópia de segurança com o agente dos serviços de recuperação do Azure (MARS) da Microsoft

Pode utilizar o [serviço de cópia de segurança do Azure](backup-overview.md) para fazer uma cópia de segurança de máquinas no local e aplicações e para fazer cópias de segurança de máquinas virtuais do Azure (VMs). Este artigo resume as definições de suporte e limitações ao utilizar o agente dos serviços de recuperação do Azure (MARS) da Microsoft para fazer uma cópia de segurança de máquinas.

## <a name="the-mars-agent"></a>O agente MARS

Cópia de segurança do Azure utiliza o agente MARS para efetuar cópias de segurança de dados de máquinas no local e VMs do Azure para um cofre dos serviços de recuperação de cópia de segurança no Azure. O agente de MARS pode:
- Execute em máquinas do Windows no local para que eles podem criar cópias de segurança diretamente para um cofre de serviços de recuperação de cópia de segurança no Azure.
- Execute em VMs do Windows para que eles podem criar cópias de segurança diretamente para um cofre.
- Execute no servidor de cópia de segurança do Azure (MABS) da Microsoft ou de um servidor do System Center Data Protection Manager (DPM). Neste cenário, máquinas e cargas de trabalho de cópia de segurança para o MABS ou para o servidor do DPM. O agente de MARS, em seguida, efetua cópias de segurança neste servidor para um cofre no Azure. 

As opções de cópia de segurança dependem de onde o agente está instalado. Para obter mais informações, consulte [arquitetura de cópia de segurança do Azure com o agente de MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Para obter informações sobre a arquitetura de cópia de segurança do MABS e o DPM, consulte [cópia de segurança para DPM ou MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Consulte também [requisitos](backup-support-matrix-mabs-dpm.md) para a arquitetura de cópia de segurança.

**Instalação** | **Detalhes**
--- | ---
Transferir o agente de MARS mais recente | Pode baixar a versão mais recente do agente a partir do cofre, ou [transferi-lo diretamente](https://aka.ms/azurebackup_agent).
Instalar diretamente num computador | Pode instalar o agente de MARS diretamente num servidor do Windows no local ou numa VM do Windows que está a executar qualquer um da [sistemas operativos suportados](https://docs.microsoft.com/en-us/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Instale num servidor de cópia de segurança | Quando configurar o DPM ou MABS para criar cópias de segurança para o Azure, transfira e instale o agente de MARS no servidor. Pode instalar o agente num [sistemas operativos suportados](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) na matriz de suporte de cópia de segurança do servidor.

> [!NOTE]
> Por predefinição, as VMs do Azure que estão ativados para cópia de segurança têm uma instalação da extensão de cópia de segurança do Azure. Esta extensão cria cópias de segurança de toda a VM. Pode instalar e executar o agente de MARS numa VM do Azure em conjunto com a extensão, se pretender criar cópias de segurança pastas específicas e ficheiros, em vez da VM completa.
> Quando executa o agente de MARS numa VM do Azure, que faz backup de arquivos ou pastas que estão no armazenamento temporário na VM. Cópias de segurança falham se os ficheiros ou pastas são removidas do armazenamento temporário ou se o armazenamento temporário é removido.


## <a name="cache-folder-support"></a>Suporte de pasta de cache

Quando utiliza o agente MARS para efetuar cópias de segurança de dados, o agente tira um instantâneo dos dados e armazena-os numa pasta de local cache antes de enviar os dados para o Azure. A pasta de cache (zero) tem vários requisitos:

**Cache** | **Detalhes**
--- | ---
Tamanho |  Espaço livre na pasta cache deve ser, pelo menos, 5 a 10 por cento do tamanho total dos seus dados de cópia de segurança. 
Localização | A pasta de cache deve ser armazenada localmente no computador que está a cópia de segurança e tem de estar online. A pasta de cache não deve estar numa partilha de rede, em mídia removível ou num volume offline. 
Pasta | A pasta de cache deve ser encriptada num volume com eliminação de duplicados ou numa pasta que é compactado, esparso ou que tenha um ponto de reanálise.
Alterações de localização | Pode alterar a localização da cache, parando o mecanismo de cópia de segurança (`net stop bengine`) e copiar a pasta de cache para uma nova unidade. (Certifique-se do que novo disco tem espaço suficiente.) Em seguida, atualize duas entradas de registo em **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** e **CloudBackupProvider/Config/ScratchLocation**) para a nova localização e reinicie o motor.

## <a name="networking-and-access-support"></a>Suporte de rede e acesso

### <a name="url-access"></a>Acesso por URL

O agente de MARS tem acesso a estes URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>Suporte de limitação

**Funcionalidade** | **Detalhes**
--- | ---
Controlo de largura de banda | Suportado. O agente de MARS, utilize **alterar propriedades** para ajustar a largura de banda.
Limitação de rede | Não está disponível para máquinas de cópia de segurança que executam o Windows Server 2008 R2, Windows Server 2008 SP2 ou Windows 7.

## <a name="support-for-direct-backups"></a>Suporte para cópias de segurança diretas

Pode utilizar o agente MARS para efetuar cópias de segurança diretamente no Azure em alguns sistemas operativos que são executadas em máquinas no local e VMs do Azure. Os sistemas operativos deve ser de 64 bits e deve ser a executar os pacotes de serviços mais recentes e as atualizações. A tabela seguinte resume estes sistemas operativos:

**Sistema operativo** | **Ficheiros/pastas** | **Estado do sistema**
--- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Sim | Não
Windows 8.1 (Enterprise, Pro)| Sim |Não
Windows 8 (Enterprise, Pro) | Sim | Não
Windows 7 (Ultimate, Enterprise, Pro, Home Premium/básico, Starter) | Sim | Não
Windows Server 2016 (Standard, Datacenter, Essentials) | Sim | Sim
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Sim | Sim
Windows Server 2012 (Standard, Datacenter, Foundation) | Sim | Sim
Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Sim | Sim
Windows Server 2008 SP2 (Standard, Datacenter, Foundation) | Sim | Não
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Sim | Não

Para obter mais informações, consulte [sistemas operativos suportados MABS e DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Limites de cópia de segurança

O Azure Backup limita o tamanho de uma origem de dados de ficheiro ou pasta que pode ser uma cópia de segurança. Os itens que criar cópias de segurança a partir de um único volume não podem exceder os tamanhos resumidos nesta tabela:

**Sistema operativo** | **Limite de tamanho**
--- | ---
Windows Server 2012 ou posterior |  54 400 GB
Windows Server 2008 R2 SP1 |    1,700 GB
Windows Server 2008 SP2 | 1,700 GB
Windows 8 ou posterior  | 54 400 GB
Windows 7   | 1,700 GB


## <a name="supported-file-types-for-backup"></a>Tipos de ficheiro suportados para cópia de segurança

**Tipo** | **Suporte** 
--- | --- 
Encriptados   | Suportado. 
Comprimidos | Suportado.
Dispersos | Suportado.
Comprimidos e dispersos | Suportado.
Ligações fixas  | Não suportado. Ignorado.
Ponto de reanálise   | Não suportado. Ignorado.
Encriptados e dispersos |  Não suportado. Ignorado.
Fluxo comprimido   | Não suportado. Ignorado.
Sequência dispersa   | Não suportado. Ignorado.
OneDrive (ficheiros sincronizados são fluxos esparsos)  | Não suportado. 

## <a name="supported-drives-or-volumes-for-backup"></a>Unidades suportadas ou volumes para cópia de segurança

**Unidade/volume** | **Suporte** | **Detalhes**
--- | --- | ---
Volumes de só de leitura   | Não suportado | Serviço de cópias de sombra de cópia de volumes (VSS) só funciona se o volume é gravável.
Offline volumes | Não suportado |   VSS só funciona se o volume está online.
Partilha de rede   | Não suportado |   O volume tem de ser local no servidor.
Volumes protegidos de BitLocker | Não suportado |   O volume tem de ser desbloqueado antes de inicia a cópia de segurança.
Identificação do sistema de ficheiros  | Não suportado |   NTFS apenas é suportado.
Suporte de dados amovível | Não suportado |   Todas as origens de item de cópia de segurança tem de ter uma *fixo* estado.
Unidades com eliminação de duplicados | Suportadas | Cópia de segurança do Azure converte com eliminação de duplicados de dados para dados normais. Ele otimiza, encripta, armazena e envia os dados para o cofre.

## <a name="support-for-initial-offline-backup"></a>Suporte para cópia de segurança inicial offline

O Azure Backup suporta *offline seeding* para transferir dados de cópia de segurança inicial para o Azure ao utilizar discos. Esse suporte é útil se a cópia de segurança inicial é suscetível de estar no intervalo de tamanho de terabytes (TB). Cópia de segurança offline é suportada para:

- Direta cópia de segurança de ficheiros e pastas em máquinas no local que executem o agente MARS.
- Cópia de segurança de cargas de trabalho e ficheiros a partir de um servidor DPM ou MABS.

Cópia de segurança offline não pode ser utilizada para os ficheiros de estado do sistema.

## <a name="support-for-data-restoration"></a>Suporte para o restauro de dados

Ao utilizar o [restaurar instantâneas](backup-instant-restore-capability.md) funcionalidade de cópia de segurança do Azure, pode restaurar os dados antes que seja copiado para o cofre. A máquina está sendo feito backup tem de executar .NET Framework 4.5.2 ou superior.

Não não possível restaurar cópias de segurança para uma máquina de destino que está a executar uma versão anterior do sistema operativo. Por exemplo, uma cópia de segurança obtida a partir de um computador que está a executar o Windows 7 pode ser restaurada no Windows 8 ou posterior. Mas uma cópia de segurança obtida a partir de um computador que está a executar o Windows 8 não é possível restaurar num computador que está a executar o Windows 7.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [cópia de segurança de arquitetura que utiliza o agente de MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Saiba o que tem suporte quando [execute o agente de MARS num servidor DPM ou MABS](backup-support-matrix-mabs-dpm.md).
