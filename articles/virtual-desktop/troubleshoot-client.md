---
title: Resolução de problemas cliente remoto de desktop Windows Virtual Desktop - Azure
description: Como resolver problemas quando configura as ligações com o cliente num ambiente de inquilino virtual do Windows Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f91e68ec2bd4b0b5400ee3e8e380d91ea6f31f36
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291324"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Resolução de problemas do cliente de ambiente de trabalho remoto

Este artigo descreve problemas comuns com o cliente remote desktop e como corrigi-los.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Cliente de ambiente de trabalho remoto para Windows 7 ou Windows 10 deixa de responder ou não pode ser aberto

A partir da versão 1.2.790, pode redefinir os dados do utilizador a partir da página Sobre ou utilizando um comando.

Utilize o seguinte comando para remover os dados do utilizador, restaurar as definições predefinidos e cancelar a subscrição de todos os espaços de trabalho.

```cmd
msrdcw.exe /reset [/f]
```

Se estiver a utilizar uma versão anterior do cliente Remote Desktop, recomendamos que desinstale e reinstale o cliente.

## <a name="web-client-wont-open"></a>O cliente web não abre

Em primeiro lugar, teste a sua ligação à Internet abrindo outro website no seu browser; por exemplo, [www.bing.com](https://www.bing.com).

Use **nslookup** para confirmar o DNS pode resolver o FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Tente ligar-se a outro cliente, como o Remote Desktop para Windows 7 ou Windows 10, e verifique se consegue abrir o cliente web.

### <a name="opening-another-site-fails"></a>Abrir outro site falha

Isto é geralmente causado por problemas de ligação à rede ou por uma falha de rede. Recomendamos que contacte o suporte da rede.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup não pode resolver o nome

Isto é geralmente causado por problemas de ligação à rede ou por uma falha de rede. Recomendamos que contacte o suporte da rede.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>O seu cliente não consegue ligar-se, mas outros clientes da sua rede podem ligar-se

Se o seu navegador começar a agir ou deixar de funcionar enquanto estiver a utilizar o cliente web, siga estas instruções para resolver problemas:

1. Reinicie o navegador.
2. Limpe os cookies do navegador. Ver [Como eliminar ficheiros de cookies no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Limpe a cache do browser. Consulte [cache de navegador claro para o seu navegador.](https://binged.it/2RKyfdU)
4. Abrir o navegador em modo Privado.

## <a name="web-client-does-not-show-my-resources"></a>O cliente web não mostra os meus recursos

Primeiro, verifique a conta Azure Ative Directory que está a usar. Se já assinou com uma conta de Diretório Azure Ative diferente da que pretende utilizar para o Windows Virtual Desktop, deve assinar a nossa ou utilizar uma janela de navegador privada.

Se estiver a utilizar o Windows Virtual Desktop (clássico), utilize o link do cliente web [neste artigo](./virtual-desktop-fall-2019/connect-web-2019.md) para se ligar aos seus recursos.

## <a name="web-client-stops-responding-or-disconnects"></a>Cliente web deixa de responder ou desconecta

Tente ligar usando outro navegador ou cliente.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Outros navegadores e clientes também avariam ou não abrem

Se os problemas continuarem mesmo depois de ter trocado de navegador, o problema pode não ser com o seu navegador, mas com a sua rede. Recomendamos que contacte o suporte da rede.

## <a name="web-client-keeps-prompting-for-credentials"></a>Cliente web continua a pedir credenciais

Se o cliente web continuar a pedir credenciais, siga estas instruções:

1. Confirme que o URL do cliente web está correto.
2. Confirme que as credenciais que está a usar são para o ambiente de ambiente de trabalho virtual do Windows ligado ao URL.
3. Limpe os cookies do navegador. Para obter mais detalhes, consulte [Como eliminar ficheiros de cookies no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Limpe a cache do browser. Para mais detalhes, consulte [Limpar a cache do navegador para o seu navegador.](https://binged.it/2RKyfdU)
5. Abra o seu navegador em modo Privado.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte [a visão geral da resolução de problemas, o feedback e o suporte](troubleshoot-set-up-overview.md).
- Para resolver problemas enquanto cria um ambiente de ambiente de trabalho virtual do Windows e uma piscina de anfitrião num ambiente de ambiente de trabalho virtual do Windows, consulte [ambiente e a criação de piscinas de anfitriões.](troubleshoot-set-up-issues.md)
- Para resolver problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration.md).
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações de modelos do Gestor de Recursos de Resolução de Problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
