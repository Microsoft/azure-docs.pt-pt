---
title: Entender como o provisionamento do Azure AD funciona | Microsoft Docs
description: Entenda como funciona o provisionamento do Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910317201275ba1598ed3e4d89815542b88fb108
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75719975"
---
# <a name="how-provisioning-works"></a>Como funciona o aprovisionamento

O provisionamento automático refere-se à criação de identidades e funções de usuário nos aplicativos de nuvem aos quais os usuários precisam acessar. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam. Antes de iniciar uma implantação, você pode examinar este artigo para saber como o provisionamento do Azure AD funciona e obter recomendações de configuração. 

O **serviço de provisionamento do Azure ad** provisiona usuários para aplicativos SaaS e outros sistemas conectando-se a um sistema para o ponto de extremidade da API de gerenciamento de usuário 2,0 do scim (gerenciamento de identidade entre domínios) fornecido pelo fornecedor do aplicativo. Esse ponto de extremidade SCIM permite que o Azure AD crie, atualize e remova programaticamente usuários. Para os aplicativos selecionados, o serviço de provisionamento também pode criar, atualizar e remover objetos adicionais relacionados à identidade, como grupos e funções. O canal usado para provisionamento entre o Azure AD e o aplicativo é criptografado usando a criptografia SSL HTTPS.


![o serviço de provisionamento do Azure AD](./media/user-provisioning/provisioning0.PNG)
*Figura 1: o serviço de provisionamento do Azure ad*

![fluxo de trabalho de provisionamento de usuário *de saída](./media/user-provisioning/provisioning1.PNG)
Figura 2: fluxo de trabalho de provisionamento de usuário "de saída" do Azure ad para aplicativos SaaS populares*

![fluxo de trabalho de provisionamento de usuário de entrada](./media/user-provisioning/provisioning2.PNG)
*Figura 3: fluxo de trabalho de provisionamento de usuário "de entrada" de aplicativos de HCM (gerenciamento de capital humano) populares para Azure Active Directory e Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Provisionamento usando o SCIM 2,0

O serviço de provisionamento do Azure AD usa o [protocolo SCIM 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) para provisionamento automático. O serviço se conecta ao ponto de extremidade do SCIM para o aplicativo e usa o esquema de objeto do usuário do SCIM e as APIs REST para automatizar o provisionamento e desprovisionamento de usuários e grupos. Um conector de provisionamento baseado em SCIM é fornecido para a maioria dos aplicativos na galeria do Azure AD. Ao criar aplicativos para o Azure AD, os desenvolvedores podem usar a API de gerenciamento de usuário do SCIM 2,0 para criar um ponto de extremidade SCIM que integre o Azure AD para provisionamento. Para obter detalhes, consulte [criar um ponto de extremidade scim e configurar o provisionamento de usuário](use-scim-to-provision-users-and-groups.md).

Para solicitar um conector de provisionamento automático do Azure AD para um aplicativo que atualmente não tem um, preencha uma [solicitação de aplicativo Azure Active Directory](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Autorização

As credenciais são necessárias para que o Azure AD se conecte à API de gerenciamento de usuários do aplicativo. Enquanto estiver configurando o provisionamento automático de usuário para um aplicativo, você precisará inserir credenciais válidas. Você pode encontrar os requisitos e tipos de credenciais para o aplicativo consultando o tutorial do aplicativo. No portal do Azure, você poderá testar as credenciais fazendo com que o Azure AD tente se conectar ao aplicativo de provisionamento do aplicativo usando as credenciais fornecidas.

Se o logon único baseado em SAML também estiver configurado para o aplicativo, o limite de armazenamento por aplicativo interno do Azure AD será de 1024 bytes. Esse limite inclui todos os certificados, tokens secretos, credenciais e dados de configuração relacionados associados a uma única instância de um aplicativo (também conhecido como registro de entidade de serviço no Azure AD). Quando o logon único baseado em SAML é configurado, o certificado usado para assinar os tokens SAML geralmente consome mais de 50% por cento do espaço. Quaisquer itens adicionais (tokens secretos, URIs, endereços de email de notificação, nomes de usuário e senhas) inseridos durante a configuração de provisionamento do usuário podem exceder o limite de armazenamento. Para obter mais informações, consulte [problema ao salvar as credenciais de administrador ao configurar o provisionamento do usuário](application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Atributos de mapeamento

Quando você habilita o provisionamento de usuário para um aplicativo SaaS de terceiros, o portal do Azure controla seus valores de atributo por meio de mapeamentos de atributo. Os mapeamentos determinam os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino quando as contas de usuário são provisionadas ou atualizadas.

Há um conjunto pré-configurado de atributos e mapeamentos de atributo entre objetos de usuário do Azure AD e os objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos junto com os usuários, como grupos.

Ao configurar o provisionamento, é importante revisar e configurar os mapeamentos de atributo e os fluxos de trabalho que definem quais propriedades de usuário (ou grupo) fluem do Azure AD para o aplicativo. Examine e configure a propriedade correspondente (**corresponder objetos usando este atributo**) que é usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas.

Você pode personalizar os mapeamentos de atributo padrão de acordo com suas necessidades de negócios. Portanto, você pode alterar ou excluir mapeamentos de atributo existentes ou criar novos mapeamentos de atributo. Para obter detalhes, consulte [Personalizando o atributo de provisionamento de usuário-mapeamentos para aplicativos SaaS](customize-application-attributes.md).

Quando configurar o aprovisionamento a uma aplicação SaaS, um dos tipos de mapeamentos de atributos que pode especificar é um mapeamento de expressão. Para esses mapeamentos, você deve escrever uma expressão do tipo script que permite transformar os dados dos usuários em formatos que são mais aceitáveis para o aplicativo SaaS. Para obter detalhes, consulte [escrevendo expressões para mapeamentos de atributo](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Cedido 
### <a name="assignment-based-scoping"></a>Escopo baseado em atribuição

Para o provisionamento de saída do Azure AD para um aplicativo SaaS, depender de [atribuições de usuário ou de grupo](assign-user-or-group-access-portal.md) é a maneira mais comum de determinar quais usuários estão no escopo do provisionamento. Como as atribuições de usuário também são usadas para habilitar o logon único, o mesmo método pode ser usado para gerenciar o acesso e o provisionamento. O escopo baseado em atribuição não se aplica a cenários de provisionamento de entrada como workday e Successfactors.

* **Grupos.** Com um plano de licença Azure AD Premium, você pode usar grupos para atribuir acesso a um aplicativo SaaS. Em seguida, quando o escopo de provisionamento for definido para **sincronizar somente usuários e grupos atribuídos**, o serviço de provisionamento do Azure ad provisionará ou desprovisionará usuários com base no fato de eles serem membros de um grupo atribuído ao aplicativo. O próprio objeto Group não é provisionado, a menos que o aplicativo dê suporte a objetos Group.

* **Grupos dinâmicos.** O serviço de provisionamento de usuários do Azure AD pode ler e provisionar usuários em [grupos dinâmicos](../users-groups-roles/groups-create-rule.md). Mantenha essas advertências e recomendações em mente:

  * Grupos dinâmicos podem afetar o desempenho do provisionamento de ponta a ponta do Azure AD para aplicativos SaaS.

  * A velocidade com que um usuário em um grupo dinâmico é provisionado ou desprovisionado em um aplicativo SaaS depende de quão rápido o grupo dinâmico pode avaliar as alterações de associação. Para obter informações sobre como verificar o status de processamento de um grupo dinâmico, consulte [verificar o status de processamento de uma regra de associação](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

  * Quando um usuário perde a associação no grupo dinâmico, ele é considerado um evento de desprovisionamento. Considere este cenário ao criar regras para grupos dinâmicos.

* **Grupos aninhados.** O serviço de provisionamento de usuário do Azure AD não pode ler ou provisionar usuários em grupos aninhados. O serviço só pode ler e provisionar usuários que são membros imediatos de um grupo explicitamente atribuído. Essa limitação de "atribuições baseadas em grupo a aplicativos" também afeta o logon único (consulte [usando um grupo para gerenciar o acesso a aplicativos SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps)). Em vez disso, atribua ou então o [escopo diretamente nos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) grupos que contêm os usuários que precisam ser provisionados.

### <a name="attribute-based-scoping"></a>Escopo baseado em atributo 

Você pode usar filtros de escopo para definir regras baseadas em atributo que determinam quais usuários são provisionados para um aplicativo. Esse método é usado normalmente para o provisionamento de entrada de aplicativos HCM para o Azure AD e Active Directory. Os filtros de escopo são configurados como parte dos mapeamentos de atributo para cada conector de provisionamento de usuário do Azure AD. Para obter detalhes sobre como configurar filtros de escopo com base em atributo, consulte [provisionamento de aplicativo baseado em atributo com filtros de escopo](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>Usuários B2B (convidados)

É possível usar o serviço de provisionamento de usuário do Azure AD para provisionar usuários B2B (ou convidados) no Azure AD para aplicativos SaaS. No entanto, para que os usuários B2B entrem no aplicativo SaaS usando o Azure AD, o aplicativo SaaS deve ter seu recurso de logon único baseado em SAML configurado de forma específica. Para obter mais informações sobre como configurar aplicativos SaaS para dar suporte a entradas de usuários B2B, consulte [configurar aplicativos SaaS para colaboração B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

## <a name="provisioning-cycles-initial-and-incremental"></a>Ciclos de provisionamento: inicial e incremental

Quando o Azure AD é o sistema de origem, o serviço de provisionamento usa o [recurso de consulta diferencial do API do Graph do Azure ad](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) para monitorar usuários e grupos. O serviço de provisionamento executa um ciclo inicial no sistema de origem e no sistema de destino, seguido por ciclos incrementais periódicos.

### <a name="initial-cycle"></a>Ciclo inicial

Quando o serviço de provisionamento for iniciado, o primeiro ciclo irá:

1. Consultar todos os usuários e grupos do sistema de origem, recuperando todos os atributos definidos nos [mapeamentos de atributo](customize-application-attributes.md).

2. Filtre os usuários e grupos retornados, usando quaisquer [atribuições](assign-user-or-group-access-portal.md) configuradas ou [filtros de escopo baseados em atributos](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando um usuário é atribuído ou está no escopo para provisionamento, o serviço consulta o sistema de destino para um usuário correspondente usando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties)especificados. Exemplo: se o nome do userPrincipal no sistema de origem for o atributo correspondente e for mapeado para userName no sistema de destino, o serviço de provisionamento consultará o sistema de destino em busca de nomes de usuário que correspondam aos valores de nome userPrincipal no sistema de origem.

4. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta e armazena em cache a ID do sistema de destino para o novo usuário. Essa ID é usada para executar todas as operações futuras nesse usuário.

5. Se um usuário correspondente for encontrado, ele será atualizado usando os atributos fornecidos pelo sistema de origem. Depois que a conta de usuário for correspondida, o serviço de provisionamento detectará e armazenará em cache a ID do sistema de destino para o novo usuário. Essa ID é usada para executar todas as operações futuras nesse usuário.

6. Se os mapeamentos de atributo contiverem atributos de "referência", o serviço fará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Gerenciador" no sistema de destino, que é vinculado a outro usuário criado no sistema de destino.

7. Mantenha uma marca-d ' água no final do ciclo inicial, que fornece o ponto de partida para os ciclos incrementais posteriores.

Alguns aplicativos, como ServiceNow, G Suite e Box, oferecem suporte não apenas ao provisionamento de usuários, mas também ao provisionamento de grupos e de seus membros. Nesses casos, se o provisionamento de grupo estiver habilitado nos [mapeamentos](customize-application-attributes.md), o serviço de provisionamento sincronizará os usuários e os grupos e, posteriormente, sincronizará as associações de grupo.

### <a name="incremental-cycles"></a>Ciclos incrementais

Após o ciclo inicial, todos os outros ciclos irão:

1. Consulte o sistema de origem para todos os usuários e grupos que foram atualizados desde que a última marca d' água foi armazenada.

2. Filtre os usuários e grupos retornados, usando quaisquer [atribuições](assign-user-or-group-access-portal.md) configuradas ou [filtros de escopo baseados em atributos](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando um usuário é atribuído ou está no escopo para provisionamento, o serviço consulta o sistema de destino para um usuário correspondente usando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties)especificados.

4. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta e armazena em cache a ID do sistema de destino para o novo usuário. Essa ID é usada para executar todas as operações futuras nesse usuário.

5. Se um usuário correspondente for encontrado, ele será atualizado usando os atributos fornecidos pelo sistema de origem. Se for uma conta atribuída recentemente que seja correspondida, o serviço de provisionamento detectará e armazenará em cache a ID do sistema de destino para o novo usuário. Essa ID é usada para executar todas as operações futuras nesse usuário.

6. Se os mapeamentos de atributo contiverem atributos de "referência", o serviço fará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Gerenciador" no sistema de destino, que é vinculado a outro usuário criado no sistema de destino.

7. Se um usuário que estava anteriormente no escopo para provisionamento for removido do escopo, incluindo a não atribuição, o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.

8. Se um usuário que estava anteriormente no escopo para provisionamento estiver desabilitado ou excluído de maneira reversível no sistema de origem, o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.

9. Se um usuário que estava anteriormente no escopo para provisionamento for excluído do sistema de origem, o serviço excluirá o usuário no sistema de destino. No Azure AD, os usuários são excluídos por 30 dias após serem excluídos de maneira reversível.

10. Mantenha uma nova marca d' água no final do ciclo incremental, que fornece o ponto de partida para os ciclos incrementais posteriores.

> [!NOTE]
> Opcionalmente, você pode desabilitar as caixas de seleção **criar**, **Atualizar**ou **excluir** usando as **ações do objeto de destino** na seção [mapeamentos](customize-application-attributes.md) . A lógica para desabilitar um usuário durante uma atualização também é controlada por meio de um mapeamento de atributo de um campo como "accountEnabled".

O serviço de provisionamento continua executando ciclos incrementais back-to-back indefinidamente, em intervalos definidos no [tutorial específico de cada aplicativo](../saas-apps/tutorial-list.md). Os ciclos incrementais continuam até que ocorra um dos seguintes eventos:

- O serviço é interrompido manualmente usando o portal do Azure ou usando o comando API do Graph apropriado 
- Um novo ciclo inicial é disparado usando a opção **limpar estado e reiniciar** na portal do Azure ou usando o comando API do Graph apropriado. Esta ação limpa qualquer marca d' água armazenada e faz com que todos os objetos de origem sejam avaliados novamente.
- Um novo ciclo inicial é disparado devido a uma alteração nos mapeamentos de atributo ou filtros de escopo. Essa ação também limpa qualquer marca-d ' água armazenada e faz com que todos os objetos de origem sejam avaliados novamente.
- O processo de provisionamento entra em quarentena (veja abaixo) devido a uma alta taxa de erros e permanece em quarentena por mais de quatro semanas. Nesse evento, o serviço será desabilitado automaticamente.

### <a name="errors-and-retries"></a>Erros e novas tentativas

Se um erro no sistema de destino impedir que um usuário individual seja adicionado, atualizado ou excluído no sistema de destino, a operação será repetida no próximo ciclo de sincronização. Se o usuário continuar a falhar, as novas tentativas começarão a ocorrer com uma frequência reduzida, redimensionando gradualmente para apenas uma tentativa por dia. Para resolver a falha, os administradores devem verificar os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) para determinar a causa raiz e executar a ação apropriada. As falhas comuns podem incluir:

- Usuários que não têm um atributo preenchido no sistema de origem que é necessário no sistema de destino
- Os usuários têm um valor de atributo no sistema de origem para o qual há uma restrição exclusiva no sistema de destino e o mesmo valor está presente em outro registro de usuário

Resolva essas falhas ajustando os valores de atributo para o usuário afetado no sistema de origem ou ajustando os mapeamentos de atributo para que eles não causem conflitos.

### <a name="quarantine"></a>Quarentena

Se a maioria ou todas as chamadas feitas no sistema de destino falharem consistentemente devido a um erro (por exemplo, credenciais de administrador inválidas), o trabalho de provisionamento entrará em um estado de "quarentena". Esse estado é indicado no [relatório de Resumo de provisionamento](check-status-user-account-provisioning.md) e por email se as notificações por email tiverem sido configuradas no portal do Azure.

Quando em quarentena, a frequência de ciclos incrementais é reduzida gradualmente para uma vez por dia.

O trabalho de provisionamento sai da quarentena depois que todos os erros incorretos são corrigidos e o próximo ciclo de sincronização é iniciado. Se o trabalho de provisionamento permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento será desabilitado. Saiba mais aqui sobre o status de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

### <a name="how-long-provisioning-takes"></a>O tempo que o aprovisionamento demora

O desempenho depende se o seu trabalho de provisionamento está executando um ciclo de provisionamento inicial ou um ciclo incremental. Para obter detalhes sobre quanto tempo o provisionamento leva e como monitorar o status do serviço de provisionamento, consulte [verificar o status do provisionamento do usuário](application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Como saber se os usuários estão sendo provisionados corretamente

Todas as operações executadas pelo serviço de provisionamento de usuário são registradas nos logs de provisionamento do Azure AD [(versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Os logs incluem todas as operações de leitura e gravação feitas nos sistemas de origem e de destino e os dados de usuário que foram lidos ou gravados durante cada operação. Para obter informações sobre como ler os logs de provisionamento no portal do Azure, consulte o guia de [relatórios de provisionamento](check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Desprovisionamento

O serviço de provisionamento do Azure AD mantém os sistemas de origem e de destino sincronizados por contas de provisionamento quando os usuários não devem mais ter acesso. 

O serviço de provisionamento do Azure AD excluirá de forma reversível um usuário em um aplicativo quando o aplicativo suupports as exclusões reversívels (solicitação de atualização com Active = false) e qualquer um dos seguintes eventos ocorrer:

* A conta de usuário é excluída no Azure AD
*   O usuário não está atribuído do aplicativo
*   O usuário não atende mais a um filtro de escopo e sai do escopo
    * Por padrão, o serviço de provisionamento do Azure AD exclui ou desabilita usuários que saem do escopo. Se você quiser substituir esse comportamento padrão, poderá definir um sinalizador para [ignorar exclusões fora do escopo](skip-out-of-scope-deletions.md).
*   A propriedade AccountEnabled está definida como false

Se um dos quatro eventos acima ocorrer e o aplicativo de destino não oferecer suporte a exclusões reversível, o serviço de provisionamento enviará uma solicitação de exclusão para excluir permanentemente o usuário do aplicativo. 

30 dias após a exclusão de um usuário no Azure AD, eles serão excluídos permanentemente do locatário. Neste ponto, o serviço de provisionamento enviará uma solicitação de exclusão para excluir permanentemente o usuário no aplicativo. A qualquer momento durante o período de 30 dias, você pode [excluir manualmente um usuário permanentemente]( https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore), o que envia uma solicitação de exclusão para o aplicativo.

Se você vir um atributo IsSoftDeleted em seus mapeamentos de atributo, ele será usado para determinar o estado do usuário e se deseja enviar uma solicitação de atualização com Active = false para excluir a exclusão reversível do usuário. 

## <a name="next-steps"></a>Próximos Passos

[Planejar uma implantação de provisionamento de usuário automático](plan-auto-user-provisioning.md)

[Configurar o provisionamento para um aplicativo de galeria](configure-automatic-user-provisioning-portal.md)

[Criar um ponto de extremidade SCIM e configurar o provisionamento ao criar seu próprio aplicativo](use-scim-to-provision-users-and-groups.md)

[Solucionar problemas com a configuração e o provisionamento de usuários para um aplicativo](application-provisioning-config-problem.md).
