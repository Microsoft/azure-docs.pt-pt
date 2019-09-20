---
title: Solucionar problemas de conexão RDP de VM do Azure por ID de evento | Microsoft Docs
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
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154199"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Resolver problemas de ligação de RDP da VM do Azure por ID de Evento 

Este artigo explica como usar IDs de evento para solucionar problemas que impedem uma conexão de protocolo de Área de Trabalho Remota (RDP) para uma VM (máquina virtual) do Azure.

## <a name="symptoms"></a>Sintomas

Você tenta usar uma sessão de protocolo de Área de Trabalho Remota (RDP) para se conectar a uma VM do Azure. Depois de inserir suas credenciais, a conexão falhará e você receberá a seguinte mensagem de erro:

**Este computador não pode se conectar ao computador remoto. Tente se conectar novamente, se o problema persistir, contate o proprietário do computador remoto ou o administrador da rede.**

Para solucionar esse problema, examine os logs de eventos na VM e, em seguida, consulte os cenários a seguir.

## <a name="before-you-troubleshoot"></a>Antes de solucionar problemas

### <a name="create-a-backup-snapshot"></a>Criar um instantâneo de backup

Para criar um instantâneo de backup, siga as etapas em fazer o [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Conectar-se à VM remotamente

Para se conectar à VM remotamente, use um dos métodos em [como usar as ferramentas remotas para solucionar problemas de VM do Azure](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Cenário 1

### <a name="event-logs"></a>Registos de eventos

Em uma instância do CMD, execute os seguintes comandos para verificar se o evento 1058 ou 1057 está registrado no log do sistema nas últimas 24 horas:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do log:**      Sistema <br />
**Original**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:** *tempo* <br />
**ID do evento:**      1058 <br />
**Categoria da tarefa:** Nenhum <br />
**Geral**         Erro <br />
**Palavras-chave**      Clássica <br />
**Utilizador:**          N/A <br />
**Computador:** *computador* do <br />
**Descrição:** O servidor de Host da Sessão RD falhou ao substituir o certificado autoassinado expirado usado para autenticação de servidor Host da Sessão RD em conexões SSL. O código de status relevante foi negado.

**Nome do log:**      Sistema <br />
**Original**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:** *tempo* <br />
**ID do evento:**      1058 <br />
**Categoria da tarefa:** Nenhum <br />
**Geral**         Erro <br />
**Palavras-chave**      Clássica <br />
**Utilizador:**          N/A <br />
**Computador:** *computador* do <br />
**Descrição:** O servidor de host da sessão da área de trabalho remota falhou ao criar um novo certificado autoassinado para ser usado para autenticação do servidor host da Sessão RD em conexões SSL. o código de status relevante do objeto já existe.

**Nome do log:**      Sistema <br />
**Original**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:** *tempo* <br />
**ID do evento:**      1057 <br />
**Categoria da tarefa:** Nenhum <br />
**Geral**         Erro <br />
**Palavras-chave**      Clássica <br />
**Utilizador:**          N/A <br />
**Computador:** *computador* do <br />
**Descrição:** O servidor de Host da Sessão RD falhou ao criar um novo certificado autoassinado para ser usado para autenticação de servidor Host da Sessão RD em conexões SSL. O código de status relevante do conjunto de chaves não existe

Você também pode verificar os eventos de erro SCHANNEL 36872 e 36870 executando os seguintes comandos:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do log:**      Sistema <br />
**Original**        Schannel <br />
**Data:** — <br />
**ID do evento:**      36870 <br />
**Categoria da tarefa:** Nenhum <br />
**Geral**         Erro <br />
**Palavras-chave**       <br />
**Utilizador:**          SISTEMA <br />
**Computador:** *computador* do <br />
**Descrição:** Ocorreu um erro fatal ao tentar acessar a chave privada de credencial do servidor SSL. O código de erro retornado do módulo de criptografia é 0x8009030D.  <br />
O estado de erro interno é 10001.

### <a name="cause"></a>Causa
Esse problema ocorre porque as chaves de criptografia RSA locais na pasta MachineKeys na VM não podem ser acessadas. Esse problema pode ocorrer por um dos seguintes motivos:

1. Configuração de permissões incorreta na pasta MachineKeys ou nos arquivos RSA.

2. Chave RSA corrompida ou ausente.

### <a name="resolution"></a>Resolução

Para solucionar esse problema, você precisa configurar as permissões corretas no certificado RDP usando estas etapas.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Conceder permissão para a pasta MachineKeys

1. Crie um script usando o seguinte conteúdo:

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

2.  Execute este script para redefinir as permissões da pasta MachineKey e redefinir os arquivos RSA para os valores padrão.

3.  Tente acessar a VM novamente.

Depois de executar o script, você pode verificar os seguintes arquivos que estão tendo problemas de permissões:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Renovar certificado RDP autoassinado

Se o problema persistir, execute o seguinte script para certificar-se de que o certificado autoassinado do RDP seja renovado:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Se você não puder renovar o certificado, siga estas etapas para tentar excluir o certificado:

1. Em outra VM na mesma VNET, abra a caixa **executar** , digite **MMC**e pressione **OK**. 

2. No menu **arquivo** , selecione **Adicionar/remover snap-in**.

3. Na lista **snap-ins disponíveis** , selecione **certificados**e, em seguida, selecione **Adicionar**.

4. Selecione **conta de computador**e, em seguida, selecione **Avançar**.

5. Selecione **outro computador**e, em seguida, adicione o endereço IP da VM que tem problemas.
   >[!Note]
   >Tente usar a rede interna para evitar o uso de um endereço IP virtual.

6. Selecione **concluir**e, em seguida, selecione **OK**.

   ![Selecionar computador](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Expanda os certificados, vá para a pasta Desktop\Certificates remota, clique com o botão direito do mouse no certificado e selecione **excluir**.

8. Reinicie o serviço de configuração Área de Trabalho Remota:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Neste ponto, se você atualizar o armazenamento do MMC, o certificado reaparecerá. 

Tente acessar a VM usando o RDP novamente.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Atualizar certificado protocolo SSL (SSL)

Se você configurar a VM para usar um certificado SSL, execute o comando a seguir para obter a impressão digital. Em seguida, verifique se é o mesmo que a impressão digital do certificado:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Se não estiver, altere a impressão digital:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Você também pode tentar excluir a chave para que o RDP use o certificado autoassinado para RDP:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Cenário 2

### <a name="event-log"></a>Log de eventos

Em uma instância do CMD, execute os seguintes comandos para verificar se o evento 36871 de erro SCHANNEL está registrado no log do sistema nas últimas 24 horas:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do log:**      Sistema <br />
**Original**        Schannel <br />
**Data:** — <br />
**ID do evento:**      36871 <br />
**Categoria da tarefa:** Nenhum <br />
**Geral**         Erro <br />
**Palavras-chave**       <br />
**Utilizador:**          SISTEMA <br />
**Computador:** *computador* do <br />
**Descrição:** Ocorreu um erro fatal ao criar uma credencial do servidor TLS. O estado de erro interno é 10013.
 
### <a name="cause"></a>Causa

Esse problema é causado por políticas de segurança. Quando versões mais antigas do TLS (como 1,0) estiverem desabilitadas, o acesso ao RDP falhará.

### <a name="resolution"></a>Resolução

O RDP usa o TLS 1,0 como o protocolo padrão. No entanto, o protocolo pode ser alterado para TLS 1,1, que é o novo padrão.

Para solucionar esse problema, consulte [solucionar erros de autenticação ao usar o RDP para se conectar à VM do Azure](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Cenário 3

Se você instalou a função **agente de conexão de área de trabalho remota** na VM, verifique se há o evento 2056 ou o evento 1296 nas últimas 24 horas. Em uma instância do CMD, execute os seguintes comandos: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do log:**      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Original**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Data:** *tempo* <br />
**ID do evento:**      2056 <br />
**Categoria da tarefa:** (109) <br />
**Geral**         Erro <br />
**Palavras-chave**       <br />
**Utilizador:**          SERVIÇO DE REDE <br />
**Computador:** *FQDN do computador* <br />
**Descrição:** A descrição para a ID de evento 2056 da origem Microsoft-Windows-TerminalServices-agente não pode ser encontrada. O componente que gera esse evento não está instalado no computador local ou a instalação está corrompida. Você pode instalar ou reparar o componente no computador local. <br />
Se o evento tiver sido originado em outro computador, as informações de exibição precisarão ser salvas com o evento. <br />
As informações a seguir foram incluídas com o evento: <br />
NULL <br />
NULL <br />
Falha ao fazer logon no banco de dados.

**Nome do log:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Original**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Data:** *tempo* <br />
**ID do evento:**      1296 <br />
**Categoria da tarefa:** (104) <br />
**Geral**         Erro <br />
**Palavras-chave**       <br />
**Utilizador:**          SERVIÇO DE REDE <br />
**Computador:** *FQDN do computador* <br />
**Descrição:** A descrição para a ID de evento 1296 da origem Microsoft-Windows-TerminalServices-agente-Client não foi encontrada. O componente que gera esse evento não está instalado no computador local ou a instalação está corrompida. Você pode instalar ou reparar o componente no computador local.
Se o evento tiver sido originado em outro computador, as informações de exibição precisarão ser salvas com o evento.
As informações a seguir foram incluídas com o evento:  <br />
*text* <br />
*text* <br />
O agente de Conexão de Área de Trabalho Remota não está pronto para comunicação RPC.

### <a name="cause"></a>Causa

Esse problema ocorre porque o nome do host do servidor do agente de Conexão de Área de Trabalho Remota é alterado, o que não é uma alteração com suporte. 

O nome do host tem entradas e dependências no banco de dados interno do Windows, o que é exigido pelo Área de Trabalho Remota farm de serviços para poder funcionar. Alterar o nome do host depois que o farm já está compilado causa muitos erros e pode fazer com que o servidor do agente pare de funcionar.

### <a name="resolution"></a>Resolução 

Para corrigir esse problema, a função de agente de Conexão de Área de Trabalho Remota e o banco de dados interno do Windows devem ser reinstalados.

## <a name="next-steps"></a>Próximos Passos

[Eventos Schannel](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Visão geral técnica do Schannel SSP](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[Falha de RDP com a ID de evento 1058 & evento 36870 com Host da Sessão da Área de Trabalho Remota certificado & comunicação SSL](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872 ou Schannel 36870 em um controlador de domínio](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[ID do evento 1058 — Serviços de Área de Trabalho Remota autenticação e criptografia](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

