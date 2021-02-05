---
title: Ca permitido para ativar https personalizados
titleSuffix: Azure Content Delivery Network
description: Se estiver a utilizar o seu próprio certificado para ativar HTTPS num domínio personalizado, deve utilizar uma autoridade de certificados permitida (CA) para o criar.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573403"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>Autoridades de certificados autorizadas para permitir https personalizados

São necessários requisitos específicos de certificado quando [ativa a funcionalidade HTTPS utilizando o seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) para um domínio personalizado Azure CDN (Rede de Entrega de Conteúdos). 

* A **Norma Azure CDN do** perfil da Microsoft requer um certificado de uma das autoridades de certificados aprovadas (CA) na lista seguinte. Se for utilizado um certificado de uma AC não aprovada ou se for utilizado um certificado auto-assinado, o pedido é rejeitado. 

* **A azure CDN Standard da Verizon** e **Azure CDN Premium dos** perfis Verizon aceitam qualquer certificado válido de qualquer CA válido. Os perfis de Verizon não suportam certificados auto-assinados.

> [!NOTE]
> A opção de usar o seu próprio certificado para ativar a funcionalidade HTTPS de domínio personalizado *não* está disponível para **Azure CDN Standard a partir de** perfis Akamai. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

