---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555049"
---
Dependendo do sistema operativo do cliente, os procedimentos para ligar remotamente ao dispositivo são diferentes.

### <a name="remotely-connect-from-a-windows-client"></a>Ligar remotamente a partir de um cliente Windows

Antes de começar, certifique-se de que o cliente do Windows está a executar o Windows PowerShell, 5.0 ou posterior.

Siga estes passos para ligar remotamente a partir de um cliente do Windows.

1. Execute uma sessão do Windows PowerShell como administrador.
2. Certifique-se de que o serviço Gestão remota do Windows está em execução no cliente. Na linha de comandos, escreva:

    `winrm quickconfig`

3. Atribua uma variável para o endereço IP do dispositivo.

    $ip = "<device_ip>"

    Substitua `<device_ip>` com o endereço IP do seu dispositivo.

4. Para adicionar o endereço IP do seu dispositivo a lista de anfitriões fidedigna do cliente, escreva o seguinte comando:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Inicie uma sessão do Windows PowerShell no dispositivo:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Forneça a palavra-passe quando lhe for pedido. Utilize a mesma palavra-passe é utilizada para iniciar sessão na IU da web local. É a palavra-passe do padrão local web da interface do Usuário *Password1*. Quando se ligar com êxito para o dispositivo com o PowerShell remoto, verá a saída de exemplo seguinte:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Ligar remotamente a partir de um cliente Linux

No cliente de Linux que utilizará para ligar:

- [Instalar o PowerShell Core mais recente para Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) do GitHub para obter a funcionalidade de comunicação remota SSH. 
- [Instalar apenas o `gss-ntlmssp` pacote a partir do módulo NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Para clientes do Ubuntu, utilize o seguinte comando:
    - `sudo apt-get install gss-ntlmssp`

Para obter mais informações, aceda a [comunicação remota do PowerShell através de SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Siga estes passos para ligar remotamente a partir de um cliente NFS.

1. Para abrir a sessão do PowerShell, escreva:

    `sudo pwsh`
 
2. Para ligar com o cliente remoto, escreva:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Quando lhe for pedido, forneça a palavra-passe utilizada para iniciar sessão no seu dispositivo.
 
> [!NOTE]
> Este procedimento não funciona no Mac OS.
