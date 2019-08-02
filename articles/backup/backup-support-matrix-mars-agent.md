---
title: Matriz de suporte para backup de computadores que executam o agente de Serviços de Recuperação do Microsoft Azure (MARS) com o backup do Azure
description: Este artigo resume o suporte ao backup do Azure ao fazer backup de computadores que executam o agente de Serviços de Recuperação do Microsoft Azure (MARS).
author: dcurwin
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: dacurwin
manager: carmonm
ms.openlocfilehash: 46863b2eed8e5c5c4074a713b50739820a1e5bc9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639528"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matriz de suporte para backup com o agente de Serviços de Recuperação do Microsoft Azure (MARS)

Você pode usar o [serviço de backup do Azure](backup-overview.md) para fazer backup de computadores e aplicativos locais e fazer backup de VMs (máquinas virtuais) do Azure. Este artigo resume as configurações de suporte e as limitações quando você usa o agente de Serviços de Recuperação do Microsoft Azure (MARS) para fazer backup de computadores.

## <a name="the-mars-agent"></a>O agente MARS

O backup do Azure usa o agente MARS para fazer backup de dados de máquinas locais e VMs do Azure para um cofre de serviços de recuperação de backup no Azure. O agente MARS pode:
- Execute em computadores Windows locais para que eles possam fazer backup diretamente em um cofre de serviços de recuperação de backup no Azure.
- Execute em VMs do Windows para que eles possam fazer backup diretamente em um cofre.
- Execute no Backup do Microsoft Azure Server (MABS) ou um servidor do System Center Data Protection Manager (DPM). Nesse cenário, as máquinas e as cargas de trabalho fazem backup em MABS ou no servidor DPM. O agente MARS faz o backup desse servidor para um cofre no Azure.

Suas opções de backup dependem de onde o agente está instalado. Para obter mais informações, consulte [arquitetura de backup do Azure usando o agente Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Para obter informações sobre a arquitetura de backup do MABS e do DPM, consulte [fazer backup para o DPM ou mAbs](backup-architecture.md#architecture-back-up-to-dpmmabs). Consulte também [os requisitos](backup-support-matrix-mabs-dpm.md) para a arquitetura de backup.

**Instalação** | **Detalhes**
--- | ---
Baixe o agente MARS mais recente | Você pode baixar a versão mais recente do agente do cofre ou [baixá-la diretamente](https://aka.ms/azurebackup_agent).
Instalar diretamente em um computador | Você pode instalar o agente MARS diretamente em um Windows Server local ou em uma VM do Windows que esteja executando qualquer um dos [sistemas operacionais com suporte](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Instalar em um servidor de backup | Ao configurar o DPM ou o MABS para fazer backup no Azure, você baixa e instala o agente MARS no servidor. Você pode instalar o agente em [sistemas operacionais com suporte](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) na matriz de suporte do servidor de backup.

> [!NOTE]
> Por padrão, as VMs do Azure que estão habilitadas para backup têm uma instalação de extensão de backup do Azure. Essa extensão faz o backup de toda a VM. Você pode instalar e executar o agente MARS em uma VM do Azure junto com a extensão se quiser fazer backup de pastas e arquivos específicos, em vez da VM completa.
> Quando você executa o agente MARS em uma VM do Azure, ele faz backup de arquivos ou pastas que estão no armazenamento temporário na VM. Os backups falharão se os arquivos ou pastas forem removidos do armazenamento temporário ou se o armazenamento temporário for removido.


## <a name="cache-folder-support"></a>Suporte à pasta de cache

Quando você usa o agente MARS para fazer backup de dados, o agente tira um instantâneo dos dados e os armazena em uma pasta de cache local antes de enviar os dados para o Azure. A pasta cache (Scratch) tem vários requisitos:

**Cache** | **Detalhes**
--- | ---
Size |  O espaço livre na pasta de cache deve ter pelo menos 5 a 10% do tamanho geral dos dados de backup.
Location | A pasta de cache deve ser armazenada localmente no computador que está sendo submetido a backup e deve estar online. A pasta de cache não deve estar em um compartilhamento de rede, em mídia removível ou em um volume offline.
Pasta | A pasta de cache deve ser criptografada em um volume com eliminação de duplicação ou em uma pasta compactada, que seja esparsa ou que tenha um ponto de nova análise.
Alterações de local | Você pode alterar o local do cache interrompendo o mecanismo`net stop bengine`de backup () e copiando a pasta de cache para uma nova unidade. (Verifique se a nova unidade tem espaço suficiente.) Em seguida, atualize duas entradas de registro em **HKLM\Software\Microsoft\Windows Azure backup** (**config/ScratchLocation** e **config/CloudBackupProvider/ScratchLocation**) para o novo local e reinicie o mecanismo.

## <a name="networking-and-access-support"></a>Suporte de rede e acesso

### <a name="url-access"></a>Acesso à URL

O agente MARS precisa de acesso a essas URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>Suporte à limitação

**Funcionalidade** | **Detalhes**
--- | ---
Controle de largura de banda | Suportado. No agente MARS, use **alterar propriedades** para ajustar a largura de banda.
Limitação de rede | Não disponível para computadores com backup que executam o Windows Server 2008 R2, o Windows Server 2008 SP2 ou o Windows 7.

## <a name="support-for-direct-backups"></a>Suporte para backups diretos

Você pode usar o agente MARS para fazer backup diretamente no Azure em alguns sistemas operacionais que são executados em máquinas locais e VMs do Azure. Os sistemas operacionais devem ser de 64 bits e devem estar executando os serviços e atualizações mais recentes. A tabela a seguir resume estes sistemas operacionais:

**Sistema operativo** | **Arquivos/pastas** | **Estado do sistema**
--- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Sim | Não
Windows 8.1 (Enterprise, Pro)| Sim |Não
Windows 8 (Enterprise, Pro) | Sim | Não
Windows 7 (Ultimate, Enterprise, pro, Home Premium/Basic, Starter) | Sim | Não
Windows Server 2016 (Standard, Datacenter, Essentials) | Sim | Sim
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Sim | Sim
Windows Server 2012 (Standard, Datacenter, Foundation) | Sim | Sim
Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Sim | Sim
Windows Server 2008 SP2 (Standard, Datacenter, Foundation) | Sim | Não
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Sim | Não

Para obter mais informações, consulte [supported mAbs and DPM Operating Systems](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Limites de backup

O backup do Azure limita o tamanho de uma fonte de dados de arquivo ou pasta cujo backup pode ser feito. Os itens que você faz backup de um único volume não podem exceder os tamanhos resumidos nesta tabela:

**Sistema operativo** | **Limite de tamanho**
--- | ---
Windows Server 2012 ou posterior |  54 400 GB
Windows Server 2008 R2 SP1 |    1\.700 GB
Windows Server 2008 SP2 | 1\.700 GB
Windows 8 ou posterior  | 54 400 GB
Windows 7   | 1\.700 GB


## <a name="supported-file-types-for-backup"></a>Tipos de arquivo com suporte para backup

**Tipo** | **Suporte**
--- | ---
Encriptado   | Suportado.
Comprimidos | Suportado.
Dispersos | Suportado.
Compactados e esparsos | Suportado.
Ligações fixas  | Não suportado. Ignorada.
Ponto de nova análise   | Não suportado. Ignorada.
Criptografado e esparso |  Não suportado. Ignorada.
Fluxo compactado   | Não suportado. Ignorada.
Fluxo esparso   | Não suportado. Ignorada.
OneDrive (arquivos sincronizados são fluxos esparsos)  | Não suportado.

## <a name="supported-drives-or-volumes-for-backup"></a>Unidades ou volumes com suporte para backup

**Unidade/volume** | **Suporte** | **Detalhes**
--- | --- | ---
Volumes somente leitura   | Não suportado | O VSS (serviço de cópias de sombra de volume) só funcionará se o volume for gravável.
Volumes offline | Não suportado |   O VSS só funcionará se o volume estiver online.
Compartilhamento de rede   | Não suportado |   O volume deve ser local no servidor.
Volumes protegidos pelo BitLocker | Não suportado |   O volume deve ser desbloqueado antes do início do backup.
Identificação do sistema de arquivos  | Não suportado |   Há suporte apenas para NTFS.
Mídia removível | Não suportado |   Todas as fontes de itens de backup devem ter um status *fixo* .
Unidades com eliminação de duplicação | Suportadas | O backup do Azure converte dados com eliminação de duplicação em dados normais. Ele otimiza, criptografa, armazena e envia os dados para o cofre.

## <a name="support-for-initial-offline-backup"></a>Suporte para backup offline inicial

O backup do Azure dá suporte à *propagação offline* para transferir dados de backup iniciais para o Azure usando discos. Esse suporte será útil se o backup inicial provavelmente estiver na faixa de tamanho de terabytes (TBs). O backup offline tem suporte para:

- Backup direto de arquivos e pastas em computadores locais que estão executando o agente MARS.
- Backup de cargas de trabalho e arquivos de um servidor DPM ou MABS.

O backup offline não pode ser usado para arquivos de estado do sistema.

## <a name="support-for-data-restoration"></a>Suporte para restauração de dados

Usando o recurso de [restauração instantânea](backup-instant-restore-capability.md) do backup do Azure, você pode restaurar os dados antes que eles sejam copiados para o cofre. O computador no qual você está fazendo backup deve estar em execução .NET Framework 4.5.2 ou superior.

Os backups não podem ser restaurados em um computador de destino que esteja executando uma versão anterior do sistema operacional. Por exemplo, um backup feito de um computador que executa o Windows 7 pode ser restaurado no Windows 8 ou posterior. Mas um backup feito de um computador que executa o Windows 8 não pode ser restaurado em um computador que esteja executando o Windows 7.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [a arquitetura de backup que usa o agente Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Saiba o que tem suporte quando você [executa o agente Mars no mAbs ou em um servidor DPM](backup-support-matrix-mabs-dpm.md).
