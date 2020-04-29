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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71168627"
---
## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e das chaves

[O SSH](https://www.ssh.com/ssh/) é um protocolo de ligação encriptado que permite inscrições seguras sobre ligações não seguras. SSH é o protocolo de ligação padrão para VMs Linux hospedado si em Azure. Embora o Próprio SSH forneça uma ligação encriptada, o uso de senhas com ligações SSH ainda deixa o VM vulnerável a ataques de força bruta ou a adivinhação de senhas. Um método mais seguro e preferido de ligação a um VM utilizando SSH é utilizando um par de chaves público-privado, também conhecido como *teclas SSH*. 

* A *chave pública* é colocada no seu VM Linux, ou em qualquer outro serviço que deseje utilizar com criptografia de chave pública.

* A *chave privada* permanece no seu sistema local. Proteja esta chave privada. Não a partilhe.

Quando utiliza um cliente SSH para se ligar ao seu VM Linux (que tem a chave pública), o VM remoto testa o cliente para se certificar de que possui a chave privada. Se o cliente tem a chave privada, tem acesso ao VM. 

Dependendo das políticas de segurança da sua organização, pode reutilizar um único par de chaves público-privadas para aceder a vários VMs e serviços Azure. Não precisa de um par de chaves separadas para cada VM ou serviço a que deseja aceder. 

A sua chave pública pode ser partilhada com qualquer pessoa, mas só você (ou a sua infraestrutura de segurança local) deve possuir a sua chave privada.