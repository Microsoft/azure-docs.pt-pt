---
title: Mensagens de erro do RDP específicas para as VMs do Azure | Documentos da Microsoft
description: Compreender as mensagens de erro específicas que possa receber ao tentar utilizam a ligação de ambiente de trabalho remoto a uma máquina virtual do Windows no Azure
keywords: Erro de área de trabalho remoto, erro de conexão de área de trabalho remoto, não é possível ligar à VM, resolução de problemas área de trabalho remoto
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f4d733e29d2ba8213e1832f2c604b726283ab3e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318702"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Resolução de mensagens de erro do RDP específicas a uma VM do Windows no Azure
Poderá receber uma mensagem de erro específico ao utilizar a ligação de ambiente de trabalho remoto a uma máquina virtual de Windows (VM) no Azure. Este artigo detalha algumas das mensagens de erro mais comuns encontradas, juntamente com passos para resolvê-los de resolução de problemas. Se estiver a ter problemas de ligação à sua VM através de RDP mas não encontrar uma mensagem de erro específica, consulte a [guia para o ambiente de trabalho remoto de resolução de problemas](troubleshoot-rdp-connection.md).

Para obter informações sobre mensagens de erro específicas, consulte o seguinte:

* [A sessão remota foi desligada porque existem não existem servidores de licença de ambiente de trabalho remoto disponíveis para fornecer uma licença](#rdplicense).
* [Ambiente de trabalho remoto não é possível localizar o computador "name"](#rdpname).
* [Ocorreu um erro de autenticação. Não é possível contactar a autoridade de segurança Local](#rdpauth).
* [Erro de segurança do Windows: As suas credenciais não funcionavam](#wincred).
* [Este computador não é possível ligar ao computador remoto](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A sessão remota foi desligada porque existem não existem servidores de licença de ambiente de trabalho remoto disponíveis para fornecer uma licença.
Causa: O período de tolerância licenciamento 120 dias para a função de servidor de ambiente de trabalho remoto expirou e tem de instalar licenças.

Como solução, guardar uma cópia local do ficheiro RDP a partir do portal e execute este comando no prompt de comando do PowerShell para ligar. Este passo desativa o licenciamento para apenas essa ligação:

        mstsc <File name>.RDP /admin

Se não precisar verdadeiramente de mais de duas ligações de ambiente de trabalho remoto simultâneas para a VM, pode utilizar o Gestor de servidor para remover a função de servidor de ambiente de trabalho remoto.

Para obter mais informações, consulte a mensagem de blogue [VM do Azure falha com "Nenhuma área de trabalho licença servidores remotos disponíveis"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Ambiente de trabalho remoto não é possível localizar o computador "name".
Causa: O cliente de ambiente de trabalho remoto no seu computador não é possível resolver o nome do computador em que as definições do ficheiro RDP.

Possíveis soluções:

* Se estiver na intranet da organização, certifique-se de que o seu computador tem acesso ao servidor proxy e pode enviar o tráfego HTTPS para o mesmo.
* Se estiver a utilizar um ficheiro RDP armazenado localmente, experimente utilizar aquele que é gerado pelo portal. Este passo garante que tem o nome DNS correto para a máquina virtual, ou o serviço em nuvem e a porta de ponto final da VM. Este é um ficheiro de RDP de exemplo gerado pelo portal:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

A parte do endereço deste ficheiro RDP tem:

* O nome de domínio completamente qualificado do serviço cloud que contém a VM ("tailspin-azdatatier.cloudapp.net" neste exemplo).
* A porta TCP externa do ponto final para o tráfego de ambiente de trabalho remoto (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ocorreu um erro de autenticação. Não é possível contactar a autoridade de segurança Local.
Causa: A VM de destino não é possível localizar a autoridade de segurança na parte do nome do usuário das credenciais.

Quando o seu nome de utilizador está no formato *SecurityAuthority*\\*UserName* (exemplo: CORP\User1), o *SecurityAuthority* parte é o nome do computador da VM (para a autoridade de segurança local) ou um nome de domínio do Active Directory.

Possíveis soluções:

* Se a conta seja local para a VM, certifique-se de que o nome da VM está escrito corretamente.
* Se a conta está num domínio do Active Directory, verifique a ortografia do nome do domínio.
* Se for uma conta de domínio do Active Directory e o nome de domínio está escrito corretamente, certifique-se de que está disponível um controlador de domínio nesse domínio. É um problema comum em redes virtuais do Azure que contêm os controladores de domínio que um controlador de domínio está indisponível porque este ainda não foi iniciado. Como solução, pode utilizar uma conta de administrador local em vez de uma conta de domínio.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Erro de segurança do Windows: As suas credenciais não funcionava.
Causa: A VM de destino não é possível validar o nome de conta e palavra-passe.

Um computador baseado em Windows pode validar as credenciais de uma conta local ou uma conta de domínio.

* Para contas locais, utilize o *nomedocomputador*\\*UserName* sintaxe (exemplo: SQL1\Admin4798).
* Para contas de domínio, utilize o *DomainName*\\*UserName* sintaxe (exemplo: CONTOSO\peterodman).

Se o ter promovido a VM para um controlador de domínio numa nova floresta do Active Directory, a conta de administrador local com que iniciou sessão é convertida para uma conta equivalente com a mesma palavra-passe na nova floresta e no domínio. A conta local, em seguida, é eliminada.

Por exemplo, se tiver iniciado sessão com a conta local DC1\DCAdmin e, em seguida, promovido a máquina virtual como controlador de domínio numa floresta de novo para o domínio corp.contoso.com, a conta local DC1\DCAdmin é eliminada e é uma nova conta de domínio (CORP\DCAdmin) criado com a mesma palavra-passe.

Certifique-se de que o nome da conta é um nome que a máquina virtual pode verificar como uma conta válida e que a palavra-passe está correta.

Se precisar de alterar a palavra-passe da conta de administrador local, consulte [como repor uma palavra-passe ou o serviço de ambiente de trabalho remoto para máquinas de virtuais do Windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Este computador não é possível ligar ao computador remoto.
Causa: A conta que é utilizada para ligar não tem direitos de início de sessão de ambiente de trabalho remoto.

Todos os computadores do Windows tem um ambiente de trabalho remoto utilizadores local grupo que contém as contas e grupos que podem iniciar sessão para o mesmo remotamente. Membros do grupo Administradores locais também tem acesso, mesmo que essas contas não estão listadas no grupo local de utilizadores de ambiente de trabalho remoto. Para computadores associados a um domínio, o grupo de administradores locais também contém os administradores de domínio para o domínio.

Certifique-se de que a conta que estiver a utilizar para ligar com tem direitos de início de sessão de ambiente de trabalho remoto. Como solução, utilize um domínio ou conta de administrador local para ligar através do ambiente de trabalho remoto. Para adicionar a conta pretendida para o grupo local de utilizadores de ambiente de trabalho remoto, utilize o snap-in do Console de gerenciamento Microsoft (**ferramentas do sistema > utilizadores e grupos locais > grupos > Remote Desktop Users**).

## <a name="next-steps"></a>Passos Seguintes
Se nenhum destes erros ocorreram e tiver um desconhecido emitir com a ligação com o protocolo RDP, consulte a [guia para o ambiente de trabalho remoto de resolução de problemas](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Para passos ao aceder ao aplicações em execução numa VM de resolução de problemas, consulte [resolver problemas de acesso para uma aplicação em execução numa VM do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Se estiver a ter problemas com o Secure Shell (SSH) para ligar a uma VM do Linux no Azure, consulte [resolver problemas de SSH ligações a uma VM do Linux no Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

