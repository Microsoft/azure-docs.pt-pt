---
title: Consumir serviço web em Excel
titleSuffix: ML Studio (classic) - Azure
description: O Azure Machine Learning Studio (clássico) facilita a chamada de serviços web diretamente do Excel sem a necessidade de escrever qualquer código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 2e95c4bfbe7342e251e6d845fd4acfed6ff6109a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150107"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Consumir um Azure Machine Learning Studio (clássico) Web Service do Excel

 O Azure Machine Learning Studio (clássico) facilita a chamada de serviços web diretamente do Excel sem a necessidade de escrever qualquer código.

Se estiver a utilizar o Excel 2013 (ou mais tarde) ou o Excel Online, recomendamos que utilize o [add-in](excel-add-in-for-web-services.md)Excel Excel .



## <a name="steps"></a>Passos
Publica um serviço web. [Tutorial 3: Implementar modelo de risco](tutorial-part3-credit-risk-deploy.md) de crédito explica como fazê-lo. Atualmente, a funcionalidade de livro do Excel só é suportada para os serviços de solicitação/resposta com uma única saída (ou seja, uma etiqueta classificação única). 

Assim que tiver um serviço web, clique na secção **WEB SERVICES** à esquerda do estúdio e, em seguida, selecione o serviço web para consumir do Excel.

**Serviço Web Clássico**

1. No **separador DASHBOARD** para o serviço web é uma linha para o serviço **REQUEST/RESPONSE.** Se este serviço tiver uma única saída, deve ver o link **Download Excel Bookbook** nessa linha.

    ![Baixe o Livro excel usando o portal de serviço web studio (clássico)](./media/consuming-from-excel/excellink.png)
2. Clique no **Livro de Transferência excel**.

**Novo Serviço Web**

1. No portal Azure Machine Learning Web Service, **selecione Consumir**.
2. Na página Consumir, na secção opções de consumo do **serviço Web,** clique no ícone Excel.

**Usando o livro**

1. Abra o livro.
2. Aparece um aviso de segurança; clique no botão **'Editar activação'.**

    ![Ativar a edição para remover o aviso de segurança de vista protegida](./media/consuming-from-excel/enableeditting.png)
3. É apresentado um aviso de segurança. Clique no botão **'Ativar conteúdo'** para executar macros na sua folha de cálculo.

    ![Ativar conteúdo para descartar o Aviso de Segurança que desativa macros](./media/consuming-from-excel/enablecontent.png)
4. Assim que as macros estejam habilitadas, é gerada uma tabela. As colunas em azul são necessárias como entrada no serviço web RRS, ou **PARAMETERS**. Note a saída do serviço RRS, **VALORES PREVISTOS** em verde. Quando todas as colunas para uma determinada linha estiverem preenchidas, a pasta de trabalho automaticamente chama a API de classificação e exibe os resultados com a pontuação.

    ![Tabela para as inputs dos parâmetros e dos valores previstos](./media/consuming-from-excel/sampletable.png)
5. Para mais de uma linha de pontuação, a segunda linha com dados e os valores previstos de preenchimento são produzidos. Pode até mesmo colar várias linhas ao mesmo tempo.

Pode utilizar qualquer uma das funcionalidades do Excel (gráficos, mapa de energia, formatação condicional, etc.) com os valores previstos para o ajudar a visualizar os dados.

## <a name="sharing-your-workbook"></a>O livro de partilha
As macros trabalhar, a chave de API deve ser parte da folha de cálculo. Isso significa que deve compartilhá-la apenas com entidades/indivíduos que confia.

## <a name="automatic-updates"></a>Atualizações automáticas
É feita uma chamada RRS nessas duas situações:

1. A primeira vez que uma linha tem conteúdo em todos os seus **PARÂMETROS**
2. Sempre que qualquer um dos **PARÂMETROS** muda de linha que tenha todos os seus **PARÂMETROS** introduzidos.