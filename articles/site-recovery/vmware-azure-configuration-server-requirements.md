---
title: Requisitos do servidor de configuração para a recuperação de desastres do VMware no Azure com Azure Site Recovery | Microsoft Docs
description: Este artigo descreve o suporte e os requisitos ao implantar o servidor de configuração para a recuperação de desastres do VMware no Azure com o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 86fa817128dc89eb97bee18f4f8a6de1f650c265
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814299"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Requisitos do servidor de configuração para a recuperação de desastres do VMware para o Azure

Você implanta um servidor de configuração local ao usar [Azure site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos no Azure.

- O servidor de configuração coordena as comunicações entre o VMware local e o Azure. Ele também gerencia a replicação de dados.
- [Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

## <a name="configuration-server-deployment"></a>Implantação do servidor de configuração

Para a recuperação de desastre de VMs VMware no Azure, você implanta o servidor de configuração como uma VM VMware.

- Site Recovery fornece um modelo OVA que você baixa do portal do Azure e importa para vCenter Server para configurar a VM do servidor de configuração.
- Quando você implanta o servidor de configuração usando o modelo OVA, a VM está em conformidade automaticamente com os requisitos listados neste artigo.
- É altamente recomendável que você configure o servidor de configuração usando o modelo OVA. No entanto, se você estiver configurando a recuperação de desastre para VMs VMware e não puder usar o modelo OVA, poderá implantar o servidor de configuração usando [essas instruções fornecidas](physical-azure-set-up-source.md).
- Se você estiver implantando o servidor de configuração para recuperação de desastre de máquinas físicas locais no Azure, siga as instruções neste [artigo](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Requisitos de hardware

**Componente** | **Requisito** 
--- | ---
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | 3, incluindo o disco do sistema operacional, o disco de cache do servidor de processo e a unidade de retenção para failback 
Espaço livre em disco (cache do servidor de processo) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB

## <a name="software-requirements"></a>Requisitos de software

**Componente** | **Requisito** 
--- | ---
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Região do sistema operativo | Inglês (en-us)
Funções do Windows Server | Não habilite estas funções: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V 
Políticas de grupo | Não habilite essas políticas de Grupo: <br> -Impedir o acesso ao prompt de comando. <br> -Impedir o acesso às ferramentas de edição do registro. <br> -Lógica de confiança para anexos de arquivo. <br> -Ative a execução do script. <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Nenhum site da Web padrão preexistente <br> -Nenhum site/aplicativo pré-existente escutando na porta 443 <br>-Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Habilitar configuração de [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 

## <a name="network-requirements"></a>Requisitos da rede

**Componente** | **Requisito** 
--- | --- 
Tipo de endereço IP | Estático 
Acesso à Internet | O servidor precisa de acesso a essas URLs (diretamente ou via proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> -https:\//Management.Azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> O OVF também precisa de acesso às seguintes URLs: <br> -https:\//login.microsoftonline.com <br> -https:\//Secure.aadcdn.microsoftonline-p.com <br> -https:\//login.Live.com  <br> -https:\//auth.gfx.ms <br> -https:\//Graph.Windows.net <br> -https:\//login.Windows.net <br> - https:\//www.live.com <br> -https:\//www.Microsoft.com <br> -https:\//dev.mysql.com/Get/downloads/MySQLInstaller/MySQL-Installer-Community-5.7.20.0.msi 
Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados) 
Tipo de NIC | VMXNET3 (se o servidor de configuração for uma VM VMware)

## <a name="required-software"></a>Software necessário

**Componente** | **Requisito** 
--- | ---
VMware vSphere PowerCLI | A [versão 6,0 do PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalada se o servidor de configuração estiver em execução em uma VM do VMware.
MYSQL | O MySQL deve ser instalado. Você pode instalar manualmente ou Site Recovery pode instalá-lo.

## <a name="sizing-and-capacity-requirements"></a>Requisitos de dimensionamento e capacidade

A tabela a seguir resume os requisitos de capacidade para o servidor de configuração. Se você estiver replicando várias VMs VMware, examine as considerações de [planejamento de capacidade](site-recovery-plan-capacity-vmware.md)e execute a ferramenta de [planejador de implantações do Azure site Recovery](site-recovery-deployment-planner.md) para replicação do VMware. Leia 

**Componente** | **Requisito** 
--- | ---

| **CPU** | **Memória** | **Disco de cache** | **Taxa de alteração de dados** | **Máquinas replicadas** |
| --- | --- | --- | --- | --- |
| 8 vCPUs<br/><br/> 2 soquetes * 4 núcleos \@ 2,5 GHz | 16 GB | 300 GB | 500 GB ou menos | Les que 100 computadores |
| 12 vCPUs<br/><br/> 2 Socks * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 a 150 computadores |
| 16 vCPUs<br/><br/> 2 Socks * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 computadores | 



## <a name="next-steps"></a>Passos Seguintes
Configure a recuperação de desastre de [VMs do VMware](vmware-azure-tutorial.md) no Azure.
