---
title: O aprovisionamento de utilizador de aplicação do SaaS no Azure AD automatizado | Documentos da Microsoft
description: Uma introdução à utilização do Azure AD para aprovisionar automaticamente, anular o aprovisionamento e atualizar continuamente as contas de utilizador em várias aplicações de SaaS de terceiros.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2019
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 516faed0f41ae36079d0f26f0311b35d5582d57a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759798"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizar o aprovisionamento e desaprovisionamento para aplicações SaaS com o Azure Active Directory de utilizador

## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>O que é o aprovisionamento automatizado do utilizador para aplicações SaaS?
Azure Active Directory (Azure AD) permite-lhe automatizar a criação, manutenção e remoção de identidades de utilizador na cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) aplicativos como o Dropbox, Salesforce, ServiceNow e muito mais.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**Esta funcionalidade permite-lhe:**

- Crie automaticamente novas contas nos sistemas certos para novas pessoas quando eles Junte-se a sua equipa ou organização.
- Desative automaticamente a contas nos sistemas certos quando deixam das pessoas a equipa ou organização.
- Certifique-se de que as identidades nas suas aplicações e sistemas são mantidas atualizadas com base nas alterações no diretório ou o seu sistema de recursos humanos.
- Provisionar objetos de não utilizadores, tais como grupos, para aplicativos que oferecem suporte a eles.

**Aprovisionamento automatizado do utilizador também inclui esta funcionalidade:**

- A capacidade de acordo com as identidades existentes entre sistemas de origem e de destino.
- Mapeamentos de atributo personalizável que definem que dados de utilizador devem fluir do sistema de origem para o sistema de destino.
- Alertas de e-mail opcional para erros de aprovisionamento.
- Registos de atividade e de relatórios para ajudar com a monitorização e resolução de problemas.

## <a name="why-use-automated-provisioning"></a>Porquê utilizar o aprovisionamento automatizado?

Alguns comuns motivações para utilizar esta funcionalidade incluem:

- Evitar os custos, ineficiências e erro humano associados aos processos de aprovisionamento manuais.
- Evitar os custos associados de hospedagem e manter soluções de aprovisionamento personalizada e scripts.
- Proteger a sua organização removendo instantaneamente as identidades dos utilizadores de aplicações de SaaS chave quando deixam a organização.
- Facilmente importar um grande número de utilizadores para uma determinada aplicação SaaS ou de sistema.
- Ter um único conjunto de políticas para determinar quem está a ser aprovisionada e quem pode iniciar sessão a uma aplicação.

## <a name="how-does-automatic-provisioning-work"></a>Como funciona o aprovisionamento automático?
    
O **serviço de aprovisionamento do Azure AD** Aprovisiona utilizadores para aplicações SaaS e outros sistemas através da ligação a pontos finais de API de gestão de utilizador fornecidos pelo fornecedor de cada aplicação. Esses pontos de extremidade do API de gestão do usuário permitem que o Azure AD para programaticamente criar, atualizar e remover utilizadores. Para aplicações selecionadas, o serviço de aprovisionamento também pode criar, atualizar e remover objetos adicionais relacionadas com a identidade, por exemplo, grupos e funções. 

![Aprovisionamento](./media/user-provisioning/provisioning0.PNG)
*figura 1: O serviço de aprovisionamento do Azure AD*

![Saída de aprovisionamento](./media/user-provisioning/provisioning1.PNG)
*figura 2: Fluxo de trabalho do Azure AD para aplicações SaaS populares de aprovisionamento de utilizadores de "Saída"*

![Aprovisionamento de entrada](./media/user-provisioning/provisioning2.PNG)
*figura 3: "Entrada" fluxo de trabalho aprovisionamento do utilizador a partir de aplicações populares de gestão de Capital humano (HCM) para o Azure Active Directory e o Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quais aplicativos e sistemas posso utilizar com o aprovisionamento de utilizadores automático do Azure AD?

As funcionalidades do Azure AD pré-integradas suporte para muitos populares SaaS aplicações e recursos humanos sistemas e genérico suporte para aplicações que implementam partes específicas do padrão SCIM 2.0.

### <a name="pre-integrated-applications"></a>Aplicações previamente integradas

Para obter uma lista de todos os aplicativos para o qual o Azure AD suporta um conector de aprovisionamento previamente integrado, consulte a [lista de tutoriais de aplicações para o aprovisionamento de utilizador](../saas-apps/tutorial-list.md).

Para contactar o Azure AD equipa para o pedido de suporte de aprovisionamento de aplicações adicionais, de engenharia de enviar uma mensagem por meio da [fórum de comentários do Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Por ordem para uma aplicação suportar o aprovisionamento automatizado do utilizador, devem primeiro fornecer as APIs que permitem a programas externos automatizar a criação, a manutenção e a remoção de utilizadores de gestão de utilizador necessário. Por conseguinte, nem todas as aplicações de SaaS são compatíveis com esta funcionalidade. Para aplicações que suportam as APIs de gestão de utilizadores, a equipa de engenharia do Azure AD, em seguida, pode criar um conector de aprovisionamento para essas aplicações, e esse trabalho é priorizado pelas necessidades atuais e potenciais clientes. 

### <a name="connecting-applications-that-support-scim-20"></a>Ligar aplicações que suportam SCIM 2.0

Para obter informações sobre como ligar genericamente aplicativos que implementam SCIM APIs, de gestão de utilizadores com base 2.0 - consulte [utilizar o SCIM para aprovisionar automaticamente os utilizadores e grupos do Azure Active Directory a aplicações](use-scim-to-provision-users-and-groups.md).

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como configurar o aprovisionamento automático a uma aplicação?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Utilize o portal do Azure Active Directory para configurar o Azure AD para uma aplicação selecionada, o serviço de aprovisionamento.

1. Abra o  **[portal do Azure Active Directory](https://aad.portal.azure.com)**.

1. Selecione **aplicações empresariais** no painel à esquerda. Uma lista de todas as aplicações configuradas é apresentado.

1. Escolher **+ nova aplicação** para adicionar uma aplicação. Adicione o seguinte dependendo do seu cenário:

   - O **adicionar sua própria aplicação** opção suporta integrações de SCIM personalizada.

   - Todas as aplicações do **adicionar a partir da galeria** > **em destaque aplicativos** aprovisionamento automático do secção suporte. Consulte a [lista de tutoriais de aplicações para o aprovisionamento de utilizador](../saas-apps/tutorial-list.md) processos adicionais.

1. Fornece detalhes e selecione **adicionar**. A nova aplicação é adicionada à lista de aplicações empresariais e abre-se para a tela de gerenciamento de aplicativos.

1. Selecione **aprovisionamento** para gerir as definições da aplicação de aprovisionamento da conta de utilizador.

   ![Definições](./media/user-provisioning/provisioning_settings0.PNG)

1. Selecione a opção automática para o **modo de aprovisionamento** para especificar as definições de credenciais de administrador, mapeamentos, iniciar e parar e sincronização.

   - Expanda **credenciais de administrador** para introduzir as credenciais necessárias para o Azure AD ligar à API de gestão de usuário do aplicativo. Esta secção também permite-lhe ativar notificações por e-mail se não das credenciais, ou a tarefa de aprovisionamento entra [quarentena](#quarantine).

   - Expanda **mapeamentos** para ver e editar os atributos de utilizador que fluem entre o Azure AD e o aplicativo de destino quando as contas de utilizador são aprovisionadas ou atualizadas. Se o aplicativo de destino o suportar, esta secção permite-lhe configurar, opcionalmente, o aprovisionamento de grupos e contas de utilizador. Selecione um mapeamento da tabela para abrir o editor de mapeamento para a direita, onde pode ver e personalizar os atributos de utilizador.
   
     **Filtros de âmbito** indicar ao serviço de aprovisionamento que utilizadores e grupos no sistema de origem devem ser aprovisionados ou desaprovisionados ao sistema de destino. Na **mapeamento do atributo** painel, selecione **âmbito de objeto de origem** para filtrar os valores de atributo específico. Por exemplo, pode especificar que apenas os utilizadores com o atributo “Department” de “Sales” devem estar no âmbito do aprovisionamento. Para obter mais informações, veja [Using scoping filters](define-conditional-rules-for-provisioning-user-accounts.md) (Utilizar filtros de âmbito).
    
     Para obter mais informações, consulte [personalizar mapeamentos de atributos](customize-application-attributes.md).

   - **Definições** controlam o funcionamento do serviço de aprovisionamento de um aplicativo, incluindo se está em execução. O **âmbito** menu permite-lhe especificar se apenas utilizadores e grupos atribuídos devem estar no âmbito de aprovisionamento, ou se deverão ser aprovisionados todos os utilizadores no diretório do Azure AD. Para obter informações sobre como “atribuir” utilizadores e grupos, veja [Assign a user or group to an enterprise app in Azure Active Directory](assign-user-or-group-access-portal.md) (Atribuir utilizadores ou grupos a uma aplicação empresarial no Azure Active Directory).

No ecrã de gestão de aplicações, selecione **registos de auditoria** para ver os registos de cada operação de execução pelo Azure AD do serviço de aprovisionamento. Para obter mais informações, consulte a [guia de relatórios de aprovisionamento](check-status-user-account-provisioning.md).

![Definições](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> O serviço de aprovisionamento de utilizador do Azure AD também podem ser configurado e geridas com o [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>O que acontece durante o aprovisionamento?

Quando o Azure AD é o sistema de origem, o serviço de aprovisionamento utiliza a [funcionalidade de consulta diferencial da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) para monitorizar os utilizadores e grupos. O serviço de aprovisionamento é executada uma sincronização inicial com o sistema de origem e o sistema de destino, seguido de sincronizações incrementais periódicas. 

### <a name="initial-sync"></a>Sincronização inicial

Quando é iniciado o serviço de aprovisionamento, a primeira sincronização nunca executada irá:

1. Consultar todos os utilizadores e grupos do sistema de origem, obter todos os atributos definidos no [mapeamentos de atributo](customize-application-attributes.md).
2. Filtrar os utilizadores e grupos retornados, usando qualquer configurado [atribuições](assign-user-or-group-access-portal.md) ou [filtros de âmbito baseadas em atributos](define-conditional-rules-for-provisioning-user-accounts.md).
3. Quando é atribuída a um utilizador ou no âmbito de aprovisionamento, o serviço consulta o sistema de destino para um utilizador correspondente utilizando especificado [correspondência atributos](customize-application-attributes.md#understanding-attribute-mapping-properties). Exemplo: Se o nome de userPrincipal no sistema de origem é o atributo correspondente e mapeia para o nome de utilizador no sistema de destino, em seguida, o serviço de aprovisionamento consulta o sistema de destino para nomes de utilizador que correspondem aos valores de nome de userPrincipal no sistema de origem.
4. Se um utilizador correspondente não for encontrado no sistema de destino, é criado usando os atributos devolvidos do sistema de origem. Depois de criar a conta de utilizador, o serviço de aprovisionamento detecta e armazena em cache o ID de sistema de destino para o novo utilizador, o que é utilizado para executar todas as operações futuras com esse utilizador.
5. Se um utilizador correspondente for encontrado, ele é atualizado usando os atributos fornecidos pelo sistema de origem. Depois da conta de utilizador é correspondida, o serviço de aprovisionamento detecta e armazena em cache o ID de sistema de destino para o novo utilizador, o que é utilizado para executar todas as operações futuras com esse utilizador.
6. Se os mapeamentos de atributos contém atributos de "referência", o serviço faz atualizações adicionais no sistema de destino para criar e ligar os objetos referenciados. Por exemplo, um utilizador pode ter um atributo "Manager" no sistema de destino, o que é ligado a outro utilizador criado no sistema de destino.
7. Manter uma marca d'água no final da sincronização inicial, que fornece o ponto de partida para as sincronizações incrementais mais tarde.

Alguns aplicativos como o ServiceNow, G Suite e caixa de suporte não apenas os utilizadores de aprovisionamento, mas também aprovisionamento grupos e seus membros. Nesses casos, se o aprovisionamento do grupo estiver ativado no [mapeamentos](customize-application-attributes.md), o serviço de aprovisionamento sincroniza os utilizadores e os grupos e, em seguida, sincronize mais tarde as associações de grupo. 

### <a name="incremental-syncs"></a>Sincronizações incrementais

Após a sincronização inicial, todas as outras sincronizações irão:

1. Consulte o sistema de origem para todos os utilizadores e grupos que foram atualizados desde a última marca d'água foi armazenada.
2. Filtrar os utilizadores e grupos retornados, usando qualquer configurado [atribuições](assign-user-or-group-access-portal.md) ou [filtros de âmbito baseadas em atributos](define-conditional-rules-for-provisioning-user-accounts.md).
3. Quando é atribuída a um utilizador ou no âmbito de aprovisionamento, o serviço consulta o sistema de destino para um utilizador correspondente utilizando especificado [correspondência atributos](customize-application-attributes.md#understanding-attribute-mapping-properties).
4. Se um utilizador correspondente não for encontrado no sistema de destino, é criado usando os atributos devolvidos do sistema de origem. Depois de criar a conta de utilizador, o serviço de aprovisionamento detecta e armazena em cache o ID de sistema de destino para o novo utilizador, o que é utilizado para executar todas as operações futuras com esse utilizador.
5. Se um utilizador correspondente for encontrado, ele é atualizado usando os atributos fornecidos pelo sistema de origem. Se for uma conta atribuída recentemente correspondente, o serviço de aprovisionamento detecta e armazena em cache o ID de sistema de destino para o novo utilizador, o que é utilizado para executar todas as operações futuras com esse utilizador.
6. Se os mapeamentos de atributos contém atributos de "referência", o serviço faz atualizações adicionais no sistema de destino para criar e ligar os objetos referenciados. Por exemplo, um utilizador pode ter um atributo "Manager" no sistema de destino, o que é ligado a outro utilizador criado no sistema de destino.
7. Se um utilizador que foi anteriormente no âmbito de aprovisionamento é removido do âmbito (incluindo a ser não atribuídos), o serviço desativa o utilizador no sistema de destino através de uma atualização.
8. Se um utilizador que foi anteriormente no âmbito de aprovisionamento está desativado ou eliminado de forma recuperável no sistema de origem, o serviço desativa o utilizador no sistema de destino através de uma atualização.
9. Se um utilizador que foi anteriormente no âmbito de aprovisionamento é eliminado no sistema de origem, o serviço elimina o utilizador no sistema de destino. No Azure AD, os utilizadores são de difícil eliminados de 30 dias após eles são eliminados de forma recuperável.
10. Manter uma nova marca d'água no final da sincronização incremental, que fornece o ponto de partida para as sincronizações incrementais mais tarde.

>[!NOTE]
> Opcionalmente, pode desativar a **criar**, **Update**, ou **eliminar** operações através da utilização o **ações de objeto de destino** caixas de seleção no [Mapeamentos](customize-application-attributes.md) secção. A lógica para desativar um utilizador durante uma atualização também é controlada por meio de um mapeamento do atributo de um campo como "accountEnabled".

O serviço de aprovisionamento continua a ser executada agraciadas sincronizações incrementais indefinidamente, em intervalos definidos no [tutorial específico de cada aplicativo](../saas-apps/tutorial-list.md), até que um dos seguintes eventos ocorra:

- O serviço foi parado manualmente com o portal do Azure ou com o comando adequado do Graph API 
- Uma nova sincronização inicial é acionada a utilizar o **Limpar estado e reinicie** opção no portal do Azure ou com o comando adequado do Graph API. Esta ação limpa qualquer marca d'água armazenada e faz com que todos os objetos de origem a ser avaliado novamente.
- Uma nova sincronização inicial é acionada devido a uma alteração em mapeamentos de atributos ou filtros de âmbito. Esta ação também limpa qualquer marca d'água armazenada e faz com que todos os objetos de origem a ser avaliado novamente.
- O processo de aprovisionamento entra em quarentena (ver abaixo) devido a uma taxa elevada de erros e se mantenha em quarentena durante mais de quatro semanas. Neste evento, o serviço será automaticamente desativado.

### <a name="errors-and-retries"></a>Erros e de tentativas

Se um utilizador individual não pode ser adicionado, atualizado ou eliminado no sistema de destino devido a um erro no sistema de destino, a operação será repetida no próximo ciclo de sincronização. Se o utilizador continuar a falhar, as novas tentativas começarão a adotar a uma frequência reduzida, aumentar gradualmente para apenas uma tentativa por dia. Para resolver a falha, os administradores têm de verificar a [registos de auditoria](check-status-user-account-provisioning.md) "caução de processo" de eventos para determinar a raiz causar e tome as medidas adequadas. Falhas comuns podem incluir:

- Utilizadores não ter um atributo preenchido no sistema de origem que é necessário no sistema de destino
- Utilizadores com um valor de atributo no sistema de origem para o qual há uma restrição exclusiva no sistema de destino e o mesmo valor está presentes no outro registo de utilizador

Estas falhas podem ser resolvidas ao ajustar os valores de atributo para o utilizador afectado no sistema de origem, ou ao ajustar os mapeamentos de atributos para não causar conflitos.   

### <a name="quarantine"></a>Quarentena

Se a maioria ou todas as chamadas feitas no sistema de destino consistentemente falharem devido a um erro (por exemplo, para as credenciais de administrador inválido), em seguida, a tarefa de aprovisionamento entra num Estado de "quarentena". Este estado é indicado na [relatório de resumo de aprovisionamento](check-status-user-account-provisioning.md) e através de e-mail, se as notificações de e-mail foram configuradas no portal do Azure. 

Quando em quarentena, a frequência de sincronizações incrementais gradualmente é reduzida a vez por dia. 

A tarefa de aprovisionamento será removida da quarentena depois de todos os erros incorretos são fixos e o próximo ciclo de sincronização é iniciado. Se a tarefa de aprovisionamento se mantenha em quarentena durante mais de quatro semanas, a tarefa de aprovisionamento está desativada.


## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo necessário para aprovisionar os utilizadores?

Desempenho depende se a tarefa de aprovisionamento está a executar uma sincronização inicial ou uma sincronização incremental.

Para **inicial sincronizações**, o tempo de trabalho depende de vários fatores, incluindo o número de utilizadores e grupos no âmbito de aprovisionamento e o número total de utilizadores e de grupo no sistema de origem. Uma lista abrangente dos fatores que afetam o desempenho da sincronização inicial são resumidas posteriormente nesta secção.

Para **sincronizações incrementais**, o tempo de trabalho depende do número de alterações detetado nesse ciclo de sincronização. Se existirem menos de 5.000 utilizador ou alterações de associação de grupo, pode concluir a tarefa dentro de um ciclo de sincronização incremental único. 

A tabela seguinte resume as horas de sincronização para cenários comuns de aprovisionamento. Nestes cenários, o sistema de origem é o Azure AD e o sistema de destino é uma aplicação SaaS. Os tempos de sincronização são derivados de uma análise estatística dos trabalhos de sincronização para as aplicações de SaaS, ServiceNow, o local de trabalho, o Salesforce e o G Suite.


| Configuração de âmbito | Os utilizadores, grupos e membros no âmbito | Tempo de sincronização inicial | Hora de sincronização incremental |
| -------- | -------- | -------- | -------- |
| Sincronizar os utilizadores atribuídos e apenas a grupos |  < 1,000 |  < a 30 minutos | < a 30 minutos |
| Sincronizar os utilizadores atribuídos e apenas a grupos |  1.000 - 10.000 | 142 - minutos 708 | < a 30 minutos |
| Sincronizar os utilizadores atribuídos e apenas a grupos |   10,000 - 100,000 | 1,170 - minutos 2,340 | < a 30 minutos |
| Sincronizar todos os utilizadores e grupos no Azure AD |  < 1,000 | < a 30 minutos  | < a 30 minutos |
| Sincronizar todos os utilizadores e grupos no Azure AD |  1.000 - 10.000 | < 120 de 30 minutos | < a 30 minutos |
| Sincronizar todos os utilizadores e grupos no Azure AD |  10,000 - 100,000  | 713 - minutos 1,425 | < a 30 minutos |
| Sincronizar todos os utilizadores no Azure AD|  < 1,000  | < a 30 minutos | < a 30 minutos |
| Sincronizar todos os utilizadores no Azure AD | 1.000 - 10.000  | 43 - minutos de 86 | < a 30 minutos |


Para a configuração **sincronização atribuído apenas grupos de utilizadores e**, pode utilizar as seguintes fórmulas para determinar a aproximado mínimo e máximo esperado **inicial sincronização** vezes:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Resumo dos fatores que influenciam o tempo que demora a concluir uma **inicial sincronização**:

- O número total de utilizadores e grupos no âmbito de aprovisionamento.

- O número total de utilizadores, grupos e membros do grupo presentes no sistema de origem (Azure AD).

- Se os utilizadores no âmbito de aprovisionamento são correspondidos com os utilizadores existentes no aplicativo de destino, ou tem de ser criado pela primeira vez. Tarefas de sincronização para o qual todos os utilizadores são criados pela primeira vez demorar cerca de *duas vezes desde* como sincronizar tarefas para o qual todos os utilizadores são correspondidos com os utilizadores existentes.

- Número de erros no [registos de auditoria](check-status-user-account-provisioning.md). O desempenho é mais lento, se existirem muitos erros e o serviço de aprovisionamento tornou-se num Estado de quarentena.    

- Limites de velocidade e a limitação implementada pelo sistema de destino do pedido. Alguns sistemas de destino implementam limites de velocidade do pedido e limitação, que pode afetar o desempenho durante as operações de sincronização de grandes dimensões. Nestas condições, uma aplicação que recebe demasiados pedidos demasiado rápidos poderá atrasar a respetiva taxa de resposta ou fechar a ligação. Para melhorar o desempenho, o conector tem de ajustar por não enviar os pedidos de aplicações mais rapidamente do que a aplicação pode processá-los. Aprovisionamento conectores criados pela Microsoft fazer esta alteração. 

- O número e os tamanhos dos grupos atribuídos. A sincronizar grupos atribuídos exige mais tempo a sincronizar os utilizadores. O número e os tamanhos dos grupos atribuídos afetam o desempenho. Se tiver um aplicativo [mapeamentos ativados para sincronização de objetos de grupo](customize-application-attributes.md#editing-group-attribute-mappings), propriedades de grupo, tais como nomes de grupo e as associações são sincronizadas, além de usuários. Estes sincronizações adicionais irão demorar mais tempo do que apenas a sincronizar objetos de utilizador.


## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Como posso saber se os utilizadores estão a ser aprovisionados corretamente?

Todas as operações a executar o serviço de aprovisionamento de utilizador são registadas no Azure AD registos de auditoria. Isso inclui todas as operações feitas para os sistemas de origem e de destino e os dados de utilizador que foi lidos ou gravados durante cada operação de leitura e escrita.

Para obter informações sobre como ler os registos de auditoria no portal do Azure, consulte a [guia de relatórios de aprovisionamento](check-status-user-account-provisioning.md).


## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Como posso resolver problemas com o aprovisionamento de utilizadores?

Para orientação baseada em cenário sobre como resolver problemas de aprovisionamento automático de utilizadores, consulte [problemas ao configurar e aprovisionar utilizadores para uma aplicação](application-provisioning-config-problem.md).


## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Quais são as melhores práticas para implementar o aprovisionamento automático de utilizadores?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Para um plano de implementação passo a passo de exemplo para o aprovisionamento de utilizador de saída a uma aplicação, consulte a [guia de implementação de identidade para o aprovisionamento de utilizadores](https://aka.ms/userprovisioningdeploymentplan).

## <a name="more-frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>O aprovisionamento automático de utilizadores com o trabalho de aplicações SaaS com utilizadores B2B no Azure AD?

Sim, é possível utilizar o aprovisionamento de utilizadores do serviço para aprovisionar B2B (ou convidado) no Azure AD para aplicações SaaS de utilizador do Azure AD.

No entanto, para B2B aos utilizadores iniciar sessão aplicação SaaS com o Azure AD, a aplicação SaaS tem de ter seu baseado em SAML único recurso de logon configurado de forma específica. Para obter mais informações sobre como configurar aplicações de SaaS para oferecer suporte a inícios de sessão dos utilizadores B2B, consulte [aplicações de SaaS configurar colaboração B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>O aprovisionamento automático de utilizadores com o trabalho de aplicações SaaS com grupos dinâmicos no Azure AD?

Sim. Quando configurado para "utilizadores da sincronização só atribuído e grupos", o serviço de aprovisionamento de utilizador do Azure AD pode aprovisionar ou desaprovisionar utilizadores. o numa aplicação SaaS com base numa se forem membros de um [dynamic grupo](../users-groups-roles/groups-create-rule.md). Grupos dinâmicos também funcionam com a opção "Sincronizar todos os utilizadores e grupos".

No entanto, a utilização de grupos dinâmicos pode afetar o desempenho geral do aprovisionamento do Azure AD para aplicações SaaS de utilizadores de ponto-a-ponto. Quando utilizar grupos dinâmicos, mantenha estas limitações e recomendações em mente:

- Como o fast um usuário num grupo dinâmico é provisionada ou desprovisionada numa aplicação SaaS depende da rapidez o grupo dinâmico pode avaliar as alterações na associação. Para obter informações sobre como verificar o estado de processamento de um grupo dinâmico, consulte [verificar o estado de processamento para uma regra de associação](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Quando utilizar grupos dinâmicos, as regras devem ser seriamente consideradas com utilizador provisionamento e desprovisionamento em mente, como a perda de resultados de associação num evento de desaprovisionamento.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>O aprovisionamento automático de utilizadores com o trabalho de aplicações SaaS com grupos aninhados no Azure AD?

Não. Quando configurado para "utilizadores da sincronização só atribuído e grupos", o serviço de aprovisionamento de utilizador do Azure AD não é possível ler ou aprovisionar utilizadores que estão em grupos aninhados. Só é capaz de ler e disponibilize para os usuários que são membros imediatos do grupo atribuído explicitamente.

Esta é uma limitação "baseado no grupo de atribuições de aplicações", que também afeta o início de sessão único e está descrito [com um grupo para gerir o acesso a aplicações SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Como solução, deve atribuir explicitamente (ou, caso contrário [definir o âmbito no](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) os grupos que contêm os utilizadores que precisam de ser aprovisionado.

## <a name="related-articles"></a>Artigos relacionados

- [Lista de tutoriais sobre como integrar aplicações SaaS](../saas-apps/tutorial-list.md)
- [Personalizar mapeamentos de atributos para o aprovisionamento do utilizador](customize-application-attributes.md)
- [Escrever expressões para mapeamentos de atributos](functions-for-customizing-application-data.md)
- [Filtros de âmbito para o aprovisionamento do utilizador](define-conditional-rules-for-provisioning-user-accounts.md)
- [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](use-scim-to-provision-users-and-groups.md)
- [Descrição geral de API de sincronização do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
