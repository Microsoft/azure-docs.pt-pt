---
title: Como atualizar o agente de dependência de insights VM
description: Este artigo descreve como atualizar o agente de dependência de insights VM usando linha de comando, assistente de configuração e outros métodos.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: 14ca614c1e108ce43f05f98520cff6ff215ec5cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722913"
---
# <a name="how-to-upgrade-the-vm-insights-dependency-agent"></a>Como atualizar o agente de dependência de insights VM

Após a implementação inicial do agente de dependência de insights VM, são lançadas atualizações que incluem correções de bugs ou suporte de novas funcionalidades ou funcionalidades.  Este artigo ajuda-o a compreender os métodos disponíveis e como realizar a atualização manualmente ou através da automatização.

## <a name="upgrade-options"></a>Opções de upgrade 

O agente Descositada para Windows e Linux pode ser atualizado para a versão mais recente manualmente ou automaticamente, dependendo do cenário de implementação e ambiente em que a máquina está a funcionar. Os seguintes métodos podem ser utilizados para atualizar o agente.

|Ambiente |Método de instalação |Método de atualização |
|------------|--------------------|---------------|
|VM do Azure | Extensão VM do agente de dependência para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | O agente é automaticamente atualizado por padrão, a menos que tenha configurado o seu modelo de Gestor de Recursos Azure para excluir, definindo a *propriedade autoUpgradeMinorVersion* a **falsa**. A atualização para versão menor onde a atualização automática é desativada, e uma atualização de versão principal segue o mesmo método - desinstalar e reinstalar a extensão. |
| Imagens VM personalizadas do Azure | Instalação manual do agente de dependência para Windows/Linux | A atualização dos VMs para a versão mais recente do agente tem de ser executada a partir da linha de comando que executa o pacote do instalador windows ou do pacote de scripts de concha auto-extraída e instalável do Linux.|
| VMs não-azure | Instalação manual do agente de dependência para Windows/Linux | A atualização dos VMs para a versão mais recente do agente tem de ser executada a partir da linha de comando que executa o pacote do instalador windows ou do pacote de scripts de concha auto-extraída e instalável do Linux. |

## <a name="upgrade-windows-agent"></a>Atualizar o agente windows 

Para atualizar o agente num VM do Windows para a versão mais recente não instalada utilizando a extensão VM do agente de dependência, ou corre a partir do Pedido de Comando, script ou outra solução de automatização, ou utilizando o Assistente de Configuração InstallDependencyAgent-Windows.exe.  

Pode baixar a versão mais recente do agente Windows a partir [daqui.](https://aka.ms/dependencyagentwindows)

### <a name="using-the-setup-wizard"></a>Usando o assistente de configuração

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Execute **InstallDependencyAgent-Windows.exe** para iniciar o Assistente de Configuração.
   
3. Siga o assistente **de configuração do Agente de Dependência** para desinstalar a versão anterior do agente de dependência e, em seguida, instale a versão mais recente.


### <a name="from-the-command-line"></a>Da linha de comando

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Execute o seguinte comando.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    O `/RebootMode=manual` parâmetro impede que a atualização reinicie automaticamente a máquina se alguns processos estiverem a utilizar ficheiros da versão anterior e tiverem um bloqueio nos mesmos. 

3. Para confirmar que a atualização foi bem sucedida, verifique se `install.log` existem informações detalhadas sobre a configuração. O diretório de registos é *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Atualizar agente Linux 

A atualização a partir de versões anteriores do agente de Dependência no Linux é suportada e executada seguindo o mesmo comando que uma nova instalação.

Pode baixar a versão mais recente do agente Linux a partir [daqui.](https://aka.ms/dependencyagentlinux)

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Executar o seguinte comando como raiz `sh InstallDependencyAgent-Linux64.bin -s` . 

Se o agente de dependência não arrancar, verifique se os registos são informativo de erro. Nos agentes Linux, o diretório de registos é */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Passos seguintes

Se pretender parar de monitorizar os seus VMs durante um período de tempo ou remover completamente os conhecimentos de VM, consulte [a monitorização desativada dos seus VMs em insights VM](../vm/vminsights-optout.md).
