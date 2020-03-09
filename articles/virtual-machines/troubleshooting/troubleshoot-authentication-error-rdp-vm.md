---
title: Erros de autenticação de resolução de problemas quando utiliza RDP para ligar ao Azure VM  Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: b7a561907e3f1968eb9adead3606822d7a1321c8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381659"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Resolver problemas de autenticação quando utiliza o protocolo RDP (Remote Desktop Protocol) para se ligar à VM do Azure

Este artigo pode ajudá-lo a resolver erros de autenticação que ocorrem quando utiliza a ligação do Protocolo de Ambiente de Trabalho Remoto (RDP) para se ligar a uma máquina virtual Azure (VM).

## <a name="symptoms"></a>Sintomas

Captura-se uma imagem de um VM Azure que mostra o ecrã Welcome e indica que o sistema operativo está em funcionamento. No entanto, quando tenta ligar-se ao VM utilizando a Ligação remota de ambiente de trabalho, recebe uma das seguintes mensagens de erro.

### <a name="error-message-1"></a>Mensagem de erro 1

**Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contactada.**

### <a name="error-message-2"></a>Mensagem de erro 2

**O computador remoto a que está a tentar ligar requer autenticação de nível de rede (NLA), mas o seu controlador de domínio Windows não pode ser contactado para executar o NLA. Se for administrador no computador remoto, pode desativar o NLA utilizando as opções no separador Remoto da caixa de diálogo System Properties.**

### <a name="error-message-3-generic-connection-error"></a>Mensagem de erro 3 (erro genérico de ligação)

**Este computador não pode ligar-se ao computador remoto. Tente ligar-se novamente, se o problema continuar, contacte o proprietário do computador remoto ou o seu administrador de rede.**

## <a name="cause"></a>Causa

Existem várias razões pelas quais a NLA pode bloquear o acesso rdp a um VM.

### <a name="cause-1"></a>Causa 1

O VM não pode comunicar com o controlador de domínio (DC). Este problema poderia impedir que uma sessão de RDP aceda a um VM utilizando credenciais de domínio. No entanto, ainda seria capaz de iniciar sessão utilizando as credenciais do Administrador Local. Este problema pode ocorrer nas seguintes situações:

1. O Canal de Segurança do Diretório Ativo entre este VM e o DC está avariado.

2. O VM tem uma cópia antiga da senha da conta e o DC tem uma cópia mais recente.

3. O DC a que este VM está a ligar não é saudável.

### <a name="cause-2"></a>Causa 2

O nível de encriptação do VM é maior do que o que é usado pelo computador cliente.

### <a name="cause-3"></a>Causa 3

Os protocolos TLS 1.0, 1.1 ou 1.2 (servidor) são desativados no VM.

### <a name="cause-4"></a>Causa 4

O VM foi criado para desativar o login utilizando credenciais de domínio, e a Autoridade de Segurança Local (LSA) é criada incorretamente.

### <a name="cause-5"></a>Causa 5

O VM foi criado para aceitar apenas ligações de algoritmos compatíveis com a Norma Federal de Processamento de Informação (FIPS). Isto é geralmente feito utilizando a política de Diretório Ativo. Esta é uma configuração rara, mas os FIPS podem ser aplicados apenas para ligações de ambiente de trabalho remoto.

## <a name="before-you-troubleshoot"></a>Antes de resolver problemas

### <a name="create-a-backup-snapshot"></a>Criar um instantâneo de backup

Para criar uma imagem de backup, siga os passos em [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Ligue-se ao VM remotamente

Para ligar remotamente ao VM, utilize um dos métodos em [Como utilizar ferramentas remotas para resolver problemas com os problemas do Azure VM](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Serviço de cliente de política de grupo

Se este for um VM filiado em domínios, primeiro pare o serviço de Cliente de Política de Grupo para evitar que qualquer Política de Diretório Ativo sobreamente as alterações. Para tal, execute o seguinte comando:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Depois de o problema ser corrigido, restaure a capacidade deste VM de contactar o domínio para recuperar o mais recente GPO do domínio. Para tal, execute os seguintes comandos:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Se a mudança for revertida, significa que uma política de Diretório Ativo está a causar o problema. 

### <a name="workaround"></a>Solução

Para resolver este problema, execute os seguintes comandos na janela de comando para desativar a NLA:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Em seguida, reinicie o VM.

Para reativar o NLA, executar o seguinte comando e, em seguida, reiniciar o VM:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="for-domain-joined-vms"></a>Para VMs unidos pelo domínio

Para resolver este problema, verifique primeiro se o VM pode ligar-se a um DC e se o DC tem um estatuto de "saudável" e pode lidar com pedidos do VM.

>[!Note] 
>Para testar a saúde em DC, pode utilizar outro VM no mesmo VNET e Subnet que partilham o mesmo servidor de logon.

Ligue-se ao VM que tem o problema utilizando a consola serial, a CMD remota ou a PowerShell remota, de acordo com os passos na secção "Ligar à VM remotamente".

Para determinar a que DC o VM está a ligar, execute o seguinte comando na consola: 

```cmd
set | find /i "LOGONSERVER"
```

Em seguida, verifique a saúde do canal seguro entre o VM e o DC. Para tal, execute o seguinte comando num caso elevado de PowerShell. Este comando devolve uma bandeira booleana que indica se o canal seguro está vivo:

```powershell
Test-ComputerSecureChannel -verbose
```

Se o canal estiver avariado, execute o seguinte comando para repará-lo:

```powershell
Test-ComputerSecureChannel -repair
```

Certifique-se de que a palavra-passe da conta de computador no Diretório Ativo é atualizada no VM e no DC:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Se a comunicação entre o DC e o VM for boa, mas o DC não for suficientemente saudável para abrir uma sessão de RDP, pode tentar reiniciar o DC.

Se os comandos anteriores não corrigirem o problema de comunicação ao domínio, pode voltar a juntar este VM ao domínio. Para tal, siga estes passos:

1. Crie um script que se chame Unjoin.ps1 utilizando o seguinte conteúdo e, em seguida, implemente o script como uma extensão de script personalizado no portal Azure:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Este guião tira o VM do domínio à força e reinicia-o 10 segundos depois. Depois, tens de limpar o objeto do Computador do lado do domínio.

2.  Depois da limpeza, volte a juntar-se a este VM ao domínio. Para isso, crie um script que se chame JoinDomain.ps1 utilizando o seguinte conteúdo e, em seguida, implemente o script como uma extensão de script personalizado no portal Azure: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Isto une o VM no domínio utilizando as credenciais especificadas.

Se o canal ative diretório estiver saudável, a palavra-passe do computador é atualizada e o controlador de domínio está a funcionar como esperado, tente os seguintes passos.

Se o problema persistir, verifique se a credencial de domínio está desativada. Para tal, abra uma janela de comando elevado e, em seguida, executar o seguinte comando para determinar se o VM está configurado para desativar contas de domínio para iniciar sessão no VM:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Se a chave estiver definida para **1,** isto significa que o servidor foi configurado para não permitir credenciais de domínio. Mude esta chave para **0**.

### <a name="for-standalone-vms"></a>Para VMs autónomos

#### <a name="check-minencryptionlevel"></a>Verifique o MinEncryptionLevel

Num caso CMD, execute o seguinte comando para consultar o valor do registo **MinCryptonLevel:**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Com base no valor do registo, siga estes passos:

* 4 (FIPS): Vá verificar as ligações de [algoritmos compatíveis](#fips-compliant)com os FIPs .

* 3 (encriptação de 128 bits): Desloque a gravidade para **2** executando o seguinte comando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (Encriptação mais elevada possível, conforme ditado pelo cliente): Pode tentar definir a encriptação para o valor mínimo de **1** executando o seguinte comando:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Reinicie o VM de modo a que as alterações ao registo entrem em vigor.

#### <a name="tls-version"></a>Versão TLS

Dependendo do sistema, o RDP utiliza o protocolo TLS 1.0, 1.1 ou 1.2 (servidor). Para consultar como estes protocolos são estabelecidos no VM, abra uma instância CMD e, em seguida, executar os seguintes comandos:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Se os valores devolvidos não forem todos **1,** isto significa que o protocolo está desativado. Para ativar estes protocolos, execute os seguintes comandos:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Para outras versões protocolares, pode executar os seguintes comandos:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Obtenha a versão SSH/TLS x.x a partir dos registos do OS Do Hóspede nos erros Do SCHANNEL.

#### <a name="fips-compliant"></a>Verifique as ligações de algoritmos compatíveis com os FIPs

O ambiente de trabalho remoto pode ser aplicado para utilizar apenas ligações de algoritmo compatíveis com OSFiPs. Isto pode ser definido utilizando uma chave de registo. Para isso, abra uma janela elevada de comando pronta e, em seguida, questione as seguintes teclas:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Se o comando devolver **1**, altere o valor do registo para **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Verifique qual é o atual MinEncryptionLevel no VM:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Se o comando devolver **4**, altere o valor do registo para **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Reinicie o VM de modo a que as alterações ao registo entrem em vigor.

## <a name="next-steps"></a>Passos seguintes

[Método de SetEncryptionLevel da classe Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Configure os níveis de autenticação e encriptação do servidor](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting aula](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
