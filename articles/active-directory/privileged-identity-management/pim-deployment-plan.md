---
title: Implementar Gestão de Identidade Privilegiada (PIM) - Ad AD Azure | Microsoft Docs
description: Descreve como planear a implantação da Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b1d18982a4f2a9ee8ba585af56a5e9ded7c1c62
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036831"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Implementar gestão de identidade privilegiada da Azure AD (PIM)

Este artigo é um guia passo a passo que descreve como planear a implementação da Gestão de Identidade Privilegiada (PIM) na sua organização Azure Ative Directory (Azure AD). Você irá reatribuir os utilizadores em papéis privilegiados para papéis menos poderosos embutidos ou personalizados, sempre que possível, e planejar atribuições de papéis just-in-time para os seus papéis mais privilegiados. Neste artigo, fazemos recomendações tanto para o planeamento de implantação como para a implementação.

> [!TIP]
> Ao longo deste artigo, verá itens marcados como:
>
> :heavy_check_mark: Microsoft **recomenda**
>
> Estas são recomendações gerais, e você deve implementá-las apenas quando se aplicam às suas necessidades específicas da empresa.

## <a name="licensing-requirements"></a>Requisitos de licenciamento

Para utilizar a Gestão de Identidade Privilegiada, o seu diretório deve ter uma das seguintes licenças pagas ou de julgamento. Para mais informações, consulte [os requisitos da Licença para utilizar a Gestão de Identidade Privilegiada.](subscription-requirements.md)

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 Education A5
- Microsoft 365 Enterprise E5

## <a name="how-pim-works"></a>Como funciona a PIM

Esta secção prevê uma revisão para fins de planeamento das partes relevantes do processo de Gestão de Identidade Privilegiada. Para mais informações, consulte [o que é Azure AD Gestão de Identidade Privilegiada?](pim-configure.md)

1. Comece a utilizar a Gestão de Identidade Privilegiada para que os utilizadores sejam elegíveis para funções privilegiadas.
1. Quando um utilizador elegível precisa de usar o seu papel privilegiado, ativam o papel utilizando a Gestão de Identidade Privilegiada.
1. O utilizador pode ser necessário nas definições para:

    - Use a autenticação de vários fatores
    - Solicitar aprovação para ativação
    - Fornecer uma razão de negócio para a ativação

1. Depois de o utilizador ativar com sucesso o seu papel, terão as permissões de função durante uma duração definida.
1. Os administradores podem ver um histórico de todas as atividades de Gestão de Identidade Privilegiada no registo de auditoria. Também podem garantir ainda mais as suas organizações AD Azure e cumprir o cumprimento usando funcionalidades de Gestão de Identidade Privilegiada, tais como revisões de acesso e alertas.

## <a name="roles-that-can-be-managed-by-pim"></a>Funções que podem ser geridas pela PIM

**As funções de Azure AD** estão todas no Azure Ative Directory (como Administrador Global, Administrador de Câmbio e Administrador de Segurança). Pode ler mais sobre as funções e a sua funcionalidade nas [permissões de funções de Administrador no Azure Ative Directory](../roles/permissions-reference.md). Para obter ajuda para determinar quais as funções a atribuir aos seus administradores, consulte [as funções menos privilegiadas por tarefa](../roles/delegate-by-task.md).

**As funções Azure** são funções que estão ligadas a um recurso Azure, grupo de recursos, subscrição ou grupo de gestão. Pode utilizar o PIM para fornecer acesso just-in-time a funções Azure incorporadas, como Proprietário, Administrador de Acesso ao Utilizador e Colaborador, bem como a [funções personalizadas.](../../role-based-access-control/custom-roles.md) Para obter mais informações sobre os papéis do Azure, consulte [o controlo de acesso baseado em funções da Azure.](../../role-based-access-control/overview.md)

Para mais informações, consulte [Funções que não pode gerir na Gestão de Identidade Privilegiada.](pim-roles.md)

## <a name="deployment-plan"></a>Plano de implementação

Antes de implementar Gestão de Identidade Privilegiada na sua organização, siga as instruções e compreenda os conceitos desta secção para ajudá-lo a criar um plano adaptado aos requisitos de identidade privilegiados da sua organização.

### <a name="identify-your-stakeholders"></a>Identifique os seus stakeholders

A secção seguinte ajuda-o a identificar todas as partes interessadas envolvidas no projeto e precisa de assinar, rever ou manter-se informado. Inclui tabelas separadas para implantação de PIM para funções AZure AD e PIM para funções de Azure. Adicione as partes interessadas à tabela seguinte, conforme apropriado para a sua organização.

- Então = Assine este projeto
- R = Rever este projeto e fornecer informações
- I = Informado deste projeto

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Stakeholders: Gestão privilegiada da identidade para funções de Ad Azure

| Name | Função | Ação |
| --- | --- | --- |
| Nome e e-mail | **Arquiteto de identidade ou Administrador Global da Azure**<br/>Um representante da equipa de gestão de identidade responsável por definir como alinhar esta mudança com a infraestrutura de gestão de identidade central na sua organização. | SO/R/I |
| Nome e e-mail | **Proprietário de serviço / gestor de linha**<br/>Um representante dos proprietários de TI de um serviço ou de um grupo de serviços. São fundamentais para tomar decisões e ajudar a lançar a Gestão de Identidade Privilegiada para a sua equipa. | SO/R/I |
| Nome e e-mail | **Proprietário de segurança**<br/>Um representante da equipa de segurança que pode assinar que o plano cumpre os requisitos de segurança da sua organização. | SO/R |
| Nome e e-mail | **Gestor de suporte de TI / Helpdesk**<br/>Um representante da organização de suporte de TI que pode fornecer feedback sobre a capacidade de apoio desta mudança numa perspetiva de helpdesk. | R/I |
| Nome e e-mail para utilizadores piloto | **Utilizadores privilegiados de funções**<br/>O grupo de utilizadores para o qual é implementada a gestão de identidade privilegiada. Terão de saber como ativar as suas funções assim que a Gestão de Identidade Privilegiada for implementada. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>Stakeholders: Gestão de Identidade Privilegiada para funções de Azure

| Name | Função | Ação |
| --- | --- | --- |
| Nome e e-mail | **Assinatura / Titular de recursos**<br/>Um representante dos proprietários de TI de cada subscrição ou recurso que pretende implementar Gestão de Identidade Privilegiada para | SO/R/I |
| Nome e e-mail | **Proprietário de segurança**<br/>Um representante da equipa de segurança que pode assinar que o plano cumpre os requisitos de segurança da sua organização. | SO/R |
| Nome e e-mail | **Gestor de suporte de TI / Helpdesk**<br/>Um representante da organização de suporte de TI que pode fornecer feedback sobre a capacidade de apoio desta mudança numa perspetiva de helpdesk. | R/I |
| Nome e e-mail para utilizadores piloto | **Utilizadores de funções Azure**<br/>O grupo de utilizadores para o qual é implementada a gestão de identidade privilegiada. Terão de saber como ativar as suas funções assim que a Gestão de Identidade Privilegiada for implementada. | I |

### <a name="start-using-privileged-identity-management"></a>Começar a utilizar o Privileged Identity Management

Como parte do processo de planeamento, deve preparar a Gestão de Identidade Privilegiada seguindo o nosso início usando o artigo [de Gestão de Identidade Privilegiada.](pim-getting-started.md) A Gestão de Identidade Privilegiada dá-lhe acesso a algumas funcionalidades que são projetadas para ajudar na sua implementação.

Se o seu objetivo é implementar Gestão de Identidade Privilegiada para recursos Azure, deve seguir os [nossos recursos da Azure para gerir em artigo de Gestão de Identidade Privilegiada.](pim-resource-roles-discover-resources.md) Apenas os proprietários de subscrições e grupos de gestão podem colocar estes recursos sob gestão pela Gestão de Identidade Privilegiada. Depois de estar sob gestão, a funcionalidade PIM está disponível para proprietários em todos os níveis, incluindo grupo de gestão, subscrição, grupo de recursos e recursos. Se você é um Administrador Global tentando implementar Gestão de Identidade Privilegiada para os seus recursos Azure, você pode [elevar o acesso para gerir todas as subscrições da Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para dar a si mesmo acesso a todos os recursos Azure no diretório para descoberta. No entanto, aconselhamos que obtenha a aprovação de cada um dos seus proprietários de subscrição antes de gerir os seus recursos com a Gestão de Identidade Privilegiada.

### <a name="enforce-principle-of-least-privilege"></a>Impor princípio de menor privilégio

É importante ter a certeza de que impôs o princípio de menor privilégio na sua organização, tanto para o seu AD Azure como para os seus papéis de Azure.

#### <a name="plan-least-privilege-delegation"></a>Plano menos delegação de privilégios

Para as funções de Azure AD, é comum que as organizações atribuam o papel de Administrador Global a um número de administradores quando a maioria dos administradores apenas precisa de um ou dois papéis de administrador específicos e menos poderosos. Com um grande número de Administradores Globais ou outros papéis de alto privilégio, é difícil acompanhar as suas atribuições privilegiadas de perto.

Siga estes passos para implementar o princípio do menor privilégio para as suas funções de AD Azure.

1. Compreender a granularidade dos papéis lendo e compreendendo os [papéis integrados Azure AD disponível.](../roles/permissions-reference.md) Você e a sua equipa também devem referenciar [funções de administrador por tarefa de identidade no AD AZure,](../roles/delegate-by-task.md)o que explica o papel menos privilegiado para tarefas específicas.

1. Lista quem tem papéis privilegiados na sua organização. Pode utilizar a Descoberta de Gestão de Identidade Privilegiada [e insights (pré-visualização)](pim-security-wizard.md) para reduzir a sua exposição.

    ![Página de descobertas e insights (pré-visualização) para reduzir a exposição através de funções privilegiadas](./media/pim-deployment-plan/new-preview-page.png)

1. Para todos os Administradores Globais da sua organização, descubra por que precisam do papel. Em seguida, remova-os da função de Administrador Global e atribua funções incorporadas ou funções personalizadas com menor privilégio dentro do Azure Ative Directory. FYI, a Microsoft tem atualmente apenas cerca de 10 administradores com o papel de Administrador Global. Saiba mais sobre [como a Microsoft utiliza a Gestão de Identidade Privilegiada.](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)

1. Para todas as outras funções da AD Azure, reveja a lista de atribuições, identifique administradores que já não precisem do papel e remova-os das suas atribuições.

Para automatizar os dois últimos passos, pode utilizar comentários de acesso na Gestão de Identidade Privilegiada. Seguindo os passos para [iniciar uma revisão de acesso para as funções de Azure AD em Gestão de Identidade Privilegiada,](pim-how-to-start-security-review.md)pode configurar uma revisão de acesso para cada papel AD Azure que tenha um ou mais membros.

![Crie um painel de revisão de acesso para funções Azure AD](./media/pim-deployment-plan/create-access-review.png)

Desaprove os revisores aos **deputados (auto)**. Todos os utilizadores da função receberão um e-mail pedindo-lhes que confirmem que precisam de acesso. Além disso, ligue **a Ressar na aprovação** nas definições avançadas para que os utilizadores devam indicar por que precisam do papel. Com base nestas informações, pode remover os utilizadores de funções desnecessárias ou delecá-los para funções de administrador mais granular.

As avaliações de acesso dependem de e-mails para notificar as pessoas para reverem o seu acesso às funções. Se tiver contas privilegiadas que não tenham e-mails ligados, certifique-se de preencher o campo de e-mail secundário nessas contas. Para obter mais informações, consulte [o atributo proxyAddresses em Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="plan-azure-resource-role-delegation"></a>Delegação de função de recursos do Plano Azure

Para subscrições e recursos do Azure, pode configurar um processo de revisão de Acesso semelhante para rever as funções em cada subscrição ou recurso. O objetivo deste processo é minimizar as atribuições do Administrador de Acesso ao Proprietário e ao Utilizador anexadas a cada subscrição ou recurso e remover atribuições desnecessárias. No entanto, as organizações delegam frequentemente tais tarefas ao proprietário de cada subscrição ou recurso porque têm uma melhor compreensão das funções específicas (especialmente as funções personalizadas).

Se estiver na função de Administrador Global tentando implementar PIM para funções de Azure na sua organização, pode elevar o [acesso para gerir todas as subscrições do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para ter acesso a cada subscrição. Em seguida, pode encontrar cada proprietário de subscrição e trabalhar com eles para remover atribuições desnecessárias e minimizar a atribuição de funções do proprietário.

Os utilizadores com a função Proprietário para uma subscrição Azure também podem usar [revisões de acesso para recursos Azure](pim-resource-roles-start-access-review.md) para auditar e remover atribuições de funções desnecessárias semelhantes ao processo descrito anteriormente para as funções AZure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Decidir quais atribuições de funções devem ser protegidas pela Gestão de Identidade Privilegiada

Depois de limpar tarefas privilegiadas na sua organização, terá de decidir quais as funções a proteger com Gestão de Identidade Privilegiada.

Se uma função for protegida pela Gestão de Identidade Privilegiada, os utilizadores elegíveis a ela atribuídos devem elevar-se para utilizar os privilégios concedidos pelo papel. O processo de elevação também pode incluir a obtenção de aprovação, usando a autenticação de vários fatores, e fornecendo uma razão para estarem ativando. A Gestão de Identidade Privilegiada também pode acompanhar as elevações através de notificações e os registos de eventos de auditoria da Azure AD.

Escolher quais os papéis a proteger com Gestão de Identidade Privilegiada pode ser difícil e será diferente para cada organização. Esta secção fornece os nossos conselhos de boas práticas para as funções Azure AD e Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

É importante priorizar a proteção dos papéis da AD Azure que têm mais permissões. Com base em padrões de utilização entre todos os clientes privilegiados de Gestão de Identidade, as 10 melhores funções de AD Azure geridas pela Gestão de Identidade Privilegiada são:

1. Administrador global
1. Administrador de segurança
1. Administrador de utilizadores
1. Administrador do Exchange
1. Administrador do SharePoint
1. Administrador do Intune
1. Leitor de segurança
1. Administrador de serviço
1. Administrador de faturação
1. Administrador do Skype para Empresas

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que gere todos os seus Administradores Globais e Administradores de Segurança usando a Gestão de Identidade Privilegiada como um primeiro passo porque são os utilizadores que podem fazer mais mal quando comprometidos.

É importante considerar quais os dados e permissão mais sensíveis para a sua organização. Como exemplo, algumas organizações podem querer proteger a sua função de Administrador de Power BI ou a sua função de Administrador de Equipas usando a Gestão de Identidade Privilegiada, uma vez que podem aceder aos dados e alterar fluxos de trabalho fundamentais.

Se houver algum papel com utilizadores convidados atribuídos, eles são vulneráveis a ataques.

> [!TIP]
> :heavy_check_mark: A Microsoft recomenda que gere todas as funções com utilizadores **convidados** que utilizem a Gestão de Identidade Privilegiada para reduzir o risco associado a contas de utilizadores comprometidas.

Os papéis do leitor como o Leitor de Diretório, O Leitor de Mensagens e o Leitor de Segurança são por vezes considerados menos importantes do que outros papéis porque não têm permissão de escrita. No entanto, temos alguns clientes que também protegem estas funções porque os atacantes com acesso a estas contas podem ser capazes de ler dados sensíveis, como dados pessoais. Tenha em consideração este risco ao decidir se pretende que os papéis dos leitores na sua organização sejam geridos utilizando a Gestão de Identidade Privilegiada.

#### <a name="azure-roles"></a>Funções do Azure

Ao decidir quais as atribuições de funções que devem ser geridas usando a Gestão de Identidade Privilegiada para recurso Azure, deve primeiro identificar as subscrições/recursos que são mais vitais para a sua organização. Exemplos de tais subscrições/recursos são:

- Recursos que acolhem os dados mais sensíveis
- Recursos que core, aplicações voltadas para o cliente dependem

Se você é um Administrador Global com dificuldade em decidir quais subscrições e recursos são mais importantes, você deve contactar os proprietários de subscrições na sua organização para recolher uma lista de recursos geridos por cada subscrição. Em seguida, trabalhe com os proprietários de subscrição para agrupar os recursos com base no nível de gravidade no caso de estarem comprometidos (baixo, médio, alto). Priorize a gestão de recursos com Gestão de Identidade Privilegiada com base neste nível de gravidade.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que trabalhe com os proprietários de subscrições/recursos de serviços críticos para configurar o fluxo de trabalho de Gestão de Identidade Privilegiada para todas as funções dentro de subscrições/recursos sensíveis.

A Gestão de Identidade Privilegiada para recursos Azure suporta contas de serviço com prazos. Deve tratar as contas de serviço exatamente da mesma forma que trataria uma conta de utilizador regular.

Para subscrições/recursos que não sejam tão críticos, não precisará de configurar a Gestão de Identidade Privilegiada para todas as funções. No entanto, deverá ainda proteger as funções de Administrador de Acesso ao Proprietário e ao Utilizador com Gestão de Identidade Privilegiada.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que gere as funções de Proprietário e administrador de acesso ao utilizador de todas as subscrições/recursos utilizando a Gestão de Identidade Privilegiada.

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>Decida se usa um grupo para atribuir funções

A atribuição de um papel a um grupo em vez de a utilizadores individuais é uma decisão estratégica. Ao planear, considere atribuir um papel a um grupo para gerir tarefas de função quando:

- Muitos utilizadores são designados para um papel
- Quer delegar atribuindo o papel

#### <a name="many-users-are-assigned-to-a-role"></a>Muitos utilizadores são designados para um papel

Acompanhar quem é designado para um papel e gerir as suas atribuições com base na sua necessidade pode levar tempo quando feito manualmente. Para atribuir um grupo a um papel, primeiro [criar um grupo atribuível](../roles/groups-create-eligible.md) e, em seguida, atribuir o grupo como elegível para um papel. Esta ação sujeita todos os membros do grupo ao mesmo processo de ativação que os utilizadores individuais que são elegíveis para elevar para o papel. Os membros do grupo ativam as suas atribuições ao grupo individualmente utilizando o pedido de ativação e o processo de aprovação da Gestão de Identidade Privilegiada. O grupo não está ativado, apenas a filiação do grupo do utilizador.

#### <a name="you-want-to-delegate-assigning-the-role"></a>Quer delegar atribuindo o papel

Um dono de grupo pode gerir a adesão a um grupo. Para os grupos azure AD atribuíveis, apenas o Administrador de Função Privilegiado, o Administrador Global e os proprietários do grupo podem gerir a adesão ao grupo. Ao adicionar novos membros ao grupo, o membro obtém acesso às funções a que o grupo é atribuído se a atribuição é elegível ou ativa. Utilize os proprietários do grupo para delegar a gestão da filiação do grupo para um papel atribuído para reduzir a amplitude de privilégios exigida. Para obter mais informações sobre a atribuição de um proprietário a um grupo ao criar o grupo, consulte [Criar um grupo atribuível a papéis em Azure AD](../roles/groups-create-eligible.md).

> [!TIP]
> :heavy_check_mark: A Microsoft recomenda que leve os **grupos** azure a atribuir funções sob gestão pela Privileged Identity Management. Depois de um grupo atribuível a papéis ser gerido pela PIM, é chamado de grupo de acesso privilegiado. Utilize o PIM para exigir que os proprietários do grupo ativem a sua atribuição de funções de Proprietário antes de poderem gerir a adesão ao grupo. Para obter mais informações sobre a gestão de grupos sob gestão pim, consulte [Trazer grupos de acesso privilegiados (pré-visualização) para a Gestão de Identidade Privilegiada.](groups-discover-groups.md)

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Decidir quais as atribuições de funções devem ser permanentes ou elegíveis

Uma vez que tenha decidido a lista de funções a gerir pela Gestão de Identidade Privilegiada, deve decidir quais os utilizadores que devem obter o papel elegível contra o papel permanentemente ativo. As funções ativas permanentes são as funções normais atribuídas através do Azure Ative Directory e dos recursos Azure, enquanto as funções elegíveis só podem ser atribuídas na Gestão de Identidade Privilegiada.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que tenha zero atribuições permanentemente ativas para as funções AD do Azure e para as funções Azure, para além das duas contas de acesso de emergência recomendadas para [o break-glass](../roles/security-emergency-access.md), que devem ter o papel de Administrador Global permanente.

Apesar de recomendarmos zero administrador permanente, por vezes é difícil para as organizações conseguir isso imediatamente. Aqui estão as coisas a ter em conta ao tomar esta decisão:

- Frequência de elevação – Se o utilizador só precisa da atribuição privilegiada uma vez, não deve ter a atribuição permanente. Por outro lado, se o utilizador necessitar do papel para o seu trabalho diário e utilizar a Gestão da Identidade Privilegiada reduziria consideravelmente a sua produtividade, pode ser considerado para o papel permanente.
- Casos específicos da sua organização – Se a pessoa que recebe o papel elegível for de uma equipa distante ou de um executivo de alto escalão ao ponto de comunicar e impor o processo de elevação é difícil, podem ser considerados para o papel permanente.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda-lhe** que crie avaliações de acesso recorrentes para utilizadores com atribuições de funções permanentes (caso tenha alguma). Saiba mais sobre a revisão de acesso recorrente na secção final deste plano de implantação

### <a name="draft-your-privileged-identity-management-settings"></a>Desabore as suas definições de Gestão de Identidade Privilegiada

Antes de implementar a sua solução de Gestão de Identidade Privilegiada, é uma boa prática elaborar as suas definições de Gestão de Identidade Privilegiada para cada papel privilegiado que a sua organização utiliza. Esta secção tem alguns exemplos de definições de Gestão de Identidade Privilegiada para funções específicas (são apenas para referência e podem ser diferentes para a sua organização). Cada uma destas definições é explicada em detalhe com as recomendações da Microsoft após as tabelas.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Definições privilegiadas de Gestão de Identidade para funções AZURE AD

| Função | Requerer MFA | Notificação | Bilhete de incidente | Requerer aprovação | Aprovador | Duração da ativação | Administrador permanente |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrador Global | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros Administradores Globais | 1 Hora | Contas de acesso de emergência |
| Administrador de Intercâmbio | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Nenhum | 2 Horas | Nenhum |
| Administrador helpdesk | :x: | :x: | :heavy_check_mark: | :x: | Nenhum | 8 Horas | Nenhum |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Definições privilegiadas de Gestão de Identidade para funções Azure

| Função | Requerer MFA | Notificação | Requerer aprovação | Aprovador | Duração da ativação | Administrador ativo | Expiração ativa | Expiração elegível |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Proprietário de subscrições críticas | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros proprietários da subscrição | 1 Hora | Nenhum | n/a | 3 meses |
| Administrador de Acesso ao Utilizador de subscrições menos críticas | :heavy_check_mark: | :heavy_check_mark: | :x: | Nenhum | 1 Hora | Nenhum | n/a | 3 meses |
| Contribuidor de Máquina Virtual | :x: | :heavy_check_mark: | :x: | Nenhum | 3 Horas | Nenhum | n/a | 6 meses |

A tabela seguinte descreve cada uma das definições.

| Definições | Descrição |
| --- | --- |
| Função | Nome do papel para o que está a definir. |
| Requerer MFA | Se o utilizador elegível precisa de realizar MFA antes de ativar a função.<br/><br/> :heavy_check_mark: **A Microsoft recomenda** que imponha MFA para todas as funções de administrador, especialmente se as funções tiverem utilizadores convidados. |
| Notificação | Se for definido como verdadeiro, o Administrador Global, o Administrador de Função Privilegiada e o Administrador de Segurança na organização receberão uma notificação de e-mail quando um utilizador elegível ativar a função.<br/><br/>**Nota:** Algumas organizações não têm um endereço de e-mail ligado às suas contas de administrador, para receber estas notificações de e-mail, você deve ir definir um endereço de e-mail alternativo para que os administradores recebam esses e-mails. |
| Bilhete de incidente | Se o utilizador elegível precisa de registar um número de bilhete de incidente ao ativar a sua função. Esta definição ajuda uma organização a identificar cada ativação com um número de incidente interno para mitigar ativações indesejadas.<br/><br/> :heavy_check_mark: A Microsoft recomenda aproveitar os números de **bilhetes** de incidente para ligar a Gestão de Identidade Privilegiada ao seu sistema interno. Este método pode ser útil para os aprovadores que precisam de contexto para a ativação. |
| Requerer aprovação | Se o utilizador elegível precisa de obter aprovação para ativar a função.<br/><br/> :heavy_check_mark: A **Microsoft recomenda-lhe** que estabeleça aprovação para funções com mais permissão. Com base nos padrões de utilização de todos os clientes privilegiados de Gestão de Identidade, Administrador Global, Administrador de Utilizador, Administrador de Câmbio, Administrador de Segurança e Administrador de Password são as funções mais comuns com a configuração de aprovação. |
| Aprovador | Se for necessária a aprovação para ativar a função elegível, esliste as pessoas que devem aprovar o pedido. Por padrão, a Gestão de Identidade Privilegiada define o aprovador como todos os utilizadores que sejam um administrador privilegiado, sejam eles permanentes ou elegíveis.<br/><br/>**Nota:** Se um utilizador for elegível para um papel AD Azure e um aprovador do papel, não será capaz de se aprovar.<br/><br/> :heavy_check_mark: A Microsoft recomenda que escolha os aprovadores para serem utilizadores mais conhecedores do papel e dos seus utilizadores **frequentes** em vez de um Administrador Global. |
| Duração da ativação | O tempo de tempo que um utilizador será ativado na função antes de expirar. |
| Administrador permanente | Lista de utilizadores que serão administradores permanentes para o papel (nunca têm de ser ativados).<br/><br/> :heavy_check_mark: **A Microsoft recomenda** que não tenha administrador permanente zero para todas as funções, exceto para administradores globais. Leia mais sobre o que deve ser elegível e quem deve ser permanentemente ativo nesta secção deste plano. |
| Administrador ativo | Para os recursos Azure, o administrador ativo é a lista de utilizadores que nunca terão de ativar para usar o papel. Esta lista não é referida como administrador permanente como nas funções Azure AD porque pode definir um prazo de validade para quando o utilizador perder esta função. |
| Expiração ativa | As atribuições de funções ativas para funções Azure expiram após a duração configurada. Pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente ativo. |
| Expiração elegível | As atribuições de funções elegíveis para funções Azure expiram após esta duração. Pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente elegível. |

## <a name="implementation-plan"></a>Plano de implementação

A base de planeamento adequado é a base na qual pode implementar uma aplicação com sucesso com o Azure Ative Directory.  Proporciona uma segurança e integração inteligentes que simplifica o embarque, reduzindo o tempo para implementações bem sucedidas.  Esta combinação garante que a sua aplicação está integrada com facilidade, ao mesmo tempo que atenua o tempo de mente para os seus utilizadores finais.

### <a name="identify-test-users"></a>Identificar utilizadores de teste

Utilize esta secção para identificar um conjunto de utilizadores e grupos de utilizadores para validar a implementação. Com base nas definições que selecionou na secção de planeamento, identifique os utilizadores que pretende testar para cada função.

> [!TIP]
> :heavy_check_mark: A Microsoft recomenda que faça com que os proprietários de **serviços** de cada papel AD do Azure sejam os utilizadores de teste para que possam familiarizar-se com o processo e tornar-se um defensor interno do lançamento.

Nesta tabela, identifique os utilizadores de teste que verificarão se as definições para as funções estão a funcionar.

| Nome da função | Utilizadores de teste |
| --- | --- |
| &lt;Nome da função&gt; | &lt;Utilizadores para testar o papel&gt; |
| &lt;Nome da função&gt; | &lt;Utilizadores para testar o papel&gt; |

### <a name="test-implementation"></a>Implementação do teste

Agora que identificou os utilizadores do teste, use este passo para configurar a Gestão de Identidade Privilegiada para os seus utilizadores de teste. Se a sua organização pretende incorporar o fluxo de trabalho de Gestão de Identidade Privilegiada na sua própria aplicação interna em vez de utilizar a Gestão de Identidade Privilegiada no portal Azure, todas as operações em Gestão de Identidade Privilegiada também são suportadas através do nosso [gráfico API.](/graph/api/resources/privilegedidentitymanagement-root)

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Configure Gestão de Identidade Privilegiada para funções de AD Azure

1. [Configure as definições de funções AD Azure](pim-how-to-change-default-settings.md) com base no que planeou.

1. Navegue para **funções AD Azure**, selecione **Roles**, e, em seguida, selecione o papel que configura.

1. Para o grupo de utilizadores de teste, se já forem administradores permanentes, pode torná-los elegíveis, procurando-os e convertendo-os de permanente para elegível selecionando os três pontos na sua linha. Se ainda não tiverem as funções, pode [fazer uma nova missão elegível.](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)

1. Repita os passos 1-3 para todas as funções que pretende testar.

1. Depois de configurar os utilizadores de teste, deve enviar-lhes o link para como ativar a [sua função AD Azure](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>Configure Gestão de Identidade Privilegiada para funções de Azure

1. [Configure as definições de função de recurso Azure](pim-resource-roles-configure-role-settings.md) para uma função dentro de uma subscrição ou recurso que pretende testar.

1. Navegue para **os recursos Azure** para essa subscrição e selecione **Roles,** selecione a função que configura.

1. Para o grupo de utilizadores de teste, se já forem um administrador ativo, pode torná-los elegíveis procurando-os e [atualizando a sua atribuição de funções](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Se ainda não tiverem o papel, pode [atribuir um novo papel.](pim-resource-roles-assign-roles.md#assign-a-role)

1. Repita os passos 1-3 para todas as funções que pretende testar.

1. Depois de configurar os utilizadores de teste, deve enviar-lhes o link para como ativar a [sua função de recurso Azure](pim-resource-roles-activate-your-roles.md).

Deve utilizar esta fase para verificar se toda a configuração configurada para as funções está a funcionar corretamente. Utilize a seguinte tabela para documentar os seus testes. Também deve utilizar esta fase para otimizar a comunicação com os utilizadores afetados.

| Função | Comportamento esperado durante a ativação | Resultados reais |
| --- | --- | --- |
| Administrador Global | (1) Requerem MFA<br/>(2) Requer aprovação<br/>(3) O aprovador recebe a notificação e pode aprovar<br/>(4) A função expira após o tempo predefinido |  |
| Proprietário da subscrição *X* | (1) Requerem MFA<br/>(2) A atribuição elegível expira após o período de tempo configurado |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Comunicar Gestão de Identidade Privilegiada às partes interessadas

Implementar a Gestão de Identidade Privilegiada introduzirá medidas adicionais para os utilizadores de funções privilegiadas. Embora a Gestão de Identidade Privilegiada reduza consideravelmente as questões de segurança associadas a identidades privilegiadas, a mudança precisa de ser eficazmente comunicada antes da implantação em toda a organização. Dependendo do número de administradores impactados, as organizações muitas vezes optam por criar um documento interno, um vídeo ou um e-mail sobre a mudança. Frequentemente incluídas nestas comunicações incluem:

- O que é PIM
- Qual é o benefício para a organização
- Quem será afetado
- Quando é que o PIM será lançado
- Que medidas adicionais serão necessárias para que os utilizadores ativem o seu papel
    - Deverá enviar links para a nossa documentação:
    - [Ativar funções AD AZure](pim-how-to-activate-role.md)
    - [Ativar funções Azure](pim-resource-roles-activate-your-roles.md)
- Informações de contacto ou ligação helpdesk para quaisquer problemas associados à PIM

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda-lhe** que concorra tempo com a sua equipa de apoio/ajuda para os acompanhar através do fluxo de trabalho de Gestão de Identidade Privilegiada (se a sua organização tiver uma equipa interna de suporte de TI). Forneça-lhes as documentações apropriadas, bem como as suas informações de contacto.

### <a name="move-to-production"></a>Mover para produção

Assim que o seu teste estiver completo e bem sucedido, mova a Gestão de Identidade Privilegiada para a produção, repetindo todos os passos nas fases de teste para todos os utilizadores de cada papel que definiu na sua configuração de Gestão de Identidade Privilegiada. Para a Gestão de Identidade Privilegiada para funções de Ad Azure, as organizações muitas vezes testam e lançam Gestão de Identidade Privilegiada para Administradores Globais antes de testar e lançar Gestão de Identidade Privilegiada para outras funções. Entretanto, para o recurso Azure, as organizações normalmente testam e lançam uma subscrição da Privileged Identity Management one Azure de cada vez.

### <a name="if-a-rollback-is-needed"></a>Se for necessário um revés

Se a Gestão de Identidade Privilegiada não funcionar como desejado no ambiente de produção, as seguintes medidas de retrocesso podem ajudá-lo a voltar a um bom estado conhecido antes de criar a Gestão de Identidade Privilegiada:

#### <a name="azure-ad-roles"></a>Funções do Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Open **Azure AD Gestão de Identidade Privilegiada.**
1. Selecione **as funções AD do Azure** e, em seguida, selecione **Funções**.
1. Para cada função que configurar, selecione a elipse **(...**) para todos os utilizadores com uma atribuição elegível.
1. Selecione a opção **Fazer permanente** para tornar a atribuição de funções permanente.

#### <a name="azure-roles"></a>Funções do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Open **Azure AD Gestão de Identidade Privilegiada.**
1. Selecione **recursos Azure** e, em seguida, selecione uma subscrição ou recurso que pretende reverter.
1. Selecione **Funções**.
1. Para cada função que configurar, selecione a elipse **(...**) para todos os utilizadores com uma atribuição elegível.
1. Selecione a opção **Fazer permanente** para tornar a atribuição de funções permanente.

## <a name="next-steps-after-deploying"></a>Próximos passos após a implantação

Implementar com sucesso a Gestão de Identidade Privilegiada na produção é um passo significativo em termos de garantia das identidades privilegiadas da sua organização. Com a implementação da Gestão de Identidade Privilegiada surgem funcionalidades adicionais de Gestão de Identidade Privilegiada que deve utilizar para segurança e conformidade.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Use alertas de Gestão de Identidade Privilegiada para salvaguardar o seu acesso privilegiado

Para obter mais informações sobre a utilização da funcionalidade de alerta integrado da Gestão de Identidade Privilegiada para salvaguardar a sua organização, consulte [alertas de segurança.](pim-how-to-configure-security-alerts.md#security-alerts) Estes alertas incluem: os administradores não estão a usar funções privilegiadas, as funções estão a ser atribuídas fora da Gestão de Identidade Privilegiada, as funções estão a ser ativadas com demasiada frequência e mais. Para proteger totalmente a sua organização, deve analisar regularmente a sua lista de alertas e corrigir os problemas. Pode ver e corrigir os seus alertas da seguinte forma:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Open **Azure AD Gestão de Identidade Privilegiada.**
1. Selecione **as funções AD do Azure** e, em seguida, selecione **Alertas**.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que lide imediatamente com todos os alertas marcados com alta gravidade. Para alertas de severidade média e baixa, deve manter-se informado e fazer alterações se acreditar que existe uma ameaça à segurança.

Se algum dos alertas específicos não for útil ou não se aplicar à sua organização, pode sempre descartar o alerta na página de alertas. Pode sempre reverter este despedimento mais tarde na página de definições AZure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Configurar avaliações de acesso recorrentes para auditar regularmente as identidades privilegiadas da sua organização

As avaliações de acesso são a melhor forma de perguntar aos utilizadores designados com funções privilegiadas ou revisores específicos se cada utilizador precisa da identidade privilegiada. As avaliações de acesso são ótimas se quiser reduzir a superfície de ataque e manter-se em conformidade. Para obter mais informações sobre o início de uma revisão de acesso, consulte [as funções AD do Azure que acedem a avaliações](pim-how-to-start-security-review.md) de acesso a [avaliações](pim-resource-roles-start-access-review.md)de acesso a peças . Para algumas organizações, a realização de uma revisão periódica de acesso é necessária para se manter em conformidade com as leis e regulamentos enquanto para outras, a revisão de acesso é a melhor maneira de impor o principal de menor privilégio em toda a sua organização.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que crie avaliações trimestrais de acesso para todas as suas funções AD e Azure.

Na maioria dos casos, o revisor para as funções de Azure AD são os próprios utilizadores, enquanto o revisor para as funções Azure é o proprietário da subscrição, em que o papel está. No entanto, muitas vezes é o caso em que as empresas têm contas privilegiadas que não estão ligadas ao endereço de e-mail de qualquer pessoa em particular. Nesses casos, ninguém lê e revê o acesso.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que adicione um endereço de e-mail secundário para todas as contas com atribuições de funções privilegiadas que não estejam ligadas a um endereço de e-mail regularmente verificado

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Obtenha o máximo do seu registo de auditoria para melhorar a segurança e a conformidade

O registo de auditoria é o local onde pode manter-se atualizado e estar em conformidade com os regulamentos. A Gestão de Identidade Privilegiada armazena atualmente uma história de 30 dias de toda a história da sua organização dentro do seu registo de auditoria, incluindo:

- Ativação/desativação de funções elegíveis
- Atividades de atribuição de funções dentro e fora da Gestão de Identidade Privilegiada
- Alterações nas definições de funções
- Solicitação/aprovação/negação de atividades para ativação de funções com configuração de aprovação
- Atualizar para alertas

Pode aceder aos registos de auditoria se for administrador global ou administrador privilegiado. Para obter mais informações, consulte [o histórico de auditoria para as funções de Azure AD](pim-how-to-use-audit-log.md) e o histórico de auditoria para as [funções de Azure](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que tenha pelo menos um administrador a ler todos os eventos de auditoria semanalmente e a exportar os seus eventos de auditoria mensalmente.

Se pretender armazenar automaticamente os seus eventos de auditoria por um período mais longo, o registo de auditoria da Gestão de Identidade Privilegiada é automaticamente sincronizado nos registos de auditoria da [AD Azure](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: A Microsoft recomenda que crie [monitorização de registos Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) para arquivar eventos de auditoria numa conta de armazenamento Azure para uma maior segurança e conformidade. 
