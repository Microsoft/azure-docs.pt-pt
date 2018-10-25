---
title: Permitido autoridades de certificação para ativar o HTTPS personalizado no serviço de porta de entrada do Azure | Documentos da Microsoft
description: Se estiver a utilizar o seu próprio certificado para ativar o HTTPS num domínio personalizado, tem de utilizar uma autoridade de certificados permitidos (AC) para criá-lo.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 5cf94079dcd68887d9725ffbe9124f9b6c897dd0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984771"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Permitido autoridades de certificação para ativar o HTTPS personalizado no serviço de porta de entrada do Azure

Para um domínio personalizado de serviço de porta de entrada do Azure, quando [ativar a funcionalidade HTTPS utilizando o seu próprio certificado](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), tem de utilizar uma autoridade de certificados permitidos (AC) para criar o seu certificado SSL. Caso contrário, se utilizar uma AC não permitido ou um certificado autoassinado, sua solicitação será rejeitada.

As CAs seguintes são permitidas quando cria seu próprio certificado:

- AddTrust externo AC de raiz
- AC de raiz de AlphaSSL
- Infra-estrutura AME 01 de AC
- Infra-estrutura AME AC 02
- Ameroot
- AC de raiz do Pacífico
- Autoridade de certificação de raiz de AP 2013
- Autoridade de certificação de raiz de AP de 2014
- APCA DM3P
- AC de raiz do autopilot
- Raiz da CyberTrust Baltimore
- Autoridade de certificação primária pública classe 3
- Autoridade de certificação COMODO RSA
- Servidor seguro do COMODO RSA domínio validação AC
- AC de classe 3 de raiz de confiança de D de 2009 2
- Serviços em nuvem da DigiCert no AC-1
- AC de raiz Global DigiCert
- DigiCert alta garantia AC-3
- AC de raiz de Assurance EV DigiCert alta
- DigiCert SHA2 estendido AC do servidor de validação
- Servidor de alta garantia de DigiCert SHA2 AC
- Servidor seguro da DigiCert SHA2 AC
- X3 de AC de raiz de horário de Verão
- AC de classe 3 de raiz de confiança de D de 2009 2
- Encriptação em qualquer lugar DV TLS AC
- Autoridade de certificação de raiz da Entrust
- G2 da autoridade de certificação de raiz - Entrust
- Autoridade de certificação Entrust.NET (2048)
- AC de GeoTrust Global
- Autoridade de certificação primária GeoTrust
- Autoridade de certificação primária GeoTrust - G2
- Geotrust RSA 2018 de AC
- GlobalSign
- GlobalSign estendido G2 de AC - SHA256 - validação
- Validação de organização GlobalSign AC - G2
- AC de raiz de GlobalSign
- Aceda a autoridade de certificação de raiz Daddy - G2
- Aceda a autoridade de certificação segura de Daddy - G2
- RapidSSL RSA 2018 de AC
- Agência de raiz
- AC de SSL do Symantec classe 3 EV - G3
- Classe da Symantec 3 servidor seguro AC - G4
- Symantec Enterprise Mobile Root para a Microsoft
- AC de raiz de Thawte primário
- AC de raiz do principal de Thawte - G2
- AC de raiz do principal de Thawte - G3
- Thawte RSA 2018 de AC
- Carimbo de Thawte AC
- TrustAsia TLS RSA AC
- AC de SSL de validação estendida 3 classe VeriSign
- Classe de VeriSign 3 validação estendida SSL SGC AC
- Autoridade de certificação primária pública classe 3 da VeriSign - G5
- Servidor internacional da VeriSign AC - classe 3
- Raiz do serviço de carimbo de data / hora da VeriSign
- Autoridade de certificação de raiz de VeriSign Universal
