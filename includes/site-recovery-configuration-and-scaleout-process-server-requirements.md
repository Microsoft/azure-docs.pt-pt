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
ms.openlocfilehash: 371cbcc50b574f95e8d9ba4efe79058b2b25a8ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58016676"
---
**Requisitos do servidor de configuração/processos**

**Componente** | **Requisito** 
--- | ---
**DEFINIÇÕES DE HARDWARE** | 
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | 3, incluindo o disco de SO, disco de cache do servidor de processo e unidade de retenção para a reativação pós-falha 
Espaço livre em disco (cache do servidor de processo) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
 | 
**DEFINIÇÕES DE SOFTWARE** | 
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Região do sistema operativo | Inglês (en-us)
Funções do Windows Server | Não ative estas funções: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V 
Políticas de grupo | Não ative estas políticas de grupo: <br> -Impedi o acesso ao prompt de comando. <br> -Impedi o acesso a ferramentas de edição do registo. <br> -Lógica para anexos de ficheiros de fidedignidade. <br> – Ative a execução do Script. <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Sem Web site predefinido do já existente <br> -Sem preexistente/aplicação do Web site escute na porta 443 <br>-Permitir [autenticação anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Ativar [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) definição 
| 
**DEFINIÇÕES DE REDE** | 
Tipo de endereço IP | Estático 
Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados) 
Tipo NIC | VMXNET3 (se o servidor de configuração é uma VM de VMware)
 |
**Acesso à Internet** (o servidor tem acesso aos seguintes URLs - diretamente ou através de proxy):|
\*.backup.windowsazure.com | Utilizado para transferência de dados replicados e coordenação
\*.store.core.windows.net | Utilizado para transferência de dados replicados e coordenação
\*.blob.core.windows.net | Utilizado para aceder à conta de armazenamento que armazena dados replicados
\*.hypervrecoverymanager.windowsazure.com | Utilizado para operações de gestão de replicação e coordenação
https:\//management.azure.com | Utilizado para operações de gestão de replicação e coordenação 
*.services.visualstudio.com | Utilizada para fins de telemetria (é opcional)
time.nist.gov | Utilizados para verificar a sincronização da hora entre o sistema e a hora global.
time.windows.com | Utilizados para verificar a sincronização da hora entre o sistema e a hora global.
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF configurar precisa de aceder a estes URLs. São utilizados para gestão de identidades e de controlo de acesso ao Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Para concluir o download do MySQL
|
**SOFTWARE PARA INSTALAR** | 
VMware vSphere PowerCLI | [Versão 6.0 do PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalada se o servidor de configuração está em execução numa VM do VMware.
MYSQL | Deve ser instalado o MySQL. Pode instalar manualmente ou recuperação de sites pode instalá-lo. (Consulte a [configurar as definições de](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) para obter mais informações)

**Requisitos de dimensionamento do servidor de configuração/processos**

**CPU** | **Memória** | **Disco de cache** | **Taxa de alteração de dados** | **Máquinas replicadas**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 sockets * 4 núcleos \@ 2,5 GHz | 16GB | 300 GB | 500 GB ou menos | < 100 máquinas
12 vCPUs<br/><br/> 2 socks * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB – 1 TB | máquinas de 100 a 150
16 vCPUs<br/><br/> 2 socks * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 máquinas

