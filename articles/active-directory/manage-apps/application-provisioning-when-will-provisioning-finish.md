---
title: Aprovisionamento de utilizadores a uma aplicação da galeria do Azure AD está a demorar horas ou mais | Documentos da Microsoft
description: Como descobrir por que o aprovisionamento da sua aplicação poderá estar a demorar mais tempo do que o esperado
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
ms.openlocfilehash: 8622a7bbd913710a2173399048baab7067d2fae7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783803"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Aprovisionamento de utilizadores a uma aplicação da galeria do Azure AD está a demorar horas ou mais

Ao primeiro ativar aprovisionamento automático para um aplicativo, a sincronização inicial pode demorar entre 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e o número de utilizadores no âmbito de aprovisionamento. 

Sincronizações subsequentes após a sincronização inicial estar mais rápido, como o serviço de aprovisionamento armazena as marcas d'água que representam o estado dos dois sistemas após a sincronização inicial, melhorando o desempenho de sincronizações subsequentes.

## <a name="how-to-improve-provisioning-performance"></a>Como melhorar o desempenho de aprovisionamento

Se a sincronização inicial está a demorar mais do que algumas horas, há uma coisa que pode fazer para melhorar o desempenho:

-   **Filtros de âmbito do utilizador.** Filtros de âmbito permitem-lhe ajustar os dados que o serviço de aprovisionamento extrai do Azure AD ao filtrar os utilizadores com base nos valores de atributo específico. Para obter mais informações sobre filtros de âmbito, veja [aprovisionamento de aplicações baseadas em atributos com filtros de âmbito](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Passos Seguintes
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e o desaprovisionamento de utilizadores em Aplicações SaaS com o Azure Active Directory)

