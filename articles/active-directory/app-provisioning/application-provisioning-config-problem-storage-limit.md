---
title: Credenciais de poupança de problemas configuram app de galeria Azure AD
description: Como resolver problemas comuns ao configurar o fornecimento de utilizadores a uma aplicação já listada na Galeria de Aplicações Da AD do Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ff80224037001e41daf49cd6fc21439b2cc5cff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522871"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Credenciais de administrador de poupança de problemas ao configurar o fornecimento de utilizadores a uma aplicação da Galeria de Diretórios Ativos Do Azure 

Ao utilizar o portal Azure para configurar [o fornecimento automático de utilizadores](user-provisioning.md) para uma aplicação empresarial, poderá encontrar uma situação em que:

* As **credenciais de administrador** introduzidas para a aplicação são válidas e o botão de **ligação** de teste funciona. No entanto, as credenciais não podem ser guardadas e o portal Azure devolve uma mensagem de erro genérica.

Se o único sign-on baseado em SAML também estiver configurado para a mesma aplicação, a causa mais provável do erro é que o limite interno de armazenamento por aplicação da Azure AD para certificados e credenciais foi ultrapassado.

A Azure AD tem atualmente uma capacidade máxima de armazenamento de 1024 bytes para todos os certificados, fichas secretas, credenciais e dados de configuração relacionados associados a uma única instância de uma aplicação (também conhecida como registo principal de serviço em Azure AD).

Quando o sinal único baseado em SAML é configurado, o certificado usado para assinar as fichas SAML é armazenado aqui, e muitas vezes consome mais de 50% do espaço.

Quaisquer tokens secretos, URIs, endereços de e-mail de notificação, nomes de utilizador e senhas que sejam introduzidos durante a configuração do fornecimento do utilizador podem fazer com que o limite de armazenamento seja ultrapassado.

## <a name="how-to-work-around-this-issue"></a>Como contornar esta questão 

Existem duas formas possíveis de resolver esta questão hoje:

1. **Utilize duas instâncias** de aplicação de galeria, uma para um único sinal e outra para o fornecimento de utilizadores - Tomando como exemplo a aplicação da galeria [LinkedIn Elevate,](../saas-apps/linkedinelevate-tutorial.md) pode adicionar linkedIn Elevate da galeria e configurá-lo para um único sinal. Para o provisionamento, adicione outra instância de LinkedIn Elevate da galeria de aplicações Azure AD, e nomeie-a "LinkedIn Elevate (Provisioning)." Para este segundo caso, configurar [o fornecimento,](../saas-apps/linkedinelevate-provisioning-tutorial.md)mas não um único sinal. Ao utilizar esta seleção, os mesmos utilizadores e grupos precisam de ser [atribuídos](../manage-apps/assign-user-or-group-access-portal.md) a ambas as aplicações. 

2. **Reduza a quantidade de dados de configuração armazenados** - Todos os dados introduzidos na secção de [credenciais do Administrador](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) do separador de fornecimento são armazenados no mesmo local que o certificado SAML. Embora não seja possível reduzir o comprimento de todos estes dados, alguns campos de configuração opcionais como o Email de **Notificação** podem ser removidos.

## <a name="next-steps"></a>Passos seguintes
[Configure o fornecimento e o desprovisionamento dos utilizadores às aplicações SaaS](user-provisioning.md)
