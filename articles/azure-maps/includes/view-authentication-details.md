---
title: Ver detalhes de autenticação do Azure Maps
description: Utilize o portal Azure para ver detalhes de autenticação do Azure Maps.
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9146844a6b83f78ad99ef7cd1aec4b028daf3ff6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988616"
---
Pode ver os detalhes da autenticação da conta Azure Maps no portal Azure. Ali, na sua conta, no menu **Definições,** selecione **Autenticação**.

![Detalhes da autenticação](../media/how-to-manage-authentication/how-to-view-auth.png)

Uma vez criada uma conta Azure Maps, o valor Azure Maps `x-ms-client-id` está presente na página de detalhes de autenticação do portal Azure. Este valor representa a conta que será utilizada para pedidos de API REST. Este valor deve ser armazenado na configuração da aplicação e recuperado antes de fazer pedidos http ao utilizar a autenticação AD AZure com o Azure Maps.
