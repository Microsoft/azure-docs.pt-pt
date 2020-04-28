---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184118"
---
Se tiver problemas de ligação, verifique os seguintes itens:

- Se exportou um certificado de cliente com **Certificado De Exportação,** certifique-se de que o exportou como ficheiro .pfx e selecionado **Inclua todos os certificados na via de certificação, se possível**. Quando a exporta com este valor, a informação do certificado de raiz também é exportada. Depois de instalar o certificado no computador cliente, o certificado de raiz no ficheiro .pfx também está instalado. Para verificar se o certificado raiz está instalado, abra **os certificados de utilizador e** selecione Trusted Root Certification **Authorities\Certificates**. Verifique se o certificado de raiz está listado, que deve estar presente para autenticação funcionar.

- Se usou um certificado emitido por uma solução Enterprise CA e não pode autenticar, verifique a ordem de autenticação no certificado de cliente. Verifique a ordem da lista de autenticação clicando duas vezes no certificado de cliente, selecionando o separador **Detalhes** e, em seguida, selecionando a **utilização**da chave melhorada . Certifique-se de que a *Autenticação* do Cliente é o primeiro item da lista. Caso não seja, emita um certificado de cliente com base no modelo de utilizador que tem a *Autenticação* do Cliente como o primeiro item da lista.

- Para obter informações adicionais sobre a resolução de problemas com P2S, veja [Resolução de problemas com ligações P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).