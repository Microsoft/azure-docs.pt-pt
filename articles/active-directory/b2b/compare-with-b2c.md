---
title: Comparar identidades externas - Diretório Ativo Azure [ Azure Ative Diretório] Microsoft Docs
description: As identidades externas da AD Azure permitem que pessoas fora da sua organização acedam às suas apps e recursos usando a sua própria identidade. Compare soluções para identidades externas, incluindo a colaboração Azure Ative Directory B2B e azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9006a70ae941abb700412a7c596627939c994028
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587515"
---
# <a name="compare-solutions-for-external-identities-in-azure-active-directory"></a>Comparar soluções para identidades externas no Diretório Ativo do Azure

Com identidades externas em Anúncio de Azure, pode permitir que pessoas fora da sua organização acedam às suas apps e recursos, deixando-as assinar em qualquer identidade que prefiram. Os seus parceiros, distribuidores, fornecedores, fornecedores e outros utilizadores convidados podem "trazer as suas próprias identidades". Quer façam parte do Azure AD ou de outro sistema gerido por TI, ou tenham uma identidade social não gerida como o Google ou o Facebook, podem usar as suas próprias credenciais para iniciar sessão. O fornecedor de identidade gere a identidade do utilizador externo e gere o acesso às suas aplicações com a AD Azure para manter os seus recursos protegidos. 

## <a name="external-identities-scenarios"></a>Cenários de identidades externas

As Identidades Externas da AD Azure focam-se menos na relação de um utilizador com a sua organização e mais na forma como um indivíduo quer iniciar sessão nas suas apps e recursos. Neste contexto, a Azure AD apoia uma variedade de cenários desde a colaboração entre empresas (B2B) e o desenvolvimento de aplicações para clientes e consumidores (negócio-a-consumidor, ou B2C).

- **Partilhar aplicações com utilizadores externos (colaboração B2B)**. Convide os utilizadores externos para o seu próprio inquilino como utilizadores "convidados" que pode atribuir permissões (para autorização) permitindo-lhes usar as suas credenciais existentes (para autenticação). Os utilizadores assinam os recursos partilhados utilizando um simples processo de convite e resgate com a sua conta de trabalho, conta escolar ou qualquer conta de e-mail. E agora com a disponibilidade de fluxos de utilizador de inscrição self-service (Pré-visualização), também pode fornecer uma experiência de entrada para os seus utilizadores externos através da aplicação que pretende partilhar. Pode configurar as definições de fluxo do utilizador para controlar a forma como o utilizador se inscreve na aplicação e que lhes permite utilizar a sua conta de trabalho, conta escolar ou qualquer identidade social (como google ou Facebook) que queiram utilizar.  Para mais informações, consulte a [documentação Azure AD B2B](index.yml).

- **Desenvolver aplicações destinadas a outros inquilinos da AD Azure (inquilino único ou multi-inquilino)**. Ao desenvolver aplicações para a AD Azure, pode direcionar utilizadores de uma única organização (inquilino único), ou utilizadores de qualquer organização que já tenha um inquilino Azure AD (chamadas aplicações multi-arrendatárias). Estas aplicações multi-arrendatárias são registadas uma vez por si mesmono seu próprio Azure AD, mas podem ser usadas por qualquer utilizador da Azure AD de qualquer organização sem qualquer trabalho adicional da sua parte.

- **Desenvolver aplicativos com rótulo branco para consumidores e clientes (Azure AD B2C)**. Se você é um negócio ou desenvolvedor criando aplicações viradas para o cliente, você pode escalar para consumidores, clientes ou cidadãos usando um Azure AD B2C. Os desenvolvedores podem usar o Azure AD como o sistema de identidade completo para a sua aplicação, ao mesmo tempo que permite que os clientes inscrevam-se com uma identidade que já estabeleceram (como facebook ou Gmail). Com o Azure AD B2C, pode personalizar e controlar completamente a forma como os clientes se inscrevem, fazem o insessão e gerem os seus perfis ao utilizarem as suas aplicações. Para mais informações, consulte a [documentação Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/).

A tabela abaixo apresenta uma comparação detalhada dos vários cenários que pode permitir com identidades externas Azure AD.

| Aplicações multi-inquilino  | Colaboração externa do utilizador (B2B) | Aplicativos para consumidores ou clientes (B2C)  |
| ---- | --- | --- |
| Cenário primário: Software empresarial-as-a-Service (SaaS) | Cenário primário: Colaboração utilizando aplicações da Microsoft (Office 365, Teams, ...) ou o seu próprio software de colaboração.  | Cenário primário: Aplicações transacionais utilizando aplicações personalizadas desenvolvidas.   |
| Destinado a: Organizações que querem fornecer software a muitos clientes empresariais.    | Destina-se a: organizações que pretendam conseguir autenticar os utilizadores de uma organização parceira, independentemente do fornecedor de identidade.    | Destinado a: Convidar clientes das suas aplicações móveis e web, sejam indivíduos, clientes institucionais ou organizacionais num diretório Azure AD separado do diretório da sua própria organização. |
| Identidades suportadas: Colaboradores com contas Azure AD. | Identidades suportadas: os funcionários ou parceiros com contas escolares ou profissionais, ou quaisquer endereços de e-mail. Em breve, irá suportar a federação direta.      | Identidades suportadas: os utilizadores com contas de aplicações locais (qualquer endereço de e-mail ou nome de utilizador) ou qualquer identidade social suportada com federação direta.       |
| Os utilizadores externos são geridos no seu próprio diretório, isolados do diretório onde a aplicação foi registada.    | Os utilizadores externos são geridos no mesmo diretório que os colaboradores, mas anotados especialmente. Podem ser geridos da mesma forma que os empregados, podem ser adicionados aos mesmos grupos, e assim por diante.    | Os utilizadores externos são geridos no diretório da aplicação. São geridos separadamente do funcionário da organização e do diretório parceiro (se houver).  |
| Um único sinal: SSO para todas as aplicações ligadas a Anúncio sintetizadas com AD Azure é suportado.          | Um único sinal: SSO para todas as aplicações ligadas a Anúncio sintetizadas com AD Azure é suportado. Por exemplo, pode conceder acesso ao Office 365 ou às aplicações no local e a outras aplicações SaaS, como o Salesforce ou Workday.    | Inscrição única: SSO para aplicações pertencentes a clientes dentro dos inquilinos Azure AD B2C é suportado. O SSO para o Office 365 ou para outras aplicações da Microsoft SaaS não é suportado.    |
| Ciclo de vida do cliente: Gerido pela organização doméstica do utilizador.      | Ciclo de vida do parceiro: gerido pelo anfitrião/organização que convida.    | Ciclo de vida do cliente: gerido automaticamente ou gerido pela aplicação.      |
| Política de segurança e conformidade: Gerida pela organização anfitriã/convidativa (por exemplo, com políticas de [acesso condicional).](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)           | Política de segurança e conformidade: Gerida pela organização anfitriã/convidativa (por exemplo, com políticas de [acesso condicional).](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access) | Política de segurança e conformidade: geridas pela aplicação.        |
| Branding: A marca de anfitrião/organização convidativa é usada.   | Branding: A marca de anfitrião/organização convidativa é usada.    | Imagem corporativa: gerida pela aplicação. Geralmente, tende a ser personalizada com base no produto, com a organização a desvanecer no fundo.   |
| Mais informações: [Gerir a identidade em aplicações multi-arrendatárias](https://docs.microsoft.com/azure/architecture/multitenant-identity/), Como [guiar](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | Mais informações: [Mensagem de blogue](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentação](what-is-b2b.md)                   | Mais informações: [Página do produto](https://azure.microsoft.com/services/active-directory-b2c/), [Documentação](https://docs.microsoft.com/azure/active-directory-b2c/)       |

Proteja e gerencie clientes e parceiros para além dos seus limites organizacionais com identidades externas da Azure AD.

### <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Acerca do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview)
