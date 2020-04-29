---
title: Matriz de suporte para o agente MARS
description: Este artigo resume o suporte de backup Azure quando faz backup de máquinas que estejam a executar o agente microsoft Azure Recovery Services (MARS).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 6085bc647c06b5907282460a2d8706b8549e1bc2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79247868"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matriz de suporte para backup com o agente microsoft Azure Recovery Services (MARS)

Pode utilizar o [serviço de backup Azure](backup-overview.md) para fazer backup de máquinas e aplicações no local e para fazer backup das máquinas virtuais Azure (VMs). Este artigo resume as definições e limitações de suporte quando utiliza o agente do Microsoft Azure Recovery Services (MARS) para fazer o back-up das máquinas.

## <a name="the-mars-agent"></a>O agente MARS

A Azure Backup utiliza o agente MARS para fazer cópias de segurança dos dados das máquinas no local e dos VMs Azure para um cofre dos Serviços de Recuperação de Backup em Azure. O agente mars pode:

- Faça o funcionar nas máquinas windows no local para que possam voltar diretamente para um cofre de serviços de recuperação de reserva em Azure.
- Execute os VMs do Windows para que possam voltar diretamente para um cofre.
- Executar no Microsoft Azure Backup Server (MABS) ou num servidor do System Center Data Protection Manager (DPM). Neste cenário, as máquinas e cargas de trabalho voltam ao MABS ou ao servidor DPM. O agente mars então apoia este servidor para um cofre em Azure.

> [!NOTE]
>A Cópia de Segurança Azure não suporta a regulação automática do relógio para o horário de verão (DST). Modificar a política para garantir que as economias de luz do dia sejam tidas em conta para evitar discrepâncias entre o tempo real e o tempo de backup programado.

As suas opções de backup dependem de onde o agente está instalado. Para mais informações, consulte a [arquitetura Azure Backup utilizando o agente MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Para obter informações sobre a arquitetura de backup MABS e DPM, consulte [Back up to DPM ou MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Consulte também [os requisitos](backup-support-matrix-mabs-dpm.md) para a arquitetura de backup.

**Instalação** | **Detalhes**
--- | ---
Descarregue o mais recente agente mars | Pode descarregar a versão mais recente do agente a partir do cofre, ou [descarregá-la diretamente](https://aka.ms/azurebackup_agent).
Instale diretamente numa máquina | Pode instalar o agente MARS diretamente num servidor Windows no local ou num VM Windows que esteja a executar qualquer um dos [sistemas operativos suportados](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Instalar num servidor de backup | Quando configurar O DPM ou O MABS para fazer o back-up para o Azure, descarrega e instala o agente MARS no servidor. Pode instalar o agente em [sistemas operativos suportados](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) na matriz de suporte do servidor de backup.

> [!NOTE]
> Por predefinição, os VMs Azure que estão ativados para cópia de segurança têm uma instalação de extensão de backup Azure. Esta extensão apoia todo o VM. Pode instalar e executar o agente MARS num VM Azure ao lado da extensão se pretender fazer o back-up de pastas e ficheiros específicos, em vez do VM completo.
> Quando executa o agente MARS num VM Azure, ele faz back-up ficheiros ou pastas que estão em armazenamento temporário no VM. As cópias de segurança falham se os ficheiros ou pastas forem removidos do armazenamento temporário ou se o armazenamento temporário for removido.

## <a name="cache-folder-support"></a>Suporte da pasta Cache

Quando utiliza o agente MARS para fazer o back-up dos dados, o agente tira uma fotografia dos dados e armazena-os numa pasta de cache local antes de enviar os dados para o Azure. A pasta cache (scratch) tem vários requisitos:

**Cache** | **Detalhes**
--- | ---
Tamanho |  O espaço livre na pasta cache deve ser de pelo menos 5 a 10% do tamanho total dos seus dados de backup.
Localização | A pasta cache deve ser armazenada localmente na máquina que está a ser apoiada, e deve estar online. A pasta cache não deve estar numa partilha de rede, em meios amovíveis ou num volume offline.
Pasta | A pasta cache não deve ser encriptada num volume deduplicado ou numa pasta comprimido, que é escassa, ou que tenha um ponto de reparo.
Mudanças de localização | Pode alterar a localização da cache`net stop bengine`parando o motor de reserva e copiando a pasta cache para uma nova unidade. (Certifique-se de que a nova unidade tem espaço suficiente.) Em seguida, atualize duas entradas de registo em **HKLM\SOFTWARE\Microsoft\Microsoft\Windows Azure Backup** **(Config/ScratchLocation** e **Config/CloudBackupProvider/ScratchLocation**) para a nova localização e reinicie o motor.

## <a name="networking-and-access-support"></a>Suporte em rede e acesso

### <a name="url-and-ip-access"></a>ACESSO URL e IP

O agente MARS precisa de acesso a estes URLs:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net

E a estes endereços IP:

- 20.190.128.0/18
- 40.126.0.0/18

O acesso a todos os URLs e endereços IP listados acima utiliza o protocolo HTTPS na porta 443.

### <a name="azure-expressroute-support"></a>Suporte Azure ExpressRoute

Pode fazer o backup dos seus dados através do Azure ExpressRoute com o público (disponível para circuitos antigos) e o peering da Microsoft. O apoio ao epeering privado não é apoiado.

Com o público: Garantir o acesso aos seguintes domínios/endereços:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

Com o peering da Microsoft, selecione os seguintes serviços/regiões e valores comunitários relevantes:

- Diretório Ativo Azure (12076:5060)
- Região do Microsoft Azure (de acordo com a localização do seu cofre de Serviços de Recuperação)
- Armazenamento Azure (de acordo com a localização do seu cofre dos Serviços de Recuperação)

Para mais informações, consulte os requisitos de [encaminhamento expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>O Público está predestinado a novos circuitos.

### <a name="throttling-support"></a>Apoio de estrangulamento

**Funcionalidade** | **Detalhes**
--- | ---
Controlo de largura de banda | Suportado. No agente MARS, utilize a **Change Properties** para ajustar a largura de banda.
Limitação da rede | Não disponível para máquinas de back-up que executam o Windows Server 2008 R2, Windows Server 2008 SP2 ou Windows 7.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

>[!NOTE]
> O agente MARS não suporta o Windows Server Core SKUs.

Pode utilizar o agente MARS para fazer o apoio direto ao Azure nos sistemas operativos listados abaixo que funcionam em:

1. Servidores windows no local
2. VMs azure executando janelas

Os sistemas operativos devem ter 64 bits e devem estar a executar os mais recentes pacotes e atualizações de serviços. O quadro seguinte resume estes sistemas operativos:

**Sistema Operativo** | **Ficheiros/pastas** | **Estado do sistema** | **Requisitos de software/módulo**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Sim | Não |  Verifique a versão correspondente do servidor para obter requisitos de software/módulo
Windows 8.1 (Enterprise, Pro)| Sim |Não | Verifique a versão correspondente do servidor para obter requisitos de software/módulo
Windows 8 (Enterprise, Pro) | Sim | Não | Verifique a versão correspondente do servidor para obter requisitos de software/módulo
Windows Server 2016 (Standard, Datacenter, Essentials) | Sim | Sim | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistribuível <br> - Consola de Gestão da Microsoft (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Fundação, Essencial) | Sim | Sim | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistribuível <br> - Consola de Gestão da Microsoft (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Fundação) | Sim | Sim |- .NET 4.5 <br> -Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistribuível <br> - Consola de Gestão da Microsoft (MMC) 3.0 <br> - Destacamento de Imagem de Manutenção e Gestão (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Sim | Não | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistribuível <br> - Consola de Gestão da Microsoft (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Sim | Sim | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistribuível <br> - Consola de Gestão da Microsoft (MMC) 3.0

Para mais informações, consulte [os sistemas operativos MABS suportados e DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>Sistemas Operativos no fim do suporte

Os seguintes sistemas operativos encontram-se no final do suporte e recomenda-se vivamente atualizar o sistema operativo para continuar a manter-se protegido.

Se os compromissos existentes impedirem a atualização do sistema operativo, considere migrar os servidores do Windows para VMs Azure e alavancar as cópias de segurança Azure VM para continuar a manter-se protegida. Visite a página de [migração aqui](https://azure.microsoft.com/migration/windows-server/) para mais informações sobre a migração do seu servidor Windows.

Para ambientes instalados ou instalados, onde não é possível atualizar o sistema operativo ou migrar para o Azure, ative atualizações de segurança estendidas para que as máquinas continuem protegidas e apoiadas. Note que apenas edições específicas são elegíveis para Atualizações de Segurança Estendidas. Visite a [página da FAQ](https://www.microsoft.com/cloud-platform/extended-security-updates) para saber mais.

| **Sistema Operativo**                                       | **Ficheiros/pastas** | **Estado do sistema** | **Requisitos de software/módulo**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Sim               | Não                 | Verifique a versão correspondente do servidor para obter requisitos de software/módulo |
| Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Fundação) | Sim               | Sim                | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Microsoft VC++ Redistribuível compatível <br>  - Consola de Gestão da Microsoft (MMC) 3.0 <br>  - Destacamento de Imagem de Manutenção e Gestão (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Fundação)  | Sim               | Não                 | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Microsoft VC++ Redistribuível compatível <br>  - Consola de Gestão da Microsoft (MMC) 3.0 <br>  - Destacamento de Imagem de Manutenção e Gestão (DISM.exe) <br>  - Base do Servidor Virtual 2005 + KB KB948515 |

## <a name="backup-limits"></a>Limites de backup

### <a name="size-limits"></a>Limites de tamanho

A Cópia de Segurança Azure limita o tamanho de um ficheiro ou fonte de dados de pasta que pode ser apoiada. Os itens que você recua de um único volume não podem exceder os tamanhos resumidos nesta tabela:

**Sistema Operativo** | **Limite de tamanho**
--- | ---
Windows Server 2012 ou posterior |54 400 GB
Windows Server 2008 R2 SP1 |1.700 GB
Windows Server 2008 SP2| 1.700 GB
Windows 8 ou posterior| 54 400 GB
Windows 7| 1.700 GB

### <a name="other-limitations"></a>Outras limitações

- Mars não suporta a proteção de várias máquinas com o mesmo nome para um único cofre.

## <a name="supported-file-types-for-backup"></a>Tipos de ficheiros suportados para backup

**Tipo** | **Suporte**
--- | ---
Encriptado<sup>*</sup>| Suportado.
Comprimidos | Suportado.
Disperso | Suportado.
Comprimido e escasso |Suportado.
Ligações fixas| Não suportado. Saltou.
Ponto de reanálise| Não suportado. Saltou.
Encriptado e escasso |Não suportado. Saltou.
Fluxo comprimido| Não suportado. Saltou.
Riacho escasso| Não suportado. Saltou.
OneDrive (ficheiros sincronizados são fluxos escassos)| Não suportado.
Pastas com replicação DFS ativadas | Não suportado.

\*Certifique-se de que o agente MARS tem acesso aos certificados necessários para aceder aos ficheiros encriptados. Os ficheiros inacessíveis serão ignorados.

## <a name="supported-drives-or-volumes-for-backup"></a>Unidades ou volumes suportados para backup

**Unidade/volume** | **Suporte** | **Detalhes**
--- | --- | ---
Volumes só de leitura| Não suportado | O Serviço sombra de cópia de volume (VSS) só funciona se o volume for repreensível.
Volumes offline| Não suportado |O VSS só funciona se o volume estiver online.
Partilha de rede| Não suportado |O volume deve ser local no servidor.
Volumes bloqueados por BitLocker| Não suportado |O volume deve ser desbloqueado antes do início da cópia de segurança.
Identificação do sistema de ficheiros| Não suportado |Apenas o NTFS é apoiado.
Meios amovíveis| Não suportado |Todas as fontes de artigo de reserva devem ter um estado *fixo.*
Unidades desduplicadas | Suportado | O Azure Backup converte dados desduplicados em dados normais. Otimiza, encripta, armazena e envia os dados para o cofre.

## <a name="support-for-initial-offline-backup"></a>Suporte para cópia de segurança offline inicial

O Azure Backup suporta *a sementeira offline* para transferir dados iniciais de backup para o Azure utilizando discos. Este suporte é útil se o seu backup inicial for suscetível de estar na gama de tamanhos de terabytes (TBs). A cópia de segurança offline é suportada para:

- Cópia de segurança direta de ficheiros e pastas nas máquinas no local que estão a executar o agente MARS.
- Cópia de segurança de cargas de trabalho e ficheiros de um servidor DPM ou MABS.

A cópia de segurança offline não pode ser usada para ficheiros do estado do sistema.

## <a name="support-for-data-restoration"></a>Apoio à restauração de dados

Ao utilizar a funcionalidade [De restauro instantâneo](backup-instant-restore-capability.md) da Cópia de Segurança Azure, pode restaurar os dados antes de ser copiado para o cofre. A máquina que está a apoiar deve estar a funcionar .NET Framework 4.5.2 ou superior.

As cópias de segurança não podem ser restauradas a uma máquina-alvo que está a executar uma versão anterior do sistema operativo. Por exemplo, uma cópia de segurança retirada de um computador que está a executar o Windows 7 pode ser restaurada no Windows 8 ou posteriormente. Mas uma cópia de segurança tirada de um computador que está a executar o Windows 8 não pode ser restaurada num computador que está a executar o Windows 7.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a arquitetura de backup que utiliza o agente MARS.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
- Saiba o que é suportado quando [executa o agente MARS no MABS ou num servidor DPM](backup-support-matrix-mabs-dpm.md).
