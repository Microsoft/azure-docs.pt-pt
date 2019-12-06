---
title: MABS & matriz de suporte do System Center DPM
description: Este artigo resume o suporte ao backup do Azure quando você usa o Backup do Microsoft Azure Server (MABS) ou o System Center DPM para fazer backup de recursos locais e da VM do Azure.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: f9ee31525f2ee5a19aebe0a9258dff3ecfdcbb92
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841172"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matriz de suporte para backup com o Backup do Microsoft Azure Server ou o System Center DPM

Você pode usar o [serviço de backup do Azure](backup-overview.md) para fazer backup de máquinas locais e cargas de trabalho e VMs (máquinas virtuais) do Azure. Este artigo resume as configurações de suporte e as limitações para fazer backup de computadores usando o Backup do Microsoft Azure Server (MABS) ou o System Center Data Protection Manager (DPM) e o backup do Azure.

## <a name="about-dpmmabs"></a>Sobre o DPM/MABS

[O System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) é uma solução empresarial que configura, facilita e gerencia o backup e a recuperação de computadores e dados corporativos. Faz parte do pacote de produtos do [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) .

MABS é um produto de servidor que pode ser usado para fazer backup de servidores físicos locais, VMs e aplicativos em execução neles.

O MABS é baseado no System Center DPM e fornece funcionalidade semelhante com algumas diferenças:

- Nenhuma licença do System Center é necessária para executar o MABS.
- Para o MABS e o DPM, o Azure fornece armazenamento de backup de longo prazo. Além disso, o DPM permite que você faça backup de dados para armazenamento de longo prazo em fita. O MABS não fornece essa funcionalidade.
- Você pode fazer backup de um servidor DPM primário com um servidor DPM secundário. O servidor secundário irá proteger a base de dados do servidor principal e as réplicas das origens de dados armazenadas no servidor principal. Se o servidor principal falhar, o servidor secundário pode continuar a proteger as cargas de trabalho que estão protegidas pelo servidor principal até que este esteja novamente disponível.  O MABS não fornece essa funcionalidade.

Você baixa o MABS do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57520). Ele pode ser executado localmente ou em uma VM do Azure.

O DPM e o MABS dão suporte ao backup de uma ampla variedade de aplicativos e sistemas operacionais de servidor e cliente. Eles fornecem vários cenários de backup:

- Você pode fazer backup no nível do computador com backup de estado do sistema ou bare-metal.
- Você pode fazer backup de volumes, compartilhamentos, pastas e arquivos específicos.
- Você pode fazer backup de aplicativos específicos usando configurações otimizadas de reconhecimento de aplicativo.

## <a name="dpmmabs-backup"></a>Backup do DPM/MABS

O backup usando o DPM/MABS e o backup do Azure funciona da seguinte maneira:

1. O agente de proteção do DPM/MABS é instalado em cada computador cujo backup será feito.
1. O backup de computadores e aplicativos é feito no armazenamento local no DPM/MABS.
1. O agente de Serviços de Recuperação do Microsoft Azure (MARS) é instalado no servidor DPM/MABS.
1. O agente MARS faz backup dos discos do DPM/MABS em um cofre de serviços de recuperação de backup no Azure usando o backup do Azure.

Para mais informações:

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do mAbs.
- [Examine o que tem suporte](backup-support-matrix-mars-agent.md) para o agente Mars.

## <a name="supported-scenarios"></a>Cenários suportados

**Cenário** | **Agente** | **Localização**
--- | --- | ---
**Fazer backup de máquinas/cargas de trabalho locais** | O agente de proteção do DPM/MABS é executado nos computadores que você deseja fazer backup.<br/><br/> O agente MARS no servidor DPM/MABS.<br/> A versão mínima do agente de Serviços de Recuperação do Microsoft Azure ou do agente de backup do Azure, necessária para habilitar esse recurso, é 2.0.8719.0.  | O DPM/MABS deve estar em execução no local.

## <a name="supported-deployments"></a>Implantações com suporte

O DPM/MABS pode ser implantado como resumido na tabela a seguir.

**Implementação** | **Suporte** | **Detalhes**
--- | --- | ---
**Implantado localmente** | Servidor físico<br/><br/>VM do Hyper-V<br/><br/> VM VMware | Se o DPM/MABS for instalado como uma VM VMware, ele fará o backup apenas das VMs e das cargas de trabalho do VMware em execução nessas VMs.
**Implantado como uma VM Azure Stack** | Somente MABS | O DPM não pode ser usado para fazer backup de Azure Stack VMs.
**Implantado como uma VM do Azure** | Protege as VMs e cargas de trabalho do Azure em execução nessas VMs | O DPM/MABS em execução no Azure não pode fazer backup de computadores locais.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemas operacionais MABS e DPM com suporte

O backup do Azure pode fazer backup de instâncias do DPM/MABS que estão executando qualquer um dos sistemas operacionais a seguir. Os sistemas operacionais devem estar executando os service packs e as atualizações mais recentes.

**Cenário** | **DPM/MABS**
--- | ---
**MABS em uma VM do Azure** | Windows Server 2012 R2.<br/><br/> Datacenter do Windows 2016.<br/><br/> Datacenter do Windows 2019.<br/><br/> Recomendamos que você inicie com uma imagem do Marketplace.<br/><br/> Padrão a2 mínimo com dois núcleos e 3,5 GB de RAM.
**DPM em uma VM do Azure** | System Center 2012 R2 com atualização 3 ou posterior.<br/><br/> Sistema operacional Windows, conforme [exigido pelo System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Recomendamos que você inicie com uma imagem do Marketplace.<br/><br/> Padrão a2 mínimo com dois núcleos e 3,5 GB de RAM.
**MABS local** | Sistemas operacionais de 64 bits com suporte:<br/><br/> MABS v3 e posterior: Windows Server 2019 (Standard, Datacenter, Essentials). <br/><br/> MABS V2 e posterior: Windows Server 2016 (Standard, Datacenter, Essentials).<br/><br/> Todas as versões do MABS: Windows Server 2012 R2.<br/><br/>Todas as versões do MABS: Windows Storage Server 2012 R2.
**DPM local** | VM do Hyper-V/servidor físico: System Center 2012 SP1 ou posterior.<br/><br/> VM VMware: System Center 2012 R2 com atualização 5 ou posterior.

## <a name="management-support"></a>Suporte de gerenciamento

**Problema** | **Detalhes**
--- | ---
**Instalação** | Instale o DPM/MABS em um computador de finalidade única.<br/><br/> Não instale o DPM/MABS em um controlador de domínio, em um computador com a instalação da função de servidor de aplicativos, em um computador que esteja executando o Microsoft Exchange Server ou System Center Operations Manager, ou em um nó de cluster.<br/><br/> [Examine todos os requisitos de sistema do DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domínio** | O DPM/MABS deve ser Unido a um domínio. Instale o primeiro e, em seguida, ingresse o DPM/MABS em um domínio. Não há suporte para a movimentação do DPM/MABS para um novo domínio após a implantação.
**Armazenamento** | O MBS (armazenamento de backup moderno) tem suporte do DPM 2016/MABS V2 e posterior. Ele não está disponível para MABS v1.
**Atualização do MABS** | Você pode instalar o MABS v3 diretamente ou atualizar para o MABS V3 do MABS v2. [Saiba mais](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Movendo MABS** | Mover MABS para um novo servidor enquanto mantém o armazenamento é suportado se você estiver usando MBS.<br/><br/> O servidor deve ter o mesmo nome que o original. Você não pode alterar o nome se quiser manter o mesmo pool de armazenamento e usar o mesmo banco de dados MABS para armazenar os pontos de recuperação.<br/><br/> Você precisará de um backup do banco de dados MABS, pois você precisará restaurá-lo.

## <a name="mabs-support-on-azure-stack"></a>Suporte do MABS no Azure Stack

Você pode implantar o MABS em uma VM Azure Stack para que você possa gerenciar o backup de VMs Azure Stack e cargas de trabalho de um único local.

**Componente** | **Detalhes**
--- | ---
**MABS na VM Azure Stack** | Pelo menos o tamanho a2. Recomendamos que você comece com uma imagem do Windows Server 2012 R2 ou do Windows Server 2016 do Azure Marketplace.<br/><br/> Não instale mais nada na VM MABS.
**Armazenamento MABS** | Use uma conta de armazenamento separada para a VM MABS. O agente MARS em execução no MABS precisa de armazenamento temporário para um local de cache e para manter os dados restaurados da nuvem.
**Pool de armazenamento MABS** | O tamanho do pool de armazenamento MABS é determinado pelo número e pelo tamanho dos discos que são anexados à VM MABS. Cada tamanho de VM Azure Stack tem um número máximo de discos. Por exemplo, a2 é de quatro discos.
**MABS retenção** | Não retenha os dados de backup nos discos MABS locais por mais de cinco dias.
**Escalar verticalmente MABS** | Para escalar verticalmente sua implantação, você pode aumentar o tamanho da VM MABS. Por exemplo, você pode alterar de uma série para D.<br/><br/> Você também pode garantir que esteja descarregando dados com backup no Azure regularmente. Se necessário, você pode implantar servidores MABS adicionais.
**.NET Framework em MABS** | A VM MABS precisa .NET Framework 3,3 SP1 ou posterior instalado.
**Domínio MABS** | A VM MABS deve ser unida a um domínio. Um usuário de domínio com privilégios de administrador deve instalar o MABS na VM.
**Backup de dados da VM Azure Stack** | Você pode fazer backup de arquivos, pastas e aplicativos.
**Backup com suporte** | Esses sistemas operacionais têm suporte para VMs das quais você deseja fazer backup:<br/><br/> Canal semestral do Windows Server (datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Suporte SQL Server para VMs Azure Stack** | Faça backup SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Fazer backup e recuperar um banco de dados.
**Suporte do SharePoint para VMs Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Faça backup e recupere um farm, banco de dados, front-end e servidor Web.
**Requisitos de rede para VMs de backup** | Todas as VMs na carga de trabalho Azure Stack devem pertencer à mesma rede virtual e pertencer à mesma assinatura.

## <a name="dpmmabs-networking-support"></a>Suporte de rede do DPM/MABS

### <a name="url-access"></a>Acesso à URL

O servidor DPM/MABS precisa de acesso a essas URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- \*.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Conectividade do DPM/MABS com o backup do Azure

A conectividade com o serviço de backup do Azure é necessária para que os backups funcionem corretamente e a assinatura do Azure deve estar ativa. A tabela a seguir mostra o comportamento se essas duas coisas não ocorrerem.

**MABS para o Azure** | **Subscrição** | **Backup/restauração**
--- | --- | ---
Ligada | Ativa | Faça backup no disco do DPM/MABS.<br/><br/> Faça backup no Azure.<br/><br/> Restaurar do disco.<br/><br/> Restaurar do Azure.
Ligada | Expirado/desprovisionado | Nenhum backup em disco ou Azure.<br/><br/> Se a assinatura tiver expirado, você poderá restaurar do disco ou do Azure.<br/><br/> Se a assinatura for encerrada, você não poderá restaurar do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.
Sem conectividade por mais de 15 dias | Ativa | Nenhum backup em disco ou Azure.<br/><br/> Você pode restaurar do disco ou do Azure.
Sem conectividade por mais de 15 dias | Expirado/desprovisionado | Nenhum backup em disco ou Azure.<br/><br/> Se a assinatura tiver expirado, você poderá restaurar do disco ou do Azure.<br/><br/> Se a assinatura for encerrada, você não poderá restaurar do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.

## <a name="dpmmabs-storage-support"></a>Suporte ao armazenamento do DPM/MABS

Os dados submetidos a backup no DPM/MABS são armazenados no armazenamento em disco local.

**Armazenamento** | **Detalhes**
--- | ---
**MBS** | O MBS (armazenamento de backup moderno) tem suporte do DPM 2016/MABS V2 e posterior. Ele não está disponível para MABS v1.
**Armazenamento MABS na VM do Azure** | Os dados são armazenados em discos do Azure que são anexados à VM do DPM/MABS e que são gerenciados no DPM/MABS. O número de discos que podem ser usados para o pool de armazenamento do DPM/MABS é limitado pelo tamanho da VM.<br/><br/> VM a2:4 discos; VM a3:8 discos; VM A4:16 discos, com um tamanho máximo de 1 TB para cada disco. Isso determina o pool de armazenamento de backup total que está disponível.<br/><br/> A quantidade de dados que você pode fazer backup depende do número e do tamanho dos discos anexados.
**Retenção de dados do MABS na VM do Azure** | Recomendamos que você retenha dados de um dia no disco do Azure/MABS do DPM e faça backup do DPM/MABS no cofre para maior retenção. Portanto, você pode proteger uma quantidade maior de dados descarregando-os no backup do Azure.

### <a name="modern-backup-storage-mbs"></a>Armazenamento de backup moderno (MBS)

Do DPM 2016/MABS v2 (em execução no Windows Server 2016) e posterior, você pode aproveitar o armazenamento de backup moderno (MBS).

- Os backups MBS são armazenados em um disco ReFS (sistema de arquivos resiliente).
- O MBS usa a clonagem de bloco ReFS para um backup mais rápido e um uso mais eficiente do espaço de armazenamento.
- Quando você adiciona volumes ao pool de armazenamento local do DPM/MABS, você os configura com letras de unidade. Em seguida, você pode configurar o armazenamento de carga de trabalho em volumes diferentes.
- Ao criar grupos de proteção para fazer backup de dados no DPM/MABS, selecione a unidade que deseja usar. Por exemplo, você pode armazenar backups para SQL ou outras cargas de trabalho de IOPS alta em uma unidade de alto desempenho e armazenar cargas de trabalho que são submetidas a backup com menos frequência em uma unidade de desempenho inferior.

## <a name="supported-backups-to-mabs"></a>Backups com suporte para MABS

Para obter informações sobre os vários servidores e cargas de trabalho que você pode proteger com Servidor de Backup do Azure, consulte a [matriz de proteção de servidor de backup do Azure](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Backups com suporte para o DPM

Para obter informações sobre os vários servidores e cargas de trabalho que você pode proteger com Data Protection Manager, consulte o artigo [o que o DPM pode fazer backup?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019).

- As cargas de trabalho clusterizadas com backup pelo DPM/MABS devem estar no mesmo domínio que o DPM/MABS ou em um domínio filho/confiável.
- Você pode usar a autenticação NTLM/de certificado para fazer backup de dados em domínios ou grupos de domínio não confiáveis.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do mAbs.
- [Examine](backup-support-matrix-mars-agent.md) o que tem suporte para o agente Mars.
- [Configure](backup-azure-microsoft-azure-backup.md) um servidor mAbs.
- [Configurar o DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
