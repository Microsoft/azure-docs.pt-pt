---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184890"
---
Os registos de quadro de política de remetente (SPF) são utilizados para especificar quais os servidores de e-mail que podem enviar e-mails em nome de um nome de domínio. A configuração correta dos registos SPF é importante para evitar que os destinatários marquem o seu e-mail como lixo.

Os RFCs DNS introduziram originalmente um novo tipo de registo SPF para apoiar este cenário. Para suportar servidores de nomes mais antigos, também permitiram a utilização do tipo de registo TXT para especificar registos SPF. Esta ambiguidade levou à confusão, que foi resolvida pelo [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Diz que os registos SPF devem ser criados utilizando o tipo de registo TXT. Também afirma que o tipo de registo SPF é depreciado.

**Os registos SPF são suportados pelo Azure DNS e devem ser criados utilizando o tipo de registo TXT.** O tipo de registo SPF obsoleto não é suportado. Quando [importa um ficheiro de zona DNS,](../articles/dns/dns-import-export.md)quaisquer registos SPF que utilizem o tipo de registo SPF são convertidos para o tipo de registo TXT.
