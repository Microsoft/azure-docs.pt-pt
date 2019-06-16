---
title: Gerir protocolos e cifras na gestão de API do Azure | Documentos da Microsoft
description: Saiba como gerir protocolos (TLS, SSL) e cifras (DES) na API Management do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: f809aaf872607e7fa61368832a3df74318f2a858
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66141508"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Gerir protocolos e cifras na gestão de API do Azure

Gestão de API do Azure suporta várias versões do protocolo TLS para os lados de cliente e o back-end, bem como a cifra 3DES.

Este guia mostra-lhe como gerir protocolos e cifras configuração para uma instância de gestão de API do Azure.

![Gerir protocolos e cifras em APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste artigo, tem de ter:

* Uma instância de gestão de API

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Como gerir cifras TLS de protocolos e 3DES

1. Navegue até à sua **instância de gestão de API** no portal do Azure.
2. Selecione **definições de protocolo** no menu.  
3. Ativar ou desativar os protocolos pretendidos ou cifras.
4. Clique em **Guardar**. As alterações serão aplicadas dentro de uma hora.  

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a gestão de API.