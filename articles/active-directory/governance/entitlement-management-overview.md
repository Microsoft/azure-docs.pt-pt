---
title: O que é a gestão de direitos? - Azure AD
description: Obtenha uma visão geral da gestão de direitos do Azure Ative Diretório e como pode usá-lo para gerir o acesso a grupos, aplicações e sites SharePoint Online para utilizadores internos e externos.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f4ce292ad507eb0208633db7743b881508a8e58
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144438"
---
# <a name="what-is-azure-ad-entitlement-management"></a>O que é a gestão de direitos do Azure AD?

A gestão de direitos da Azure Ative Directory (Azure AD) é uma funcionalidade de [governação](identity-governance-overview.md) de identidade que permite às organizações gerir a identidade e aceder ao ciclo de vida em escala, automatizando fluxos de trabalho de pedidos de acesso, atribuições de acesso, revisões e expiração.

Os colaboradores das organizações precisam de acesso a vários grupos, aplicações e sites para desempenhar em seu trabalho. Gerir este acesso é um desafio, à medida que os requisitos mudam - novas aplicações são adicionadas ou os utilizadores precisam de direitos de acesso adicionais.  Este cenário torna-se mais complicado quando colabora com organizações externas - pode não saber quem na outra organização precisa de acesso aos recursos da sua organização, e eles não saberão que aplicações, grupos ou sites a sua organização está a usar.

A gestão de direitos da Azure AD pode ajudá-lo a gerir de forma mais eficiente o acesso a grupos, aplicações e sites SharePoint Online para utilizadores internos, bem como para utilizadores fora da sua organização que precisam de acesso a esses recursos.

## <a name="why-use-entitlement-management"></a>Por que usar a gestão de direitos?

As organizações empresariais enfrentam frequentemente desafios na gestão do acesso dos colaboradores a recursos como:

- Os utilizadores podem não saber que acesso devem ter, e mesmo que o façam, podem ter dificuldade em localizar os indivíduos certos para aprovaro seu acesso
- Uma vez que os utilizadores encontrem e tenham acesso a um recurso, podem manter-se no acesso mais tempo do que o necessário para fins comerciais

Estes problemas são agravados para os utilizadores que precisam de acesso de outra organização, como utilizadores externos que são de organizações de cadeias de fornecimento ou outros parceiros de negócio. Por exemplo:

- Ninguém pode conhecer todos os indivíduos específicos dos diretórios de outra organização para poder convidá-los
- Mesmo que pudessem convidar estes utilizadores, ninguém naquela organização se lembraria de gerir de forma consistente todo o acesso do utilizador.

A gestão de direitos da Azure AD pode ajudar a enfrentar estes desafios.  Para saber mais sobre como os clientes têm usado a gestão de direitos da Azure AD, você pode ler o estudo de [caso Avanade](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) e o estudo de [caso Centrica](https://customers.microsoft.com/story/757467-centrica-energy-azure).  Este vídeo fornece uma visão geral da gestão de direitos e do seu valor:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>O que posso fazer com a gestão de direitos?

Aqui estão algumas das capacidades de gestão de direitos:

- Delegar a não administradores a capacidade de criar pacotes de acesso. Estes pacotes de acesso contêm recursos que os utilizadores podem solicitar, e os gestores de pacotes de acesso delegados podem definir políticas com regras para as quais os utilizadores podem solicitar, quem deve aprovar o seu acesso e quando o acesso expirar.
- Selecione organizações conectadas cujos utilizadores possam solicitar acesso.  Quando um utilizador que ainda não se encontra no seu diretório solicita acesso e é aprovado, é automaticamente convidado para o seu diretório e acesso atribuído.  Quando o seu acesso expirar, se não tiverem outras atribuições de pacotes de acesso, a sua conta B2B no seu diretório pode ser automaticamente removida.

Você pode começar com o nosso tutorial para criar o seu primeiro pacote de [acesso.](entitlement-management-access-package-first.md) Você também pode ler os [cenários comuns](entitlement-management-scenarios.md), ou assistir vídeos, incluindo

- [Como implementar a gestão de direitos da Azure AD na sua organização](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Como monitorizar e escalar o seu uso da gestão de direitos da Azure AD](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Como delegar na gestão de direitos](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Quais são os pacotes de acesso e quais os recursos que posso gerir com eles?

A gestão de direitos introduz à Azure AD o conceito de pacote de *acesso.* Um pacote de acesso é um conjunto de todos os recursos com o acesso que um utilizador precisa para trabalhar num projeto ou executar a sua tarefa. Os pacotes de acesso são usados para reger o acesso aos seus colaboradores internos, bem como utilizadores fora da sua organização.

 Aqui estão os tipos de recursos a que pode gerir o acesso do utilizador com a gestão de direitos:

- Adesão a grupos de segurança da AD Azure
- Adesão ao Office 365 Grupos e Equipas
- Atribuição a aplicações empresariais da Azure AD, incluindo aplicações SaaS e aplicações personalizadas integradas que apoiam federação/sinal único sobre e/ou provisionamento
- Adesão a sites online SharePoint

Também pode controlar o acesso a outros recursos que dependem de grupos de segurança da AD Azure ou do Office 365 Groups.  Por exemplo:

- Pode dar licenças aos utilizadores para o Microsoft Office 365 utilizando um grupo de segurança Azure AD num pacote de acesso e configurando [licenças baseadas em grupo](../users-groups-roles/licensing-groups-assign.md) para esse grupo
- Você pode dar aos utilizadores acesso para gerir os recursos Azure usando um grupo de segurança Azure AD em um pacote de acesso e criando uma atribuição de [função Azure](../../role-based-access-control/role-assignments-portal.md) para esse grupo

## <a name="how-do-i-control-who-gets-access"></a>Como controlo quem tem acesso?

Com um pacote de acesso, um administrador ou gestor de pacotes de acesso delegado lista os recursos (grupos, apps e sites), e as funções que os utilizadores precisam para esses recursos.

Os pacotes de acesso também incluem uma ou mais *políticas.* Uma política define as regras ou guarda-costas para a atribuição do pacote de acesso. Cada política pode ser utilizada para garantir que apenas os utilizadores adequados possam solicitar o acesso, que existam aprovadores para o seu pedido, e que o seu acesso a esses recursos seja limitado no tempo e expirará se não for renovado.

![Pacote de acesso e políticas](./media/entitlement-management-overview/elm-overview-access-package.png)

Dentro de cada política, um administrador ou gestor de pacotes de acesso define

- Ou os utilizadores já existentes (normalmente empregados ou convidados), ou as organizações parceiras de utilizadores externos, que são elegíveis para solicitar acesso
- O processo de aprovação e os utilizadores que podem aprovar ou negar o acesso
- A duração da atribuição de acesso de um utilizador, uma vez aprovada, antes de expirar a atribuição

O diagrama que se segue mostra um exemplo dos diferentes elementos na gestão de direitos. Mostra um catálogo com dois pacotes de acesso exemplo.

- **O pacote de acesso 1** inclui um único grupo como recurso. O acesso é definido com uma política que permite a um conjunto de utilizadores no diretório solicitar acesso.
- **O pacote de acesso 2** inclui um grupo, uma aplicação e um site SharePoint Online como recursos. O acesso é definido com duas políticas diferentes. A primeira política permite que um conjunto de utilizadores no diretório solicite acesso. A segunda política permite que os utilizadores num diretório externo solicitem acesso.

![Visão geral da gestão dos direitos](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Quando devo usar pacotes de acesso?

Os pacotes de acesso não substituem outros mecanismos de atribuição de acesso.  São mais adequados em situações como:

- Os empregados precisam de acesso limitado ao tempo para uma determinada tarefa.  Por exemplo, você pode usar licenciamento baseado em grupo e um grupo dinâmico para garantir que todos os colaboradores têm uma caixa de correio Exchange Online, e, em seguida, usar pacotes de acesso para situações em que os colaboradores precisam de acesso adicional, como ler recursos departamentais de outro departamento.
- O acesso tem de ser aprovado pelo gestor de um empregado ou por outras pessoas designadas.
- Os departamentos desejam gerir as suas próprias políticas de acesso aos seus recursos sem o envolvimento das TI.  
- Duas ou mais organizações estão a colaborar num projeto e, como resultado, vários utilizadores de uma organização terão de ser trazidos via Azure AD B2B para aceder aos recursos de outra organização.

## <a name="how-do-i-delegate-access"></a>Como delegar o acesso?

 Os pacotes de acesso são definidos em contentores *chamados catálogos.*  Você pode ter um único catálogo para todos os seus pacotes de acesso, ou você pode designar indivíduos para criar e possuir seus próprios catálogos. Um administrador pode adicionar recursos a qualquer catálogo, mas um não administrador só pode adicionar a um catálogo os recursos que possuem. O proprietário de um catálogo pode adicionar outros utilizadores como coproprietários de catálogos, ou como gestores de pacotes de acesso.  Estes cenários são descritos ainda no artigo [delegação e funções na gestão de direitos da AD Azure.](entitlement-management-delegate.md)

## <a name="summary-of-terminology"></a>Resumo da terminologia

Para melhor compreender a gestão do direito e a sua documentação, pode remeter para a seguinte lista de termos.

| Termo | Descrição |
| --- | --- |
| pacote de acesso | Um conjunto de recursos que uma equipa ou projeto precisa e é governado com políticas. Um pacote de acesso está sempre contido num catálogo. Criaria um novo pacote de acesso para um cenário em que os utilizadores precisam de solicitar acesso.  |
| pedido de acesso | Um pedido de acesso aos recursos num pacote de acesso. Um pedido normalmente passa por um fluxo de trabalho de aprovação.  Se aprovado, o utilizador solicitado recebe uma atribuição de pacote de acesso. |
| atribuição | Uma atribuição de um pacote de acesso a um utilizador garante que o utilizador tem todas as funções de recursos desse pacote de acesso.  As atribuições de pacotes de acesso normalmente têm um prazo antes de expirarem. |
| catálogo | Um contentor de recursos relacionados e pacotes de acesso.  Os catálogos são utilizados para a delegação, para que os não administradores possam criar os seus próprios pacotes de acesso. Os proprietários de catálogos podem adicionar recursos que possuem a um catálogo. |
| criador de catálogo | Uma coleção de utilizadores que estão autorizados a criar novos catálogos.  Quando um utilizador não administrador autorizado a ser criador de catálogos cria um novo catálogo, torna-se automaticamente o proprietário desse catálogo. |
| organização conectada | Um diretório ou domínio externo da AD Azure com o que tem uma relação. Os utilizadores de uma organização conectada podem ser especificados numa política como sendo permitido solicitar acesso. |
| política | Um conjunto de regras que define o ciclo de vida de acesso, como a forma como os utilizadores têm acesso, quem pode aprovar, e quanto tempo os utilizadores têm acesso através de uma atribuição. Uma política está ligada a um pacote de acesso. Por exemplo, um pacote de acesso poderia ter duas políticas - uma para os colaboradores solicitarem acesso e uma segunda para os utilizadores externos solicitarem acesso. |
| recurso | Um ativo, como um grupo de Escritórios, um grupo de segurança, uma aplicação ou um site SharePoint Online, com uma função a que um utilizador pode ser autorizado. |
| diretório de recursos | Um diretório que tem um ou mais recursos para partilhar. |
| papel de recursos | Uma coleção de permissões associadas e definidas por um recurso. Um grupo tem duas funções : membro e proprietário. Os sites do SharePoint normalmente têm 3 funções, mas podem ter funções personalizadas adicionais. As aplicações podem ter papéis personalizados. |


## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Nuvens especializadas, como a Azure Germany, e a Azure China 21Vianet, não estão atualmente disponíveis para uso.

### <a name="how-many-licenses-must-you-have"></a>Quantas licenças tem de ter?

Certifique-se de que o seu diretório tem pelo menos tantas licenças Azure AD Premium P2 como tem:

- Utilizadores membros que **possam** solicitar um pacote de acesso.
- Membro e utilizadores convidados que solicitem um pacote de acesso.
- Membros e utilizadores convidados que aprovam pedidos de um pacote de acesso.
- Membros e utilizadores convidados que tenham uma atribuição direta a um pacote de acesso.

As licenças Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Não são necessárias licenças para os utilizadores com a função de Administrador Global que configuram os catálogos iniciais, pacotes de acesso e políticas, e delegam tarefas administrativas a outros utilizadores.
- Não são necessárias licenças para utilizadores que tenham sido delegadas tarefas administrativas, tais como criador de catálogo, proprietário de catálogo e gestor de pacotes de acesso.
- Não são necessárias licenças para os hóspedes que **possam** solicitar pacotes de acesso, mas **não** solicitem um pacote de acesso.

Por cada licença Azure AD Premium P2 paga que compra para os seus utilizadores membros (colaboradores), pode utilizar o Azure AD B2B para convidar até 5 utilizadores convidados. Estes utilizadores também podem utilizar funcionalidades Azure AD Premium P2. Para mais informações, consulte a orientação de licenciamento de [colaboração Azure AD B2B.](../b2b/licensing-guidance.md)

Para mais informações sobre licenças, consulte [Atribuir ou remover licenças utilizando o portal de Diretório Ativo Azure](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Cenários de licença de exemplo

Aqui estão alguns cenários de licença de exemplo para ajudá-lo a determinar o número de licenças que deve ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| Um Administrador Global do Woodgrove Bank cria catálogos iniciais e delega tarefas administrativas a 6 outros utilizadores. Uma das políticas especifica que **Todos os colaboradores** (2.000 colaboradores) podem solicitar um conjunto específico de pacotes de acesso. 150 funcionários solicitam os pacotes de acesso. | 2.000 funcionários que **podem** solicitar os pacotes de acesso | 2.000 |
| Um Administrador Global do Woodgrove Bank cria catálogos iniciais e delega tarefas administrativas a 6 outros utilizadores. Uma das políticas especifica que **Todos os colaboradores** (2.000 colaboradores) podem solicitar um conjunto específico de pacotes de acesso. Outra política especifica que alguns utilizadores **de Utilizadores do parceiro Contoso** (hóspedes) podem solicitar os mesmos pacotes de acesso sujeitos a aprovação. Contoso tem 30.000 utilizadores. 150 colaboradores solicitam o acesso aos pacotes de acesso e 10.500 utilizadores da Contoso solicitam acesso. | 2.000 colaboradores + 500 utilizadores convidados de Contoso que excedem o rácio 1:5 (10.500 - (2.000 * 5)) | 2.500 |

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Crie o seu primeiro pacote de acesso](entitlement-management-access-package-first.md)
- [Cenários comuns](entitlement-management-scenarios.md)
