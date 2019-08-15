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
ms.openlocfilehash: 2ea936ba489024648583cf22c5ab73ae06bb1537
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011976"
---
## <a name="about-the-user-delegation-sas-preview"></a>Sobre a SAS de delegação de usuário (visualização)

Um token SAS para acesso a um contêiner ou blob pode ser protegido usando as credenciais do Azure AD ou uma chave de conta. Uma SAS protegida com as credenciais do Azure AD é chamada de SAS de delegação de usuário, pois o token 2,0 do OAuth usado para assinar a SAS é solicitado em nome do usuário.

A Microsoft recomenda que você use as credenciais do Azure AD quando possível como uma prática recomendada de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando seu design de aplicativo requer assinaturas de acesso compartilhado, use as credenciais do Azure AD para criar uma SAS de delegação de usuário para segurança superior. Para obter mais informações sobre a SAS de delegação de usuário, consulte [criar uma SAS de delegação de usuário](/rest/api/storageservices/create-a-user-delegation-sas).

> [!NOTE]
> A versão prévia da SAS de delegação de usuário destina-se somente ao uso de não produção.

> [!CAUTION]
> Qualquer cliente que possua uma SAS válida pode acessar dados em sua conta de armazenamento, conforme permitido por essa SAS. É importante proteger uma SAS contra uso mal-intencionado ou indesejado. Use o critério de distribuição de uma SAS e tenha um plano em vigor para revogar uma SAS comprometida.

Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../articles/storage/common/storage-sas-overview.md).
