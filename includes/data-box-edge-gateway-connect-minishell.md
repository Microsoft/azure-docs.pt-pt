---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184721"
---
Dependendo do sistema operativo do cliente, os procedimentos para ligar remotamente ao dispositivo são diferentes.

### <a name="remotely-connect-from-a-windows-client"></a>Ligar remotamente a partir de um cliente do Windows

Antes de começar, certifique-se de que o seu cliente Windows está a executar o Windows PowerShell 5.0 ou mais tarde.

Siga estes passos para ligar remotamente a partir de um cliente windows.

1. Execute uma sessão do Windows PowerShell como administrador.
2. Certifique-se de que o serviço de Gestão Remota do Windows está a funcionar no seu cliente. Na linha de comandos, escreva:

    `winrm quickconfig`

3. Atribuir uma variável ao endereço IP do dispositivo.

    $ip = "> <device_ip"

    Substitua-o `<device_ip>` pelo endereço IP do seu dispositivo.

4. Para adicionar o endereço IP do seu dispositivo à lista de anfitriões fidedignos do cliente, digite o seguinte comando:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Inicie uma sessão do Windows PowerShell no dispositivo:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Forneça a senha quando solicitada. Use a mesma senha que é usada para iniciar sessão na UI web local. A senha de UI web local padrão é *Password1*. Quando se conecta com sucesso ao dispositivo utilizando powerShell remoto, vê a seguinte saída da amostra:  

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

No cliente Linux que usará para ligar:

- [Instale o mais recente PowerShell Core para Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) do GitHub para obter a função de remoing SSH. 
- [Instale `gss-ntlmssp` apenas a embalagem a partir do módulo NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Para os clientes Ubuntu, utilize o seguinte comando:
    - `sudo apt-get install gss-ntlmssp`

Para mais informações, vá à [PowerShell remoing sobre SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Siga estes passos para ligar remotamente a partir de um cliente NFS.

1. Para abrir a sessão PowerShell, escreva:

    `sudo pwsh`
 
2. Para ligar o cliente remoto, escreva:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Quando solicitado, forneça a palavra-passe utilizada para iniciar sessão no seu dispositivo.
 
> [!NOTE]
> Este procedimento não funciona no Mac OS.
