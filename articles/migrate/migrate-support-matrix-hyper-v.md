---
title: Matriz de suporte do Azure Migrate para avaliação do Hyper-V e migração
description: Resume as definições e as limitações de avaliação do Hyper-V e de migração com o serviço Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2019
ms.author: raynew
ms.openlocfilehash: f6edbe19429b38d68aea1f1ecfe426c9b2d194d0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811352"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Matriz de suporte para a migração e de avaliação do Hyper-V

Pode utilizar o [serviço Azure Migrate](migrate-overview.md) para avaliar e migrar as máquinas para a cloud do Microsoft Azure. Este artigo resume as definições de suporte e limitações para avaliar e migrar VMs de Hyper-V no local.



## <a name="hyper-v-scenarios"></a>Cenários de Hyper-V

A tabela resume os cenários suportados para VMs de Hyper-V.

**Implementação** | **Detalhes*** 
--- | --- 
**Avaliar as VMs de Hyper-V no local** | [Configurar](tutorial-prepare-hyper-v.md) sua avaliação primeiro.<br/><br/> [Executar](scale-hyper-v-assessment.md) uma avaliação de grande escala.
**Migrar VMs de Hyper-V para o Azure** | [Experimentar o](tutorial-migrate-hyper-v.md) migração para o Azure.

    

## <a name="azure-migrate-projects"></a>Projetos de migração do Azure

**Suporte** | **Detalhes**
--- | ---
Permissões do Azure | Precisa de permissões de proprietário ou contribuinte na subscrição para criar um projeto do Azure Migrate.
VMs Hyper-V | Avalie até 10 000 VMs de Hyper-V num único projeto.

Um projeto pode incluir as VMs de VMware e VMs de Hyper-V, até aos limites de avaliação.


## <a name="assessment-hyper-v-host-requirements"></a>Requisitos de anfitrião de avaliação de Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implementação do anfitrião**       | O anfitrião de Hyper-V pode ser autónomo ou implementado num cluster. |
| **Permissões**           | Precisa de permissões de administrador no anfitrião Hyper-V. |
| **Sistema operativo anfitrião** | Windows Server 2016 ou Windows Server 2012 R2.<br/> Não é possível avaliar VMs localizadas nos anfitriões de Hyper-V com o Windows Server 2019. |
| **Comunicação remota do PowerShell**   | Tem de estar ativada em cada anfitrião. |
| **Réplica do Hyper-V**       | Se usar a réplica do Hyper-V (ou se tiver várias VMs com os mesmos identificadores VM) e descubra ambas as originais e replicadas VMs com o Azure Migrate, a avaliação gerada pelo Azure Migrate poderá não ser precisa. |


## <a name="assessment-hyper-v-vm-requirements"></a>Requisitos de VM de avaliação de Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Todos os [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) sistemas operativos que são suportados pelo Azure. |
| **Permissões**           | Precisa de permissões de administrador em cada VM de Hyper-V que pretende avaliar. |
| **Serviços de integração**       | [Serviços de integração do Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) deve estar em execução em VMs que avaliar, para capturar informações do sistema operativo. |
| **Alterações necessárias para o Azure** | Algumas VMs podem exigir alterações para que eles podem ser executados no Azure. O Azure Migrate torna estas alterações automaticamente para os seguintes sistemas operativos:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7,8<br/><br/> Para outros sistemas operacionais, precisa fazer ajustes manualmente antes da migração. Os artigos relevantes contém instruções sobre como fazer isso. |
| **Arranque do Linux**                 | Se for /boot numa partição dedicada, devem residir no disco do SO e não serão distribuído por vários discos.<br/> Se /boot faz parte da partição de raiz (/), em seguida, a partição '/' deve ser no disco do SO e não abrangem outros discos. |
| **Arranque UEFI**                  | As VMs com arranque UEFI não são suportadas para migração. |
| **Discos/volumes encriptados**    | As VMs com discos/volumes encriptados não são suportadas para migração. |
| **Discos RDM/pass-through**      | Se as VMs têm discos RDM ou pass-through, estes discos não ser replicados para o Azure. |
| **NFS**                        | Volumes NFS montados como volumes nas VMs não ser replicadas. |
| **Disco de destino**                | Avaliações do Azure Migrate recomenda a migração para VMs do Azure com discos geridos apenas. |


## <a name="assessment-appliance-requirements"></a>Requisitos de aplicação de avaliação

Para avaliação, o Azure Migrate executa uma aplicação simples para detetar VMs de Hyper-V e enviar dados de metadados e o desempenho da VM para o Azure Migrate. A aplicação é executada numa VM do Hyper-V e configurar com um VHD de Hyper-V comprimido que transferir a partir do portal do Azure. A tabela seguinte resume os requisitos da aplicação.

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Projeto de migração do Azure**  |  Uma aplicação pode ser associada um único projeto.<br/> Pode detetar até 5000 VMs de Hyper-V com uma única aplicação.
| **Limitações de Hyper-V**    |  Implementar a aplicação como uma VM de Hyper-V.<br/> A aplicação da VM fornecido é a VM de Hyper-V versão 5.0.<br/> O anfitrião VM deve ser a executar o Windows Server 2012 R2 ou posterior.<br/> Ele tem espaço suficiente para alocar 16 GB de RAM, 4 processadores virtuais, e 1 externo mudar para a aplicação da VM.<br/> Aplicação requer um endereço IP estático ou dinâmico e o acesso à internet.
| **Aplicação de Hyper-V**      |  A aplicação é configurada como uma VM de Hyper-V.<br/> O VHD fornecido para download é a VM de Hyper-V versão 5.0.
| **Anfitrião**                   | O anfitrião VM com a aplicação da VM deve ser a executar o Windows Server 2012 R2 ou posterior.<br/> Ele tem espaço suficiente para alocar 16 GB RAM, 4 processadores virtuais e um comutador externo para a aplicação da VM.<br/> Aplicação requer um endereço IP estático ou dinâmico e o acesso à internet. |
| **Suporte para a migração**      | Para iniciar a replicação de máquinas, serviço do Gateway de migração sobre a aplicação tem de ser 1.18.7141.12919 ou posterior. Inicie sessão na aplicação web da aplicação para verificar a versão. |

## <a name="assessment-appliance-url-access"></a>Acesso de URL da aplicação de avaliação

Para avaliar as VMs, a aplicação do Azure Migrate necessita de conectividade de internet.

- Ao implementar a aplicação, o Azure Migrate faz uma verificação de conectividade para os URLs resumidos na tabela abaixo.
- Se estiver a utilizar um firewall.proxy baseado em URL, permita o acesso aos URLs na tabela, certificar-se de que o proxy resolve quaisquer registos CNAME receberam ao pesquisar os URLs.
- Se tiver um proxy de interceção, precisará de importar o certificado de servidor do servidor proxy para a aplicação. 

    
**URL** | **Detalhes**  
--- | --- 
*.portal.azure.com | Navegação no portal do Azure
\*.windows.net | Inicie sessão na sua subscrição do Azure
*.microsoftonline.com | Aplicativos de criação do Azure Active Directory para a aplicação para comunicações de serviço.
management.azure.com | Aplicativos de criação do Azure Active Directory para a aplicação para comunicações de serviço.
dc.services.visualstudio.com | Início de sessão e monitorização 
*.vault.azure.net | Gerir segredos no Cofre de chaves do Azure durante a comunicação entre a aplicação e o serviço.


## <a name="assessment-port-requirements"></a>Requisitos de portas de avaliação

A tabela seguinte resume os requisitos de portas para avaliação.

**dispositivo** | **ligação**
--- | --- 
**Aplicação** | Ligações de entrada na porta TCP 3389 para permitir ligações de ambiente de trabalho remotas para a aplicação.<br/> Ligações na porta 44368 para aceder remotamente a aplicação de gestão da aplicação através do URL de entrada: https://<appliance-ip-or-name>:44368<br/> Ligações de saída na porta 443 para enviar metadados de deteção e de desempenho para o Azure Migrate.
**Anfitrião/cluster de Hyper-V** | Ligações nas portas de WinRM 5985 (HTTP) e 5986 (HTTPS) para extrair os metadados de configuração e o desempenho das VMs Hyper-V através de uma sessão do modelo CIM (Common Information Model) de entrada.

## <a name="migration-hyper-v-host-requirements"></a>Requisitos de migração-Hyper-V anfitrião

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implementação do anfitrião**       | O anfitrião de Hyper-V pode ser autónomo ou implementado num cluster. |
| **Permissões**           | Precisa de permissões de administrador no anfitrião Hyper-V. |
| **Sistema operativo anfitrião** | 2019 do Windows Server, Windows Server 2016 ou Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Requisitos de VM de migração-Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Todos os [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) sistemas operativos que são suportados pelo Azure. |
| **Permissões**           | Precisa de permissões de administrador em cada VM de Hyper-V que pretende avaliar. |
| **Serviços de integração**       | [Serviços de integração do Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) deve estar em execução em VMs que avaliar, para capturar informações do sistema operativo. |
| **Alterações necessárias para o Azure** | Algumas VMs podem exigir alterações para que eles podem ser executados no Azure. O Azure Migrate torna estas alterações automaticamente para os seguintes sistemas operativos:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7,8<br/><br/> Para outros sistemas operacionais, precisa fazer ajustes manualmente antes da migração. Os artigos relevantes contém instruções sobre como fazer isso. |
| **Arranque do Linux**                 | Se for /boot numa partição dedicada, devem residir no disco do SO e não serão distribuído por vários discos.<br/> Se /boot faz parte da partição de raiz (/), em seguida, a partição '/' deve ser no disco do SO e não abrangem outros discos. |
| **Arranque UEFI**                  | As VMs com arranque UEFI não são suportadas para migração. |
| **Discos/volumes encriptados**    | As VMs com discos/volumes encriptados não são suportadas para migração. |
| **Discos RDM/pass-through**      | Se as VMs têm discos RDM ou pass-through, estes discos não ser replicados para o Azure. |
| **NFS**                        | Volumes NFS montados como volumes nas VMs não ser replicadas. |
| **Disco de destino**                | Pode migrar para as VMs do Azure com discos geridos apenas. |




## <a name="migration-hyper-v-host-url-access"></a>Acesso de URL de migração-Hyper-V anfitrião

A tabela seguinte resume os requisitos de acesso de URL para anfitriões Hyper-V.

**URL** | **Detalhes**  
--- | ---
login.microsoftonline.com | Acesso controlo e gestão de identidades com o Active Directory.
*.backup.windowsazure.com | Transferência de dados de replicação e coordenação.
*.hypervrecoverymanager.windowsazure.com | Ligar a URLs do serviço Azure Migrate.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.
dc.services.visualstudio.com | Carregar registos de aplicação para monitorizar interno.
time.windows.com | Verifica se a sincronização da hora entre o sistema e a hora global.

## <a name="migration-port-access"></a>Acesso de porta de migração

A tabela seguinte resume os requisitos de portas em anfitriões de Hyper-V e VMs para a migração de VM.

**dispositivo** | **ligação**
--- | --- 
Anfitriões/VMs de Hyper-V | Ligações de saída no HTTPS porta 443 para enviar dados de replicação de VM para o Azure Migrate.

  
## <a name="migration-vm-disk-support"></a>Suporte de disco da VM de migração 

**Suporte** | **Detalhes**
--- | ---
Discos migrados | As VMs só podem ser migradas para os managed disks (HHD standard, premium SSD) no Azure.
   

## <a name="next-steps"></a>Passos seguintes

[Preparar a avaliação da VM de Hyper-V](tutorial-prepare-hyper-v.md) para migração.




 
