---
title: Não existem utilizadores estão a ser aprovisionados para um aplicativo de galeria do Azure AD | Documentos da Microsoft
description: Como resolver problemas comuns enfrentados ao não vir os utilizadores que aparecem num aplicativo de galeria do Azure AD que configurou para o aprovisionamento de utilizadores com o Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaeb97f88c2482cb9d091afb1c205e9b09a85ce0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784577"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Não existem utilizadores estão a ser aprovisionados para um aplicativo de galeria do Azure AD
Após o aprovisionamento automático foi configurado para uma aplicação (incluindo a verificar que as credenciais de aplicação fornecidas para o Azure AD para ligar à aplicação são válidas), em seguida, os utilizadores e/ou grupos são aprovisionados para a aplicação. Aprovisionamento é determinado através dos seguintes pontos:

-   Que utilizadores e grupos foram **atribuídos** à aplicação. Para obter mais informações sobre a atribuição, consulte [atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Active Directory](assign-user-or-group-access-portal.md).
-   Se pretende ou não **mapeamentos de atributo** estão ativados e configurados para sincronizar atributos válidos do Azure AD para a aplicação. Para obter mais informações sobre mapeamentos de atributos, consulte [personalizando aprovisionamento atributo mapeamentos de utilizador para aplicações de SaaS no Azure Active Directory](customize-application-attributes.md).
-   Se é ou não existe uma **filtro de âmbito** que está a filtrar os utilizadores com base nos valores de atributo específico. Para obter mais informações sobre filtros de âmbito, veja [aprovisionamento de aplicações baseadas em atributos com filtros de âmbito](define-conditional-rules-for-provisioning-user-accounts.md).
  
Se observar que os utilizadores não estão a ser aprovisionados, consulte os registos de auditoria no Azure AD. Procurar entradas de registo de um utilizador específico.

Os registos de auditoria de aprovisionamento podem ser acedidos no portal do Azure, no **do Azure Active Directory &gt; aplicações empresariais &gt; \[nome da aplicação\] &gt; registos de auditoria** separador. Filtrar os registos a **aprovisionamento da conta** categoria para ver apenas os eventos de aprovisionamento para essa aplicação. Pode procurar utilizadores com base no "ID correspondente" que foi configurado para os mesmos nos mapeamentos de atributos. Por exemplo, se tiver configurado o "nome principal de utilizador" ou "endereço de e-mail" como o atributo correspondente no lado do Azure AD e o utilizador está a ser aprovisionamento não tem um valor de "audrey@contoso.com", em seguida, procurar os registos de auditoria para "audrey@contoso.com" e reveja as entradas devolvido.

A auditoria de aprovisionamento regista todas as operações efetuadas pelo serviço de aprovisionamento, incluindo a consultar o Azure AD para os utilizadores atribuídos, que se encontrem no âmbito para o aprovisionamento, consultando a aplicação de destino para a existência dos utilizadores, comparando os objetos de utilizador de registo entre o sistema. Em seguida, adicionar, atualizar ou desativar a conta de utilizador no sistema de destino com base na comparação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas com problemas gerais com aprovisionamento a considerar
Segue-se uma lista das áreas de problema geral que pode pormenorizar se tiver uma idéia de onde começar.

- [Serviço de aprovisionamento não é apresentado ao iniciar](#provisioning-service-does-not-appear-to-start)
- [Registos de auditoria dizer que os utilizadores são ignorados e não aprovisionados, mesmo que estão atribuídos](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de aprovisionamento não é apresentado ao iniciar
Se definir o **estado de aprovisionamento** ser **no** no **Azure Active Directory &gt; aplicações empresariais &gt; \[nome da aplicação\] &gt;Aprovisionamento** seção do portal do Azure. No entanto não outros detalhes de estado são apresentados nessa página depois recarrega subsequentes, é provável que o serviço está em execução, mas não concluiu ainda uma sincronização inicial. Verifique os **registos de auditoria** descrito acima para determinar as operações que o serviço está a ser executado, e se existem erros.

>[!NOTE]
>Uma sincronização inicial pode demorar entre 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e o número de utilizadores no âmbito de aprovisionamento. Sincronizações subsequentes após a sincronização inicial são mais rápidas, como o serviço de aprovisionamento armazena as marcas d'água que representam o estado dos dois sistemas após a sincronização inicial. A sincronização inicial melhora o desempenho de sincronizações subsequentes.
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Registos de auditoria dizer que os utilizadores são ignorados e não aprovisionados, mesmo que estão atribuídos

Quando um utilizador aparece como "ignorada" nos registos de auditoria, é importante ler os obter mais detalhes na mensagem de registo para determinar o motivo. Seguem-se motivos e resoluções comuns:

- **Foi configurado um filtro de âmbito** **que é filtrar o utilizador com base num valor de atributo**. Para obter mais informações sobre filtros de âmbito, veja [filtros de âmbito](define-conditional-rules-for-provisioning-user-accounts.md).
- **O utilizador é "não efetivamente ultrapassará".** Se vir esta mensagem de erro específico, é porque não existe um problema com o registo de atribuição do utilizador armazenado no Azure AD. Para corrigir este problema, anular a atribuição de utilizador (ou grupo) a partir da aplicação e reatribuí-la novamente. Para obter mais informações sobre a atribuição, consulte [atribuir acesso de utilizador ou grupo](assign-user-or-group-access-portal.md).
- **Um atributo obrigatório está em falta ou não preenchida para um utilizador.** É uma coisa importante a considerar ao configurar o aprovisionamento rever e configurar os mapeamentos de atributos e fluxos de trabalho que definem qual usuário (ou grupo) propriedades fluxo a partir do Azure AD à aplicação. Esta configuração inclui a definição de "propriedade correspondente" que é utilizada para identificar exclusivamente e corresponder ao utilizadores/grupos entre os dois sistemas. Para obter mais informações sobre este processo importante, consulte [personalizando aprovisionamento atributo mapeamentos de utilizador para aplicações de SaaS no Azure Active Directory](customize-application-attributes.md).
- **Mapeamentos de atributos para grupos:** Aprovisionamento do nome do grupo e detalhes do grupo, além de membros, se for suportado para alguns aplicativos. É possível habilitar ou desabilitar essa funcionalidade ativando ou desativando a **mapeamento** para objetos de grupo, mostrados na **aprovisionamento** separador. Se o provisionamento de grupos estiver ativado, certifique-se de que reveja os mapeamentos de atributos para garantir que um campo adequado, está a ser utilizado para o ID"correspondente". O ID correspondente pode ser o alias de e-mail ou nome de exibição. O grupo e seus membros não são aprovisionados se a propriedade correspondente estiver vazio ou não preenchida para um grupo no Azure AD.

## <a name="next-steps"></a>Passos Seguintes

[Sincronização do Azure AD Connect: Understanding Declarative Provisioning](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md) (Sincronização do Azure AD Connect: Compreender o Aprovisionamento Declarativo)
