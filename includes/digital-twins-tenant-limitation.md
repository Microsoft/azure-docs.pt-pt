---
author: baanders
description: incluir arquivo descrevendo a limitação do inquilino cruzado com Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589390"
---
Como resultado, os pedidos às APIs das Gémeas Digitais Azure requerem um utilizador ou diretor de serviço que faça parte do mesmo inquilino onde reside a instância Azure Digital Twins. Para evitar a digitalização maliciosa dos pontos finais da Azure Digital Twins, os pedidos com fichas de acesso de fora do inquilino originado serão devolvidos uma mensagem de erro "404 Sub-Domain não encontrada". Este erro será devolvido *mesmo que* o utilizador ou o diretor de serviço tenha recebido um [papel](../articles/digital-twins/concepts-security.md) de Azure Digital Twins Data Owner ou Azure Digital Twins Data Reader através da colaboração [Azure AD B2B.](../articles/active-directory/external-identities/what-is-b2b.md) 