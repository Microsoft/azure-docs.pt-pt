---
title: Ligue-se à consola de máquinas virtuais no dispositivo GPU Azure Stack Edge Pro
description: Descreve como ligar-se à consola de máquinas virtuais num VM em execução no dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962691"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Ligue-se a uma consola de máquinas virtuais num dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A solução GPU Azure Stack Edge Pro executa cargas de trabalho não contentorizadas através das máquinas virtuais. Este artigo descreve como ligar-se à consola de uma máquina virtual implantada no seu dispositivo. 

A consola de máquinas virtuais permite-lhe aceder aos seus VMs com funcionalidades de teclado, rato e ecrã utilizando as ferramentas de ambiente de trabalho remoto normalmente disponíveis. Pode aceder à consola e resolver problemas ao implementar uma máquina virtual no seu dispositivo. Pode ligar-se à consola de máquinas virtuais mesmo que o seu VM não tenha conseguido o fornecimento.


## <a name="workflow"></a>Fluxo de trabalho

O fluxo de trabalho de alto nível tem os seguintes passos:

1. Ligue-se à interface PowerShell no seu dispositivo.
1. Permitir o acesso à consola ao VM.
1. Ligue-se ao VM utilizando o protocolo de ambiente de trabalho remoto (RDP).
1. Revogar o acesso à consola ao VM.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de ter completado os seguintes pré-requisitos:

#### <a name="for-your-device"></a>Para o seu dispositivo

Deverá ter acesso a um dispositivo GPU Azure Stack Edge Pro que esteja ativado. O aparelho deve ter um ou mais VMs nele implantados. Pode implementar VMs através do Azure PowerShell, através dos modelos, ou através do portal Azure.

#### <a name="for-client-accessing-the-device"></a>Para o cliente aceder ao dispositivo

Certifique-se de que tem acesso a um sistema de clientes que:

- Pode aceder à interface PowerShell do dispositivo. O cliente está a executar um [sistema operativo suportado.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)
- O cliente está a executar o PowerShell 7.0 ou mais tarde. Esta versão do PowerShell funciona para clientes Windows, Mac e Linux. Consulte as instruções para [instalar o PowerShell 7.0](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true).
- Tem capacidades de ambiente de trabalho remotos. Dependendo se está a utilizar o Windows, macOS ou Linux, deverá instalar um [destes clientes](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)de ambiente de trabalho remoto . Este artigo fornece instruções com [o Windows Remote Desktop](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) e o [FreeRDP](https://www.freerdp.com/). <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>Ligar à consola VM

Siga estes passos para ligar à consola de máquinas virtuais no seu dispositivo.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>Ligue-se à interface PowerShell no seu dispositivo

O primeiro passo é [ligar à interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) do seu dispositivo. 

### <a name="enable-console-access-to-the-vm"></a>Permitir o acesso à consola ao VM

1.  Na interface PowerShell, executar o seguinte comando para permitir o acesso à consola VM.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. Na saída da amostra, tome nota do ID da máquina virtual. Vai precisar disto para um passo mais tarde.

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>Ligar à VM

Agora pode utilizar um cliente de Ambiente de Trabalho Remoto para ligar à consola de máquinas virtuais.

#### <a name="use-windows-remote-desktop"></a>Use o ambiente de trabalho remoto do Windows

1. Crie um novo ficheiro de texto e insira o seguinte texto.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. Guarde o ficheiro como **.rdp* no seu sistema cliente. Usará este perfil para ligar ao VM.
1. Clique duas vezes no perfil para ligar ao VM. Fornecer as seguintes credenciais:

    - **Nome de utilizador**: Inscreva-se como EdgeARMUser.
    - **Senha**: Forneça a senha do Gestor de Recursos Azure local para o seu dispositivo. Se esqueceu a palavra-passe, reinicie a [palavra-passe do Gestor de Recursos Azure através do portal Azure](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal). 

#### <a name="use-freerdp"></a>Use FreeRDP

Se utilizar o FreeRDP no seu cliente Linux, executar o seguinte comando: 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>Revogar o acesso à consola VM

Para revogar o acesso à consola VM, volte à interface PowerShell do seu dispositivo. Execute o seguinte comando:

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
Aqui está uma saída de exemplo:

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> Recomendamos que, depois de ter terminado a utilização da consola VM, ou revogue o acesso ou feche a janela PowerShell para sair da sessão. 

## <a name="next-steps"></a>Passos seguintes

- Resolução de problemas [Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md) no portal Azure.