---
title: Troubleshoot Remote Desktop cliente Windows Virtual Desktop - Azure
description: Como resolver problemas quando configura as ligações do cliente num ambiente de inquilino sinuoso virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e3a240901ffca2c126e2b61eaee0cf287cc31d6e
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127502"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Resolução de problemas ao cliente do Ambiente de Trabalho Remoto

Este artigo descreve problemas comuns com o cliente remote Desktop e como corrigi-los.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Cliente de Ambiente de Trabalho Remoto para Windows 7 ou Windows 10 deixa de responder ou não pode ser aberto

Utilize os seguintes cmdlets PowerShell para limpar registos de clientes fora da banda (OOB).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Navegue para **%AppData%\RdClientRadc** e elimine todos os conteúdos.

Desinstale e reinstale o cliente remote Desktop para windows 7 e Windows 10.

## <a name="web-client-wont-open"></a>O cliente web não abre

Primeiro, teste a sua ligação à Internet abrindo outro website no seu navegador; por exemplo, [www.bing.com.](https://www.bing.com)

Use **nslookup** para confirmar que o DNS pode resolver o FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Tente conectar-se com outro cliente, como o cliente Remote Desktop para windows 7 ou Windows 10, e verifique se consegue abrir o cliente web.

### <a name="opening-another-site-fails"></a>Abrir outro site falha

Isto é geralmente causado por problemas de ligação de rede ou uma falha de rede. Recomendamos que contacte o suporte da rede.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup não pode resolver o nome

Isto é geralmente causado por problemas de ligação de rede ou uma falha de rede. Recomendamos que contacte o suporte da rede.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>O seu cliente não pode ligar-se, mas outros clientes da sua rede podem ligar-se

Se o seu navegador começar a agir ou deixar de funcionar enquanto utiliza o cliente web, siga estas instruções para o resolver:

1. Reinicie o navegador.
2. Limpe os cookies do navegador. Ver [Como eliminar ficheiros de cookies no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Limpe a cache do browser. Consulte [a cache clara do navegador para o seu navegador](https://binged.it/2RKyfdU).
4. Abra o navegador em modo Privado.

## <a name="web-client-stops-responding-or-disconnects"></a>O cliente da Web deixa de responder ou desliga

Tente conectar-se usando outro navegador ou cliente.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Outros navegadores e clientes também avariam ou não abrem

Se os problemas continuarem mesmo depois de ter trocado de navegador, o problema pode não estar com o seu navegador, mas com a sua rede. Recomendamos que contacte o suporte da rede.

## <a name="web-client-keeps-prompting-for-credentials"></a>Cliente web continua a pedir credenciais

Se o cliente da Web continuar a pedir credenciais, siga estas instruções:

1. Confirme que o URL do cliente web está correto.
2. Confirme que as credenciais que está a usar são para o ambiente de ambiente de trabalho virtual do Windows ligado ao URL.
3. Limpe os cookies do navegador. Para mais detalhes, consulte [Como eliminar ficheiros de cookies no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Limpe a cache do browser. Para mais detalhes, consulte [clear browser cache para o seu navegador](https://binged.it/2RKyfdU).
5. Abra o seu navegador em modo Privado.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte a [visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de Resolução de Problemas.
- Para resolver problemas ao criar um inquilino e uma piscina de hospedas num ambiente de ambiente de trabalho virtual windows, consulte [tenant e host pool creation](troubleshoot-set-up-issues.md).
- Para resolver problemas ao configurar uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração virtual](troubleshoot-vm-configuration.md)do anfitrião da sessão .
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)do modelo de gestor de recursos de resolução de problemas .