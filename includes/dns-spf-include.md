---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331334"
---
Registos de framework (SPF) de política de remetente são utilizados para especificar quais os servidores de e-mail podem enviar um e-mail em nome de um nome de domínio. Configuração correta dos registros SPF é importante para impedir que os destinatários marcar o seu e-mail como lixo.

Os RFCs DNS originalmente introduzido um novo tipo de registo do SPF para suportar este cenário. Para suportar servidores de nomes mais antigos, eles também permitiu a utilização do tipo de registo TXT para especificar os registos SPF. Esta ambiguidade levou à confusão, que foi resolvido pelo [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Ele diz que os registos SPF devem ser criados com o tipo de registo TXT. Também determina que o tipo de registo do SPF foi preterido.

**Os registos SPF são suportados pelo DNS do Azure e tem de ser criados utilizando o tipo de registo TXT.** Não é suportado o tipo de registo do SPF obsoleto. Quando [importar um ficheiro de zona DNS](../articles/dns/dns-import-export.md), todos os registos SPF que utilizam o tipo de registo do SPF são convertidos para o tipo de registo TXT.
