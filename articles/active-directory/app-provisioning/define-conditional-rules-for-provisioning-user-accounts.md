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
ms.openlocfilehash: 41a4eb565f49b75b64cc072d3017c41000154e2a
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066827"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Provisionamento de aplicativo baseado em atributo com filtros de escopo
O objetivo deste artigo é explicar como usar filtros de escopo para definir regras baseadas em atributo que determinam quais usuários são provisionados para um aplicativo.

## <a name="scoping-filter-use-cases"></a>Casos de uso de filtro de escopo

Um filtro de escopo permite que o serviço de provisionamento do Azure Active Directory (Azure AD) inclua ou exclua qualquer usuário que tenha um atributo que corresponda a um valor específico. Por exemplo, ao provisionar usuários do Azure AD para um aplicativo SaaS usado por uma equipe de vendas, você pode especificar que somente os usuários com um atributo "Department" de "Sales" devem estar no escopo para provisionamento.

Os filtros de escopo podem ser usados de forma diferente, dependendo do tipo de conector de provisionamento:

* **Fornecimento de saída da AD Azure para aplicações SaaS.** Quando o Azure AD é o sistema de origem, as atribuições de [utilizadores e grupos](../manage-apps/assign-user-or-group-access-portal.md) são o método mais comum para determinar quais os utilizadores que estão no âmbito do provisionamento. Essas atribuições também são usadas para habilitar o logon único e fornecer um único método para gerenciar o acesso e o provisionamento. Os filtros de escopo podem ser usados opcionalmente, além de atribuições ou em vez de eles, para filtrar os usuários com base em valores de atributo.

    >[!TIP]
    > Pode desativar o fornecimento com base em atribuições para uma aplicação empresarial alterando as definições no menu [Scope](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) sob as definições de provisionamento para **Sincronizar todos os utilizadores e grupos**. Usar essa opção mais filtros de escopo com base em atributo oferece um desempenho mais rápido do que usar atribuições baseadas em grupo.  

* **Fornecimento de entrada a partir de aplicações HCM para Azure AD e Ative Diretório**. Quando uma [aplicação HCM como](../saas-apps/workday-tutorial.md) o Workday é o sistema de origem, os filtros de deteção são o principal método para determinar quais os utilizadores que devem ser provisionados a partir da aplicação HCM para Ative Directory ou Azure AD.

Por padrão, os conectores de provisionamento do Azure AD não têm nenhum filtro de escopo baseado em atributo configurado. 

## <a name="scoping-filter-construction"></a>Construção de filtro de escopo

Um filtro de deteção consiste numa ou mais *cláusulas*. As cláusulas determinam quais usuários têm permissão para passar pelo filtro de escopo, avaliando os atributos de cada usuário. Por exemplo, você pode ter uma cláusula que exija que o atributo "estado" de um usuário seja igual a "Nova York", de modo que somente os usuários de Nova York sejam provisionados no aplicativo. 

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
Os filtros de escopo são configurados como parte dos mapeamentos de atributo para cada conector de provisionamento de usuário do Azure AD. O procedimento seguinte pressupõe que já criou o fornecimento automático para [uma das aplicações suportadas](../saas-apps/tutorial-list.md) e está a adicionar-lhe um filtro de deteção.

### <a name="create-a-scoping-filter"></a>Criar um filtro de escopo
1. No [portal Azure,](https://portal.azure.com)vá ao **Diretório Ativo do Azure** > **Aplicações empresariais** > **todas as aplicações.**

2. Selecione o aplicativo para o qual você configurou o provisionamento automático: por exemplo, "ServiceNow".

3. Selecione o separador **Provisioning.**

4. Na secção **Mapeamento,** selecione o mapeamento para o que pretende configurar um filtro de deteção para: por exemplo, "Synchronize Azure Ative Directory Users to ServiceNow".

5. Selecione o menu de âmbito de **objeto fonte.**

6. **Selecione Adicionar filtro de deteção**.

7. Defina uma cláusula selecionando um nome de **atributo**de origem, um **Operador,** e um **Valor de Atributo** para combinar com. Há suporte para os seguintes operadores:

   a. **EQUALS**. A cláusula retornará "true" se o atributo avaliado corresponder ao valor da cadeia de caracteres de entrada exatamente (maiúsculas e minúsculas).

   b. **NÃO é igual.** A cláusula retornará "true" se o atributo avaliado não corresponder ao valor da cadeia de caracteres de entrada (diferencia maiúsculas de minúsculas).

   c. **É VERDADE.** A cláusula retornará "true" se o atributo avaliado contiver um valor booliano de true.

   d. **É FALSO.** A cláusula retornará "true" se o atributo avaliado contiver um valor booliano de false.

   e. **É NULO.** A cláusula retornará "true" se o atributo avaliado estiver vazio.

   f. **NÃO É NULO.** A cláusula retornará "true" se o atributo avaliado não estiver vazio.

   g. **Regex MATCH**. A cláusula retornará "true" se o atributo avaliado corresponder a um padrão de expressão regular. Por exemplo: ([1-9][0-9]) corresponde a qualquer número entre 10 e 99.

   h. **NÃO REGEX MATCH**. A cláusula retornará "true" se o atributo avaliado não corresponder a um padrão de expressão regular.
   
   i. **Greater_Than.** A cláusula devolve "verdadeiro" se o atributo avaliado for superior ao valor. O valor especificado no filtro de deteção deve ser um inteiro e o atributo no utilizador deve ser um inteiro [0,1,2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** A cláusula devolve "verdadeiro" se o atributo avaliado for maior ou igual ao valor. O valor especificado no filtro de deteção deve ser um inteiro e o atributo no utilizador deve ser um inteiro [0,1,2,...]. 


>[!IMPORTANT] 
> Os filtros includes e IsMemberOf não têm suporte. Em breve, eles serão removidos da interface do usuário.

9. Opcionalmente, repita as etapas 7-8 para adicionar mais cláusulas de escopo.

10. No Título do **Filtro de Deteção,** adicione um nome para o filtro de deteção.

11. Selecione **OK**.

12. Selecione **OK** novamente no ecrã **filtros de deteção.** Opcionalmente, repita as etapas 6-11 para adicionar outro filtro de escopo.

13. Selecione **Guardar** no ecrã de Mapeamento do **Atributo.** 

>[!IMPORTANT] 
> Salvar um novo filtro de escopo dispara uma nova sincronização completa para o aplicativo, onde todos os usuários no sistema de origem são avaliados novamente em relação ao novo filtro de escopo. Se um usuário no aplicativo estava anteriormente no escopo para provisionamento, mas estiver fora do escopo, sua conta será desabilitada ou desprovisionada no aplicativo. Para anular este comportamento predefinido, consulte a [eliminação de Skip para contas de utilizador que ficam fora de alcance](../app-provisioning/skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Filtros de escopo comuns
| Atributo de destino| Operador | Valor | Descrição|
|----|----|----|----|
|userPrincipalName|CORRESPONDÊNCIA DE REGEX|.\*@domain.com |Todos os utilizadores com o utilizadorPrincipal que tenha o domínio @domain.com estarão no âmbito do provisionamento|
|userPrincipalName|NÃO CORRESPONDÊNCIA DE REGEX|.\*@domain.com|Todos os utilizadores com o utilizadorPrincipal que tenha o domínio @domain.com estarão fora do âmbito de provisionamento|
|Departamento|SEJA|vendas|Todos os usuários do departamento de vendas estão no escopo para provisionamento|
|workerid|CORRESPONDÊNCIA DE REGEX|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Todos os funcionários com workerIDs entre 1 milhão e 2 milhões estão no escopo para provisionamento.|

## <a name="related-articles"></a>Artigos relacionados
* [Automatizar o fornecimento e o desprovisionamento de utilizadores às aplicações SaaS](../app-provisioning/user-provisioning.md)
* [Personalize mapeamentos de atributos para o fornecimento de utilizadores](../app-provisioning/customize-application-attributes.md)
* [Escrever expressões para mapeamentos de atributos](functions-for-customizing-application-data.md)
* [Notificações de provisionamento de contas](../app-provisioning/user-provisioning.md)
* [Utilizar o SCIM para permitir o fornecimento automático de utilizadores e grupos do Diretório Ativo azure às aplicações](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Lista de tutoriais sobre como integrar aplicações do SaaS](../saas-apps/tutorial-list.md)

