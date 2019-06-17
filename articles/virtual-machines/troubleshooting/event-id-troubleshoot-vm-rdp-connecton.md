---
title: Resolver problemas de ligação de RDP da VM do Azure por ID de evento | Documentos da Microsoft
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
ms.openlocfilehash: 4c783c70217a84bbe5ccf15accc4a2bec0b7cca8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61485516"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Resolver problemas de ligação de RDP da VM do Azure por ID de Evento 

Este artigo explica como utilizar os IDs de eventos para resolver os problemas que impedem uma ligação de protocolo (RDP) do ambiente de trabalho remoto para uma Máquina Virtual do Azure (VM).

## <a name="symptoms"></a>Sintomas

Tente utilizar uma sessão de protocolo (RDP) do ambiente de trabalho remoto para ligar a uma VM do Azure. Depois de introduzir as suas credenciais, a ligação falhar e receberá a seguinte mensagem de erro:

**Este computador não é possível ligar ao computador remoto. Tente ligar novamente, se o problema persistir, contacte o proprietário do computador remoto ou o administrador da rede.**

Para resolver este problema, reveja os registos de eventos na VM e, em seguida, consulte os seguintes cenários.

## <a name="before-you-troubleshoot"></a>Antes de resolver problemas

### <a name="create-a-backup-snapshot"></a>Criar um instantâneo de cópia de segurança

Para criar um instantâneo de cópia de segurança, siga os passos em [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Ligar remotamente à VM

Para ligar remotamente à VM, utilize um dos métodos na [como utilizar ferramentas remotas para resolver problemas de VM do Azure](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Cenário 1

### <a name="event-logs"></a>Registos de eventos

Numa instância CMD, execute os seguintes comandos para verificar se o evento 1058 ou evento 1057 é registado no registo do sistema dentro das últimas 24 horas:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Sistema <br />
**Origem:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:** *tempo* <br />
**ID de evento:**      1058 <br />
**Categoria de tarefa:** Nenhuma <br />
**Nível de:**         Erro <br />
**Palavras-chave:**      Clássica <br />
**Utilizador:**          N/A <br />
**Computador:** *computador* <br />
**Descrição:** O servidor de anfitrião de sessões de RD não substitua auto assinado o certificado utilizado para autenticação de servidor de anfitrião de sessões de RD em ligações de SSL expirados. O código de estado relevantes era que o acesso é negado.

**Nome do registo:**      Sistema <br />
**Origem:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:** *tempo* <br />
**ID de evento:**      1058 <br />
**Categoria de tarefa:** Nenhuma <br />
**Nível de:**         Erro <br />
**Palavras-chave:**      Clássica <br />
**Utilizador:**          N/A <br />
**Computador:** *computador* <br />
**Descrição:** Servidor de anfitrião de sessões de RD não conseguiu criar um novo certificado autoassinado a ser utilizado para autenticação de servidor de anfitrião de sessões de RD em ligações de SSL, o código de estado relevantes foi o objeto já existe.

**Nome do registo:**      Sistema <br />
**Origem:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:** *tempo* <br />
**ID de evento:**      1057 <br />
**Categoria de tarefa:** Nenhuma <br />
**Nível de:**         Erro <br />
**Palavras-chave:**      Clássica <br />
**Utilizador:**          N/A <br />
**Computador:** *computador* <br />
**Descrição:** O servidor de anfitrião de sessões de RD não conseguiu criar um novo que certificado a ser utilizado para autenticação de servidor de anfitrião de sessões de RD em ligações de SSL auto assinado. O código de estado relevantes foi o que conjunto de chaves não existe

Também pode procurar eventos de erro SCHANNEL 36872 e 36870 ao executar os comandos seguintes:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Sistema <br />
**Origem:**        Schannel <br />
**Data:** — <br />
**ID de evento:**      36870 <br />
**Categoria de tarefa:** Nenhuma <br />
**Nível de:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SISTEMA <br />
**Computador:** *computador* <br />
**Descrição:** Ocorreu um erro fatal ao tentar aceder à chave privada do servidor credencial SSL. O código de erro devolvido do módulo criptográfico é 0x8009030D.  <br />
O estado de erro interno é 10001.

### <a name="cause"></a>Causa
Este problema ocorre porque não não possível aceder as chaves de encriptação RSA locais na pasta MachineKeys na VM. Este problema pode ocorrer para um dos seguintes motivos:

1. Configuração de permissões erradas na pasta Machinekeys ou os ficheiros RSA.

2. Chave RSA de danificado ou em falta.

### <a name="resolution"></a>Resolução

Para resolver este problema, terá de configurar as permissões corretas no certificado RDP com estes passos.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Conceder permissão para a pasta MachineKeys

1. Crie um script, utilize o seguinte conteúdo:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Execute este script para repor as permissões da pasta MachineKey e para repor os ficheiros RSA para os valores predefinidos.

3.  Tente aceder à VM novamente.

Depois de executar o script, pode verificar os seguintes ficheiros que estão a ter problemas de permissões:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Renovar o certificado autoassinado do RDP

Se o problema persistir, execute o seguinte script para se certificar de que o certificado autoassinado do RDP é renovado:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Se não é possível renovar o certificado, siga estes passos para tentar eliminar o certificado:

1. Em outra VM na mesma VNET, abra a **execute** , escreva **mmc**e, em seguida, prima **OK**. 

2. Sobre o **arquivo** menu, selecione **Adicionar/Remover Snap-in**.

3. Na **snap-Ins disponíveis** lista, selecione **certificados**e, em seguida, selecione **Add**.

4. Selecione **conta de computador**e, em seguida, selecione **próxima**.

5. Selecione **noutro computador**e, em seguida, adicione o endereço IP da VM que tem problemas.
   >[!Note]
   >Tente utilizar a rede interna para evitar o uso de um endereço IP virtual.

6. Selecione **Finish**e, em seguida, selecione **OK**.

   ![Selecione o computador](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Expanda os certificados, aceda à pasta Desktop\Certificates remoto, o certificado com o botão direito e, em seguida, selecione **eliminar**.

8. Reinicie o serviço de configuração do ambiente de trabalho remoto:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Neste momento, se atualizar o arquivo do mmc, o certificado será exibida novamente. 

Tente aceder à VM através de RDP novamente.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Atualizar o certificado de Secure Sockets Layer (SSL)

Se configurar a VM para utilizar um certificado SSL, execute o seguinte comando para obter o thumbprint. Em seguida, verifique se é o mesmo que o thumbprint do certificado:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Se não estiver, altere a impressão digital:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Também pode tentar eliminar a chave para que o RDP utiliza o certificado autoassinado para RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Cenário 2

### <a name="event-log"></a>Registo de eventos

Numa instância CMD, execute os seguintes comandos para verificar se o evento de erro SCHANNEL 36871 é registado no registo do sistema dentro das últimas 24 horas:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Sistema <br />
**Origem:**        Schannel <br />
**Data:** — <br />
**ID de evento:**      36871 <br />
**Categoria de tarefa:** Nenhuma <br />
**Nível de:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SISTEMA <br />
**Computador:** *computador* <br />
**Descrição:** Ocorreu um erro fatal ao criar uma credencial de servidor TLS. O estado de erro interno é 10013.
 
### <a name="cause"></a>Causa

Este problema é causado pelas políticas de segurança. Quando as versões mais antigas do TLS (por exemplo, 1.0) estão desativadas, falha de acesso do RDP.

### <a name="resolution"></a>Resolução

RDP utiliza o TLS 1.0 como o protocolo predefinido. No entanto, o protocolo pode ser alterado para TLS 1.1, que é o novo padrão.

Para resolver este problema, consulte [resolver erros de autenticação ao utilizar o RDP para ligar à VM do Azure](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Cenário 3

Se tiver instalado o **Mediador de ligações de ambiente de trabalho remoto** função na VM, verifique se existe o evento 2056 ou evento 1296 dentro das últimas 24 horas. Numa instância CMD, execute os seguintes comandos: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Origem:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Data:** *tempo* <br />
**ID de evento:**      2056 <br />
**Categoria de tarefa:** (109) <br />
**Nível de:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SERVIÇO DE REDE <br />
**Computador:** *fqdn do computador* <br />
**Descrição:** A descrição do evento 2056 de ID de origem não é possível localizar o Microsoft-Windows-TerminalServices-SessionBroker. O componente que gera esse evento não está instalado no seu computador local ou a instalação está danificada. Pode instalar ou reparar o componente no computador local. <br />
Se a origem do evento em outro computador, tinham de apresentar as informações sejam guardados com o evento. <br />
As seguintes informações foi incluídas com o evento: <br />
NULL <br />
NULL <br />
Falha de início de sessão para a base de dados.

**Nome do registo:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Origem:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Data:** *tempo* <br />
**ID de evento:**      1296 <br />
**Categoria de tarefa:** (104) <br />
**Nível de:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SERVIÇO DE REDE <br />
**Computador:** *fqdn do computador* <br />
**Descrição:** A descrição do evento 1296 de ID de origem não é possível localizar o Microsoft-Windows-TerminalServices-SessionBroker-Client. O componente que gera esse evento não está instalado no seu computador local ou a instalação está danificada. Pode instalar ou reparar o componente no computador local.
Se a origem do evento em outro computador, tinham de apresentar as informações sejam guardados com o evento.
As seguintes informações foi incluídas com o evento:  <br />
*text* <br />
*text* <br />
Mediador de ligações de ambiente de trabalho remoto não está pronto para comunicações de RPC.

### <a name="cause"></a>Causa

Este problema ocorre porque o nome de anfitrião do servidor de Mediador de ligações de ambiente de trabalho remoto é alterado, que não é uma alteração suportada. 

O nome do anfitrião tem entradas e as dependências no Windows interno base de dados, é necessário pelo farm de serviço de ambiente de trabalho remoto para poder trabalhar. Alterar o nome de anfitrião depois do farm já está incorporado faz com que muitos erros e pode fazer com que o servidor de Mediador deixe de funcionar.

### <a name="resolution"></a>Resolução 

Para corrigir este problema, a função de Mediador de ligações de ambiente de trabalho remoto e a base de dados interna do Windows tem de ser reinstalado.

## <a name="next-steps"></a>Próximos Passos

[Eventos de Schannel](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Visão geral técnica do Schannel SSP](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[Falha de RDP com o ID de evento 1058 & evento 36870 com comunicação SSL & de certificado de anfitrião de sessões de ambiente de trabalho remoto](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[O Schannel 36872 ou Schannel 36870 num controlador de domínio](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[ID de evento 1058 — Dos serviços de ambiente de trabalho remoto autenticação e encriptação](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

