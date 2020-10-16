---
title: Gerir protocolos e cifras na Azure API Management Microsoft Docs
description: Saiba como gerir protocolos (TLS) e cifras (DES) na Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 043a3d0b63dfc74f587b58b3c2ac42f1a084cc4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86250316"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Gerir protocolos e cifras na Gestão de API do Azure

A Azure API Management suporta várias versões do protocolo TLS tanto para os lados do cliente como para os lados de backend, bem como para a cifra 3DES.

Este guia mostra-lhe como gerir protocolos e configurações de cifras para uma instância de Gestão API da Azure.

![Gerir protocolos e cifras na APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, você deve ter:

* Um caso de Gestão da API

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Como gerir os protocolos TLS e a cifra 3DES

1. Navegue para a sua **instância de Gestão API** no portal Azure.
2. Selecione **as definições** de Protocolo do menu.  
3. Ativar ou desativar os protocolos ou cifras pretendidos.
4. Clique em **Guardar**. As alterações serão aplicadas dentro de uma hora.  

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o TLS (Transport Layer Security)](/dotnet/framework/network-programming/tls).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a API Management.
