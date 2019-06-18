---
title: Saiba de que quando um utilizador específico poderá aceder a uma aplicação | Documentos da Microsoft
description: Como saber quando um usuário extremamente importante ser capaz de aceder a uma aplicação que configurou para o aprovisionamento de utilizadores com o Azure AD
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
ms.openlocfilehash: 08a78a311771395f7c2113f03d5137d59999d7a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783875"
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Saber quando um utilizador específico poderá aceder a uma aplicação
Ao utilizar o aprovisionamento automático de utilizadores com uma aplicação, do Azure AD automaticamente contas de utilizador de aprovisionamento e a atualização num aplicativo com base em coisas como [atribuição de utilizador e grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) regularmente agendada no tempo de intervalo, normalmente cada dez minutos.

## <a name="how-long-does-it-take"></a>Quanto tempo demora?

O tempo necessário para um determinado usuário a ser aprovisionado depende principalmente se já tiver ocorrido uma sincronização inicial de "completa".

A primeira sincronização entre o Azure AD e uma aplicação pode demorar entre 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e o número de utilizadores no âmbito de aprovisionamento. 

Sincronizações subsequentes após a sincronização inicial ser mais rápida (por exemplo, dentro de 10 minutos), como o serviço de aprovisionamento armazena as marcas d'água que representam o estado dos dois sistemas após a sincronização inicial, melhorando o desempenho de sincronizações subsequentes.

## <a name="how-to-check-the-status-of-a-user"></a>Como verificar o estado de um utilizador

Para ver o estado de aprovisionamento de um utilizador selecionado, consulte os registos de auditoria no Azure AD.

Os registos de auditoria de aprovisionamento podem ser acedidos no portal do Azure, no **do Azure Active Directory &gt; aplicações empresariais &gt; \[nome da aplicação\] &gt; registos de auditoria** separador. Filtrar os registos a **aprovisionamento da conta** categoria para ver apenas os eventos de aprovisionamento para essa aplicação. Pode procurar utilizadores com base no "ID correspondente" que foi configurado para os mesmos nos mapeamentos de atributos. 

Por exemplo, se tiver configurado o "nome principal de utilizador" ou "endereço de e-mail" como o atributo correspondente no lado do Azure AD e o utilizador está a ser aprovisionamento não tem um valor de "audrey@contoso.com", em seguida, procurar os registos de auditoria para "audrey@contoso.com" e, em seguida, reveja as entradas devolvido.

Os registos de auditoria aprovisionamento registram todas as operações executadas pelo serviço de aprovisionamento, incluindo:

* Consultar o Azure AD para os utilizadores atribuídos, que se encontrem no âmbito de aprovisionamento
* Consultar a aplicação de destino para a existência desses utilizadores
* Comparar os objetos de utilizador entre o sistema
* Adicionar, atualizar ou desabilitar a conta de utilizador no sistema de destino com base na comparação

## <a name="next-steps"></a>Passos Seguintes
[Automatizar o aprovisionamento e desaprovisionamento para aplicações SaaS com o Azure Active Directory de utilizador](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)'
