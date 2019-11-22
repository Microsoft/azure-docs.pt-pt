---
title: Problema ao configurar o provisionamento de usuário para um aplicativo da galeria do Azure AD
description: Como solucionar problemas comuns enfrentados ao configurar o provisionamento de usuário para um aplicativo já listado na Galeria de aplicativos do Azure AD
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
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55b9b8dae6ff47099935f42f75286b1b4ddd3708
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275748"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problema ao configurar o provisionamento de usuário para um aplicativo da galeria do Azure AD

A configuração do [provisionamento automático de usuário](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) para um aplicativo (onde há suporte) requer que instruções específicas sejam seguidas para preparar o aplicativo para provisionamento automático. Em seguida, você pode usar o portal do Azure para configurar o serviço de provisionamento para sincronizar contas de usuário para o aplicativo.

Você deve sempre começar encontrando o tutorial de instalação específico para configurar o provisionamento para seu aplicativo. Em seguida, siga essas etapas para configurar o aplicativo e o Azure AD para criar a conexão de provisionamento. Uma lista de tutoriais do aplicativo pode ser encontrada na [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Como ver se o provisionamento está funcionando 

Depois que o serviço é configurado, a maioria das informações sobre a operação do serviço pode ser desenhada de dois locais:

-   **Provisionando logs (versão prévia)** – os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) registram todas as operações executadas pelo serviço de provisionamento, incluindo a consulta do Azure ad para usuários atribuídos que estão no escopo do provisionamento. Consulte o aplicativo de destino para a existência desses usuários, comparando os objetos de usuário entre o sistema. Em seguida, adicione, atualize ou desabilite a conta de usuário no sistema de destino com base na comparação. Você pode acessar os logs de provisionamento no portal do Azure selecionando **Azure Active Directory** &gt; **aplicativos empresariais** &gt; **Provisionando logs (versão prévia)** na seção **atividade** .

-   **Status atual –** Um resumo da última execução de provisionamento para um determinado aplicativo pode ser visto no **Azure Active Directory &gt; aplicativos empresariais &gt; \[nome do aplicativo\] &gt;seção provisionamento** , na parte inferior da tela, nas configurações do serviço. A seção status atual mostra se um ciclo de provisionamento começou a provisionar contas de usuário. Você pode ver o progresso do ciclo, ver quantos usuários e grupos foram provisionados e ver quantas funções são criadas. Se houver erros, os detalhes poderão ser encontrados nos logs de [provisionamento (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas problemáticas gerais com provisionamento a ser considerado

Abaixo está uma lista das áreas problemáticas gerais que você pode analisar se tiver uma ideia de onde começar.

* [O serviço de provisionamento não parece iniciar](#provisioning-service-does-not-appear-to-start)
* Não é possível salvar a configuração porque as credenciais do aplicativo não estão funcionando
* [Os logs de provisionamento dizem que os usuários são "ignorados" e não provisionados, embora sejam atribuídos](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>O serviço de provisionamento não parece iniciar

Se você definir o **status de provisionamento** como **On** no **Azure Active Directory &gt; Enterprise apps &gt; \[nome do aplicativo\] &gt;** seção de provisionamento do portal do Azure. No entanto, nenhum outro detalhe de status é mostrado nessa página após recargas subsequentes. É provável que o serviço esteja em execução, mas ainda não tenha concluído um ciclo inicial. Verifique os **logs de provisionamento** descritos acima para determinar quais operações o serviço está executando e se há erros.

>[!NOTE]
>Um ciclo inicial pode levar de 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento. As sincronizações subsequentes após o ciclo inicial serão mais rápidas, pois o serviço de provisionamento armazena as marcas d' água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Não é possível salvar a configuração porque as credenciais do aplicativo não estão funcionando

Para que o provisionamento funcione, o Azure AD requer credenciais válidas que permitem que ele se conecte a uma API de gerenciamento de usuário fornecida pelo aplicativo. Se essas credenciais não funcionarem ou se você não souber o que elas são, examine o tutorial para configurar esse aplicativo, descrito anteriormente.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Logs de provisionamento, digamos que os usuários sejam ignorados e não provisionados mesmo que sejam atribuídos

Quando um usuário aparece como "ignorado" nos logs de provisionamento, é muito importante ler os detalhes estendidos na mensagem de log para determinar o motivo. Veja abaixo os motivos e as resoluções comuns:

- Foi **configurado um filtro de escopo** **que está filtrando o usuário com base em um valor de atributo**. Para obter mais informações sobre filtros de escopo, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

- **O usuário é "não é efetivamente qualificado".** Se você vir essa mensagem de erro específica, é porque há um problema com o registro de atribuição de usuário armazenado no Azure AD. Para corrigir esse problema, cancele a atribuição do usuário (ou grupo) do aplicativo e reatribua-o novamente. Para obter mais informações sobre atribuição, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

- **Um atributo necessário está ausente ou não foi populado para um usuário.** Uma coisa importante a ser considerada ao configurar o provisionamento é revisar e configurar os mapeamentos de atributo e os fluxos de trabalho que definem quais propriedades de usuário (ou grupo) fluem do Azure AD para o aplicativo. Isso inclui a definição da "propriedade correspondente" que é usada para identificar e corresponder exclusivamente usuários/grupos entre os dois sistemas. Para obter mais informações sobre esse processo importante, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

  * **Mapeamentos de atributo para grupos:** Provisionamento do nome do grupo e dos detalhes do grupo, além dos membros, se houver suporte para alguns aplicativos. Você pode habilitar ou desabilitar essa funcionalidade habilitando ou desabilitando o **mapeamento** para objetos de grupo mostrados na guia **provisionamento** . Se o provisionamento de grupos estiver habilitado, certifique-se de examinar os mapeamentos de atributo para garantir que um campo apropriado esteja sendo usado para a "ID correspondente". Pode ser o nome de exibição ou alias de email), pois o grupo e seus membros não serão provisionados se a propriedade correspondente estiver vazia ou não for preenchida para um grupo no Azure AD.

## <a name="next-steps"></a>Passos seguintes
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e o desaprovisionamento de utilizadores em Aplicações SaaS com o Azure Active Directory)
