---
title: Resolver erros de autenticação ao utilizar o RDP para ligar à VM do Azure | Documentos da Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 47d3b827099d3a4a7520ac66765d2928795b6e49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60594930"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Resolver erros de autenticação ao utilizar o RDP para ligar à VM do Azure

Este artigo pode ajudá-lo a resolver erros de autenticação que ocorrem quando usa a ligação de protocolo RDP (Remote Desktop) para ligar a uma máquina virtual do Azure (VM).

## <a name="symptoms"></a>Sintomas

Capturar uma captura de ecrã da VM do Azure que mostra o ecrã de boas-vindas e indica que o sistema operativo está em execução. No entanto, quando tentar ligar à VM com a conexão de área de trabalho remoto, receberá uma das seguintes mensagens de erro.

### <a name="error-message-1"></a>Mensagem de erro 1

**Ocorreu um erro de autenticação. Não é possível contactar a autoridade de segurança Local.**

### <a name="error-message-2"></a>Mensagem de erro 2

**O computador remoto que está a tentar ligar ao requer autenticação de nível de rede (NLA), mas não é possível contactar o seu controlador de domínio do Windows para executar o NLA. Se for um administrador no computador remoto, pode desativar o NLA utilizando as opções no separador remoto da caixa de diálogo de propriedades do sistema.**

### <a name="error-message-3-generic-connection-error"></a>Mensagem de erro 3 (erro de ligação genérico)

**Este computador não é possível ligar ao computador remoto. Tente ligar novamente, se o problema persistir, contacte o proprietário do computador remoto ou o administrador da rede.**

## <a name="cause"></a>Causa

Há vários motivos por que motivo o NLA poderão bloquear o acesso RDP a uma VM.

### <a name="cause-1"></a>Causa 1

A VM não consegue comunicar com o controlador de domínio (DC). Esse problema pode impedir que uma sessão RDP acedam a uma VM com credenciais de domínio. No entanto, ainda seria capaz de iniciar sessão com as credenciais de administrador Local. Esse problema pode ocorrer nas seguintes situações:

1. O canal de segurança de diretório do Active Directory entre esta VM e o controlador de domínio está quebrado.

2. A VM tem uma cópia antiga da palavra-passe de conta e o controlador de domínio tem uma cópia mais recente.

3. O controlador de domínio que está a ligar a esta VM está danificado.

### <a name="cause-2"></a>Causa 2

O nível de encriptação da VM é maior do que aquele que é utilizado pelo computador cliente.

### <a name="cause-3"></a>Causa 3

Os protocolos TLS 1.0, 1.1 ou 1.2 (servidor) estão desativados na VM.

### <a name="cause-4"></a>Causa 4

A VM tiver sido configurada para desativar o início de sessão com credenciais de domínio e a autoridade de segurança Local (LSA) é configurado incorretamente.

### <a name="cause-5"></a>Causa 5

A VM tiver sido configurada para aceitar apenas Federal Information Processing Standard (FIPS)-ligações de algoritmo de conformidade. Normalmente, isso é feito utilizando a política do Active Directory. Esta é uma configuração rara, mas FIPS podem ser imposto para apenas ligações de ambiente de trabalho remoto.

## <a name="before-you-troubleshoot"></a>Antes de resolver problemas

### <a name="create-a-backup-snapshot"></a>Criar um instantâneo de cópia de segurança

Para criar um instantâneo de cópia de segurança, siga os passos em [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Ligar remotamente à VM

Para ligar remotamente à VM, utilize um dos métodos na [como utilizar ferramentas remotas para resolver problemas de VM do Azure](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Serviço de cliente de política de grupo

Se se tratar de uma VM associados a um domínio, primeiro pare o serviço de cliente de política de grupo para impedir que qualquer política do Active Directory substitua as alterações. Para tal, execute o seguinte comando:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Depois do problema é resolvido, restaure a capacidade desta VM entre em contato com o domínio para obter o GPO mais recente do domínio. Para tal, execute os seguintes comandos:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Se a alteração é revertida, significa que uma política do Active Directory está a causar o problema. 

### <a name="workaround"></a>Solução

Para contornar este problema, execute os seguintes comandos na janela de comando para desativar o NLA:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Em seguida, reinicie a VM.

Para reativar o NLA, execute o seguinte comando e, em seguida, reinicie a VM:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="for-domain-joined-vms"></a>Para VMs associados a um domínio

Para resolver este problema, verifique primeiro se a VM pode ligar a um controlador de domínio e se o controlador de domínio tem o estado de "integridade" e pode processar pedidos da VM.

>[!Note] 
>Para testar o estado de funcionamento do controlador de domínio, pode utilizar outra VM na mesma VNET e sub-rede que partilham o mesmo servidor de início de sessão.

Para ligar à VM que tem o problema ao utilizar a consola de série, o CMD remoto ou o PowerShell remoto, de acordo com os passos na secção "Ligar remotamente à VM".

Para determinar qual controlador de domínio a VM está a ligar a, execute o seguinte comando na consola do: 

```cmd
set | find /i "LOGONSERVER"
```

Em seguida, verifique o estado de funcionamento de um canal seguro entre a VM e o controlador de domínio. Para tal, execute o seguinte comando numa instância elevada do PowerShell. Este comando devolve um sinalizador booleano que indica se o canal seguro está ativo:

```powershell
Test-ComputerSecureChannel -verbose
```

Se o canal for interrompido, execute o seguinte comando para o reparar:

```powershell
Test-ComputerSecureChannel -repair
```

Certifique-se de que a palavra-passe de conta do computador no Active Directory é atualizada no VM e o controlador de domínio:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Se a comunicação entre o controlador de domínio e a VM é bom, mas o controlador de domínio não é bom o suficiente para abrir uma sessão RDP, pode tentar reiniciar o controlador de domínio.

Se os comandos anteriores não corrigir o problema de comunicação ao domínio, pode voltar a participar desta VM ao domínio. Para tal, siga estes passos:

1. Criar um script com o nome Unjoin.ps1 utilizando o seguinte conteúdo e, em seguida, implementar o script como uma extensão de Script personalizado no portal do Azure:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Este script demora a forçar a VM fora do domínio e reinicia-10 segundos mais tarde. Em seguida, terá de limpar o objeto de computador no lado do domínio.

2.  Depois da limpeza estiver concluída, voltar a participar desta VM ao domínio. Para tal, crie um script com o nome JoinDomain.ps1 utilizando o seguinte conteúdo e, em seguida, implementar o script como uma extensão de Script personalizado no portal do Azure: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Isso é associado a VM no domínio utilizando as credenciais especificadas.

Se o canal do Active Directory está em bom estado, a palavra-passe do computador é atualizada e o controlador de domínio está a funcionar conforme esperado, tente os passos seguintes.

Se o problema persistir, verifique se a credencial de domínio está desativada. Para tal, abra uma janela de linha de comandos elevada e, em seguida, execute o seguinte comando para determinar se a VM é definida para desativar as contas de domínio para o registo de sessão na VM:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Se a chave está definida como **1**, isso significa que o servidor foi definido até para não permitir credenciais de domínio. Alterar esta chave para **0**.

### <a name="for-standalone-vms"></a>Para uma VM autónoma

#### <a name="check-minencryptionlevel"></a>Verificação MinEncryptionLevel

Numa instância CMD, execute o seguinte comando para consultar o **MinEncryptionLevel** valor de registo:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Com base no valor de registo, siga estes passos:

* 4 (FIPS): Aceda a [ligações de algoritmos em conformidade com FIPs verificar](#fips-compliant).

* 3 (encriptação de 128 bits): Definir a gravidade **2** executando o seguinte comando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (mais alta encriptação possível, conforme ditado pelo cliente): Pode tentar definir a encriptação para o valor mínimo de **1** executando o seguinte comando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Reinicie a VM para que as alterações no Registro em vigor.

#### <a name="tls-version"></a>Versão do TLS

Dependendo do sistema, o RDP utiliza o protocolo TLS 1.0, 1.1 ou 1.2 (servidor). Para consultar como esses protocolos são configurados na VM, abra uma instância CMD e, em seguida, execute os seguintes comandos:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Se os valores devolvidos não são todas **1**, isso significa que o protocolo está desabilitado. Para habilitar esses protocolos, execute os seguintes comandos:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Para outras versões de protocolo, pode executar os seguintes comandos:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Obtenha o x de versão do SSH/TLS de registos do SO convidado sobre os erros SCHANNEL.

#### <a name="fips-compliant"></a> Verifique as ligações de algoritmos em conformidade com FIPs

Ambiente de trabalho remoto pode ser imposta para utilizar apenas ligações de algoritmo compatível com FIPs. Isto pode ser definido com uma chave de registo. Para fazer isso, abra uma janela de linha de comandos elevada e, em seguida, consultar as seguintes chaves:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Se o comando devolve **1**, altere o valor de registo para **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Verificação de que é o atual MinEncryptionLevel na VM:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Se o comando devolve **4**, altere o valor de registo para **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Reinicie a VM para que as alterações no Registro em vigor.

## <a name="next-steps"></a>Passos Seguintes

[Método SetEncryptionLevel da classe Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Configurar a autenticação de servidor e níveis de encriptação](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Classe de Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
