---
title: Nenhum usuário está sendo provisionado para um aplicativo da galeria do Azure AD | Microsoft Docs
description: Como solucionar problemas comuns enfrentados quando você não vê os usuários aparecendo em um aplicativo da galeria do Azure AD que você configurou para o provisionamento de usuários com o Azure AD
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
ms.openlocfilehash: b80539181e6614283b6170b9cd9d4db85f812a5f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879897"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nenhum usuário está sendo provisionado para um aplicativo da galeria do Azure AD
Após o provisionamento automático ter sido configurado para um aplicativo (incluindo a verificação de que as credenciais do aplicativo fornecidas ao Azure AD para se conectar ao aplicativo são válidas), os usuários e/ou grupos são provisionados para o aplicativo. O provisionamento é determinado pelos seguintes itens:

-   Quais usuários e grupos foram **atribuídos** ao aplicativo. Observe que não há suporte para o provisionamento de grupos aninhados ou grupos do Office 365. Para obter mais informações sobre atribuição, consulte [atribuir um usuário ou grupo a um aplicativo empresarial no Azure Active Directory](assign-user-or-group-access-portal.md).
-   Se os mapeamentos de **atributo** estão ou não habilitados e configurados para sincronizar atributos válidos do Azure ad para o aplicativo. Para obter mais informações sobre mapeamentos de atributo, consulte [Personalizando mapeamentos de atributo de provisionamento de usuário para aplicativos SaaS em Azure Active Directory](customize-application-attributes.md).
-   Se há ou não um **filtro de escopo** presente que esteja filtrando usuários com base em valores de atributo específicos. Para obter mais informações sobre filtros de escopo, consulte [provisionamento de aplicativo baseado em atributo com filtros de escopo](define-conditional-rules-for-provisioning-user-accounts.md).
  
Se você observar que os usuários não estão sendo provisionados, consulte os logs de auditoria no Azure AD. Pesquise por entradas de log para um usuário específico.

Os logs de auditoria de provisionamento podem ser acessados no portal do Azure, na **guia &gt; logs de &gt; auditoria Azure Active Directory\] nome &gt; de aplicativo dos aplicativos \[empresariais** . Filtre os logs na categoria de **provisionamento de conta** para ver apenas os eventos de provisionamento para esse aplicativo. Você pode pesquisar usuários com base na "ID correspondente" que foi configurada para eles nos mapeamentos de atributo. Por exemplo, se você configurou o "nome UPN" ou "endereço de email" como o atributo correspondente no lado do Azure AD, e o usuário que não está sendo provisionado tem um valor deaudrey@contoso.com"", então pesquise os logs de auditoriaaudrey@contoso.compara "" e revise as entradas exibido.

Os logs de auditoria de provisionamento registram todas as operações executadas pelo serviço de provisionamento, incluindo a consulta do Azure AD para usuários atribuídos que estão no escopo do provisionamento, consultando o aplicativo de destino para a existência desses usuários, comparando os objetos de usuário entre o sistema. Em seguida, adicione, atualize ou desabilite a conta de usuário no sistema de destino com base na comparação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas problemáticas gerais com provisionamento a ser considerado
Abaixo está uma lista das áreas problemáticas gerais que você pode analisar se tiver uma ideia de onde começar.

- [O serviço de provisionamento não parece iniciar](#provisioning-service-does-not-appear-to-start)
- [Logs de auditoria dizem que os usuários são ignorados e não provisionados, embora sejam atribuídos](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>O serviço de provisionamento não parece iniciar
Se você definir o **status de provisionamento** como **on** na seção  **&gt; Azure Active Directory aplicativos empresariais \[ &gt; de provisionamento de nome\] &gt;de aplicativo** do portal do Azure . No entanto, nenhum outro detalhe de status é mostrado nessa página após recargas subsequentes, é provável que o serviço esteja em execução, mas ainda não tenha concluído uma sincronização inicial. Verifique os **logs de auditoria** descritos acima para determinar quais operações o serviço está executando e se há erros.

>[!NOTE]
>Uma sincronização inicial pode levar de 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento. Sincronizações subsequentes após a sincronização inicial são mais rápidas, uma vez que o serviço de provisionamento armazena marcas d' água que representam o estado de ambos os sistemas após a sincronização inicial. A sincronização inicial melhora o desempenho das sincronizações subsequentes.
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Logs de auditoria dizem que os usuários são ignorados e não provisionados mesmo que sejam atribuídos

Quando um usuário aparece como "ignorado" nos logs de auditoria, é importante ler os detalhes estendidos na mensagem de log para determinar o motivo. Veja abaixo os motivos e as resoluções comuns:

- **Um filtro de escopo foi configurado** **isso está filtrando o usuário com base em um valor de atributo**. Para obter mais informações sobre filtros de escopo, consulte [filtros de escopo](define-conditional-rules-for-provisioning-user-accounts.md).
- **O usuário é "não é efetivamente qualificado".** Se você vir essa mensagem de erro específica, é porque há um problema com o registro de atribuição de usuário armazenado no Azure AD. Para corrigir esse problema, cancele a atribuição do usuário (ou grupo) do aplicativo e reatribua-o novamente. Para obter mais informações sobre atribuição, consulte [atribuir acesso de usuário ou grupo](assign-user-or-group-access-portal.md).
- **Um atributo necessário está ausente ou não foi populado para um usuário.** Uma coisa importante a ser considerada ao configurar o provisionamento é examinar e configurar os mapeamentos de atributo e os fluxos de trabalho que definem quais propriedades de usuário (ou grupo) fluem do Azure AD para o aplicativo. Essa configuração inclui a definição da "propriedade correspondente" que é usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Para obter mais informações sobre esse processo importante, consulte [Personalizando mapeamentos de atributo de provisionamento de usuário para aplicativos SaaS no Azure Active Directory](customize-application-attributes.md).
- **Mapeamentos de atributo para grupos:** Provisionamento do nome do grupo e dos detalhes do grupo, além dos membros, se houver suporte para alguns aplicativos. Você pode habilitar ou desabilitar essa funcionalidade habilitando ou desabilitando o **mapeamento** para objetos de grupo mostrados na guia **provisionamento** . Se o provisionamento de grupos estiver habilitado, certifique-se de examinar os mapeamentos de atributo para garantir que um campo apropriado esteja sendo usado para a "ID correspondente". A ID de correspondência pode ser o nome de exibição ou alias de email. O grupo e seus membros não serão provisionados se a propriedade correspondente estiver vazia ou não for preenchida para um grupo no Azure AD.

## <a name="next-steps"></a>Passos Seguintes

[Sincronização do Azure AD Connect: Understanding Declarative Provisioning](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md) (Sincronização do Azure AD Connect: Compreender o Aprovisionamento Declarativo)
