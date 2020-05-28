---
title: O que são as revisões de acesso? - Diretório Ativo Azure / Microsoft Docs
description: Utilizando avaliações de acesso ao Azure Ative Directory, pode controlar a adesão do grupo e o acesso a aplicações para atender à governação, gestão de riscos e iniciativas de conformidade na sua organização.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a711fa522f6b105eb98d57d4f63afb90be255b3f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995042"
---
# <a name="what-are-azure-ad-access-reviews"></a>O que são as revisões de acesso do Azure AD?

As análises de acesso ao Azure Ative Directory (Azure AD) permitem às organizações gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso do utilizador pode ser revisto regularmente para garantir que apenas as pessoas certas tenham acesso continuado.

Aqui está um vídeo que fornece uma visão geral rápida das avaliações de acesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Por que as avaliações de acesso são importantes?

A Azure AD permite-lhe colaborar internamente dentro da sua organização e com utilizadores de organizações externas, como parceiros. Os utilizadores podem juntar-se a grupos, convidar convidados, conectar-se a aplicações na nuvem e trabalhar remotamente a partir do seu trabalho ou dispositivos pessoais. A conveniência de alavancar o poder do self-service levou à necessidade de melhores capacidades de gestão de acesso.

- À medida que os novos colaboradores se juntam, como é que garante que têm o acesso certo para serem produtivos?
- À medida que as pessoas movem equipas ou saem da empresa, como é que garantes que o seu antigo acesso seja removido, especialmente quando envolve hóspedes?
- Direitos de acesso excessivos podem levar a resultados e compromissos de auditoria, uma vez que indicam falta de controlo sobre o acesso.
- É preciso envolver-se proativamente com os proprietários de recursos para garantir que eles revejam regularmente quem tem acesso aos seus recursos.

## <a name="when-to-use-access-reviews"></a>Quando usar as avaliações de acesso?

- **Demasiados utilizadores em papéis privilegiados:** É uma boa ideia verificar quantos utilizadores têm acesso administrativo, quantos deles são Administradores Globais, e se há convidados ou parceiros que não tenham sido removidos depois de terem sido designados para fazer uma tarefa administrativa. Pode recertificar os utilizadores de atribuição de funções em [funções de AD Azure,](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) tais como Administradores Globais, ou funções de [recursos Azure,](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) tais como Administrador de Acesso ao Utilizador na experiência [Azure AD Privileged Identity Management (PIM).](../privileged-identity-management/pim-configure.md)
- Quando a **automatização é inviável:** Pode criar regras para a adesão dinâmica a grupos de segurança ou grupos do Office 365, mas e se os dados de RH não estiverem em Azure AD ou se os utilizadores ainda precisarem de acesso após deixarem o grupo para treinar a sua substituição? Em seguida, pode criar uma revisão sobre esse grupo para garantir que aqueles que ainda precisam de acesso devem ter acesso continuado.
- **Quando um grupo é utilizado para um novo propósito:** Se tiver um grupo que será sincronizado com a Azure AD, ou se planeia ativar a aplicação Salesforce para todos no grupo de equipas de vendas, seria útil pedir ao proprietário do grupo que reveja a adesão ao grupo antes de o grupo ser utilizado num conteúdo de risco diferente.
- Acesso crítico de dados às **empresas:** para determinados recursos, pode ser necessário pedir às pessoas fora das TI que se inscrevam regularmente e dêem uma justificação sobre a razão pela qual precisam de acesso para fins de auditoria.
- **Para manter a lista de exceções de uma política:** Num mundo ideal, todos os utilizadores seguiriam as políticas de acesso para garantir o acesso aos recursos da sua organização. No entanto, às vezes há casos de negócios que exigem que você alhe exceções. Como administrador de TI, pode gerir esta tarefa, evitar a supervisão das exceções políticas e fornecer aos auditores provas de que estas exceções são revistas regularmente.
- **Peça aos proprietários do grupo que confirmem que ainda precisam de hóspedes nos seus grupos:** O acesso dos funcionários pode ser automatizado com alguns no local DOIAM, mas não convidados. Se um grupo dá acesso a conteúdos sensíveis ao negócio, então é da responsabilidade do proprietário do grupo confirmar que os hóspedes ainda têm uma necessidade legítima de acesso.
- **As análises repetem-se periodicamente:** Pode configurar revisões de acesso recorrentes de utilizadores em frequências definidas, como semanal, mensal, trimestral ou anual, e os revisores serão notificados no início de cada revisão. Os revisores podem aprovar ou negar o acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

## <a name="where-do-you-create-reviews"></a>Onde cria críticas?

Dependendo do que pretende rever, irá criar a sua análise de acesso em avaliações de acesso a AD Azure, aplicações empresariais Azure AD (em pré-visualização) ou Azure AD PIM.

| Direitos de acesso dos utilizadores | Os revisores podem ser | Revisão criada em | Experiência do revisor |
| --- | --- | --- | --- |
| Membros do grupo de segurança</br>Membros do grupo de escritórios | Revisores especificados</br>Proprietários de grupos</br>Auto-revisão | Revisões de acesso do Azure AD</br>Grupos do Azure AD | Painel de acesso |
| Atribuído a uma aplicação conectada | Revisores especificados</br>Auto-revisão | Revisões de acesso do Azure AD</br>Aplicativos empresariais Azure AD (em pré-visualização) | Painel de acesso |
| Papel da AD Azure | Revisores especificados</br>Auto-revisão | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |
| Papel de recurso Azure | Revisores especificados</br>Auto-revisão | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |


## <a name="create-access-reviews"></a>Criar avaliações de acesso

Para criar uma avaliação de acesso, siga estes passos:

1. Vá ao [portal Azure](https://portal.azure.com) para gerir as avaliações de acesso e iniciar sessão como administrador global ou administrador de utilizador.

1. Procure e selecione **Azure Active Directory**.

      ![Pesquisa do portal Azure por Azure Ative Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. Selecione **Governação de Identidade**.

1. Na página Iniciar-da-página, clique no botão criar um botão de revisão de **acesso.**

   ![Página inicial de comentários de acesso](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 

### <a name="creating-access-review-on-a-group-that-can-be-assigned-to-azure-ad-role"></a>Criação de revisão de acesso a um grupo que pode ser atribuído ao papel da AD Azure
Se estiver na versão mais recente das Access Reviews (os seus revisores são direcionados para **o Meu Acesso** por defeito) então só o Administrador Global pode criar uma revisão de acesso em grupos designados por papéis. No entanto, se estiver na versão mais antiga das Avaliações de Acesso (os seus revisores são direcionados para o Painel de **Acesso** por defeito), então tanto o Administrador Global como o Administrador de Utilizador podem criar uma revisão de acesso em grupos designados por papéis.  

A nova experiência será lançada a todos os clientes no dia 1 de agosto de 2020, mas se quiser fazer o upgrade mais cedo, por favor faça um pedido aqui - [Azure AD Access Reviews - Experiência atualizada de revisores no My Access Signup](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

[Saiba mais sobre atribuir grupos a funções de AD Azure.](https://go.microsoft.com/fwlink/?linkid=2103037)

## <a name="learn-about-access-reviews"></a>Saiba mais sobre avaliações de acesso

Para saber mais sobre a criação e realização de avaliações de acesso, assista a esta pequena demonstração:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se estiver pronto para implementar avaliações de acesso na sua organização, siga estes passos no vídeo para bordo, treine os seus administradores e crie a sua primeira revisão de acesso!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Quantas licenças tem de ter?

Certifique-se de que o seu diretório tem pelo menos tantas licenças Azure AD Premium P2 como tem colaboradores que irão realizar as seguintes tarefas:

- Membros e utilizadores convidados que são designados como revisores
- Membro e utilizadores convidados que realizam uma auto-revisão
- Proprietários de grupos que realizam uma revisão de acesso
- Proprietários de candidaturas que realizam uma revisão de acesso

As licenças Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Não são necessárias licenças para os utilizadores com as funções de Administrador Global ou Administrador de Utilizador que configuram avaliações de acesso, configuram configurações ou aplicam as decisões a partir das avaliações.

Por cada licença Azure AD Premium P2 paga que atribui a um dos utilizadores da sua própria organização, pode utilizar o Azure AD business-to-business (B2B) para convidar até cinco utilizadores convidados ao abrigo do Subsídio de Utilizador Externo. Estes utilizadores também podem utilizar funcionalidades Azure AD Premium P2. Para mais informações, consulte a orientação de licenciamento de [colaboração Azure AD B2B.](../b2b/licensing-guidance.md)

Para mais informações sobre licenças, consulte [Atribuir ou remover licenças utilizando o portal de Diretório Ativo Azure](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Cenários de licença de exemplo

Aqui estão alguns cenários de licença de exemplo para ajudá-lo a determinar o número de licenças que deve ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| Um administrador cria uma revisão de acesso do Grupo A com 75 utilizadores e 1 proprietário do grupo, e atribui o proprietário do grupo como revisor. | 1 licença para o proprietário do grupo como revisor | 1 |
| Um administrador cria uma revisão de acesso do Grupo B com 500 utilizadores e 3 proprietários do grupo, e atribui os 3 proprietários do grupo como revisores. | 3 licenças para cada proprietário de grupo como revisores | 3 |
| Um administrador cria uma revisão de acesso do Grupo B com 500 utilizadores. Faz com que seja uma auto-revisão. | 500 licenças para cada utilizador como auto-revisores | 500 |
| Um administrador cria uma revisão de acesso do Grupo C com 50 utilizadores membros e 25 utilizadores convidados. Faz com que seja uma auto-revisão. | 50 licenças para cada utilizador como auto-revisores.<br/>(os utilizadores convidados estão cobertos na relação de 1:5 exigida) | 50 |
| Um administrador cria uma revisão de acesso do Grupo D com 6 utilizadores membros e 108 utilizadores convidados. Faz com que seja uma auto-revisão. | 6 licenças para cada utilizador como auto-revisores + 16 licenças adicionais para cobrir todos os 108 utilizadores convidados na relação de 1:5 exigida. 6 licenças, que abrangem 6 \* 5=30 utilizadores convidados. Para os restantes (108-6 \* 5)=78 utilizadores convidados, são necessárias 78/5=16 licenças adicionais. Assim, no total, são necessárias 6+16=22 licenças. | 22 |

## <a name="next-steps"></a>Próximos passos

- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [Criar uma revisão de acesso de utilizadores numa função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Rever o acesso a grupos ou aplicações](perform-access-review.md)
- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
