---
title: Problemas de poupança de credenciais de administração configurando app de galeria Azure AD
description: Saiba como resolver problemas ao guardar credenciais de administrador enquanto configura o fornecimento do utilizador a uma aplicação da Azure Ative Directory Gallery.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/21/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 7a3340e72499087dce7773264272601dfce8a50f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91266787"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Credenciais de administrador de poupança de problemas ao mesmo tempo que configura o fornecimento do utilizador a uma aplicação da Galeria do Diretório Ativo Azure 

Ao utilizar o portal Azure para configurar o [provisionamento automático do utilizador](user-provisioning.md) para uma aplicação empresarial, poderá encontrar uma situação em que:

* As **credenciais de administração** inseridas para a aplicação são válidas e o botão **de Ligação** de Teste funciona. No entanto, as credenciais não podem ser guardadas e o portal Azure devolve uma mensagem de erro genérica.

Se o sign-on único baseado em SAML também estiver configurado para a mesma aplicação, a causa mais provável do erro é que o limite interno de armazenamento por aplicação da Azure AD para certificados e credenciais tenha sido ultrapassado.

A Azure AD tem atualmente uma capacidade máxima de armazenamento de 1024 bytes para todos os certificados, fichas secretas, credenciais e dados de configuração relacionados associados a uma única instância de uma aplicação (também conhecido como registo principal de serviço em Azure AD).

Quando o sign-on único baseado em SAML é configurado, o certificado usado para assinar os tokens SAML é armazenado aqui, e muitas vezes consome mais de 50% do espaço.

Quaisquer fichas secretas, URIs, endereços de e-mail de notificação, nomes de utilizador e palavras-passe que sejam introduzidos durante a configuração do fornecimento do utilizador podem fazer com que o limite de armazenamento seja ultrapassado.

## <a name="how-to-work-around-this-issue"></a>Como contornar esta questão 

Há duas formas possíveis de resolver esta questão hoje:

1. **Utilize duas instâncias de aplicação de galeria, uma para uma única inscrição e outra para provisionamento** de utilizadores - Tomando como exemplo a aplicação de galeria [LinkedIn Elevate,](../saas-apps/linkedinelevate-tutorial.md) pode adicionar LinkedIn Elevate da galeria e configurgá-la para um único sinal. Para o provisionamento, adicione outro exemplo de LinkedIn Elevate da galeria de aplicações AD Azure, e nomeie-o "LinkedIn Elevate (Provisioning)". Para este segundo caso, [configurar o provisionamento](../saas-apps/linkedinelevate-provisioning-tutorial.md), mas não uma única súblio. Ao utilizar esta solução alternativa, os mesmos utilizadores e grupos precisam de ser [atribuídos](../manage-apps/assign-user-or-group-access-portal.md) a ambas as aplicações. 

2. **Reduzir a quantidade de dados de configuração armazenados** - Todos os dados introduzidos na secção de [credenciais de administração](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) do separador de provisionamento são armazenados no mesmo local que o certificado SAML. Embora possa não ser possível reduzir o comprimento de todos estes dados, alguns campos de configuração opcional como o **Email de Notificação** podem ser removidos.

## <a name="next-steps"></a>Passos seguintes
[Configure o fornecimento e desaconses dos utilizadores às aplicações saaS](user-provisioning.md)
