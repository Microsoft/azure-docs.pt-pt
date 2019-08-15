---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3af8077627d56ce44c5e2959e2c79b967b09d9e5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011947"
---
Uma SAS (assinatura de acesso compartilhado) permite que você conceda acesso limitado a contêineres e blobs em sua conta de armazenamento. Ao criar uma SAS, você especifica suas restrições, incluindo quais recursos de armazenamento do Azure um cliente tem permissão para acessar, quais permissões eles têm nesses recursos e por quanto tempo a SAS é válida.

Cada SAS é assinada com uma chave. Você pode assinar uma SAS de uma das duas maneiras:

- Com uma chave criada usando as credenciais do Azure Active Directory (AD do Azure). Uma SAS assinada com as credenciais do Azure AD é uma SAS de *delegação de usuário* .
- Com a chave da conta de armazenamento. Uma *SAS de serviço* e uma *SAS de conta* são assinadas com a chave da conta de armazenamento.
