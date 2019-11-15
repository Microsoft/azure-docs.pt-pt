---
title: Solucionar problemas de atualização do provedor de Site Recovery de Microsoft Azure
description: Resolva problemas comuns que ocorrem ao atualizar o provedor de Site Recovery de Microsoft Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: 0e6631d36f8b8153c34f787f8941b45b7522aceb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083930"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Resolver problemas de falhas de atualização do Fornecedor do Microsoft Azure Site Recovery

Este artigo ajuda você a resolver problemas que podem causar falhas durante uma atualização de provedor de Site Recovery Microsoft Azure.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>A atualização falha ao relatar que o provedor de Site Recovery mais recente já está instalado

Ao atualizar Microsoft Azure o provedor de Site Recovery (DRA), a atualização de instalação unificada falha e emite a mensagem de erro:

Não há suporte para a atualização porque uma versão mais recente do software já está instalada.

Para atualizar, use as seguintes etapas:

1. Baixe a configuração unificada do Microsoft Azure Site Recovery:
   1. Na seção "links para pacotes cumulativos de atualizações com suporte no momento" do artigo [atualizações de serviço no Azure site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) , selecione o provedor para o qual você está atualizando.
   2. Na página ROLLUP, localize a seção **informações de atualização** e baixe o pacote cumulativo de atualizações para Microsoft Azure site Recovery configuração unificada.

2. Abra um prompt de comando e navegue até a pasta para a qual você baixou o arquivo de instalação unificada. Extraia os arquivos de instalação do download usando o comando a seguir, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x:&lt;caminho da pasta para os arquivos extraídos&gt;.
    
    Comando de exemplo:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. No prompt de comando, navegue até a pasta para a qual você extraiu os arquivos e execute os seguintes comandos de instalação:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART  UCX_SERVER_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Retorne à pasta para a qual você baixou a configuração unificada e execute MicrosoftAzureSiteRecoveryUnifiedSetup. exe para concluir a atualização. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Falha de atualização devido à pasta de terceiros sendo renomeada

Para que a atualização seja realizada com sucesso, a pasta de terceiros não deve ser renomeada.

Para resolver o problema.

1. Inicie o editor do registro (regedit. exe) e abra o HKEY_LOCAL_MACHINE \SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 Branch.
1. Inspecione o valor da chave de `Build_Version`. Se estiver definido como a versão mais recente, reduza o número de versão. Por exemplo, se a versão mais recente for 9,22.\* e a chave de `Build_Version` configurada para esse valor e, em seguida, reduza-a para 9,21.\*.
1. Baixe o Microsoft Azure mais recente Site Recovery configuração unificada:
   1. Na seção "links para pacotes cumulativos de atualizações com suporte no momento" do artigo [atualizações de serviço no Azure site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) , selecione o provedor para o qual você está atualizando.
   2. Na página ROLLUP, localize a seção **informações de atualização** e baixe o pacote cumulativo de atualizações para Microsoft Azure site Recovery configuração unificada.
1. Abra um prompt de comando e navegue até a pasta para a qual você baixou o arquivo de instalação unificado e extraia os arquivos de instalação do download usando o comando a seguir, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x:&lt;caminho da pasta para os arquivos extraídos&gt;.

    Comando de exemplo:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. No prompt de comando, navegue até a pasta para a qual você extraiu os arquivos e execute os seguintes comandos de instalação:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. Use o Gerenciador de tarefas para monitorar o progresso da instalação. Quando o processo para CX_THIRDPARTY_SETUP. EXE não estará mais visível no Gerenciador de tarefas, vá para a próxima etapa.
1. Verifique se C:\thirdparty existe e se a pasta contém as bibliotecas RRD.
1. Retorne à pasta para a qual você baixou a configuração unificada e execute MicrosoftAzureSiteRecoveryUnifiedSetup. exe para concluir a atualização. 