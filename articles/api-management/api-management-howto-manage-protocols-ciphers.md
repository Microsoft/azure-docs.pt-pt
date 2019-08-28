---
title: Gerenciar protocolos e codificações no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como gerenciar protocolos (TLS, SSL) e codificações (DES) no gerenciamento de API do Azure.
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
ms.openlocfilehash: f7c7fdd06480ce3da70c86d38ab0685b9b3aaaf2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072395"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Gerenciar protocolos e codificações no gerenciamento de API do Azure

O gerenciamento de API do Azure dá suporte a várias versões do protocolo TLS para os lados de cliente e back-end, bem como a codificação 3DES.

Este guia mostra como gerenciar a configuração de protocolos e codificações para uma instância de gerenciamento de API do Azure.

![Gerenciar protocolos e codificações no APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas neste artigo, você deve ter:

* Uma instância de gerenciamento de API

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Como gerenciar protocolos TLS e a codificação 3DES

1. Navegue até sua **instância de gerenciamento de API** no portal do Azure.
2. Selecione **configurações de protocolo** no menu.  
3. Habilitar ou desabilitar os protocolos ou as codificações desejadas.
4. Clique em **Guardar**. As alterações serão aplicadas em uma hora.  

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [TLS (segurança da camada de transporte)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o gerenciamento de API.