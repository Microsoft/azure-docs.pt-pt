---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168627"
---
## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e das chaves

O [SSH](https://www.ssh.com/ssh/) é um protocolo de conexão criptografado que permite entradas seguras em conexões não seguras. SSH é o protocolo de conexão padrão para VMs Linux hospedadas no Azure. Embora o SSH em si forneça uma conexão criptografada, o uso de senhas com conexões SSH ainda deixa a VM vulnerável a ataques de força bruta ou adivinhação de senhas. Um método mais seguro e preferencial de se conectar a uma VM usando o SSH é usar um par de chaves pública-privada, também conhecido como *chaves SSH*. 

* A *chave pública* é colocada em sua VM do Linux ou em qualquer outro serviço que você queira usar com a criptografia de chave pública.

* A *chave privada* permanece em seu sistema local. Proteja esta chave privada. Não a partilhe.

Quando você usa um cliente SSH para se conectar à VM do Linux (que tem a chave pública), a VM remota testa o cliente para verificar se ele possui a chave privada. Se o cliente tiver a chave privada, ele terá acesso à VM. 

Dependendo das políticas de segurança de sua organização, você pode reutilizar um único par de chaves públicas-privadas para acessar várias VMs e serviços do Azure. Você não precisa de um par separado de chaves para cada VM ou serviço que você deseja acessar. 

Sua chave pública pode ser compartilhada com qualquer pessoa, mas apenas você (ou sua infraestrutura de segurança local) deve ter sua chave privada.