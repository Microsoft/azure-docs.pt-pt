---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f9707ab48d64ede2e796675eb3a0a6e7820c7073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102603383"
---
Dependendo do sistema operativo do cliente, os procedimentos para ligar remotamente ao dispositivo são diferentes.

### <a name="remotely-connect-from-a-windows-client"></a>Ligação remota de um cliente Windows

Antes de começar, certifique-se de que o seu cliente Windows está a executar o Windows PowerShell 5.0 ou mais tarde.

Siga estes passos para ligar remotamente a partir de um cliente Windows.

1. Executar uma sessão Windows PowerShell como administrador.
2. Certifique-se de que o serviço de Gestão Remota do Windows está a funcionar no seu cliente. Na linha de comandos, escreva:

    `winrm quickconfig`

3. Atribua uma variável ao endereço IP do dispositivo.

    $ip = "<device_ip>"

    `<device_ip>`Substitua-o pelo endereço IP do seu dispositivo.

4. Para adicionar o endereço IP do seu dispositivo à lista de anfitriões fidedignos do cliente, digite o seguinte comando:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Inicie uma sessão Do Windows PowerShell no dispositivo:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Forneça a palavra-passe quando solicitado. Use a mesma palavra-passe que é usada para iniciar súm na UI web local. A senha de acesso web local padrão é *Password1*. Quando se liga com sucesso ao dispositivo utilizando o PowerShell remoto, vê a seguinte saída da amostra:  

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

### <a name="remotely-connect-from-a-linux-client"></a>Ligação remota de um cliente Linux

No cliente Linux que utilizará para ligar:

- [Instale o mais recente PowerShell Core para Linux](/powershell/scripting/install/installing-powershell-core-on-linux) do GitHub para obter a função de remoting SSH. 
- [Instale apenas a `gss-ntlmssp` embalagem a partir do módulo NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Para clientes Ubuntu, utilize o seguinte comando:
    - `sudo apt-get install gss-ntlmssp`

Para obter mais informações, aceda ao [PowerShell remoing sobre SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Siga estes passos para ligar remotamente a um cliente NFS.

1. Para abrir a sessão PowerShell, escreva:

    `pwsh`
 
2. Para ligar utilizando o cliente remoto, escreva:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Quando solicitado, forneça a palavra-passe utilizada para iniciar sedutar no seu dispositivo.
 
> [!NOTE]
> Este procedimento não funciona no macOS.