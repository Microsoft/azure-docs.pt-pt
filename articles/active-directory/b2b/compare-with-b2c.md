---
title: Comparar colaboração B2B e B2C - Azure Active Directory | Documentos da Microsoft
description: Qual é a diferença entre a colaboração B2B do Azure Active Directory e o Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 01/30/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba06952b4d01e06d7925f70ee4bc26407b48e130
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670596"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Comparar a colaboração B2B e o B2C no Azure Active Directory

Quer a colaboração do Azure Active Directory (Azure AD) B2B como o Azure AD B2C permitem trabalhar com utilizadores externos no Microsoft Azure AD. Mas como são comparados?

O **Azure AD B2B** é para as empresas que desejam partilhar ficheiros e recursos com utilizadores externos em segurança para que possam colaborar. Um administrador do Azure configura a B2B no portal do Azure e o Microsoft Azure AD encarrega-se da federação entre a sua empresa e o seu parceiro externo. Os utilizadores iniciam sessão nos recursos partilhados através de um processo de convite e resgate simples com a conta escolar ou profissional ou qualquer conta de e-mail.
 
O **Azure AD B2C** é principalmente para as empresas e programadores que criam aplicações destinadas aos clientes. Com o Azure AD B2C, os programadores podem utilizar o Microsoft Azure AD como o sistema de identidade completo para a aplicação, ao permitir aos clientes iniciarem sessão com uma identidade já estabelecida (como o Facebook ou o Gmail).

A tabela abaixo apresenta uma comparação detalhada.


Capacidades da colaboração B2B |     Oferta autónoma do Azure AD B2C
-------- | --------
Se destina a: Organizações que pretendem conseguir autenticar os utilizadores de uma organização de parceiro, independentemente do fornecedor de identidade. | Se destina a: Convidar os clientes do seu dispositivo móvel e aplicações web, se pessoas, os clientes institucionais ou organizacionais para o Azure AD.
Suportada de identidades: Funcionários com o trabalho ou escola contas, parceiros com contas escolares ou profissionais ou qualquer endereço de e-mail. Em breve, irá suportar a federação direta.  | Suportada de identidades: Os utilizadores com contas de aplicação local (qualquer e-mail endereço ou nome de utilizador) ou qualquer suporte a identidade de redes sociais com a Federação direta.
Utilizadores externos são geridos no mesmo diretório que os funcionários, mas especialmente anotados. Podem ser geridos da mesma forma que os funcionários, eles podem ser adicionados aos mesmos grupos e assim por diante  | Utilizadores externos são geridos no diretório da aplicação. Que sejam geridos em separado do funcionário e diretório de parceiros (se houver) da organização.
É suportado o início de sessão único (SSO) para todas as aplicações ligadas ao Microsoft Azure AD. Por exemplo, pode conceder acesso ao Office 365 ou às aplicações no local e a outras aplicações SaaS, como o Salesforce ou Workday.  |  É suportado o SSO em aplicações pertencentes aos cliente nos inquilinos do Azure AD B2C. O SSO no Office 365 ou outras aplicações SaaS da Microsoft e que não são da Microsoft não são suportados.
Ciclo de vida de parceiro: Gerido pelo anfitrião/convidar organização.  | Ciclo de vida do cliente: Self-Service ou gerenciado pela aplicação.
Política de segurança e conformidade: Gerido pelo anfitrião/convidar organização (por exemplo, com [políticas de acesso condicional](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Política de segurança e conformidade: Geridos pela aplicação.
Imagem corporativa: Anfitrião/convidar marca da organização é utilizado.  |    Imagem corporativa: Gerido pelo aplicativo. Geralmente, tende a ser personalizada com base no produto, com a organização a desvanecer no fundo.
Obter mais informações: [Mensagem de blogue](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [documentação](what-is-b2b.md)  | Obter mais informações: [Página de produto](https://azure.microsoft.com/services/active-directory-b2c/), [documentação](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Passos Seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Propriedades do utilizador de colaboração B2B](user-properties.md)

