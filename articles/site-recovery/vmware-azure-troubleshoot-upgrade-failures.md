---
title: Resolver problemas de falhas de atualização do fornecedor do Microsoft Azure Site Recovery | Documentos da Microsoft
description: Compreender e
author: vDonGlover
manager: jarrettr
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: v-doglov
ms.openlocfilehash: 3a3e9da66cf9ca6e08bb25b4f4b9d09aa0c5b6e7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431942"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Resolver problemas de falhas de atualização do Fornecedor do Microsoft Azure Site Recovery

Este artigo ajuda-o a resolver problemas que podem causar falhas durante um fornecedor do Microsoft Azure Site Recovery atualizar.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>A atualização falha de geração de relatórios que o fornecedor mais recente do Site Recovery já está instalado

Ao atualizar o Microsoft Azure Site Recovery Provider (DRA), a atualização de configuração unificada falha e emite a mensagem de erro:

Atualização não é suportada porque já está instalada uma versão superior do software.

Para atualizar, utilize os seguintes passos:

1. Baixe o Microsoft Azure Site Recovery a configuração unificada:
   1. Na seção "Links para suportado atualmente update rollups" a [as atualizações no Azure Site Recovery de serviço](service-updates-how-to.md##links-to-currently-supported-update-rollups) artigo, selecione o fornecedor para a qual estiver a atualizar.
   2. Na página de rollup, localize a **atualizar informações** secção e Baixe o Update Rollup do Microsoft Azure Site Recovery Unified Setup.

2. Abra uma linha de comandos e navegue para a pasta para o qual transferiu o ficheiro de configuração unificada. Extraia os ficheiros de configuração do download usando o seguinte comando, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;caminho da pasta para os ficheiros extraídos&gt;.
    
    Comando de exemplo:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. No prompt de comando, navegue para a pasta para onde extraiu os ficheiros e execute os seguintes comandos de instalação:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART  UCX_SERVER_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Voltar para a pasta para o qual transferiu a configuração unificada e execute MicrosoftAzureSiteRecoveryUnifiedSetup.exe para concluir a atualização. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Falha da atualização devido a pasta de 3rd party a ser renomeada

Para a atualização com êxito, a pasta de 3rd party não deve ser renomeada.

Para resolver o problema.

2. Inicie o Editor de registo (regedit.exe) e abra o ramo HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
3. Inspecione o `Build_Version` valor da chave. Se estiver definido para a versão mais recente, reduza o número de versão. Por exemplo, se a versão mais recente é 9.22. \* e o `Build_Version` conjunto para o valor da chave, em seguida, reduzi-lo para 9.21.\*.
4. Baixe o mais recente Microsoft Azure Site Recovery Unified Setup:
   1. Na seção "Links para suportado atualmente update rollups" a [as atualizações no Azure Site Recovery de serviço](service-updates-how-to.md##links-to-currently-supported-update-rollups) artigo, selecione o fornecedor para a qual estiver a atualizar.
   2. Na página de rollup, localize a **atualizar informações** secção e Baixe o Update Rollup do Microsoft Azure Site Recovery Unified Setup.
5. Abra uma linha de comandos e navegue para a pasta para o qual transferiu o ficheiro de configuração unificada e o extrair os ficheiros de configuração do download usando o seguinte comando, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;caminho da pasta para o extrair ficheiros&gt;.

    Comando de exemplo:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

4. No prompt de comando, navegue para a pasta para onde extraiu os ficheiros e execute os seguintes comandos de instalação:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

5. Utilize o Gestor de tarefas para monitorizar o progresso da instalação. Quando o processo para CX_THIRDPARTY_SETUP. EXE já não está visível no Gerenciador de tarefas, avance para o passo seguinte.
6. Certifique-se de que C:\thirdparty existe e que a pasta contém as bibliotecas RRD.
1. Voltar para a pasta para o qual transferiu a configuração unificada e execute MicrosoftAzureSiteRecoveryUnifiedSetup.exe para concluir a atualização. 