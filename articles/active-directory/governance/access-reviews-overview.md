---
title: Quais são as revisões de acesso do Azure AD? | Microsoft Docs
description: Utilizar revisões de acesso do Azure Active Directory, pode controlar o acesso de associação e a aplicação de grupo para atender a governação, gestão de riscos e iniciativas de conformidade na sua organização.
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
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5fa81e564cf28f8b5478abe9ec5bb63d0f11bd3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449670"
---
# <a name="what-are-azure-ad-access-reviews"></a>Quais são as revisões de acesso do Azure AD?

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

- **Demasiados utilizadores nas funções privilegiadas:** Ele é uma boa idéia para verificar quantos utilizadores têm acesso administrativo, quantos deles não estão os administradores globais, e se houver qualquer convidado convidados ou parceiros que não foram removidos depois de serem atribuídas a fazer uma tarefa administrativa. Pode voltar a certificar os utilizadores de atribuição de função no [funções de diretório do Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) por exemplo, os administradores globais, ou [funções de recursos do Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) como administrador de acesso de utilizador no [do Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) experiência.
- **Quando a automação é inviável:** Pode criar regras de associação de grupo dinâmica em grupos de segurança ou grupos do Office 365, mas e se os dados de RH não estiver no Azure AD ou se os usuários ainda precisam de acesso após a deixar o grupo para preparar a sua substituição? Em seguida, pode criar uma revisão nesse grupo para garantir que aqueles que ainda precisam de acesso devem ter acesso contínuo.
- **Quando um grupo é utilizado para uma nova finalidade:** Se tiver um grupo que vai ser sincronizado com o Azure AD, ou se pretender ativar a aplicação Salesforce para todos os utilizadores no grupo de equipa de vendas, seria útil fazer o proprietário do grupo para rever a associação de grupo antes do grupo que está a ser utilizado num co de risco diferentes nteúdos.
- **Acesso de dados críticos de negócio:** para determinados recursos, poderá ser necessário para pedir às pessoas fora do departamento de TI regularmente termine a sessão e dar uma justificativa sobre por que eles precisarem de acesso para fins de auditoria.
- **Para manter a lista de exceções de uma política:** Num mundo ideal, todos os utilizadores seguir o acesso políticas para proteger o acesso aos recursos da sua organização. No entanto, às vezes, existem casos comerciais que exigem que crie exceções. O administrador de TI, pode gerir esta tarefa, evitar supervisão de exceções da política e fornecer auditores prova que essas exceções são revisadas regularmente.
- **Faça os proprietários do grupo para confirmar que ainda precisam convidados nos respetivos grupos:** Acesso do funcionário pode ser automatizado com alguns no local IAM, mas não convidados convidados. Se um grupo fornece acesso de convidados para conteúdo confidencial da empresa, em seguida, é responsabilidade do proprietário do grupo para confirmar se os convidados ainda terá uma necessidade legítima de negócios para acesso.
- **Repetir periodicamente as revisões de ter:** Pode configurar recorrentes revisões de acesso dos utilizadores em frequências de conjunto como semanalmente, mensalmente, trimestralmente ou anualmente, e serão notificados os revisores no início de cada revisão. Os revisores podem aprovar ou negar o acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

## <a name="where-do-you-create-reviews"></a>Onde criar revisões?

Consoante o que pretende rever, criará sua revisão de acesso em revisões de acesso do Azure AD, aplicações empresariais do Azure AD (em pré-visualização) ou Azure AD PIM.

| Direitos de acesso de utilizadores | Os revisores podem ser | Revisão criado em | Experiência do revisor |
| --- | --- | --- | --- |
| Membros do grupo de segurança</br>Membros de grupo do Office | Revisores especificados</br>Proprietários do grupo</br>Revisão automática | Revisões de acesso do Azure AD</br>Grupos do Azure AD | Painel de acesso |
| Atribuído a uma aplicação ligada | Revisores especificados</br>Revisão automática | Revisões de acesso do Azure AD</br>Aplicações de enterprise do Azure AD (em pré-visualização) | Painel de acesso |
| Função de diretório do Azure AD | Revisores especificados</br>Revisão automática | Azure AD PIM | Portal do Azure |
| Função de recursos do Azure | Revisores especificados</br>Revisão automática | Azure AD PIM | Portal do Azure |

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar as revisões de acesso, tem de ter uma das seguintes licenças:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 licença

Para obter mais informações, consulte [como: Inscrever-se no Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) ou [Enterprise Mobility + Security E5 avaliação](https://aka.ms/emse5trial).

## <a name="get-started-with-access-reviews"></a>Comece com as revisões de acesso

Para saber mais sobre a criação e execução de revisões de acesso, assista a esta breve demonstração:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se estiver pronto para implementar as revisões de acesso na sua organização, siga estes passos no vídeo para carregar, dar formação aos seus administradores e criar a sua primeira revisão de acesso!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>Ative as revisões de acesso

Para ativar as revisões de acesso, siga estes passos.

1. Como um Administrador Global ou administrador de conta de utilizador, inicie sessão para o [portal do Azure](https://portal.azure.com) em que pretende utilizar o acesso a revisões.

1. Clique em **todos os serviços** e encontrar o acesso a revisões de serviço.

1. Clique em **as revisões de acesso**.

    ![Todos os serviços - as revisões de acesso](./media/access-reviews-overview/all-services-access-reviews.png)

1. Na lista de navegação, clique em **carregar** para abrir o **carregar revisões de acesso** página.

    ![Carregar revisões de acesso](./media/access-reviews-overview/onboard-button.png)

1. Clique em **criar** ativar o acesso a revisões no diretório atual.

    ![Carregar revisões de acesso](./media/access-reviews-overview/onboard-access-reviews.png)

    Da próxima vez que inicia o acesso a revisões, as opções de revisão de acesso serão ativadas.

    ![Revisões de acesso ativadas](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [Criar uma revisão de acesso de utilizadores numa função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Rever o acesso a grupos ou aplicações](perform-access-review.md)
- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
