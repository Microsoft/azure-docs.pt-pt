---
title: Aplicativos de provisão com filtros de scoping / Microsoft Docs
description: Aprenda a usar filtros de deteção para evitar que objetos em aplicações que suportem o fornecimento automatizado de utilizadores sejam a provisionados se um objeto não satisfizer os requisitos do seu negócio.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: kenwith
ms.openlocfilehash: 1765c6a7d19a11730567acd5e41deebc0a20cec2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993997"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Provisionamento de aplicações baseados em atributos com filtros de escotagem
O objetivo deste artigo é explicar como usar filtros de digitalização para definir regras baseadas em atributos que determinam quais os utilizadores que estão a forcam a uma aplicação.

## <a name="scoping-filter-use-cases"></a>Escoar casos de utilização do filtro

Um filtro de deteção permite que o serviço de prestação do Azure Ative Directory (Azure AD) inclua ou exclua qualquer utilizadores que tenham um atributo que corresponda a um valor específico. Por exemplo, ao a provisionar utilizadores do Azure AD a uma aplicação SaaS utilizada por uma equipa de vendas, pode especificar que apenas os utilizadores com um atributo "Department" de "Vendas" devem estar em possibilidade de provisão.

Os filtros de escoamento podem ser utilizados de forma diferente, dependendo do tipo de conector de provisionamento:

* **Fornecimento de saída de Azure AD para aplicações SaaS**. Quando o Azure AD é o sistema de origem, as [atribuições de utilizador e grupo](../manage-apps/assign-user-or-group-access-portal.md) são o método mais comum para determinar quais os utilizadores que estão em âmbito de provisão. Estas atribuições também são utilizadas para permitir uma única inscrição e fornecem um único método para gerir o acesso e o provisionamento. Os filtros de deteção podem ser utilizados opcionalmente, para além de atribuições ou em vez deles, para filtrar os utilizadores com base nos valores de atributos.

    >[!TIP]
    > Pode desativar o provisionamento com base em atribuições para uma aplicação empresarial alterando as definições no menu [Scope](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) ao abrigo das definições de provisionamento para **Sincronizar todos os utilizadores e grupos**. 

* **Fornecimento de entrada de aplicações HCM para Azure AD e Ative Directory**. Quando uma [aplicação HCM como o Workday](../saas-apps/workday-tutorial.md) é o sistema de origem, os filtros de deteção são o método principal para determinar quais os utilizadores que devem ser a provisionados a partir da aplicação HCM para Ative Directory ou Azure AD.

Por predefinição, os conectores AD AD Azure não têm nenhum filtro de scoping baseado em atributos configurados. 

## <a name="scoping-filter-construction"></a>Construção de filtro de escotagem

Um filtro de scoping consiste numa ou mais *cláusulas.* As cláusulas determinam quais os utilizadores autorizados a passar através do filtro de deteção, avaliando os atributos de cada utilizador. Por exemplo, pode ter uma cláusula que requer que o atributo "Estado" de um utilizador seja igual a "Nova Iorque", pelo que apenas os utilizadores de Nova Iorque estão a ser adustados na aplicação. 

Uma única cláusula define uma única condição para um único valor de atributo. Se várias cláusulas forem criadas num único filtro de digitalização, são avaliadas em conjunto utilizando a lógica "E". Isto significa que todas as cláusulas devem avaliar "verdadeiro" para que um utilizador seja provisionado.

Finalmente, podem ser criados vários filtros de deteção para uma única aplicação. Se estiverem presentes vários filtros de deteção, são avaliados em conjunto utilizando a lógica "OR". Isto significa que se todas as cláusulas em qualquer um dos filtros de digitalização configurados avaliarem "verdadeiro", o utilizador é a provisionado.

Cada utilizador ou grupo processado pelo serviço de fornecimento Azure AD é sempre avaliado individualmente contra cada filtro de escoamento.

Como exemplo, considere o seguinte filtro de deteção:

![Filtro de escotagem](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

De acordo com este filtro de deteção, os utilizadores devem satisfazer os seguintes critérios a prever:

* Devem estar em Nova Iorque.
* Devem trabalhar no departamento de Engenharia.
* A identificação dos empregados da empresa deve estar entre 1.000.000 e 2.000.000.
* O seu cargo não deve ser nulo ou vazio.

## <a name="create-scoping-filters"></a>Criar filtros de escotagem
Os filtros de deteção são configurados como parte dos mapeamentos de atributos para cada conector de fornecimento de um utilizador Azure AD. O procedimento a seguir pressupõe que já criou o provisionamento automático para [uma das aplicações apoiadas](../saas-apps/tutorial-list.md) e está a adicionar-lhe um filtro de escoamento.

### <a name="create-a-scoping-filter"></a>Criar um filtro de escotagem
1. No [portal Azure,](https://portal.azure.com)aceda à secção **Azure Ative Directory**  >  **Enterprise Applications**  >  **All applications.**

2. Selecione a aplicação para a qual configurar o provisionamento automático: por exemplo, "ServiceNow".

3. Selecione o separador **Aprovisionamento**.

4. Na secção **Mappings,** selecione o mapeamento que pretende configurar um filtro de deteção para: por exemplo, "Sincronizar Utilizadores do Diretório Ativo Azure ao ServiceNow".

5. Selecione o menu **de âmbito do objeto Fonte.**

6. **Selecione Adicionar filtro de escotagem**.

7. Defina uma cláusula selecionando um **Nome de Atributo** de Origem, um **Operador** e um Valor **De Atributo** para corresponder. São apoiados os seguintes operadores:

   a. **É IGUAL.** A cláusula devolve "verdadeiro" se o atributo avaliado corresponder exatamente ao valor da cadeia de entrada (sensível a caso).

   b. **NÃO É IGUAL.** A cláusula devolve "verdadeiro" se o atributo avaliado não corresponder ao valor da cadeia de entrada (sensível à caixa).

   c. **É VERDADE.** A cláusula devolve "verdadeiro" se o atributo avaliado contiver um valor booleano de verdade.

   d. **É FALSO.** A cláusula devolve "verdadeiro" se o atributo avaliado contiver um valor Boolean de falso.

   e. **É NULO.** A cláusula devolve "verdadeiro" se o atributo avaliado estiver vazio.

   f. **NÃO É NULO.** A cláusula devolve "verdadeiro" se o atributo avaliado não estiver vazio.

   exemplo, **REGEX MATCH**. A cláusula devolve "verdadeiro" se o atributo avaliado corresponder a um padrão de expressão regular. Por exemplo: ([1-9][0-9]) corresponde a qualquer número entre 10 e 99.

   h. **NÃO REGEX MATCH**. A cláusula devolve "verdadeiro" se o atributo avaliado não corresponder a um padrão de expressão regular.
   
   i. **Greater_Than.** A cláusula devolve "verdadeiro" se o atributo avaliado for maior do que o valor. O valor especificado no filtro de escoamento deve ser um número inteiro e o atributo no utilizador deve ser um número inteiro [0,1,2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** A cláusula devolve "verdadeiro" se o atributo avaliado for maior ou igual ao valor. O valor especificado no filtro de escoamento deve ser um número inteiro e o atributo no utilizador deve ser um número inteiro [0,1,2,...]. 
   
   k. **Inclui.** A cláusula devolve "verdadeiro" se o atributo avaliado contiver o valor de cadeia (sensível a caso) como descrito [aqui](/dotnet/api/system.string.contains?view=netframework-4.8). 


>[!IMPORTANT] 
> - O filtro IsMemberOf não é suportado atualmente.
> - EQUALS e NÃO EQUALS não são suportados para atributos multi-valor

9. Opcionalmente, repita os passos 7-8 para adicionar mais cláusulas de digitalização.

10. No **Título de Filtro de Scoping,** adicione um nome para o seu filtro de escotagem.

11. Selecione **OK**.

12. Selecione **OK** novamente no ecrã **de Filtros de Deteção.** Opcionalmente, repita os passos 6-11 para adicionar outro filtro de escotagem.

13. **Selecione Guardar** no ecrã **de mapeamento de atributos.** 

>[!IMPORTANT] 
> A poupança de um novo filtro de deteção desencadeia uma nova sincronização completa para a aplicação, onde todos os utilizadores do sistema de origem são novamente avaliados contra o novo filtro de deteção. Se um utilizador na aplicação estivesse anteriormente em possibilidade de provisão, mas se desprovativo, a sua conta é desativada ou desprovisionada na aplicação. Para anular este comportamento predefinido, consulte a [eliminação de Skip para contas de utilizador que ficam fora de alcance](../app-provisioning/skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Filtros de escotagem comuns
| Atributo-alvo| Operador | Valor | Descrição|
|----|----|----|----|
|userPrincipalName|JOGO REGEX|.\*@domain.com |Todos os utilizadores com utilizadorPrincipal que tenha o domínio @domain.com estarão em campo para o provisionamento|
|userPrincipalName|NÃO REGEX MATCH|.\*@domain.com|Todos os utilizadores com utilizadorPrincipal que tenha o domínio @domain.com estarão fora de alcance para o provisionamento|
|departamento|IGUAIS|vendas|Todos os utilizadores do departamento de vendas estão em possibilidade de provisão|
|trabalhadorID|JOGO REGEX|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Todos os trabalhadores com 100000000 e 2000000 estão em possibilidade de provisão.|

## <a name="related-articles"></a>Artigos relacionados
* [Automatizar o fornecimento e desprovisionamento de utilizadores para aplicações SaaS](../app-provisioning/user-provisioning.md)
* [Personalize os mapeamentos de atributos para o fornecimento do utilizador](../app-provisioning/customize-application-attributes.md)
* [Escrever expressões para mapeamentos de atributos](functions-for-customizing-application-data.md)
* [Notificações de provisão de conta](../app-provisioning/user-provisioning.md)
* [Utilizar o SCIM para permitir o fornecimento automático de utilizadores e grupos do Azure Ative Directory às aplicações](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Lista de tutoriais sobre como integrar apps saaS](../saas-apps/tutorial-list.md)