---
title: Atualização da resolução de problemas do Provedor de Recuperação do Site microsoft Azure
description: Resolva problemas comuns que ocorrem ao atualizar o fornecedor microsoft Azure Site Recovery.
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.openlocfilehash: f53cc8d5732db85f69e48efdaddd518e75ff518b
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579497"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Resolver problemas de falhas de atualização do Fornecedor do Microsoft Azure Site Recovery

Este artigo ajuda-o a resolver problemas que podem causar falhas durante uma atualização do Microsoft Azure Site Recovery Provider.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>A atualização falha em informar que o mais recente Fornecedor de Recuperação de Sítios já está instalado

Ao atualizar o Microsoft Azure Site Recovery Provider (DRA), a atualização de configuração unificada falha e emite a mensagem de erro:

A atualização não é suportada, uma vez que uma versão mais alta do software já está instalada.

Para atualizar, utilize os seguintes passos:

1. Descarregue a Configuração Unificada de Recuperação do Site Microsoft Azure:
   1. Na secção "Links to atualmente suportados rollups" das atualizações de Serviço no artigo [de Recuperação do Site Azure,](service-updates-how-to.md#links-to-currently-supported-update-rollups) selecione o fornecedor ao qual está a atualizar.
   2. Na página rollup, localize a secção **de informações de Atualização** e descarregue o Rollup de Atualização para a Configuração Unificada de Recuperação do Site do Microsoft Azure.

2. Abra um pedido de comando e navegue para a pasta para a qual descarregou o ficheiro De configuração unificada. Extrair os ficheiros de configuração do download utilizando o seguinte comando, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x: &lt; caminho de pasta para os ficheiros extraídos &gt; .
    
    Comando de exemplo:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extraída

3. Na visou prompt de comando, navegue para a pasta para a qual extraiu os ficheiros e execute os seguintes comandos de instalação:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPRESSMSGBOXES /NORESTART UCX_SERVER_SETUP.EXE /VERYSILENT /SUPRESSMSGBOXES /NORESTART /UPGRADE

1. Volte à pasta à qual descarregou a Configuração Unificada e execute MicrosoftAzureSiteRecoveryUnifiedSetup.exe para terminar a atualização. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Falha de upgrade devido à pasta do 3º partido ser renomeada

Para que a atualização tenha sucesso, a pasta do terceiro partido não deve ser renomeada.

Para resolver o problema,

1. Inicie o Editor de Registo (regedit.exe) e abra a filial HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Inspecione o valor da `Build_Version` chave. Se estiver definido para a versão mais recente, reduza o número da versão. Por exemplo, se a versão mais recente for 9.22. \* e o `Build_Version` conjunto de chave para esse valor, em seguida, reduzi-lo para 9.21. . . \* .
1. Descarregue a mais recente Configuração Unificada de Recuperação do Site microsoft Azure:
   1. Na secção "Links to atualmente suportados rollups" das atualizações de Serviço no artigo [de Recuperação do Site Azure,](service-updates-how-to.md#links-to-currently-supported-update-rollups) selecione o fornecedor ao qual está a atualizar.
   2. Na página rollup, localize a secção **de informações de Atualização** e descarregue o Rollup de Atualização para a Configuração Unificada de Recuperação do Site do Microsoft Azure.
1. Abra um pedido de comando e navegue para a pasta para a qual descarregou o ficheiro Configuração Unificada e extrai os ficheiros de configuração do download utilizando o seguinte comando, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x: &lt; caminho de pasta para os ficheiros extraídos &gt; .

    Comando de exemplo:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extraída

1. Na visou prompt de comando, navegue para a pasta para a qual extraiu os ficheiros e execute os seguintes comandos de instalação:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPRESSMSGBOXES /NORESTART

1. Utilize o gestor de tarefas para monitorizar o progresso da instalação. Quando o processo de CX_THIRDPARTY_SETUP.EXE deixar de ser visível no gestor de tarefas, proceda ao passo seguinte.
1. Verifique se c:\terceiro partido existe e que a pasta contém as bibliotecas RRD.
1. Volte à pasta à qual descarregou a Configuração Unificada e execute MicrosoftAzureSiteRecoveryUnifiedSetup.exe para terminar a atualização.

## <a name="upgrade-failure-due-to-master-target-installation-failure"></a>Falha de upgrade devido à falha de instalação do Master Target

Ao atualizar o Microsoft Azure Site Recovery Provider (DRA), a instalação Master Target falha com o erro "A localização de instalação não existe e/ou não tem 1 GB de espaço livre e/ou não existe numa unidade fixa.".

Isto pode dever-se ao valor nulo de um parâmetro na Chave de Registo. Para resolver a questão -

1. Inicie o Editor de Registo (regedit.exe) e abra a filial HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\4.
1. Inspecione o valor da chave 'InstalarDirectório'. Se for nulo, adicione o valor atual do diretório de instalação.
1. Da mesma forma, abra a filial HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5 no Editor de Registos.
1. Inspecione o valor da chave 'InstalarDirectório' e adicione o valor atual do diretório de instalação.
1. Re-executar o instalador de configuração unificado.
