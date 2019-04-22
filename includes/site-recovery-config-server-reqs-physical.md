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
ms.openlocfilehash: afeae4af9b41bf434b26833a3bd927118a4697ae
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58887020"
---
**Requisitos do servidor de configuração/processos para a replicação de servidor físico**

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
IIS | -Sem Web site predefinido do preexistente <br> -Sem preexistente/aplicação do Web site escute na porta 443 <br>-Permitir [autenticação anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Ativar [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) definição.
Tipo de endereço IP | Estático 
| 
**DEFINIÇÕES DE ACESSO** | 
MYSQL | MySQL deve ser instalado no servidor de configuração. Pode instalar manualmente ou recuperação de sites pode instalá-lo durante a implementação. Para o Site Recovery instalar, verifique que a máquina consegue contactar http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
URLs | O servidor de configuração precisa de aceder a estes URLs (diretamente ou através do proxy):<br/><br/> Azure AD: `login.microsoftonline.com`; `login.microsoftonline.us`; `*.accesscontrol.windows.net`<br/><br/> Transferência de dados de replicação: `*.backup.windowsazure.com`; `*.backup.windowsazure.us`<br/><br/> Gestão da replicação: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`; `*.services.visualstudio.com`<br/><br/> Acesso de armazenamento: `*.blob.core.windows.net`; `*.blob.core.usgovcloudapi.net`<br/><br/> Sincronização de hora: `time.nist.gov`; `time.windows.com`<br/><br/> Telemetria (opcional): `dc.services.visualstudio.com`
Firewall | Regras de firewall baseadas no endereço IP devem permitir a comunicação para URLs do Azure. Para simplificar e limitar os intervalos de IP, recomendamos que utilize a filtragem de URL.<br/><br/>**Para IPs comerciais:**<br/><br/>-Permitir que o [intervalos de IP de Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443).<br/><br/> -Permitir que os intervalos de endereços IP para oeste dos E.U.A. (utilizados para controlo de acesso e gestão de identidades).<br/><br/> -Permitir que os intervalos de endereços IP para a região do Azure da sua subscrição, para suportar os URLs necessários para o Azure Active Directory, cópia de segurança, replicação e armazenamento.<br/><br/> **Para o Governo IPs:**<br/><br/> -Permitir que os intervalos IP do Azure Government Datacenter e a porta HTTPS (443).<br/><br/> -Permitir que os intervalos de endereços IP para todos os US Gov regiões (Virginia, Texas, Arizona e Iowa) suportar os URLs necessários para o Azure Active Directory, cópia de segurança, replicação e armazenamento.
Portas | Permitir 443 (orquestração de canal de controlo)<br/><br/> Permitir 9443 (transporte de dados) 


**Requisitos de dimensionamento do servidor de configuração/processos**

**CPU** | **Memória** | **Disco de cache** | **Taxa de alteração de dados** | **Máquinas replicadas**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 sockets * 4 núcleos \@ 2,5 GHz | 16GB | 300 GB | 500 GB ou menos | < 100 máquinas
12 vCPUs<br/><br/> 2 socks * 6 núcleos \@ 2,5 GHz | 18 GB | 600 GB | 500 GB – 1 TB | máquinas de 100 a 150
16 vCPUs<br/><br/> 2 socks * 8 núcleos \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 máquinas

