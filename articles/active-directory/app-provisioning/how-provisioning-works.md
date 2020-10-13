---
title: Entenda como funciona o provisionamento da AD AZure Microsoft Docs
description: Entenda como funciona o fornecimento de AD AZure
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 05/20/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5fdce791ba8848b93a8457f3738392b1f5f15508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91801805"
---
# <a name="how-provisioning-works"></a>Como funciona o aprovisionamento

O fornecimento automático refere-se à criação de identidades e funções dos utilizadores nas aplicações em nuvem a que os utilizadores precisam de acesso. Além da criação de identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como alteração de estado ou de funções. Antes de iniciar uma implementação, pode rever este artigo para saber como funciona a provisão AD do Azure e obter recomendações de configuração. 

O **Serviço de Provisionamento AZure AD** fornece aos utilizadores aplicações saaS e outros sistemas, conectando-se a um Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) 2.0 ponto final de gestão de utilizadores API fornecido pelo fornecedor de aplicações. Este ponto final SCIM permite que a Azure AD crie, atualize e remova os utilizadores. Para aplicações selecionadas, o serviço de fornecimento também pode criar, atualizar e remover objetos adicionais relacionados com a identidade, tais como grupos e funções. O canal utilizado para o provisionamento entre a Azure AD e a aplicação é encriptado utilizando encriptação HTTPS TLS 1.2.


![Azure Ad Provisioning Service ](./media/how-provisioning-works/provisioning0.PNG)
 *Figure 1: The Azure Ad Provisioning Service*

![Saída do fluxo de trabalho do utilizador ](./media/how-provisioning-works/provisioning1.PNG)
 *Figura 2: "Outbound" utilizador que fornece fluxo de trabalho de Azure AD para aplicações saaS populares*

![Inbound user provisioning workflow ](./media/how-provisioning-works/provisioning2.PNG)
 *Figure 3: "Inbound" user provisioning workflow from popular Human Capital Management (HCM) applications to Azure Ative Directory and Windows Server Ative Directory*

## <a name="provisioning-using-scim-20"></a>Provisionamento utilizando SCIM 2.0

O serviço de fornecimento Azure AD utiliza o [protocolo SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) para o provisionamento automático. O serviço liga-se ao ponto final do SCIM para a aplicação, e utiliza o esquema de objetos de utilizador SCIM e as APIs REST para automatizar o fornecimento e desavisionamento de utilizadores e grupos. Um conector de provisionamento baseado no SCIM é fornecido para a maioria das aplicações na galeria Azure AD. Ao construir aplicativos para Azure AD, os desenvolvedores podem usar a API de gestão de utilizadores SCIM 2.0 para construir um ponto final SCIM que integra Azure AD para provisionamento. Para mais informações, consulte [build a SCIM endpoint e configurar o provisionamento do utilizador](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Para solicitar um conector automático de provisionamento Azure AD para uma aplicação que não tenha atualmente uma, preencha um [Pedido de Aplicação do Diretório Ativo Azure](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Autorização

São necessárias credenciais para que a Azure AD se conecte à API de gestão de utilizadores da aplicação. Enquanto configurar o fornecimento automático do utilizador para uma aplicação, terá de introduzir credenciais válidas. Pode encontrar tipos e requisitos credenciais para a aplicação, referindo-se ao tutorial da aplicação. No portal Azure, poderá testar as credenciais, tendo a Azure AD tentado ligar-se à app de provisionamento da app utilizando as credenciais fornecidas.

Se o sign-on único baseado em SAML também estiver configurado para a aplicação, o limite interno de armazenamento por aplicação da Azure AD é de 1024 bytes. Este limite inclui todos os certificados, fichas secretas, credenciais e dados de configuração relacionados associados a uma única instância de uma aplicação (também conhecido como registo principal de serviço em Azure AD). Quando o sign-on único baseado em SAML é configurado, o certificado usado para assinar os tokens SAML consome frequentemente mais de 50% do espaço. Quaisquer itens adicionais (tokens secretos, URIs, endereços de e-mail de notificação, nomes de utilizador e palavras-passe) que introduza durante a configuração do fornecimento do utilizador podem exceder o limite de armazenamento. Para obter mais informações, consulte [as credenciais de administrador de poupança de problemas enquanto configura o provisionamento do utilizador](./application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Atributos de mapeamento

Quando permite o provisionamento do utilizador para uma aplicação SaaS de terceiros, o portal Azure controla os seus valores de atributos através de mapeamentos de atributos. Os mapeamentos determinam os atributos do utilizador que fluem entre o Azure AD e a aplicação-alvo quando as contas do utilizador são a provisionadas ou atualizadas.

Existe um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de utilizador AZure AD e objetos de utilizador de cada aplicação SaaS. Algumas aplicações gerem outros tipos de objetos juntamente com os Utilizadores, como grupos.

Ao configurar o provisionamento, é importante rever e configurar os mapeamentos de atributos e fluxos de trabalho que definem quais as propriedades do utilizador (ou grupo) que fluem do Azure AD para a aplicação. Reveja e configuure a propriedade correspondente **(Match objects using this attribute**) que é usada para identificar e combinar exclusivamente utilizadores/grupos entre os dois sistemas.

Pode personalizar os mapeamentos de atributos padrão de acordo com as necessidades do seu negócio. Assim, pode alterar ou eliminar os mapeamentos de atributos existentes ou criar novos mapeamentos de atributos. Para mais informações, consulte [personalizar o fornecimento de mapeamentos de atributos para aplicações SaaS](./customize-application-attributes.md).

Ao configurar o fornecimento de uma aplicação SaaS, um dos tipos de mapeamentos de atributos que pode especificar é um mapeamento de expressão. Para estes mapeamentos, deve escrever uma expressão semelhante a um script que lhe permita transformar os dados dos seus utilizadores em formatos mais aceitáveis para a aplicação SaaS. Para mais detalhes, consulte [as expressões de escrita para mapeamentos de atributos](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Escoar 
### <a name="assignment-based-scoping"></a>Escagem baseada em atribuição

Para o fornecimento de saída da Azure AD a uma aplicação SaaS, confiar nas [atribuições](../manage-apps/assign-user-or-group-access-portal.md) de utilizador ou grupo é a forma mais comum de determinar quais os utilizadores que estão em possibilidade de provisão. Uma vez que as atribuições do utilizador também são utilizadas para permitir uma única inscrição, o mesmo método pode ser utilizado para gerir tanto o acesso como o provisionamento. A deteção baseada em atribuição não se aplica a cenários de provisionamento de entrada, tais como Workday e Successfactors.

* **Os grupos.** Com um plano de licença Azure AD Premium, pode utilizar grupos para atribuir acesso a uma aplicação SaaS. Em seguida, quando o âmbito de provisionamento é definido apenas para **utilizadores e grupos atribuídos,** o serviço de fornecimento de AD AZure irá prestar ou desatar utilizadores com base no facto de serem membros de um grupo que está atribuído à aplicação. O objeto de grupo em si não é a provisionado a menos que a aplicação suporte objetos de grupo. Certifique-se de que os grupos atribuídos à sua aplicação têm a propriedade "SecurityEnabled" definida como "True".

* **Grupos dinâmicos.** O serviço de fornecimento de utilizadores Azure AD pode ler e prestar aos utilizadores em [grupos dinâmicos.](../users-groups-roles/groups-create-rule.md) Tenha em mente estas ressalvas e recomendações:

  * Os grupos dinâmicos podem impactar o desempenho do fornecimento de ponta a ponta das aplicações Azure AD para SaaS.

  * A rapidez com que um utilizador num grupo dinâmico é a provisionado ou desavisionado numa aplicação SaaS depende da rapidez com que o grupo dinâmico pode avaliar as mudanças de adesão. Para obter informações sobre como verificar o estado de processamento de um grupo dinâmico, consulte o estado de [processamento de verificação de uma regra de adesão](../users-groups-roles/groups-create-rule.md).

  * Quando um utilizador perde a adesão ao grupo dinâmico, é considerado um evento de desavisionamento. Considere este cenário ao criar regras para grupos dinâmicos.

* **Grupos aninhados.** O serviço de fornecimento de utilizadores Azure AD não pode ler ou providenciar utilizadores em grupos aninhados. O serviço só pode ler e providenciar aos utilizadores que sejam membros imediatos de um grupo explicitamente designado. Esta limitação de "atribuições baseadas em grupo a aplicações" também afeta uma única sessão de sessão (ver [Utilização de um grupo para gerir o acesso às aplicações saaS).](../users-groups-roles/groups-saasapps.md) Em vez disso, atribua diretamente ou de outra forma [o âmbito nos](define-conditional-rules-for-provisioning-user-accounts.md) grupos que contêm os utilizadores que precisam de ser provisionados.

### <a name="attribute-based-scoping"></a>Scoping baseado em atributos 

Pode utilizar filtros de deteção para definir regras baseadas em atributos que determinam quais os utilizadores que estão a forcê-lo a uma aplicação. Este método é comumente utilizado para o provisionamento de entrada a partir de aplicações de HCM para Azure AD e Ative Directory. Os filtros de deteção são configurados como parte dos mapeamentos de atributos para cada conector de fornecimento de um utilizador Azure AD. Para obter detalhes sobre a configuração de filtros de deteção baseados em atributos, consulte [o provisionamento de aplicações baseados em Atributos com filtros de escoamento](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>Utilizadores B2B (convidados)

É possível utilizar o serviço de fornecimento de utilizadores Azure AD para a prestação de utilizadores B2B (ou convidados) em Azure AD a aplicações SaaS. No entanto, para que os utilizadores B2B inscrevam-se na aplicação SaaS utilizando a Azure AD, a aplicação SaaS deve ter a sua capacidade de entrada única baseada em SAML configurada de uma forma específica. Para obter mais informações sobre como configurar aplicações SaaS para apoiar os insurretos dos utilizadores B2B, consulte [aplicações Configure SaaS para colaboração B2B](../external-identities/configure-saas-apps.md).

Note que o nome de utilizadorPrincipalName para um utilizador convidado é frequentemente armazenado como "alias#EXT# @domain.com ". quando o utilizadorPrincipalName está incluído nos mapeamentos do seu atributo como um atributo de origem, o #EXT# é retirado do nome do utilizadorPrincipalName. Se necessitar que o #EXT# esteja presente, substitua o nome de utilizadorPrincipalName por originalUserPrincipalName como atributo de origem. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Ciclos de provisionamento: Inicial e incremental

Quando o Azure AD é o sistema de origem, o serviço de fornecimento utiliza a [consulta Delta Use para rastrear alterações nos dados do Microsoft Graph](/graph/delta-query-overview) para monitorizar utilizadores e grupos. O serviço de fornecimento executa um ciclo inicial contra o sistema de origem e o sistema alvo, seguido de ciclos incrementais periódicos.

### <a name="initial-cycle"></a>Ciclo inicial

Quando o serviço de prestação de serviços for iniciado, o primeiro ciclo:

1. Consultar todos os utilizadores e grupos do sistema de origem, recuperando todos os atributos definidos nos [mapeamentos](customize-application-attributes.md)do atributo .

2. Filtrar os utilizadores e grupos devolvidos, utilizando [quaisquer atribuições](../manage-apps/assign-user-or-group-access-portal.md) configuradas ou [filtros de scoping baseados em atributos](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando um utilizador é atribuído ou em possibilidade de provisionamento, o serviço consulta o sistema-alvo de um utilizador correspondente utilizando os [atributos de correspondência especificados](customize-application-attributes.md#understanding-attribute-mapping-properties). Exemplo: Se o nome do utilizadorPrincipal no sistema de origem for o atributo correspondente e os mapas ao nome do utilizador no sistema-alvo, então o serviço de fornecimento consulta o sistema alvo de nomes de utilizador que correspondam aos valores do nome do utilizadorPrincipal no sistema de origem.

4. Se um utilizador correspondente não for encontrado no sistema alvo, é criado utilizando os atributos devolvidos do sistema de origem. Após a criação da conta de utilizador, o serviço de fornecimento deteta e caches o ID do sistema alvo para o novo utilizador. Este ID é usado para executar todas as operações futuras nesse utilizador.

5. Se for encontrado um utilizador correspondente, é atualizado utilizando os atributos fornecidos pelo sistema de origem. Após a correspondência da conta do utilizador, o serviço de fornecimento deteta e caches o ID do sistema alvo para o novo utilizador. Este ID é usado para executar todas as operações futuras nesse utilizador.

6. Se os mapeamentos do atributo contiverem atributos de "referência", o serviço faz atualizações adicionais no sistema alvo para criar e ligar os objetos referenciados. Por exemplo, um utilizador pode ter um atributo "Manager" no sistema alvo, que está ligado a outro utilizador criado no sistema-alvo.

7. Persistir uma marca de água no final do ciclo inicial, que fornece o ponto de partida para os ciclos incrementais posteriores.

Algumas aplicações como ServiceNow, G Suite e Box suportam não só o a provisionamento dos utilizadores, mas também o provisionamento de grupos e seus membros. Nesses casos, se o provisionamento em grupo estiver habilitado nos [mapeamentos,](customize-application-attributes.md)o serviço de fornecimento sincroniza os utilizadores e os grupos e, posteriormente, sincroniza os membros do grupo.

### <a name="incremental-cycles"></a>Ciclos incrementais

Após o ciclo inicial, todos os outros ciclos irão:

1. Consultar o sistema de origem para quaisquer utilizadores e grupos que foram atualizados desde que a última marca de água foi armazenada.

2. Filtrar os utilizadores e grupos devolvidos, utilizando [quaisquer atribuições](../manage-apps/assign-user-or-group-access-portal.md) configuradas ou [filtros de scoping baseados em atributos](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando um utilizador é atribuído ou em possibilidade de provisionamento, o serviço consulta o sistema-alvo de um utilizador correspondente utilizando os [atributos de correspondência especificados](customize-application-attributes.md#understanding-attribute-mapping-properties).

4. Se um utilizador correspondente não for encontrado no sistema alvo, é criado utilizando os atributos devolvidos do sistema de origem. Após a criação da conta de utilizador, o serviço de fornecimento deteta e caches o ID do sistema alvo para o novo utilizador. Este ID é usado para executar todas as operações futuras nesse utilizador.

5. Se for encontrado um utilizador correspondente, é atualizado utilizando os atributos fornecidos pelo sistema de origem. Se for uma conta recém-atribuída que é correspondida, o serviço de fornecimento deteta e caches o ID do sistema alvo para o novo utilizador. Este ID é usado para executar todas as operações futuras nesse utilizador.

6. Se os mapeamentos do atributo contiverem atributos de "referência", o serviço faz atualizações adicionais no sistema alvo para criar e ligar os objetos referenciados. Por exemplo, um utilizador pode ter um atributo "Manager" no sistema alvo, que está ligado a outro utilizador criado no sistema-alvo.

7. Se um utilizador que já estava no âmbito do provisionamento for removido do âmbito, incluindo não ter sido atribuído, o serviço desativa o utilizador no sistema-alvo através de uma atualização.

8. Se um utilizador que já estava no âmbito do provisionamento for desativado ou eliminado suavemente no sistema de origem, o serviço desativa o utilizador no sistema-alvo através de uma atualização.

9. Se um utilizador que já estava no âmbito de provisão for eliminado no sistema de origem, o serviço elimina o utilizador no sistema-alvo. No Azure AD, os utilizadores são duramente eliminados 30 dias após serem apagados suavemente.

10. Persistir uma nova marca de água no final do ciclo incremental, que fornece o ponto de partida para os ciclos incrementais posteriores.

> [!NOTE]
> Pode desativar opcionalmente as operações **Criar,** **Atualizar**ou **Eliminar** utilizando as ações do **objeto Alvo,** verificar caixas na secção [mapeamentos.](customize-application-attributes.md) A lógica de desativar um utilizador durante uma atualização também é controlada através de um mapeamento de atributos de um campo como "accountEnabled".

O serviço de prestação continua a funcionar indefinidamente ciclos incrementais, em intervalos definidos no [tutorial específico de cada aplicação.](../saas-apps/tutorial-list.md) Os ciclos incrementais continuam até que ocorra um dos seguintes eventos:

- O serviço é interrompido manualmente utilizando o portal Azure ou utilizando o comando API do Microsoft Graph apropriado.
- Um novo ciclo inicial é desencadeado utilizando a opção **Clear state e restart** no portal Azure, ou utilizando o comando API do Microsoft Graph apropriado. Esta ação limpa qualquer marca de água armazenada e faz com que todos os objetos de origem sejam novamente avaliados.
- Um novo ciclo inicial é desencadeado devido a uma alteração nos mapeamentos de atributos ou filtros de scoping. Esta ação também limpa qualquer marca de água armazenada e faz com que todos os objetos de origem sejam novamente avaliados.
- O processo de provisionamento entra em quarentena (ver abaixo) devido a uma elevada taxa de erro, e permanece em quarentena por mais de quatro semanas. Neste caso, o serviço será automaticamente desativado.

### <a name="errors-and-retries"></a>Erros e retrítrios

Se um erro no sistema-alvo impedir que um utilizador individual seja adicionado, atualizado ou eliminado no sistema-alvo, a operação é novamente experimentada no ciclo de sincronização seguinte. Se o utilizador continuar a falhar, as retró quais vão começar a ocorrer numa frequência reduzida, reduzindo gradualmente para apenas uma tentativa por dia. Para resolver a falha, os administradores devem verificar os [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) para determinar a causa raiz e tomar as medidas adequadas. Falhas comuns podem incluir:

- Utilizadores que não tenham um atributo preenchido no sistema de origem que é necessário no sistema alvo
- Os utilizadores que têm um valor de atributo no sistema de origem para o qual há uma restrição única no sistema alvo, e o mesmo valor está presente noutro registo de utilizador

Resolver estas falhas ajustando os valores de atributos para o utilizador afetado no sistema de origem, ou ajustando os mapeamentos do atributo para que não causem conflitos.

### <a name="quarantine"></a>Quarentena

Se a maioria ou todas as chamadas que são feitas contra o sistema alvo falharem consistentemente devido a um erro (por exemplo, credenciais de administração inválidas) o trabalho de provisionamento entra num estado de "quarentena". Este estado é indicado no relatório de resumo do [provisionamento](./check-status-user-account-provisioning.md) e via e-mail se as notificações por e-mail foram configuradas no portal Azure.

Quando em quarentena, a frequência dos ciclos incrementais é gradualmente reduzida para uma vez por dia.

O trabalho de provisionamento sai em quarentena depois de todos os erros ofensivos serem corrigidos e o ciclo de sincronização seguinte começar. Se o trabalho de provisionamento permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento é deficiente. Saiba mais aqui sobre o estado de quarentena [aqui.](./application-provisioning-quarantine-status.md)

### <a name="how-long-provisioning-takes"></a>O tempo que o aprovisionamento demora

O desempenho depende se o seu trabalho de provisionamento está a executar um ciclo inicial de provisionamento ou um ciclo incremental. Para obter mais informações sobre o tempo de fornecimento e como monitorizar o estado do serviço de avisão, consulte [verificar o estado do fornecimento do utilizador](application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Como saber se os utilizadores estão a ser a provisionados corretamente

Todas as operações executadas pelo serviço de fornecimento de utilizadores são registadas nos registos de Provisionamento Azure AD [(pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Os registos incluem todas as operações de leitura e escrita feitas para os sistemas de origem e alvo, bem como os dados do utilizador que foram lidos ou escritos durante cada operação. Para obter informações sobre como ler os registos de provisionamento no portal Azure, consulte o [guia de informação sobre](./check-status-user-account-provisioning.md)o provisionamento .

## <a name="de-provisioning"></a>Desesvisão
O serviço de fornecimento de Azure AD mantém os sistemas de origem e alvo sincronizados através da desavisionamento de contas quando o acesso ao utilizador é removido.

O serviço de prestação suporta a eliminação e a desativação (por vezes designada por eliminação suave) dos utilizadores. A definição exata de desativação e eliminação varia em com base na implementação da aplicação-alvo, mas geralmente um desativado indica que o utilizador não pode iniciar sação. Uma eliminação indica que o utilizador foi completamente removido da aplicação. Para aplicações SCIM, um disable é um pedido para definir a propriedade *ativa* para falso em um utilizador. 

**Configure a sua aplicação para desativar um utilizador**

Certifique-se de que selecionou a caixa de verificação para obter atualizações.

Certifique-se de que tem o mapeamento *ativo* para a sua aplicação. Se utilizar uma aplicação na galeria de aplicações, o mapeamento pode ser ligeiramente diferente. Certifique-se de que utiliza o predefinido /fora do mapeamento da caixa para aplicações de galeria.


**Configure a sua aplicação para eliminar um utilizador**

Os seguintes cenários desencadearão um desativação ou uma eliminação: 
* Um utilizador é eliminado suavemente em Azure AD (enviado para o caixote do lixo de reciclagem / Propriedade AccountEnabled definida como falsa).
    30 dias após a perda de um utilizador em Azure AD, serão permanentemente eliminados do arrendatário. Neste momento, o serviço de fornecimento enviará um pedido de DELETE para eliminar permanentemente o utilizador na aplicação. A qualquer momento durante a janela de 30 dias, pode [eliminar manualmente um utilizador permanentemente](../fundamentals/active-directory-users-restore.md), o que envia um pedido de eliminação para a aplicação.
* Um utilizador é permanentemente eliminado / removido do caixote do lixo em Azure AD.
* Um utilizador não é atribuído a partir de uma aplicação.
* Um utilizador vai de âmbito para fora do alcance (já não passa um filtro de escotagem).
    
Por predefinição, o serviço de fornecimento AZure AD elimina ou desativa os utilizadores que ficam fora de alcance. Se quiser anular este comportamento predefinido, pode definir uma bandeira para [saltar as exclusões fora de alcance.](skip-out-of-scope-deletions.md)

Se ocorrer um dos quatro eventos acima e a aplicação-alvo não suportar eliminações suaves, o serviço de fornecimento enviará um pedido de DELETE para eliminar permanentemente o utilizador da aplicação.

Se vir um atributo IsSoftDeleted nos mapeamentos do seu atributo, é utilizado para determinar o estado do utilizador e se deve enviar um pedido de atualização com ativo = falso para eliminar suavemente o utilizador.

**Limitações conhecidas**

* Se um utilizador que foi previamente gerido pelo serviço de fornecimento não for designado a partir de uma aplicação, ou de um grupo atribuído a uma app, enviaremos um pedido de desativação. Nessa altura, o utilizador não é gerido pelo serviço e não enviaremos um pedido de eliminação quando estes forem eliminados do diretório.
* Não é suportado o fornecimento de um utilizador que esteja desativado em Azure AD. Devem estar ativos na Azure AD antes de serem a provisionados.
* Quando um utilizador passa de soft-deleted para ativo, o serviço de fornecimento AZure AD ativará o utilizador na aplicação-alvo, mas não irá restaurar automaticamente os membros do grupo. A aplicação-alvo deve manter os membros do grupo para o utilizador em estado inativo. Se a aplicação-alvo não o apoiar, pode reiniciar a provisão para atualizar os membros do grupo. 

**Recomendação**

Ao desenvolver uma aplicação, apoie sempre as eliminações suaves e as eliminações duras. Permite que os clientes recuperem quando um utilizador é acidentalmente desativado.


## <a name="next-steps"></a>Passos Seguintes

[Planear uma implementação de aprovisionamento automático de utilizadores](../app-provisioning/plan-auto-user-provisioning.md)

[Configurar o aprovisionamento para uma aplicação da galeria](./configure-automatic-user-provisioning-portal.md)

[Construa um ponto final SCIM e configuure o provisionamento ao criar a sua própria app](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Problemas de resolução de problemas com configuração e provisionamento de utilizadores a uma aplicação](./application-provisioning-config-problem.md).
