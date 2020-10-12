---
title: Mensagens de erro PDR específicas para VMs Azure / Microsoft Docs
description: Compreenda as mensagens de erro específicas que poderá receber ao tentar utilizar a ligação remote desktop a uma máquina virtual do Windows em Azure
keywords: Erro remoto no ambiente de trabalho,erro de ligação ao ambiente de trabalho remoto, não pode ligar-se a VM,resolução remota de problemas no ambiente de trabalho
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
ms.openlocfilehash: f3e4c411223a3e8fc0602602cd941a00f7a19cca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028248"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Resolver mensagens de erro do RDP específicas para uma VM do Windows no Azure
Pode receber uma mensagem de erro específica quando utilizar a ligação remote desktop a uma máquina virtual Windows (VM) em Azure. Este artigo detalha algumas das mensagens de erro mais comuns encontradas, juntamente com medidas de resolução de problemas para as resolver. Se tiver problemas ligados ao seu VM utilizando RDP, mas não encontrar uma mensagem de erro específica, consulte o [guia de resolução de problemas para o Ambiente de Trabalho Remoto](troubleshoot-rdp-connection.md).

Para obter informações sobre mensagens de erro específicas, consulte o seguinte:

* [A sessão remota foi desligada porque não existem servidores de licença de secretária remoto disponíveis para fornecer uma licença](#rdplicense).
* [O Ambiente de Trabalho Remoto não consegue encontrar o "nome" do computador.](#rdpname)
* [Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contactada.](#rdpauth)
* [Erro de segurança do Windows: As suas credenciais não funcionaram](#wincred).
* [Este computador não pode ligar-se ao computador remoto.](#rdpconnect)

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A sessão remota foi desligada porque não existem servidores de licença de ambiente de trabalho remoto disponíveis para fornecer uma licença.
Causa: O período de carência de 120 dias para a função Remote Desktop Server expirou e é necessário instalar licenças.

Como solução alternativa, guarde uma cópia local do ficheiro RDP do portal e execute este comando num pedido de comando PowerShell para ligar. Este passo desativa o licenciamento para essa ligação:

```powershell
mstsc <File name>.RDP /admin
```

Se não precisar de mais de duas ligações de ambiente de trabalho remoto simultâneas ao VM, pode utilizar o Server Manager para remover a função "Servidor de Ambiente de Trabalho Remoto".

Para obter mais informações, consulte o post de blog [Azure VM falha com "Nenhum Servidor de Licença de Ambiente de Trabalho Remoto disponível"](/archive/blogs/mast/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>O Ambiente de Trabalho Remoto não consegue encontrar o "nome" do computador.
Causa: O cliente de ambiente de trabalho remoto no seu computador não consegue resolver o nome do computador nas definições do ficheiro RDP.

Soluções possíveis:

* Se estiver na intranet de uma organização, certifique-se de que o seu computador tem acesso ao servidor proxy e pode enviar-lhe tráfego HTTPS.
* Se estiver a utilizar um ficheiro RDP armazenado localmente, tente utilizar o que é gerado pelo portal. Este passo garante que tem o nome DNS correto para a máquina virtual, ou o serviço de nuvem e a porta de ponto final do VM. Aqui está uma amostra de ficheiro RDP gerado pelo portal:

    ```output
    full address:s:tailspin-azdatatier.cloudapp.net:55919
    prompt for credentials:i:1
    ```

A parte de endereço deste ficheiro RDP tem:

* O nome de domínio totalmente qualificado do serviço em nuvem que contém o VM ("tailspin-azdatatier.cloudapp.net" neste exemplo).
* A porta TCP externa do ponto final para o tráfego de ambiente de trabalho remoto (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contactada.
Causa: O VM alvo não consegue localizar a autoridade de segurança na parte do nome de utilizador das suas credenciais.

Quando o seu nome de utilizador está no formulário *SecurityAuthority* \\ *UserName* (exemplo: CORP\User1), a parte *SecurityAuthority* é o nome de computador do VM (para a autoridade de segurança local) ou um nome de domínio ative Diretório.

Soluções possíveis:

* Se a conta for local para o VM, certifique-se de que o nome VM está escrito corretamente.
* Se a conta estiver num domínio do Diretório Ativo, verifique a ortografia do nome de domínio.
* Se for uma conta de domínio do Ative Directory e o nome de domínio estiver corretamente escrito, verifique se um controlador de domínio está disponível nesse domínio. É uma questão comum nas redes virtuais do Azure que contêm controladores de domínio que um controlador de domínio está indisponível porque ainda não foi iniciado. Como uma solução alternativa, pode utilizar uma conta de administrador local em vez de uma conta de domínio.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Erro de segurança do Windows: As suas credenciais não funcionaram.
Causa: O VM alvo não pode validar o nome da sua conta e senha.

Um computador baseado no Windows pode validar as credenciais de uma conta local ou de uma conta de domínio.

* Para contas locais, utilize a sintaxe do nome de utilizador *Do Nome de Computador* \\ *UserName* (exemplo: SQL1\Admin4798).
* Para contas de domínio, utilize a sintaxe do nome de utilizador *do Nome de Domínio* \\ *UserName* (exemplo: CONTOSO\peterodman).

Se promoveu o seu VM a um controlador de domínio numa nova floresta de Diretório Ativo, a conta de administrador local com a qual assinou é convertida para uma conta equivalente com a mesma senha na nova floresta e domínio. A conta local é então apagada.

Por exemplo, se você assinou com a conta local DC1\DCAdmin, e depois promoveu a máquina virtual como controlador de domínio em uma nova floresta para o domínio corp.contoso.com, a conta local DC1\DCAdmin é eliminada e uma nova conta de domínio (CORP\DCAdmin) é criada com a mesma senha.

Certifique-se de que o nome da conta é um nome que a máquina virtual pode verificar como uma conta válida e que a palavra-passe está correta.

Se precisar de alterar a palavra-passe da conta de administrador local, consulte [como redefinir uma palavra-passe ou o serviço de ambiente de trabalho remoto para máquinas virtuais Do Windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Este computador não consegue ligar-se ao computador remoto.
Causa: A conta que é usada para ligar não tem direitos de entrada no Ambiente de Trabalho Remoto.

Todos os computadores Windows têm um grupo local de utilizadores de desktop remoto, que contém as contas e grupos que podem iniciar sê-lo remotamente. Os membros do grupo de administradores locais também têm acesso, mesmo que essas contas não estejam listadas no grupo local de utilizadores de desktop remoto. Para máquinas de domínio, o grupo de administradores locais também contém os administradores de domínio para o domínio.

Certifique-se de que a conta que está a utilizar para se ligar tem direitos de entrada no Ambiente de Trabalho Remoto. Como solução alternativa, utilize uma conta de administrador de domínio ou local para ligar ao Ambiente de Trabalho Remoto. Para adicionar a conta desejada ao grupo local de utilizadores de desktop remoto, utilize o snap-in do Microsoft Management Console **(Ferramentas de Sistema > Grupos > Grupos > locais > Utilizadores remotos de ambiente de trabalho).**

## <a name="next-steps"></a>Passos seguintes
Se nenhum destes erros ocorrer e tiver um problema desconhecido com a ligação utilizando RDP, consulte o [guia de resolução de problemas para o Ambiente de Trabalho Remoto](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Para obter etapas de resolução de problemas no acesso a aplicações em execução num VM, consulte [o acesso de Resolução de Problemas a uma aplicação em execução num VM Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).
* Se tiver problemas em utilizar a Secure Shell (SSH) para ligar a um Linux VM em Azure, consulte [as ligações SSH de resolução de problemas a um Linux VM em Azure](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
