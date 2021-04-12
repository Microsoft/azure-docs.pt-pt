---
title: Resolução de problemas Azure Bastion | Microsoft Docs
description: Neste artigo, aprenda a resolver problemas com Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: troubleshooting
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 23b7a66afcc91cf1cf4a5dd9f720aad24ad40071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98133995"
---
# <a name="troubleshoot-azure-bastion"></a>Resolver problemas do Azure Bastion

Este artigo mostra-lhe como resolver problemas com Azure Bastion.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Incapaz de criar um NSG em AzureBastionSubnet

**Q:** Quando tento criar um NSG na sub-rede Azure Bastion, recebo o seguinte erro: *"O grupo de segurança da rede <NSG name> não tem regras necessárias para a Subnet AzureBastionSubnet AzureBastionSubnet"*.

**A:** Se criar e aplicar um NSG ao *AzureBastionSubnet,* certifique-se de que adicionou as regras exigidas ao NSG. Para obter uma lista das regras exigidas, consulte [Trabalhar com acesso NSG e Azure Bastion](./bastion-nsg.md). Se não adicionar estas regras, a criação/atualização NSG falhará.

Um exemplo das regras NSG está disponível para referência no [modelo quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).
Para mais informações, consulte [a orientação da NSG para O Bastião Azure.](bastion-nsg.md)

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Incapaz de usar a minha chave SSH com Azure Bastion

**Q:** Quando tento navegar no meu ficheiro de chave SSH, recebo o seguinte erro: *"A chave SSH Private deve começar com -----BEGIN RSA PRIVATE KEY----- e termina com -----END RSA PRIVATE KEY-----'.*

**A:** Azure Bastion suporta apenas as teclas RSA SSH, neste momento. Certifique-se de que navega num ficheiro chave que é a chave privada RSA para SSH, com a chave pública prevista no VM alvo. 

Como exemplo, pode utilizar o seguinte comando para criar uma nova chave RSA SSH:

**ssh-keygen -t rsa -b 4096 -C email@domain.com "**

Resultado:

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

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Incapaz de iniciar sedutil na minha máquina virtual de domínio Windows

**Q:** Não consigo ligar-me à minha máquina virtual do Windows que está ligada ao domínio.

**A:** O Azure Bastion suporta o sismo VM associado ao domínio apenas para o pedido de sismo baseado em password de utilizador. Ao especificar as credenciais de domínio no portal Azure, utilize o formato UPN username@domain () em vez do formato *de nome de utilizador de domínio para* iniciar sing. Isto é suportado para máquinas virtuais unidas ao domínio ou híbridas (ambas unidas ao domínio, bem como a ad-joined AZure). Não é suportado para máquinas virtuais só aderidas a Azure.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Problemas de transferência de ficheiros

**Q:** A transferência de ficheiros é suportada com Azure Bastion?

**A:** A transferência de ficheiros não é suportada neste momento. Estamos a trabalhar para adicionar apoio.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Tela preta no portal Azure

**Q:** Quando tento ligar-me usando O Bastião Azure, não consigo enganar o VM alvo e tenho uma tela preta no portal Azure.

**A:** Isto acontece quando existe um problema de conectividade de rede entre o seu navegador web e O Azure Bastion (a firewall da Internet do seu cliente pode estar bloqueando o tráfego de WebSockets ou similares), ou entre o Bastião Azure e o seu VM alvo. A maioria dos casos inclui um NSG aplicado quer à AzureBastionSubnet, quer à sub-rede VM alvo que está a bloquear o tráfego RDP/SSH na sua rede virtual. Permita o tráfego webSockets na firewall da internet do seu cliente e verifique os NSGs na sub-rede VM do seu alvo.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte as [FAQ do Bastião.](bastion-faq.md)
