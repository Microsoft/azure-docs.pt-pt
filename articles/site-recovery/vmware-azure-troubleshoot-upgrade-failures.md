---
title: Atualização de problemas do Fornecedor de Recuperação de Sites do Microsoft Azure
description: Resolver problemas comuns que ocorrem ao atualizar o fornecedor de recuperação de websites do Microsoft Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: b59f933fedd5f1d3ed3f7972b1a1fe653df31be2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75893904"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Resolver problemas de falhas de atualização do Fornecedor do Microsoft Azure Site Recovery

Este artigo ajuda-o a resolver problemas que podem causar falhas durante uma atualização do Microsoft Azure Site Recovery Provider.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>A atualização falha em informar que o mais recente Fornecedor de Recuperação do Site já está instalado

Ao atualizar o Fornecedor de Recuperação de Websites do Microsoft Azure (DRA), a atualização unificada de configuração falha e emite a mensagem de erro:

A atualização não é suportada, uma vez que uma versão mais alta do software já está instalada.

Para atualizar, utilize os seguintes passos:

1. Descarregue a configuração unificada de recuperação do site do Microsoft Azure:
   1. Na secção "Links to currently supported update rollups" das atualizações do Serviço no artigo de Recuperação de [Sites Do Azure,](service-updates-how-to.md#links-to-currently-supported-update-rollups) selecione o fornecedor para o qual está a atualizar.
   2. Na página de rollup, localize a secção de **informações do Update** e descarregue o Rollup de Atualização para a Configuração Unificada de Recuperação do Site microsoft Azure.

2. Abra um pedido de comando e navegue para a pasta para a qual descarregou o ficheiro De Configuração Unificada. Extrair os ficheiros de configuração do download utilizando o seguinte comando, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;caminho da pasta para os ficheiros extraídos&gt;.
    
    Comando de exemplo:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extratou

3. No pedido de comando, navegue para a pasta para a qual extraiu os ficheiros e execute os seguintes comandos de instalação:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART UCX_SERVER_SETUP. EX /VERYSILENT /SUPRESSma /SUPRESSÃO /NORESTART /UPGRADE

1. Volte à pasta para a qual descarregou a Configuração Unificada e execute o MicrosoftAzureSiteRecoveryUnifiedSetup.exe para terminar a atualização. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Falha de upgrade devido à renome da pasta de terceiros

Para que a atualização tenha sucesso, a pasta de terceiros não deve ser renomeada.

Para resolver o problema.

1. Inicie o Editor de Registo (regedit.exe) e abra a filial HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Inspecione `Build_Version` o valor-chave. Se estiver definido para a versão mais recente, reduza o número da versão. Por exemplo, se a versão mais recente for 9.22. \* e `Build_Version` a chave definida para esse valor, em seguida, reduzi-lo para 9.21. \*.
1. Faça o download da mais recente configuração unificada de recuperação do site do Microsoft Azure:
   1. Na secção "Links to currently supported update rollups" das atualizações do Serviço no artigo de Recuperação de [Sites Do Azure,](service-updates-how-to.md#links-to-currently-supported-update-rollups) selecione o fornecedor para o qual está a atualizar.
   2. Na página de rollup, localize a secção de **informações do Update** e descarregue o Rollup de Atualização para a Configuração Unificada de Recuperação do Site microsoft Azure.
1. Abra um pedido de comando e navegue para a pasta para a qual descarregou o ficheiro De configuração Unificada e o extrato dos ficheiros de configuração do download utilizando o seguinte comando, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;caminho de pasta para os ficheiros extraídos&gt;.

    Comando de exemplo:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extratou

1. No pedido de comando, navegue para a pasta para a qual extraiu os ficheiros e execute os seguintes comandos de instalação:
   
    CX_THIRDPARTY_SETUP. EX /VERYSILENT /SUPRESSor /NORESTART

1. Utilize o gestor de tarefas para monitorizar o progresso da instalação. Quando o processo para CX_THIRDPARTY_SETUP. ExE já não é visível no gestor de tarefas, passar para o próximo passo.
1. Verifique se c:\terceiro existe e que a pasta contém as bibliotecas RRD.
1. Volte à pasta para a qual descarregou a Configuração Unificada e execute o MicrosoftAzureSiteRecoveryUnifiedSetup.exe para terminar a atualização. 