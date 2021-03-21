---
title: Autoridades de certificados permitidas para Azure Front Door Standard/Premium (Pré-visualização)
description: Este artigo lista todas as autoridades de certificados permitidas quando cria o seu próprio certificado.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: e31ad0734630fbd0b4960c26f8d562cea66ae675
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434871"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Autoridades de certificados permitidas para Azure Front Door Standard/Premium (Pré-visualização)

Quando ativa a função HTTPS utilizando o seu próprio certificado para um domínio personalizado Azure Front Door Standard/Premium. Precisa de uma autoridade de certificados permitida (CA) para criar o seu certificado TLS/SSL. Caso contrário, se utilizar um CA não permitido ou um certificado auto-assinado, o seu pedido será rejeitado.

[!INCLUDE [cdn-front-door-allowed-ca](../../../includes/cdn-front-door-allowed-ca.md)]
