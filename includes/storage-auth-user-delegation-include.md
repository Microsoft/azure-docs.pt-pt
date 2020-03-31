---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76118073"
---
## <a name="about-the-user-delegation-sas"></a>Sobre a delegação de utilizadores SAS

Um símbolo SAS para acesso a um recipiente ou bolha pode ser protegido utilizando credenciais AD Azure ou uma chave de conta. Um SAS protegido com credenciais AD Azure é chamado de delegação de utilizadores SAS, porque o token OAuth 2.0 usado para assinar o SAS é solicitado em nome do utilizador.

A Microsoft recomenda que utilize credenciais de AD Azure sempre que possível como uma boa prática de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando o design da sua aplicação necessitar de assinaturas de acesso partilhado, utilize credenciais Azure AD para criar uma delegação de utilizadores SAS para uma segurança superior. Para obter mais informações sobre a delegação de utilizadores SAS, consulte [Criar uma delegação de utilizadores SAS](/rest/api/storageservices/create-user-delegation-sas).

> [!CAUTION]
> Qualquer cliente que possua um SAS válido pode aceder a dados na sua conta de armazenamento, conforme permitido por esse SAS. É importante proteger um SAS de uso malicioso ou não intencional. Use a discrição na distribuição de um SAS, e tenha um plano em vigor para revogar um SAS comprometido.

Para obter mais informações sobre assinaturas de acesso partilhado, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../articles/storage/common/storage-sas-overview.md).
