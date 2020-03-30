---
title: Problemas Desfilmam problemas de ligação Azure VM RDP por Id de eventos [ Microsoft Docs
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
ms.openlocfilehash: 166648402eec7f8033c090a3f7862a902bae4be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154199"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Resolver problemas de ligação de RDP da VM do Azure por ID de Evento 

Este artigo explica como usar iDs de eventos para resolver problemas que impedem uma ligação remota de protocolo de ambiente de trabalho (RDP) a uma Máquina Virtual Azure (VM).

## <a name="symptoms"></a>Sintomas

Tente utilizar uma sessão de protocolo de ambiente de trabalho remoto (RDP) para se ligar a um VM Azure. Depois de inseri-lo as suas credenciais, a ligação falha e recebe a seguinte mensagem de erro:

**Este computador não pode ligar-se ao computador remoto. Tente ligar-se novamente, se o problema continuar, contacte o proprietário do computador remoto ou o seu administrador de rede.**

Para resolver esta questão, reveja os registos do evento no VM e, em seguida, consulte os seguintes cenários.

## <a name="before-you-troubleshoot"></a>Antes de resolver problemas

### <a name="create-a-backup-snapshot"></a>Criar um instantâneo de backup

Para criar uma imagem de backup, siga os passos em [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Ligue-se ao VM remotamente

Para ligar remotamente ao VM, utilize um dos métodos em [Como utilizar ferramentas remotas para resolver problemas com os problemas do Azure VM](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Cenário 1

### <a name="event-logs"></a>Registos de eventos

Em caso de CMD, execute os seguintes comandos para verificar se o evento 1058 ou o evento 1057 está registado no registo do Sistema nas últimas 24 horas:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Sistema <br />
**Fonte:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *hora* <br />
**ID do evento:** 1058 <br />
**Categoria de Tarefa:** Nenhuma. <br />
**Nível:**         Erro <br />
**Palavras-chave:**      Clássico <br />
**Utilizador:**          N/A <br />
**Computador:**      *computador* <br />
**Descrição:** O Servidor de Anfitriões de Sessão RD não substituiu o certificado auto-assinado expirado utilizado para a autenticação do Servidor de Anfitriões de Sessão RD nas ligações SSL. O código de estado relevante foi o Acesso negado.

**Nome do registo:**      Sistema <br />
**Fonte:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *hora* <br />
**ID do evento:** 1058 <br />
**Categoria de Tarefa:** Nenhuma. <br />
**Nível:**         Erro <br />
**Palavras-chave:**      Clássico <br />
**Utilizador:**          N/A <br />
**Computador:**      *computador* <br />
**Descrição:** O servidor de anfitriões rd Session não conseguiu criar um novo certificado auto-assinado para ser usado para autenticação do servidor de anfitriões RD Session em ligações SSL, o código de estado relevante já existia.

**Nome do registo:**      Sistema <br />
**Fonte:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *hora* <br />
**ID do evento:** 1057 <br />
**Categoria de Tarefa:** Nenhuma. <br />
**Nível:**         Erro <br />
**Palavras-chave:**      Clássico <br />
**Utilizador:**          N/A <br />
**Computador:**      *computador* <br />
**Descrição:** O Servidor de Anfitriões de Sessão RD não conseguiu criar um novo certificado auto-assinado para ser usado para autenticação do Servidor de Anfitriões de Sessão RD em ligações SSL. O código de estado relevante era Keyset não existe

Também pode verificar se os erros do SCHANNEL 36872 e 36870 executam os seguintes comandos:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Sistema <br />
**Fonte:**        Canal <br />
**Data:** — <br />
**ID do evento:** 36870 <br />
**Categoria de Tarefa:** Nenhuma. <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SISTEMA <br />
**Computador:**      *computador* <br />
**Descrição:** Ocorreu um erro fatal ao tentar aceder à chave privada credencial do servidor SSL. O código de erro devolvido do módulo criptográfico é 0x8009030D.  <br />
O estado de erro interno é 10001.

### <a name="cause"></a>Causa
Este problema ocorre porque as chaves de encriptação RSA locais na pasta MachineKeys no VM não podem ser acedidas. Esta questão pode ocorrer por uma das seguintes razões:

1. Configuração de permissões erradas na pasta Machinekeys ou nos ficheiros RSA.

2. Chave RSA corrompida ou desaparecida.

### <a name="resolution"></a>Resolução

Para resolver este problema, tem de configurar as permissões corretas no Certificado RDP utilizando estes passos.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Conceder permissão à pasta MachineKeys

1. Criar um script utilizando o seguinte conteúdo:

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

2.  Executar este script para redefinir as permissões da pasta MachineKey e redefinir os ficheiros RSA para os valores predefinidos.

3.  Tente aceder ao VM de novo.

Depois de executar o script, pode verificar os seguintes ficheiros que estão a experimentar problemas de permissões:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Renovar certificado auto-assinado RDP

Se o problema persistir, execute o seguinte guião para se certificar de que o certificado auto-assinado rdp é renovado:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Se não conseguir renovar o certificado, siga estes passos para tentar apagar o certificado:

1. Em outro VM no mesmo VNET, abra a caixa **Run,** escreva **mmc,** e, em seguida, pressione **OK**. 

2. No menu **'Ficheiro',** selecione **Adicionar/Remover o Snap-in**.

3. Na lista **de snap-ins disponíveis,** selecione **Certificados,** e, em seguida, selecione **Adicionar**.

4. Selecione **a conta de computador,** e, em seguida, selecione **Next**.

5. Selecione **Outro computador**e, em seguida, adicione o endereço IP do VM que tem problemas.
   >[!Note]
   >Tente utilizar a rede interna para evitar a utilização de um endereço IP virtual.

6. Selecione **Terminar,** e depois selecione **OK**.

   ![Selecione Computador](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Expanda os certificados, vá para a pasta Remote Desktop\Certificates, clique no certificado à direita e, em seguida, selecione **Delete**.

8. Reiniciar o serviço de configuração de ambiente de trabalho remoto:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Neste ponto, se refrescar a loja a partir do MMC, o certificado reaparece. 

Tente aceder ao VM utilizando novamente o RDP.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Certificado de camada de camada de tomadas seguras (SSL)

Se configurar o VM para utilizar um certificado SSL, execute o seguinte comando para obter a impressão digital. Então verifique se é o mesmo que a impressão digital do certificado:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Se não for, mude a impressão digital:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Também pode tentar apagar a chave para que o PDR utilize o certificado auto-assinado para RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Cenário 2

### <a name="event-log"></a>Registo de eventos

Num caso CMD, execute os seguintes comandos para verificar se o erro do SCHANNEL 36871 está registado no registo do Sistema nas últimas 24 horas:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Sistema <br />
**Fonte:**        Canal <br />
**Data:** — <br />
**ID do evento:** 36871 <br />
**Categoria de Tarefa:** Nenhuma. <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SISTEMA <br />
**Computador:**      *computador* <br />
**Descrição:** Um erro fatal ocorreu ao criar uma credencial de servidor TLS. O estado de erro interno é 10013.
 
### <a name="cause"></a>Causa

Esta questão é causada por políticas de segurança. Quando as versões mais antigas de TLS (como 1.0) são desativadas, o acesso rdp falha.

### <a name="resolution"></a>Resolução

RdP usa TLS 1.0 como o protocolo padrão. No entanto, o protocolo pode ser alterado para TLS 1.1, que é o novo padrão.

Para resolver este problema, consulte erros de [autenticação troubleshoot quando utilizar RDP para ligar ao Azure VM](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Cenário 3

Se instalou a função de Corretor de **Ligação** remota no VM, verifique se há evento 2056 ou evento 1296 nas últimas 24 horas. Num caso CMD, executar os seguintes comandos: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Microsoft-Windows-TerminalServices-SessionBroker/Operacional <br />
**Fonte:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Data:**          *hora* <br />
**ID do evento:** 2056 <br />
**Categoria tarefa:** (109) <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SERVIÇO DE REDE <br />
**Computador:**      *fqdn do computador* <br />
**Descrição:** A descrição do Evento ID 2056 da fonte Microsoft-Windows-TerminalServices-SessionBroker não pode ser encontrada. Ou o componente que levanta este evento não está instalado no seu computador local ou a instalação é corrompida. Pode instalar ou reparar o componente no computador local. <br />
Se o evento teve origem noutro computador, as informações do visor tiveram de ser guardadas com o evento. <br />
As seguintes informações foram incluídas no evento: <br />
NULL <br />
NULL <br />
O logon na base de dados falhou.

**Nome do registo:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operacional <br />
**Fonte:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Data:**          *hora* <br />
**ID do evento:** 1296 <br />
**Categoria tarefa:** (104) <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SERVIÇO DE REDE <br />
**Computador:**      *fqdn do computador* <br />
**Descrição:** A descrição do Id do evento 1296 da fonte Microsoft-Windows-TerminalServices-SessionBroker-Client não pode ser encontrada. Ou o componente que levanta este evento não está instalado no seu computador local ou a instalação é corrompida. Pode instalar ou reparar o componente no computador local.
Se o evento teve origem noutro computador, as informações do visor tiveram de ser guardadas com o evento.
As seguintes informações foram incluídas no evento:  <br />
*texto* <br />
*texto* <br />
O corretor de ligação à secretária de trabalho remoto não está pronto para a comunicação RPC.

### <a name="cause"></a>Causa

Este problema ocorre porque o nome do anfitrião do servidor remote Desktop Connection Broker é alterado, o que não é uma alteração suportada. 

O nome de anfitrião tem entradas e dependências na Base de Dados Interna do Windows, que é exigida pela fazenda Remote Desktop Service para poder funcionar. Mudar o nome de anfitrião depois de a quinta já estar construída causa muitos erros e pode fazer com que o servidor de corretor deixe de funcionar.

### <a name="resolution"></a>Resolução 

Para corrigir este problema, a função de Corretor de Ligação ao Ambiente de Trabalho Remoto e a Base de Dados Interna do Windows devem ser reinstaladas.

## <a name="next-steps"></a>Passos Seguintes

[Eventos Schannel](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Descrição Geral Técnica do Schannel SSP](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[RdP Falha com Id de evento 1058 & Evento 36870 com Certificado de anfitrião de sessão de ambiente de trabalho remoto & Comunicação SSL](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Canal 36872 ou Canal 36870 num Controlador de Domínio](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[Id do evento 1058 - Autenticação e Encriptação de serviços de ambiente de trabalho remoto](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

