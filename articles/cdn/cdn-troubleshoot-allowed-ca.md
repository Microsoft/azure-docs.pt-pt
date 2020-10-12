---
title: Ca permitido para ativar HTTPS personalizado em Azure CDN
description: Se estiver a utilizar o seu próprio certificado para ativar HTTPS num domínio personalizado, deve utilizar uma autoridade de certificados permitida (CA) para o criar.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 29b6cb25e021e86ce6663b4db5c89217aaf70a37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84887408"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autoridades de certificados autorizadas para permitir https personalizados na Azure CDN

Tem de cumprir os requisitos específicos do certificado quando [ativar a funcionalidade HTTPS utilizando o seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) para um domínio personalizado da Rede de Entrega de Conteúdos Azure (CDN). A **Norma Azure CDN do** perfil da Microsoft requer um certificado de uma das autoridades de certificados aprovadas (CA) na lista seguinte. Se for utilizado um certificado de uma AC não aprovada ou se for utilizado um certificado auto-assinado, o pedido é rejeitado. **A azure CDN Standard da Verizon** e **Azure CDN Premium dos** perfis Verizon aceitam qualquer certificado válido de qualquer CA válido.

> [!NOTE]
> A opção de usar o seu próprio certificado para ativar a funcionalidade HTTPS de domínio personalizado *não* está disponível para **Azure CDN Standard a partir de** perfis Akamai. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

