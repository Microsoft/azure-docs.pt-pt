---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016386"
---
## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e chaves

[SSH](https://www.ssh.com/ssh/) é um protocolo de ligação encriptado que fornece acessos seguros sobre ligações não seguras. SSH é o protocolo de ligação padrão para Os VMs Linux hospedados em Azure. Embora o SSH forneça uma ligação encriptada, a utilização de palavras-passe com ligações SSH ainda deixa o VM vulnerável a ataques de força bruta. Recomendamos a ligação a um VM sobre SSH utilizando um par de chaves público-privado, também conhecido como *teclas SSH*. 

- A *chave pública* é colocada no seu Linux VM.

- A *chave privada* permanece no seu sistema local. Proteja esta chave privada. Não a partilhe.

Quando utiliza um cliente SSH para ligar ao seu VM Linux (que tem a chave pública), o VM remoto testa o cliente para se certificar de que tem a chave privada correta. Se o cliente tem a chave privada, tem acesso ao VM. 

Dependendo das políticas de segurança da sua organização, pode reutilizar um único par de chaves público-privado para aceder a vários VMs e serviços Azure. Não precisa de um par de chaves separado para cada VM ou serviço a que deseje aceder. 

A sua chave pública pode ser partilhada com qualquer pessoa, mas apenas você (ou a sua infraestrutura de segurança local) deve ter acesso à sua chave privada.