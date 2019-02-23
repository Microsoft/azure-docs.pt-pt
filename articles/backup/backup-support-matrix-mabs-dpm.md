---
title: Matriz de suporte para o servidor de cópia de segurança do Microsoft Azure e o System Center DPM
description: Este artigo resume o suporte de cópia de segurança do Azure quando utiliza o Microsoft Azure Backup Server ou o System Center DPM para cópia de segurança no local e recursos de VM do Azure.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 0544782763bebac42b4b68fda42cff80b18ff067
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670739"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-serversystem-center-dpm"></a>Matriz de suporte para cópia de segurança com o Microsoft Azure Backup Server/System Center DPM

Pode utilizar o [serviço de cópia de segurança do Azure](backup-overview.md) para fazer uma cópia de segurança de máquinas no local e cargas de trabalho e as VMs do Azure. Este artigo resume as definições de suporte e limitações das cópias de máquinas com o servidor de cópia de segurança do Azure (MABS) da Microsoft ou System Center Data Protection Manager (DPM) e cópia de segurança do Azure.

## <a name="about-mabsdpm"></a>Sobre o MABS/DPM

[O System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) é uma solução empresarial que configura, facilita e gere a cópia de segurança e recuperação de máquinas de empresa e dados. Ele da parte a [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) conjunto de produtos.


Servidor de cópia de segurança do Azure (MABS) da Microsoft é um produto de servidor que pode ser utilizado para fazer uma cópia de segurança de servidores físicos no local, máquinas virtuais (VMs) e aplicações em execução neles, e as VMs do Azure.

- MABS se baseia no System Center Data Protection Manager (DPM) e fornece uma funcionalidade semelhante com algumas diferenças:
    - Nenhuma licença do System Center é necessário para executar o MABS.
    - Para o MABS e do DPM Azure fornece armazenamento de cópia de segurança de longa duração. Além disso, o DPM permite-lhe criar cópias de segurança para armazenamento de longa duração em banda. MABS não fornece essa funcionalidade.
- Transferir o servidor de MABS a partir da [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=57520). Pode ser executado no local, ou numa VM do Azure no Azure.

O DPM e o MABS oferecem suporte de backup de uma grande variedade de aplicações e servidor e sistemas operativos cliente. Eles fornecem vários cenários de cópia de segurança:
    - Pode criar cópias de segurança ao nível da máquina com o backup de estado do sistema ou bare-metal.
    - Pode fazer backup de volumes específicos, partilhas, pastas e ficheiros.
    - Pode criar cópias de segurança aplicações específicas, usando as configurações com suporte para a aplicação otimizadas.

## <a name="mabsdpm-backup"></a>Cópia de segurança MABS/DPM

Cópia de segurança com o MABS/DPM e de cópia de segurança do Azure funciona da seguinte forma:

1. Agente de proteção do DPM/MABS instalado em cada máquina que será efetuada. 
2. Aplicações/máquinas são guardadas no armazenamento local no DPM/MABS.
3. O agente dos serviços de recuperação do Azure (MARS) da Microsoft está instalado no servidor DPM/MABS.
4. O agente de MARS faz o backup dos discos do DPM/MABS para um cofre dos serviços de recuperação de cópia de segurança no Azure, através de cópia de segurança do Azure.

Para obter mais informações:
- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Revisão](backup-support-matrix-mars-agent.md) o que é suportado para o agente de MARS.

## <a name="supported-scenarios"></a>Cenários suportados 

**Cenário** | **Agente** | **Localização**
--- | --- | ---
**Cópia de segurança no local/cargas de trabalho de máquinas** | Agente de proteção do DPM/MABS é executado nas máquinas que pretende criar cópias de segurança<br/><br/> O agente de MARS no servidor MABS/DPM | MABS/DPM tem de ser executado no local
**Criar cópias de segurança de VMs do Azure/cargas de trabalho** | Agente de proteção do DPM/MABS no computador protegido<br/><br/> Agente de MARS MABS/DPM | Tem de executar o DPM/MABS numa VM do Azure.

## <a name="supported-deployments"></a>Implementações suportadas

O DPM/MABS pode ser implementado conforme resumido na tabela.

**Implementação** | **Suporte** | **Detalhes**
--- | --- | ---
**Implementar no local** | Servidor físico<br/><br/>VM de Hyper-V<br/><br/> VM de VMware | Se o DPM/MABS é instalado como uma VM do VMware que só faz backup de VMs de VMware e cargas de trabalho em execução nessas VMS.
**Implementado como uma VM de pilha do Azure** | Apenas o MABS | O DPM não pode ser utilizado para fazer uma cópia de segurança de VMs do Azure Stack.
**Implementado como uma VM do Azure** | Protege as VMs do Azure e cargas de trabalho em execução nessas VMS. | MABS/DPM em execução no Azure não é possível fazer backup de máquinas no local.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemas de operativos MABS e o DPM suportados

O Azure Backup pode criar cópias de segurança MABS/o DPM for executado qualquer um dos seguintes sistemas operativos. Sistemas operativos devem estar a executar as atualizações e service packs mais recentes.

**Cenário** | **MABS/DPM** 
--- | --- 
**MABS numa VM do Azure** | Windows Server 2012 R2<br/><br/> O Centro de dados do Windows 2016<br/><br/> O Centro de dados do Windows de 2019<br/><br/> Recomendamos que comece com uma imagem do marketplace.<br/><br/> Mínimo A2 padrão com dois núcleos e 3,5 GB de RAM. 
**DPM numa VM do Azure** | System Center 2012 R2 com Update 3 ou posterior.<br/><br/> Sistema de operativo do Windows conforme [exigidas pelo System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Recomendamos que comece com uma imagem do marketplace.<br/><br/> Mínimo A2 padrão com dois núcleos e 3,5 GB de RAM. 
**MABS no local** | Suporte para sistemas de operativos de 64 bits:<br/><br/> E posteriores para o MABS v3: Windows Server 2019 (Standard, Datacenter, Essentials) <br/><br/> V2 do MABS e posteriores: Windows Server 2016 (Standard, Datacenter, Essentials)<br/><br/> Todas as versões do MABS: Windows Server 2012 R2/2012 (Standard, Datacenter, Foundation)<br/><br/>Todas as versões do MABS: Windows Storage Server 2012 R2/2012 (Standard/Workgroup)
**DPM no local** | Físico servidor/Hyper-V VM: System Center 2012 SP1 ou posterior.<br/><br/> VM de VMware: System Center 2012 R2 com Update 5 ou posterior. 



## <a name="management-support"></a>Suporte de gestão
**Problema** | **Detalhes**
--- | ---
**Instalação** | O DPM/MABS deve ser instalado numa máquina de objetivo único.<br/><br/> Não instale o DPM/MABS num controlador de domínio, num computador com a instalação da função de servidor de aplicações, num computador a executar o Exchange Server ou o System Center Operations Manager ou num nó de cluster.<br/><br/> [Revisão](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) todos os requisitos de sistema do DPM.
**Domínio** | O servidor DPM/MABS deve ser associado a um domínio. Instalar o primeiro e, em seguida, associar o DPM/MABS a um domínio. Mover o DPM/MABS para um novo domínio após a implementação não é suportada.
**Armazenamento** | Armazenamento de cópia de segurança Moderno (MBS) é suportado a partir do v2 do DPM 2016/MABS e posteriores. Não está disponível para a v1 do MABS.
**Atualização do MABS** | Pode diretamente MABS v3 de instalar ou atualizar o MABS v3 do MABS v2. [Saiba mais](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Mover o MABS** | Mover o MABS para um novo servidor, mantendo o armazenamento é suportada se estiver a utilizar MB.<br/><br/> O servidor tem de ter o mesmo nome que o original. Não é possível alterar o nome, se pretender manter o mesmo agrupamento de armazenamento e utilizar a mesma base de dados do MABS para armazenar os pontos de recuperação de dados.<br/><br/> Terá uma cópia de segurança da base de dados MABS porque terá de restaurá-lo.




## <a name="mabs-support-on-azure-stack"></a>Suporte do MABS no Azure Stack

Pode implementar MABS numa VM do Azure Stack para que possam gerir cópia de segurança de VMs do Azure Stack e cargas de trabalho a partir de uma única localização.

**Componente** | **Detalhes**
--- | --- 
**MABS na VM do Azure Stack** | Pelo menos tamanho A2. Recomendamos que comece com uma imagem do Windows Server 2012 R2 ou Windows Server 2016 no Azure marketplace.<br/><br/> Não deve instalar qualquer outra coisa na VM do MABS.
**Armazenamento MABS** | Utilize uma conta de armazenamento separada para a VM de MAB. O agente de MARS em execução no MABS precisa de armazenamento temporário para uma localização da cache e para manter o restauro de dados da cloud.
**Agrupamento de armazenamento do MABS** | O tamanho do agrupamento de armazenamento do MABS é determinado pelo número e tamanho dos discos ligados à VM do MABS. Cada tamanho de VM do Azure Stack tem um número máximo de discos. Por exemplo A2 é quatro discos.
**Retenção do MABS** | Não deve manter os dados com cópia de segurança dos discos locais do MABS durante mais de cinco dias.
**MABS aumentar verticalmente** | Para aumentar verticalmente a sua implementação, pode aumentar o tamanho da MABS VM. Por exemplo a partir de à série D.<br/><br/> Também pode garantir estiver descarregar dados com cópia de segurança para o Azure regularmente e, se necessário, pode implementar servidores adicionais do MABS.
.NET framework em MABS | A VM do MABS precisa do .NET Framework 3.3 SP1 ou superior instalado no mesmo.
**Domínio do MABS** | A VM do MABS tem de estar associada a um domínio. Um utilizador de domínio com privilégios de administrador tem de instalar MABS na VM.
**Cópia de segurança de dados do Azure Stack VM** | Pode fazer backup de arquivos, pastas e aplicações.
**Cópia de segurança suportada** | Estes sistemas operativos são suportados para VMs que pretende criar cópias de segurança:<br/><br/> Windows Server via de atualizações Semianuais (Centro de dados/Enterprise/Standard)<br/><br/> Windows Server 2016/2012 R2/2012/2008 R2.
**Suporte do SQL para as VMs do Azure Stack** | Cópia de segurança do SQL Server 2016/2014/2012 SP1.<br/><br/> Criar cópias de segurança e recuperar a base de dados.
**Suporte para VMs do Azure Stack SharePoint** | SharePoint 2016/2013/2010.<br/><br/> Criar cópias de segurança e recupere o farm, base de dados, front-end, servidor web.
**Requisitos de rede para VMs com cópia de segurança** | Todas as VMs na carga de trabalho do Azure Stack tem de ser a mesma VNet e pertencer à mesma subscrição.


## <a name="dpmmabs-networking-support"></a>Suporte a redes DPM/MABS

### <a name="url-access"></a>Acesso por URL

O servidor DPM/MABS precisa de aceder a estes URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- \*.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>O DPM/MABS conectividade para o Azure Backup

Conectividade para o serviço de cópia de segurança do Azure é necessária para as cópias de segurança funcionar corretamente, e a subscrição do Azure deve estar ativa. A tabela seguinte mostra o comportamento se essas duas coisas não ocorrerem.

**MABS para o Azure** | **Subscrição** | **Cópia de segurança/restauro** 
--- | --- | --- 
Ligado | Ativa | Cópia de segurança em disco do DPM/MABS<br/><br/> Cópia de Segurança para o Azure<br/><br/> Restaurar a partir do disco<br/><br/> Restaurar a partir do Azure
Ligado | Expirado/desaprovisionada | Nenhuma cópia de segurança para o disco ou do Azure.<br/><br/> Se a subscrição expirou pode restaurar a partir de disco ou no Azure.<br/><br/> Se a subscrição está desativada não é possível restaurar a partir de disco ou no Azure. Os pontos de recuperação do Azure são eliminados.
Sem conectividade durante mais de 15 dias | Ativa | Nenhuma cópia de segurança para o disco ou do Azure.<br/><br/> Pode restaurar a partir do disco ou do Azure.
Sem conectividade durante mais de 15 dias | Expirado/desaprovisionada | Nenhuma cópia de segurança para o disco ou do Azure.<br/><br/> Se a subscrição expirou pode restaurar a partir de disco ou no Azure.<br/><br/> Se a subscrição está desativada não é possível restaurar a partir de disco ou no Azure. Os pontos de recuperação do Azure são eliminados.



## <a name="dpmmabs-storage-support"></a>Suporte de armazenamento do DPM/MABS

Dados copiados para o DPM/MABS são armazenados no armazenamento de disco local. 

**Armazenamento** | **Detalhes**
--- | ---
**MBS** | Armazenamento de cópia de segurança Moderno (MBS) é suportado a partir do v2 do DPM 2016/MABS e posteriores. Não está disponível para a v1 do MABS.
**Armazenamento MABS numa VM do Azure** | Dados são armazenados nos discos Azure ligados à VM DPM/MABS e geridos no DPM/MABS. O número de discos que podem ser utilizadas para agrupamento de armazenamento do DPM/MABS é limitado pelo tamanho da VM.<br/><br/> A2 VM: 4 discos; A3 VM: 8 discos; A4 VM: 16 discos, com um tamanho máximo de 1 TB para cada disco. Determina o agrupamento de armazenamento de cópia de segurança total disponível.<br/><br/> A quantidade de dados, que pode criar cópias de segurança depende do número e tamanho dos discos anexados.
**Retenção de dados do MABS numa VM do Azure** | Recomendamos que mantenha os dados durante um dia no disco do Azure do DPM/MABS e cópia de segurança do DPM/MABS para o cofre para retenção mais longa. Assim, pode proteger uma quantidade maior de dados ao descarregá-los para o Azure Backup.


### <a name="modern-backup-storage-mbs"></a>Armazenamento de cópia de segurança Moderno (MBS)
No DPM 2016/MABS v2 (em execução no Windows Server 2016) ou posterior, pode tirar partido do armazenamento de cópia de segurança Moderno (MBS).

- Cópias de segurança MBS são armazenadas num disco de sistema de ficheiros Resiliente (ReFS).
- Utilizações MBS ReFS bloqueiam a clonagem e de cópia de segurança mais rápido e mais eficiente utilizam de espaço de armazenamento.
- Quando adiciona volumes ao agrupamento de armazenamento local do DPM/MABS, configurado-los com letras de unidade. Em seguida, pode configurar o armazenamento de carga de trabalho em volumes diferentes.
- Quando criar grupos de proteção para cópias de segurança no DPM/MABS, selecione a unidade que pretende utilizar. Por exemplo, pode armazenar cópias de segurança para SQL ou outras cargas de trabalho IOPS elevadas, uma unidade de elevado desempenho e cargas de trabalho que são uma cópia de segurança com menos frequência numa unidade de desempenho inferior.


## <a name="supported-backups-to-mabs"></a>Cópias de segurança suportadas MABS

A tabela seguinte resume o que pode ser uma cópia de segurança para o MABS de máquinas no local e VMs do Azure.


**Cópia de segurança** | **Versões** | **MABS** | **Detalhes**
--- | --- | --- | ---
**Windows 10, 8.1, 8, 7**<br/><br/>(32/64-bit) | MABS v3, v2, V1 | Local | Volume/share/folder/file<br/><br/> Volumes com eliminação de duplicados suportados<br/><br/> Volumes têm de ser, pelo menos, 1 GB e NTFS.
**Windows Server 2016 (Centro de dados, padrão, não o Nano)**<br/><br/> 64/32 bits | MABS v3, v2 | VM no local/Azure.| Volume/share/folder/file; system-state/bare-metal<br/><br/> Volumes com eliminação de duplicados suportados.
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bits | MABS v3, v2, v1 | VM no local/Azure. | **Proteção do local**: Volume/share/folder/file; system-state/bare-metal<br/><br/> **Azure VMprotection**: Volume/share/folder/file<br/><br/> Volumes com eliminação de duplicados suportados.
**Windows Server 2012 com SP1 (Datacenter e Standard)**<br/><br/> 64/32 bits | MABS v3, v2, v1<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) tem de ser instalado. | VM no local/Azure. | **Proteção do local**: Volume/share/folder/file; system-state/bare-metal<br/><br/> **Proteção de VM do Azure**: Volume/share/folder/file<br/><br/> Volumes com eliminação de duplicados suportados.
**Windows Server 2012 (Datacenter e Standard)**<br/><br/> 64/32 bits | MABS v1 | VM no local/Azure. | **Proteção do local**: Volume/share/folder/file; system-state/bare-metal<br/><br/> **Proteção de VM do Azure**: Volume/share/folder/file<br/><br/> Volumes com eliminação de duplicados suportados.
**Windows 2008 R2 com SP1 (Standard e Enterprise)**<br/><br/> 64/32 bits | Suportado pelo MABS v3, v2, v1.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) tem de ser instalado. | VM no local/Azure. |   **Proteção do local**: Volume/share/folder/file; system-state/bare-metal<br/><br/> **Proteção de VM do Azure**: Volume/share/folder/file<br/><br/> Volumes com eliminação de duplicados suportados.
**Windows 2008 R2 (Standard e Enterprise)**<br/><br/> 64/32 bits | MABS v1. Para o MABS v2/v3 o sistema operacional tem de executar SP1. | VM no local/Azure. | **Proteção do local**: Volume/share/folder/file; system-state/bare-metal<br/><br/> **Proteção de VM do Azure**: Volume/share/folder/file<br/><br/> Volumes com eliminação de duplicados suportados.
**Windows Server 2008 com SP2**<br/><br/> 64/32 bits | MABS v1, v2, v3 | Apenas v1 é suportada quando o MABS é implementado como uma máquina física no local ou VM do Hyper-V.<br/><br/> MABS v1, 2, 3 é suportada quando o MABS é implementado como uma VM de VMware.<br/><br/> Não é suportada para o MABS em execução numa VM do Azure | Volume/share/folder/file; system-state/bare-metal.
**Windows Storage Server 2008** | MABS v1, v2, v3 | MABS como no local físico servidor/Hyper-V VM | Não é suportada para o MABS em execução numa VM do Azure. | Volume/share/folder/file; system-state/bare-metal.
**SQL Server 2017** | MABS v3 | VM no local/Azure.| Criar cópias de segurança da base de dados do SQL Server.<br/><br/> Backup de cluster do SQL Server suportado.<br/><br/>Bases de dados armazenados em CSVs não suportados.
**SQL Server 2016 de Maio de 2016 com SP1** | MABS v3, v2 | VM no local/Azure.| Criar cópias de segurança da base de dados do SQL Server.<br/><br/> Backup de cluster do SQL Server suportado.<br/><br/>Bases de dados armazenados em CSVs não suportados.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2, v1 | VM no local/Azure.| Criar cópias de segurança da base de dados do SQL Server.<br/><br/> Backup de cluster do SQL Server suportado.<br/><br/>Bases de dados armazenados em CSVs não suportados.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2, V1 | No local. | Fazer cópias de segurança do servidor do Exchange autónomo, base de dados num Dag<br/><br/> Recupere a caixa de correio, bases de dados de caixa de correio num Dag.<br/><br/> ReFS não suportado.<br/><br/> Criar cópias de segurança os clusters de disco não partilhados.<br/><br/> Criar cópias de segurança do Exchange Server configurado para replicação contínua.
**SharePoint 2016**<br/><br/> **Sharepoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2, v1 | VM no local/Azure | Fazer backup de farm, o servidor de web de front-end.<br/><br/> Recupere a farm, base de dados, aplicação web, ficheiro ou item de lista, procura do SharePoint, servidor de web de front-end.<br/><br/> Não é possível fazer uma cópia de segurança de um farm com o AlwaysOn para as bases de dados de conteúdo.
**Hyper-V no Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (com SP1)** | MABS v3, v2, v1 | Local | **Agente do MABS num anfitrião Hyper-V**: Cópia de segurança de VMs completas e hospedagem de arquivos de dados. Fazer uma cópia de segurança de VMs com o armazenamento local, VMs no cluster com o armazenamento CSV, as VMs com armazenamento de servidor de ficheiros SMB.<br/><br/> **Agente MABS na VM do convidado**: Cópia de segurança de cargas de trabalho em execução na VM. CSVs.<br/><br/> **Recuperação**: VM, recuperação ao nível do item de VHD/volume/pastas/ficheiros.<br/><br/> **VMs do Linux**: Criar cópias de segurança quando o Hyper-V está em execução no Windows Server 2012 R2 e posteriores. Recuperação para VMs do Linux destina-se a toda a máquina.
**VMs de VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> V1 do MABS precisa update rollup 1) | Local | Fazer uma cópia de segurança de VMs de VMware no armazenamento de CSV, NFS e SAN.<br/><br/> Recupere VM inteira.<br/><br/> Cópia de segurança do Windows/Linux.<br/><br/> Recuperação ao nível do item de pasta/para VMs do Windows apenas ficheiros.<br/><br/> Os VMware vApps não são suportados.<br/><br/> Recuperação para VMs do Linux destina-se a toda a máquina.



## <a name="supported-backups-to-dpm"></a>Cópias de segurança suportadas para o DPM

A tabela seguinte resume o que pode ser uma cópia de segurança para o DPM de máquinas no local e VMs do Azure.



**Cópia de segurança** | **DPM** | **Detalhes**
--- | --- | --- 
**Windows 10, 8.1, 8, 7**<br/><br/>(32/64-bit) | Apenas no local.<br/><br/> Para fazer backup com o Windows 10 com o DPM 2012 R2 Recomendamos que instale [atualização 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Volume/share/folder/file<br/><br/> Volumes com eliminação de duplicados suportados<br/><br/> Volumes têm de ser, pelo menos, 1 GB e NTFS.
**Windows Server 2016 (Centro de dados, padrão, não o Nano)**<br/><br/> 64/32 bits | VM no local/Azure.<br/><br/> Apenas para o DPM 2016.| Volume/share/folder/file; system-state/bare-metal<br/><br/> Volumes com eliminação de duplicados suportados.
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bits | VM no local/Azure | **Proteção do local**: Volume/share/folder/file; system-state/bare-metal<br/><br/> **Proteção de VM do Azure**: Volume/share/folder/file<br/><br/> Volumes com eliminação de duplicados suportados com o DPM 2012 R2 e posteriores.
**Windows Server 2012 com SP1 (Datacenter e Standard)**<br/><br/> 64/32 bits | VM no local/Azure. | **Proteção do local**: Volume/share/folder/file; system-state/bare-metal<br/><br/> **Proteção de VM do Azure**: Volume/share/folder/file<br/><br/> Volumes com eliminação de duplicados suportados com o DPM 2012 R2 e posteriores.
**Windows Server 2012 (Datacenter e Standard)**<br/><br/> 64/32 bits |  VM no local/Azure. | **Proteção do local**: Volume/share/folder/file; system-state/bare-metal<br/><br/> **Proteção de VM do Azure**: Volume/share/folder/file<br/><br/> Volumes com eliminação de duplicados suportados com o DPM 2012 R2 e posteriores.
**Windows 2008 R2 com SP1 (Standard e Enterprise)**<br/><br/> 64/32 bits | VM no local/Azure <br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) tem de ser instalado. |   **Proteção do local**: Volume/share/folder/file; system-state/bare-metal<br/><br/> **Proteção de VM do Azure**: Volume/share/folder/file.
**Windows 2008 R2 (Standard e Enterprise)**<br/><br/> 64/32 bits | No local.<br/><br/> Não é possível instalar o DPM como uma VM de VMware.<br/><br/> O DPM for executado numa VM do Azure não é suportado. | **Proteção do local**: Volume/share/folder/file; system-state/bare-metal
**Windows Server 2008 com SP2**<br/><br/> 64/32 bits | Apenas no local.<br/><br/> O DPM é suportado quando em execução como uma VM de VMware. Não é suportada em execução como um servidor físico ou VM do Hyper-V. | Volume/share/folder/file; system-state/bare-metal.
**Windows Storage Server 2008** | DPM no local em execução como um servidor físico ou VM do Hyper-V. | Volume/share/folder/file; system-state/bare-metal.
**SQL Server 2017** | DPM SAC; O DPM 2016 com o Update Rollup 5 ou posterior<br/><br/> VM no local/Azure.| Criar cópias de segurança da base de dados do SQL Server.<br/><br/> Backup de cluster do SQL Server suportado.<br/><br/>Bases de dados armazenados em CSVs não suportados.
**SQL Server 2016 com SP1** | Não é suportada para o DPM 2012 R2; Suportado para DPM SAC, o DPM 2016, executar o Update Rollup 4 ou posterior.<br/><br/> VM no local/Azure.| Criar cópias de segurança da base de dados do SQL Server.<br/><br/> Backup de cluster do SQL Server suportado.<br/><br/>Bases de dados armazenados em CSVs não suportados.
**SQL Server 2016** | Não é suportada para o DPM 2012 R2. Suportado para DPM SAC, o DPM 2016 a partir do Update Rollup 2.<br/><br/> VM no local/Azure.| Criar cópias de segurança da base de dados do SQL Server.<br/><br/> Backup de cluster do SQL Server suportado.<br/><br/>Bases de dados armazenados em CSVs não suportados.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014 com o DPM 2012 R2 com Update Rollup 4 e posteriores.<br/><br/> VM no local/Azure.| Criar cópias de segurança da base de dados do SQL Server.<br/><br/> Backup de cluster do SQL Server suportado.<br/><br/>Bases de dados armazenados em CSVs não suportados.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Para o Exchange 2016, o DPM 2012 R2 tem Update Rollup 9 ou posterior.<br/><br/> Local | Fazer cópias de segurança do servidor do Exchange autónomo, base de dados num Dag<br/><br/> Recupere a caixa de correio, bases de dados de caixa de correio num Dag.<br/><br/> ReFS não suportado.<br/><br/> Criar cópias de segurança os clusters de disco não partilhados.<br/><br/> Criar cópias de segurança do Exchange Server configurado para replicação contínua.
**SharePoint 2016**<br/><br/> **Sharepoint 2013**<br/><br/> **SharePoint 2010** | No DPM 2016 e posteriores do SharePoint 2016.<br/><br/>VM no local/Azure | Fazer backup de farm, o servidor de web de front-end.<br/><br/> Recupere a farm, base de dados, aplicação web, ficheiro ou item de lista, procura do SharePoint, servidor de web de front-end.<br/><br/> Não é possível fazer uma cópia de segurança de um farm com o AlwaysOn para as bases de dados de conteúdo.
**Hyper-V no Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (com SP1)** | Hyper-V suportadas para o DPM 2016 e posteriores de 2016.<br/><br/> Local | **Agente do MABS num anfitrião Hyper-V**: Cópia de segurança de VMs completas e hospedagem de arquivos de dados. Fazer uma cópia de segurança de VMs com o armazenamento local, VMs no cluster com o armazenamento CSV, as VMs com armazenamento de servidor de ficheiros SMB.<br/><br/> **Agente MABS na VM do convidado**: Cópia de segurança de cargas de trabalho em execução na VM. CSVs.<br/><br/> **Recuperação**: VM, recuperação ao nível do item de VHD/volume/pastas/ficheiros.<br/><br/> **VMs do Linux**: Criar cópias de segurança quando o Hyper-V está em execução no Windows Server 2012 R2 e posteriores. Recuperação para VMs do Linux destina-se a toda a máquina.
**VMs de VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> O DPM 2012 R2 necessita de System Center Update Rollup 1) | Local | Fazer uma cópia de segurança de VMs de VMware no armazenamento de CSV, NFS e SAN.<br/><br/> Recupere VM inteira.<br/><br/> Cópia de segurança do Windows/Linux.<br/><br/> Recuperação ao nível do item de pasta/para VMs do Windows apenas ficheiros.<br/><br/> Os VMware vApps não são suportados.<br/><br/> Recuperação para VMs do Linux destina-se a toda a máquina.


- Tenha em atenção que as cargas de trabalho em cluster, cópias de segurança no DPM/MABS devem no mesmo domínio que o DPM/MABS, ou num domínio fidedigno/filho. 
- Pode utilizar a autenticação de NTLM/certificados para criar cópias de segurança em grupos de trabalho ou domínios não fidedignos.



## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Revisão](backup-support-matrix-mars-agent.md) o que é suportado para o agente de MARS.
- [Configurar](backup-azure-microsoft-azure-backup.md) um servidor do MABS.
- [Configurar o DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180)
