---
title: Permitido autoridades de certificação para ativar o HTTPS personalizado na CDN do Azure | Documentos da Microsoft
description: Se estiver a utilizar o seu próprio certificado para ativar o HTTPS num domínio personalizado, tem de utilizar uma autoridade de certificados permitidos (AC) para criá-lo.
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
ms.openlocfilehash: 754941163ddce9512870f0b76a96207472e5b2aa
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593350"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Permitido autoridades de certificação para ativar o HTTPS personalizado na CDN do Azure

Para um domínio personalizado da rede de entrega conteúdo (CDN) numa **CDN Standard do Microsoft Azure** ponto final, quando [ativar a funcionalidade HTTPS utilizando o seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), tem de utilizar um permitidos autoridade de certificação (AC) para criar o seu certificado SSL. Caso contrário, se utilizar uma AC não permitido ou um certificado autoassinado, sua solicitação será rejeitada.

> [!NOTE]
> A opção de utilizar o seu próprio certificado para ativar o HTTPS personalizados está disponível apenas com **CDN Standard do Microsoft Azure** perfis. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

