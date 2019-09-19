---
title: Mensagens de erro específicas de RDP para VMs do Azure | Microsoft Docs
description: Entenda as mensagens de erro específicas que você pode receber ao tentar usar a conexão Área de Trabalho Remota com uma máquina virtual do Windows no Azure
keywords: Erro de área de trabalho remota, erro de conexão de área de trabalho remota, não é possível conectar-se à VM, solução de problemas
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 851c5eb4ebfee4e4a4836a07b51578dd2b0c68cd
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088172"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Solucionando problemas de mensagens de erro específicas de RDP para uma VM do Windows no Azure
Você pode receber uma mensagem de erro específica ao usar Área de Trabalho Remota conexão com uma VM (máquina virtual) do Windows no Azure. Este artigo detalha algumas das mensagens de erro mais comuns encontradas, juntamente com as etapas de solução de problemas para resolvê-las. Se você estiver tendo problemas para se conectar à sua VM usando o RDP, mas não encontrar uma mensagem de erro específica, consulte o [Guia de solução de problemas para área de trabalho remota](troubleshoot-rdp-connection.md).

Para obter informações sobre mensagens de erro específicas, consulte o seguinte:

* [A sessão remota foi desconectada porque não há servidores de licença área de trabalho remota disponíveis para fornecer uma licença](#rdplicense).
* [Área de trabalho remota não pode localizar o computador "nome"](#rdpname).
* [Ocorreu um erro de autenticação. Não é possível contatar](#rdpauth)a autoridade de segurança local.
* [Erro de segurança do Windows: Suas credenciais não funcionaram](#wincred).
* [Este computador não pode se conectar ao computador remoto](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A sessão remota foi desconectada porque não há servidores de licença Área de Trabalho Remota disponíveis para fornecer uma licença.
Motivo: O período de carência de licenciamento de 120 dias para a função de servidor Área de Trabalho Remota expirou e você precisa instalar licenças.

Como alternativa, salve uma cópia local do arquivo RDP do portal e execute esse comando em um prompt de comando do PowerShell para se conectar. Esta etapa desabilita o licenciamento apenas para essa conexão:

        mstsc <File name>.RDP /admin

Se você não precisar realmente de mais de duas conexões Área de Trabalho Remota simultâneas com a VM, poderá usar Gerenciador do Servidor para remover a função de servidor Área de Trabalho Remota.

Para obter mais informações, consulte a postagem do blog [falha na VM do Azure com "sem área de trabalho remota licença de servidores disponíveis"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Área de Trabalho Remota não pode localizar o computador "nome".
Motivo: O cliente do Área de Trabalho Remota no computador não pode resolver o nome do computador nas configurações do arquivo RDP.

Possíveis soluções:

* Se você estiver na intranet de uma organização, verifique se o computador tem acesso ao servidor proxy e pode enviar tráfego HTTPS para ele.
* Se você estiver usando um arquivo RDP armazenado localmente, tente usar aquele gerado pelo portal. Essa etapa garante que você tenha o nome DNS correto para a máquina virtual ou o serviço de nuvem e a porta do ponto de extremidade da VM. Aqui está um arquivo RDP de exemplo gerado pelo portal:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

A parte do endereço deste arquivo RDP tem:

* O nome de domínio totalmente qualificado do serviço de nuvem que contém a VM ("tailspin-azdatatier.cloudapp.net" neste exemplo).
* A porta TCP externa do ponto de extremidade para o tráfego de Área de Trabalho Remota (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ocorreu um erro de autenticação. Não é possível contatar a autoridade de segurança local.
Motivo: A VM de destino não pode localizar a autoridade de segurança na parte do nome de usuário de suas credenciais.

Quando seu nome de usuário estiver no formato *SecurityAuthority*\\*username* (exemplo: CORP\User1), a parte *SecurityAuthority* é o nome do computador da VM (para a autoridade de segurança local) ou um nome de domínio Active Directory.

Possíveis soluções:

* Se a conta for local para a VM, verifique se o nome da VM está escrito corretamente.
* Se a conta estiver em um domínio Active Directory, verifique a ortografia do nome de domínio.
* Se for uma conta de domínio Active Directory e o nome de domínio estiver grafado corretamente, verifique se um controlador de domínio está disponível nesse domínio. É um problema comum nas redes virtuais do Azure que contêm controladores de domínio que um controlador de domínio está indisponível porque não foi iniciado. Como alternativa, você pode usar uma conta de administrador local em vez de uma conta de domínio.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Erro de segurança do Windows: Suas credenciais não funcionaram.
Motivo: A VM de destino não pode validar o nome da conta e a senha.

Um computador baseado no Windows pode validar as credenciais de uma conta local ou de uma conta de domínio.

* Para contas locais, use a sintaxe *ComputerName*\\*nome de usuário* (exemplo: SQL1\Admin4798).
* Para contas de domínio, use a sintaxe de*nome de usuário* *DomainName*\\(exemplo: CONTOSO\peterodman).

Se você tiver promovido sua VM para um controlador de domínio em uma nova floresta Active Directory, a conta de administrador local com a qual você se conectou será convertida em uma conta equivalente com a mesma senha na nova floresta e no mesmo domínio. A conta local é então excluída.

Por exemplo, se você entrou com a conta local DC1\DCAdmin e promoveu a máquina virtual como um controlador de domínio em uma nova floresta para o domínio corp.contoso.com, a conta local DC1\DCAdmin é excluída e uma nova conta de domínio (CORP\DCAdmin) é criado com a mesma senha.

Verifique se o nome da conta é um nome que a máquina virtual pode verificar como uma conta válida e que a senha está correta.

Se você precisar alterar a senha da conta de administrador local, consulte [como redefinir uma senha ou o serviço de área de trabalho remota para máquinas virtuais do Windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Este computador não pode se conectar ao computador remoto.
Motivo: A conta que é usada para se conectar não tem Área de Trabalho Remota direitos de entrada.

Cada computador com Windows tem um grupo local Área de Trabalho Remota usuários, que contém as contas e os grupos que podem entrar remotamente. Os membros do grupo Administradores local também têm acesso, embora essas contas não estejam listadas no grupo local Área de Trabalho Remota usuários. Para computadores ingressados no domínio, o grupo Administradores local também contém os administradores de domínio para o domínio.

Verifique se a conta que você está usando para se conectar tem Área de Trabalho Remota direitos de entrada. Como alternativa, use uma conta de administrador local ou de domínio para se conectar por Área de Trabalho Remota. Para adicionar a conta desejada ao grupo local Área de Trabalho Remota usuários, use o snap-in do console de gerenciamento Microsoft (**Ferramentas do sistema > usuários e grupos locais > grupos > área de trabalho remota usuários**).

## <a name="next-steps"></a>Passos Seguintes
Se nenhum desses erros ocorreu e você tiver um problema desconhecido com a conexão usando o RDP, consulte o [Guia de solução de problemas para área de trabalho remota](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Para obter as etapas de solução de problemas de acesso a aplicativos executados em uma VM, consulte [solucionar problemas de acesso a um aplicativo em execução em uma VM do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Se você estiver tendo problemas ao usar o Secure Shell (SSH) para se conectar a uma VM do Linux no Azure, consulte [solucionar problemas de conexões SSH para uma VM do Linux no Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

