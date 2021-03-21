---
title: O que são as revisões de acesso? - Azure Ative Directory | Microsoft Docs
description: Utilizando avaliações de acesso ao Azure Ative Directory, pode controlar a adesão ao grupo e o acesso à aplicação para atender a iniciativas de governação, gestão de riscos e conformidade na sua organização.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 10/29/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.custom: contperf-fy21q1
ms.openlocfilehash: 22bfef17f68b2e83e4f7462d8e9af6fb8aacc284
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98246745"
---
# <a name="what-are-azure-ad-access-reviews"></a>O que são as revisões de acesso do Azure AD?

As análises de acesso do Azure Ative Directory (Azure AD) permitem às organizações gerir eficientemente os membros do grupo, aceder a aplicações empresariais e atribuições de funções. O acesso do utilizador pode ser revisto regularmente para garantir que apenas as pessoas certas tenham acesso continuado.

Aqui está um vídeo que fornece uma visão geral rápida das avaliações de acesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Porque é que as avaliações de acesso são importantes?

O Azure AD permite-lhe colaborar com utilizadores de dentro da sua organização e com utilizadores externos. Os utilizadores podem juntar-se a grupos, convidar os hóspedes, conectar-se a aplicações na nuvem e trabalhar remotamente a partir do seu trabalho ou dispositivos pessoais. A conveniência de utilizar o self-service levou à necessidade de melhores capacidades de gestão de acesso.

- À medida que os novos colaboradores se juntam, como garante que têm o acesso de que precisam para serem produtivos?
- À medida que as pessoas movem as equipas ou saem da empresa, como se certificam que o seu antigo acesso é removido?
- Direitos de acesso excessivos podem conduzir a compromissos.
- O direito de acesso excessivo também pode conduzir a conclusões da auditoria, uma vez que indicam falta de controlo sobre o acesso.
- É preciso envolver-se proativamente com os proprietários de recursos para garantir que revejam regularmente quem tem acesso aos seus recursos.

## <a name="when-should-you-use-access-reviews"></a>Quando deve utilizar comentários de acesso?

- **Demasiados utilizadores em funções privilegiadas:** É uma boa ideia verificar quantos utilizadores têm acesso administrativo, quantos deles são Administradores Globais, e se há algum convidado ou parceiro que não tenha sido removido após ter sido designado para fazer uma tarefa administrativa. Pode retificar os utilizadores de atribuição de funções em [funções de Azure AD,](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) tais como Administradores Globais, ou [funções de recursos Azure,](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) como o Administrador de Acesso ao Utilizador na experiência [Azure AD Privileged Identity Management (PIM).](../privileged-identity-management/pim-configure.md)
- **Quando a automatização não é possível:** Pode criar regras para a adesão dinâmica em grupos de segurança ou Grupos Microsoft 365, mas e se os dados de RH não estiverem no AZure AD ou se os utilizadores ainda precisarem de acesso após deixarem o grupo para treinar a sua substituição? Em seguida, pode criar uma revisão sobre esse grupo para garantir que aqueles que ainda precisam de acesso devem ter acesso continuado.
- **Quando um grupo é usado para um novo propósito:** Se tiver um grupo que será sincronizado com o AZure AD, ou se planeia ativar a aplicação Salesforce para todos no grupo de equipas de Vendas, seria útil pedir ao dono do grupo que revisse a filiação do grupo antes de o grupo ser utilizado num conteúdo de risco diferente.
- **Acesso crítico de dados empresariais:** para determinados recursos, pode ser necessário pedir às pessoas fora das TI que assinem regularmente e dêem uma justificação sobre o porquê de precisarem de acesso para fins de auditoria.
- **Para manter a lista de exceções de uma apólice:** Num mundo ideal, todos os utilizadores seguiriam as políticas de acesso para garantir o acesso aos recursos da sua organização. No entanto, às vezes há casos de negócios que exigem que asse exceções. Como administrador de TI, pode gerir esta tarefa, evitar a supervisão de exceções políticas e fornecer aos auditores a prova de que estas exceções são revistas regularmente.
- **Peça aos proprietários do grupo que confirmem que ainda precisam de hóspedes nos seus grupos:** O acesso dos colaboradores pode ser automatizado com alguns nas instalações Gestão de Identidade e Acesso (IAM), mas não convidados. Se um grupo dá aos hóspedes acesso a conteúdos sensíveis ao negócio, então é da responsabilidade do proprietário do grupo confirmar que os hóspedes ainda têm uma necessidade legítima de acesso ao negócio.
- **Ter revisões repetem-se periodicamente:** Pode configurar avaliações de acesso recorrentes dos utilizadores em frequências definidas, como semanal, mensal, trimestral ou anualmente, e os revisores serão notificados no início de cada revisão. Os revisores podem aprovar ou negar o acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

>[!NOTE]
>Se estiver pronto para experimentar comentários de Acesso, dê uma olhada na [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)

## <a name="where-do-you-create-reviews"></a>Onde cria críticas?

Dependendo do que pretende rever, criará a sua análise de acesso em avaliações de acesso AZure AD, aplicações empresariais AZure AD (em pré-visualização) ou Azure AD PIM.

| Direitos de acesso dos utilizadores | Os revisores podem ser | Revisão criada em | Experiência de revisor |
| --- | --- | --- | --- |
| Membros do grupo de segurança</br>Membros do grupo de escritório | Revisores especificados</br>Proprietários do grupo</br>Auto-revisão | Revisões de acesso do Azure AD</br>Grupos do Azure Active Directory | Painel de acesso |
| Atribuído a uma aplicação conectada | Revisores especificados</br>Auto-revisão | Revisões de acesso do Azure AD</br>Aplicativos empresariais AZURE AD (em pré-visualização) | Painel de acesso |
| Papel de AD AZure | Revisores especificados</br>Auto-revisão | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |
| Papel de recurso azul | Revisores especificados</br>Auto-revisão | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Quantas licenças tem?

O seu diretório necessita de pelo menos tantas licenças Azure AD Premium P2 como o número de funcionários que irão desempenhar as seguintes tarefas:

-   Utilizadores membros que são designados como revisores
-   Utilizadores membros que realizam uma auto-revisão
-   Utilizadores membros como proprietários de grupo que realizam uma revisão de acesso
-   Utilizadores membros como proprietários de aplicações que realizam uma revisão de acesso

Para os utilizadores convidados, as necessidades de licenciamento dependerão do modelo de licenciamento que está a usar. No entanto, as atividades dos utilizadores abaixo são consideradas a utilização Azure AD Premium P2:

-   Utilizadores convidados que são designados como revisores
-   Utilizadores convidados que realizam uma auto-revisão
-   Utilizadores convidados como proprietários de grupo que realizam uma revisão de acesso
-   Utilizadores convidados como proprietários de aplicações que realizam uma revisão de acesso


As licenças Azure AD Premium P2 **não** são necessárias para utilizadores com as funções de Administrador Global ou Administrador de Utilizador que configuram revisões de acesso, configuram definições ou aplicam as decisões das avaliações.

O acesso do utilizador azure AD é baseado num modelo de faturação mensal de utilizadores ativos (MAU), que substitui o modelo de faturação de rácio de 1:5. Para obter mais informações, consulte [os preços das identidades externas da Azure AD.](../external-identities/external-identities-pricing.md)

Para obter mais informações sobre licenças, consulte [atribuir ou remover licenças utilizando o portal Azure Ative Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exemplo de cenários de licença

Aqui estão alguns cenários de licença de exemplo para ajudá-lo a determinar o número de licenças que deve ter.

| Scenario | Cálculo | Número de licenças |
| --- | --- | --- |
| Um administrador cria uma revisão de acesso do Grupo A com 75 utilizadores e 1 dono do grupo, e atribui o proprietário do grupo como revisor. | 1 licença para o proprietário do grupo como revisor | 1 |
| Um administrador cria uma revisão de acesso do Grupo B com 500 utilizadores e 3 proprietários do grupo, e atribui os 3 proprietários do grupo como revisores. | 3 licenças para cada dono do grupo como revisores | 3 |
| Um administrador cria uma revisão de acesso do Grupo B com 500 utilizadores. Torna-se uma auto-revisão. | 500 licenças para cada utilizador como auto-revisores | 500 |
| Um administrador cria uma revisão de acesso do Grupo C com 50 utilizadores membros e 25 utilizadores convidados. Torna-se uma auto-revisão. | 50 licenças para cada utilizador como auto-revisores.* | 50 |
| Um administrador cria uma revisão de acesso do Grupo D com 6 utilizadores membros e 108 utilizadores convidados. Torna-se uma auto-revisão. | 6 licenças para cada utilizador como auto-revisores. Os utilizadores convidados são cobrados mensalmente por utilizador ativo (MAU). Não são necessárias licenças adicionais. *  | 6 |

\* O preço das identidades externas da Azure AD (utilizador convidado) baseia-se nos utilizadores ativos mensais (MAU), que é a contagem de utilizadores únicos com atividade de autenticação dentro de um mês civil. Este modelo substitui o modelo de faturação de rácio de 1:5, que permitiu até cinco utilizadores convidados por cada licença Azure AD Premium no seu inquilino. Quando o seu inquilino estiver ligado a uma subscrição e utilizar funcionalidades de Identidades Externas para colaborar com os utilizadores convidados, será automaticamente faturado utilizando o modelo de faturação baseado na MAU. Para mais informações, consulte o modelo de Faturação para identidades externas Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [Criar uma revisão de acesso de utilizadores numa função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Rever o acesso a grupos ou aplicações](perform-access-review.md)
- [Complete uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
