---
title: Autoridade de certificação permitida para habilitar HTTPS personalizado na CDN do Azure
description: Se você estiver usando seu próprio certificado para habilitar o HTTPS em um domínio personalizado, deverá usar uma autoridade de certificação (CA) permitida para criá-lo.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5462502514a3e327913122fe99fd699856891216
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083107"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autoridades de certificação permitidas para habilitar HTTPS personalizado na CDN do Azure

Para um domínio personalizado da CDN (rede de distribuição de conteúdo) do Azure em um **padrão da CDN do Azure do Microsoft** EndPoint, ao [habilitar o recurso https usando seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), você deve usar uma autoridade de certificação (CA) permitida para criar seu certificado SSL. Caso contrário, se você usar uma autoridade de certificação não permitida ou um certificado autoassinado, sua solicitação será rejeitada.

> [!NOTE]
> A opção de usar seu próprio certificado para habilitar o HTTPS personalizado está disponível somente com o **Azure CDN Standard de perfis da Microsoft** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

