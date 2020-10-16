---
title: Planeie uma implementação de avaliações de acesso a diretórios ativos do Azure
description: Guia de planeamento para uma implementação bem sucedida de Avaliações de Acesso
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/14/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51983e52643d587fc497a1a67fcb0cd29dbb7f17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306570"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Planeamento Azure Ative Directy Access Reviews implementação

[Azure Ative Directory (Azure AD) Access Reviews](access-reviews-overview.md) ajudam a sua organização a manter a rede mais segura, gerindo o seu [ciclo de vida de acesso a recursos.](identity-governance-overview.md) Com comentários de acesso, pode:

* Agende revisões regulares ou efetue avaliações ad-hoc para ver quem tem acesso a recursos específicos, tais como aplicações e grupos

* Acompanhe as revisões por razões de informação, conformidade ou política

* Delegar revisões a administradores específicos, empresários ou utilizadores finais que possam auto-atestar a necessidade de acesso continuado

* Use os insights para determinar eficazmente se os utilizadores devem continuar a ter acesso

* Automatizar os resultados da revisão, tais como a eliminação do acesso dos utilizadores aos recursos

  ![Diagrama que mostra o fluxo de avaliações de acesso.](./media/deploy-access-review/1-planning-review.png)

Access Reviews é uma capacidade [de governança de identidade Azure AD.](identity-governance-overview.md) As outras capacidades são [Gestão de Direitos,](entitlement-management-overview.md) [Gestão de Identidade Privilegiada](../privileged-identity-management/pim-configure.md) e Termos de [Utilização.](../conditional-access/terms-of-use.md) Juntos, ajudam as organizações a abordar estas quatro questões:

* Que utilizadores devem ter acesso a que recursos?

* O que é que os utilizadores estão a fazer com esse acesso?

* Existe um controlo organizacional eficaz para gerir o acesso?

* Os auditores podem verificar se os controlos estão a funcionar?

Planear a sua implementação de Access Reviews é essencial para garantir que alcança a estratégia de governação desejada para os utilizadores da sua organização.

### <a name="key-benefits"></a>Principais vantagens

Os principais benefícios de permitir avaliações de acesso são:

* **Colaboração de controlo**. O Access Reviews permite que as organizações consigam o acesso a todos os recursos de que os seus utilizadores necessitam. Quando os seus utilizadores partilham e colaboram, as organizações podem ter a certeza de que a informação está apenas entre os utilizadores autorizados.

* **Gerir o risco**. Os Comentários de Acesso fornecem às organizações uma forma de rever o acesso a dados e aplicações, reduzindo o risco de fuga de dados e derrame de dados. Isto inclui capacidades para rever regularmente o acesso do parceiro externo aos recursos corporativos. 

* **Abordar a conformidade e a governação.** Com comentários de acesso, pode governar e recertificar o ciclo de vida de acesso a grupos, apps e sites. Pode controlar avaliações de rastreio para conformidade ou aplicações sensíveis ao risco específicas da sua organização. 

* **Reduzir o custo.** Os Comentários de Acesso são construídos na nuvem, e nativamente funciona com recursos em nuvem, tais como grupos, aplicações e Pacotes de Acesso. Usar comentários de acesso é menos dispendioso do que construir as suas próprias ferramentas ou, de outra forma, atualizar o seu bloco de ferramentas no local.

### <a name="training-resources"></a>Recursos de formação

Os seguintes vídeos podem ser úteis à medida que aprende sobre comentários de acesso:

* [O que são comentários de acesso em Azure AD?](https://youtu.be/kDRjQQ22Wkk)

* [Como criar comentários de acesso em Azure AD](https://youtu.be/6KB3TZ8Wi40)

* [Como ativar comentários de acesso em Azure AD](https://youtu.be/X1SL2uubx9M)

* [Como rever o acesso usando o Meu Acesso](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licenças

Precisa de uma licença Azure AD Premium (P2) válida para cada pessoa, para além de Administradores Globais ou Administradores de Utilizadores, que criará ou realizará Avaliações de Acesso. Para obter mais informações, consulte [os requisitos de licença do Access Reviews.](access-reviews-overview.md)

Poderá também necessitar de outras funcionalidades de Governação de Identidade, tais como [Gestão de Ciclo de Vida de Direito](entitlement-management-overview.md) ou Gestão de Identidade Privilegiada. Nesse caso, também pode precisar de licenças relacionadas. Para mais informações, consulte [os preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="plan-the-access-reviews-deployment-project"></a>Planear o projeto de implantação de Avaliações de Acesso

Considere as suas necessidades organizacionais para determinar a estratégia de implementação de Avaliações de Acesso no seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, normalmente fazem-no devido a expectativas desajustadas no impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas](https://aka.ms/deploymentplans) e que as funções do projeto são claras.

Para avaliações de acesso, provavelmente incluirá representantes das seguintes equipas dentro da sua organização:

* **A administração de TI** gere a sua infraestrutura de TI e administra os seus investimentos em nuvem e aplicações de Software como um Serviço (SaaS). Esta equipa vai:

   * Reveja o acesso privilegiado a infraestruturas e aplicações, incluindo o Microsoft 365 e o Azure AD.

   * Agendar e executar Revisões de Acesso em grupos que são usados para manter listas de exceções ou projetos-piloto de TI, para manter listas de acesso atualizadas.

   * Certifique-se de que o acesso programático (scripted) aos recursos através dos Principais de Serviço é regido e revisto.

* **As equipas de** desenvolvimento constroem e mantêm candidaturas para a sua organização. Esta equipa vai:

   * Controle quem pode aceder e gerir componentes em recursos SaaS, PaaS e IaaS que compõem as soluções desenvolvidas.

   * Gerir grupos que possam aceder a aplicações e ferramentas para o desenvolvimento de aplicações internas.

   * Requer identidades privilegiadas que tenham acesso a software de produção ou soluções que estejam hospedadas para os seus clientes

* **As unidades de negócio** gerem projetos e aplicações próprias. Esta equipa vai: 

   * Reveja e aprove ou negue o acesso a grupos e aplicações para utilizadores internos e externos.

   * Agende e realize revisões para atestar o acesso continuado dos colaboradores e identidades externas, como parceiros de negócio.

* **O governo corporativo** garante que a organização está a seguir a política interna e a cumprir os regulamentos. Esta equipa vai:

   * Solicitar ou agendar novos Comentários de Acesso.

   * Avaliar processos e procedimentos de revisão do acesso, incluindo documentação e registos para o cumprimento.

   * Reveja os resultados de avaliações passadas para a maioria dos recursos críticos.

> [!NOTE]
> Para avaliações que exijam avaliações manuais, certifique-se de planear para revisores adequados e ciclos de revisão que atendam às suas necessidades de política e conformidade. Se os ciclos de revisão forem demasiado frequentes, ou se houver muito poucos revisores, a qualidade pode perder-se e muitas ou poucas pessoas podem ter acesso. 

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo processo de negócio. Comunicar proativamente aos utilizadores como e quando a sua experiência vai mudar, e como ganhar apoio se eles experimentarem problemas. 

#### <a name="communicate-changes-in-accountability"></a>Comunicar alterações na prestação de contas

Os Comentários de Acesso apoiam a mudança da responsabilidade de rever e agir sobre o acesso continuado aos empresários. A dissociação das decisões de acesso a partir de TI impulsiona decisões de acesso mais precisas. Trata-se de uma mudança cultural na responsabilidade e responsabilidade do proprietário dos recursos. Comunicar proativamente esta mudança e garantir que os proprietários de recursos são treinados e capazes de usar os insights para tomar boas decisões.

É evidente que a TI quererá manter-se no controlo de todas as decisões de acesso relacionadas com infraestruturas e de atribuições privilegiadas de funções. 

#### <a name="customize-email-communication"></a>Personalizar comunicação de e-mail

Ao agendar uma revisão, nomeia utilizadores que realizarão esta análise. Estes revisores recebem então uma notificação por e-mail de novas avaliações que lhes foram atribuídas, bem como lembretes antes de expirar uma revisão a eles.

Os administradores podem optar por enviar esta notificação a meio caminho antes de expirar a revisão ou um dia antes de expirar. 

O e-mail enviado aos revisores pode ser personalizado para incluir uma mensagem curta personalizada que os encoraja a agir na revisão. Recomendamos que utilize o texto adicional para:

* Inclua uma mensagem pessoal aos revisores, para que entendam que é enviada pelo seu departamento de Conformidade ou TI.

* Inclua uma hiperligação ou referência à informação interna sobre quais são as expectativas da revisão e material adicional de referência ou formação.

* Inclua um link para instruções sobre [como realizar uma auto-revisão do acesso.](review-your-access.md) 

  ![E-mail de revisor](./media/deploy-access-review/2-plan-reviewer-email.png)

Ao selecionar a revisão do Início, os revisores serão direcionados para o [portal myAccess](https://myapplications.microsoft.com/) para análises de grupo e de acesso a aplicações. O portal dá-lhes uma visão geral de todos os utilizadores que tenham acesso ao recurso que estão a rever, e recomendações do sistema com base na última informação de acesso e acesso.

### <a name="plan-a-pilot"></a>Planeie um piloto

Encorajamos os clientes a pilotar inicialmente avaliações de acesso com um pequeno grupo e a direcionar recursos não críticos. A pilotagem pode ajudá-lo a ajustar processos e comunicações conforme necessário, e aumentar a capacidade dos utilizadores e revisores de cumprir os requisitos de segurança e conformidade.

No seu piloto, recomendamos:

* Comece com as análises em que os resultados não são aplicados automaticamente e pode controlar as implicações.

* Certifique-se de que todos os utilizadores têm endereços de e-mail válidos listados no AZure AD, e eles recebem comunicação de e-mail para tomar as medidas apropriadas. 

* Documente qualquer acesso removido como parte do piloto no caso de precisar restaurá-lo rapidamente.

* Monitorize os registos de auditoria para garantir que todos os eventos são devidamente auditados.

Para mais informações, consulte [as melhores práticas para um piloto.](../fundamentals/active-directory-deployment-plans.md)

## <a name="introduction-to-access-reviews"></a>Introdução a Avaliações de Acesso

Esta secção introduz conceitos de Access Review que deve conhecer antes de planear as suas avaliações.

### <a name="what-resource-types-can-be-reviewed"></a>Que tipos de recursos podem ser revistos?

Assim que integrar os recursos da sua organização com a AZure AD (como utilizadores, aplicações e grupos), estes podem ser geridos e revistos. 

Os alvos típicos para revisão incluem:

* [Aplicações integradas com Azure AD para single sign-on](../manage-apps/what-is-application-management.md) (como SaaS, linha de negócios).

* Membro [do](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) grupo (sincronizado com Azure AD, ou criado em AZure AD ou Microsoft 365, incluindo Microsoft Teams).

* [Pacote de acesso](/azure/active-directory/governance/entitlement-management-overview) que agrupam recursos (grupos, apps e sites) num único pacote para gerir o acesso.

* [Funções de AD AZure e Funções de Recursos Azure definidas](../privileged-identity-management/pim-resource-roles-assign-roles.md) na Gestão de Identidade Privilegiada.

### <a name="who-will-create-and-manage-access-reviews"></a>Quem irá criar e gerir os Comentários de Acesso?

A função administrativa necessária para criar, gerir ou ler uma Revisão de Acesso depende do tipo de recurso que está a ser revisto. O quadro a seguir indica as funções necessárias para cada tipo de recurso:

| Tipo de recurso| Criar e gerir avaliações de acesso (Criadores)| Ler resultados da revisão do acesso |
| - | - | -|
| Grupo ou aplicação| Administrador Global <p>Administrador de Utilizadores| Criadores e Administrador de Segurança |
| Papéis privilegiados em Azure AD| Administrador Global <p>Administrador privilegiado| Criadores <p>Leitor de Segurança<p>Administrador de Segurança |
| Funções privilegiadas em Azure (recursos)| Administrador Global<p>Administrador de Utilizadores<p>Proprietário de Recursos| Criadores |
| Pacote de acesso| Administrador Global<p>Pacote de Criador de Acesso| Administrador Global apenas |


Para obter mais informações, consulte [permissões de função de administrador no Diretório Ativo Azure](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="who-will-review-the-access-to-the-resource"></a>Quem irá rever o acesso ao recurso?

O criador da revisão de acesso decide no momento da criação quem irá realizar a revisão. Esta definição não pode ser alterada uma vez que a revisão é iniciada. Os revisores são representados por três personalidades:

* Proprietários de recursos, que são os proprietários do recurso.

* Um conjunto de delegados selecionados individualmente, selecionados pelo administrador do Access Reviews.

* Utilizadores finais que irão cada um auto-atestar a sua necessidade de acesso continuado.

Ao criar uma Análise de Acesso, os administradores podem escolher um ou mais revisores. Todos os revisores podem iniciar e realizar uma revisão, escolhendo os utilizadores para o acesso continuado a um recurso ou removendo-os. 

### <a name="components-of-an-access-review"></a>Componentes de uma Revisão de Acesso

Antes de implementar os seus Comentários de Acesso, deverá planear os tipos de avaliações relevantes para a sua organização. Para tal, terá de tomar decisões empresariais sobre o que pretende rever e as ações a tomar com base nessas avaliações.

Para criar uma política de revisão de acesso, tem de ter as seguintes informações.

* Quais são os recursos a rever?

* Cujo acesso está a ser revisto.

* Com que frequência deve ocorrer a revisão?

* Quem fará a crítica?

   * Como serão notificados para rever?

   * Quais são os prazos a aplicar para revisão?

* Que ações automáticas devem ser aplicadas com base na revisão?

   * O que acontece se o revisor não responder a tempo?

* Que ações manuais serão tomadas em consequência da revisão?

* Que comunicações devem ser enviadas com base em ações tomadas?


**Plano de revisão de acesso de exemplo**

| Componente| Valor |
| - | - |
| **Recursos a rever**| Acesso à Microsoft Dynamics |
| **Frequência de revisão**| Mensalmente |
| **Quem realiza a revisão**| Gestores de programas do grupo de negócios Dynamics |
| **Notificação**| E-mail 24 horas antes de rever o pseudónimo Dynamics-Pms<p>Incluir mensagem personalizada encorajadora para os revisores para garantir a sua entrada |
| **Linha cronológica**| 48 horas a partir da notificação |
|**Ações automáticas**| Remova o acesso de qualquer conta que não tenha qualquer sômposições interativas no prazo de 90 dias, removendo o utilizador do acesso dinâmico do grupo de segurança. <p>*Executar ações se não for revisto dentro da linha do tempo.* |
| **Ações manuais**| Os revisores podem efetuar a aprovação de remoção antes da ação automatizada, se desejar. |
| **Comunicações**| Envie utilizadores internos (membro) que sejam removidos um e-mail explicando que são removidos e como recuperar o acesso. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Automatizar ações com base em avaliações de acesso

Pode optar por ter a remoção de acesso automatizada, definindo os resultados de aplicação automática à opção de recurso para Ativar.

  ![Planejando avaliações de acesso](./media/deploy-access-review/3-automate-actions-settings.png)

Uma vez concluída a revisão e terminada, os utilizadores que não tenham sido aprovados pelo revisor serão automaticamente removidos do recurso – ou mantidos com acesso continuado. Isto poderia significar a remoção da sua filiação em grupo, a sua atribuição de candidaturas ou a revogação do seu direito de elevar para um papel privilegiado.

Aceite recomendações

As recomendações são apresentadas aos revisores como parte da experiência do revisor e indicam o último sinal de uma pessoa ao inquilino, ou último acesso a uma aplicação. Esta informação ajuda os revisores a tomar a decisão de acesso certa. Selecionar recomendações take seguirá as recomendações do Access Review. No final de uma Análise de Acesso, o sistema aplicará automaticamente estas recomendações aos utilizadores que os revisores não responderam.

As recomendações baseiam-se nos critérios da revisão do acesso. Por exemplo, se configurar o reexame para remover o acesso sem acesso interativa durante 90 dias, recomendará que todos os utilizadores que se enquadrem nesses critérios sejam removidos. A Microsoft está continuamente a trabalhar no reforço das recomendações. 

### <a name="review-guest-user-access"></a>Rever o acesso do utilizador do hóspede

Use comentários de acesso para rever e limpar as identidades dos parceiros de colaboração de organizações externas. A configuração de uma revisão por parceiro pode satisfazer os requisitos de conformidade.

As identidades externas podem ser concedidas ao acesso aos recursos da empresa através de uma das seguintes ações:

* Adicionado a um grupo 

* Convidados para equipas 

* Atribuído a uma aplicação empresarial ou pacote de acesso

* Atribuído um papel privilegiado na AD AZure ou numa subscrição do Azure

Consulte o [guião da amostra.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse) O guião mostrará onde são usadas identidades externas convidadas para o inquilino. Pode ver a filiação externa do grupo de utilizadores, atribuições de funções e atribuições de aplicações em Azure AD. O script não mostrará nenhuma atribuição fora do Azure AD, por exemplo, atribuição de direitos diretos aos recursos do Sharepoint, sem o uso de grupos.

Ao criar um Access Review para grupos ou aplicações, pode optar por deixar o revisor concentrar-se apenas em Todos os utilizadores com acesso ou hóspedes. Ao selecionar apenas os utilizadores de Hóspedes, os revisores recebem uma lista focalizada de identidades externas do Azure AD B2B que têm acesso ao recurso.

 ![Rever utilizadores de hóspedes](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> Isto NÃO incluirá membros externos que tenham um utilizadorType de membro. Isto também não incluirá utilizadores convidados fora da colaboração Azure AD B2B, por exemplo aqueles que têm acesso a conteúdo partilhado diretamente através do SharePoint.

## <a name="plan-access-reviews-for-access-packages"></a>Plan Access Reviews for Access Packages

[Os pacotes de acesso](entitlement-management-overview.md) podem simplificar muito a sua estratégia de governação e revisão de acesso. Um Pacote de Acesso é um pacote de todos os recursos com o acesso que um utilizador precisa para trabalhar num projeto ou executar a sua tarefa. Por exemplo, pode querer criar um Pacote de Acesso que inclua todas as aplicações de que os desenvolvedores da sua organização necessitam, ou todas as aplicações a que os utilizadores externos devem ter acesso. Um administrador ou gestor delegado do Access Package em seguida agruca os recursos (grupos ou aplicações), e as funções que os utilizadores precisam para esses recursos.

Ao [criar um Pacote de Acesso,](entitlement-management-access-package-create.md)pode criar uma ou mais políticas de acesso que estabeleçam condições para as quais os utilizadores podem solicitar um Pacote de Acesso, como é o processo de aprovação e com que frequência uma pessoa teria de reenca solicitá-lo. Os Comentários de Acesso são configurados ao criar ou editar uma política de Pacotes de Acesso.

Abra o separador Ciclo de Vida para deslocar-se para baixo para avaliações de acesso.

 ![Screenshot que mostra a "política de edição" no separador "Ciclo de Vida".](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Análises de Acesso ao Plano para grupos

Além dos Pacotes de Acesso, rever a adesão ao grupo é a forma mais eficaz de reger o acesso. Recomendamos que o acesso aos recursos seja atribuído através de [grupos de segurança ou grupos Microsoft 365](../fundamentals/active-directory-manage-groups.md), e que os utilizadores sejam adicionados a esses grupos para terem acesso.

Um único grupo pode ter acesso a todos os recursos adequados. Pode atribuir o acesso do grupo a recursos individuais ou a um Pacote de Acesso que agrupar aplicações e outros recursos. Com este método, pode rever o acesso ao grupo em vez do acesso de um indivíduo a cada aplicação. 

A adesão ao grupo pode ser revista por: 

* Administradores

* Proprietários do grupo

* Utilizadores selecionados, capacidade de revisão delegada quando a revisão é criada

* Membros do grupo, atestando por si mesmos

### <a name="group-ownership"></a>Propriedade do grupo

Recomendamos que os donos do grupo revejam a adesão, uma vez que estão mais bem situados para saber quem precisa de acesso. A propriedade dos grupos difere com o tipo de grupo:

Os grupos criados na Microsoft 365 e Azure AD têm um ou mais proprietários bem definidos. Na maioria dos casos, estes proprietários fazem revisores perfeitos para os seus próprios grupos, pois sabem quem deve ter acesso. 

Por exemplo, o Microsoft Teams utiliza o Microsoft 365 Groups como o modelo de autorização subjacente para conceder aos utilizadores acesso a recursos que estão em SharePoint, Exchange, OneNote ou outros serviços Microsoft 365. O criador da equipa torna-se automaticamente proprietário e deve ser responsável por atestar a adesão a esse grupo. 

Os grupos criados manualmente no portal AD Azure ou através da script através do Microsoft Graph podem não ter necessariamente os proprietários definidos. Recomendamos que os defina através do Portal AD Azure na secção "Proprietários" do grupo ou via Gráfico.

Grupos sincronizados a partir de diretório ativo no local não podem ter um proprietário em Azure AD. Ao criar um Access Review para eles, deve selecionar indivíduos que sejam mais adequados para decidir sobre a adesão neles.

> [!NOTE]
> Recomendamos definir políticas empresariais que definam como os grupos são criados para garantir uma clara propriedade do grupo e responsabilidade para uma revisão regular da adesão. 

### <a name="review-membership-of-exclusion-groups-in-ca-policies"></a>Rever a adesão a grupos de exclusão nas políticas da AC 

Há momentos em que as políticas de Acesso Condicional (CA) concebidas para manter a sua rede segura não devem ser aplicadas a todos os utilizadores. Por exemplo, uma política de CA que apenas permite que os utilizadores assinem enquanto estão na rede corporativa pode não se aplicar à equipa de Vendas, que viaja extensivamente. Nesse caso, os membros da equipa sales seriam colocados num grupo e esse grupo seria excluído da política da AC. 

Rever regularmente uma adesão a um grupo, uma vez que a exclusão representa um risco potencial se os membros errados forem excluídos da exigência.

Pode [utilizar comentários de acesso Azure AD para gerir utilizadores excluídos das políticas de Acesso Condicional.](conditional-access-exclusion.md)

### <a name="review-external-users-group-memberships"></a>Rever as filiações externas do grupo de utilizadores

Para minimizar o trabalho manual e os potenciais erros associados, considere usar [grupos dinâmicos](../users-groups-roles/groups-create-rule.md) para atribuir a filiação do grupo com base nos atributos de um utilizador. Pode querer criar um ou mais Grupos Dinâmicos para utilizadores externos. O patrocinador interno pode atuar como revisor para a adesão ao grupo. 

Nota: Os utilizadores externos que são removidos de um grupo como resultado de uma Revisão de Acesso não são eliminados do arrendatário. 

Podem ser eliminados de um inquilino removido manualmente, ou através de um roteiro.

### <a name="review-access-to-on-premises-groups"></a>Rever o acesso a grupos no local

Os Comentários de Acesso não podem alterar a adesão ao grupo de grupos que sincronizam a partir do local com [o Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Isto porque a fonte de autoridade está no local.

Você ainda pode usar Comentários de Acesso para agendar e manter avaliações regulares de grupos no local. Os revisores tomarão então medidas no grupo no local. Esta estratégia mantém os Comentários de Acesso como a ferramenta para todas as avaliações.

Pode utilizar os resultados de uma Revisão de Acesso em grupos no local e processá-los ainda mais, quer através de:

* Descarregar o relatório CSV a partir da Revisão de Acesso e tomar medidas manualmente.

* Utilizar o Microsoft Graph para aceder programaticamente a resultados e decisões em análises de acesso concluídas.

Por exemplo, para aceder aos resultados de um grupo gerido pelo Windows AD, utilize este [script da amostra PowerShell](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises). O script descreve as chamadas de gráficos necessárias e exporta os comandos windows AD-PowerShell para realizar as alterações.

## <a name="plan-access-reviews-for-applications"></a>Planear revisões de acesso para aplicações 

Ao rever o acesso a uma aplicação, está a rever o acesso dos colaboradores e identidades externas às informações e dados dentro da aplicação. Opte por rever uma aplicação quando precisa de saber quem tem acesso a uma aplicação específica, em vez de um Pacote de Acesso ou de um grupo. 

Recomendamos que planeie revisões para aplicações nos seguintes cenários:

* Aos utilizadores é concedido acesso direto à aplicação (fora de um grupo ou pacote de acesso).

* A aplicação expõe informações críticas ou sensíveis.

* O pedido tem requisitos de conformidade específicos aos quais deve atestar.

* Suspeita de acesso impróprio.

Para criar comentários de acesso para uma aplicação, detete a atribuição do utilizador necessária? propriedade para Sim. Se definido para Nº, todos os utilizadores do seu diretório, incluindo identidades externas, podem aceder à aplicação e não pode rever o acesso à aplicação. 

 ![planear atribuições de aplicativos](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Em seguida, deve [atribuir aos utilizadores e grupos](../manage-apps/assign-user-or-group-access-portal.md) que pretende ter acesso. 

### <a name="reviewers-for-an-application"></a>Revisores para uma aplicação

As avaliações de acesso podem ser para os membros de um grupo ou para utilizadores que foram designados para uma aplicação. As aplicações no Azure AD não têm necessariamente um proprietário, e é por isso que a opção para selecionar o proprietário da aplicação como revisor não é possível. Pode ainda analisar um reexame para rever apenas os utilizadores convidados atribuídos à aplicação, em vez de rever todos os acessos.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Revisão do plano das funções de recursos Azure AD e Azure

[A Gestão de Identidade Privilegiada (PIM)](../privileged-identity-management/pim-configure.md) simplifica a forma como as empresas gerem o acesso privilegiado aos recursos em Azure AD. Isto mantém a lista de funções privilegiadas, tanto em [recursos Azure AD](../users-groups-roles/directory-assign-admin-roles.md) como [Azure](../../role-based-access-control/built-in-roles.md) muito menores e aumenta a segurança geral do diretório.

O Access Reviews permite que os revisores ateste se os utilizadores ainda precisam de estar numa função. Tal como os comentários de acesso a pacotes de acesso, as avaliações das funções AD do Azure e do recurso Azure estão integradas na experiência do utilizador de administração PIM. Recomendamos que reveja regularmente as seguintes atribuições de funções:

* Administrador Global

* Administrador de Utilizadores

* Administrador de Autenticação Privilegiada

* Administrador de acesso condicional

* Administrador de Segurança

* Todas as funções da Microsoft 365 e da Administração de Serviços Dinâmicos

As funções aqui selecionadas incluem papel permanente e elegível. 

Na secção de Revisores, selecione uma ou mais pessoas para rever todos os utilizadores. Ou pode selecionar para que os membros revejam o seu próprio acesso.

 ![Editar política](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Implementar comentários de acesso

Depois de ter preparado uma estratégia e um plano para rever o acesso aos recursos integrados com a Azure AD, implementar e gerir avaliações utilizando os recursos abaixo.

### <a name="review-access-packages"></a>Rever pacotes de acesso

Para reduzir o risco de acesso à estagnação, os administradores podem permitir revisões periódicas de utilizadores que tenham atribuições ativas a um pacote de acesso. Siga as instruções no link abaixo:

| Artigos de procedimentos| Descrição |
| - | - |
| [Criar comentários de acesso](entitlement-management-access-reviews-create.md)| Ativar comentários sobre pacote de acesso. |
| [Realizar comentários de acesso](entitlement-management-access-reviews-review-access.md)| Execute comentários de acesso a outros utilizadores que estejam atribuídos a um Pacote de Acesso. |
| [Pacotes de acesso atribuídos por auto-revisão](entitlement-management-access-reviews-self-review.md)| Auto-revisão dos pacotes de acesso atribuídos |


> [!NOTE]
> Os utilizadores finais que se autorrevejam e dizem que já não precisam de acesso não são imediatamente removidos do Pacote de Acesso. São removidos do Pacote de Acesso quando a revisão termina ou se um administrador parar a revisão.

### <a name="review-groups-and-apps"></a>Grupos de revisão e aplicativos

As necessidades de acesso a grupos e aplicações para funcionários e hóspedes provavelmente mudam ao longo do tempo. Para reduzir o risco associado a atribuições de acessos à porta, os administradores podem criar revisões de acesso para membros do grupo ou acesso a aplicações. Siga as instruções no link abaixo:

| Artigos de procedimentos| Descrição |
| - | - |
| [Criar comentários de acesso](create-access-review.md)| Crie uma ou mais avaliações de acesso para membros do grupo ou acesso a aplicações. |
| [Realizar comentários de acesso](perform-access-review.md)| Realize uma revisão de acesso para membros de um grupo ou utilizadores com acesso a uma aplicação. |
| [Autorreveja o seu acesso](review-your-access.md)| Os membros revejam o acesso próprio a um grupo ou a uma aplicação |
| [Análise completa de acesso](complete-access-review.md)| Ver uma Análise de Acesso e aplicar os resultados |
| [Tomar medidas para grupos no local](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| Experimente o script PowerShell para atuar em Avaliações de Acesso para grupos no local. |


### <a name="review-azure-ad-roles"></a>Rever funções de AD AZure

Para reduzir o risco associado a atribuições de papéis antigos, deve rever regularmente o acesso a funções privilegiadas de Azure AD.

![Screenshot que mostra a lista de "Membros de Revisão" de papéis de Azure A D.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Siga as instruções nos links abaixo:

| Artigos de procedimentos | Descrição |
| - | - |
 [Criar comentários de acesso](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Criar avaliações de acesso para funções privilegiadas da Azure AD em PIM |
| [Autorreveja o seu acesso](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Se estiver designado para uma função administrativa, aprove ou negue o acesso ao seu papel |
| [Complete uma avaliação de acesso](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Ver uma Análise de Acesso e aplicar os resultados |


### <a name="review-azure-resource-roles"></a>Rever funções de recursos do Azure

Para reduzir o risco associado a atribuições de funções velhas, deve rever regularmente o acesso a funções privilegiadas de recursos Azure. 

![rever papéis de anúncios azure](./media/deploy-access-review/9-review-azure-roles-picker.png)

Siga as instruções nos links abaixo:

| Artigos de procedimentos| Descrição |
| - | -|
| [Criar comentários de acesso](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Criar avaliações de acesso para funções privilegiadas de recursos Azure em PIM |
| [Autorreveja o seu acesso](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Se estiver designado para uma função administrativa, aprove ou negue o acesso ao seu papel |
| [Complete uma avaliação de acesso](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Ver uma Análise de Acesso e aplicar os resultados |


## <a name="use-the-access-reviews-api"></a>Utilize a API de Comentários de Acesso

Consulte [os métodos de API de gráficos](/graph/api/resources/accessreviews-root?view=graph-rest-beta) e [verificações de autorização de função e pedido](/graph/api/resources/accessreviews-root?view=graph-rest-beta) para interagir e gerir recursos reexuáveis. Os métodos de Avaliação de Acesso na API do Microsoft Graph estão disponíveis tanto para contextos de aplicação como de utilizador. Ao executar scripts no contexto da aplicação, a conta utilizada para executar a API (o princípio do serviço) deve ser concedida a permissão "AccessReview.Read.All" para consultar informações de Comentários de Acesso.

As tarefas de Avaliação de Acesso Popular para automatizar utilizando a API do Gráfico para Avaliações de Acesso são:

* Criar e iniciar uma avaliação de acesso

* Termine manualmente uma Revisão de Acesso antes do seu fim programado

* Listar todos os comentários de acesso em execução e o seu estado

* Veja a história de uma série de comentários e as decisões e ações tomadas em cada revisão.

* Recolher decisões de uma Análise de Acesso

* Recolher decisões de revisões concluídas onde o revisor tomou uma decisão diferente do recomendado pelo sistema.

Ao criar novas consultas de API para automatização, recomendamos a utilização do [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer). Pode construir e explorar as suas consultas de gráficos antes de as colocar em scripts e código. Isto pode ajudá-lo a iterar rapidamente a sua consulta para obter exatamente os resultados que procura, sem alterar o código do seu script.

## <a name="monitor-access-reviews"></a>MonitorIze comentários de acesso

As atividades de Access Reviews são registadas e disponíveis nos [registos de auditoria da Azure AD.](../reports-monitoring/concept-audit-logs.md) Pode filtrar os dados de auditoria na categoria, tipo de atividade e intervalo de data. Aqui está uma consulta de amostra:

| Categoria| Política |
| - | - |
| Tipo de atividade| Criar revisão de acesso |
| | Atualizar a revisão do acesso |
| | Análise de Acesso encerrada |
| | Eliminar revisão de acesso |
| | Aprovar decisão |
| | Negar decisão |
| | Reposição da decisão |
| | Aplicar decisão |
| Intervalo de datas| sete dias |


Para consultas mais avançadas e análise de Avaliações de Acesso, e para acompanhar as alterações e a conclusão de avaliações, recomendamos que exporte os seus Registos de Auditoria AZure AD para [Azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) ou Azure Event Hub. Quando armazenado no Azure Log Analytics, pode utilizar a [poderosa linguagem analítica](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) – e construir os seus próprios dashboards.

## <a name="next-steps"></a>Passos seguintes

Conheça as tecnologias abaixo relacionadas.

* [O que é Azure AD Entitlement Management?](entitlement-management-overview.md)

* [O que é o Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)

