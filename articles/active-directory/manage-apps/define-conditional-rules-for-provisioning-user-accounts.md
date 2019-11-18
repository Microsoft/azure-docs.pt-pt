---
title: Provisionar aplicativos com filtros de escopo | Microsoft Docs
description: Saiba como usar filtros de escopo para impedir que objetos em aplicativos que dão suporte ao provisionamento automatizado de usuários sejam provisionados se um objeto não atender aos seus requisitos de negócios.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82360dacd68de512bc12ff5d39ddbd3a21578aa7
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74120110"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Provisionamento de aplicativo baseado em atributo com filtros de escopo
O objetivo deste artigo é explicar como usar filtros de escopo para definir regras baseadas em atributo que determinam quais usuários são provisionados para um aplicativo.

## <a name="scoping-filter-use-cases"></a>Casos de uso de filtro de escopo

Um filtro de escopo permite que o serviço de provisionamento do Azure Active Directory (Azure AD) inclua ou exclua qualquer usuário que tenha um atributo que corresponda a um valor específico. Por exemplo, ao provisionar usuários do Azure AD para um aplicativo SaaS usado por uma equipe de vendas, você pode especificar que somente os usuários com um atributo "Department" de "Sales" devem estar no escopo para provisionamento.

Os filtros de escopo podem ser usados de forma diferente, dependendo do tipo de conector de provisionamento:

* **Provisionamento de saída do Azure ad para aplicativos SaaS**. Quando o Azure AD é o sistema de origem, as [atribuições de usuário e grupo](assign-user-or-group-access-portal.md) são o método mais comum para determinar quais usuários estão no escopo para provisionamento. Essas atribuições também são usadas para habilitar o logon único e fornecer um único método para gerenciar o acesso e o provisionamento. Os filtros de escopo podem ser usados opcionalmente, além de atribuições ou em vez de eles, para filtrar os usuários com base em valores de atributo.

    >[!TIP]
    > Você pode desabilitar o provisionamento com base nas atribuições de um aplicativo empresarial alterando as configurações no menu [escopo](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) nas configurações de provisionamento para **sincronizar todos os usuários e grupos**. Usar essa opção mais filtros de escopo com base em atributo oferece um desempenho mais rápido do que usar atribuições baseadas em grupo.  

* **Provisionamento de entrada de aplicativos HCM para o Azure AD e Active Directory**. Quando um [aplicativo HCM como o workday](../saas-apps/workday-tutorial.md) é o sistema de origem, os filtros de escopo são o principal método para determinar quais usuários devem ser provisionados do aplicativo HCM para Active Directory ou o Azure AD.

Por padrão, os conectores de provisionamento do Azure AD não têm nenhum filtro de escopo baseado em atributo configurado. 

## <a name="scoping-filter-construction"></a>Construção de filtro de escopo

Um filtro de escopo consiste em uma ou mais *cláusulas*. As cláusulas determinam quais usuários têm permissão para passar pelo filtro de escopo, avaliando os atributos de cada usuário. Por exemplo, você pode ter uma cláusula que exija que o atributo "estado" de um usuário seja igual a "Nova York", de modo que somente os usuários de Nova York sejam provisionados no aplicativo. 

Uma única cláusula define uma única condição para um valor de atributo único. Se várias cláusulas forem criadas em um único filtro de escopo, elas serão avaliadas juntas usando a lógica "e". Isso significa que todas as cláusulas devem ser avaliadas como "true" para que um usuário seja provisionado.

Por fim, vários filtros de escopo podem ser criados para um único aplicativo. Se houver vários filtros de escopo, eles serão avaliados juntos usando a lógica "ou". Isso significa que, se todas as cláusulas em qualquer um dos filtros de escopo configurados forem avaliadas como "true", o usuário será provisionado.

Cada usuário ou grupo processado pelo serviço de provisionamento do Azure AD é sempre avaliado individualmente em relação a cada filtro de escopo.

Como exemplo, considere o seguinte filtro de escopo:

![Filtro de escopo](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

De acordo com esse filtro de escopo, os usuários devem atender aos seguintes critérios para serem provisionados:

* Eles devem estar em Nova York.
* Eles devem trabalhar no departamento de engenharia.
* A ID de funcionário da empresa deve estar entre 1 milhão e 2 milhões.
* O título do trabalho não deve ser nulo ou estar vazio.

## <a name="create-scoping-filters"></a>Criar filtros de escopo
Os filtros de escopo são configurados como parte dos mapeamentos de atributo para cada conector de provisionamento de usuário do Azure AD. O procedimento a seguir pressupõe que você já configurou o provisionamento automático para [um dos aplicativos com suporte](../saas-apps/tutorial-list.md) e está adicionando um filtro de escopo a ele.

### <a name="create-a-scoping-filter"></a>Criar um filtro de escopo
1. Na [portal do Azure](https://portal.azure.com), acesse a seção **Azure Active Directory** > **aplicativos empresariais** > **todos os aplicativos** .

2. Selecione o aplicativo para o qual você configurou o provisionamento automático: por exemplo, "ServiceNow".

3. Selecione a guia **provisionamento** .

4. Na seção **mapeamentos** , selecione o mapeamento para o qual você deseja configurar um filtro de escopo: por exemplo, "sincronizar Azure Active Directory usuários com o ServiceNow".

5. Selecione o menu **escopo do objeto de origem** .

6. Selecione **Adicionar filtro de escopo**.

7. Defina uma cláusula selecionando um **nome de atributo**de origem, um **operador**e um **valor de atributo** para correspondência. Há suporte para os seguintes operadores:

   a. **É igual a**. A cláusula retornará "true" se o atributo avaliado corresponder ao valor da cadeia de caracteres de entrada exatamente (maiúsculas e minúsculas).

   b. **não é igual a**. A cláusula retornará "true" se o atributo avaliado não corresponder ao valor da cadeia de caracteres de entrada (diferencia maiúsculas de minúsculas).

   c. **é verdadeiro**. A cláusula retornará "true" se o atributo avaliado contiver um valor booliano de true.

   d. **é false**. A cláusula retornará "true" se o atributo avaliado contiver um valor booliano de false.

   e. **é nulo**. A cláusula retornará "true" se o atributo avaliado estiver vazio.

   f. **não é nulo**. A cláusula retornará "true" se o atributo avaliado não estiver vazio.

   g. **correspondência de Regex**. A cláusula retornará "true" se o atributo avaliado corresponder a um padrão de expressão regular. Por exemplo: ([1-9] [0-9]) corresponde a qualquer número entre 10 e 99.

   h. **não há correspondência de Regex**. A cláusula retornará "true" se o atributo avaliado não corresponder a um padrão de expressão regular.

8. Selecione **Adicionar novo escopo cláusula**.

9. Opcionalmente, repita as etapas 7-8 para adicionar mais cláusulas de escopo.

10. Em **título do filtro de escopo**, adicione um nome para o filtro de escopo.

11. Selecione **OK**.

12. Selecione **OK** novamente na tela **filtros de escopo** . Opcionalmente, repita as etapas 6-11 para adicionar outro filtro de escopo.

13. Selecione **salvar** na tela de **mapeamento de atributos** . 

>[!IMPORTANT] 
> Salvar um novo filtro de escopo dispara uma nova sincronização completa para o aplicativo, onde todos os usuários no sistema de origem são avaliados novamente em relação ao novo filtro de escopo. Se um usuário no aplicativo estava anteriormente no escopo para provisionamento, mas estiver fora do escopo, sua conta será desabilitada ou desprovisionada no aplicativo. Para substituir esse comportamento padrão, consulte [ignorar a exclusão de contas de usuário que saem do escopo](skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Filtros de escopo comuns
| Atributo de destino| Operador | Valor | Descrição|
|----|----|----|----|
|userPrincipalName|CORRESPONDÊNCIA DE REGEX|.\*@domain.com |Todos os usuários com userPrincipal que têm o domínio @domain.com estarão no escopo para provisionamento|
|userPrincipalName|NÃO CORRESPONDÊNCIA DE REGEX|.\*@domain.com|Todos os usuários com userPrincipal que têm o domínio @domain.com estarão fora do escopo para provisionamento|
|Departamento|SEJA|impostos|Todos os usuários do departamento de vendas estão no escopo para provisionamento|
|workerid|CORRESPONDÊNCIA DE REGEX|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Todos os funcionários com workerIDs entre 1 milhão e 2 milhões estão no escopo para provisionamento.|

## <a name="related-articles"></a>Artigos relacionados
* [Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS](user-provisioning.md)
* [Personalizar mapeamentos de atributo para provisionamento de usuário](customize-application-attributes.md)
* [Escrever expressões para mapeamentos de atributos](functions-for-customizing-application-data.md)
* [Notificações de provisionamento de conta](user-provisioning.md)
* [Use o SCIM para habilitar o provisionamento automático de usuários e grupos de Azure Active Directory para aplicativos](use-scim-to-provision-users-and-groups.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)

