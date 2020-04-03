---
title: Troubleshoot Azure Bastion [ Microsoft Docs
description: Neste artigo, aprenda a resolver os problemas de Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 749d7125c013f419197ef8243d2475e612dc81b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619174"
---
# <a name="troubleshoot-azure-bastion"></a>Resolver problemas do Azure Bastion

Este artigo mostra-lhe como resolver o tiroteio de Azure Bastion.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Incapaz de criar um NSG na AzureBastionSubnet

**Q:** Quando tento criar um NSG na subnet do Bastião Azure, tenho o seguinte erro: *"O <NSG name> grupo de segurança da rede não tem regras necessárias para a Azure Bastion Subnet AzureBastionSubnet".*

**A:** Se criar e aplicar um NSG à *AzureBastionSubnet,* certifique-se de ter adicionado as seguintes regras no seu NSG. Se não adicionar estas regras, a criação/atualização do NSG falhará.

1. Controlar a conectividade do avião – Entrada na 443 do GatewayManager
2. Diagnósticos de registo e outros – Saída em 443 para AzureCloud (As etiquetas regionais dentro desta etiqueta de serviço ainda não são suportadas.)
3. Target VM – Saída para 3389 e 22 para VirtualNetwork

Um exemplo das regras do NSG está disponível para referência no [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion).
Para mais informações, consulte [a orientação da NSG para o Azure Bastion](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Incapaz de usar a minha chave SSH com o Bastião Azure

**Q:** Quando tento navegar no meu ficheiro de teclaSS, tenho o seguinte erro: *'A tecla Privada SSH deve começar com -----BEGIN RSA PRIVATE KEY----- e termina com -----END RSA PRIVATE KEY-----'.*

**A:** A Azure Bastion suporta apenas as teclas RSA SSH, neste momento. Certifique-se de que navega num ficheiro chave que é chave privada RSA para SSH, com chave pública aprovisionada no VM alvo. 

Como exemplo, pode utilizar o seguinte comando para criar uma nova tecla RSA SSH:

**ssh-keygen -t rsa -b 4096 -C "email@domain.com**

Saída:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Incapaz de iniciar sessão na minha máquina virtual unida pelo domínio do Windows

**Q:** Não consigo ligar-me à minha máquina virtual do Windows que é unida ao domínio.

**A:** O Azure Bastion suporta o sinal de VM filiado no domínio apenas para o sign-in de domínio baseado em nome de utilizador. Ao especificar as credenciais de domínio no portalusername@domainAzure, utilize o formato UPN () em vez do formato *domínio\username* para iniciar sessão. Isto é suportado para máquinas virtuais unidas ao domínio ou híbridas (ambas unidas ao domínio, bem como a azure ad-joined) máquinas virtuais. Não é suportado para máquinas virtuais só com a AD Azure.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Problemas de transferência de ficheiros

**Q:** A transferência de ficheiros é suportada com o Azure Bastion?

**A:** A transferência de ficheiros não é suportada neste momento. Estamos a trabalhar na adição de apoio.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Tela preta no portal Azure

**Q:** Quando tento ligar-me usando o Azure Bastion, tenho um ecrã preto no portal Azure.

**A:** Isto acontece quando existe um problema de conectividade de rede entre o seu navegador web e o Azure Bastion (a firewall da Internet do seu cliente pode estar a bloquear o tráfego webSockets ou similar), ou entre o Bastião Azure e o seu VM alvo. A maioria dos casos inclui um NSG aplicado quer à AzureBastionSubnet, quer na sua subnet VM alvo que está a bloquear o tráfego RDP/SSH na sua rede virtual. Permita o tráfego de WebSockets na firewall da internet do seu cliente e verifique os NSGs na sua subnet VM alvo.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte o [Bastião FAQ](bastion-faq.md).