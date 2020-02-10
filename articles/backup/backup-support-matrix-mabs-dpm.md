---
title: MABS e System Center DPM matriz de suporte
description: Este artigo resume o suporte de backup Azure quando utiliza o Microsoft Azure Backup Server (MABS) ou o System Center DPM para fazer backup no local e nos recursos Do VM Do Azure.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 211a7e39dc9cda9e4bd96e3a66924b2195524be7
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111459"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matriz de suporte para backup com O Servidor de Backup Microsoft Azure ou DPM do System Center

Pode utilizar o [serviço de backup Azure](backup-overview.md) para fazer backup nas instalações de máquinas e cargas de trabalho, e máquinas virtuais Azure (VMs). Este artigo resume as definições de suporte e limitações para o backup de máquinas utilizando o Microsoft Azure Backup Server (MABS) ou system center Data Protection Manager (DPM) e o Azure Backup.

## <a name="about-dpmmabs"></a>Sobre DPM/MABS

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) é uma solução empresarial que configura, facilita e gere a backup e recuperação de máquinas e dados empresariais. Faz parte do conjunto de produtos do [System Center.](https://www.microsoft.com/cloud-platform/system-center-pricing)

O MABS é um produto de servidor que pode ser usado para fazer back-up sem servidores físicos, VMs e aplicações em execução neles.

O MABS baseia-se no System Center DPM e fornece funcionalidades semelhantes com algumas diferenças:

- Nenhuma licença do System Center é necessária para executar MABS.
- Tanto para MABS como DPM, o Azure fornece armazenamento de reserva a longo prazo. Além disso, o DPM permite-lhe fazer o armazenamento a longo prazo em fita. O MABS não fornece esta funcionalidade.
- Pode fazer o backup a um servidor DPM primário com um servidor DPM secundário. O servidor secundário protegerá a base de dados primária do servidor e as réplicas de fonte de dados armazenadas no servidor primário. Se o servidor primário falhar, o servidor secundário pode continuar a proteger as cargas de trabalho protegidas pelo servidor primário, até que o servidor primário esteja novamente disponível.  O MABS não fornece esta funcionalidade.

Você descarrega MABS do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57520). Pode ser executado no local ou num VM Azure.

O suporte dPM e MABS suporta uma grande variedade de aplicações, e sistemas operativos de servidores e clientes. Fornecem múltiplos cenários de backup:

- Pode recuar ao nível da máquina com suporte de sistema ou de metal nu.
- Pode fazer o back up volumes, partilhas, pastas e ficheiros específicos.
- Pode fazer o backup de aplicações específicas utilizando configurações otimizadas para aplicações.

## <a name="dpmmabs-backup"></a>Backup DPM/MABS

Backup utilizando DPM/MABS e Azure Backup funciona da seguinte forma:

1. O agente de proteção DPM/MABS está instalado em cada máquina que será apoiada.
1. Máquinas e aplicativos são apoiados até ao armazenamento local em DPM/MABS.
1. O agente Microsoft Azure Recovery Services (MARS) está instalado no servidor/MABS DPM.
1. O agente MARS apoia os discos DPM/MABS para um cofre de serviços de recuperação de reserva em Azure utilizando o Azure Backup.

Para obter mais informações:

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura MABS.
- [Reveja o que é apoiado](backup-support-matrix-mars-agent.md) pelo agente mars.

## <a name="supported-scenarios"></a>Cenários suportados

**Cenário** | **Agente** | **Localização**
--- | --- | ---
**Máquinas/cargas de trabalho de back-up no local** | O agente de proteção DPM/MABS funciona nas máquinas que pretende fazer recuar.<br/><br/> O agente MARS no servidor DPM/MABS.<br/> A versão mínima do agente dos Serviços de Recuperação do Microsoft Azure, ou agente de backup Azure, necessária para ativar esta funcionalidade é de 2.0.8719.0.  | O DPM/MABS deve estar a funcionar no local.

## <a name="supported-deployments"></a>Implementações apoiadas

O DPM/MABS pode ser implantado conforme resumido na tabela seguinte.

**Implementação** | **Suporte** | **Detalhes**
--- | --- | ---
**Implantado no local** | Servidor físico<br/><br/>VM hiper-V<br/><br/> VMware VM | Se o DPM/MABS for instalado como vMware VM, apenas faz backup VMs VMware e cargas de trabalho que estão em execução nesses VMs.
**Implantado como um VM de pilha de azure** | Apenas MABS | O DPM não pode ser usado para apoiar os VMs do Azure Stack.
**Implantado como um VM Azure** | Protege os VMs Azure e as cargas de trabalho que estão a decorrer nesses VMs | DPM/MABS em funcionamento em Azure não pode apoiar as máquinas no local.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemas operativos MABS e DPM suportados

A Backup Azure pode fazer backup nas instâncias DPM/MABS que estão a executar qualquer um dos seguintes sistemas operativos. Os sistemas operativos devem estar a executar os mais recentes pacotes e atualizações de serviços.

**Cenário** | **DPM/MABS**
--- | ---
**MABS em um VM Azure** | Windows Server 2012 R2.<br/><br/> Centro de Dados windows 2016.<br/><br/> Centro de Dados Windows 2019.<br/><br/> Recomendamos que comece com uma imagem do mercado.<br/><br/> Padrão Mínimo A2 com dois núcleos e 3,5 GB de RAM.
**DPM em um VM Azure** | System Center 2012 R2 com Atualização 3 ou posterior.<br/><br/> Sistema operativo Windows [conforme exigido pelo System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Recomendamos que comece com uma imagem do mercado.<br/><br/> Padrão Mínimo A2 com dois núcleos e 3,5 GB de RAM.
**MABS no local** | Sistemas operativos suportados de 64 bits:<br/><br/> MABS v3 e mais tarde: Windows Server 2019 (Standard, Datacenter, Essentials). <br/><br/> MABS v2 e mais tarde: Windows Server 2016 (Standard, Datacenter, Essentials).<br/><br/> Todas as versões MABS: Windows Server 2012 R2.<br/><br/>Todas as versões MABS: Windows Storage Server 2012 R2.
**DPM no local** | Servidor físico/Hyper-V VM: System Center 2012 SP1 ou mais tarde.<br/><br/> VMware VM: System Center 2012 R2 com Update 5 ou mais tarde.

>[!NOTE]
>A instalação do Servidor de Backup Azure não é suportada no Windows Server Core ou no Microsoft Hyper-V Server.

## <a name="management-support"></a>Apoio à gestão

**Edição** | **Detalhes**
--- | ---
**Instalação** | Instale DPM/MABS numa máquina unipropósito.<br/><br/> Não instale DPM/MABS num controlador de domínio, numa máquina com a instalação de função do Servidor de Aplicações, numa máquina que esteja a executar o Microsoft Exchange Server ou o System Center Operations Manager, ou num nó de cluster.<br/><br/> [Reveja todos os requisitos do sistema DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domínio** | O DPM/MABS deve ser unido a um domínio. Instale primeiro e, em seguida, junte o DPM/MABS a um domínio. A deslocação do DPM/MABS para um novo domínio após a implantação não é suportada.
**Armazenamento** | O armazenamento de backup moderno (MBS) é suportado a partir de DPM 2016/MABS v2 e posteriormente. Não está disponível para MABS v1.
**Atualização do MABS** | Pode instalar diretamente mABS v3 ou atualizar para MABS v3 a partir de MABS v2. [Saiba mais](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**MABS em movimento** | Mover o MABS para um novo servidor enquanto mantém o armazenamento é suportado se estiver a utilizar MBS.<br/><br/> O servidor deve ter o mesmo nome que o original. Não pode alterar o nome se quiser manter o mesmo depósito e utilizar a mesma base de dados MABS para armazenar pontos de recuperação de dados.<br/><br/> Vai precisar de uma cópia de segurança da base de dados MABS porque terá de restaurá-la.

## <a name="mabs-support-on-azure-stack"></a>Suporte mABS em Azure Stack

Você pode colocar MABS em um VM De Pilha Azure para que você possa gerir backup de VMs De Pilha Azure e cargas de trabalho a partir de um único local.

**Componente** | **Detalhes**
--- | ---
**MABS em Azure Stack VM** | Pelo menos tamanho A2. Recomendamos que comece com uma imagem Do Windows Server 2012 R2 ou Windows Server 2016 do Azure Marketplace.<br/><br/> Não instale mais nada no MABS VM.
**Armazenamento MABS** | Utilize uma conta de armazenamento separada para o MABS VM. O agente MARS que funciona no MABS precisa de armazenamento temporário para uma localização de cache e para manter os dados restaurados da nuvem.
**Piscina de armazenamento MABS** | O tamanho do depósito MABS é determinado pelo número e tamanho dos discos que estão ligados ao MABS VM. Cada tamanho de VM Azure Stack tem um número máximo de discos. Por exemplo, A2 é quatro discos.
**Retenção de MABS** | Não guarde dados de reserva nos discos MABS locais por mais de cinco dias.
**Escala de MABS** | Para aumentar a sua implantação, pode aumentar o tamanho do MABS VM. Por exemplo, pode mudar de série A para D.<br/><br/> Também pode garantir que está a descarregar dados com cópia de segurança para o Azure regularmente. Se necessário, pode implementar servidores MABS adicionais.
**.QUADRO NET em MABS** | O MABS VM necessita de .NET Framework 3.3 SP1 ou posteriormente instalado nele.
**Domínio MABS** | O MABS VM deve ser unido a um domínio. Um utilizador de domínio com privilégios de administração deve instalar MABS no VM.
**Backup de dados do Azure Stack VM** | Pode fazer o backup de ficheiros, pastas e aplicações.
**Backup suportado** | Estes sistemas operativos são suportados para VMs que pretende fazer apoio:<br/><br/> Canal Semi-Anual do Servidor do Windows (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Suporte ao Servidor SQL para VMs de Stack Azure** | Back up SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Volte e recupere uma base de dados.
**Suporte sharePoint para VMs Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Volte e recupere uma fazenda, base de dados, frente e servidor web.
**Requisitos de rede para VMs apoiados** | Todos os VMs em Azure Stack devem pertencer à mesma rede virtual e pertencer à mesma subscrição.

## <a name="dpmmabs-networking-support"></a>Suporte de rede DPM/MABS

### <a name="url-access"></a>Acesso url

O servidor DPM/MABS precisa de acesso a estes URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Conectividade DPM/MABS com backup azure

A conectividade com o serviço de backup Azure é necessária para que as cópias de segurança funcionem corretamente, e a subscrição Azure deve estar ativa. A tabela que se segue mostra o comportamento se estas duas coisas não ocorrerem.

**MABS para Azure** | **Subscrição** | **Backup/Restauro**
--- | --- | ---
Ligada | Ativa | Volte para o disco DPM/MABS.<br/><br/> De volta a Azure.<br/><br/> Restaurar do disco.<br/><br/> Restaurar de Azure.
Ligada | Expirado/deprovisionado | Sem reforços para o disco ou para o Azure.<br/><br/> Se a subscrição estiver caducada, pode restaurar a partir do disco ou do Azure.<br/><br/> Se a subscrição for desativada, não pode restaurar a partir do disco ou do Azure. Os pontos de recuperação do Azure são eliminados.
Sem conectividade por mais de 15 dias | Ativa | Sem reforços para o disco ou para o Azure.<br/><br/> Pode restaurar a partir do disco ou do Azure.
Sem conectividade por mais de 15 dias | Expirado/deprovisionado | Sem reforços para o disco ou para o Azure.<br/><br/> Se a subscrição estiver caducada, pode restaurar a partir do disco ou do Azure.<br/><br/> Se a subscrição for desativada, não pode restaurar a partir do disco ou do Azure. Os pontos de recuperação do Azure são eliminados.

## <a name="dpmmabs-storage-support"></a>Suporte de armazenamento DPM/MABS

Os dados que são apoiados até DPM/MABS são armazenados no armazenamento de discos locais.

**Armazenamento** | **Detalhes**
--- | ---
**MBS** | O armazenamento de backup moderno (MBS) é suportado a partir de DPM 2016/MABS v2 e posteriormente. Não está disponível para MABS v1.
**Armazenamento De MABS em Azure VM** | Os dados são armazenados em discos Azure que estão ligados ao DPM/MABS VM, e que são geridos em DPM/MABS. O número de discos que podem ser utilizados para o depósito DPM/MABS é limitado pelo tamanho do VM.<br/><br/> A2 VM: 4 discos; A3 VM: 8 discos; A4 VM: 16 discos, com um tamanho máximo de 1 TB para cada disco. Isto determina o total de armazenamento de reserva disponível.<br/><br/> A quantidade de dados que pode fazer o backback depende do número e tamanho dos discos anexados.
**Retenção de dados do MABS no Azure VM** | Recomendamos que guarde dados para um dia no disco DPM/MABS Azure e volte do DPM/MABS para o cofre para uma retenção mais longa. Assim, pode proteger uma maior quantidade de dados descarregando-os para o Azure Backup.

### <a name="modern-backup-storage-mbs"></a>Armazenamento de backup moderno (MBS)

A partir de DPM 2016/MABS v2 (em execução no Windows Server 2016) e mais tarde, pode aproveitar o armazenamento de backup moderno (MBS).

- As cópias de segurança MBS são armazenadas num disco do Sistema de Ficheiros Resiliente (ReFS).
- O MBS utiliza a clonagem do bloco ReFS para uma cópia de segurança mais rápida e uma utilização mais eficiente do espaço de armazenamento.
- Quando adicionar volumes à piscina de armazenamento DPM/MABS local, configure-os com letras de unidade. Em seguida, pode configurar o armazenamento de carga de trabalho em diferentes volumes.
- Quando cria grupos de proteção para fazer o backup de dados para DPM/MABS, selecione a unidade que pretende utilizar. Por exemplo, pode armazenar backups para SQL ou outras cargas de trabalho de IOPS de alta performance numa unidade de alto desempenho, e armazenar cargas de trabalho que são apoiadas com menos frequência numa unidade de desempenho mais baixa.

## <a name="supported-backups-to-mabs"></a>Backups suportados para MABS

Para obter informações sobre os vários servidores e cargas de trabalho que pode proteger com o Servidor de Backup Azure, consulte a Matriz de Proteção do Servidor de [Backup Azure](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Backups suportados ao DPM

Para obter informações sobre os vários servidores e cargas de trabalho que pode proteger com o Gestor de Proteção de Dados, consulte o artigo [O que pode o DPM fazer o backup?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019)

- As cargas de trabalho agrupadas apoiadas por DPM/MABS devem estar no mesmo domínio que o DPM/MABS ou num domínio infantil/fidedigno.
- Pode utilizar a autenticação NTLM/certificado para fazer cópias de segurança em domínios ou grupos de trabalho não fidedignos.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura MABS.
- [Reveja](backup-support-matrix-mars-agent.md) o que é apoiado pelo agente mars.
- [Instale](backup-azure-microsoft-azure-backup.md) um servidor MABS.
- [Configurar DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
