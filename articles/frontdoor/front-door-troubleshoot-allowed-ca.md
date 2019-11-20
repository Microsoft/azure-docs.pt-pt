---
title: Autoridade de certificação permitida para habilitar HTTPS personalizado no serviço de porta de recepção do Azure
description: Se você estiver usando seu próprio certificado para habilitar o HTTPS em um domínio personalizado, deverá usar uma autoridade de certificação (CA) permitida para criá-lo.
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
ms.openlocfilehash: 62420889d9a4cb1e9d1c570a0845c704fca56cb3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184585"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Autoridades de certificação permitidas para habilitar o HTTPS personalizado no serviço de porta frontal do Azure

Para um domínio personalizado do serviço de porta frontal do Azure, ao [habilitar o recurso https usando seu próprio certificado](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), você deve usar uma autoridade de certificação (CA) permitida para criar seu certificado SSL. Caso contrário, se você usar uma autoridade de certificação não permitida ou um certificado autoassinado, sua solicitação será rejeitada.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
