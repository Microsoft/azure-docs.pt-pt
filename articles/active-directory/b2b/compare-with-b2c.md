---
title: Comparar a colaboração B2B e B2C-Azure Active Directory | Microsoft Docs
description: Qual é a diferença entre a colaboração B2B do Azure Active Directory e o Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 07/22/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e7f5aa324db869e30a8b2fe214416129baca8d9
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380743"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Comparar a colaboração B2B e o B2C no Azure Active Directory

Quer a colaboração do Azure Active Directory (Azure AD) B2B como o Azure AD B2C permitem trabalhar com utilizadores externos no Microsoft Azure AD. Mas como são comparados?

O **Azure AD B2B** é para as empresas que desejam partilhar ficheiros e recursos com utilizadores externos em segurança para que possam colaborar. Um administrador do Azure configura a B2B no portal do Azure e o Microsoft Azure AD encarrega-se da federação entre a sua empresa e o seu parceiro externo. Os utilizadores iniciam sessão nos recursos partilhados através de um processo de convite e resgate simples com a conta escolar ou profissional ou qualquer conta de e-mail.
 
O **Azure AD B2C** é principalmente para as empresas e programadores que criam aplicações destinadas aos clientes. Com o Azure AD B2C, os programadores podem utilizar o Microsoft Azure AD como o sistema de identidade completo para a aplicação, ao permitir aos clientes iniciarem sessão com uma identidade já estabelecida (como o Facebook ou o Gmail).

A tabela abaixo apresenta uma comparação detalhada.


Capacidades da colaboração B2B |     Oferta autónoma do Azure AD B2C
-------- | --------
Destinado a: Organizações que desejam ser capazes de autenticar usuários de uma organização parceira, independentemente do provedor de identidade. | Destinado a: Convidar clientes de seus aplicativos móveis e Web, sejam indivíduos, clientes institucionais ou organizacionais em seu Azure AD.
Identidades com suporte: Funcionários com contas corporativas ou de estudante, parceiros com contas corporativas ou de estudante ou qualquer endereço de email. Em breve, irá suportar a federação direta.  | Identidades com suporte: Usuários do consumidor com contas de aplicativos locais (qualquer nome de usuário ou endereço de email) ou qualquer identidade social com suporte com Federação direta.
Os usuários externos são gerenciados no mesmo diretório que os funcionários, mas anotados especialmente. Eles podem ser gerenciados da mesma maneira que os funcionários, podem ser adicionados aos mesmos grupos e assim por diante  | Os usuários externos são gerenciados no diretório do aplicativo. Eles são gerenciados separadamente do diretório de funcionários e parceiros da organização (se houver).
É suportado o início de sessão único (SSO) para todas as aplicações ligadas ao Microsoft Azure AD. Por exemplo, pode conceder acesso ao Office 365 ou às aplicações no local e a outras aplicações SaaS, como o Salesforce ou Workday.  |  É suportado o SSO em aplicações pertencentes aos cliente nos inquilinos do Azure AD B2C. Não há suporte para SSO para Office 365 ou outros aplicativos SaaS da Microsoft.
Ciclo de vida do parceiro: Gerenciado pela organização host/convidada.  | Ciclo de vida do cliente: Autoatendimento ou gerenciado pelo aplicativo.
Política de segurança e conformidade: Gerenciado pela organização host/convidada (por exemplo, com [políticas de acesso condicional](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Política de segurança e conformidade: Gerenciado pelo aplicativo.
Identidade visual A marca de host/convidada da organização é usada.  |    Identidade visual Gerenciado pelo aplicativo. Geralmente, tende a ser personalizada com base no produto, com a organização a desvanecer no fundo.
Mais informações: [Postagem no blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [documentação](what-is-b2b.md)  | Mais informações: [Página do produto](https://azure.microsoft.com/services/active-directory-b2c/), [documentação](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Passos Seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Propriedades do utilizador de colaboração B2B](user-properties.md)

