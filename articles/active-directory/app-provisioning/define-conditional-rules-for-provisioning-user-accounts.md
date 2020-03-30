---
title: Aplicativos de provisão com filtros de deteção / Microsoft Docs
description: Aprenda a usar filtros de deteção para evitar que objetos em aplicações que suportem o fornecimento automatizado de utilizadores sejam provisionados se um objeto não satisfazer os seus requisitos de negócio.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c667409f2abb9f1cf89ae3b34f08e0f9eec067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138540"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Fornecimento de aplicações baseado em atributos com filtros de deteção
O objetivo deste artigo é explicar como usar filtros de deteção para definir regras baseadas em atributos que determinam quais os utilizadores que são provisionados a uma aplicação.

## <a name="scoping-filter-use-cases"></a>Casos de utilização de filtros de deteção

Um filtro de deteção permite que o serviço de provisionamento azure Ative Directory (Azure AD) inclua ou exclua quaisquer utilizadores que tenham um atributo que corresponda a um valor específico. Por exemplo, ao fornecer utilizadores da Azure AD a uma aplicação SaaS utilizada por uma equipa de vendas, pode especificar que apenas os utilizadores com um atributo "Department" de "Vendas" devem estar no âmbito do provisionamento.

Os filtros de deteção podem ser utilizados de forma diferente, dependendo do tipo de conector de provisionamento:

* **Fornecimento de saída da AD Azure para aplicações SaaS.** Quando o Azure AD é o sistema de origem, as atribuições de [utilizadores e grupos](../manage-apps/assign-user-or-group-access-portal.md) são o método mais comum para determinar quais os utilizadores que estão no âmbito do provisionamento. Estas atribuições também são utilizadas para permitir uma única inscrição e fornecer um único método para gerir o acesso e o provisionamento. Os filtros de deteção podem ser utilizados opcionalmente, para além de atribuições ou em vez deles, para filtrar os utilizadores com base nos valores dos atributos.

    >[!TIP]
    > Pode desativar o fornecimento com base em atribuições para uma aplicação empresarial alterando as definições no menu [Scope](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) sob as definições de provisionamento para **Sincronizar todos os utilizadores e grupos**. A utilização desta opção mais filtros de deteção baseados em atributos oferece um desempenho mais rápido do que usar atribuições baseadas em grupo.  

* **Fornecimento de entrada a partir de aplicações HCM para Azure AD e Ative Diretório**. Quando uma [aplicação HCM como](../saas-apps/workday-tutorial.md) o Workday é o sistema de origem, os filtros de deteção são o principal método para determinar quais os utilizadores que devem ser provisionados a partir da aplicação HCM para Ative Directory ou Azure AD.

Por predefinição, os conectores de fornecimento de AD Azure não têm quaisquer filtros de deteção baseados em atributos configurados. 

## <a name="scoping-filter-construction"></a>Construção de filtros de deteção

Um filtro de deteção consiste numa ou mais *cláusulas*. As cláusulas determinam quais os utilizadores autorizados a passar pelo filtro de digitalização avaliando os atributos de cada utilizador. Por exemplo, pode ter uma cláusula que requer que o atributo "State" de um utilizador seja igual a "Nova Iorque", pelo que apenas os utilizadores nova-iorquinos são aprovisionados na aplicação. 

Uma única cláusula define uma única condição para um único valor de atributo. Se várias cláusulas forem criadas num único filtro de deteção, são avaliadas em conjunto utilizando a lógica "AND". Isto significa que todas as cláusulas devem avaliar como "verdadeiras" para que um utilizador seja provisionado.

Finalmente, vários filtros de deteção podem ser criados para uma única aplicação. Se vários filtros de deteção estiverem presentes, são avaliados em conjunto utilizando a lógica "OR". Isto significa que se todas as cláusulas de qualquer um dos filtros de digitalização configurados avaliarem "verdadeiramente", o utilizador é provisionado.

Cada utilizador ou grupo processado pelo serviço de provisionamento Azure AD é sempre avaliado individualmente contra cada filtro de deteção.

Como exemplo, considere o seguinte filtro de deteção:

![Filtro de deteção](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

De acordo com este filtro de deteção, os utilizadores devem satisfazer os seguintes critérios a fornecer:

* Devem estar em Nova Iorque.
* Devem trabalhar no departamento de Engenharia.
* A identificação dos empregados da empresa deve ser entre 1.000.000 e 2.000.000.
* O seu cargo não deve ser nulo ou vazio.

## <a name="create-scoping-filters"></a>Criar filtros de deteção
Os filtros de deteção são configurados como parte dos mapeamentos de atributos para cada conector de fornecimento de utilizadores da AD Azure. O procedimento seguinte pressupõe que já criou o fornecimento automático para [uma das aplicações suportadas](../saas-apps/tutorial-list.md) e está a adicionar-lhe um filtro de deteção.

### <a name="create-a-scoping-filter"></a>Criar um filtro de deteção
1. No [portal Azure,](https://portal.azure.com)vá à secção de**Aplicações empresariais** > de **diretório** > ativo Azure**Todas as aplicações.**

2. Selecione a aplicação para a qual configurao o fornecimento automático: por exemplo, "ServiceNow".

3. Selecione o separador **Provisioning.**

4. Na secção **Mapeamento,** selecione o mapeamento para o que pretende configurar um filtro de deteção para: por exemplo, "Synchronize Azure Ative Directory Users to ServiceNow".

5. Selecione o menu de âmbito de **objeto fonte.**

6. **Selecione Adicionar filtro de deteção**.

7. Defina uma cláusula selecionando um nome de **atributo**de origem, um **Operador,** e um **Valor de Atributo** para combinar com. São apoiados os seguintes operadores:

   a. **EQUALS**. A cláusula devolve "verdadeiro" se o atributo avaliado corresponder ao valor de cadeia de entrada exatamente (sensível ao caso).

   b. **NÃO é igual.** A cláusula devolve "verdadeiro" se o atributo avaliado não corresponder ao valor da cadeia de entrada (sensível ao caso).

   c. **É VERDADE.** A cláusula retorna "verdadeira" se o atributo avaliado contiver um valor booleano de verdade.

   d. **É FALSO.** A cláusula retorna "verdadeira" se o atributo avaliado contiver um valor booleano de falso.

   e. **É NULO.** A cláusula devolve "verdadeiro" se o atributo avaliado estiver vazio.

   f. **NÃO É NULO.** A cláusula devolve "verdadeiro" se o atributo avaliado não estiver vazio.

   g. **Regex MATCH**. A cláusula retorna "verdadeira" se o atributo avaliado corresponder a um padrão de expressão regular. Por exemplo: ([1-9][0-9]) corresponde a qualquer número entre 10 e 99.

   h. **NÃO REGEX MATCH**. A cláusula retorna "verdadeira" se o atributo avaliado não corresponder a um padrão de expressão regular.
   
   i. **Greater_Than.** A cláusula devolve "verdadeiro" se o atributo avaliado for superior ao valor. O valor especificado no filtro de deteção deve ser um inteiro e o atributo no utilizador deve ser um inteiro [0,1,2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** A cláusula devolve "verdadeiro" se o atributo avaliado for maior ou igual ao valor. O valor especificado no filtro de deteção deve ser um inteiro e o atributo no utilizador deve ser um inteiro [0,1,2,...]. 
   
   k. **Inclui.** A cláusula devolve "verdadeiro" se o atributo avaliado contiver o valor de cadeia (sensível ao caso) descrito [aqui](https://docs.microsoft.com/dotnet/api/system.string.contains?view=netframework-4.8). 


>[!IMPORTANT] 
> - O filtro IsMemberOf não é suportado atualmente.
> - EQUALS e NOT EQUALS não são suportados para atributos de vários valores

9. Opcionalmente, repita os passos 7-8 para adicionar mais cláusulas de deteção.

10. No Título do **Filtro de Deteção,** adicione um nome para o filtro de deteção.

11. Selecione **OK**.

12. Selecione **OK** novamente no ecrã **filtros de deteção.** Opcionalmente, repita os passos 6-11 para adicionar outro filtro de deteção.

13. Selecione **Guardar** no ecrã de Mapeamento do **Atributo.** 

>[!IMPORTANT] 
> A poupança de um novo filtro de deteção desencadeia uma nova sincronização completa para a aplicação, onde todos os utilizadores do sistema de origem são novamente avaliados contra o novo filtro de deteção. Se um utilizador da aplicação estivesse previamente no âmbito do provisionamento, mas não for abrangido, a sua conta é desativada ou desprovisionada no pedido. Para anular este comportamento predefinido, consulte a [eliminação de Skip para contas de utilizador que ficam fora de alcance](../app-provisioning/skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Filtros comuns de deteção
| Atributo-alvo| Operador | Valor | Descrição|
|----|----|----|----|
|userPrincipalName|REGEX MATCH|.\*@domain.com |Todos os utilizadores com userPrincipal que tenha o domínio @domain.com estarão no âmbito do fornecimento|
|userPrincipalName|NÃO JOGO REGEX|.\*@domain.com|Todos os utilizadores com userPrincipal que tenha o domínio @domain.com estarão fora do âmbito de provisionamento|
|departamento|EQUALS|vendas|Todos os utilizadores do departamento de vendas estão no âmbito do provisionamento|
|trabalhadorID|REGEX MATCH|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Todos os trabalhadores com IDs de trabalhadores entre 10000000 e 2000000 estão no âmbito do provisionamento.|

## <a name="related-articles"></a>Artigos relacionados
* [Automatizar o fornecimento e o desprovisionamento de utilizadores às aplicações SaaS](../app-provisioning/user-provisioning.md)
* [Personalize mapeamentos de atributos para o fornecimento de utilizadores](../app-provisioning/customize-application-attributes.md)
* [Escrever expressões para mapeamentos de atributos](functions-for-customizing-application-data.md)
* [Notificações de provisionamento de contas](../app-provisioning/user-provisioning.md)
* [Utilizar o SCIM para permitir o fornecimento automático de utilizadores e grupos do Diretório Ativo azure às aplicações](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Lista de tutoriais sobre como integrar aplicações do SaaS](../saas-apps/tutorial-list.md)

