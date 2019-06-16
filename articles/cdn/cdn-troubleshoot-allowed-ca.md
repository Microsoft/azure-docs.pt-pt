---
title: Permitido autoridades de certificação para ativar o HTTPS personalizado na CDN do Azure | Documentos da Microsoft
description: Se estiver a utilizar o seu próprio certificado para ativar o HTTPS num domínio personalizado, tem de utilizar uma autoridade de certificados permitidos (AC) para criá-lo.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 11651c2721756a4f750a5a5e78f86fdbd363fb9d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60323523"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Permitido autoridades de certificação para ativar o HTTPS personalizado na CDN do Azure

Para um domínio personalizado da rede de entrega conteúdo (CDN) numa **CDN Standard do Microsoft Azure** ponto final, quando [ativar a funcionalidade HTTPS utilizando o seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), tem de utilizar um permitidos autoridade de certificação (AC) para criar o seu certificado SSL. Caso contrário, se utilizar uma AC não permitido ou um certificado autoassinado, sua solicitação será rejeitada.

> [!NOTE]
> A opção de utilizar o seu próprio certificado para ativar o HTTPS personalizados está disponível apenas com **CDN Standard do Microsoft Azure** perfis. 
>

As CAs seguintes são permitidas quando cria seu próprio certificado:

- AddTrust externo AC de raiz
- AlphaSSL Root CA
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- APCA-DM3P
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
- DST Root CA X3
- AC de classe 3 de raiz de confiança de D de 2009 2
- Encriptação em qualquer lugar DV TLS AC
- Autoridade de certificação de raiz da Entrust
- G2 da autoridade de certificação de raiz - Entrust
- Entrust.net Certification Authority (2048)
- AC de GeoTrust Global
- Autoridade de certificação primária GeoTrust
- Autoridade de certificação primária GeoTrust - G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign estendido G2 de AC - SHA256 - validação
- GlobalSign Organization Validation CA - G2
- AC de raiz de GlobalSign
- Aceda a autoridade de certificação de raiz Daddy - G2
- Aceda a autoridade de certificação segura de Daddy - G2
- QuoVadis Root CA2 G3
- RapidSSL RSA CA 2018
- AC de SSL do Symantec classe 3 EV - G3
- Classe da Symantec 3 servidor seguro AC - G4
- Symantec Enterprise Mobile Root for Microsoft
- AC de raiz de Thawte primário
- AC de raiz do principal de Thawte - G2
- AC de raiz do principal de Thawte - G3
- Thawte RSA CA 2018
- Carimbo de Thawte AC
- TrustAsia TLS RSA CA
- AC de SSL de validação estendida 3 classe VeriSign
- Classe de VeriSign 3 validação estendida SSL SGC AC
- Autoridade de certificação primária pública classe 3 da VeriSign - G5
- Servidor internacional da VeriSign AC - classe 3
- Raiz do serviço de carimbo de data / hora da VeriSign
- Autoridade de certificação de raiz de VeriSign Universal


