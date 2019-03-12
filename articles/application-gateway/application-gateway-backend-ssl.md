---
title: Ativar SSL de ponta a ponta no Gateway de Aplicação do Azure | Microsoft Docs
description: Esta página fornece uma descrição geral do suporte de SSL de ponta a ponta do Gateway de Aplicação.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: amsriva
ms.openlocfilehash: 856f23de8a8772255f570a923ecf1708dc819bb5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778353"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Descrição geral do SSL ponto a ponto com o Gateway de Aplicação

O Gateway de Aplicação suporta a terminação de SSL no gateway, após o qual o tráfego normalmente flui desencriptado para os servidores de back-end. Esta funcionalidade permite que os servidores Web estejam livres de sobrecarga de encriptação e desencriptação dispendiosa. No entanto, para alguns clientes, a comunicação sem encriptação para os servidores de back-end não é uma opção aceitável. Esta comunicação desencriptada pode dever-se aos requisitos de segurança, requisitos de conformidade ou a aplicação pode aceitar apenas uma ligação segura. Para essas aplicações, o gateway de aplicação suporta a encriptação SSL de ponta a ponta.

## <a name="overview"></a>Descrição geral

O SSL de ponto a ponto permite-lhe transmitir dados confidenciais de forma segura para o back-end encriptado e tirar partido dos benefícios das funcionalidades de balanceamento de carga da Camada 7 que o gateway de aplicação proporciona. Algumas destas funcionalidades são a afinidade do cookie baseado na sessão, encaminhamento baseado em URL, suporte para encaminhamento baseado em sites ou capacidade para injetar cabeçalhos Encaminhados para X-*.

Quando configurado com o modo de comunicação SSL de ponta a ponta, o gateway de aplicação termina as sessões de SSL no gateway e desencripta o tráfego de utilizador. Em seguida, aplica as regras configuradas para selecionar uma instância de conjunto de back-end adequada para encaminhar o tráfego. O gateway de aplicação, em seguida, inicia uma nova ligação SSL ao servidor de back-end e encripta novamente os dados com o certificado de chave pública do servidor de back-end antes de transmitir o pedido para o back-end. O SSL de ponta a ponta é ativado ao configurar a definição do protocolo no BackendHTTPSetting como HTTPS que, em seguida, é aplicado a um conjunto de back-end. Cada servidor de back-end no conjunto de back-end com SSL de ponta a ponta ativado deve ser configurado com um certificado, para permitir a comunicação segura.

![cenário de SSL ponto a ponto][1]

Neste exemplo, os pedidos que utilizam o TLS1.2 são encaminhados para servidores de back-end no Pool1 através do SSL ponto a ponto.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL ponto a ponto e lista de certificados permitidos

O gateway de aplicação comunica apenas com instâncias de back-end conhecidas que colocaram o respetivo certificado na lista de permissões com o gateway de aplicação. Para ativar a lista de certificados permitidos, tem de carregar a chave pública dos certificados de servidor de back-end para o gateway de aplicação (e não o certificado de raiz). Apenas são permitidas ligações a back-ends conhecidos e na lista de permissões. Os restantes back-ends resultam num erro de gateway. Os certificados autoassinados são apenas para fins de teste e não são recomendados para cargas de trabalho de produção. Esses certificados têm de estar na lista de permissões com o gateway de aplicação, conforme descrito nos passos anteriores, para poderem ser utilizados.

## <a name="next-steps"></a>Passos Seguintes

Após a aprendizagem sobre SSL de ponto a ponto, aceda a [ativar SSL de ponta a ponta no gateway de aplicação](application-gateway-end-to-end-ssl-powershell.md), para criar um gateway de aplicação com SSL de ponto a ponto.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png
