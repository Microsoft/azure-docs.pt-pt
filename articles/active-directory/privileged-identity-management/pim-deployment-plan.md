---
title: Implementação de Gestão de Identidade Privilegiada (PIM) - Azure AD / Microsoft Docs
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
ms.date: 02/04/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8c77b3454026aa309d979bd938674e7c3ae7b6a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026001"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Implementar a Gestão de Identidade Privilegiada da Azure AD (PIM)

Este guia passo a passo descreve como planear a implantação da Gestão de Identidade Privilegiada (PIM) na sua organização Azure Ative Directory (Azure AD).

> [!TIP]
> Ao longo deste artigo, verá itens marcados como:
> 
> :heavy_check_mark: **A Microsoft recomenda**
> 
> Estas são recomendações gerais, e você só deve implementar se elas se aplicarem às suas necessidades específicas da empresa.

## <a name="learn-about-privileged-identity-management"></a>Conheça a Gestão de Identidade Privilegiada

A Azure AD Privileged Identity Management ajuda-o a gerir funções administrativas privilegiadas em toda a Azure AD, recursos Azure e outros Serviços Online da Microsoft. Num mundo onde identidades privilegiadas são atribuídas e esquecidas, a Privileged Identity Management fornece soluções como acesso just-in-time, solicitação de fluxos de trabalho de aprovação e avaliações de acesso totalmente integradas para que possa identificar, descobrir e prevenir mal-intencionados atividades de papéis privilegiados em tempo real. Implementar a Gestão de Identidade Privilegiada para gerir os seus papéis privilegiados em toda a sua organização reduzirá consideravelmente o risco, ao mesmo tempo que surgirá informações valiosas sobre as atividades dos seus papéis privilegiados.

### <a name="business-value-of-privileged-identity-management"></a>Valor empresarial da Gestão de Identidade Privilegiada

**Gerir o risco** - Proteja a sua organização aplicando o princípio do [menor acesso privilegiado](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e acesso just-in-time. Ao minimizar o número de atribuições permanentes dos utilizadores a papéis privilegiados e aplicação de aprovações e MFA para elevação, pode reduzir consideravelmente os riscos de segurança relacionados com o acesso privilegiado na sua organização. A aplicação do menor privilégio e acesso just-in-time também lhe permitirá ver um histórico de acesso a papéis privilegiados e rastrear questões de segurança à medida que elas acontecem.

Abordar o **cumprimento e a governação** - A implementação da Gestão de Identidade Privilegiada cria um ambiente para a governação de identidade em curso. A elevação justa-in-time de identidades privilegiadas fornece uma forma de a Gestão de Identidade Privilegiada acompanhar as atividades de acesso privilegiados na sua organização. Poderá também visualizar e receber notificações para todas as atribuições de funções permanentes e elegíveis dentro da sua organização. Através da revisão de acesso, pode regularmente auditar e remover identidades privilegiadas desnecessárias e certificar-se de que a sua organização está em conformidade com as normas de identidade, acesso e segurança mais rigorosas.

**Reduzir custos** - Reduzir custos eliminando ineficiências, erros humanos e questões de segurança, implementando corretamente a Gestão de Identidade Privilegiada. O resultado líquido é a redução dos crimes cibernéticos associados a identidades privilegiadas, que são dispendiosas e difíceis de recuperar. A Gestão de Identidade Privilegiada também ajudará a sua organização a reduzir os custos associados à auditoria de informação de acesso no que diz respeito ao cumprimento de regulamentos e padrões.

Para obter mais informações, consulte [o que é o Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Requisitos de licenciamento

Para utilizar a Gestão de Identidade Privilegiada, o seu diretório deve ter uma das seguintes licenças pagas ou experimentais:

- Azure AD Premium P2
- Mobilidade Empresarial + Segurança (EMS) E5
- Microsoft 365 M5

Para mais informações, consulte [os requisitos de Licença para utilizar a Gestão de Identidade Privilegiada.](subscription-requirements.md)

### <a name="key-terminology"></a>Terminologia chave

| Termo ou conceito | Descrição |
| --- | --- |
| elegível | Uma atribuição de funções que requer que um utilizador realize uma ou mais ações para usar o papel. Se um utilizador tiver sido elegível para um papel, isso significa que pode ativar a função quando precisa de executar tarefas privilegiadas. Não há diferença no acesso dado a alguém com um papel permanente contra um papel elegível. A única diferença é que algumas pessoas não precisam de ter acesso a toda a hora. |
| ativar | O processo de realização de uma ou mais ações para utilizar uma função para a qual um utilizador é elegível. As ações podem incluir a execução de uma verificação de MFA (autenticação multifator), o fornecimento de uma justificativa de negócios ou a solicitação de aprovação de aprovadores designados. |
| acesso just-in-time (JIT) | Um modelo no qual os utilizadores recebem permissões temporárias para executar tarefas privilegiadas, o que impede que utilizadores maliciosos ou não autorizados tenham acesso após o termo das permissões. O acesso só é concedido quando os utilizadores precisam. |
| princípio de acesso menos privilegiado | Uma prática de segurança recomendada na qual cada utilizador é dotado apenas dos privilégios mínimos necessários para realizar as tarefas que está autorizado a executar. Esta prática minimiza o número de Administradores Globais e, em vez disso, utiliza funções específicas de administrador para determinados cenários. |

Para mais informações, consulte [terminologia.](pim-configure.md#terminology)

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Visão geral de alto nível de como funciona a Gestão privilegiada da identidade

1. A Gestão de Identidade Privilegiada é criada para que os utilizadores sejam elegíveis para funções privilegiadas.
1. Quando um utilizador elegível precisa de usar o seu papel privilegiado, ativa o papel na Gestão de Identidade Privilegiada.
1. Dependendo das definições de Gestão de Identidade Privilegiada configuradas para o papel, o utilizador deve completar determinados passos (tais como a autenticação de vários fatores, obter aprovação ou especificar uma razão.)
1. Assim que o utilizador ativar com sucesso a sua função, obterá o papel por um período de tempo pré-configurado.
1. Os administradores podem ver um histórico de todas as atividades de Gestão de Identidade Privilegiada no registo de auditoria. Também podem garantir as suas organizações de AD Azure e cumprir o cumprimento usando funcionalidades de Gestão de Identidade Privilegiada, como avaliações de acesso e alertas.

Para obter mais informações, consulte [o que é o Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Funções que podem ser geridas pela Privileged Identity Management

**Funções Azure AD** – Estas funções estão todas no Diretório Ativo azure (como Administrador Global, Administrador de Intercâmbio e Administrador de Segurança). Pode ler mais sobre as funções e a sua funcionalidade em autorizações de [papel de Administrador no Diretório Ativo do Azure](../users-groups-roles/directory-assign-admin-roles.md). Para ajudar na determinação das funções a atribuir aos seus administradores, consulte [funções menos privilegiadas por tarefa](../users-groups-roles/roles-delegate-by-task.md).

**Funções** de recurso Azure – Estas funções estão ligadas a um grupo de recursos, recursos, subscrição ou grupo de gestão azure. A Privileged Identity Management proporciona acesso just-in-time a ambas as funções incorporadas como Owner, User Access Administrator e Contributor, bem como [funções personalizadas.](../../role-based-access-control/custom-roles.md) Para obter mais informações sobre as funções de recurso Azure, consulte [o controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md).

Para mais informações, consulte [Funções que não pode gerir na Gestão](pim-roles.md)de Identidade Privilegiada.

## <a name="plan-your-deployment"></a>Planeie a sua implementação

Esta secção centra-se no que precisa de fazer antes de implementar a Gestão de Identidade Privilegiada na sua organização. É essencial seguir as instruções e compreender os conceitos nesta secção, pois irão guiá-lo para criar o melhor plano adaptado para as identidades privilegiadas da sua organização.

### <a name="identify-your-stakeholders"></a>Identifique as suas partes interessadas

A secção seguinte ajuda-o a identificar todas as partes interessadas envolvidas no projeto e precisa assinar, rever ou manter-se informado. Inclui tabelas separadas para a implementação de gestão de identidade privilegiada para funções de AD Azure e Gestão de Identidade Privilegiada para funções de recursos Azure. Adicione as partes interessadas à tabela seguinte, conforme apropriado para a sua organização.

- Então = Assine este projeto
- R = Rever este projeto e fornecer entrada
- I = Informado deste projeto

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Stakeholders: Gestão privilegiada de identidade para funções de AD Azure

| Nome | Função | Ação |
| --- | --- | --- |
| Nome e e-mail | **Arquiteto de identidade ou Administrador Global Azure**<br/>Um representante da equipa de gestão de identidade responsável pela definição de como esta mudança está alinhada com a principal infraestrutura de gestão de identidade na sua organização. | SO/R/I |
| Nome e e-mail | **Proprietário de serviço / Gestor de linha**<br/>Um representante dos proprietários de TI de um serviço ou de um grupo de serviços. São fundamentais para tomar decisões e ajudar a lançar a Gestão de Identidade Privilegiada para a sua equipa. | SO/R/I |
| Nome e e-mail | **Proprietário de segurança**<br/>Um representante da equipa de segurança que pode assinar que o plano cumpre os requisitos de segurança da sua organização. | SO/R |
| Nome e e-mail | **Gestor de suporte de TI / Helpdesk**<br/>Um representante da organização de apoio de TI que pode fornecer informações sobre a capacidade de suporte desta mudança de uma perspetiva de helpdesk. | R/I |
| Nome e e-mail para utilizadores-piloto | **Utilizadores privilegiados**<br/>O grupo de utilizadores para os quais é implementada uma gestão privilegiada da identidade. Terão de saber como ativar as suas funções assim que a Gestão de Identidade Privilegiada for implementada. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Stakeholders: Gestão privilegiada da identidade para funções de recursos Azure

| Nome | Função | Ação |
| --- | --- | --- |
| Nome e e-mail | **Subscrição / Proprietário de recursos**<br/>Um representante dos proprietários de TI de cada subscrição ou recurso que pretende implementar Gestão de Identidade Privilegiada para | SO/R/I |
| Nome e e-mail | **Proprietário de segurança**<br/>Um representante da equipa de segurança que pode assinar que o plano cumpre os requisitos de segurança da sua organização. | SO/R |
| Nome e e-mail | **Gestor de suporte de TI / Helpdesk**<br/>Um representante da organização de apoio de TI que pode fornecer informações sobre a capacidade de suporte desta mudança de uma perspetiva de helpdesk. | R/I |
| Nome e e-mail para utilizadores-piloto | **Utilizadores de funções RBAC**<br/>O grupo de utilizadores para os quais é implementada uma gestão privilegiada da identidade. Terão de saber como ativar as suas funções assim que a Gestão de Identidade Privilegiada for implementada. | I |

### <a name="enable-privileged-identity-management"></a>Ativar a Gestão de Identidade Privilegiada

Como parte do processo de planeamento, você deve primeiro consentir e ativar a Gestão de Identidade Privilegiada, seguindo o nosso início usando o artigo de Gestão de [Identidade Privilegiada.](pim-getting-started.md) Permitir a Gestão de Identidade Privilegiada dá-lhe acesso a algumas funcionalidades que são especificamente concebidas para ajudar na sua implementação.

Se o seu objetivo é implementar a Gestão de Identidade Privilegiada para os recursos Azure, deve seguir os nossos [recursos do Azure para gerir em](pim-resource-roles-discover-resources.md) artigo de Gestão de Identidade Privilegiada. Apenas os proprietários de subscrições e grupos de gestão podem descobrir e embarcar esses recursos na Gestão de Identidade Privilegiada. Depois de a bordo, a funcionalidade PIM está disponível para proprietários em todos os níveis, incluindo grupo de gestão, subscrição, grupo de recursos e recursos. Se é um Administrador Global que tenta implementar a Gestão de Identidade Privilegiada para os seus recursos Azure, pode [elevar o acesso a gerir todas as subscrições do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para se dar acesso a todos os recursos do Azure no diretório para descoberta. No entanto, aconselhamos que obtenha a aprovação de cada um dos seus proprietários de subscrições antes de gerir os seus recursos com a Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Impor o princípio do menor privilégio

É importante garantir que impôs o princípio do menor privilégio na sua organização tanto para o seu Azure AD como para os seus recursos Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

Para as funções da AD Azure, é comum que as organizações atribuam o papel de Administrador Global a um número significativo de administradores quando a maioria dos administradores apenas precisa de uma ou duas funções específicas de administrador. As atribuições de papéis privilegiados também tendem a ser deixadas sem gestão.

> [!NOTE]
> Armadilhas comuns na delegação de funções:
>
> - O administrador responsável pela Exchange tem o papel de Administrador Global, embora só precisem do papel de Administrador de Câmbio para desempenhar o seu trabalho diário.
> - A função de Administrador Global é atribuída a um administrador do Office porque o administrador precisa tanto das funções de administrador de Segurança como de SharePoint e é mais fácil apenas delegar uma função.
> - O administrador foi designado para desempenhar uma tarefa há muito tempo, mas nunca foi removido.

Siga estes passos para impor o princípio do menor privilégio para as suas funções de AD Azure.

1. Compreender a granularidade das funções lendo e compreendendo as funções de administrador da [AD Azure disponível.](../users-groups-roles/directory-assign-admin-roles.md#available-roles) Você e a sua equipa também devem referir funções de [administrador por tarefa de identidade no Azure AD,](../users-groups-roles/roles-delegate-by-task.md)o que explica o papel menos privilegiado para tarefas específicas.

1. Lista que tem papéis privilegiados na sua organização. Pode utilizar o assistente de [Gestão de Identidade Privilegiada](pim-security-wizard.md#run-the-wizard) para chegar a uma página como a seguinte.

    ![Descubra papéis privilegiados que mostram quem tem papéis privilegiados](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Para todos os Administradores Globais da sua organização, descubra por que precisam do papel. Com base na leitura da documentação anterior, se o trabalho da pessoa puder ser desempenhado por uma ou mais funções de administrador granular, deve removê-las do papel de Administrador Global e efetuar atribuições em conformidade dentro do Diretório Ativo azure (como referência: A Microsoft conta atualmente apenas com cerca de 10 administradores com o papel de Administrador Global. Saiba mais sobre [como a Microsoft utiliza a Gestão de Identidade Privilegiada).](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)

1. Para todas as outras funções da Azure AD, reveja a lista de atribuições, identifique os administradores que já não precisam do papel e remova-os das suas atribuições.

Para automatizar os dois últimos passos, pode utilizar avaliações de acesso em Gestão de Identidade Privilegiada. Seguindo os passos no início de uma revisão de [acesso para funções de AD Azure na Gestão de Identidade Privilegiada,](pim-how-to-start-security-review.md)pode configurar uma revisão de acesso para cada função azure AD que tenha um ou mais membros.

![Criar um painel de revisão de acesso para funções da AD Azure](./media/pim-deployment-plan/create-access-review.png)

Deve definir os revisores para **Deputados (eu)** . Isto enviará um e-mail a todos os membros do papel para que confirmem se precisam ou não do acesso. Também deve ativar **Exigir razão na aprovação** nas definições avançadas para que os utilizadores possam indicar por que precisam da função. Com base nesta informação, poderá remover os utilizadores de funções desnecessárias e delegar papéis de administrador mais granular no caso dos Administradores Globais.

As avaliações de acesso dependem de e-mails para notificar as pessoas para rever em seu acesso às funções. Se tem contas privilegiadas que não têm e-mails ligados, certifique-se de preencher o campo de e-mail secundário nessas contas. Para mais informações, consulte [proxyAddresss atribui-se em Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Funções de recursos do Azure

Para subscrições e recursos Azure, pode configurar um processo de revisão do Acesso semelhante para rever as funções em cada subscrição ou recurso. O objetivo deste processo é minimizar as atribuições do Proprietário e do Administrador de Acesso ao Utilizador anexadas a cada subscrição ou recurso, bem como remover atribuições desnecessárias. No entanto, as organizações frequentemente delegam tais tarefas ao proprietário de cada subscrição ou recurso porque têm uma melhor compreensão das funções específicas (especialmente papéis personalizados).

Se é um administrador de TI com o papel de Administrador Global que tenta implementar a Gestão de Identidade Privilegiada para os recursos do Azure na sua organização, pode [elevar o acesso a gerir todas as subscrições do Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) para ter acesso a cada subscrição. Em seguida, pode encontrar cada proprietário de subscrição e trabalhar com eles para remover atribuições desnecessárias e minimizar a atribuição de funções do proprietário.

Os utilizadores com a função Proprietária para uma subscrição do Azure também podem utilizar avaliações de [acesso para os recursos do Azure](pim-resource-roles-start-access-review.md) para auditar e remover atribuições de funções desnecessárias semelhantes ao processo descrito anteriormente para funções da AD Azure.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Decida quais as atribuições de funções que devem ser protegidas pela Gestão de Identidade Privilegiada

Depois de limpar atribuições de papéis privilegiados na sua organização, terá de decidir quais as funções a proteger com a Gestão de Identidade Privilegiada.

Se uma função for protegida pela Gestão privilegiada da identidade, os utilizadores elegíveis que lhe forem atribuídos devem elevar-se à utilização dos privilégios concedidos pela função. O processo de elevação também pode incluir a obtenção de aprovação, a realização de autenticação multifactor e/ou fornecer uma razão para a sua ativação. A Gestão de Identidade Privilegiada também pode acompanhar elevações através de notificações e dos registos de eventos de auditoria privilegiados de Gestão de Identidade e Azure AD.

Escolher quais as funções a proteger com a Gestão de Identidade Privilegiada pode ser difícil e será diferente para cada organização. Esta secção fornece os nossos conselhos de boas práticas para as funções de recursos Azure AD e Azure.

#### <a name="azure-ad-roles"></a>Funções do Azure AD

É importante priorizar a proteção de papéis da AD Azure que têm o maior número de permissões. Com base em padrões de utilização entre todos os clientes privilegiados de Gestão de Identidade, as 10 melhores funções de AD Azure geridas pela Privileged Identity Management são:

1. Administrador global
1. Administrador de segurança
1. Administrador do usuário
1. Administrador de intercâmbio
1. Administrador do SharePoint
1. Administrador insinado
1. Leitor de segurança
1. Administrador de serviço
1. Administrador de faturação
1. Skype para administrador de negócios

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que gere todos os seus Administradores Globais e Administradores de Segurança usando a Gestão de Identidade Privilegiada como um primeiro passo, pois são os que podem causar mais danos quando comprometidos.

É importante considerar quais os dados e permissão mais sensíveis para a sua organização. Como exemplo, algumas organizações podem querer proteger o seu papel de Administrador power BI ou o seu papel de Administrador de Equipas usando a Gestão de Identidade Privilegiada, uma vez que têm a capacidade de aceder a dados e/ou alterar fluxos de trabalho fundamentais.

Se houver alguma função com utilizadores convidados atribuídos, eles são particularmente vulneráveis a ataques.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que gere todas as funções com utilizadores convidados que utilizem a Gestão de Identidade Privilegiada para reduzir o risco associado às contas de utilizadores comprometidos.

Funções de leitor como o Leitor de Diretório, O Leitor de Mensagens e o Leitor de Segurança são por vezes considerados menos importantes em comparação com outros papéis, uma vez que não têm permissão de escrita. No entanto, temos visto alguns clientes também protegerem estas funções porque os atacantes que tiveram acesso a estas contas podem ser capazes de ler dados sensíveis, como dados pessoais. Deve ter isso em consideração ao decidir se os papéis dos leitores na sua organização precisam de ser geridos utilizando a Privileged Identity Management.

#### <a name="azure-resource-roles"></a>Funções de recursos do Azure

Ao decidir quais as atribuições de funções que devem ser geridas utilizando a Gestão de Identidade Privilegiada para o recurso Azure, deve primeiro identificar as subscrições/recursos que são mais vitais para a sua organização. Exemplos de tais assinaturas/recursos são:

- Recursos que acolhem os dados mais sensíveis
- Recursos que core, aplicações voltadas para o cliente dependem

Se é um Administrador Global com dificuldades em decidir quais as subscrições/recursos mais importantes, deve contactar os proprietários de subscrições da sua organização para reunir uma lista de recursos geridos por cada subscrição. Em seguida, deve trabalhar com os proprietários de subscrições para agrupar os recursos com base no nível de gravidade no caso de estarem comprometidos (baixo, médio, alto). Deve priorizar a gestão de recursos com gestão de identidade privilegiada com base neste nível de gravidade.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que trabalhe com os proprietários de subscrição/recursos de serviços críticos para criar fluxos de trabalho de Gestão de Identidade Privilegiadapara todas as funções dentro de subscrições/recursos sensíveis.

A Gestão privilegiada de Identidade dos recursos do Azure suporta contas de serviço com limite de tempo. Deve tratar as contas de serviço exatamente da mesma forma que trataria uma conta de utilizador regular.

Para subscrições/recursos que não sejam tão críticos, não precisará de criar gestão de identidade privilegiada para todas as funções. No entanto, deve ainda proteger as funções de Administrador de Acesso ao Utilizador e Doutilizador com Gestão de Identidade Privilegiada.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que gere as funções do Proprietário e o Administrador de Acesso ao Utilizador de todas as subscrições/recursos utilizando a Gestão de Identidade Privilegiada.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Decida quais as atribuições de funções que devem ser permanentes ou elegíveis

Depois de ter decidido que a lista de funções será gerida pela Privileged Identity Management, deve decidir quais os utilizadores que devem obter o papel elegível em relação ao papel permanentemente ativo. As funções permanentemente ativas são as funções normais atribuídas através dos recursos azure Ative Directory e Azure, enquanto as funções elegíveis só podem ser atribuídas na Gestão de Identidade Privilegiada.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que tenha zero atribuições permanentemente ativas para funções de AD Azure e funções de recurso Azure que não sejam as duas contas de acesso de emergência de vidro partido [recomendadas](../users-groups-roles/directory-emergency-access.md), que devem ter o papel de Administrador Global permanente.

Apesar de recomendarmos zero administrador permanente, por vezes é difícil para as organizações o conseguirem de imediato. Aqui estão as coisas a considerar ao tomar esta decisão:

- Frequência de elevação – Se o utilizador só necessitar da atribuição privilegiada uma vez, não deve ter a atribuição permanente. Por outro lado, se o utilizador precisar do papel para o seu trabalho diário e utilizar a Gestão de Identidade Privilegiada reduziria consideravelmente a sua produtividade, podem ser considerados para o papel permanente.
- Casos específicos da sua organização – Se a pessoa que recebe o papel elegível for de uma equipa muito distante ou de um executivo de alto escalão ao ponto de comunicar e fazer cumprir o processo de elevação é difícil, podem ser considerados para o papel permanente.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda-lhe** que instale avaliações de acesso recorrentes para utilizadores com atribuições de funções permanentes (caso tenha alguma). Saiba mais sobre a revisão recorrente de acesso na secção final deste plano de implementação

### <a name="draft-your-privileged-identity-management-settings"></a>Rediga as suas definições de Gestão de Identidade Privilegiada

Antes de implementar a sua solução privilegiada de Gestão de Identidade, é uma boa prática elaborar as suas definições de Gestão de Identidade Privilegiada para cada papel privilegiado que a sua organização utiliza. Esta secção tem alguns exemplos de definições de Gestão de Identidade Privilegiada para funções particulares (são apenas para referência e podem ser diferentes para a sua organização). Cada uma destas definições é explicada em detalhe com as recomendações da Microsoft após as tabelas.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Definições privilegiadas de Gestão de Identidade para funções azure AD

| Função | Requerer MFA | Notificação | Bilhete incidente | Exigir aprovação | Aprovador | Duração da ativação | Administrador permanente |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrador Global | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros Administradores Globais | Uma Hora | Contas de acesso de emergência |
| Administrador de Intercâmbio | :heavy_check_mark: | :heavy_check_mark: | w.x.y. | w.x.y. | Nenhuma | 2 Horas | Nenhuma |
| Administrador de assistência técnica | w.x.y. | w.x.y. | :heavy_check_mark: | w.x.y. | Nenhuma | 8 Horas | Nenhuma |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Definições privilegiadas de Gestão de Identidade para funções de recursos Do Azure

| Função | Requerer MFA | Notificação | Exigir aprovação | Aprovador | Duração da ativação | Administrador ativo | Expiração ativa | Expiração elegível |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Proprietário de subscrições críticas | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Outros proprietários da subscrição | Uma Hora | Nenhuma | n/d | 3 meses |
| Administrador de Acesso ao Utilizador de subscrições menos críticas | :heavy_check_mark: | :heavy_check_mark: | w.x.y. | Nenhuma | Uma Hora | Nenhuma | n/d | 3 meses |
| Colaborador de Máquina Virtual | w.x.y. | :heavy_check_mark: | w.x.y. | Nenhuma | 3 Horas | Nenhuma | n/d | 6 meses |

A tabela seguinte descreve cada uma das definições.

| Definição | Descrição |
| --- | --- |
| Função | Nome do papel para o que está a definir as definições. |
| Requerer MFA | Se o utilizador elegível precisa de executar MFA antes de ativar o papel.<br/><br/> :heavy_check_mark: A **Microsoft recomenda** que aplique o MFA para todas as funções de administrador, especialmente se as funções tiverem utilizadores convidados. |
| Notificação | Se for definido como verdadeiro, o Administrador Global, O Administrador de Papel Privilegiado e administrador de segurança da organização receberão uma notificação por e-mail quando um utilizador elegível ativar a função.<br/><br/>**Nota:** Algumas organizações não têm um endereço de e-mail ligado às suas contas de administrador, para receber estas notificações de e-mail, você deve ir definir um endereço de e-mail alternativo para que os administradores recebam esses e-mails. |
| Bilhete incidente | Se o utilizador elegível precisa registar um número de bilhete incidente ao ativar o seu papel. Esta definição ajuda uma organização a identificar cada ativação com um número de incidente interno para mitigar ativações indesejadas.<br/><br/> :heavy_check_mark: A **Microsoft recomenda** aproveitar os números de bilhetes incidentes para ligar a Privileged Identity Management ao seu sistema interno. Isto é particularmente útil para os aprovadores que precisam de contexto para a ativação. |
| Exigir aprovação | Se o utilizador elegível precisa de obter aprovação para ativar a função.<br/><br/> :heavy_check_mark: A **Microsoft recomenda-lhe** que estabeleça a aprovação para funções com mais permissão. Com base nos padrões de utilização de todos os clientes privilegiados de Gestão de Identidade, Administrador Global, Administrador de Utilizador, Administrador de Câmbio, Administrador de Segurança e Administrador de Passwords são as funções mais comuns com a configuração da aprovação. |
| Aprovador | Se for necessária a aprovação para ativar a função elegível, enumere as pessoas que devem aprovar o pedido. Por predefinição, a Privileged Identity Management define o aprovador como todos os utilizadores que são um administrador privilegiado, quer sejam permanentes ou elegíveis.<br/><br/>**Nota:** Se um utilizador for elegível para uma função de AD Azure e um aprovador do papel, não poderá aprovar-se a si próprio.<br/><br/> :heavy_check_mark: A **Microsoft recomenda** que escolha os aprovadores para serem aqueles que têm mais conhecimento sobre a função específica e os seus utilizadores frequentes em vez de um Administrador Global. |
| Duração da ativação | O tempo de tempo que um utilizador será ativado na função antes de expirar. |
| Administrador permanente | Lista de utilizadores que serão administradores permanentes para o papel (nunca ter de ativar).<br/><br/> :heavy_check_mark: A **Microsoft recomenda** que tenha administrador permanente zero para todas as funções, exceto para administradores globais. Leia mais sobre o mesmo sobre quem deve ser elegível e quem deve ser permanentemente ativo secção deste plano. |
| Administrador ativo | Para os recursos do Azure, o administrador ativo é a lista de utilizadores que nunca terão de ativar para utilizar a função. Isto não é referido como administrador permanente como nas funções Da D Da Azure porque pode definir um prazo de validade para quando o utilizador perder esta função. |
| Expiração ativa | Uma atribuição de funções ativas para funções de recurso Azure expira após este período de tempo configurado. Pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente ativo. |
| Expiração elegível | Uma atribuição de funções elegíveis para funções de recurso Azure expira após este período de tempo configurado. Pode escolher entre 15 dias, 1 mês, 3 meses, 6 meses, 1 ano ou permanentemente elegíveis. |

## <a name="implement-your-solution"></a>Implementar sua solução

A base de um planeamento adequado é a base na qual pode implementar uma aplicação com sucesso com o Azure Ative Directory.  Proporciona segurança e integração inteligentes que simplificam o embarque, reduzindo ao mesmo tempo o tempo para implementações bem sucedidas.  Esta combinação garante que a sua aplicação está integrada com facilidade, atenuando o tempo de paragem para os utilizadores finais.

### <a name="identify-test-users"></a>Identificar utilizadores de testes

Utilize esta secção para identificar um conjunto de utilizadores e ou grupos de utilizadores para validar a implementação. Com base nas definições selecionadas na secção de planeamento, identifique os utilizadores que pretende testar para cada função.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que os proprietários de serviços de cada função De AD Azure sejam os utilizadores de teste para que possam familiarizar-se com o processo e tornar-se um defensor interno do lançamento.

Nesta tabela, identifique os utilizadores de teste que verificarão se as definições para cada função estão a funcionar.

| Nome da função | Utilizadores de teste |
| --- | --- |
| &lt;nome&gt; | &lt;Utilizadores para testar o papel&gt; |
| &lt;nome&gt; | &lt;Utilizadores para testar o papel&gt; |

### <a name="test-implementation"></a>Implementação de testes

Agora que identificou os utilizadores do teste, use este passo para configurar a Gestão de Identidade Privilegiada para os seus utilizadores de teste. Se a sua organização quiser incorporar o fluxo de trabalho privilegiado de Gestão de Identidade na sua própria aplicação interna em vez de utilizar a Gestão de Identidade Privilegiada no portal Azure, todas as operações em Gestão de Identidade Privilegiada são também suportadas através do nosso [gráfico API.](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root)

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Configure Gestão de Identidade Privilegiada para funções de AD Azure

1. [Configure as definições de funções da AD Azure](pim-how-to-change-default-settings.md) com base no que planeou.

1. Navegue para **funções de AD Azure,** clique em **Funções**e, em seguida, selecione o papel que acabou de configurar.

1. Para o grupo de utilizadores de testes, se já forem administradores permanentes, pode torná-los elegíveis procurando-os e convertendo-os de permanentes para elegíveis clicando nos três pontos da sua linha. Se ainda não tiverem as atribuições, pode [fazer uma nova missão elegível.](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)

1. Repita os passos 1-3 para todas as funções que pretende testar.

1. Depois de configurar os utilizadores de teste, deve enviar-lhes o link para como ativar a [sua função Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Configure Gestão de Identidade Privilegiada para funções de recursos Azure

1. [Configure as definições de função](pim-resource-roles-configure-role-settings.md) de recurso Azure para uma função dentro de uma subscrição ou recurso que pretende testar.

1. Navegue para **os recursos do Azure** para essa subscrição e clique em **Funções,** selecione a função que acabou de configurar.

1. Para o grupo de utilizadores de testes, se já forem um administrador ativo, pode torná-los elegíveis procurando-os e atualizando a sua atribuição de [funções.](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment) Se ainda não tiverem o papel, pode [atribuir um novo papel.](pim-resource-roles-assign-roles.md#assign-a-role)

1. Repita os passos 1-3 para todas as funções que pretende testar.

1. Depois de configurar os utilizadores de teste, deve enviar-lhes o link para como ativar a sua função de [recurso Azure](pim-resource-roles-activate-your-roles.md).

Deve utilizar este palco para verificar se toda a configuração configurada para as funções está a funcionar corretamente. Utilize a tabela seguinte para documentar os seus testes. Deve também utilizar esta fase para otimizar a comunicação com os utilizadores afetados.

| Função | Comportamento esperado durante a ativação | Resultados reais |
| --- | --- | --- |
| Administrador Global | (1) Exigir MFA<br/>(2) Exigir aprovação<br/>(3) O aprovador recebe notificação e pode aprovar<br/>(4) A função expira após o tempo pré-definido |  |
| Proprietário da subscrição *X* | (1) Exigir MFA<br/>(2) A atribuição elegível expira após período de tempo configurado |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Comunicar Gestão de Identidade Privilegiada às partes interessadas afetadas

A implementação da Gestão de Identidade Privilegiada introduzirá passos adicionais para os utilizadores de funções privilegiadas. Embora a Gestão de Identidade Privilegiada reduza consideravelmente as questões de segurança associadas a identidades privilegiadas, a mudança tem de ser efetivamente comunicada antes da implantação em todo o arrendamento. Dependendo do número de administradores com impacto, as organizações optam frequentemente por criar um documento interno, um vídeo ou um e-mail sobre a mudança. Frequentemente incluído nestas comunicações incluem:

- O que é PIM
- Qual é o benefício para a organização
- Quem será afetado
- Quando será lançado pim
- Que passos adicionais serão necessários para que os utilizadores ativam a sua função
    - Deve enviar links para a nossa documentação:
    - [Ativar funções de AD Azure](pim-how-to-activate-role.md)
    - [Ativar funções de recurso Azure](pim-resource-roles-activate-your-roles.md)
- Contacte informações ou ligação de helpdesk para quaisquer problemas associados à PIM

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda-lhe** que reserve tempo com a sua equipa de apoio/suporte para os acompanhar através do fluxo de trabalho privilegiado de Gestão de Identidade (se a sua organização tiver uma equipa interna de suporte de TI). Forneça-lhes as documentações apropriadas, bem como as informações de contacto.

### <a name="move-to-production"></a>Mover para produção

Uma vez que os seus testes estão completos e bem sucedidos, mova a Gestão de Identidade Privilegiada para a produção, repetindo todos os passos nas fases de teste para todos os utilizadores de cada função que definiu na sua configuração de Gestão de Identidade Privilegiada. Para a Gestão de Identidade Privilegiada para funções de AD Azure, as organizações frequentemente testam e lançam gestão de identidade privilegiada para administradores globais antes de testar e lançar a Gestão de Identidade Privilegiada para outras funções. Enquanto isso, para o recurso Azure, as organizações normalmente testam e lançam a Privilegiada Gestão de Identidade uma assinatura Azure de cada vez.

### <a name="in-the-case-a-rollback-is-needed"></a>No caso de uma reversão é necessário

Se a Gestão de Identidade Privilegiada não funcionar como desejado no ambiente de produção, os seguintes passos de retrocesso podem ajudá-lo a voltar a um bom estado conhecido antes de criar a Gestão de Identidade Privilegiada:

#### <a name="azure-ad-roles"></a>Funções do Azure AD

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Abra **Azure ad Privileged Identity Management**.
1. Clique em **funções de AD Azure** e, em seguida, clique em **Funções**.
1. Para cada função que configurar, clique na elipse**para**todos os utilizadores com uma atribuição elegível.
1. Clique na opção **Tornar permanente** a opção para tornar a atribuição de funções permanente.

#### <a name="azure-resource-roles"></a>Funções de recursos do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Abra **Azure ad Privileged Identity Management**.
1. Clique nos **recursos do Azure** e, em seguida, clique numa subscrição ou recurso que pretende reverter.
1. Clique em **funções**.
1. Para cada função que configurar, clique na elipse**para**todos os utilizadores com uma atribuição elegível.
1. Clique na opção **Tornar permanente** a opção para tornar a atribuição de funções permanente.

## <a name="next-steps-after-deploying"></a>Próximos passos após a implantação

Implementar com sucesso a Gestão de Identidade Privilegiada na produção é um passo significativo em termos de assegurar as identidades privilegiadas da sua organização. Com a implementação da Gestão de Identidade Privilegiada surgem funcionalidades adicionais de Gestão de Identidade Privilegiada que deve utilizar para segurança e conformidade.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Utilize alertas privilegiados de Gestão de Identidade para salvaguardar o seu acesso privilegiado

Deve utilizar a funcionalidade de alerta de identidade privilegiada incorporada para melhor salvaguardar o seu inquilino. Para mais informações, consulte [alertas](pim-how-to-configure-security-alerts.md#security-alerts)de segurança . Estes alertas incluem: os administradores não estão a usar papéis privilegiados, as funções estão a ser atribuídas fora da Gestão de Identidade Privilegiada, as funções estão a ser ativadas com demasiada frequência e mais. Para proteger totalmente a sua organização, deve analisar regularmente a sua lista de alertas e corrigir os problemas. Pode ver e corrigir os seus alertas da seguinte forma:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Abra **Azure ad Privileged Identity Management**.
1. Clique em **funções de AD Azure** e, em seguida, clique em **Alertas**.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que lide com todos os alertas marcados com elevada gravidade imediatamente. Para alertas de gravidade média e baixa, deve manter-se informado e fazer alterações se acreditar que existe uma ameaça à segurança.

Se algum dos alertas específicos não for útil ou não se aplicar à sua organização, pode sempre descartar o alerta na página de alertas. Pode sempre reverter este despedimento mais tarde na página de definições de Anúncio sinuoso.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Crie avaliações de acesso recorrentes para auditar regularmente as identidades privilegiadas da sua organização

As avaliações de acesso são a melhor forma de perguntar aos utilizadores atribuídos com papéis privilegiados ou revisores específicos se cada utilizador precisa da identidade privilegiada. As avaliações de acesso são ótimas se quiser reduzir a superfície de ataque e manter-se conforme. Para obter mais informações sobre o início de uma revisão de acesso, consulte as [funções da Azure AD aceder a revisões](pim-how-to-start-security-review.md) e a utilizações de [recursos do Azure.](pim-resource-roles-start-access-review.md) Para algumas organizações, a realização de uma revisão periódica de acesso é necessária para se manter em conformidade com as leis e regulamentos, enquanto para outras, a revisão de acesso é a melhor maneira de fazer valer o principal de menor privilégio em toda a sua organização.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que instale avaliações trimestrais de acesso para todas as suas funções de recurso Azure AD e Azure.

Na maioria dos casos, o revisor para funções de AD Azure é o próprio utilizador, enquanto o revisor para funções de recurso Azure é o proprietário da subscrição, na qual está o papel. No entanto, muitas vezes acontece que as empresas têm contas privilegiadas que não estão ligadas ao endereço de e-mail de qualquer pessoa em particular. Nesses casos, ninguém lê e revê o acesso.

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que adicione um endereço de e-mail secundário para todas as contas com atribuições de papéis privilegiadas que não estejam ligadas a um endereço de e-mail regularmente verificado

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Tire o máximo partido do seu registo de auditoria para melhorar a segurança e a conformidade

O registo de auditoria é o local onde pode manter-se atualizado e estar em conformidade com os regulamentos. A Privileged Identity Management armazena atualmente um histórico de 30 dias de toda a história da sua organização dentro do seu registo de auditoria, incluindo:

- Ativação/desativação de funções elegíveis
- Atividades de atribuição de funções dentro e fora da Gestão de Identidade Privilegiada
- Alterações nas definições de funções
- Solicitar/aprovar/negar atividades de ativação de funções com configuração de aprovação
- Atualizar para alertas

Pode aceder a estes registos de auditoria se for administrador global ou administrador privilegiado. Para mais informações, consulte o histórico de [auditoria para funções de AD Azure](pim-how-to-use-audit-log.md) e [o histórico de auditoria para funções](azure-pim-resource-rbac.md)de recursos Azure .

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda** que leia pelo menos um administrador em todos os eventos de auditoria semanalmente e exporte mensalmente os seus eventos de auditoria.

Se pretender armazenar automaticamente os seus eventos de auditoria por um período mais longo de tempo, o registo de auditoria da Privileged Identity Management é automaticamente sincronizado nos registos de auditoria da [AD Azure](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: A **Microsoft recomenda-lhe** que [crie monitorização](../reports-monitoring/concept-activity-logs-azure-monitor.md) de registo sonante do Azure para arquivar eventos de auditoria numa conta de armazenamento Azure para a necessidade de segurança e conformidade.
