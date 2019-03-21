---
title: Consumir o serviço web no Excel
titleSuffix: Azure Machine Learning Studio
description: O Azure Machine Learning Studio facilita chamar serviços web diretamente a partir do Excel sem a necessidade de escrever qualquer código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: ef1d8f1a72c5936ff661636c4c51acf439a0a5ea
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855202"
---
# <a name="consuming-an-azure-machine-learning-studio-web-service-from-excel"></a>Consumir um serviço de Web do Azure Machine Learning Studio a partir do Excel

 O Azure Machine Learning Studio facilita chamar serviços web diretamente a partir do Excel sem a necessidade de escrever qualquer código.

Se estiver a utilizar o Excel 2013 (ou posterior) ou o Excel Online, recomendamos que utilize o Excel [suplemento do Excel](excel-add-in-for-web-services.md).



## <a name="steps"></a>Passos
Publica um serviço web. [Tutorial 3: Implementar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md) explica como fazer isso. Atualmente, a funcionalidade de livro do Excel só é suportada para os serviços de solicitação/resposta com uma única saída (ou seja, uma etiqueta classificação única). 

Depois de ter um serviço da web, clique nas **serviços da WEB** secção à esquerda do studio e, em seguida, selecione o serviço web para consumir a partir do Excel.

**Serviço Web clássico**

1. Sobre o **DASHBOARD** separador para o serviço web é uma linha para o **SOLICITAÇÃO/resposta** serviço. Se este serviço teve uma única saída, deverá ver o **transferir o livro do Excel** ligação nessa linha.

    ![Transferir o livro do Excel com o portal de serviço Web do Studio](./media/consuming-from-excel/excellink.png)
2. Clique em **transferir o livro do Excel**.

**Novo serviço Web**

1. No portal do serviço Web do Azure Machine Learning, selecione **Consume**.
2. Na página Consume, no **opções de consumo do serviço Web** secção, clique no ícone do Excel.

**Utilizar o livro**

1. Abra o livro.
2. É apresentado um aviso de segurança; Clique nas **Enable Editing** botão.

    ![Ativar a edição remover o aviso de segurança de vista protegida](./media/consuming-from-excel/enableeditting.png)
3. É apresentado um aviso de segurança. Clique nas **ativar conteúdo** botão para executar macros na folha de cálculo.

    ![Permitir que os conteúdos ignorar o aviso de segurança desativando macros](./media/consuming-from-excel/enablecontent.png)
4. Assim que as macros estejam habilitadas, é gerada uma tabela. Colunas azul são necessários como entrada para o serviço web RRS, ou **parâmetros**. Tenha em atenção a saída do serviço RRS, **valores previstos** em verde. Quando todas as colunas para uma determinada linha estiverem preenchidas, a pasta de trabalho automaticamente chama a API de classificação e exibe os resultados com a pontuação.

    ![Valores de previsão de tabela para entradas de parâmetro e resultante](./media/consuming-from-excel/sampletable.png)
5. Para mais de uma linha de pontuação, a segunda linha com dados e os valores previstos de preenchimento são produzidos. Pode até mesmo colar várias linhas ao mesmo tempo.

Pode utilizar qualquer uma das funcionalidades do Excel (gráficos, mapa de energia, formatação condicional, etc.) com os valores previstos para o ajudar a visualizar os dados.

## <a name="sharing-your-workbook"></a>O livro de partilha
As macros trabalhar, a chave de API deve ser parte da folha de cálculo. Isso significa que deve compartilhá-la apenas com entidades/indivíduos que confia.

## <a name="automatic-updates"></a>Atualizações automáticas
É feita uma chamada RRS nessas duas situações:

1. Na primeira vez numa linha tem conteúdo em todos os seus **parâmetros**
2. Sempre que qualquer um da **parâmetros** alterações numa linha que tinha todos seus **parâmetros** introduzido.