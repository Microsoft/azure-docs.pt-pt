---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184890"
---
Os registos de quadro de política de remetente (SPF) são utilizados para especificar quais os servidores de e-mail que podem enviar e-mails em nome de um nome de domínio. A configuração correta dos registos SPF é importante para evitar que os destinatários marquem o seu e-mail como lixo.

Os RFCs DNS introduziram originalmente um novo tipo de registo SPF para apoiar este cenário. Para suportar servidores de nomes mais antigos, também permitiram a utilização do tipo de registo TXT para especificar registos SPF. Esta ambiguidade levou à confusão, que foi resolvida pelo [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Diz que os registos SPF devem ser criados utilizando o tipo de registo TXT. Também afirma que o tipo de registo SPF é depreciado.

**Os registos SPF são suportados pelo Azure DNS e devem ser criados utilizando o tipo de registo TXT.** O tipo de registo SPF obsoleto não é suportado. Quando [importa um ficheiro de zona DNS,](../articles/dns/dns-import-export.md)quaisquer registos SPF que utilizem o tipo de registo SPF são convertidos para o tipo de registo TXT.
