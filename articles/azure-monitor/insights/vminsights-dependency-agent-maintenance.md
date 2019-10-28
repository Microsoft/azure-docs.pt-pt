---
title: Como atualizar o Azure Monitor para VMs agente de dependência | Microsoft Docs
description: Este artigo descreve como atualizar o Azure Monitor para VMs agente de dependência usando a linha de comando, o assistente de configuração e outros métodos.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 09/30/2019
ms.openlocfilehash: f062dead8d479fe4da5de46b76b82cee9207bd83
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933721"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Como atualizar o Azure Monitor para VMs agente de dependência

Após a implantação inicial do Azure Monitor para VMs agente de dependência, são lançadas atualizações que incluem correções de bugs ou suporte a novos recursos ou funcionalidades.  Este artigo ajuda você a entender os métodos disponíveis e como executar a atualização manualmente ou por meio da automação.

## <a name="upgrade-options"></a>Opções de atualização 

O Dependency Agent para Windows e Linux pode ser atualizado para a versão mais recente manualmente ou automaticamente, dependendo do cenário de implantação e do ambiente no qual o computador está sendo executado. Os métodos a seguir podem ser usados para atualizar o agente.

|Ambiente |Método de instalação |Método de atualização |
|------------|--------------------|---------------|
|VM do Azure | Extensão de VM do agente de dependência para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | O Agent é atualizado automaticamente por padrão, a menos que você tenha configurado seu modelo de Azure Resource Manager para recusar definindo a propriedade *autoUpgradeMinorVersion* como **false**. A atualização para a versão secundária em que a atualização automática está desabilitada e uma atualização de versão principal segue o mesmo método – desinstale e reinstale a extensão. |
| Imagens personalizadas de VM do Azure | Instalação manual do agente de dependência para Windows/Linux | A atualização de VMs para a versão mais recente do agente precisa ser executada na linha de comando que executa o pacote do Windows Installer ou o grupo de script de shell instalável e de extração automática do Linux.|
| VMs não Azure | Instalação manual do agente de dependência para Windows/Linux | A atualização de VMs para a versão mais recente do agente precisa ser executada na linha de comando que executa o pacote do Windows Installer ou o grupo de script de shell instalável e de extração automática do Linux. |

## <a name="upgrade-windows-agent"></a>Atualizar o agente do Windows 

Para atualizar o agente em uma VM do Windows para a versão mais recente não instalada usando a extensão de VM do agente de dependência, execute no prompt de comando, script ou outra solução de automação ou usando o assistente de instalação do Installdependencyagent-Windows. exe.  

Você pode baixar a versão mais recente do agente do Windows [aqui](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Usando o assistente de instalação

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute **installdependencyagent-Windows. exe** para iniciar o assistente de instalação.

3. Na caixa de diálogo **configuração do Dependency Agent 9.9.1** , clique em **concordo** para aceitar o contrato de licença.

5. Na caixa de diálogo **Dependency Agent desinstalação do 9.9.0** , clique em **Avançar**. A página de status exibe o progresso da desinstalação da versão anterior.

6. Na caixa de diálogo **Dependency Agent desinstalação do 9.9.0** , clique em **desinstalar** para continuar com a desinstalação da versão anterior do caminho especificado na caixa de diálogo. 

7. Na caixa de diálogo **desinstalação do Dependency Agent 9.9.0** , o progresso da desinstalação é mostrado e, quando concluído, a página **concluindo Dependency Agent desinstalação** é exibida. Clique em **Concluir**.

8. Na caixa de diálogo **Dependency Agent configuração do 9.9.1** , o progresso da instalação é mostrado. Quando a página **concluindo Dependency Agent desinstalar** for exibida, clique em **concluir**. 

### <a name="from-the-command-line"></a>Na linha de comando

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute o comando a seguir.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    O parâmetro `/RebootMode=manual` impede que a atualização reinicie automaticamente o computador se alguns processos estiverem usando arquivos da versão anterior e tiverem um bloqueio neles. 

3. Para confirmar se a atualização foi bem-sucedida, consulte as `install.log` para obter informações detalhadas de configuração. O diretório de log é *%ProgramFiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Atualizar agente do Linux 

A atualização de versões anteriores do Dependency Agent no Linux é suportada e executada seguindo o mesmo comando que uma nova instalação.

Você pode baixar a versão mais recente do agente do Windows [aqui](https://aka.ms/dependencyagentlinux).

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute o comando a seguir como raiz`sh InstallDependencyAgent-Linux64.bin -s`. 

Se o agente de dependência não for iniciado, verifique os logs para obter informações detalhadas sobre o erro. Em agentes do Linux, o diretório de log é */var/opt/Microsoft/Dependency-Agent/log*. 

## <a name="next-steps"></a>Passos seguintes

Se você quiser interromper o monitoramento de suas VMs por um período de tempo ou remover totalmente Azure Monitor para VMs, consulte [desabilitar o monitoramento de suas VMs no Azure monitor para VMs](vminsights-optout.md).