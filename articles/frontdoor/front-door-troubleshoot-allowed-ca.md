---
title: Ca permitido para ativar HTTPS personalizado na porta frontal Azure
description: Se estiver a utilizar o seu próprio certificado para ativar HTTPS num domínio personalizado, deve utilizar uma autoridade de certificados permitida (CA) para o criar.
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
ms.openlocfilehash: 7bdef37561687b49b030d8237472c0d35f945c13
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399128"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autoridades de certificados autorizadas para permitir https personalizados na porta da frente Azure

Para um domínio personalizado Azure Front Door, quando [ativar a funcionalidade HTTPS utilizando o seu próprio certificado,](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)deve utilizar uma autoridade de certificados permitida (CA) para criar o seu certificado TLS/SSL. Caso contrário, se utilizar um CA não permitido ou um certificado auto-assinado, o seu pedido será rejeitado.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
