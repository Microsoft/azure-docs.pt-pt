---
title: CA permitido para permitir HTTPS personalizado na porta da frente azure
description: Se estiver a utilizar o seu próprio certificado para ativar https num domínio personalizado, deve utilizar uma autoridade de certificado saneada (CA) para o criar.
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
ms.openlocfilehash: cac6bc9895f2b8778f2b27cc6b1dff4d4b898ae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471528"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autoridades de certificados permitidas para permitir HTTPS personalizado na Porta da Frente Azure

Para um domínio personalizado azure Front Door, quando [ativa a funcionalidade HTTPS utilizando o seu próprio certificado,](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)deve utilizar uma autoridade de certificado permitida (CA) para criar o seu certificado SSL. Caso contrário, se utilizar um Ca não permitido ou um certificado auto-assinado, o seu pedido será rejeitado.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
