---
title: Como atualizar o Monitor Azure para o agente de dependência de VMs. Microsoft Docs
description: Este artigo descreve como atualizar o Monitor Azure para o agente de dependência de VMs utilizando linha de comando, assistente de configuração e outros métodos.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: c98c48a4494ac37ef4868c44d4a7adacfd0d48da
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662438"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Como atualizar o Monitor Azure para agente de dependência de VMs

Após a implementação inicial do Monitor Azure para o agente de dependência de VMs, são lançadas atualizações que incluem correções de bugs ou suporte de novas funcionalidades ou funcionalidades.  Este artigo ajuda-o a compreender os métodos disponíveis e como realizar a atualização manualmente ou através da automatização.

## <a name="upgrade-options"></a>Opções de upgrade 

O agente Dependency para Windows e Linux pode ser atualizado para a mais recente versão manual ou automaticamente, dependendo do cenário de implementação e ambiente em que a máquina está a funcionar. Os seguintes métodos podem ser utilizados para atualizar o agente.

|Ambiente |Método de instalação |Método de atualização |
|------------|--------------------|---------------|
|VM do Azure | Extensão VM do agente de dependência para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | O agente é automaticamente atualizado por padrão, a menos que tenha configurado o seu modelo de Gestor de Recursos Azure para optar por não o fazer, definindo a propriedade *autoUpgradeMinorVersion* para **falsa**. A atualização para uma versão menor onde a atualização automática é desativada, e uma atualização de versão principal siga o mesmo método - desinstale e reinstale a extensão. |
| Imagens VM Vip personalizadas | Instalação manual do agente Dependency para Windows/Linux | A atualização dos VMs para a versão mais recente do agente tem de ser executada a partir da linha de comando que executa o pacote de instalação do Windows ou o pacote de scripts de auto-extracção e instalação do Script Linux.|
| VMs não-Azure | Instalação manual do agente Dependency para Windows/Linux | A atualização dos VMs para a versão mais recente do agente tem de ser executada a partir da linha de comando que executa o pacote de instalação do Windows ou o pacote de scripts de auto-extracção e instalação do Script Linux. |

## <a name="upgrade-windows-agent"></a>Atualizar o agente Windows 

Para atualizar o agente num VM do Windows para a versão mais recente não instalada utilizando a extensão VM do agente de dependência, ou executa a partir do Comando Solicitação, script ou outra solução de automatização, ou utilizando o Assistente de Configuração InstallDependencyAgent-Windows.exe.  

Pode descarregar a versão mais recente do agente Windows a partir [daqui](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Usando o Assistente de Configuração

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Execute **instalaçãoDependencAgente-Windows.exe** para iniciar o Assistente de Configuração.
   
3. Siga o assistente de **configuração** do Agente de Dependência para desinstalar a versão anterior do agente de dependência e, em seguida, instalar a versão mais recente.


### <a name="from-the-command-line"></a>Da linha de comando

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Executar o seguinte comando.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    O parâmetro `/RebootMode=manual` impede que a atualização reinicie automaticamente a máquina se alguns processos estiverem a utilizar ficheiros da versão anterior e tiverem um bloqueio nos mesmos. 

3. Para confirmar que a atualização foi bem sucedida, verifique a `install.log` informações detalhadas sobre a configuração. O diretório de registo é *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Atualizar agente Linux 

O upgrade de versões anteriores do agente Dependency no Linux é suportado e realizado seguindo o mesmo comando que uma nova instalação.

Pode descarregar a versão mais recente do agente Windows a partir [daqui](https://aka.ms/dependencyagentlinux).

1. Inscreva-se no computador com uma conta que tenha direitos administrativos.

2. Executar o seguinte comando como raiz`sh InstallDependencyAgent-Linux64.bin -s`. 

Se o agente de dependência não iniciar, verifique os registos para obter informações de erro detalhadas. Nos agentes Linux, o diretório de registo é */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Passos seguintes

Se pretender parar de monitorizar os seus VMs durante um período de tempo ou remover completamente o Monitor Azure para VMs, consulte a [monitorização de desativação dos seus VMs no Monitor Azure para VMs](vminsights-optout.md).
