---
title: Problema ao salvar credenciais de administrador Configurando o aplicativo da galeria do Azure AD | Microsoft Docs
description: Como solucionar problemas comuns enfrentados ao configurar o provisionamento de usuário para um aplicativo já listado na Galeria de aplicativos do Azure AD
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
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 152b704ca597fb473a820124ee6147d6d9bc7845
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381474"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problema ao salvar as credenciais de administrador ao configurar o provisionamento de usuário para um aplicativo da Galeria de Azure Active Directory 

Ao usar o portal do Azure para configurar o [provisionamento automático de usuário](user-provisioning.md) para um aplicativo empresarial, você pode encontrar uma situação em que:

* As **credenciais de administrador** inseridas para o aplicativo são válidas e o botão **testar conexão** funciona. No entanto, as credenciais não podem ser salvas e o portal do Azure retorna uma mensagem de erro genérica.

Se o logon único baseado em SAML também estiver configurado para o mesmo aplicativo, a causa mais provável do erro é que o limite de armazenamento interno por aplicativo do Azure AD para certificados e credenciais foi excedido.

Atualmente, o Azure AD tem uma capacidade de armazenamento máxima de 1024 bytes para todos os certificados, tokens secretos, credenciais e dados de configuração relacionados associados a uma única instância de um aplicativo (também conhecido como registro de entidade de serviço no Azure AD).

Quando o logon único baseado em SAML é configurado, o certificado usado para assinar os tokens SAML é armazenado aqui e geralmente consome mais de 50%% do espaço.

Quaisquer tokens secretos, URIs, endereços de email de notificação, nomes de usuário e senhas que são inseridos durante a instalação do provisionamento do usuário podem fazer com que o limite de armazenamento seja excedido.

## <a name="how-to-work-around-this-issue"></a>Como contornar esse problema 

Há duas maneiras possíveis de solucionar esse problema hoje:

1. **Use duas instâncias de aplicativo da galeria, uma para logon único e outra para provisionamento de usuário** – assumindo o aplicativo da Galeria [LinkedIn Elevate](../saas-apps/linkedinelevate-tutorial.md) como um exemplo, você pode adicionar o LinkedIn Elevate da galeria e configurá-lo para logon único. Para o provisionamento, adicione outra instância do LinkedIn Elevate da Galeria de aplicativos do Azure AD e nomeie-o "LinkedIn Elevate (provisionamento)". Para esta segunda instância, configure o [provisionamento](../saas-apps/linkedinelevate-provisioning-tutorial.md), mas não o logon único. Ao usar essa solução alternativa, os mesmos usuários e grupos precisam ser [atribuídos](assign-user-or-group-access-portal.md) a ambos os aplicativos. 

2. **Reduzir a quantidade de dados de configuração armazenados** – todos os dados inseridos na seção [credenciais de administrador](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) da guia provisionamento são armazenados no mesmo local que o certificado SAML. Embora talvez não seja possível reduzir o tamanho de todos esses dados, alguns campos de configuração opcionais, como o **email de notificação** , podem ser removidos.

## <a name="next-steps"></a>Passos seguintes
[Configurar o provisionamento e desprovisionamento de usuários para aplicativos SaaS](user-provisioning.md)
