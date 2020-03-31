---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184890"
---
Os registos de quadros de política do remetente (SPF) são usados para especificar quais servidores de e-mail podem enviar e-mail em nome de um nome de domínio. A configuração correta dos registos SPF é importante para evitar que os destinatários marcam o seu e-mail como lixo.

Os DNS RFCs introduziram originalmente um novo tipo de registo SPF para suportar este cenário. Para suportar servidores de nomes mais antigos, também permitiram a utilização do tipo de registo TXT para especificar os registos SPF. Esta ambiguidade levou à confusão, que foi resolvida pelo [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Diz que os registos SPF devem ser criados utilizando o tipo de registo TXT. Também afirma que o tipo de registo SPF é depreciado.

**Os registos SPF são suportados pelo Azure DNS e devem ser criados utilizando o tipo de registo TXT.** O obsoleto tipo de registo SPF não é suportado. Quando [importa um ficheiro de zona DNS,](../articles/dns/dns-import-export.md)quaisquer registos SPF que utilizem o tipo de registo SPF são convertidos para o tipo de registo TXT.
