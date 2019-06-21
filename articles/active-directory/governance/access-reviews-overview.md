---
title: O que são as revisões de acesso? -O azure Active Directory | Documentos da Microsoft
description: Utilizar as revisões de acesso do Azure Active Directory, pode controlar o acesso de associação e a aplicação de grupo para atender a governação, gestão de riscos e iniciativas de conformidade na sua organização.
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
ms.date: 06/05/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fcc804db66430598e72e9ebf31a8837eda1cca6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67204598"
---
# <a name="what-are-azure-ad-access-reviews"></a>Revisões de quais são de acesso do Azure AD?

As revisões de acesso do Azure Active Directory (Azure AD) permitem às organizações com eficiência gerir membros do grupo, acesso a aplicações empresariais e atribuições de funções. Acesso de utilizador pode ser revisto em intervalos regulares para se certificar de que apenas as pessoas certas tenham acesso contínuo.

Aqui está um vídeo que fornece uma visão geral de revisões de acesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Por que são o acesso a revisões importante?

O Azure AD permite-lhe colaborar internamente dentro da sua organização e com os utilizadores de organizações externas, tais como parceiros. Os utilizadores podem associar grupos, convidar convidados, ligar a aplicações na cloud e trabalhem remotamente a partir dos seus dispositivos pessoais ou profissional. A conveniência de tirar partido do poder de Self-Service tem levado para uma necessidade de melhores recursos de gerenciamento de acesso.

- Como participar de novos funcionários, como pode garantir que tem o acesso adequado para ser produtivo?
- À medida que as pessoas mover as equipes ou deixam a empresa, como pode garantir que o acesso antigo é removido, especialmente quando ela envolve convidados?
- Direitos de acesso excessivo podem levar a auditoria descobertas e comprometimentos, conforme indicam uma falta de controlo sobre o acesso.
- Precisa envolva de forma proativa com os proprietários de recursos, para garantir que eles analisam regularmente quem tem acesso aos respetivos recursos.

## <a name="when-to-use-access-reviews"></a>Quando utilizar o acesso a revisões?

- **Demasiados utilizadores nas funções privilegiadas:** Ele é uma boa idéia para verificar quantos utilizadores têm acesso administrativo, quantos deles não estão os administradores globais, e se houver qualquer convidado convidados ou parceiros que não foram removidos depois de serem atribuídas a fazer uma tarefa administrativa. Pode voltar a certificar os utilizadores de atribuição de função no [funções do Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) por exemplo, os administradores globais, ou [funções de recursos do Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) como administrador de acesso de utilizador no [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) experiência.
- **Quando a automação é inviável:** Pode criar regras de associação de grupo dinâmica em grupos de segurança ou grupos do Office 365, mas e se os dados de RH não estiver no Azure AD ou se os usuários ainda precisam de acesso após a deixar o grupo para preparar a sua substituição? Em seguida, pode criar uma revisão nesse grupo para garantir que aqueles que ainda precisam de acesso devem ter acesso contínuo.
- **Quando um grupo é utilizado para uma nova finalidade:** Se tiver um grupo que vai ser sincronizado com o Azure AD, ou se pretender ativar a aplicação Salesforce para todos os utilizadores no grupo de equipa de vendas, seria útil fazer o proprietário do grupo para rever a associação de grupo antes do grupo que está a ser utilizado num co de risco diferentes nteúdos.
- **Acesso de dados críticos de negócio:** para determinados recursos, poderá ser necessário para pedir às pessoas fora do departamento de TI regularmente termine a sessão e dar uma justificativa sobre por que eles precisarem de acesso para fins de auditoria.
- **Para manter a lista de exceções de uma política:** Num mundo ideal, todos os utilizadores siga as políticas de acesso para proteger o acesso aos recursos da sua organização. No entanto, às vezes, existem casos comerciais que exigem que crie exceções. O administrador de TI, pode gerir esta tarefa, evitar supervisão de exceções da política e fornecer auditores prova que essas exceções são revisadas regularmente.
- **Faça os proprietários do grupo para confirmar que ainda precisam convidados nos respetivos grupos:** Acesso do funcionário pode ser automatizado com alguns no local IAM, mas não convidados convidados. Se um grupo fornece acesso de convidados para conteúdo confidencial da empresa, em seguida, é responsabilidade do proprietário do grupo para confirmar se os convidados ainda terá uma necessidade legítima de negócios para acesso.
- **Repetir periodicamente as revisões de ter:** Pode configurar recorrentes revisões de acesso dos utilizadores em frequências de conjunto como semanalmente, mensalmente, trimestralmente ou anualmente, e serão notificados os revisores no início de cada revisão. Os revisores podem aprovar ou negar o acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

## <a name="where-do-you-create-reviews"></a>Onde criar revisões?

Consoante o que pretende rever, criará sua revisão de acesso no Azure AD aceder revisões, aplicações empresariais do Azure AD (em pré-visualização) ou Azure AD PIM.

| Direitos de acesso de utilizadores | Os revisores podem ser | Revisão criado em | Experiência do revisor |
| --- | --- | --- | --- |
| Membros do grupo de segurança</br>Membros de grupo do Office | Revisores especificados</br>Proprietários do grupo</br>Self-rever | Revisões de acesso do Azure AD</br>Grupos do Azure AD | Painel de acesso |
| Atribuído a uma aplicação ligada | Revisores especificados</br>Self-rever | Revisões de acesso do Azure AD</br>Aplicações de enterprise do Azure AD (em pré-visualização) | Painel de acesso |
| Função do Azure AD | Revisores especificados</br>Self-rever | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |
| Função de recursos do Azure | Revisores especificados</br>Self-rever | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |

## <a name="which-users-must-have-licenses"></a>Os utilizadores que tem de ter licenças?

Cada utilizador que interage com as revisões de acesso tem de ter uma licença paga do Azure AD Premium P2. Os exemplos incluem:

- Administradores que criar uma revisão de acesso
- Reveja os proprietários do grupo que executam um acesso
- Utilizadores como revisores
- Utilizadores que realizam a rever personalizada

Também pode fazer os utilizadores convidados para rever o seu próprio acesso. Para cada licença paga do Azure AD Premium P2 que são atribuídas a um dos utilizadores da sua organização, pode utilizar o Azure AD empresa-empresa (B2B) para convidar até cinco utilizadores sob a permissão de utilizador externo. Estes utilizadores convidados também podem utilizar as funcionalidades do Azure AD Premium P2. Para obter mais informações, consulte [colaboração B2B do Azure AD, orientação de licenciamento](../b2b/licensing-guidance.md).

Seguem-se alguns cenários de exemplo para ajudar a determinar o número de licenças que tem de ter.

| Cenário | Cálculo | Número necessário de licenças |
| --- | --- | --- |
| Um administrador cria uma revisão de acesso de um grupo com 500 utilizadores.<br/>Atribui 3 proprietários do grupo como revisores. | 1 administrador + 3 proprietários do grupo | 4 |
| Um administrador cria uma revisão de acesso de um grupo com 500 utilizadores.<br/>Torna um Self-rever. | 1 administrador + 500 utilizadores como revisores por conta própria | 501 |
| Um administrador cria uma revisão de acesso de grupo A, com 5 utilizadores e de 25 utilizadores convidados.<br/>Torna um Self-rever. | 1 administrador + 5 utilizadores como revisores por conta própria<br/>(os utilizadores convidados são abordados na proporção de 1:5 necessária) | 6 |
| Um administrador cria uma revisão de acesso de grupo A, com 5 usuários e 28 utilizadores de convidados.<br/>Torna um Self-rever. | 1 administrador + 5 utilizadores como revisores por conta própria + 1 utilizador para abranger os utilizadores convidados na proporção de 1 necessária: 5 | 7 |

Para obter informações sobre como atribuir licenças aos seus usos, consulte [atribuir ou remover licenças através do portal do Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="learn-about-access-reviews"></a>Saiba mais sobre as revisões de acesso

Para saber mais sobre a criação e execução de revisões de acesso, assista a esta breve demonstração:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se estiver pronto para implementar as revisões de acesso na sua organização, siga estes passos no vídeo para carregar, dar formação aos seus administradores e criar a sua primeira revisão de acesso!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [Criar uma revisão de acesso de utilizadores numa função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Rever o acesso a grupos ou aplicações](perform-access-review.md)
- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
