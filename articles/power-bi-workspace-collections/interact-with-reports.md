---
title: Interagir com relatórios através da API de JavaScript | Microsoft Docs
description: A API de JavaScript do Power BI permite-lhe incorporar facilmente os relatórios do Power BI nas suas aplicações.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: bdd885d3-1b00-4dcf-bdff-531eb1f97bfb
ms.service: power-bi-embedded
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: d5a411e227704eb80b0020f68fad072491576f18
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57883802"
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interagir com relatórios do Power BI através da API de JavaScript

A API de JavaScript do Power BI permite-lhe incorporar facilmente os relatórios do Power BI nas suas aplicações. Com a API, as suas aplicações podem interagir, programaticamente, com diferentes elementos dos relatórios, como páginas e filtros. Esta interatividade faz com que os relatórios do Power BI sejam uma parte mais integrante das aplicações.

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Para incorporar um relatório do Power BI na sua aplicação, é utilizada uma iframe que é alojada como parte da aplicação. A iframe funciona como limite entre a aplicação e o relatório, como pode ver na imagem seguinte:

![iframe da Coleção de Áreas de Trabalho do Power BI sem API de Javascript](media/interact-with-reports/iframe-without-javacript.png)

A iframe facilita bastante o processo de incorporação, mas a aplicação e o relatório não podem interagir um com o outro sem a API do JavaScript. Esta falta de interação pode fazer parecer que o relatório não faz, realmente, parte da aplicação. O relatório e a aplicação têm, efetivamente, de conseguir comunicar mutuamente, como ilustrado na imagem seguinte:

![iframe da Coleção de Áreas de Trabalho do Power BI com API de Javascript](media/interact-with-reports/iframe-with-javascript.png)

A API de JavaScript do Power BI permite-lhe escrever código que pode ser transmitido em segurança através do limite da iframe. Desta forma, a aplicação pode realizar, programaticamente, uma ação num relatório e pode escutar eventos de ações que os utilizadores fazem no relatório.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>O que pode fazer com a API de JavaScript do Power BI?

Com a API de JavaScript, pode gerir relatórios, navegar para páginas num relatório, filtrar um relatório e processar a incorporação de eventos. O diagrama seguinte mostra a estrutura da API.

![Diagrama da API de JavaScript do Power BI](media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>Gerir relatórios
A API de JavaScript permite-lhe gerir o comportamento ao nível do relatório e página:

* Incorporar um Relatório do Power BI específico em segurança na aplicação - experimente a [aplicação de demonstração incorporada](https://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * Definir o token de acesso
* Configurar o relatório
  * Ativar e desativar o painel do filtro e o painel de navegação da página - experimente a [aplicação de demonstração de atualização de definições](https://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * Definir predefinições para páginas e filtros - experimente a [demonstração de definição de predefinições](https://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* Entrar e sair do modo de ecrã inteiro

[Saiba mais sobre a incorporação de relatórios](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>Navegar para páginas num relatório
A API de JavaScript permite-lhe descobrir todas as páginas de um relatório e definir a página atual. Experimente a [aplicação de demonstração de navegação](https://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Saiba mais sobre a navegação nas páginas](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrar um relatório
A API de JavaScript proporciona capacidades básicas e avançadas de filtragem para relatórios incorporados e para páginas de relatórios. Experimente a [aplicação de demonstração de filtragem](https://azure-samples.github.io/powerbi-angular-client/#/scenario4) e veja alguns códigos introdutórios aqui.

#### <a name="basic-filters"></a>Filtros básicos
É colocado um filtro básico numa coluna ou ao nível da hierarquia, o qual contém uma lista de valores a incluir ou excluir.

```typescript
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "https://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>Filtros avançados
Os filtros avançados utilizam o operador lógico AND ou OR e aceitam uma ou duas condições, cada qual com o seu próprio operador e valor. As condições suportadas são:

* Nenhuma
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contains
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```typescript
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "https://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[Saiba mais sobre a filtragem](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>Processar eventos

Para além de enviar informações para a iframe, a sua aplicação também pode obter informações sobre os eventos seguintes provenientes da iframe:

* Incorporar
  * carregado
  * erro
* Relatórios
  * pageChanged
  * dataSelected (brevemente)

[Saiba mais sobre o processamento de eventos](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a API de JavaScript do Power BI, veja as ligações seguintes:

* [Wiki da API de JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [Referência de modelo de objeto](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* [Demonstração em direto](https://microsoft.github.io/PowerBI-JavaScript/demo/)