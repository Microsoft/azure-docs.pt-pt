---
title: Problema ao configurar o aprovisionamento de utilizadores a uma aplicação da galeria do Azure AD | Documentos da Microsoft
description: Como resolver problemas comuns enfrentados ao configurar aprovisionamento de utilizador para uma aplicação já listados na Galeria de aplicações do Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf496e448cbc11b9e986ca3b58c956c4cd18a34e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113457"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problema ao configurar o aprovisionamento de utilizadores a uma aplicação da galeria do Azure AD

Configurando [aprovisionamento automático de utilizadores](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) para uma aplicação (onde for suportado), necessita de ser seguida de instruções específicas para preparar a aplicação para aprovisionamento automático. Em seguida, pode utilizar o portal do Azure para configurar o serviço de aprovisionamento para sincronizar contas de utilizador para a aplicação.

Deve sempre começar localizando o tutorial de configuração específicas para configurar o aprovisionamento para a sua aplicação. Em seguida, siga os passos para configurar a aplicação e o Azure AD para criar a ligação de aprovisionamento. Uma lista de tutoriais de aplicação pode ser encontrada em [lista de tutoriais sobre como integrar aplicações de SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Como ver se o aprovisionamento está a funcionar 

Assim que o serviço está configurado, a maioria das informações sobre o funcionamento do serviço podem ser desenhadas partir de dois locais:

-   **Registos de auditoria** – os aprovisionamento registos de auditoria registo atribuído de todas as operações executadas pelo serviço de aprovisionamento, incluindo a consultar o Azure AD para utilizadores que estão no âmbito de aprovisionamento. Consulte a aplicação de destino para a existência desses usuários, comparando os objetos de utilizador entre o sistema. Em seguida, adicionar, atualizar ou desativar a conta de utilizador no sistema de destino com base na comparação. Os registos de auditoria de aprovisionamento podem ser acedidos no portal do Azure, no **do Azure Active Directory &gt; aplicações empresariais &gt; \[nome da aplicação\] &gt; registos de auditoria** separador. Filtrar os registos a **aprovisionamento da conta** categoria para ver apenas os eventos de aprovisionamento para essa aplicação.

-   **Estado – de aprovisionamento** um resumo do provisionamento última execução para uma determinada aplicação pode ser vista na **do Azure Active Directory &gt; aplicações empresariais &gt; \[nome da aplicação\] &gt;Aprovisionamento** secção, na parte inferior do ecrã sob as configurações de serviço. Esta secção resume quantos utilizadores (e/ou grupos) estão atualmente a ser sincronizados entre os dois sistemas, e se existem erros. Detalhes do erro ser nos registos de auditoria. Tenha em atenção que o estado de aprovisionamento não ser preenchida até estar concluída uma sincronização completa inicial entre o Azure AD e a aplicação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas com problemas gerais de aprovisionamento a considerar

Segue-se uma lista das áreas de problema geral que pode pormenorizar se tiver uma idéia de onde começar.

* [Serviço de aprovisionamento não é apresentado ao iniciar](#provisioning-service-does-not-appear-to-start)
* Não é possível guardar a configuração devido a credenciais de aplicação não está a funcionar
* [Registos de auditoria dizer que os utilizadores são "ignorados" e não aprovisionados, mesmo que estão atribuídos](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de aprovisionamento não é apresentado ao iniciar

Se definir o **estado de aprovisionamento** ser **no** no **Azure Active Directory &gt; aplicações empresariais &gt; \[nome da aplicação\] &gt;Aprovisionamento** seção do portal do Azure. No entanto, não existem outros detalhes de estado são apresentados nessa página depois recarrega subsequentes. É provável que o serviço está em execução, mas não concluiu ainda uma sincronização inicial. Verifique os **registos de auditoria** descrito acima para determinar as operações que o serviço está a ser executado, e se existem erros.

>[!NOTE]
>Uma sincronização inicial pode demorar entre 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e o número de utilizadores no âmbito de aprovisionamento. Sincronizações subsequentes após a sincronização inicial estar mais rápido, como o serviço de aprovisionamento armazena as marcas d'água que representam o estado dos dois sistemas após a sincronização inicial, melhorando o desempenho de sincronizações subsequentes.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Não é possível guardar a configuração devido a credenciais de aplicação não está a funcionar

Por ordem para o aprovisionamento para funcionar, o Azure AD requer credenciais válidas, que lhe permitem ligar a um API fornecida por essa aplicação de gestão de utilizadores. Se estas credenciais não funcionam, ou não sabe o que são, veja o tutorial para configurar esta aplicação, descrita anteriormente.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Registos de auditoria dizer que os utilizadores são ignorados e não aprovisionados, mesmo que estão atribuídos

Quando um utilizador aparece como "ignorada" nos registos de auditoria, é muito importante ler os obter mais detalhes na mensagem de registo para determinar o motivo. Seguem-se motivos e resoluções comuns:

- **Foi configurado um filtro de âmbito** **que é filtrar o utilizador com base num valor de atributo**. Para obter mais informações sobre filtros de âmbito, veja <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

- **O utilizador é "não efetivamente ultrapassará".** Se vir esta mensagem de erro específico, é porque não existe um problema com o registo de atribuição do utilizador armazenado no Azure AD. Para corrigir este problema, o utilizador de anular a atribuição (ou grupo) a partir da aplicação e, reatribuí-la novamente. Para obter mais informações sobre a atribuição, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

- **Um atributo obrigatório está em falta ou não preenchida para um utilizador.** Uma coisa importante a considerar ao configurar o aprovisionamento ser para rever e configurar os mapeamentos de atributos e fluxos de trabalho que definem qual usuário (ou grupo) propriedades fluxo a partir do Azure AD à aplicação. Isto inclui a definição "da propriedade correspondente" que ser utilizado para identificar exclusivamente e corresponder ao utilizadores/grupos entre os dois sistemas. Para obter mais informações sobre este processo importante, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

  * **Mapeamentos de atributos para grupos:** Aprovisionamento do nome do grupo e detalhes do grupo, além de membros, se for suportado para alguns aplicativos. É possível habilitar ou desabilitar essa funcionalidade ativando ou desativando a **mapeamento** para objetos de grupo, mostrados na **aprovisionamento** separador. Se o provisionamento de grupos estiver ativado, certifique-se de que reveja os mapeamentos de atributos para garantir que um campo adequado, está a ser utilizado para o ID"correspondente". Isso pode ser o alias de e-mail ou nome de exibição), como o grupo e seus membros não ser aprovisionado se a propriedade correspondente estiver vazio ou não preenchida para um grupo no Azure AD.

## <a name="next-steps"></a>Passos Seguintes
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e o desaprovisionamento de utilizadores em Aplicações SaaS com o Azure Active Directory)
