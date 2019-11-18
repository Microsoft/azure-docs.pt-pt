---
title: Provisionamento de usuário de aplicativo SaaS automatizado no Azure AD | Microsoft Docs
description: Uma introdução a como você pode usar o Azure AD para provisionar, desprovisionar e atualizar continuamente as contas de usuário em vários aplicativos SaaS de terceiros.
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
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144532"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos com Azure Active Directory

No Azure Active Directory (AD do Azure), o termo **provisionamento de aplicativo** refere-se à criação automática de identidades de usuário e funções nos aplicativos de nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) aos quais os usuários precisam acessar. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam. Os cenários comuns incluem o provisionamento de um usuário do Azure AD em aplicativos como [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)e muito mais.

![Diagrama de visão geral de provisionamento](media/user-provisioning/provisioning-overview.png)

Esse recurso permite que você:

- **Automatizar o provisionamento**: crie automaticamente novas contas nos sistemas certos para novas pessoas ao ingressarem em sua equipe ou organização.
- **Automatizar o desprovisionamento:** Desative automaticamente as contas nos sistemas certos quando as pessoas saírem da equipe ou da organização.
- **Sincronizar dados entre sistemas:** Certifique-se de que as identidades em seus aplicativos e sistemas sejam mantidas atualizadas com base nas alterações no diretório ou no sistema de recursos humanos.
- **Provisionar grupos:** Provisione grupos para aplicativos que dão suporte a eles.
- **Controle de acesso:** Monitore e audite quem foi provisionado em seus aplicativos.
- **Implantar diretamente em cenários de campo marrom:** Combine identidades existentes entre sistemas e permita uma fácil integração, mesmo quando os usuários já existem no sistema de destino.
- **Usar personalização avançada:** Aproveite os mapeamentos de atributo personalizáveis que definem quais dados de usuário devem fluir do sistema de origem para o sistema de destino.
- **Obter alertas para eventos críticos:** O serviço de provisionamento fornece alertas para eventos críticos e permite a integração de Log Analytics em que você pode definir alertas personalizados para atender às suas necessidades de negócios.

## <a name="benefits-of-automatic-provisioning"></a>Benefícios do provisionamento automático

Como o número de aplicativos usados em organizações modernas continua crescendo, os administradores de ti são tarefa com gerenciamento de acesso em escala. Padrões como SAML (Security Asserties Markup Language) ou OIDC (Open ID Connect) permitem que os administradores configurem rapidamente o SSO (logon único), mas o acesso também exige que os usuários sejam provisionados no aplicativo. Para muitos administradores, o provisionamento significa criar manualmente cada conta de usuário ou carregar arquivos CSV a cada semana, mas esses processos são demorados, caros e sujeitos a erros. Soluções como JIT (just-in-time) do SAML foram adotadas para automatizar o provisionamento, mas as empresas também precisam de uma solução para desprovisionar usuários quando deixam a organização ou não precisam mais de acesso a determinados aplicativos com base na alteração de função.

Algumas motivações comuns para usar o provisionamento automático incluem:

- Maximizar a eficiência e a precisão dos processos de provisionamento.
- Economizando os custos associados à hospedagem e manutenção de soluções e scripts de provisionamento desenvolvidos com personalização.
- Proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles deixam a organização.
- Importar facilmente um grande número de usuários para um sistema ou aplicativo SaaS específico.
- Ter um único conjunto de políticas para determinar quem é provisionado e quem pode entrar em um aplicativo.

O provisionamento de usuários do Azure AD pode ajudar a resolver esses desafios. Para saber mais sobre como os clientes usam o provisionamento de usuário do Azure AD, você pode ler o [estudo de caso do Asos](https://aka.ms/asoscasestudy). O vídeo abaixo fornece uma visão geral do provisionamento de usuário no Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quais aplicativos e sistemas posso usar com o provisionamento automático de usuário do Azure AD?

O Azure AD apresenta suporte integrado para muitos sistemas de recursos humanos e aplicativos SaaS populares e suporte genérico para aplicativos que implementam partes específicas do [padrão SCIM 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Aplicativos previamente integrados (aplicativos SaaS de galeria)** . Você pode encontrar todos os aplicativos para os quais o Azure AD dá suporte a um conector de provisionamento previamente integrado na [lista de tutoriais de aplicativos para provisionamento de usuários](../saas-apps/tutorial-list.md). Os aplicativos previamente integrados listados na Galeria geralmente usam APIs de gerenciamento de usuário com base em SCIM 2,0 para provisionamento. 

   ![Logotipo do Salesforce](media/user-provisioning/gallery-app-logos.png)

   Se você quiser solicitar um novo aplicativo para provisionamento, poderá [solicitar que seu aplicativo seja integrado à nossa galeria de aplicativos](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing). Para uma solicitação de provisionamento de usuário, exigimos que o aplicativo tenha um ponto de extremidade compatível com SCIM. Solicite que o fornecedor do aplicativo siga o padrão SCIM para que possamos integrar o aplicativo à nossa plataforma rapidamente.

* **Aplicativos que dão suporte ao SCIM 2,0**. Para obter informações sobre como se conectar genericamente a aplicativos que implementam APIs de gerenciamento de usuário baseadas em SCIM 2,0, consulte [usando o scim para provisionar automaticamente usuários e grupos de Azure Active Directory para aplicativos](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-scim"></a>O que é o SCIM?

Para ajudar a automatizar o provisionamento e o desprovisionamento, os aplicativos expõem APIs de usuário e de grupo proprietárias. No entanto, qualquer pessoa que tentasse gerenciar usuários em mais de um aplicativo informará que cada aplicativo tenta executar as mesmas ações simples, como criar ou atualizar usuários, adicionar usuários a grupos ou desprovisionar usuários. Ainda assim, todas essas ações simples são implementadas de forma um pouco diferente, usando diferentes caminhos de ponto de extremidade, métodos diferentes para especificar informações de usuário e um esquema diferente para representar cada elemento de informações.

Para resolver esses desafios, a especificação SCIM fornece um esquema de usuário comum para ajudar os usuários a migrar, de e para os aplicativos. O SCIM está se tornando o padrão de fato para o provisionamento e, quando usado em conjunto com os padrões de Federação como SAML ou OpenID Connect, fornece aos administradores uma solução baseada em padrões de ponta a ponta para o gerenciamento de acesso.

Para obter orientações detalhadas sobre como usar o SCIM para automatizar o provisionamento e desprovisionamento de usuários e grupos para um aplicativo, consulte [provisionamento de usuário do scim com o Azure Active Directory](use-scim-to-provision-users-and-groups.md).

## <a name="how-does-automatic-provisioning-work"></a>Como funciona o provisionamento automático?

O **serviço de provisionamento do Azure ad** provisiona usuários para aplicativos SaaS e outros sistemas conectando-se aos pontos de extremidade da API de gerenciamento de usuários fornecidos por cada fornecedor de aplicativos. Esses pontos de extremidade da API de gerenciamento de usuários permitem que o Azure AD crie, atualize e remova programaticamente usuários. Para os aplicativos selecionados, o serviço de provisionamento também pode criar, atualizar e remover objetos adicionais relacionados à identidade, como grupos e funções.

![o serviço de provisionamento do Azure AD](./media/user-provisioning/provisioning0.PNG)
*Figura 1: o serviço de provisionamento do Azure ad*

![fluxo de trabalho de provisionamento de usuário *de saída](./media/user-provisioning/provisioning1.PNG)
Figura 2: fluxo de trabalho de provisionamento de usuário "de saída" do Azure ad para aplicativos SaaS populares*

![fluxo de trabalho de provisionamento de usuário de entrada](./media/user-provisioning/provisioning2.PNG)
*Figura 3: fluxo de trabalho de provisionamento de usuário "de entrada" de aplicativos de HCM (gerenciamento de capital humano) populares para Azure Active Directory e Windows Server Active Directory*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como fazer configurar o provisionamento automático para um aplicativo?

Para aplicativos previamente integrados listados na Galeria, a orientação passo a passo está disponível para configurar o provisionamento automático. Consulte a [lista de tutoriais para aplicativos da Galeria integrada](https://docs.microsoft.com/azure/active-directory/saas-apps/). O vídeo a seguir demonstra como configurar o provisionamento automático de usuário para o SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Para outros aplicativos que dão suporte ao SCIM 2,0, siga as etapas no artigo [scim provisionamento de usuário com Azure Active Directory](use-scim-to-provision-users-and-groups.md).

## <a name="what-happens-during-provisioning"></a>O que acontece durante o provisionamento?

Quando o Azure AD é o sistema de origem, o serviço de provisionamento usa o [recurso de consulta diferencial do API do Graph do Azure ad](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) para monitorar usuários e grupos. O serviço de provisionamento executa um ciclo inicial no sistema de origem e no sistema de destino, seguido por ciclos incrementais periódicos.

### <a name="initial-cycle"></a>Ciclo inicial

Quando o serviço de provisionamento for iniciado, a primeira sincronização já será executada:

1. Consultar todos os usuários e grupos do sistema de origem, recuperando todos os atributos definidos nos [mapeamentos de atributo](customize-application-attributes.md).
1. Filtre os usuários e grupos retornados, usando quaisquer [atribuições](assign-user-or-group-access-portal.md) configuradas ou [filtros de escopo baseados em atributos](define-conditional-rules-for-provisioning-user-accounts.md).
1. Quando um usuário é atribuído ou está no escopo para provisionamento, o serviço consulta o sistema de destino para um usuário correspondente usando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties)especificados. Exemplo: se o nome do userPrincipal no sistema de origem for o atributo correspondente e for mapeado para userName no sistema de destino, o serviço de provisionamento consultará o sistema de destino em busca de nomes de usuário que correspondam aos valores de nome userPrincipal no sistema de origem.
1. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta e armazena em cache a ID do sistema de destino para o novo usuário, que é usado para executar todas as operações futuras nesse usuário.
1. Se um usuário correspondente for encontrado, ele será atualizado usando os atributos fornecidos pelo sistema de origem. Após a correspondência da conta de usuário, o serviço de provisionamento detecta e armazena em cache a ID do sistema de destino para o novo usuário, que é usado para executar todas as operações futuras nesse usuário.
1. Se os mapeamentos de atributo contiverem atributos de "referência", o serviço fará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Gerenciador" no sistema de destino, que é vinculado a outro usuário criado no sistema de destino.
1. Mantenha uma marca-d ' água no final do ciclo inicial, que fornece o ponto de partida para os ciclos incrementais posteriores.

Alguns aplicativos, como ServiceNow, G Suite e Box, oferecem suporte não apenas ao provisionamento de usuários, mas também ao provisionamento de grupos e de seus membros. Nesses casos, se o provisionamento de grupo estiver habilitado nos [mapeamentos](customize-application-attributes.md), o serviço de provisionamento sincronizará os usuários e os grupos e, posteriormente, sincronizará as associações de grupo.

### <a name="incremental-cycles"></a>Ciclos incrementais

Após o ciclo inicial, todos os outros ciclos irão:

1. Consulte o sistema de origem para todos os usuários e grupos que foram atualizados desde que a última marca d' água foi armazenada.
1. Filtre os usuários e grupos retornados, usando quaisquer [atribuições](assign-user-or-group-access-portal.md) configuradas ou [filtros de escopo baseados em atributos](define-conditional-rules-for-provisioning-user-accounts.md).
1. Quando um usuário é atribuído ou está no escopo para provisionamento, o serviço consulta o sistema de destino para um usuário correspondente usando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties)especificados.
1. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta e armazena em cache a ID do sistema de destino para o novo usuário, que é usado para executar todas as operações futuras nesse usuário.
1. Se um usuário correspondente for encontrado, ele será atualizado usando os atributos fornecidos pelo sistema de origem. Se for uma conta atribuída recentemente que é correspondida, o serviço de provisionamento detecta e armazena em cache a ID do sistema de destino para o novo usuário, que é usado para executar todas as operações futuras nesse usuário.
1. Se os mapeamentos de atributo contiverem atributos de "referência", o serviço fará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Gerenciador" no sistema de destino, que é vinculado a outro usuário criado no sistema de destino.
1. Se um usuário que estava anteriormente no escopo para provisionamento for removido do escopo (incluindo a não atribuição), o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.
1. Se um usuário que estava anteriormente no escopo para provisionamento estiver desabilitado ou excluído de maneira reversível no sistema de origem, o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.
1. Se um usuário que estava anteriormente no escopo para provisionamento for excluído do sistema de origem, o serviço excluirá o usuário no sistema de destino. No Azure AD, os usuários são excluídos por 30 dias após serem excluídos de maneira reversível.
1. Mantenha uma nova marca d' água no final do ciclo incremental, que fornece o ponto de partida para os ciclos incrementais posteriores.

> [!NOTE]
> Opcionalmente, você pode desabilitar as caixas de seleção **criar**, **Atualizar**ou **excluir** usando as **ações do objeto de destino** na seção [mapeamentos](customize-application-attributes.md) . A lógica para desabilitar um usuário durante uma atualização também é controlada por meio de um mapeamento de atributo de um campo como "accountEnabled".

O serviço de provisionamento continua executando ciclos incrementais back-to-back indefinidamente, em intervalos definidos no [tutorial específico de cada aplicativo](../saas-apps/tutorial-list.md), até que um dos seguintes eventos ocorra:

- O serviço é interrompido manualmente usando o portal do Azure ou usando o comando API do Graph apropriado 
- Um novo ciclo inicial é disparado usando a opção **limpar estado e reiniciar** na portal do Azure ou usando o comando API do Graph apropriado. Esta ação limpa qualquer marca d' água armazenada e faz com que todos os objetos de origem sejam avaliados novamente.
- Um novo ciclo inicial é disparado devido a uma alteração nos mapeamentos de atributo ou filtros de escopo. Essa ação também limpa qualquer marca-d ' água armazenada e faz com que todos os objetos de origem sejam avaliados novamente.
- O processo de provisionamento entra em quarentena (veja abaixo) devido a uma alta taxa de erros e permanece em quarentena por mais de quatro semanas. Nesse evento, o serviço será desabilitado automaticamente.

### <a name="errors-and-retries"></a>Erros e novas tentativas

Se um usuário individual não puder ser adicionado, atualizado ou excluído no sistema de destino devido a um erro no sistema de destino, a operação será repetida no próximo ciclo de sincronização. Se o usuário continuar a falhar, as novas tentativas começarão a ocorrer com uma frequência reduzida, redimensionando gradualmente para apenas uma tentativa por dia. Para resolver a falha, os administradores devem verificar os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) para determinar a causa raiz e executar a ação apropriada. As falhas comuns podem incluir:

- Usuários que não têm um atributo preenchido no sistema de origem que é necessário no sistema de destino
- Os usuários têm um valor de atributo no sistema de origem para o qual há uma restrição exclusiva no sistema de destino e o mesmo valor está presente em outro registro de usuário

Essas falhas podem ser resolvidas ajustando os valores de atributo para o usuário afetado no sistema de origem ou ajustando os mapeamentos de atributo para não causar conflitos.

### <a name="quarantine"></a>Quarentena

Se a maioria ou todas as chamadas feitas no sistema de destino falharem consistentemente devido a um erro (por exemplo, para credenciais de administrador inválidas), o trabalho de provisionamento entrará em um estado de "quarentena". Esse estado é indicado no [relatório de Resumo de provisionamento](check-status-user-account-provisioning.md) e por email se as notificações por email tiverem sido configuradas no portal do Azure.

Quando em quarentena, a frequência de ciclos incrementais é reduzida gradualmente para uma vez por dia.

O trabalho de provisionamento será removido da quarentena depois que todos os erros incorretos forem corrigidos e o próximo ciclo de sincronização for iniciado. Se o trabalho de provisionamento permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento será desabilitado. Saiba mais aqui sobre o status de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo será necessário para provisionar os usuários?

O desempenho depende se o seu trabalho de provisionamento está executando um ciclo de provisionamento inicial ou um ciclo incremental. Para obter detalhes sobre quanto tempo o provisionamento leva e como monitorar o status do serviço de provisionamento, consulte [verificar o status do provisionamento do usuário](application-provisioning-when-will-provisioning-finish-specific-user.md).

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Como saber se os usuários estão sendo provisionados corretamente?

Todas as operações executadas pelo serviço de provisionamento de usuário são registradas nos logs de provisionamento do Azure AD [(versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Isso inclui todas as operações de leitura e gravação feitas nos sistemas de origem e de destino, bem como os dados de usuário que foram lidos ou gravados durante cada operação.

Para obter informações sobre como ler os logs de provisionamento no portal do Azure, consulte o guia de [relatórios de provisionamento](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Como fazer solucionar problemas com o provisionamento do usuário?

Para obter diretrizes baseadas em cenários sobre como solucionar problemas de provisionamento automático de usuário, confira [problemas ao configurar e provisionar usuários em um aplicativo](application-provisioning-config-problem.md).

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Quais são as práticas recomendadas para distribuir o provisionamento automático de usuário?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Para obter um exemplo de plano de implantação passo a passo para o provisionamento de usuário de saída para um aplicativo, consulte o [Guia de implantação de identidade para provisionamento de usuário](https://aka.ms/deploymentplans/userprovisioning).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>O provisionamento automático de usuário para aplicativos SaaS funciona com usuários B2B no Azure AD?

Sim, é possível usar o serviço de provisionamento de usuários do Azure AD para provisionar usuários B2B (ou convidados) no Azure AD para aplicativos SaaS.

No entanto, para que os usuários B2B entrem no aplicativo SaaS usando o Azure AD, o aplicativo SaaS deve ter seu recurso de logon único baseado em SAML configurado de forma específica. Para obter mais informações sobre como configurar aplicativos SaaS para dar suporte a entradas de usuários B2B, consulte [configurar aplicativos SaaS para colaboração B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>O provisionamento automático de usuário para aplicativos SaaS funciona com grupos dinâmicos no Azure AD?

Sim. Quando configurado para "sincronizar apenas usuários e grupos atribuídos", o serviço de provisionamento de usuários do Azure AD pode provisionar ou desprovisionar usuários em um aplicativo SaaS com base em se eles são membros de um [grupo dinâmico](../users-groups-roles/groups-create-rule.md). Grupos dinâmicos também funcionam com a opção "sincronizar todos os usuários e grupos".

No entanto, o uso de grupos dinâmicos pode afetar o desempenho geral do provisionamento de usuário de ponta a ponta do Azure AD para aplicativos SaaS. Ao usar grupos dinâmicos, mantenha essas advertências e recomendações em mente:

- O quão rápido um usuário em um grupo dinâmico é provisionado ou desprovisionado em um aplicativo SaaS depende de quão rápido o grupo dinâmico pode avaliar as alterações de associação. Para obter informações sobre como verificar o status de processamento de um grupo dinâmico, consulte [verificar o status de processamento de uma regra de associação](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Ao usar grupos dinâmicos, as regras devem ser cuidadosamente consideradas com o provisionamento e desprovisionamento de usuários em mente, como uma perda de associação resulta em um evento de desprovisionamento.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>O provisionamento automático de usuário para aplicativos SaaS funciona com grupos aninhados no Azure AD?

Não. Quando configurado para "sincronizar apenas usuários e grupos atribuídos", o serviço de provisionamento de usuários do Azure AD não é capaz de ler ou provisionar usuários que estão em grupos aninhados. Só é capaz de ler e provisionar usuários que são membros imediatos do grupo explicitamente atribuído.

Essa é uma limitação de "atribuições baseadas em grupo a aplicativos", que também afeta o logon único e é descrito em [como usar um grupo para gerenciar o acesso a aplicativos SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Como alternativa, você deve atribuir explicitamente (ou, de outra forma, [escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) os grupos que contêm os usuários que precisam ser provisionados.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Está Provisionando entre o Azure AD e um aplicativo de destino usando um canal criptografado?

Sim. Usamos a criptografia SSL HTTPS para o destino do servidor.

## <a name="related-articles"></a>Artigos relacionados

- [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
- [Personalizando mapeamentos de atributo para provisionamento de usuário](customize-application-attributes.md)
- [Gravando expressões para mapeamentos de atributo](functions-for-customizing-application-data.md)
- [Filtros de escopo para provisionamento de usuário](define-conditional-rules-for-provisioning-user-accounts.md)
- [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Azure AD para aplicativos](use-scim-to-provision-users-and-groups.md)
- [Visão geral da API de sincronização do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
