---
title: O provisionamento de usuário para um aplicativo da galeria do Azure AD está levando horas ou mais | Microsoft Docs
description: Como descobrir por que o provisionamento para seu aplicativo pode estar demorando mais do que o esperado
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca2257a46815dd5e685b9cc746a64cede510d10a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034171"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>O provisionamento de usuário para um aplicativo da galeria do Azure AD está demorando horas ou mais

Ao habilitar o provisionamento automático para um aplicativo pela primeira vez, o ciclo inicial pode levar de 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento. 

As sincronizações subsequentes após o ciclo inicial serão mais rápidas, pois o serviço de provisionamento armazena as marcas d' água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes.

## <a name="how-to-improve-provisioning-performance"></a>Como melhorar o desempenho de provisionamento

Se o ciclo inicial estiver demorando mais do que algumas horas, há uma coisa que você pode fazer para melhorar o desempenho:

-   **Filtros de escopo de usuário.** Os filtros de escopo permitem ajustar os dados que o serviço de provisionamento extrai do Azure AD filtrando os usuários com base em valores de atributo específicos. Para obter mais informações sobre filtros de escopo, consulte [provisionamento de aplicativo baseado em atributo com filtros de escopo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Passos Seguintes
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e o desaprovisionamento de utilizadores em Aplicações SaaS com o Azure Active Directory)

