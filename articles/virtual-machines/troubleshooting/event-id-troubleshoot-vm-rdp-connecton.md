---
title: Problemas de resolução de problemas Problemas Azure VM RDP problemas de conexão por | de ID de evento Microsoft Docs
description: Utilize iDs de eventos para resolver problemas que impedem a ligação de um protocolo de ambiente de trabalho remoto (RDP) a uma Máquina Virtual Azure (VM).
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
ms.openlocfilehash: c293945a52dd810975b36144f224278163166ba8
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878448"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Resolver problemas de ligação de RDP da VM do Azure por ID de Evento 

Este artigo explica como usar iDs de eventos para resolver problemas que impedem a ligação de um protocolo de ambiente de trabalho remoto (RDP) a uma Máquina Virtual Azure (VM).

## <a name="symptoms"></a>Sintomas

Tente utilizar uma sessão de protocolo de ambiente de trabalho remoto (RDP) para ligar a um VM Azure. Depois de inserir as suas credenciais, a ligação falha e recebe a seguinte mensagem de erro:

**Este computador não consegue ligar-se ao computador remoto. Tente ligar novamente, se o problema continuar, contacte o proprietário do computador remoto ou o administrador da rede.**

Para resolver este problema, reveja os registos do evento no VM e, em seguida, consulte os seguintes cenários.

## <a name="before-you-troubleshoot"></a>Antes de resolver problemas

### <a name="create-a-backup-snapshot"></a>Criar uma imagem de reserva

Para criar uma imagem de backup, siga os passos no [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Ligue-se ao VM remotamente

Para ligar remotamente ao VM, utilize um dos métodos de utilização de [ferramentas remotas para resolver problemas com o Azure VM](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Cenário 1

### <a name="event-logs"></a>Registos de eventos

Em caso CMD, executar os seguintes comandos para verificar se o evento 1058 ou o evento 1057 está registado no registo do Sistema nas últimas 24 horas:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Sistema <br />
**Fonte:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *hora* <br />
**ID do evento:**      1058 <br />
**Categoria de Tarefa:** Nenhum. <br />
**Nível:**         Erro <br />
**Palavras-chave:**      Clássico <br />
**Utilizador:**          N/A <br />
**Computador:**      *computador* <br />
**Descrição:** O Servidor anfitrião de sessão RD não substituiu o certificado auto-assinado expirado utilizado para a autenticação do Servidor do Anfitrião de Sessão RD nas ligações TLS. O código de estado relevante foi o Access é negado.

**Nome do registo:**      Sistema <br />
**Fonte:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *hora* <br />
**ID do evento:**      1058 <br />
**Categoria de Tarefa:** Nenhum. <br />
**Nível:**         Erro <br />
**Palavras-chave:**      Clássico <br />
**Utilizador:**          N/A <br />
**Computador:**      *computador* <br />
**Descrição:** O servidor anfitrião da RD Session falhou na criação de um novo certificado auto-assinado para ser utilizado para a autenticação do servidor do anfitrião RD Session nas ligações TLS, o código de estado relevante já existe.

**Nome do registo:**      Sistema <br />
**Fonte:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Data:**          *hora* <br />
**ID do evento:**      1057 <br />
**Categoria de Tarefa:** Nenhum. <br />
**Nível:**         Erro <br />
**Palavras-chave:**      Clássico <br />
**Utilizador:**          N/A <br />
**Computador:**      *computador* <br />
**Descrição:** O Servidor anfitrião de sessão RD falhou na criação de um novo certificado auto-assinado para ser utilizado para a autenticação do Servidor do Anfitrião de Sessão RD nas ligações TLS. O código de estado relevante era Keyset não existe

Também pode verificar se os erros do SCHANNEL 36872 e 36870 executam os seguintes comandos:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Sistema <br />
**Fonte:**        Canalil <br />
**Data:**          — <br />
**ID do evento:**      36870 <br />
**Categoria de Tarefa:** Nenhum. <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SISTEMA <br />
**Computador:**      *computador* <br />
**Descrição:** Ocorreu um erro fatal ao tentar aceder à chave privada credencial do servidor TLS. O código de erro devolvido do módulo criptográfico é 0x8009030D.  <br />
O estado de erro interno é 10001.

### <a name="cause"></a>Causa
Este problema ocorre porque as chaves de encriptação RSA locais na pasta MachineKeys no VM não podem ser acedidas. Esta questão pode ocorrer por uma das seguintes razões:

1. Configuração de permissões erradas na pasta Machinekeys ou nos ficheiros RSA.

2. Chave RSA corrompida ou desaparecida.

### <a name="resolution"></a>Resolução

Para resolver este problema, tem de configurar as permissões corretas no Certificado RDP utilizando estes passos.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Conceder permissão à pasta MachineKeys

1. Crie um script utilizando o seguinte conteúdo:

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

2.  Executar este script para redefinir as permissões da pasta MachineKey e para redefinir os ficheiros RSA para os valores predefinidos.

3.  Tente aceder ao VM de novo.

Depois de executar o script, pode verificar os seguintes ficheiros que estão a experimentar problemas de permissões:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Renovar certificado auto-assinado rdp

Se a questão persistir, execute o seguinte script para garantir que o certificado auto-assinado RDP seja renovado:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Se não puder renovar o certificado, siga estes passos para tentar apagar o certificado:

1. Em outro VM no mesmo VNET, abra a caixa **Run,** escreva **mmc,** e, em seguida, prima **OK**. 

2. No menu **Ficheiro,** **selecione Adicionar/Remover Snap-in**.

3. Na lista **de snap-Ins disponíveis,** selecione **Certificados** e, em seguida, selecione **Adicionar**.

4. Selecione **a conta de Computador** e, em seguida, selecione **Seguinte**.

5. Selecione **Outro computador** e, em seguida, adicione o endereço IP do VM que tem problemas.
   >[!Note]
   >Tente utilizar a rede interna para evitar a utilização de um endereço IP virtual.

6. **Selecione Terminar** e, em seguida, selecione **OK**.

   ![Selecione Computador](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Expandir os certificados, ir à pasta Remote Desktop\Certificates, clicar com o botão direito no certificado e, em seguida, selecionar **Eliminar**.

8. Reiniciar o serviço de configuração de ambiente de trabalho remoto:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Neste momento, se refrescar a loja do MMC, o certificado reaparece. 

Tente aceder ao VM utilizando novamente o PDR.

#### <a name="update-tlsssl-certificate"></a>Certificado TLS/SSL de atualização

Se configurar o VM para utilizar um certificado TLS/SSL, executar o seguinte comando para obter a impressão digital. Então verifique se é o mesmo que a impressão digital do certificado:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Se não for, mude a impressão digital:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Pode também tentar eliminar a chave para que o PDR utilize o certificado auto-assinado para PDR:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Cenário 2

### <a name="event-log"></a>Registo de eventos

Em caso CMD, executar os seguintes comandos para verificar se o evento de erro SCHANNEL 36871 está registado no registo do Sistema nas últimas 24 horas:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Sistema <br />
**Fonte:**        Canalil <br />
**Data:**          — <br />
**ID do evento:**      36871 <br />
**Categoria de Tarefa:** Nenhum. <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SISTEMA <br />
**Computador:**      *computador* <br />
**Descrição:** Ocorreu um erro fatal ao criar uma credencial de servidor TLS. O estado de erro interno é 10013.
 
### <a name="cause"></a>Causa

Esta questão é causada por políticas de segurança. Quando as versões mais antigas do TLS (como 1.0) são desativadas, o acesso rdp falha.

### <a name="resolution"></a>Resolução

O RDP utiliza o TLS 1.0 como protocolo predefinido. No entanto, o protocolo pode ser alterado para TLS 1.1, que é a nova norma.

Para resolver este problema, consulte [os erros de autenticação da Resolução de Problemas quando utilizar o RDP para ligar ao Azure VM](/troubleshoot/azure/virtual-machines/cannot-connect-rdp-azure-vm#tls-version).

## <a name="scenario-3"></a>Cenário 3

Se instalou a função **De Corretor de Conexão de Ambiente de Trabalho Remoto** no VM, verifique se há evento 2056 ou evento 1296 nas últimas 24 horas. Em caso CMD, executar os seguintes comandos: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Nome do registo:**      Microsoft-Windows-TerminalServices-SessionBroker/Operacional <br />
**Fonte:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Data:**          *hora* <br />
**ID do evento:**      2056 <br />
**Categoria tarefa:** (109) <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SERVIÇO DE REDE <br />
**Computador:**      *computador fqdn* <br />
**Descrição:** A descrição para o Evento ID 2056 a partir da fonte Microsoft-Windows-TerminalServices-SessionBroker não pode ser encontrada. Ou o componente que levanta este evento não está instalado no computador local ou a instalação está corrompida. Pode instalar ou reparar o componente no computador local. <br />
Se o evento tiver origem noutro computador, as informações do visor tiveram de ser guardadas com o evento. <br />
As seguintes informações foram incluídas no evento: <br />
NULL <br />
NULL <br />
O início de sê-lo na base de dados falhou.

**Nome do registo:**      Microsoft-Windows-TerminalServices-SessionBroker-Cliente/Operacional <br />
**Fonte:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Data:**          *hora* <br />
**ID do evento:**      1296 <br />
**Categoria tarefa:** (104) <br />
**Nível:**         Erro <br />
**Palavras-chave:**       <br />
**Utilizador:**          SERVIÇO DE REDE <br />
**Computador:**      *computador fqdn* <br />
**Descrição:** A descrição para o Evento ID 1296 da fonte Microsoft-Windows-TerminalServices-SessionBroker-Client não pode ser encontrada. Ou o componente que levanta este evento não está instalado no computador local ou a instalação está corrompida. Pode instalar ou reparar o componente no computador local.
Se o evento tiver origem noutro computador, as informações do visor tiveram de ser guardadas com o evento.
As seguintes informações foram incluídas no evento:  <br />
*Texto* <br />
*Texto* <br />
O Corretor de Ligação remota do Ambiente de Trabalho não está pronto para a comunicação RPC.

### <a name="cause"></a>Causa

Este problema ocorre porque o nome de anfitrião do servidor Remote Desktop Connection Broker é alterado, o que não é uma alteração suportada. 

O nome anfitrião tem entradas e dependências na Base de Dados Interna do Windows, que é exigida pela fazenda Remote Desktop Service para poder trabalhar. Mudar o nome de anfitrião depois de a quinta já ter sido construída causa muitos erros e pode fazer com que o servidor do corretor deixe de funcionar.

### <a name="resolution"></a>Resolução 

Para corrigir este problema, a função De Corretor de Conexão de Ambiente de Trabalho Remoto e a Base de Dados Interna do Windows devem ser reinstaladas.

## <a name="next-steps"></a>Passos Seguintes

[Eventos do Canal da Schannel](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn786445(v=ws.11))

[Descrição Geral Técnica do Schannel SSP](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn786429(v=ws.11))

[RDP Falha com o Evento ID 1058 & Event 36870 com Certificado de Anfitrião de Sessão de Secretária Remota & Comunicação SSL](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)

[Canalil 36872 ou Schannel 36870 num Controlador de Domínio](/archive/blogs/instan/schannel-36872-or-schannel-36870-on-a-domain-controller)

[ID do evento 1058 — Autenticação e Encriptação de Serviços de Ambiente de Trabalho Remoto](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee890862(v=ws.10))