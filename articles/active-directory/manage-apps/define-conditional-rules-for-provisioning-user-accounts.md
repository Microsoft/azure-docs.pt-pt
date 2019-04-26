---
title: Aprovisionar aplicações com filtros de âmbito | Documentos da Microsoft
description: Saiba como utilizar filtros de âmbito para impedir que os objetos nas aplicações que suportam o aprovisionamento de utilizadores automatizado do que está a ser aprovisionado, se um objeto não satisfazer os seus requisitos empresariais.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ebc6c1e8a264c5570f3100885c4fca7d0d0d90d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442863"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Provisionamento de aplicativos de baseadas em atributos com filtros de âmbito
O objetivo deste artigo é explicar como utilizar filtros de âmbito para definir regras baseadas em atributos que determinam quais usuários são aprovisionados para um aplicativo.

## <a name="scoping-filter-use-cases"></a>Casos de utilização do filtro de âmbito

Um filtro de âmbito permite que o Azure Active Directory (Azure AD) do serviço de aprovisionamento incluir ou excluir qualquer usuário que ter um atributo que corresponde a um valor específico. Por exemplo, quando o aprovisionamento de utilizadores do Azure AD para uma aplicação SaaS utilizada por uma equipa de vendas, pode especificar que apenas os utilizadores com um atributo de "Departamento" de "Sales" devem estar no âmbito de aprovisionamento.

Filtros de âmbito podem ser usado forma diferente consoante o tipo de conector de aprovisionamento:

* **Saída de aprovisionamento do Azure AD para aplicações SaaS**. Quando o Azure AD é o sistema de origem [atribuições de utilizador e grupo](assign-user-or-group-access-portal.md) são o método mais comum para determinar quais os utilizadores que estão no âmbito de aprovisionamento. Estas atribuições também são utilizadas para ativar o início de sessão único e fornecem um único método para gerir o acesso e aprovisionamento. Filtros de âmbito podem ser utilizado, opcionalmente, além de atribuições ou em vez de eles, para filtrar os utilizadores com base nos valores de atributo.

    >[!TIP]
    > Pode desativar o aprovisionamento com base em atribuições para uma aplicação empresarial, alterando as definições no [âmbito](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) menu em que as definições de aprovisionamento **sincronizar todos os utilizadores e grupos**. Usar esta opção e os filtros de âmbito baseadas em atributos oferece um desempenho mais rápido que a utilização de atribuições baseadas em grupo.  

* **Aprovisionamento de aplicações de HCM para o Azure AD de entrada e o Active Directory**. Quando um [aplicação de HCM, tais como o Workday](../saas-apps/workday-tutorial.md) é o sistema de origem, filtros de âmbito são o método primário para determinar quais os utilizadores que devem ser fornecidos no aplicativo de HCM para Active Directory ou do Azure AD.

Por predefinição, conectores de aprovisionamento do Azure AD não tem quaisquer baseadas em atributos filtros de âmbito configurados. 

## <a name="scoping-filter-construction"></a>Construção de filtro de âmbito

Um filtro de âmbito é composta por um ou mais *cláusulas*. Cláusulas determinam quais usuários têm permissão para passar o filtro de âmbito através da avaliação de atributos de cada utilizador. Por exemplo, pode ter uma cláusula que requer que o atributo de "Estado" de um utilizador é igual a "New York", para que apenas os utilizadores de Nova Iorque são aprovisionados no aplicativo. 

Uma cláusula única define uma condição única para um valor de atributo único. Se várias cláusulas são criadas num único filtro de âmbito, eles são avaliados em conjunto usando a lógica "AND". Isso significa que todas as cláusulas devem ser avaliadas como "true" na ordem de um utilizador a ser aprovisionado.

Por fim, vários filtros de âmbito podem ser criados para um único aplicativo. Se existirem vários filtros de âmbito, eles são avaliados em conjunto, utilizando a lógica "OR". Isso significa que, se avaliam todas as cláusulas em qualquer um dos filtros de âmbito configurados como "true", o utilizador está aprovisionado.

Cada utilizador ou grupo processados pelo serviço de aprovisionamento do Azure AD é sempre avaliado individualmente em cada filtro de âmbito.

Por exemplo, considere o seguinte filtro de âmbito:

![Filtro de âmbito](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

De acordo com este filtro de âmbito, os utilizadores tem de cumprir para ser aprovisionado, os seguintes critérios:

* Tem de ser em nova York.
* Tem de trabalhar no departamento de engenharia.
* O ID do funcionário da empresa tem de ser entre 1.000.000 e 2.000.000.
* Seu cargo não tem de ser nulo nem estar vazio.

## <a name="create-scoping-filters"></a>Criar filtros de âmbito
Filtros de âmbito são configurados como parte dos mapeamentos de atributos para cada conector de provisionamento de usuários do Azure AD. O procedimento seguinte assume que já configurou o aprovisionamento automático para [uma das aplicações suportadas](../saas-apps/tutorial-list.md) e estiver a adicionar um filtro de âmbito ao mesmo.

### <a name="create-a-scoping-filter"></a>Criar um filtro de âmbito
1. Na [portal do Azure](https://portal.azure.com), aceda ao **Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** secção.

2. Selecione a aplicação para o qual que configurou o aprovisionamento automático: por exemplo, "ServiceNow".

3. Selecione o **aprovisionamento** separador.

4. Na **mapeamentos** secção, selecione o mapeamento que deseja configurar um filtro de âmbito para: por exemplo, "Sincronizar do Azure Active Directory utilizadores para ServiceNow".

5. Selecione o **âmbito de objeto de origem** menu.

6. Selecione **Adicionar filtro de âmbito**.

7. Definir uma cláusula ao selecionar uma origem **nome do atributo**, um **operador**e um **valor do atributo** para correspondência. São suportados os seguintes operadores:

   a. **EQUALS**. Cláusula devolvido "true" se o atributo avaliado o valor de cadeia de entrada de corresponder exatamente (sensível a maiúsculas e minúsculas).

   b. **NOT EQUALS**. Cláusula devolve "verdadeira" se o atributo avaliado não corresponder o valor de cadeia de entrada (sensível a maiúsculas e minúsculas).

   c. **É VERDADE**. Cláusula devolve "verdadeira" se o atributo avaliado contém um valor booleano de true.

   d. **É FALSO**. Cláusula devolve "verdadeira" se o atributo avaliado contém um valor booleano FALSE.

   e. **IS NULL**. Cláusula devolve "verdadeira" se o atributo avaliado está vazio.

   f. **IS NOT NULL**. Cláusula devolve "verdadeira" se o atributo avaliado não está vazio.

   g. **CORRESPONDÊNCIA DE REGEX**. Cláusula devolve "verdadeira" se o atributo avaliado corresponde a um padrão de expressão regular. Por exemplo: ([1-9][0-9]) corresponde a qualquer número entre 10 e 99.

   h. **NÃO CORRESPONDÊNCIA DE REGEX**. Cláusula devolve "verdadeira" se o atributo avaliado não corresponde a um padrão de expressão regular.

8. Selecione **Adicionar nova cláusula de âmbito**.

9. Opcionalmente, repita os passos 7 e 8 para adicionar mais cláusulas de âmbito.

10. Na **título do filtro de âmbito**, adicionar um nome para o seu filtro de âmbito.

11. Selecione **OK**.

12. Selecione **OK** novamente na **filtros de âmbito** ecrã. Opcionalmente, repita os passos 6-11 para adicionar outro filtro de âmbito.

13. Selecione **salvar** sobre o **mapeamento do atributo** ecrã. 

>[!IMPORTANT] 
> A guardar um novo âmbito filtro aciona uma sincronização completa nova da aplicação, onde todos os utilizadores no sistema de origem são avaliados novamente o novo filtro de âmbito. Se um usuário no aplicativo era anteriormente no âmbito de aprovisionamento, mas sai do escopo, a respetiva conta está desativada ou desaprovisionada no aplicativo.


## <a name="related-articles"></a>Artigos relacionados
* [Automatizar o aprovisionamento e desaprovisionamento para aplicações SaaS de utilizador](user-provisioning.md)
* [Personalizar mapeamentos de atributos para o aprovisionamento do utilizador](customize-application-attributes.md)
* [Escrever expressões para mapeamentos de atributos](functions-for-customizing-application-data.md)
* [Notificações do aprovisionamento de contas](user-provisioning.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](use-scim-to-provision-users-and-groups.md)
* [Lista de tutoriais sobre como integrar aplicações SaaS](../saas-apps/tutorial-list.md)

