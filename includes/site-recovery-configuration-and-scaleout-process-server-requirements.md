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
ms.openlocfilehash: 1aaec104e9130eeef723c6505e04e3317271566b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234219"
---
**Requisitos de configuração e servidor de processos**


## <a name="hardware-requirements"></a>Requisitos de hardware

**Componente** | **Requisito** 
--- | ---
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | 3, incluindo o disco OS, o disco de cache do servidor de processo e a unidade de retenção para o failback 
Espaço de disco gratuito (cache do servidor de processos) | 600 GB
Espaço de disco gratuito (disco de retenção) | 600 GB
 | 

## <a name="software-requirements"></a>Requisitos de software

**Componente** | **Requisito** 
--- | ---
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Região do sistema operativo | Inglês (en-*)
Funções do Windows Server | Não ative estes papéis: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V 
Políticas de grupo | Não permita estas políticas de grupo: <br> - Impedir o acesso ao pedido de comando. <br> - Impedir o acesso às ferramentas de edição de registo. <br> - Lógica de confiança para anexos de ficheiros. <br> - Ligue a execução do guião. <br> [Mais informações](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nenhum website preexistente <br> - Nenhum site/aplicação pré-existente ouvindo na porta 443 <br>- Ativar [a autenticação anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Ativar a definição [de FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
FIPS (Normas Federais de Processamento de Informação) | Não ativar o modo FIPS
|

## <a name="network-requirements"></a>Requisitos da rede

**Componente** | **Requisito** 
--- | --- 
Tipo de endereço IP | Estático 
Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados) 
Tipo NIC | VMXNET3 (se o servidor de configuração for um VMware VM)
 |
**Acesso à Internet** (o servidor precisa de acesso aos seguintes URLs, diretamente ou via procuração):|
\*.backup.windowsazure.com | Utilizado para transferência e coordenação de dados replicados
\*.store.core.windows.net | Utilizado para transferência e coordenação de dados replicados
\*.blob.core.windows.net | Usado para aceder à conta de armazenamento que armazena dados replicados
\*.hypervrecoverymanager.windowsazure.com | Utilizado para operações de gestão de replicação e coordenação
https:\//management.azure.com | Utilizado para operações de gestão de replicação e coordenação 
*.services.visualstudio.com | Utilizado para efeitos de telemetria (opcional)
time.nist.gov | Usado para verificar a sincronização do tempo entre o sistema e o tempo global
time.windows.com | Usado para verificar a sincronização do tempo entre o sistema e o tempo global
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | A configuração ovf precisa de acesso a estes URLs. São usados para controlo de acesso e gestão de identidade pela Azure Ative Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Para completar o download do MySQL. </br> Em algumas regiões, o download pode ser redirecionado para o URL da CDN. Certifique-se de que o URL do CDN também está na lista branca, se necessário.
|

## <a name="required-software"></a>Software necessário

**Componente** | **Requisito** 
--- | ---
VMware vSphere PowerCLI | [A versão PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalada se o Servidor de Configuração estiver a funcionar num VMware VM.
MYSQL | O MySQL deve ser instalado. Pode instalar manualmente, ou a Recuperação do Site pode instalá-la. (Consulte [as definições de configuração](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) para mais informações)
|

## <a name="sizing-and-capacity-requirements"></a>Dimensionamento e requisitos de capacidade

A tabela que se segue resume os requisitos de capacidade para o servidor de configuração. Se estiver a replicar vários VMs VMware, reveja as [considerações de planeamento](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) de capacidade e execute a ferramenta de [planificador](../articles/site-recovery/site-recovery-deployment-planner.md)de implantação de implementação de locais azure .


**CPU** | **Memória** | **Disco cache** | **Taxa de alteração de dados** | **Máquinas replicadas**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 tomadas * \@ 4 núcleos 2,5 GHz | 16 GB | 300 GB | 500 GB ou menos | < 100 máquinas
12 vCPUs<br/><br/> 2 meias \@ * 6 núcleos 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 a 150 máquinas
16 vCPUs<br/><br/> 2 meias \@ * 8 núcleos 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 máquinas
|

