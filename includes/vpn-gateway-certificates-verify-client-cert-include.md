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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184118"
---
Se tiver problemas em ligar, verifique os seguintes itens:

- Se exportou um certificado de cliente com **Assistente para exportar certificados**, certifique-se de que exportou-lo como um ficheiro. pfx e selecionado **incluir todos os certificados no caminho de certificação se possível**. Quando o exporta com este valor, as informações do certificado de raiz também são exportadas. Depois de instalar o certificado no computador cliente, o certificado de raiz no arquivo. pfx também é instalado. Para verificar se o certificado de raiz é instalado, abra **gerir certificados de utilizador** e selecione **fidedigno certificação de raiz Fidedigna\Certificados**. Certifique-se de que o certificado de raiz está listado, o que deve estar presente para a autenticação funcione.

- Se utilizou um certificado que foi emitido por uma solução de AC empresarial e que não é possível realizar a autenticação, certifique-se a ordem de autenticação do certificado de cliente. Verifique a ordem da lista de autenticação clicando duas vezes o certificado de cliente, selecionando o **detalhes** separador e, em seguida, selecionando **utilização de chave avançada**. Certifique-se *autenticação de cliente* é o primeiro item na lista. Se não estiver, emitir um certificado de cliente com base no modelo de utilizador que tenha *autenticação de cliente* como o primeiro item na lista.

- Para obter informações adicionais sobre a resolução de problemas com P2S, veja [Resolução de problemas com ligações P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).