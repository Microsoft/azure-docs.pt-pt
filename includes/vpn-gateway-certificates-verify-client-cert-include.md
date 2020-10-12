---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184118"
---
Se tiver problemas em ligar, verifique os seguintes itens:

- Se exportar um certificado de cliente com **Certificado De exportação de Assistente,** certifique-se de que o exportou como ficheiro .pfx e selecionado **Inclua todos os certificados na rota de certificação, se possível.** Ao exportá-lo com este valor, a informação do certificado de raiz também é exportada. Depois de instalar o certificado no computador cliente, o certificado raiz no ficheiro .pfx também é instalado. Para verificar se o certificado raiz está instalado, abra **os certificados de utilizador do Manage e** selecione As **Autoridades de Certificação de Raiz Fidedignas\Certificados**. Verifique se o certificado de raiz está listado, que deve estar presente para a autenticação funcionar.

- Se utilizou um certificado emitido por uma solução Enterprise CA e não pode autenticar, verifique a ordem de autenticação no certificado do cliente. Consulte a ordem da lista de autenticação clicando duas vezes no certificado do cliente, selecionando o separador **Detalhes** e, em seguida, selecionando **Utilização melhorada da chave**. Certifique-se de que *a Autenticação* do Cliente é o primeiro item da lista. Caso não seja, emita um certificado de cliente com base no modelo de utilizador que tem *a Autenticação* do Cliente como o primeiro item da lista.

- Para obter informações adicionais sobre a resolução de problemas com P2S, veja [Resolução de problemas com ligações P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).