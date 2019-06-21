---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: bca78e2963f19b60071b1b27c8dc65c76818e10e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184269"
---
## <a name="overview-of-ssh-and-keys"></a>Descrição geral de SSH e chaves

[SSH](https://www.ssh.com/ssh/) é um protocolo de conexão criptografada que permite que os inícios de sessão seguros através de ligações não seguras. O SSH é o protocolo de ligação predefinido para VMs do Linux alojados no Azure. Embora o SSH em si fornece uma ligação encriptada, utilizar palavras-passe com ligações SSH ainda deixa a VM vulnerável a força bruta ataques ou a adivinhação de palavras-passe. Um método mais seguro e preferencial de ligação a uma VM com SSH é utilizar um par de chaves públicas-privadas, também conhecido como *chaves SSH*. 

* O *chave pública* é colocada na sua VM do Linux, ou qualquer outro serviço que deseja usar com a criptografia de chave pública.

* O *chave privada* permanece no sistema local. Proteja esta chave privada. Não a partilhe.

Quando utiliza um cliente SSH para ligar à sua VM do Linux (que tem a chave pública), a VM remota testa o cliente para se certificar de que ele possui a chave privada. Se o cliente tiver a chave privada, é concedido acesso à VM. 

Dependendo das políticas de segurança da sua organização, pode reutilizar um único par de chaves públicas-privadas para aceder a várias VMs do Azure e serviços. Não é necessário um separado par de chaves para cada VM ou serviço que pretende aceder. 

A chave pública pode ser partilhada com ninguém, mas apenas (ou a infraestrutura de segurança local) deve possuir a sua chave privada.