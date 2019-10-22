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
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "67184118"
---
Se você tiver problemas para se conectar, verifique os seguintes itens:

- Se você exportou um certificado de cliente com o **Assistente para exportação de certificados**, certifique-se de que você o exportou como um arquivo. pfx e selecionado **incluir todos os certificados no caminho de certificação, se possível**. Quando você o exporta com esse valor, as informações do certificado raiz também são exportadas. Depois de instalar o certificado no computador cliente, o certificado raiz no arquivo. pfx também é instalado. Para verificar se o certificado raiz está instalado, abra **gerenciar certificados de usuário** e selecione **Authorities\Certificates de certificação de raiz confiável**. Verifique se o certificado raiz está listado, que deve estar presente para que a autenticação funcione.

- Se você usou um certificado que foi emitido por uma solução de AC corporativa e não puder autenticar, verifique a ordem de autenticação no certificado do cliente. Verifique a ordem da lista de autenticação clicando duas vezes no certificado do cliente, selecionando a guia **detalhes** e, em seguida, selecionando **uso avançado de chave**. Verifique se a *autenticação do cliente* é o primeiro item da lista. Se não estiver, emita um certificado de cliente com base no modelo de usuário que tem a *autenticação de cliente* como o primeiro item na lista.

- Para obter informações adicionais sobre a resolução de problemas com P2S, veja [Resolução de problemas com ligações P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).