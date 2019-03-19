---
title: Requisitos de servidor de configuração para a recuperação de desastre do VMware para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve requisitos e suporte ao implementar o servidor de configuração para a recuperação de desastre do VMware para o Azure com o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 92eef714f651ef0ce4e58fcfbb8fad75a38c55c8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121337"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Requisitos de servidor de configuração para a recuperação de desastre do VMware para o Azure

Implementar um servidor de configuração no local quando utiliza [do Azure Site Recovery](site-recovery-overview.md) para recuperação após desastre de VMs de VMware e servidores físicos para o Azure.

- As comunicações de coordenadas do servidor de configuração entre locais VMware e o Azure. Ele também gerencia a replicação de dados.
- As comunicações de coordenadas do servidor de configuração entre locais VMware e o Azure. Ele também gerencia a replicação de dados.
- [Saiba mais](vmware-azure-architecture.md) sobre os componentes de servidor de configuração e os processos.

## <a name="configuration-server-deployment"></a>Implementação de servidor de configuração

Para a recuperação de desastre de VMs de VMware para o Azure, implementar o servidor de configuração como uma VM de VMware.

- Recuperação de sites fornece um modelo de OVA que transferir a partir do portal do Azure e importar para o vCenter Server para configurar a VM do servidor de configuração.
- Ao implementar o servidor de configuração com o modelo de OVA, a VM automaticamente cumpre os requisitos listados neste artigo.
- Recomendamos vivamente que configure o servidor de configuração com o modelo de OVA. No entanto, se estiver configurando a recuperação após desastre para VMs de VMware e não é possível utilizar o modelo de OVA, pode implementar o servidor de configuração utilizando [estas instruções fornecidas](physical-azure-set-up-source.md).
- Se estiver a implementar o servidor de configuração para recuperação após desastre de computadores físicos no local para o Azure, siga as instruções em [este artigo](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Requisitos de hardware

**Componente** | **Requisito** 
--- | ---
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | 3, incluindo o disco de SO, disco de cache do servidor de processo e unidade de retenção para a reativação pós-falha 
Espaço livre em disco (cache do servidor de processo) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB

## <a name="software-requirements"></a>Requisitos de software

**Componente** | **Requisito** 
--- | ---
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Região do sistema operativo | Inglês (en-us)
Funções do Windows Server | Não ative estas funções: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V 
Políticas de grupo | Não ative estas políticas de grupo: <br> -Impedi o acesso ao prompt de comando. <br> -Impedi o acesso a ferramentas de edição do registo. <br> -Lógica para anexos de ficheiros de fidedignidade. <br> – Ative a execução do Script. <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Sem Web site predefinido do preexistente <br> -Sem preexistente/aplicação do Web site escute na porta 443 <br>-Permitir [autenticação anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Ativar [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) definição 

## <a name="network-requirements"></a>Requisitos da rede

**Componente** | **Requisito** 
--- | --- 
Tipo de endereço IP | Estático 
Acesso à Internet | O servidor precisa de acesso a estes URLs (diretamente ou através do proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF também precisa de acesso para os seguintes URLs: <br> -https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> -https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> -https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados) 
Tipo NIC | VMXNET3 (se o servidor de configuração é uma VM de VMware)

## <a name="required-software"></a>Software necessário

**Componente** | **Requisito** 
--- | ---
VMware vSphere PowerCLI | [Versão 6.0 do PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalada se o servidor de configuração está em execução numa VM do VMware.
MYSQL | Deve ser instalado o MySQL. Pode instalar manualmente ou recuperação de sites pode instalá-lo.

## <a name="sizing-and-capacity-requirements"></a>Dimensionamento e os requisitos de capacidade

A tabela seguinte resume os requisitos de capacidade para o servidor de configuração. Se estiver a replicar várias VMs de VMware, deve rever o [considerações de planeamento de capacidade](site-recovery-plan-capacity-vmware.md)e execute o [do Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) ferramenta para replication.read de VMWare 

**Componente** | **Requisito** 
--- | ---

| **CPU** | **Memória** | **Disco de cache** | **Taxa de alteração de dados** | **Máquinas replicadas** |
| --- | --- | --- | --- | --- |
| 8 vCPUs<br/><br/> 2 sockets * 4 núcleos \@ 2,5 GHz | 16 GB | 300 GB | 500 GB ou menos | Les que 100 máquinas |
| 12 vCPUs<br/><br/> 2 socks * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB – 1 TB | máquinas de 100 a 150 |
| 16 vCPUs<br/><br/> 2 socks * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 200 máquinas | 



## <a name="next-steps"></a>Passos Seguintes
Configurar a recuperação após desastre de [VMs de VMware](vmware-azure-tutorial.md) para o Azure.
