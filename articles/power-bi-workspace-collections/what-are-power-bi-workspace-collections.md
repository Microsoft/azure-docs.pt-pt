---
title: O que são as Coleções de Área de Trabalho do Power BI?
description: Power BI Embedded permite-lhe integrar relatórios do Power BI nas suas aplicações móveis ou web, pelo que não precisa para criar soluções personalizadas.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 6650eed6bf2559e9fb76b573189179a7f5df81ca
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896709"
---
# <a name="what-are-power-bi-workspace-collections"></a>O que são as Coleções de Área de Trabalho do Power BI?

Com o **coleções de área de trabalho do Power BI**, pode integrar relatórios do Power BI diretamente nas suas aplicações móveis ou web.

![Diagrama de aplicação](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> As Coleções de Áreas de Trabalho do Power BI foram preteridas e estão disponíveis até junho de 2018 ou até quando indicar o contrato. Recomendamos que planeie a migração para o Power BI Embedded para evitar interrupções na sua aplicação. Para obter informações sobre como migrar os dados para o Power BI Embedded, veja [How to migrate Power BI Workspace Collections content to Power BI Embedded (Como migrar o conteúdo das Coleções de Áreas de Trabalho do Power BI para o Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

As coleções de área de trabalho do Power BI são uma **serviço do Azure** que permite que os ISVs e programadores de aplicações para experiências de dados do Power BI superfície nas respetivas aplicações. Como desenvolvedor, criou aplicativos e esses aplicativos têm seus próprios usuários e um conjunto distinto de recursos. Esses aplicativos também podem acontecer ter alguns elementos de dados incorporadas, como gráficos e relatórios que agora podem ser alimentados por coleções de área de trabalho do Microsoft Power BI. Não precisa de uma conta do Power BI para utilizar a sua aplicação. Pode continuar a iniciar sessão na sua aplicação, assim como antes e ver e interagir com a experiência de geração de relatórios sem a necessidade de quaisquer licenças adicionais do Power BI.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Licenciamento do Microsoft Power BI área de trabalho coleções

Na **coleções de área de trabalho do Microsoft Power BI** através do modelo de utilização, o licenciamento para o Power BI não é da responsabilidade do utilizador final.  Em vez disso, **sessões** são adquiridas pelo programador da aplicação que está a consumir os elementos visuais e são cobradas à subscrição que é proprietário desses recursos. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Modelo conceitual de coleções de área de trabalho do Microsoft Power BI

![Fluxo da aplicação com coleções de área de trabalho](media/what-are-power-bi-workspace-collections/model.png)

Como qualquer outro serviço no Azure, os recursos para coleções de área de trabalho do Power BI são aprovisionados através do [APIs do Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). Neste caso, é o recurso que está a aprovisionar um **coleção de área de trabalho do Power BI**.

## <a name="workspace-collection"></a>Coleção de área de trabalho

R **coleção de área de trabalho** é o nível superior contentor do Azure para os recursos que contém 0 ou mais **áreas de trabalho**.  R **área de trabalho** **coleção** tem todas as propriedades do Azure standard, bem como o seguinte:

* **Chaves de acesso** – as chaves usadas ao chamar com segurança as APIs do Power BI (descrito na secção posterior).
* **Os utilizadores** – os utilizadores do Azure Active Directory (AAD) que têm direitos de administrador para gerir a coleção de área de trabalho do Power BI através do portal do Azure ou a API do Azure Resource Manager.
* **Região** – como parte de aprovisionamento de um **coleção de área de trabalho**, pode selecionar uma região a ser aprovisionado no. Para obter mais informações, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Área de trabalho

R **área de trabalho** é um contentor do Power BI conteúdo, que pode incluir os conjuntos de dados e relatórios. R **área de trabalho** está vazio quando criado pela primeira vez. Vai criar conteúdo com o Power BI Desktop e vai implementar o PBIX através de programação em sua área de trabalho com o [API do Power BI importar](https://msdn.microsoft.com/library/mt711504.aspx). Pode também programaticamente criar o conjunto de dados e, em seguida, criar relatórios na sua aplicação em vez de utilizar o Power BI Desktop.

## <a name="using-workspace-collections-and-workspaces"></a>Usando coleções de área de trabalho e áreas de trabalho

**Coleções de área de trabalho** e **áreas de trabalho** são contentores de conteúdo que são utilizados e organizados em qualquer forma que melhor se adequa a estrutura da aplicação estiver a criar. Haverá muitas formas diferentes, que poderia organizar o conteúdo dentro dos mesmos. Pode optar por colocar todo o conteúdo dentro de uma área de trabalho e, em seguida, utilizar tokens de aplicação mais tarde para subdividir o conteúdo entre os seus clientes. Também pode optar por colocar todos os seus clientes em áreas de trabalho separadas, para que haja alguns separação entre eles. Em alternativa, pode optar por organizar os utilizadores por região e não pelo cliente. Esse design flexível permite-lhe escolher a melhor forma de organizar o conteúdo.

## <a name="cached-datasets"></a>Conjuntos de dados em cache

Conjuntos de dados em cache podem ser utilizados.  No entanto, não é possível atualizar dados em cache depois de terem sido carregado no **coleções de área de trabalho do Microsoft Power BI**. Um conjunto de dados em cache significa que tiver importado os dados para o Power BI Desktop em vez de utilizar o DirectQuery.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autenticação e autorização com tokens de aplicação

**Coleções de área de trabalho do Microsoft Power BI** difere à sua aplicação para efetuar a autenticação de utilizador necessário e autorização. Não existe nenhum requisito explícito que os utilizadores finais ser clientes do Azure Active Directory (Azure AD).  Em vez disso, a sua aplicação expressa para **coleções de área de trabalho do Microsoft Power BI** autorização para compor um relatório do Power BI utilizando **Tokens de autenticação de aplicação (Tokens de aplicação)**.  Estes **Tokens de aplicação** são criadas conforme necessário quando seu aplicativo deseja compor um relatório.

![Diagrama de utilização de token de aplicação](media/what-are-power-bi-workspace-collections/app-tokens.png)

**Tokens de autenticação de aplicação (Tokens de aplicação)** são utilizados para autenticar face **coleções de área de trabalho do Microsoft Power BI**.  Existem três tipos de **Tokens de aplicação**:

1. Aprovisionamento Tokens - utilizados durante o aprovisionamento de um novo **área de trabalho** num **coleção de área de trabalho**
2. Tokens de desenvolvimento - utilizadas ao efetuar chamadas diretamente para o **APIs de REST do Power BI**
3. Tokens de incorporação - utilizadas ao efetuar chamadas para compor um relatório numa embedded iframe

Estes tokens são utilizados para as fases de suas interações com **coleções de área de trabalho do Microsoft Power BI**.  Os tokens destinam-se para que pode delegar as permissões da sua aplicação para o Power BI. Para obter mais informações, consulte [fluxo de Token de aplicação](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Criar ou editar relatórios na sua aplicação

Agora pode editar os relatórios existentes ou criar novos relatórios diretamente em seu aplicativo sem ter de utilizar o Power BI Desktop. Isto requer que um conjunto de dados existem na sua área de trabalho.

## <a name="see-also"></a>Consulte também

[Cenários comuns de coleções de área de trabalho do Microsoft Power BI](scenarios.md)  
[Começar a utilizar coleções de área de trabalho do Microsoft Power BI](get-started.md)  
[Introdução com exemplo](get-started-sample.md)  
[Incorporar um relatório](embed-report.md)  
[Autenticação e autorização nas Coleções de Áreas de Trabalho do Power BI](app-token-flow.md)  
[Exemplo de Incorporação de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Repositório de Git do Power BI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositório de Git de nó do Power BI](https://github.com/Microsoft/PowerBI-Node)  

Mais perguntas? [Tente a Comunidade do Power BI](https://community.powerbi.com/)
