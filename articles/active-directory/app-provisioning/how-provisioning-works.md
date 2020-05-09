---
title: Entenda como funciona o fornecimento de Anúncios Azure [ Microsoft Docs
description: Entenda como funciona o fornecimento de anúncios Azure
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 7ee685da3492b6915a687151beea3e82e46185de
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593731"
---
# <a name="how-provisioning-works"></a>Como funciona o aprovisionamento

O fornecimento automático refere-se à criação de identidades e funções de utilizador nas aplicações na nuvem a que os utilizadores precisam de ter acesso. Além de criar identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como alteração de estado ou funções. Antes de iniciar uma implementação, pode rever este artigo para saber como funciona a provisão da Azure AD e obter recomendações de configuração. 

O Serviço de Provisionamento de **AD Azure** fornece aos utilizadores aplicações SaaS e outros sistemas através da ligação a um Sistema de Gestão de Identidade de Domínio Transversal (SCIM) 2.0 ponto final de gestão de utilizadores Fornecido pelo fornecedor de aplicações. Este ponto final do SCIM permite que o Azure AD crie, atualize e remova os utilizadores programáticamente. Para aplicações selecionadas, o serviço de provisionamento também pode criar, atualizar e remover objetos adicionais relacionados com a identidade, tais como grupos e funções. O canal utilizado para o fornecimento entre a AD Azure e a aplicação é encriptado utilizando encriptação HTTPS TLS 1.2.


![Azure AD Provisioning Service](./media/how-provisioning-works/provisioning0.PNG)
Figure*1: The Azure AD Provisioning Service*

![Fluxo de trabalho](./media/how-provisioning-works/provisioning1.PNG)
de fornecimento de utilizadores de saída Figura*2: fluxo de trabalho de fornecimento de utilizadores "outbound" de Azure AD para aplicações populares do SaaS*

![Fluxo de trabalho](./media/how-provisioning-works/provisioning2.PNG)
de fornecimento de utilizadores de entrada Figura 3: Fluxo de trabalho de fornecimento de utilizadores *"Inbound" de aplicações populares de Gestão de Capital Humano (HCM) para O Diretório Ativo azure e Diretório Ativo do Servidor Windows*

## <a name="provisioning-using-scim-20"></a>Provisionamento utilizando o SCIM 2.0

O serviço de provisionamento Da Azure AD utiliza o [protocolo SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) para o provisionamento automático. O serviço liga-se ao ponto final do SCIM para a aplicação e utiliza o esquema de objetos de utilizador SCIM e ASAP REST para automatizar o fornecimento e desprovisionamento de utilizadores e grupos. Um conector de provisionamento baseado em SCIM é fornecido para a maioria das aplicações na galeria Azure AD. Ao construir aplicativos para a AD Azure, os desenvolvedores podem usar a API de gestão de utilizadores SCIM 2.0 para construir um ponto final SCIM que integra a Azure AD para o provisionamento. Para mais detalhes, consulte [Construir um ponto final Do SCIM e configurar o fornecimento do utilizador](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Para solicitar um conector de fornecimento automático de AD para uma aplicação que não tenha atualmente uma, preencha um Pedido de Aplicação de [Diretório Ativo Azure](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Autorização

São necessárias credenciais para que a AD Azure se conectem à API de gestão de utilizadores da aplicação. Enquanto estiver a configurar o fornecimento automático de utilizadores para uma aplicação, terá de introduzir credenciais válidas. Pode encontrar tipos e requisitos credenciais para a aplicação, referindo-se ao tutorial da aplicação. No portal Azure, poderá testar as credenciais fazendo com que a Azure AD tente ligar-se à aplicação de provisionamento da aplicação utilizando as credenciais fornecidas.

Se o único sign-on baseado em SAML também estiver configurado para a aplicação, o limite interno de armazenamento por aplicação da Azure AD é de 1024 bytes. Este limite inclui todos os certificados, fichas secretas, credenciais e dados de configuração relacionados associados a uma única instância de uma aplicação (também conhecido como registo principal de serviço em Azure AD). Quando o sinal único baseado em SAML é configurado, o certificado usado para assinar as fichas SAML consome frequentemente mais de 50% do espaço. Quaisquer itens adicionais (fichas secretas, URIs, endereços de e-mail de notificação, nomes de utilizador e palavras-passe) que introduza durante a configuração do fornecimento do utilizador podem exceder o limite de armazenamento. Para obter mais informações, consulte as credenciais do administrador de [poupança de problemas enquanto configura o fornecimento do utilizador](../manage-apps/application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Atributos de mapeamento

Ao ativar o fornecimento de utilizadores para uma aplicação SaaS de terceiros, o portal Azure controla os seus valores de atributo através de mapeamentos de atributos. Os mapeamentos determinam os atributos do utilizador que fluem entre a AD Azure e a aplicação-alvo quando as contas do utilizador são aprovisionadas ou atualizadas.

Há um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de utilizador da AD Azure e os objetos de utilizador de cada aplicação SaaS. Algumas aplicações gerem outros tipos de objetos juntamente com os Utilizadores, como grupos.

Ao configurar o fornecimento, é importante rever e configurar os mapeamentos e fluxos de trabalho que definem quais as propriedades do utilizador (ou grupo) que fluem da AD Azure para a aplicação. Reveja e configure a propriedade correspondente **(Match objects using this attribute**) que é usada para identificar e combinar exclusivamente utilizadores/grupos entre os dois sistemas.

Pode personalizar os mapeamentos de atributos padrão de acordo com as suas necessidades de negócio. Assim, pode alterar ou eliminar os mapeamentos de atributos existentes, ou criar novos mapeamentos de atributos. Para mais detalhes, consulte personalizar [o fornecimento de atributos para aplicações SaaS](../manage-apps/customize-application-attributes.md).

Ao configurar o fornecimento a uma aplicação SaaS, um dos tipos de mapeamento de atributos que pode especificar é um mapeamento de expressão. Para estes mapeamentos, deve escrever uma expressão semelhante a um guião que lhe permita transformar os dados dos seus utilizadores em formatos mais aceitáveis para a aplicação SaaS. Para mais detalhes, consulte [expressões de escrita para mapeamento de atributos](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Scoping 
### <a name="assignment-based-scoping"></a>Scoping baseado em atribuição

Para o fornecimento de saída da Azure AD a uma aplicação SaaS, confiar nas [atribuições](../manage-apps/assign-user-or-group-access-portal.md) de utilizador ou grupo é a forma mais comum de determinar quais os utilizadores que estão no âmbito do provisionamento. Uma vez que as atribuições do utilizador também são utilizadas para permitir uma única inscrição, o mesmo método pode ser utilizado para gerir tanto o acesso como o provisionamento. A deteção baseada em atribuição não se aplica a cenários de fornecimento de entrada, tais como Workday e Successfactors.

* **Grupos, grupos.** Com um plano de licença Azure AD Premium, pode utilizar grupos para atribuir acesso a uma aplicação SaaS. Em seguida, quando o âmbito de provisionamento for definido para **Sync apenas utilizadores e grupos atribuídos**, o serviço de provisionamento AD Azure fornecerá ou desprovisionará utilizadores com base no facto de serem membros de um grupo que é atribuído à aplicação. O objeto de grupo em si não é provisionado a menos que a aplicação suporte objetos de grupo. Certifique-se de que os grupos atribuídos à sua aplicação têm o imóvel "SecurityEnabled" definido para "Falso".

* **Grupos dinâmicos.** O serviço de fornecimento de utilizadores da AD Azure pode ler e fornecer utilizadores em [grupos dinâmicos.](../users-groups-roles/groups-create-rule.md) Tenha em mente estas ressalvas e recomendações:

  * Os grupos dinâmicos podem ter impacto no desempenho do fornecimento de ponta a ponta das aplicações Azure AD para SaaS.

  * A rapidez com que um utilizador num grupo dinâmico é aprovisionado ou desprovisionado numa aplicação SaaS depende da rapidez com que o grupo dinâmico pode avaliar as alterações de adesão. Para obter informações sobre como verificar o estado de processamento de um grupo dinâmico, consulte o estado de [processamento de verificação de uma regra de adesão](../users-groups-roles/groups-create-rule.md).

  * Quando um utilizador perde a adesão ao grupo dinâmico, é considerado um evento de desprovisionamento. Considere este cenário ao criar regras para grupos dinâmicos.

* **Grupos aninhados.** O serviço de fornecimento de utilizadores da AD Azure não pode ler ou fornecer utilizadores em grupos aninhados. O serviço só pode ler e fornecer utilizadores que sejam membros imediatos de um grupo explicitamente designado. Esta limitação das "atribuições baseadas em grupo às aplicações" também afeta o único sign-on (ver [Utilizar um grupo para gerir o acesso às aplicações SaaS).](../users-groups-roles/groups-saasapps.md) Em vez disso, atribuir diretamente ou de outra forma [o âmbito nos](define-conditional-rules-for-provisioning-user-accounts.md) grupos que contêm os utilizadores que precisam de ser aprovisionados.

### <a name="attribute-based-scoping"></a>Deteção baseada em atributos 

Pode utilizar filtros de deteção para definir regras baseadas em atributos que determinam quais os utilizadores que são provisionados a uma aplicação. Este método é comumente utilizado para o fornecimento de entrada a partir de aplicações HCM para Azure AD e Ative Directory. Os filtros de deteção são configurados como parte dos mapeamentos de atributos para cada conector de fornecimento de utilizadores da AD Azure. Para mais detalhes sobre a configuração dos filtros de deteção baseados em atributos, consulte o [fornecimento de aplicações baseada no Atributo com filtros](define-conditional-rules-for-provisioning-user-accounts.md)de deteção .

### <a name="b2b-guest-users"></a>Utilizadores B2B (convidado)

É possível utilizar o serviço de fornecimento de utilizadores Da Azure AD para fornecer utilizadores B2B (ou convidado) em aplicações Azure AD para SaaS. No entanto, para que os utilizadores b2B inscrevam-se na aplicação SaaS utilizando o Azure AD, a aplicação SaaS deve ter a sua capacidade de inscrição única baseada no SAML configurada de forma específica. Para obter mais informações sobre como configurar as aplicações SaaS para suportar os sign-ins dos utilizadores B2B, consulte [as aplicações Configure SaaS para colaboração B2B](../b2b/configure-saas-apps.md).

Note que o nome principal do utilizador para um utilizador convidado@domain.comé frequentemente armazenado como "alias#EXT# ". quando o nome principal do utilizador é incluído nos mapeamentos do seu atributo como um atributo de origem, o #EXT# é retirado do nome principal do utilizador. Se necessitar que o #EXT# esteja presente, substitua o userPrincipalName por originalUserPrincipalName como atributo de origem. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Ciclos de provisionamento: Inicial e incremental

Quando o Azure AD é o sistema de origem, o serviço de provisionamento utiliza a [consulta delta use para rastrear alterações nos dados](https://docs.microsoft.com/graph/delta-query-overview) do Microsoft Graph para monitorizar utilizadores e grupos. O serviço de provisionamento executa um ciclo inicial contra o sistema de origem e o sistema de alvo, seguido de ciclos incrementais periódicos.

### <a name="initial-cycle"></a>Ciclo inicial

Quando o serviço de provisionamento for iniciado, o primeiro ciclo:

1. Consulta a todos os utilizadores e grupos do sistema de origem, recuperando todos os atributos definidos nos [mapeamentos de atributos.](customize-application-attributes.md)

2. Filtrar os utilizadores e grupos devolvidos, utilizando quaisquer [atribuições](../manage-apps/assign-user-or-group-access-portal.md) configuradas ou [filtros de deteção baseados em atributos](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando um utilizador é atribuído ou no âmbito do fornecimento, o serviço questiona o sistema de destino para um utilizador correspondente utilizando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties)especificados . Exemplo: Se o nome principal do utilizador no sistema de origem for o atributo correspondente e os mapas ao userName no sistema alvo, então o serviço de provisionamento questiona o sistema de destino para userNames que correspondem aos valores de nome principal do utilizador no sistema de origem.

4. Se um utilizador correspondente não for encontrado no sistema de destino, é criado utilizando os atributos devolvidos do sistema de origem. Após a criação da conta de utilizador, o serviço de fornecimento deteta e cache o ID do sistema alvo para o novo utilizador. Este ID é usado para executar todas as operações futuras nesse utilizador.

5. Se for encontrado um utilizador correspondente, é atualizado utilizando os atributos fornecidos pelo sistema de origem. Após a correspondência da conta de utilizador, o serviço de provisionamento deteta e cache o ID do sistema alvo para o novo utilizador. Este ID é usado para executar todas as operações futuras nesse utilizador.

6. Se os mapeamentos do atributo contiverem atributos de "referência", o serviço faz atualizações adicionais no sistema alvo para criar e ligar os objetos referenciados. Por exemplo, um utilizador pode ter um atributo "Manager" no sistema de destino, que está ligado a outro utilizador criado no sistema alvo.

7. Persistir uma marca de água no final do ciclo inicial, que fornece o ponto de partida para os ciclos incrementais posteriores.

Algumas aplicações como ServiceNow, G Suite e Box suportam não só o fornecimento de utilizadores, mas também grupos de aprovisionamento e seus membros. Nesses casos, se o fornecimento de grupo supor nos [mapeamentos,](customize-application-attributes.md)o serviço de provisionamento sincroniza os utilizadores e os grupos e, posteriormente, sincroniza os membros do grupo.

### <a name="incremental-cycles"></a>Ciclos incrementais

Após o ciclo inicial, todos os outros ciclos:

1. Consultar o sistema de origem para quaisquer utilizadores e grupos que foram atualizados desde que a última marca de água foi armazenada.

2. Filtrar os utilizadores e grupos devolvidos, utilizando quaisquer [atribuições](../manage-apps/assign-user-or-group-access-portal.md) configuradas ou [filtros de deteção baseados em atributos](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando um utilizador é atribuído ou no âmbito do fornecimento, o serviço questiona o sistema de destino para um utilizador correspondente utilizando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties)especificados .

4. Se um utilizador correspondente não for encontrado no sistema de destino, é criado utilizando os atributos devolvidos do sistema de origem. Após a criação da conta de utilizador, o serviço de fornecimento deteta e cache o ID do sistema alvo para o novo utilizador. Este ID é usado para executar todas as operações futuras nesse utilizador.

5. Se for encontrado um utilizador correspondente, é atualizado utilizando os atributos fornecidos pelo sistema de origem. Se for uma conta recém-atribuída que é compatível, o serviço de provisionamento deteta e cacheo o ID do sistema alvo para o novo utilizador. Este ID é usado para executar todas as operações futuras nesse utilizador.

6. Se os mapeamentos do atributo contiverem atributos de "referência", o serviço faz atualizações adicionais no sistema alvo para criar e ligar os objetos referenciados. Por exemplo, um utilizador pode ter um atributo "Manager" no sistema de destino, que está ligado a outro utilizador criado no sistema alvo.

7. Se um utilizador que estava anteriormente no âmbito de fornecimento for removido do âmbito, incluindo não ser atribuído, o serviço desativa o utilizador no sistema-alvo através de uma atualização.

8. Se um utilizador que estava anteriormente no âmbito de fornecimento for desativado ou eliminado suavemente no sistema de origem, o serviço desativa o utilizador no sistema-alvo através de uma atualização.

9. Se um utilizador que estava anteriormente no âmbito do fornecimento for duramente eliminado no sistema de origem, o serviço elimina o utilizador no sistema-alvo. Em Azure AD, os utilizadores são eliminados duramente 30 dias após serem eliminados suavemente.

10. Persistir uma nova marca de água no final do ciclo incremental, que fornece o ponto de partida para os ciclos incrementais posteriores.

> [!NOTE]
> Pode desativar opcionalmente as operações **Create**, **Update**, or **Delete** utilizando as ações de **objetos Target** verificar caixas na secção [Mapeamentos.](customize-application-attributes.md) A lógica de desativar um utilizador durante uma atualização também é controlada através de um mapeamento de atributos de um campo como "accountEnabled".

O serviço de provisionamento continua a executar ciclos incrementais de costas para trás indefinidamente, em intervalos definidos no [tutorial específico de cada aplicação](../saas-apps/tutorial-list.md). Os ciclos incrementais continuam até que um dos seguintes eventos ocorra:

- O serviço é interrompido manualmente utilizando o portal Azure ou utilizando o comando API do Microsoft Graph.
- Um novo ciclo inicial é desencadeado utilizando o estado Clear e a opção de **reinício** no portal Azure, ou utilizando o comando API do Microsoft Graph API apropriado. Esta ação elimina qualquer marca de água armazenada e faz com que todos os objetos de origem sejam novamente avaliados.
- Um novo ciclo inicial é desencadeado devido a uma mudança nos mapeamentos de atributos ou filtros de deteção. Esta ação também limpa qualquer marca de água armazenada e faz com que todos os objetos de origem sejam novamente avaliados.
- O processo de provisionamento entra em quarentena (ver abaixo) devido a uma elevada taxa de erro, e permanece em quarentena por mais de quatro semanas. Neste caso, o serviço será automaticamente desativado.

### <a name="errors-and-retries"></a>Erros e tentativas

Se um erro no sistema alvo impedir que um utilizador individual seja adicionado, atualizado ou eliminado no sistema-alvo, a operação é novamente tentada no próximo ciclo de sincronização. Se o utilizador continuar a falhar, as repetições começarão a ocorrer numa frequência reduzida, reduzindo gradualmente para apenas uma tentativa por dia. Para resolver a falha, os administradores devem verificar os registos de [provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) para determinar a causa principal e tomar as medidas adequadas. Falhas comuns podem incluir:

- Utilizadores que não tenham um atributo povoado no sistema de origem que é exigido no sistema alvo
- Os utilizadores com um valor de atributo no sistema de origem para o qual existe uma restrição única no sistema alvo, e o mesmo valor está presente noutro registo do utilizador.

Resolver estas falhas ajustando os valores de atributo para o utilizador afetado no sistema de origem, ou ajustando os mapeamentos de atributos para que não causem conflitos.

### <a name="quarantine"></a>Quarentena

Se a maioria ou todas as chamadas que são feitas contra o sistema alvo falharem consistentemente devido a um erro (por exemplo, credenciais de administração inválidas) o trabalho de provisionamento entra num estado de "quarentena". Este estado está indicado no [relatório de](../manage-apps/check-status-user-account-provisioning.md) resumo do fornecimento e via e-mail se as notificações de e-mail foram configuradas no portal Azure.

Quando em quarentena, a frequência dos ciclos incrementais é gradualmente reduzida para uma vez por dia.

O fornecimento de emprego sai da quarentena depois de todos os erros ofensivos serem corrigidos e o próximo ciclo de sincronização começar. Se o emprego de provisionamento permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento é desativado. Saiba mais aqui sobre o estado de quarentena [aqui.](../manage-apps/application-provisioning-quarantine-status.md)

### <a name="how-long-provisioning-takes"></a>O tempo que o aprovisionamento demora

O desempenho depende se o seu trabalho de provisionamento está a executar um ciclo inicial de provisionamento ou um ciclo incremental. Para obter mais informações sobre quanto tempo o provisionamento demora e como monitorizar o estado do serviço de provisionamento, consulte [Verificar o estado do fornecimento dos utilizadores](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Como dizer se os utilizadores estão a ser devidamente provisionados

Todas as operações executadas pelo serviço de provisionamento do utilizador são registadas nos registos de provisionamento de AD Azure [(pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Os registos incluem todas as operações de leitura e escrita efetuadas para os sistemas de origem e alvo, e os dados do utilizador que foram lidos ou escritos durante cada operação. Para obter informações sobre como ler os registos de fornecimento no portal Azure, consulte o guia de informação de [provisionamento](../manage-apps/check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Desprovisionamento

O serviço de provisionamento de AD Azure mantém os sistemas de origem e alvo sincronizados através da desprovisionamento de contas quando os utilizadores já não deveriam ter acesso. 

O serviço de provisionamento da AD Azure eliminará um utilizador numa aplicação quando a aplicação suupportia eliminações suaves (pedido de atualização com ativo = falso) e qualquer um dos seguintes eventos ocorra:

* A conta de utilizador é eliminada em Azure AD
*   O utilizador não é atribuído a partir da aplicação
*   O utilizador já não encontra um filtro de deteção e sai do âmbito
    * Por predefinição, o serviço de fornecimento de AD Azure elimina ou desativa utilizadores que saem do âmbito. Se quiser anular este comportamento predefinido, pode definir uma bandeira para [saltar supressões fora do alcance](../app-provisioning/skip-out-of-scope-deletions.md).
*   A propriedade AccountEnabled está definida para falso

Se um dos quatro eventos acima ocorrer e a aplicação-alvo não suportar eliminações suaves, o serviço de fornecimento enviará um pedido DE EXCLUSÃO para eliminar permanentemente o utilizador da aplicação. 

30 dias após a eliminação de um utilizador em Azure AD, serão permanentemente eliminados do inquilino. Neste ponto, o serviço de provisionamento enviará um pedido DE ELIMINAÇÃO para eliminar permanentemente o utilizador na aplicação. A qualquer momento durante a janela de 30 dias, pode [eliminar manualmente um utilizador permanentemente](../fundamentals/active-directory-users-restore.md), que envia um pedido de exclusão para a aplicação.

Se vir um atributo IsSoftDeleted nos seus mapeamentos de atributos, é utilizado para determinar o estado do utilizador e se envia um pedido de atualização com ativo = falso para apagar suavemente o utilizador. 

## <a name="next-steps"></a>Passos Seguintes

[Planear uma implementação de aprovisionamento automático de utilizadores](../app-provisioning/plan-auto-user-provisioning.md)

[Configurar o aprovisionamento para uma aplicação da galeria](../manage-apps/configure-automatic-user-provisioning-portal.md)

[Construa um ponto final sCIM e configure o fornecimento ao criar a sua própria app](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Problemas de resolução de problemas com a configuração e o fornecimento](../manage-apps/application-provisioning-config-problem.md)de utilizadores a uma aplicação .
