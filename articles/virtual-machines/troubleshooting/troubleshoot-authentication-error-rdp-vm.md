---
title: Solucionar erros de autenticação ao usar o RDP para se conectar à VM do Azure | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard,csscontent
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 68037ab55918a76567f2dfee7cbda1d84d0c442e
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69908007"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Solucionar erros de autenticação ao usar o RDP para se conectar à VM do Azure

Este artigo pode ajudá-lo a solucionar problemas de erros de autenticação que ocorrem quando você usa a conexão protocolo RDP (RDP) para se conectar a uma VM (máquina virtual) do Azure.

## <a name="symptoms"></a>Sintomas

Você captura uma captura de tela de uma VM do Azure que mostra o ecrã de boas-vindas e indica que o sistema operacional está em execução. No entanto, ao tentar se conectar à VM usando Conexão de Área de Trabalho Remota, você receberá uma das seguintes mensagens de erro.

### <a name="error-message-1"></a>Mensagem de erro 1

**Ocorreu um erro de autenticação. Não é possível contatar a autoridade de segurança local.**

### <a name="error-message-2"></a>Mensagem de erro 2

**O computador remoto ao qual você está tentando se conectar requer Autenticação no Nível da Rede (NLA), mas o controlador de domínio do Windows não pode ser contatado para executar o NLA. Se você for um administrador no computador remoto, poderá desabilitar o NLA usando as opções na guia remoto da caixa de diálogo Propriedades do sistema.**

### <a name="error-message-3-generic-connection-error"></a>Mensagem de erro 3 (erro de conexão genérica)

**Este computador não pode se conectar ao computador remoto. Tente se conectar novamente, se o problema persistir, contate o proprietário do computador remoto ou o administrador da rede.**

## <a name="cause"></a>Causa

Há várias razões pelas quais a NLA pode bloquear o acesso de RDP a uma VM.

### <a name="cause-1"></a>Causa 1

A VM não pode se comunicar com o controlador de domínio (DC). Esse problema pode impedir que uma sessão RDP acesse uma VM usando credenciais de domínio. No entanto, você ainda poderá fazer logon usando as credenciais de administrador local. Esse problema pode ocorrer nas seguintes situações:

1. O canal de segurança Active Directory entre essa VM e o DC está desfeito.

2. A VM tem uma cópia antiga da senha da conta e o controlador de domínio tem uma cópia mais recente.

3. O controlador de domínio ao qual esta VM está se conectando não está íntegro.

### <a name="cause-2"></a>Causa 2

O nível de criptografia da VM é maior que aquele usado pelo computador cliente.

### <a name="cause-3"></a>Causa 3

Os protocolos TLS 1,0, 1,1 ou 1,2 (servidor) estão desabilitados na VM.

### <a name="cause-4"></a>Causa 4

A VM foi configurada para desabilitar o logon usando credenciais de domínio e a autoridade de segurança local (LSA) está configurada incorretamente.

### <a name="cause-5"></a>Causa 5

A VM foi configurada para aceitar somente conexões de algoritmo em conformidade com padrão FIPS (FIPS). Isso geralmente é feito usando Active Directory política. Essa é uma configuração rara, mas o FIPS pode ser aplicado somente para conexões Área de Trabalho Remotas.

## <a name="before-you-troubleshoot"></a>Antes de solucionar problemas

### <a name="create-a-backup-snapshot"></a>Criar um instantâneo de backup

Para criar um instantâneo de backup, siga as etapas em fazer o [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Conectar-se à VM remotamente

Para se conectar à VM remotamente, use um dos métodos em [como usar as ferramentas remotas para solucionar problemas de VM do Azure](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Serviço de cliente de diretiva de grupo

Se esta for uma VM ingressada no domínio, primeiro interrompa o serviço de cliente Política de Grupo para impedir que qualquer política de Active Directory substitua as alterações. Para tal, execute o seguinte comando:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Depois que o problema for corrigido, restaure a capacidade dessa VM para entrar em contato com o domínio para recuperar o GPO mais recente do domínio. Para fazer isso, execute os seguintes comandos:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Se a alteração for revertida, isso significa que uma política de Active Directory está causando o problema. 

### <a name="workaround"></a>Solução

Para contornar esse problema, execute os seguintes comandos na janela de comando para desabilitar a NLA:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Em seguida, reinicie a VM.

Para reabilitar o NLA, execute o seguinte comando e reinicie a VM:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="for-domain-joined-vms"></a>Para VMs ingressadas no domínio

Para solucionar esse problema, primeiro verifique se a VM pode se conectar a um controlador de domínio e se o controlador de domínio tem um status de "íntegro" e pode lidar com solicitações da VM.

>[!Note] 
>Para testar a integridade do controlador de domínio, você pode usar outra VM na mesma VNET e sub-rede que compartilham o mesmo servidor de logon.

Conecte-se à VM que tem o problema usando Console serial, CMD remoto ou PowerShell remoto, de acordo com as etapas na seção "conectar-se à VM remotamente".

Para determinar a qual DC a VM está se conectando, execute o seguinte comando no console do: 

```cmd
set | find /i "LOGONSERVER"
```

Em seguida, verifique a integridade do canal seguro entre a VM e o controlador de domínio. Para fazer isso, execute o seguinte comando em uma instância do PowerShell com privilégios elevados. Esse comando retorna um sinalizador booliano que indica se o canal seguro está ativo:

```powershell
Test-ComputerSecureChannel -verbose
```

Se o canal for interrompido, execute o seguinte comando para repará-lo:

```powershell
Test-ComputerSecureChannel -repair
```

Verifique se a senha da conta do computador no Active Directory está atualizada na VM e no controlador de domínio:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Se a comunicação entre o DC e a VM for boa, mas o controlador de domínio não estiver íntegro o suficiente para abrir uma sessão RDP, você poderá tentar reiniciar o controlador de domínio.

Se os comandos anteriores não corrigiram o problema de comunicação com o domínio, você poderá reingressar essa VM no domínio. Para tal, siga estes passos:

1. Crie um script chamado Unjoin. ps1 usando o conteúdo a seguir e, em seguida, implante o script como uma extensão de script personalizado no portal do Azure:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Esse script leva a VM do domínio de modo forçado e reinicia 10 segundos depois. Em seguida, você precisa limpar o objeto de computador no lado do domínio.

2.  Depois que a limpeza for concluída, reingresse essa VM no domínio. Para fazer isso, crie um script chamado JoinDomain. ps1 usando o conteúdo a seguir e, em seguida, implante o script como uma extensão de script personalizado no portal do Azure: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Isso une a VM no domínio usando as credenciais especificadas.

Se o canal de Active Directory estiver íntegro, a senha do computador será atualizada e o controlador de domínio estiver funcionando conforme o esperado, tente as etapas a seguir.

Se o problema persistir, verifique se a credencial do domínio está desabilitada. Para fazer isso, abra uma janela de prompt de comandos com privilégios elevados e execute o seguinte comando para determinar se a VM está configurada para desabilitar contas de domínio para fazer logon na VM:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Se a chave for definida como **1**, isso significa que o servidor foi configurado para não permitir credenciais de domínio. Altere essa chave para **0**.

### <a name="for-standalone-vms"></a>Para VMs autônomas

#### <a name="check-minencryptionlevel"></a>Verificar MinEncryptionLevel

Em uma instância do CMD, execute o seguinte comando para consultar o valor do registro **MinEncryptionLevel** :

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Com base no valor do registro, siga estas etapas:

* 4 (FIPS): Vá para [verificar conexões de algoritmos compatíveis com FIPS](#fips-compliant).

* 3 (criptografia de 128 bits): Defina a severidade como **2** executando o seguinte comando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (criptografia mais alta possível, conforme ditado pelo cliente): Você pode tentar definir a criptografia com o valor mínimo de **1** executando o seguinte comando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Reinicie a VM para que as alterações no registro entrem em vigor.

#### <a name="tls-version"></a>Versão do TLS

Dependendo do sistema, o RDP usa o protocolo TLS 1,0, 1,1 ou 1,2 (servidor). Para consultar como esses protocolos são configurados na VM, abra uma instância do CMD e execute os seguintes comandos:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Se os valores retornados não forem todos **1**, isso significará que o protocolo está desabilitado. Para habilitar esses protocolos, execute os seguintes comandos:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Para outras versões de protocolo, você pode executar os seguintes comandos:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Obtenha a versão x. x do SSH/TLS nos logs do sistema operacional convidado nos erros do SCHANNEL.

#### <a name="fips-compliant"></a>Verificar conexões de algoritmos em conformidade com FIPs

A área de trabalho remota pode ser imposta para usar somente conexões de algoritmo em conformidade com FIPs. Isso pode ser definido usando uma chave do registro. Para fazer isso, abra uma janela de prompt de comandos com privilégios elevados e consulte as seguintes chaves:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Se o comando retornar **1**, altere o valor do registro para **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Verifique qual é o MinEncryptionLevel atual na VM:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Se o comando retornar **4**, altere o valor do registro para **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Reinicie a VM para que as alterações no registro entrem em vigor.

## <a name="next-steps"></a>Passos Seguintes

[Método SetEncryptionLevel da classe Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Configurar os níveis de autenticação e criptografia do servidor](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Classe Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
