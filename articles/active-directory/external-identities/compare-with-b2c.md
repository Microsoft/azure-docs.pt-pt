---
title: Comparar identidades externas - Azure Ative Directory / Microsoft Docs
description: As identidades externas da AD AD permitem que pessoas fora da sua organização acedam às suas apps e recursos usando a sua própria identidade. Compare soluções para identidades externas, incluindo a colaboração do Azure Ative Directory B2B e do Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf57e60fc05d579365d459e4a5d5288c2ca52bb0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442155"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>O que são identidades externas no Diretório Ativo Azure?

Com identidades externas em Azure AD, pode permitir que pessoas fora da sua organização acedam às suas apps e recursos, deixando-as assinar em qualquer identidade que prefiram. Os seus parceiros, distribuidores, fornecedores, fornecedores e outros utilizadores convidados podem "trazer as suas próprias identidades". Quer sejam parte do Azure AD ou de outro sistema gerido por TI, ou tenham uma identidade social não gerida como o Google ou o Facebook, podem usar as suas próprias credenciais para se inscreverem. O fornecedor de identidade gere a identidade do utilizador externo e gere o acesso às suas apps com Azure AD para manter os seus recursos protegidos. 

## <a name="external-identities-scenarios"></a>Cenários de identidades externas

As identidades externas do AD AZure focam-se menos na relação de um utilizador com a sua organização e mais na forma como um indivíduo quer iniciar seducação nas suas apps e recursos. Neste contexto, a Azure AD apoia uma variedade de cenários, desde a colaboração entre empresas e empresas (B2B) até ao desenvolvimento de aplicações para clientes e consumidores (empresa-a-consumidor, ou B2C).

- **Partilhar aplicativos com utilizadores externos (colaboração B2B)**. Convide os utilizadores externos para o seu próprio inquilino como utilizadores "convidados" para que possa atribuir permissões (para autorização) permitindo-lhes utilizar as suas credenciais existentes (para autenticação). Os utilizadores inscrevem-se nos recursos partilhados utilizando um simples processo de convite e resgate com a sua conta de trabalho, conta escolar ou qualquer conta de e-mail. E agora com a disponibilidade de fluxos de utilizador de inscrição self-service (Preview), também pode fornecer uma experiência de entrada para os seus utilizadores externos através da aplicação que pretende partilhar. Pode configurar as definições de fluxo do utilizador para controlar a forma como o utilizador se inscreve para a aplicação e que lhes permite utilizar a sua conta de trabalho, conta escolar ou qualquer identidade social (como o Google ou o Facebook) que queiram usar.  Para mais informações, consulte a [documentação Azure AD B2B](index.yml).

- **Desenvolver aplicativos destinados a outros inquilinos da AD Azure (inquilino único ou multi-inquilino)**. Ao desenvolver aplicações para Azure AD, você pode direcionar os utilizadores de uma única organização (inquilino único), ou utilizadores de qualquer organização que já tenha um inquilino AZURE AD (chamado aplicações multi-inquilinos). Estas aplicações multi-inquilinos são registadas uma vez por si no seu próprio AD Azure, mas podem ser usadas por qualquer utilizador AZure AD de qualquer organização sem qualquer trabalho adicional da sua parte.

- **Desenvolver aplicativos com rótulo branco para consumidores e clientes (Azure AD B2C)**. Se você é um negócio ou desenvolvedor que cria aplicações viradas para o cliente, você pode escalar para consumidores, clientes ou cidadãos usando um Azure AD B2C. Os desenvolvedores podem usar o Azure AD como o sistema de identidade completo para a sua aplicação, ao mesmo tempo que deixam os clientes iniciar súmpi com uma identidade que já estabeleceram (como o Facebook ou o Gmail). Com o Azure AD B2C, pode personalizar e controlar completamente a forma como os clientes se inscrevem, se inscrevem e gerem os seus perfis ao utilizarem as suas aplicações. Para mais informações, consulte a [documentação Azure AD B2C](../../active-directory-b2c/index.yml).

## <a name="compare-external-identities-solutions"></a>Comparar soluções de Identidades Externas

A tabela abaixo apresenta uma comparação detalhada dos vários cenários que pode permitir com identidades externas AD AZure.

| Aplicações multi-inquilino  | Colaboração externa do utilizador (B2B) | Aplicativos para consumidores ou clientes (B2C)  |
| ---- | --- | --- |
| Cenário primário: Enterprise Software-as-a-Service (SaaS) | Cenário primário: Colaboração utilizando aplicações da Microsoft (Microsoft 365, Teams, ...) ou o seu próprio software de colaboração.  | Cenário primário: Aplicações transacionais utilizando aplicações desenvolvidas personalizadas.   |
| Destina-se a: Organizações que pretendem fornecer software a muitos clientes empresariais.    | Destina-se a: organizações que pretendam conseguir autenticar os utilizadores de uma organização parceira, independentemente do fornecedor de identidade.    | Destina-se a: Convidar clientes das suas aplicações móveis e web, sejam indivíduos, clientes institucionais ou organizacionais para um diretório AD Azure separados do diretório da sua própria organização. |
| Identidades suportadas: Funcionários com contas AD Azure. | Identidades suportadas: os funcionários ou parceiros com contas escolares ou profissionais, ou quaisquer endereços de e-mail. Em breve, irá suportar a federação direta.      | Identidades suportadas: os utilizadores com contas de aplicações locais (qualquer endereço de e-mail ou nome de utilizador) ou qualquer identidade social suportada com federação direta.       |
| Os utilizadores externos são geridos no seu próprio diretório, isolados do diretório onde a aplicação foi registada.    | Os utilizadores externos são geridos no mesmo diretório que os colaboradores, mas anotados especialmente. Podem ser geridos da mesma forma que os empregados, podem ser adicionados aos mesmos grupos, e assim por diante.    | Os utilizadores externos são geridos no diretório de aplicações. São geridos separadamente do diretório de empregados e parceiros da organização (se houver).  |
| Um único sinal: SSO para todas as aplicações ligadas a Ad Azure é suportado.          | Um único sinal: SSO para todas as aplicações ligadas a Ad Azure é suportado. Por exemplo, pode fornecer acesso a aplicações microsoft 365 ou no local, e a outras aplicações SaaS como Salesforce ou Workday.    | SSO para aplicações de propriedade de clientes dentro dos inquilinos Azure AD B2C é suportado. SSO para o Microsoft 365 ou para outras aplicações microsoft SaaS não é suportado.    |
| Ciclo de vida do cliente: Gerido pela organização do utilizador.      | Ciclo de vida do parceiro: gerido pelo anfitrião/organização que convida.    | Ciclo de vida do cliente: gerido automaticamente ou gerido pela aplicação.      |
| Política de segurança e conformidade: Gerido pela organização de acolhimento/convite (por exemplo, com [políticas de Acesso Condicional).](./conditional-access.md)           | Política de segurança e conformidade: Gerido pela organização de acolhimento/convite (por exemplo, com [políticas de Acesso Condicional).](./conditional-access.md) | Política de segurança e conformidade: geridas pela aplicação.        |
| Branding: É utilizada a marca de organização de anfitriões/convidativos.   | Branding: É utilizada a marca de organização de anfitriões/convidativos.    | Imagem corporativa: gerida pela aplicação. Geralmente, tende a ser personalizada com base no produto, com a organização a desvanecer no fundo.   |
| Mais [informações: Gerir identidade em aplicações multi-inquilinos,](/azure/architecture/multitenant-identity/) [Como-a-Guia](../develop/howto-convert-app-to-be-multi-tenant.md) | Mais informações: [Mensagem de blogue](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentação](what-is-b2b.md)                   | Mais informações: [Página do produto](https://azure.microsoft.com/services/active-directory-b2c/), [Documentação](../../active-directory-b2c/index.yml)       |

Proteja e gere clientes e parceiros para além dos seus limites organizacionais com identidades externas Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Acerca do Azure AD B2C](../../active-directory-b2c/overview.md)