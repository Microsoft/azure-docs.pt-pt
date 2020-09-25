---
title: Ca permitido para ativar HTTPS personalizado na porta frontal Azure
description: Se estiver a utilizar o seu próprio certificado para ativar HTTPS num domínio Azure Front Door 0custom, deve utilizar uma autoridade de certificados permitida (CA) para o criar.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 973df2505eefc2a46aa105b874f32b61fe6e8b36
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269811"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autoridades de certificados autorizadas para permitir https personalizados na porta da frente Azure

Para um domínio personalizado Azure Front Door, quando [ativar a funcionalidade HTTPS utilizando o seu próprio certificado,](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)deve utilizar uma autoridade de certificados permitida (CA) para criar o seu certificado TLS/SSL. Caso contrário, se utilizar um CA não permitido ou um certificado auto-assinado, o seu pedido será rejeitado.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
