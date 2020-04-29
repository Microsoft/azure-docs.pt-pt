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
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184485"
---
**Configuração/Requisitos do servidor de processo para replicação física do servidor**

**Componente** | **Requisito** 
--- | ---
**DEFINIÇÕES DE HARDWARE** | 
Núcleos de CPU | 8 
RAM | 16 GB
Número de discos | 3, incluindo o disco OS, o disco de cache do servidor de processo e a unidade de retenção para o failback 
Espaço de disco gratuito (cache do servidor de processos) | 600 GB
Espaço de disco gratuito (disco de retenção) | 600 GB
 | 
**DEFINIÇÕES DE SOFTWARE** | 
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Região do sistema operativo | Inglês (en-us)
Funções do Windows Server | Não ative estes papéis: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V 
Políticas de grupo | Não permita estas políticas de grupo: <br> - Impedir o acesso ao pedido de comando. <br> - Impedir o acesso às ferramentas de edição de registo. <br> - Lógica de confiança para anexos de ficheiros. <br> - Ligue a execução do guião. <br> [Mais informações](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nenhum website preexistente <br> - Nenhum site/aplicação pré-existente ouvindo na porta 443 <br>- Ativar [a autenticação anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Ativar a regulação [fastCGI.](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
Tipo de endereço IP | Estático 
| 
**DEFINIÇÕES DE ACESSO** | 
MYSQL | O MySQL deve ser instalado no servidor de configuração. Pode instalar manualmente, ou a Recuperação do Site pode instalá-la durante a implementação. Para instalar a Recuperação do Local, verifique se a máquina pode alcançar http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
URLs | O servidor de configuração precisa de acesso a estes URLs (diretamente ou via procuração):<br/><br/> Azure AD: `login.microsoftonline.com` `login.microsoftonline.us`;`*.accesscontrol.windows.net`<br/><br/> Transferência de dados `*.backup.windowsazure.com`de replicação:`*.backup.windowsazure.us`<br/><br/> Gestão de `*.hypervrecoverymanager.windowsazure.com`replicação: `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`;`*.services.visualstudio.com`<br/><br/> Acesso ao `*.blob.core.windows.net`armazenamento: ;`*.blob.core.usgovcloudapi.net`<br/><br/> Sincronização do tempo: `time.nist.gov``time.windows.com`<br/><br/> Telemetria (opcional):`dc.services.visualstudio.com`
Firewall | As regras de firewall baseadas em endereçoIP devem permitir a comunicação aos URLs Azure. Para simplificar e limitar as gamas IP, recomendamos a utilização de filtragem de URL.<br/><br/>**Para iPs comerciais:**<br/><br/>- Permitir as gamas IP do Centro de [Dados Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443).<br/><br/> - Permitir intervalos de endereços IP para os EUA Ocidentais (utilizados para controlo de acesso e gestão de identidade).<br/><br/> - Permitir intervalos de endereçoip para a região azure da sua subscrição, para apoiar os URLs necessários para o Diretório Ativo Azure, backup, replicação e armazenamento.<br/><br/> **Para os IPs do governo:**<br/><br/> - Permitir o Datacenter IP Ranges do Governo Azure e a porta HTTPS (443).<br/><br/> - Permitir intervalos de endereços IP para todas as regiões norte-americanas gov (Virgínia, Texas, Arizona e Iowa), para apoiar os URLs necessários para o Azure Ative Directory, backup, replicação e armazenamento.
Portas | Permitir 443 (Orquestração de canais de controlo)<br/><br/> Permitir 9443 (Transporte de dados) 


**Requisitos de tamanho do servidor de configuração/processo**

**CPU** | **Memória** | **Disco cache** | **Taxa de alteração de dados** | **Máquinas replicadas**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 tomadas * \@ 4 núcleos 2,5 GHz | 16GB | 300 GB | 500 GB ou menos | < 100 máquinas
12 vCPUs<br/><br/> 2 meias \@ * 6 núcleos 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 a 150 máquinas
16 vCPUs<br/><br/> 2 meias \@ * 8 núcleos 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 máquinas

