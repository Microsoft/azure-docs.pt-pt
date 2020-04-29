---
title: Gerir protocolos e cifras na Azure API Management [ API Management] Microsoft Docs
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
ms.openlocfilehash: 7f87389016286c9f1b91abc77155be9e94005371
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335876"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Gerir protocolos e cifras na Gestão de API do Azure

A Azure API Management suporta várias versões do protocolo TLS para os lados do cliente e backend, bem como a cifra 3DES.

Este guia mostra-lhe como gerir protocolos e configurações de cifras para uma instância de Gestão API Azure.

![Gerir protocolos e cifras na APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, deve ter:

* Uma instância de Gestão API

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Como gerir os protocolos TLS e a cifra 3DES

1. Navegue para a sua **instância de Gestão API** no portal Azure.
2. Selecione **as definições** de Protocolo no menu.  
3. Ativar ou desativar os protocolos ou cifras pretendidos.
4. Clique em **Guardar**. As alterações serão aplicadas dentro de uma hora.  

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [tLS (Segurança](https://docs.microsoft.com/dotnet/framework/network-programming/tls)da Camada de Transporte) .
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a Gestão da API.