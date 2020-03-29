---
title: CA permitido para ativar HTTPS personalizado em Azure CDN
description: Se estiver a utilizar o seu próprio certificado para ativar https num domínio personalizado, deve utilizar uma autoridade de certificado saneada (CA) para o criar.
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
ms.openlocfilehash: 7b71611d43bc2d4de4c3e609462906c44fba0443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919979"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autoridades de certificados autorizadas por permitir HTTPS personalizado no Azure CDN

Deve cumprir os requisitos específicos do certificado quando [ativa ré](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) https utilizando o seu próprio certificado para um domínio personalizado da Rede de Entrega de Conteúdos Azure (CDN). O **Padrão Azure CDN do** perfil da Microsoft requer um certificado de uma das autoridades de certificados aprovadas (CA) na lista seguinte. Se for utilizado um certificado de uma AC não aprovada ou se for utilizado um certificado auto-assinado, o pedido é rejeitado. **A Norma Azure CDN da Verizon** e **do Azure CDN Premium dos** perfis da Verizon aceitam qualquer certificado válido de qualquer CA válido.

> [!NOTE]
> A opção de utilizar o seu próprio certificado para ativar a funcionalidade HTTPS de domínio personalizado *não* está disponível para **o Azure CDN Standard a partir de** perfis Da Akamai. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

