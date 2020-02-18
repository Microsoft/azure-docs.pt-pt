---
title: Matriz de suporte para o agente MARS
description: Este artigo resume o suporte ao backup do Azure ao fazer backup de computadores que executam o agente de Serviços de Recuperação do Microsoft Azure (MARS).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 8f5ce33b5057b11caa33c0ae80cf72e1b13da5d0
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425022"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matriz de suporte para backup com o agente de Serviços de Recuperação do Microsoft Azure (MARS)

Pode utilizar o [serviço de backup Azure](backup-overview.md) para fazer backup de máquinas e aplicações no local e para fazer backup das máquinas virtuais Azure (VMs). Este artigo resume as configurações de suporte e as limitações quando você usa o agente de Serviços de Recuperação do Microsoft Azure (MARS) para fazer backup de computadores.

## <a name="the-mars-agent"></a>O agente MARS

O backup do Azure usa o agente MARS para fazer backup de dados de máquinas locais e VMs do Azure para um cofre de serviços de recuperação de backup no Azure. O agente MARS pode:

- Execute em computadores Windows locais para que eles possam fazer backup diretamente em um cofre de serviços de recuperação de backup no Azure.
- Execute em VMs do Windows para que eles possam fazer backup diretamente em um cofre.
- Execute no Backup do Microsoft Azure Server (MABS) ou um servidor do System Center Data Protection Manager (DPM). Nesse cenário, as máquinas e as cargas de trabalho fazem backup em MABS ou no servidor DPM. O agente MARS faz o backup desse servidor para um cofre no Azure.

> [!NOTE]
>O backup do Azure não dá suporte ao ajuste automático do relógio para horário de Verão (DST). Modifique a política para garantir que a economia de horário de verão seja levada em conta para evitar discrepâncias entre a hora real e o horário de backup agendado.

Suas opções de backup dependem de onde o agente está instalado. Para mais informações, consulte a [arquitetura Azure Backup utilizando o agente MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Para obter informações sobre a arquitetura de backup MABS e DPM, consulte [Back up to DPM ou MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Consulte também [os requisitos](backup-support-matrix-mabs-dpm.md) para a arquitetura de backup.

**Instalação** | **Detalhes**
--- | ---
Baixe o agente MARS mais recente | Pode descarregar a versão mais recente do agente a partir do cofre, ou [descarregá-la diretamente](https://aka.ms/azurebackup_agent).
Instalar diretamente em um computador | Pode instalar o agente MARS diretamente num servidor Windows no local ou num VM Windows que esteja a executar qualquer um dos [sistemas operativos suportados](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Instalar em um servidor de backup | Ao configurar o DPM ou o MABS para fazer backup no Azure, você baixa e instala o agente MARS no servidor. Pode instalar o agente em [sistemas operativos suportados](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) na matriz de suporte do servidor de backup.

> [!NOTE]
> Por padrão, as VMs do Azure que estão habilitadas para backup têm uma instalação de extensão de backup do Azure. Esta extensão apoia todo o VM. Você pode instalar e executar o agente MARS em uma VM do Azure junto com a extensão se quiser fazer backup de pastas e arquivos específicos, em vez da VM completa.
> Quando você executa o agente MARS em uma VM do Azure, ele faz backup de arquivos ou pastas que estão no armazenamento temporário na VM. Os backups falharão se os arquivos ou pastas forem removidos do armazenamento temporário ou se o armazenamento temporário for removido.

## <a name="cache-folder-support"></a>Suporte à pasta de cache

Quando você usa o agente MARS para fazer backup de dados, o agente tira um instantâneo dos dados e os armazena em uma pasta de cache local antes de enviar os dados para o Azure. A pasta cache (Scratch) tem vários requisitos:

**Cache** | **Detalhes**
--- | ---
Tamanho |  O espaço livre na pasta de cache deve ter pelo menos 5 a 10% do tamanho geral dos dados de backup.
Localização | A pasta de cache deve ser armazenada localmente no computador que está sendo submetido a backup e deve estar online. A pasta de cache não deve estar em um compartilhamento de rede, em mídia removível ou em um volume offline.
Pasta | A pasta de cache não deve ser criptografada em um volume com eliminação de duplicação ou em uma pasta compactada, que seja esparsa ou que tenha um ponto de nova análise.
Alterações de local | Pode alterar a localização da cache parando o motor de reserva (`net stop bengine`) e copiando a pasta cache para uma nova unidade. (Certifique-se de que a nova unidade tem espaço suficiente.) Em seguida, atualize duas entradas de registo em **HKLM\SOFTWARE\Microsoft\Microsoft\Windows Azure Backup** **(Config/ScratchLocation** e **Config/CloudBackupProvider/ScratchLocation**) para a nova localização e reinicie o motor.

## <a name="networking-and-access-support"></a>Suporte de rede e acesso

### <a name="url-and-ip-access"></a>URL e acesso a IP

O agente MARS precisa de acesso a essas URLs:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

E para estes endereços IP:

- 20.190.128.0/18
- 40.126.0.0/18

O acesso a todas as URLs e endereços IP listados acima usa o protocolo HTTPS na porta 443.

### <a name="throttling-support"></a>Suporte à limitação

**Funcionalidade** | **Detalhes**
--- | ---
Controle de largura de banda | Suportado. No agente MARS, utilize a **Change Properties** para ajustar a largura de banda.
Estrangulamento da rede | Não disponível para computadores com backup que executam o Windows Server 2008 R2, o Windows Server 2008 SP2 ou o Windows 7.

## <a name="support-for-direct-backups"></a>Suporte para backups diretos

>[!NOTE]
> O agente MARS não dá suporte a SKUs do Windows Server Core.

Você pode usar o agente MARS para fazer backup diretamente no Azure nos sistemas operacionais listados abaixo que são executados em:

1. Servidores locais do Windows
2. VMs do Azure executando o Windows

Os sistemas operacionais devem ser de 64 bits e devem estar executando os serviços e atualizações mais recentes. A tabela a seguir resume estes sistemas operacionais:

**Sistema operativo** | **Ficheiros/pastas** | **Estado do sistema** | **Requisitos de software/módulo**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Sim | Não |  Verificar a versão do servidor correspondente para os requisitos de software/módulo
Windows 8.1 (Enterprise, Pro)| Sim |Não | Verificar a versão do servidor correspondente para os requisitos de software/módulo
Windows 8 (Enterprise, Pro) | Sim | Não | Verificar a versão do servidor correspondente para os requisitos de software/módulo
Windows 7 (Ultimate, Enterprise, pro, Home Premium/Basic, Starter) | Sim | Não | Verificar a versão do servidor correspondente para os requisitos de software/módulo
Windows Server 2016 (Standard, Datacenter, Essentials) | Sim | Sim | -.NET 4,5 <br> -Windows PowerShell <br> -Redistribuível do Microsoft VC + + compatível mais recente <br> -MMC (console de gerenciamento Microsoft) 3,0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Sim | Sim | -.NET 4,5 <br> -Windows PowerShell <br> -Redistribuível do Microsoft VC + + compatível mais recente <br> -MMC (console de gerenciamento Microsoft) 3,0
Windows Server 2012 (Standard, Datacenter, Foundation) | Sim | Sim |-.NET 4,5 <br> -Windows PowerShell <br> -Redistribuível do Microsoft VC + + compatível mais recente <br> -MMC (console de gerenciamento Microsoft) 3,0 <br> -Gerenciamento e manutenção de imagens de implantação (DISM. exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Sim | Não | -.NET 4,5 <br> -Windows PowerShell <br> -Redistribuível do Microsoft VC + + compatível mais recente <br> -MMC (console de gerenciamento Microsoft) 3,0
Windows Server 2019 (Standard, Datacenter, Essentials) | Sim | Sim | -.NET 4,5 <br> -Windows PowerShell <br> -Redistribuível do Microsoft VC + + compatível mais recente <br> -MMC (console de gerenciamento Microsoft) 3,0

Para mais informações, consulte [os sistemas operativos MABS suportados e DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Limites de backup

### <a name="size-limits"></a>Limites de tamanho

O backup do Azure limita o tamanho de uma fonte de dados de arquivo ou pasta cujo backup pode ser feito. Os itens que você faz backup de um único volume não podem exceder os tamanhos resumidos nesta tabela:

**Sistema operativo** | **Limite de tamanho**
--- | ---
Windows Server 2012 ou posterior |54 400 GB
Windows Server 2008 R2 SP1 |1\.700 GB
Windows Server 2008 SP2| 1\.700 GB
Windows 8 ou posterior| 54 400 GB
Windows 7| 1\.700 GB

### <a name="other-limitations"></a>Outras limitações

- O MARS não oferece suporte à proteção de vários computadores com o mesmo nome para um único cofre.

## <a name="supported-file-types-for-backup"></a>Tipos de arquivo com suporte para backup

**Tipo** | **Suporte**
--- | ---
Encriptados| Suportado.
Comprimidos | Suportado.
Dispersos | Suportado.
Compactados e esparsos |Suportado.
Ligações fixas| Não suportado. Ignorada.
Ponto reparse| Não suportado. Ignorada.
Criptografado e esparso |Não suportado. Ignorada.
Sequência comprimida| Não suportado. Ignorada.
Sequência dispersa| Não suportado. Ignorada.
OneDrive (arquivos sincronizados são fluxos esparsos)| Não suportado.
Pastas com replicação DFS ativadas | Não suportado.

## <a name="supported-drives-or-volumes-for-backup"></a>Unidades ou volumes com suporte para backup

**Unidade/volume** | **Suporte** | **Detalhes**
--- | --- | ---
Volumes somente leitura| Não suportado | O VSS (serviço de cópias de sombra de volume) só funcionará se o volume for gravável.
Volumes offline| Não suportado |O VSS só funcionará se o volume estiver online.
Compartilhamento de rede| Não suportado |O volume deve ser local no servidor.
Volumes bloqueados pelo BitLocker| Não suportado |O volume deve ser desbloqueado antes do início do backup.
Identificação do sistema de arquivos| Não suportado |Há suporte apenas para NTFS.
Meios amovíveis| Não suportado |Todas as fontes de artigo de reserva devem ter um estado *fixo.*
Unidades com eliminação de duplicação | Suportado | O backup do Azure converte dados com eliminação de duplicação em dados normais. Ele otimiza, criptografa, armazena e envia os dados para o cofre.

## <a name="support-for-initial-offline-backup"></a>Suporte para backup offline inicial

O Azure Backup suporta *a sementeira offline* para transferir dados iniciais de backup para o Azure utilizando discos. Esse suporte será útil se o backup inicial provavelmente estiver na faixa de tamanho de terabytes (TBs). O backup offline tem suporte para:

- Backup direto de arquivos e pastas em computadores locais que estão executando o agente MARS.
- Backup de cargas de trabalho e arquivos de um servidor DPM ou MABS.

O backup offline não pode ser usado para arquivos de estado do sistema.

## <a name="support-for-data-restoration"></a>Suporte para restauração de dados

Ao utilizar a funcionalidade [De restauro instantâneo](backup-instant-restore-capability.md) da Cópia de Segurança Azure, pode restaurar os dados antes de ser copiado para o cofre. O computador no qual você está fazendo backup deve estar em execução .NET Framework 4.5.2 ou superior.

Os backups não podem ser restaurados em um computador de destino que esteja executando uma versão anterior do sistema operacional. Por exemplo, um backup feito de um computador que executa o Windows 7 pode ser restaurado no Windows 8 ou posterior. Mas um backup feito de um computador que executa o Windows 8 não pode ser restaurado em um computador que esteja executando o Windows 7.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a arquitetura de backup que utiliza o agente MARS.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
- Saiba o que é suportado quando [executa o agente MARS no MABS ou num servidor DPM](backup-support-matrix-mabs-dpm.md).
