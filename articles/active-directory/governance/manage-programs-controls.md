---
title: Gerir programas e controlos de revisões de acesso - Azure Active Directory | Documentos da Microsoft
description: Aprenda a criar programas adicionais para cada governação, gestão de riscos e iniciativa de conformidade na sua organização para recolher e organizar as revisões de acesso do Azure Active Directory como controles.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43c0f1c041bfed1b041a9926efd869d167c6f1e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60246035"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>Gerir programas e as revisões de acesso de controles para o Azure AD

Azure Active Directory (Azure AD) inclui as revisões de acesso de membros do grupo e o acesso à aplicação. Estes exemplos de controles Certifique-se de supervisão para quem tem acesso a aplicações e associações a grupos da sua organização. As organizações podem utilizar estes controlos para abordar com eficiência seus governação, gestão de riscos e os requisitos de conformidade.

## <a name="create-and-manage-programs-and-their-controls"></a>Criar e gerir programas e respetivos controlos
Pode simplificar a forma de controlar e recolher as revisões de acesso para diferentes fins, organizando-os em programas. Cada revisão de acesso pode ser associado a um programa. Em seguida, quando preparar relatórios para um auditor, se possa focar nas revisões de acesso no âmbito de uma iniciativa específica.  Programas e os resultados de revisão de acesso são visíveis para os utilizadores o Administrador Global, o administrador de utilizadores, o administrador de segurança ou a função de leitor de segurança.

Para ver uma lista de programas, vá para o [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) e selecione **programas**.

**Programa predefinido** sempre esteja presente. Se fizer parte de um administrador global ou função de administrador do utilizador, pode criar programas adicionais. Por exemplo, pode optar por ter um programa para cada iniciativa de conformidade ou de suas metas de negócios.

Se já não necessita de um programa e ele não tem quaisquer controles ligados ao mesmo, pode eliminá-la.

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [Obter resultados de revisão de acesso para grupos ou aplicações](retrieve-access-review.md)
