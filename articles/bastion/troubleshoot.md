---
title: Solucionar problemas do Azure bastião | Microsoft Docs
description: Neste artigo, saiba como solucionar problemas de bastiões do Azure.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: de112ff441bb53a0b3bc7f4ffa4456f1c241682c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512954"
---
# <a name="troubleshoot-azure-bastion"></a>Solucionar problemas de bastiões do Azure

Este artigo mostra como solucionar problemas de bastiões do Azure.

## <a name="nsg"></a>Não é possível criar um NSG em AzureBastionSubnet

**P:** Quando tento criar um NSG na sub-rede de bastiões do Azure, obtenho o seguinte erro: *' grupo de segurança de rede <NSG name> não tem as regras necessárias para a AzureBastionSubnet de sub-rede de bastiões do Azure "* .

**R:** Se você criar e aplicar um NSG ao *AzureBastionSubnet*, certifique-se de ter adicionado as regras a seguir em seu NSG. Se você não adicionar essas regras, a criação/atualização do NSG falhará.

1. Conectividade do plano de controle – entrada em 443 do Gatewaymanager
2. Log de diagnóstico e outros – saída em 443 para AzureCloud (as marcas regionais nesta marcação de serviço ainda não têm suporte.)
3. VM de destino – saída de 3389 e 22 para VirtualNetwork

Um exemplo das regras NSG está disponível para referência no modelo de [início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion).
Para obter mais informações, consulte [NSG Guidance for Azure bastiões](bastion-nsg.md).

## <a name="sshkey"></a>Não é possível usar minha chave SSH com a bastiões do Azure

**P:** Quando tento procurar meu arquivo de chave SSH, obtenho o seguinte erro: ' a *chave privada SSH deve começar com-----iniciar a chave privada rsa-----e termina com-----end RSA Private key-----'* .

**R:** A bastiões do Azure dá suporte apenas a chaves de SSH da RSA, neste momento. Verifique se você navegou um arquivo de chave que é RSA Private Key para SSH, com a chave pública provisionada na VM de destino. 

Por exemplo, você pode usar o seguinte comando para criar uma nova chave RSA SSH:

**ssh-keygen-t RSA-b 4096-C "email@domain.com"**

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

## <a name="domain"></a>Não é possível entrar na minha máquina virtual ingressada no domínio do Windows

**P:** Não consigo me conectar à minha máquina virtual do Windows que ingressou no domínio.

**R:** A bastiões do Azure dá suporte à entrada de VM ingressada no domínio somente para logon de domínio baseado em senha de nome de usuário. Ao especificar as credenciais de domínio no portal do Azure, use o formato UPN (username@domain) em vez de *Domain* Format para entrar. Isso tem suporte para máquinas virtuais associadas ao domínio ou ao ingresso híbrido (ingressado no domínio, bem como ao Azure AD). Não há suporte para máquinas virtuais somente ingressadas no Azure AD.

## <a name="filetransfer"></a>Problemas de transferência de arquivo

**P:** A transferência de arquivos tem suporte com a bastiões do Azure?

**R:** Não há suporte para a transferência de arquivos no momento. Estamos trabalhando para adicionar suporte.

## <a name="blackscreen"></a>Tela preta no portal do Azure

**P:** Quando tento me conectar usando a bastiões do Azure, obtenho uma tela preta na portal do Azure.

**R:** Isso acontece quando há um problema de conectividade de rede entre o navegador da Web e a bastiões do Azure (o firewall de Internet do cliente pode estar bloqueando o tráfego do WebSocket ou semelhante) ou entre a bastiões do Azure e a VM de destino. A maioria dos casos inclui um NSG aplicado ao AzureBastionSubnet ou à sua sub-rede VM de destino que está bloqueando o tráfego RDP/SSH em sua rede virtual. Permita o tráfego do WebSocket no firewall de Internet do cliente e verifique o NSGs em sua sub-rede de VM de destino.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte as [perguntas frequentes sobre bastiões](bastion-faq.md).