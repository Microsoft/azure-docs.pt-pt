---
title: Ca permitido para ativar HTTPS personalizado na porta frontal Azure
description: Se estiver a utilizar o seu próprio certificado para ativar HTTPS num domínio personalizado, deve utilizar uma autoridade de certificados permitida (CA) para o criar.
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
ms.openlocfilehash: 611f5730afed4c3a84b81d6acfd33b633c532bbc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80874675"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autoridades de certificados autorizadas para permitir https personalizados na porta da frente Azure

Para um domínio personalizado Azure Front Door, quando [ativar a funcionalidade HTTPS utilizando o seu próprio certificado,](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)deve utilizar uma autoridade de certificados permitida (CA) para criar o seu certificado TLS/SSL. Caso contrário, se utilizar um CA não permitido ou um certificado auto-assinado, o seu pedido será rejeitado.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
