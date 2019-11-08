---
title: incluir ficheiro
description: incluir ficheiro
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: d77269c1e965d5bca1e32b756ef26e2c694e5c81
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747830"
---
**Requisitos do servidor de configuração e processo**


## <a name="hardware-requirements"></a>Requisitos de hardware

**Componente** | **Requisito** 
--- | ---
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | 3, incluindo o disco do sistema operacional, o disco de cache do servidor de processo e a unidade de retenção para failback 
Espaço livre em disco (cache do servidor de processo) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
 | 

## <a name="software-requirements"></a>Requisitos de software

**Componente** | **Requisito** 
--- | ---
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Região do sistema operativo | Inglês (en-us)
Funções do Windows Server | Não habilite estas funções: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V 
Políticas de grupo | Não habilite essas políticas de Grupo: <br> -Impedir o acesso ao prompt de comando. <br> -Impedir o acesso às ferramentas de edição do registro. <br> -Lógica de confiança para anexos de arquivo. <br> -Ative a execução do script. <br> [Saber mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Nenhum site da Web padrão preexistente. <br> -Nenhum site ou aplicativo pré-existente escutando na porta 443. <br>-Habilitar a [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx). <br> – Habilite a configuração de [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) .
| 

## <a name="network-requirements"></a>Requisitos da rede

**Componente** | **Requisito** 
--- | --- 
Tipo de endereço IP | Estático 
Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados) 
Tipo de NIC | VMXNET3 (se o servidor de configuração for uma VM VMware)
 |
**Acesso à Internet** (o servidor precisa acessar as seguintes URLs, diretamente ou via proxy):|
\*.backup.windowsazure.com | Usado para transferência e coordenação de dados replicados
\*.store.core.windows.net | Usado para transferência e coordenação de dados replicados
\*.blob.core.windows.net | Usado para acessar a conta de armazenamento que armazena os dados replicados
\*.hypervrecoverymanager.windowsazure.com | Usado para operações de gerenciamento de replicação e coordenação
https:\//management.azure.com | Usado para operações de gerenciamento de replicação e coordenação 
*.services.visualstudio.com | Usado para fins de telemetria (opcional)
time.nist.gov | Usado para verificar a sincronização de horário entre o sistema e a hora global
time.windows.com | Usado para verificar a sincronização de horário entre o sistema e a hora global
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | A instalação do OVF precisa de acesso a essas URLs. Eles são usados para controle de acesso e gerenciamento de identidade por Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Para concluir o download do MySQL. </br> Em algumas regiões, o download pode ser redirecionado para a URL da CDN. Verifique se a URL da CDN também está na lista de permissões, se necessário.
|

## <a name="required-software"></a>Software necessário

**Componente** | **Requisito** 
--- | ---
VMware vSphere PowerCLI | A [versão 6,0 do PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalada se o servidor de configuração estiver em execução em uma VM do VMware.
MySQL | O MySQL deve ser instalado. Você pode instalar manualmente ou Azure Site Recovery pode instalá-lo. (Para obter mais informações, consulte [definir configurações](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings).)

## <a name="sizing-and-capacity-requirements"></a>Requisitos de dimensionamento e capacidade

A tabela a seguir resume os requisitos de capacidade para o servidor de configuração. Se você estiver replicando várias VMs VMware, examine as [considerações de planejamento de capacidade](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) e execute a [ferramenta de planejador de implantações do Azure site Recovery](../articles/site-recovery/site-recovery-deployment-planner.md).


**CPUs** | **Memória** | **Disco de cache** | **Taxa de alteração de dados** | **Máquinas replicadas**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 soquetes * 4 núcleos \@ 2,5 GHz | 16 GB | 300 GB | 500 GB ou menos | < 100 computadores
12 vCPUs<br/><br/> 2 Socks * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB a 1 TB | 100 a 150 computadores
16 vCPUs<br/><br/> 2 x Socks * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1 a 2 TB | 150 a 200 computadores

