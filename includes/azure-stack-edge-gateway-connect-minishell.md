---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 89e648099a5ac6d905f475319cc108dd0d05a6e9
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081059"
---
Dependendo do sistema operativo do cliente, os procedimentos para ligar remotamente ao dispositivo são diferentes.

### <a name="remotely-connect-from-a-windows-client"></a>Ligação remota de um cliente Windows


#### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

- O seu cliente Windows está a executar o Windows PowerShell 5.0 ou mais tarde.
- O seu cliente Windows tem a corrente de assinatura (certificado raiz) correspondente ao certificado de nó instalado no dispositivo. Para obter instruções detalhadas, consulte [o certificado de instalação no seu cliente Windows](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
- O `hosts` ficheiro localizado para o seu cliente Windows tem uma entrada correspondente ao certificado de nó no seguinte `C:\Windows\System32\drivers\etc` formato:

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    Aqui está uma entrada de exemplo para o `hosts` ficheiro:
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>Passos detalhados

Siga estes passos para ligar remotamente a partir de um cliente Windows.

1. Executar uma sessão Windows PowerShell como administrador.
2. Certifique-se de que o serviço de Gestão Remota do Windows está a funcionar no seu cliente. Na linha de comandos, escreva:

    `winrm quickconfig`

    Para obter mais informações, consulte [instalação e configuração para gestão remota do Windows](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Atribua uma variável ao endereço IP do dispositivo.

    $ip = "<device_ip>"

    `<device_ip>`Substitua-o pelo endereço IP do seu dispositivo.

4. Para adicionar o endereço IP do seu dispositivo à lista de anfitriões fidedignos do cliente, digite o seguinte comando:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Inicie uma sessão Do Windows PowerShell no dispositivo:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    Se vir um erro relacionado com a relação de confiança, verifique se a cadeia de assinatura do certificado de nó enviado para o seu dispositivo também está instalada no cliente que acede ao seu dispositivo.

    Se não estiver a utilizar os certificados (recomendamos que utilize os certificados!), pode ignorar esta verificação utilizando as opções de sessão: `-SkipCACheck -SkipCNCheck -SkipRevocationCheck` .

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
    ```

    > [!NOTE] 
    > Quando utiliza a `-UseSSL` opção, está a remoe através do PowerShell *em https*. Recomendamos que utilize sempre *https* para ligar remotamente através do PowerShell. 

6. Forneça a palavra-passe quando solicitado. Use a mesma palavra-passe que é usada para iniciar súm na UI web local. A senha de acesso web local padrão é *Password1*. Quando se liga com sucesso ao dispositivo utilizando o PowerShell remoto, vê a seguinte saída da amostra:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

> [!IMPORTANT]
> Na versão atual, pode ligar-se à interface PowerShell do dispositivo apenas através de um cliente Windows. A `-UseSSL` opção não funciona com os clientes Linux.

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->