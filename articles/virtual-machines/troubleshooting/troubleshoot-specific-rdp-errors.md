---
title: Mensagens de erro específicas do PDR para VMs Azure Microsoft Docs
description: Compreenda mensagens de erro específicas que pode receber ao tentar utilizar a ligação Remote Desktop a uma máquina virtual do Windows em Azure
keywords: Erro remoto no ambiente de trabalho,erro de ligação remota de ambiente de trabalho,não pode ligar-se a VM,resolução remota de problemas no ambiente de trabalho
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266874"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Resolver mensagens de erro do RDP específicas para uma VM do Windows no Azure
Pode receber uma mensagem de erro específica ao utilizar a ligação Remote Desktop a uma máquina virtual do Windows (VM) em Azure. Este artigo detalha algumas das mensagens de erro mais comuns encontradas, juntamente com medidas de resolução de problemas para resolvê-las. Se tiver problemas de ligação ao seu VM utilizando RDP, mas não encontrar uma mensagem de erro específica, consulte o guia de resolução de [problemas para](troubleshoot-rdp-connection.md)o Ambiente de Trabalho Remoto .

Para obter informações sobre mensagens de erro específicas, consulte o seguinte:

* [A sessão remota foi desligada porque não existem servidores](#rdplicense)de licença de ambiente de trabalho remoto disponíveis para fornecer uma licença .
* [O Ambiente de Trabalho Remoto não consegue encontrar o "nome" do computador.](#rdpname)
* [Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contactada.](#rdpauth)
* [Erro de Segurança do Windows: As suas credenciais não funcionaram](#wincred).
* [Este computador não pode ligar-se ao computador remoto.](#rdpconnect)

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A sessão remota foi desligada porque não existem servidores de licença seletiva seletivas disponíveis para fornecer uma licença.
Causa: O período de carência de licença de 120 dias para a função Remote Desktop Server expirou e precisa de instalar licenças.

Como uma suposição, guarde uma cópia local do ficheiro RDP do portal e execute este comando num pedido de comando PowerShell para ligar. Este passo desativa o licenciamento para apenas essa ligação:

        mstsc <File name>.RDP /admin

Se não necessitar de mais de duas ligações simultâneas de ambiente de trabalho remoto para o VM, pode utilizar o Server Manager para remover a função Remote Desktop Server.

Para mais informações, consulte o post de blog [Azure VM falha com "No Remote Desktop License Servers available"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>O Ambiente de Trabalho Remoto não consegue encontrar o "nome" do computador.
Causa: O cliente Remote Desktop no seu computador não consegue resolver o nome do computador nas definições do ficheiro RDP.

Possíveis soluções:

* Se estiver na intranet de uma organização, certifique-se de que o seu computador tem acesso ao servidor proxy e pode enviar-lhe tráfego HTTPS.
* Se estiver a usar um ficheiro RDP armazenado localmente, tente utilizar o que é gerado pelo portal. Este passo garante que tem o nome DNS correto para a máquina virtual, ou o serviço de nuvem e a porta de ponto final do VM. Aqui está uma amostra de ficheiro RDP gerado pelo portal:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

A parte de endereço deste ficheiro RDP tem:

* O nome de domínio totalmente qualificado do serviço em nuvem que contém o VM ("tailspin-azdatatier.cloudapp.net" neste exemplo).
* A porta TCP externa do ponto final para tráfego remoto de ambiente de trabalho (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contactada.
Causa: O VM alvo não pode localizar a autoridade de segurança na parte do nome do utilizador das suas credenciais.

Quando o seu nome de utilizador está no formulário *SecurityAuthority*\\*UserName* (exemplo: CORP\User1), a parte *SecurityAuthority* é o nome de computador do VM (para a autoridade de segurança local) ou um nome de domínio de Diretório Ativo.

Possíveis soluções:

* Se a conta for local para o VM, certifique-se de que o nome VM está corretamente escrito.
* Se a conta estiver num domínio de Diretório Ativo, verifique a ortografia do nome de domínio.
* Se for uma conta de domínio do Diretório Ativo e o nome de domínio estiver corretamente escrito, verifique se um controlador de domínio está disponível nesse domínio. É um problema comum nas redes virtuais do Azure que contêm controladores de domínio que um controlador de domínio está indisponível porque ainda não foi iniciado. Como sutique, pode utilizar uma conta de administrador local em vez de uma conta de domínio.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Erro de Segurança do Windows: As suas credenciais não funcionaram.
Causa: O VM alvo não pode validar o nome da sua conta e a sua palavra-passe.

Um computador baseado no Windows pode validar as credenciais de uma conta local ou de uma conta de domínio.

* Para contas locais, utilize o *nome de computador*\\sintaxe *username* (exemplo: SQL1\Admin4798).
* Para contas de domínio, utilize o *Nome de Domínio*\\sintaxe *username* (exemplo: CONTOSO\peterodman).

Se promoveu o seu VM a um controlador de domínio numa nova floresta de Diretório ativo, a conta de administrador local com a sua assinatura é convertida numa conta equivalente com a mesma senha na nova floresta e domínio. A conta local é então apagada.

Por exemplo, se você assinou com a conta local DC1\DCAdmin, e depois promoveu a máquina virtual como um controlador de domínio em uma nova floresta para o domínio corp.contoso.com, a conta local DC1\DCAdmin é eliminada e uma nova conta de domínio (CORP\DCAdmin) é criado com a mesma senha.

Certifique-se de que o nome da conta é um nome que a máquina virtual pode verificar como uma conta válida e que a palavra-passe está correta.

Se precisar de alterar a palavra-passe da conta de administrador local, consulte [como redefinir uma palavra-passe ou o serviço Remote Desktop para máquinas virtuais windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Este computador não pode ligar-se ao computador remoto.
Causa: A conta que é usada para ligar não tem direitos de entrada no Ambiente remoto.

Todos os computadores do Windows têm um grupo local de utilizadores de Ambiente de Trabalho Remoto, que contém as contas e grupos que podem iniciar sessão remotamente. Os membros do grupo de administradores locais também têm acesso, embora essas contas não estejam listadas no grupo local de utilizadores do Remote Desktop. Para máquinas unidas ao domínio, o grupo de administradores locais também contém os administradores de domínio para o domínio.

Certifique-se de que a conta que está a usar para se ligar tem direitos de entrada no Ambiente de Trabalho Remoto. Como suver, utilize uma conta de domínio ou administrador local para se conectar em desktop remoto. Para adicionar a conta desejada ao grupo local dos utilizadores do Ambiente de Trabalho Remoto, utilize o snap-in da Consola de Gestão da Microsoft (**System Tools > Utilizadores Locais e Grupos > Grupos > Utilizadores de Ambiente**de Trabalho Remoto).

## <a name="next-steps"></a>Passos Seguintes
Se nenhum destes erros ocorrer e tiver um problema desconhecido com a ligação utilizando RDP, consulte o guia de resolução de [problemas para](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)o Ambiente de Trabalho Remoto .

* Para obter medidas de resolução de problemas no acesso a aplicações em execução num VM, consulte o [acesso da Troubleshoot a uma aplicação em execução num Azure VM](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Se estiver a ter problemas com a Secure Shell (SSH) para se ligar a um Linux VM em Azure, consulte [as ligações SSH de Troubleshoot a um Linux VM em Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

