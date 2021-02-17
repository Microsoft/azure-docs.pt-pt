---
title: incluir ficheiro
description: incluir ficheiro
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 07/08/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 61aa5ffcbab493109371067b1eb9d199a29cb852
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551392"
---
**Requisitos de configuração e servidor de processo**


## <a name="hardware-requirements"></a>Requisitos de hardware

**Componente** | **Requisito** 
--- | ---
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | 3, incluindo o disco de so, o disco de cache do servidor de processo e a unidade de retenção para falha 
Espaço livre em disco (cache do servidor de processo) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
 | 

## <a name="software-requirements"></a>Requisitos de software

**Componente** | **Requisito** 
--- | ---
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Região do sistema operativo | Inglês (en-*)
Funções do Windows Server | Não ative estes papéis: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V 
Políticas de grupo | Não ative estas políticas de grupo: <br> - Impedir o acesso à ordem de comando. <br> - Impedir o acesso a ferramentas de edição de registo. <br> - Lógica de confiança para anexos de ficheiros. <br> - Ligue a execução do guião. <br> [Saiba mais](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Nenhum website pre-existente <br> - Nenhum website/aplicação pré-existente na porta 443 <br>- Ativar a  [autenticação anónima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Ativar a definição [fastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 
FIPS (Normas Federais de Processamento de Informação) | Não ative o modo FIPS
|

## <a name="network-requirements"></a>Requisitos de rede

**Componente** | **Requisito** 
--- | --- 
Tipo de endereço IP | Estático 
Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados) 
Tipo NIC | VMXNET3 (se o servidor de configuração for um VMware VM)
 |
**Acesso**  à Internet (o servidor precisa de acesso aos urLs seguintes, diretamente ou via procuração):|
\*.backup.windowsazure.com | Utilizado para transferência e coordenação de dados replicados
\*.blob.core.windows.net | Usado para aceder à conta de armazenamento que armazena dados replicados. Pode fornecer o URL específico da sua conta de armazenamento de cache.
\*.hypervrecoverymanager.windowsazure.com | Utilizado para operações de gestão e coordenação de replicação
https:\//login.microsoftonline.com | Utilizado para operações de gestão e coordenação de replicação 
time.nist.gov | Usado para verificar a sincronização do tempo entre o sistema e o tempo global
time.windows.com | Usado para verificar a sincronização do tempo entre o sistema e o tempo global
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https: \/ /login.live.com </li><li> https: \/ /graph.windows.net </li><li> https:\//login.windows.net </li><li> *.services.visualstudio.com (Opcional) </li><li> https: \/ /www.live.com </li><li> https: \/ /www.microsoft.com </li></ul> | A configuração da OVF precisa de acesso a estes URLs adicionais. São usados para controlo de acesso e gestão de identidade pela Azure Ative Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Para concluir o download do MySQL. </br> Em algumas regiões, o download pode ser redirecionado para o URL CDN. Certifique-se de que o URL CDN também está aprovado, se necessário.
|

> [!NOTE]
> Se tiver [ligações privadas com](../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md) o cofre de recuperação do local, não necessita de qualquer acesso adicional à Internet para o Servidor de Configuração. Uma exceção a isso é que ao configurar a máquina CS utilizando o modelo OVA, você precisará de acesso a urls para além do acesso de link privado - https://management.azure.com e https://www.live.com https://www.microsoft.com . Se não pretender permitir o acesso a estes URLs, por favor, erre o CS utilizando o Instalador Unificado.

## <a name="required-software"></a>Software necessário

**Componente** | **Requisito** 
--- | ---
VMware vSphere PowerCLI | Não é necessário para versões 9.14 e superior
MYSQL | O MySQL deve ser instalado. Pode instalar manualmente ou a Recuperação do Local pode instalá-la. (Consulte as [definições de configuração](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) para obter mais informações)
|

## <a name="sizing-and-capacity-requirements"></a>Requisitos de dimensionamento e capacidade

A tabela seguinte resume os requisitos de capacidade para o servidor de configuração. Se estiver a replicar vários VMware VMs, reveja as [considerações de planeamento de capacidade e](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) execute a ferramenta [Azure Site Recovery Planner](../articles/site-recovery/site-recovery-deployment-planner.md).


**CPU** | **Memória** | **Disco de cache** | **Taxa de alteração de dados** | **Máquinas replicadas**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 tomadas * 4 núcleos \@ 2,5 GHz | 16 GB | 300 GB | 500 GB ou menos | < 100 máquinas
12 vCPUs<br/><br/> 2 meias * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 a 150 máquinas
16 vCPUs<br/><br/> 2 meias * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 máquinas
|