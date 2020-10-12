---
title: Ver detalhes de autenticação do Azure Maps
description: Utilize o portal Azure para ver detalhes de autenticação do Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6ffcbf5a8c36958dd3ea74de4d826fe25a1139c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87126465"
---
Pode ver os detalhes da autenticação da conta Azure Maps no portal Azure. Ali, na sua conta, no menu **Definições,** selecione **Autenticação**.

![Detalhes da autenticação](../media/how-to-manage-authentication/how-to-view-auth.png)

Uma vez criada uma conta Azure Maps, o valor Azure Maps `x-ms-client-id` está presente na página de detalhes de autenticação do portal Azure. Este valor representa a conta que será utilizada para pedidos de API REST. Este valor deve ser armazenado na configuração da aplicação e recuperado antes de fazer pedidos http ao utilizar a autenticação AD AZure com o Azure Maps.
