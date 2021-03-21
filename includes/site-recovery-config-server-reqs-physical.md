---
title: incluir ficheiro
description: incluir ficheiro
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 3a8a7be6f437687a4de31ce8e0ac62588f64e2eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016913"
---
**Requisitos do servidor de configuração/processo para replicação do servidor físico**

**Componente** | **Requisito** 
--- | ---
**DEFINIÇÕES DE HARDWARE** | 
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | 3, incluindo o disco de so, o disco de cache do servidor de processo e a unidade de retenção para falha 
Espaço livre em disco (cache do servidor de processo) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
 | 
**DEFINIÇÕES DE SOFTWARE** | 
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Região do sistema operativo | Inglês (en-us)
Funções do Windows Server | Não ative estes papéis: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V 
Políticas de grupo | Não ative estas políticas de grupo: <br> - Impedir o acesso à ordem de comando. <br> - Impedir o acesso a ferramentas de edição de registo. <br> - Lógica de confiança para anexos de ficheiros. <br> - Ligue a execução do guião. <br> [Saiba mais](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Nenhum website preexisting preexisting preexisting <br> - Sem pré-existente website/aplicação escutando na porta 443 <br>- Ativar a  [autenticação anónima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Ativar a regulação [fastCGI.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
Tipo de endereço IP | Estático 
| 
**DEFINIÇÕES DE ACESSO** | 
MYSQL | O MySQL deve ser instalado no servidor de configuração. Pode instalar manualmente ou a Recuperação do Local pode instalá-la durante a sua implantação. Para a Recuperação do Local, verifique se a máquina pode chegar http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi .
URLs | O servidor de configuração precisa de acesso a estes URLs (direta ou via procuração):<br/><br/> Ad Azure: `login.microsoftonline.com` `login.microsoftonline.us` ; `*.accesscontrol.windows.net`<br/><br/> Transferência de dados de `*.backup.windowsazure.com` replicação: `*.backup.windowsazure.us`<br/><br/> Gestão de replicação: `*.hypervrecoverymanager.windowsazure.com` `*.hypervrecoverymanager.windowsazure.us` ; `https://management.azure.com` ; `*.services.visualstudio.com`<br/><br/> Acesso ao armazenamento: `*.blob.core.windows.net` ; `*.blob.core.usgovcloudapi.net`<br/><br/> Sincronização `time.nist.gov` temporal: `time.windows.com`<br/><br/> Telemetria (opcional): `dc.services.visualstudio.com`
Firewall | As regras de firewall baseadas em endereços IP devem permitir a comunicação aos URLs Azure. Para simplificar e limitar as gamas IP, recomendamos a utilização de filtragem de URL.<br/><br/>**Para iPs comerciais:**<br/><br/>- Permitir os intervalos IP do [Centro de Dados Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443).<br/><br/> - Permitir intervalos de endereços IP para os EUA Ocidentais (utilizados para controlo de acesso e gestão de identidade).<br/><br/> - Permitir gamas de endereços IP para a região Azure da sua subscrição, para suportar os URLs necessários para o Azure Ative Directory, backup, replicação e armazenamento.<br/><br/> **Para os IPs do governo:**<br/><br/> - Permitir que os intervalos IP do Centro de Dados do Governo Azure e a porta HTTPS (443).<br/><br/> - Permitir que os intervalos de endereços IP para todas as regiões gov dos EUA (Virginia, Texas, Arizona e Iowa), apoiem os URLs necessários para o Azure Ative Directory, backup, replicação e armazenamento.
Portas | Permitir 443 (Orquestração do canal de controlo)<br/><br/> Permitir 9443 (Transporte de dados) 


**Requisitos de dimensionamento de servidor de configuração/processo**

**CPU** | **Memória** | **Disco de cache** | **Taxa de alteração de dados** | **Máquinas replicadas**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 tomadas * 4 núcleos \@ 2,5 GHz | 16GB | 300 GB | 500 GB ou menos | < 100 máquinas
12 vCPUs<br/><br/> 2 meias * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 a 150 máquinas
16 vCPUs<br/><br/> 2 meias * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 máquinas