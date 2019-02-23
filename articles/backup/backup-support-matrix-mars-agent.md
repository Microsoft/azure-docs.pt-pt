---
title: Matriz de suporte para cópia de segurança de máquinas que executem o agente dos serviços de recuperação do Azure (MARS) da Microsoft com cópia de segurança do Azure
description: Este artigo resume o suporte de cópia de segurança do Azure, quando cria cópias de segurança de máquinas que executa o agente dos serviços de recuperação do Azure (MARS) da Microsoft.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: b579816a4367537a7da4aa0f68c8776465295448
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670758"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matriz de suporte para cópia de segurança com o agente dos serviços de recuperação do Azure (MARS) da Microsoft

Pode utilizar o [serviço de cópia de segurança do Azure](backup-overview.md) para fazer uma cópia de segurança de máquinas no local e aplicações e as VMs do Azure. Este artigo resume as definições de suporte e limitações das cópias de máquinas com o agente dos serviços de recuperação do Azure (MARS) da Microsoft.

## <a name="about-the-mars-agent"></a>Sobre o agente MARS

O agente de MARS é utilizado pela cópia de segurança do Azure para o Cofre de cópia de segurança dados de máquinas no local e VMs do Azure para uma cópia de segurança dos serviços de recuperação no Azure. O agente de MARS pode ser usado da seguinte forma:
- Execute o agente em máquinas do Windows no local para que eles podem criar cópias de segurança diretamente para um cofre de serviços de recuperação de cópia de segurança no Azure.
- Execute o agente em VMs do Windows Azure, para que ele pode criar cópias de segurança diretamente para um cofre.
- Execute o agente num Microsoft Azure Backup Server (MABS) ou uma proteção de dados no System Center - server Manager (DPM). Neste cenário, as máquinas e cargas de trabalho de cópia de segurança para o MABS/DPM e, em seguida, MABS/DPM conta com cópia de segurança para um cofre no Azure com o agente MARS. 

O que pode criar cópias de segurança depende de onde o agente está instalado.

- [Saiba mais](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) sobre arquitetura de cópia de segurança com o agente MARS.
- [Saiba mais]() sobre o MABS/DPM [arquitetura de cópia de segurança](backup-architecture.md#architecture-back-up-to-dpmmabs)e [requisitos](backup-support-matrix-mabs-dpm.md).


## <a name="supported-installation"></a>Instalação suportada

**Instalar** | **Detalhes**
--- | ---
**Transferir o agente de MARS mais recente** | Pode baixar a versão mais recente do agente a partir do cofre, ou [transferi-lo diretamente](https://aka.ms/azurebackup_agent).
**Instalar diretamente num computador** | Pode instalar o agente de MARS diretamente num servidor do Windows no local ou VM do Windows Azure com qualquer uma da [sistemas operativos suportados]().
**Instale num servidor de cópia de segurança** | Quando configurar o DPM ou MABS para criar cópias de segurança para o Azure, transfira e instale o agente de MARS no servidor. O agente pode ser instalado em conformidade com o [sistemas operativos suportados](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) na matriz de suporte de cópia de segurança do servidor.

> [!NOTE]
> Por padrão VMs do Azure ativado para a cópia de segurança têm uma instalação da extensão de cópia de segurança do Azure. Esta extensão cria cópias de segurança de toda a VM. Pode instalar e executar o agente de MARS numa VM do Azure em conjunto com a extensão, se pretender criar cópias de segurança pastas específicas e ficheiros, em vez da VM completa.
> Quando executa o agente de MARS numa VM do Azure, que faz backup de ficheiros/pastas localizadas no armazenamento temporário na VM. As cópias de segurança irão falhar se os ficheiros/pastas são removidas do armazenamento temporário, ou se o armazenamento temporário é removido.


## <a name="cache-folder-support"></a>Suporte de pasta de cache

Quando criar cópias de segurança com o agente MARS, o agente tira um instantâneo dos dados e armazena-os numa pasta de local cache antes de os enviar para o Azure. A pasta de cache (zero) tem um número de requisitos.

**Cache** | **Detalhes**
--- | ---
**Tamanho da cache** |  Espaço livre no tamanho da pasta cache deve ser, pelo menos, 5 a 10% do tamanho total dos seus dados de cópia de segurança. 
**Localização da cache** | A pasta da cache tem de ser local para a máquina a cópia de segurança e tem de estar online.<br/><br/> A pasta de cache não deve estar localizada numa partilha de rede, em mídia removível ou num volume offline. 
**Pasta de cache** | A pasta de cache deve ser encriptada, num volume com eliminação de duplicados, ou numa pasta que é compactado/dispersas /-ponto de reanálise
**Modificar a localização da cache** | Pode alterar a localização da cache, a parar o mecanismo de cópia de segurança (net stop bengine) e copiar a pasta de cache para uma nova unidade (Certifique-se de que tem espaço suficiente). Em seguida, atualizar duas entradas do Registro em HKLM\SOFTWARE\Microsoft\Windows Azure Backup (configuração/ScratchLocation e configuração/CloudBackupProvider/ScratchLocation) para a nova localização e reinicie o motor de.

## <a name="networking-and-access-support"></a>Suporte de rede e acesso

### <a name="url-access"></a>Acesso por URL

O agente de MARS tem acesso a estes URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- \*.windows.net

### <a name="throttling-support"></a>Suporte de limitação

**Funcionalidade** | **Detalhes**
--- | ---
Controlo de largura de banda | Suportadas<br/><br/> Uso **alterar propriedades** no agente de MARS para ajustar a largura de banda.
Limitação de rede | Não está disponível para a cópia de segurança de máquinas que executem o Windows Server 2008 R2, Windows Server 2008 SP2 ou Windows 7.

## <a name="support-for-direct-backups"></a>Suporte para cópias de segurança diretas

A tabela seguinte resume quais sistemas operacionais em execução em máquinas no local e VMs do Azure podem ser copiados diretamente para o Azure com o agente MARS.

- Todos os sistemas operativos são 64 bits.
- Todos os sistemas operativos devem estar a executar os pacotes de serviços mais recentes e as atualizações.
- Para obter detalhes sobre os quais o DPM e o MABS servidores podem ser uma cópia de segurança com o agente MARS, reveja [esta tabela](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

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
Windows Storage Server 2016/2012 R2/2012(Standard, Workgroup | Sim | Não


## <a name="backup-limits"></a>Limites de cópia de segurança

Cópia de segurança do Azure coloca um limite no tamanho de uma origem de dados de ficheiro/pasta que pode ser uma cópia de segurança. O tamanho dos itens selecionados para cópia de segurança de um único volume não pode exceder os tamanhos resumidos na tabela.

**Sistema operativo** | **Limite de tamanho**
--- | ---
Windows Server 2012 ou posterior |  54 400 GB
Windows Server 2008 R2 SP1 |    1700 GB
Windows Server 2008 SP2 | 1700 GB
Windows 8 ou posterior  | 54 400 GB
Windows 7   | 1700 GB


## <a name="supported-file-types-for-backup"></a>Tipos de ficheiro suportados para cópia de segurança

**Tipo** | **Suportado** 
--- | --- 
Encriptados   | Sim 
Comprimidos | Sim
Dispersos | Sim 
Comprimidos e dispersos | Sim
Ligações fixas  | Não suportado<br/><br/> Ignorada
Ponto de reanálise   | Não suportado<br/><br/> Ignorada
Encriptados e dispersos |  Não suportado<br/><br/> Ignorada
Fluxo comprimido   | Não suportado<br/><br/> Ignorada
Sequência dispersa   | Não suportado<br/><br/> Ignorada
Uma unidade (ficheiros sincronizados são fluxos esparsos)    | Não suportado 

## <a name="supported-drivesvolumes-for-backup"></a>Suporte de unidades/volumes para cópia de segurança

**Unidade/volume** | **Suportado** | **Detalhes**
--- | --- | ---
Volumes de só de leitura   | Não suportado | O volume tem de ser gravável para o VSS funcione.
Offline volumes | Não suportado |   O volume tem de estar online para que o VSS funcione.
Partilha de rede   | Não suportado |   O volume tem de ser local no servidor para cópia de segurança.
Volumes protegidos de BitLocker | Não suportado |   O volume tem de ser desbloqueado antes para cópia de segurança trabalhar.
Identificação do sistema de ficheiros  | Não suportado |   Apenas NTFS.
Suporte de dados amovível | Não suportado |   Todas as origens de item de cópia de segurança tem de ter estado fixo.
Unidades com eliminação de duplicados | Suportado.<br/><br/> Cópia de segurança do Azure converte com eliminação de duplicados de dados para dados normais. Ele o otimiza os dados, encripta-lo, armazena e envia-os para o cofre.

## <a name="support-for-initial-offline-backup"></a>Suporte para cópia de segurança inicial offline

O Azure Backup suporta a "propagação offline" para transferir dados de cópia de segurança iniciais para o Azure com discos. Isso é útil se a cópia de segurança inicial é suscetível de estar no intervalo de terabytes (TB). Cópia de segurança offline é suportada para:

- Direta cópia de segurança de ficheiros e pastas em máquinas no local que executa o agente de MARS.
- Cópia de segurança de cargas de trabalho e ficheiros a partir de um servidor DPM ou MABS.
- Cópia de segurança offline não pode ser utilizada para os ficheiros de estado do sistema.


## <a name="support-for-restore"></a>Suporte para o restauro

- A nova [restauro imediato](backup-instant-restore-capability.md) versão da cópia de segurança do Azure permite-lhe restaurar os dados antes que seja foi copiado para o cofre.<br/><br/> Para utilizar esta funcionalidade a máquina a cópia de segurança tem de estar a executar o .NET Framework 4.5.2 ou superior.
- As cópias de segurança não não possível restaurar um computador de destino que executa uma versão anterior do sistema operativo. Por exemplo, uma cópia de segurança obtida a partir de um computador com Windows 7 pode ser restaurada no Windows 8 ou posterior. No entanto, uma cópia de segurança obtida a partir de um computador com Windows 8 não é possível restaurar para um computador com Windows 7.


## <a name="next-steps"></a>Passos Seguintes
- [Saiba mais](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) sobre a arquitetura de cópia de segurança com o agente MARS.
- [Saiba](backup-support-matrix-mabs-dpm.md) o que é suportado quando executa o agente de MARS num servidor de cópia de segurança do Azure (MABS) da Microsoft ou o System Center DPM.


