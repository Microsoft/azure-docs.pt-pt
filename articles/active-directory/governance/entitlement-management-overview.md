---
title: O que é a gestão de direitos? - Azure AD
description: Obtenha uma visão geral da gestão do direito do Azure Ative Directory e como pode usá-lo para gerir o acesso a grupos, aplicações e sites SharePoint Online para utilizadores internos e externos.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 11/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.custom: contperfq1
ms.openlocfilehash: 163947268d79a0297eef3f3f6e97187a0aef6994
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95738147"
---
# <a name="what-is-azure-ad-entitlement-management"></a>O que é a gestão de direitos do Azure AD?

A gestão de direitos do Azure Ative Directory (Azure AD) é uma funcionalidade [de governação de identidade](identity-governance-overview.md) que permite às organizações gerir o ciclo de vida de identidade e acesso em escala, automatizando fluxos de trabalho de pedidos de acesso, atribuições de acesso, revisões e expiração.

Os colaboradores das organizações precisam de acesso a vários grupos, aplicações e sites para desempenhar o seu trabalho. Gerir este acesso é um desafio, uma vez que os requisitos mudam - novas aplicações são adicionadas ou os utilizadores precisam de direitos de acesso adicionais.  Este cenário torna-se mais complicado quando colabora com organizações externas - pode não saber quem na outra organização precisa de acesso aos recursos da sua organização, e eles não saberão quais aplicações, grupos ou sites que a sua organização está a usar.

A gestão de direitos AD AZure pode ajudá-lo a gerir de forma mais eficiente o acesso a grupos, aplicações e sites SharePoint Online para utilizadores internos, e também para utilizadores fora da sua organização que precisam de acesso a esses recursos.

## <a name="why-use-entitlement-management"></a>Porquê usar a gestão de direitos?

As organizações empresariais enfrentam frequentemente desafios na gestão do acesso dos colaboradores a recursos como:

- Os utilizadores podem não saber que acesso devem ter, e mesmo que tenham, podem ter dificuldade em localizar os indivíduos certos para aprovarem o seu acesso.
- Uma vez que os utilizadores encontrem e recebam acesso a um recurso, podem manter o acesso a mais tempo do que o necessário para fins comerciais

Estes problemas são agravados para utilizadores que precisam de acesso de outra organização, como utilizadores externos que são de organizações da cadeia de fornecimento ou outros parceiros de negócio. Por exemplo:

- Ninguém pode conhecer todos os indivíduos específicos nos diretórios de outra organização para poder convidá-los
- Mesmo que pudessem convidar estes utilizadores, ninguém naquela organização se lembraria de gerir o acesso de todos os utilizadores de forma consistente.

A gestão de direitos da AD Azure pode ajudar a resolver estes desafios.  Para saber mais sobre como os clientes têm usado a gestão de direitos Ad Azure, você pode ler o [estudo de caso Avanade](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) e o [estudo de caso Centrica.](https://customers.microsoft.com/story/757467-centrica-energy-azure)  Este vídeo fornece uma visão geral da gestão de direitos e do seu valor:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>O que posso fazer com a gestão de direitos?

Aqui estão algumas das capacidades de gestão de direitos:

- Delegar em não administradores a capacidade de criar pacotes de acesso. Estes pacotes de acesso contêm recursos que os utilizadores podem solicitar, e os gestores de pacotes de acesso delegado podem definir políticas com regras para as quais os utilizadores podem solicitar, quem deve aprovar o seu acesso e quando o acesso expirar.
- Selecione organizações conectadas cujos utilizadores podem solicitar acesso.  Quando um utilizador que ainda não está no seu diretório solicita acesso, e é aprovado, é automaticamente convidado para o seu diretório e acesso atribuído.  Quando o seu acesso expirar, se não tiverem outras atribuições de pacotes de acesso, a sua conta B2B no seu diretório pode ser automaticamente removida.

>[!NOTE]
>Se estiver pronto para experimentar a gestão de direitos pode começar com o nosso [tutorial para criar o seu primeiro pacote de acesso.](entitlement-management-access-package-first.md)

Também pode ler os [cenários comuns,](entitlement-management-scenarios.md)ou ver vídeos, incluindo

- [Como implementar a gestão de direitos AD da Azure na sua organização](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Como monitorizar e escalar o seu uso da gestão de direitos Ad Azure](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Como delegar na gestão de direitos](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Quais são os pacotes de acesso e que recursos posso gerir com eles?

A gestão de direitos introduz à Azure AD o conceito de pacote de *acesso.* Um pacote de acesso é um pacote de todos os recursos com o acesso que um utilizador precisa para trabalhar num projeto ou executar a sua tarefa. Os pacotes de acesso são usados para reger o acesso aos seus funcionários internos, e também utilizadores fora da sua organização.

 Aqui estão os tipos de recursos a que pode gerir o acesso do utilizador com a gestão de direitos:

- Adesão a grupos de segurança Azure AD
- Membro da Microsoft 365 Grupos e Equipas
- Atribuição a aplicações empresariais AZure AD, incluindo aplicações SaaS e aplicações personalizadas que suportam a federação/único sign-on e/ou provisioning
- Adesão a sites online do SharePoint

Também pode controlar o acesso a outros recursos que dependem de grupos de segurança AD Azure ou grupos Microsoft 365.  Por exemplo:

- Pode dar licenças aos utilizadores para o Microsoft 365 utilizando um grupo de segurança AZure AD num pacote de acesso e configurando [o licenciamento baseado em grupo](../enterprise-users/licensing-groups-assign.md) para esse grupo.
- Você pode dar aos utilizadores acesso para gerir recursos Azure usando um grupo de segurança Azure AD em um pacote de acesso e criando uma [atribuição de papel Azure](../../role-based-access-control/role-assignments-portal.md) para esse grupo.
- Pode dar aos utilizadores acesso para gerir as funções de Azure AD utilizando grupos atribuíveis às funções AD do Azure num pacote de acesso e [atribuindo um papel de AD Azure a esse grupo.](../roles/groups-assign-role.md)

## <a name="how-do-i-control-who-gets-access"></a>Como controlo quem tem acesso?

Com um pacote de acesso, um administrador ou gestor de pacotes de acesso delegado lista os recursos (grupos, apps e sites) e as funções que os utilizadores precisam para esses recursos.

Os pacotes de acesso também incluem uma ou mais *políticas.* Uma política define as regras ou guarda-costas para a atribuição ao pacote de acesso. Cada política pode ser utilizada para garantir que apenas os utilizadores adequados possam solicitar o acesso, que existam aprovadores para o seu pedido, e que o seu acesso a esses recursos é limitado no tempo e expirará se não for renovado.

![Pacote de acesso e políticas](./media/entitlement-management-overview/elm-overview-access-package.png)

Dentro de cada política, um administrador ou gestor de pacotes de acesso define

- Quer os utilizadores já existentes (normalmente empregados ou hóspedes já convidados), ou as organizações parceiras de utilizadores externos, que são elegíveis para solicitar acesso
- O processo de aprovação e os utilizadores que podem aprovar ou negar o acesso
- A duração da atribuição de acesso de um utilizador, uma vez aprovada, antes de expirar a atribuição

O diagrama que se segue mostra um exemplo dos diferentes elementos na gestão dos direitos. Mostra um catálogo com pacotes de acesso de dois exemplos.

- **O pacote de acesso 1** inclui um único grupo como recurso. O acesso é definido com uma política que permite a um conjunto de utilizadores no diretório solicitar acesso.
- **O pacote de acesso 2** inclui um grupo, uma aplicação e um site SharePoint Online como recursos. O acesso é definido com duas políticas diferentes. A primeira política permite que um conjunto de utilizadores no diretório solicitem acesso. A segunda política permite que os utilizadores de um diretório externo solicitem acesso.

![Visão geral da gestão de direitos](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Quando devo usar pacotes de acesso?

Os pacotes de acesso não substituem outros mecanismos de acesso.  São mais adequados em situações como:

- Os empregados precisam de acesso limitado a tempo para uma determinada tarefa.  Por exemplo, você pode usar o licenciamento baseado em grupo e um grupo dinâmico para garantir que todos os funcionários têm uma caixa de correio Exchange Online, e depois usar pacotes de acesso para situações em que os funcionários precisam de acesso adicional, como ler recursos departamés de departamento de outro departamento.
- Acesso que requer a aprovação do gerente de um empregado ou de outros indivíduos designados.
- Os departamentos querem gerir as suas próprias políticas de acesso aos seus recursos sem o envolvimento das TI.  
- Duas ou mais organizações estão a colaborar num projeto e, como resultado, vários utilizadores de uma organização terão de ser trazidos através do Azure AD B2B para aceder aos recursos de outra organização.

## <a name="how-do-i-delegate-access"></a>Como devorei acesso?

 Os pacotes de acesso são definidos em contentores *chamados catálogos.*  Você pode ter um único catálogo para todos os seus pacotes de acesso, ou você pode designar indivíduos para criar e possuir seus próprios catálogos. Um administrador pode adicionar recursos a qualquer catálogo, mas um não administrador só pode adicionar a um catálogo os recursos que possui. Um proprietário de catálogo pode adicionar outros utilizadores como coproprietários de catálogos, ou como gestores de pacotes de acesso.  Estes cenários são descritos mais aprofundadamente na delegação de [artigos e nas funções na gestão dos direitos da AD Azure.](entitlement-management-delegate.md)

## <a name="summary-of-terminology"></a>Resumo da terminologia

Para melhor compreender a gestão de direitos e a sua documentação, pode remeter para a seguinte lista de termos.

| Termo | Descrição |
| --- | --- |
| pacote de acesso | Um conjunto de recursos que uma equipa ou projeto precisa e é governado com políticas. Um pacote de acesso está sempre contido num catálogo. Criaria um novo pacote de acesso para um cenário em que os utilizadores precisam de pedir acesso.  |
| pedido de acesso | Um pedido de acesso aos recursos num pacote de acesso. Um pedido normalmente passa por um fluxo de trabalho de aprovação.  Se aprovado, o utilizador que solicita recebe uma atribuição de pacote de acesso. |
| atribuição | Uma atribuição de um pacote de acesso a um utilizador garante que o utilizador tem todas as funções de recurso desse pacote de acesso.  As atribuições de pacotes de acesso normalmente têm um prazo limite antes de expirarem. |
| catálogo | Um contentor de recursos relacionados e pacotes de acesso.  Os catálogos são utilizados para delegação, para que os não administradores possam criar os seus próprios pacotes de acesso. Os proprietários de catálogos podem adicionar recursos que possuem a um catálogo. |
| criador de catálogo | Uma coleção de utilizadores autorizados a criar novos catálogos.  Quando um utilizador não administrador autorizado a ser um criador de catálogos cria um novo catálogo, torna-se automaticamente o proprietário desse catálogo. |
| organização conectada | Um diretório ou domínio AD Azure externo com o que tem uma relação. Os utilizadores de uma organização conectada podem ser especificados numa política como sendo autorizados a solicitar acesso. |
| política | Um conjunto de regras que definem o ciclo de vida de acesso, como a forma como os utilizadores têm acesso, quem pode aprovar e quanto tempo os utilizadores têm acesso através de uma atribuição. Uma política está ligada a um pacote de acesso. Por exemplo, um pacote de acesso poderia ter duas políticas - uma para os funcionários solicitarem o acesso e uma segunda para os utilizadores externos solicitarem acesso. |
| recurso | Um ativo, como um grupo de Escritório, um grupo de segurança, uma aplicação ou um site SharePoint Online, com uma função a que um utilizador pode ser autorizado. |
| diretório de recursos | Um diretório que tem um ou mais recursos para partilhar. |
| papel de recursos | Uma coleção de permissões associadas e definidas por um recurso. Um grupo tem duas funções - membro e proprietário. Os sites sharePoint normalmente têm 3 funções, mas podem ter funções personalizadas adicionais. As aplicações podem ter funções personalizadas. |


## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Nuvens especializadas, como a Azure Germany, e a Azure China 21Vianet, não estão atualmente disponíveis para uso.

### <a name="how-many-licenses-must-you-have"></a>Quantas licenças tem?

Certifique-se de que o seu diretório tem pelo menos tantas licenças Azure AD Premium P2 como tem:

- Utilizadores membros que **podem** solicitar um pacote de acesso.
- Utilizadores membros que <u>solicitam</u> um pacote de acesso.
- Utilizadores <u>membros que aprovam pedidos</u> de um pacote de acesso.
- Utilizadores membros que <u>analisam atribuições</u> para um pacote de acesso. 
- Utilizadores membros que têm uma <u>atribuição direta</u> a um pacote de acesso.

Para os utilizadores convidados, as necessidades de licenciamento dependerão do [modelo de licenciamento](../external-identities/external-identities-pricing.md) que está a usar. No entanto, as atividades dos utilizadores abaixo são consideradas a utilização Azure AD Premium P2:
- Utilizadores convidados que <u>solicitam</u> um pacote de acesso. 
- Utilizadores <u>convidados que aprovam pedidos</u> de um pacote de acesso.
- Utilizadores <u>convidados que analisam atribuições</u> para um pacote de acesso.
- Utilizadores convidados que tenham uma <u>atribuição direta</u> a um pacote de acesso. 

As licenças Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Não são necessárias licenças para utilizadores com a função de Administrador Global que criem os catálogos iniciais, pacotes de acesso e políticas e delegam tarefas administrativas a outros utilizadores.
- Não são necessárias licenças para utilizadores que tenham sido delegadas tarefas administrativas, tais como criador de catálogos, proprietário de catálogos e gestor de pacotes de acesso.
- Não são necessárias licenças para os hóspedes que **possam** solicitar pacotes de acesso, mas **não** solicitem um pacote de acesso.

Para obter mais informações sobre licenças, consulte [atribuir ou remover licenças utilizando o portal Azure Ative Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exemplo de cenários de licença

Aqui estão alguns cenários de licença de exemplo para ajudá-lo a determinar o número de licenças que deve ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| Um Administrador Global do Woodgrove Bank cria catálogos iniciais e delega tarefas administrativas a outros 6 utilizadores. Uma das políticas especifica que **todos os colaboradores** (2.000 colaboradores) podem solicitar um conjunto específico de pacotes de acesso. 150 funcionários pedem os pacotes de acesso. | 2.000 funcionários que **podem** solicitar os pacotes de acesso | 2.000 |
| Um Administrador Global do Woodgrove Bank cria catálogos iniciais e delega tarefas administrativas a outros 6 utilizadores. Uma das políticas especifica que **todos os colaboradores** (2.000 colaboradores) podem solicitar um conjunto específico de pacotes de acesso. Outra política especifica que alguns utilizadores de **Utilizadores do parceiro Contoso** (convidados) podem solicitar os mesmos pacotes de acesso sujeitos a aprovação. Contoso tem 30.000 utilizadores. 150 colaboradores solicitam o acesso aos pacotes de acesso e 10.500 utilizadores da Contoso solicitam acesso. | 2.000 colaboradores + 500 utilizadores convidados de Contoso que excedem o rácio 1:5 (10.500 - (2.000 * 5)) | 2.500 |

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Crie o seu primeiro pacote de acesso](entitlement-management-access-package-first.md)
- [Cenários comuns](entitlement-management-scenarios.md)
