---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 74031a8dbc9b64d6a09533789eed1296ff334d47
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67184920"
---
Uma zona DNS é utilizada para alojar os registos DNS para um determinado domínio. Para começar a alojar o seu domínio no DNS do Azure, tem de criar uma zona DNS para esse nome de domínio. Cada registo DNS para o seu domínio é então criado no interior desta zona DNS.

Por exemplo, o domínio “contoso.com” pode conter vários registos DNS, como “mail.contoso.com” (para um servidor de e-mail) e “www.contoso.com” (para um Web site).

Ao criar uma zona DNS no Azure DNS:

* O nome da zona deve ser exclusivo dentro do grupo de recursos e a zona não pode já existir. Caso contrário, a operação falha.
* O mesmo nome de zona pode ser reutilizado num grupo de recursos diferente ou numa subscrição diferente do Azure.
* Onde as várias zonas partilham o mesmo nome, cada instância é atribuída a diferentes endereços do servidor do nome. Apenas um conjunto de endereços pode ser configurado com a entidade de registo de nome de domínio.

> [!NOTE]
> Não é necessário possuir um nome de domínio para criar uma zona DNS com esse nome de domínio no DNS do Azure. No entanto, tem de ser o proprietário do domínio para configurar os servidores de nomes DNS do Azure como os servidores de nomes corretos para o nome de domínio com a entidade de registo do nome de domínio.
> 
> Para mais informações, consulte [Delegar um domínio para o DNS Azure](../articles/dns/dns-domain-delegation.md).