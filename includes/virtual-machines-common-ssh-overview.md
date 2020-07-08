---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "71168627"
---
## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e chaves

[SSH](https://www.ssh.com/ssh/) é um protocolo de ligação encriptado que permite iniciar s-ins seguros sobre ligações não seguras. SSH é o protocolo de ligação padrão para Os VMs Linux hospedados em Azure. Embora o próprio SSH forneça uma ligação encriptada, usar palavras-passe com ligações SSH ainda deixa o VM vulnerável a ataques de força bruta ou adivinhação de palavras-passe. Um método mais seguro e preferido de ligação a um VM utilizando SSH é utilizando um par de chaves público-privado, também conhecido como *teclas SSH*. 

* A *chave pública* é colocada no seu Linux VM, ou em qualquer outro serviço que deseje utilizar com criptografia de chave pública.

* A *chave privada* permanece no seu sistema local. Proteja esta chave privada. Não a partilhe.

Quando utiliza um cliente SSH para ligar ao seu VM Linux (que tem a chave pública), o VM remoto testa o cliente para se certificar de que possui a chave privada. Se o cliente tem a chave privada, tem acesso ao VM. 

Dependendo das políticas de segurança da sua organização, pode reutilizar um único par de chaves público-privado para aceder a vários VMs e serviços Azure. Não precisa de um par de chaves separado para cada VM ou serviço a que deseje aceder. 

A sua chave pública pode ser partilhada com qualquer pessoa, mas apenas você (ou a sua infraestrutura de segurança local) deve possuir a sua chave privada.