---
title: Consumir serviço Web no Excel
titleSuffix: Azure Machine Learning Studio (classic)
description: Azure Machine Learning Studio (clássico) facilita a chamada de serviços da Web diretamente do Excel sem a necessidade de escrever nenhum código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: afa6430a76650b08f979687e2a1e5c7b53c89180
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493257"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Consumindo um serviço Web Azure Machine Learning Studio (clássico) do Excel

 Azure Machine Learning Studio (clássico) facilita a chamada de serviços da Web diretamente do Excel sem a necessidade de escrever nenhum código.

Se você estiver usando o Excel 2013 (ou posterior) ou o Excel online, recomendamos o uso do [suplemento Excel Excel](excel-add-in-for-web-services.md).



## <a name="steps"></a>Passos
Publicar um serviço Web. [Tutorial 3: implantar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md) explica como fazer isso. Atualmente, o recurso de pasta de trabalho do Excel só tem suporte para serviços de solicitação/resposta que têm uma única saída (ou seja, um único rótulo de pontuação). 

Quando você tiver um serviço Web, clique na seção **Serviços Web** à esquerda do estúdio e selecione o serviço Web a ser consumido do Excel.

**Serviço Web clássico**

1. Na guia **painel** do serviço Web está uma linha para o serviço de **solicitação/resposta** . Se esse serviço tiver uma única saída, você deverá ver o link **baixar pasta de trabalho do Excel** nessa linha.

    ![Baixar a pasta de trabalho do Excel usando o portal do serviço Web Studio (clássico)](./media/consuming-from-excel/excellink.png)
2. Clique em **baixar pasta de trabalho do Excel**.

**Novo serviço Web**

1. No Azure Machine Learning Portal de serviço Web, selecione **consumir**.
2. Na página consumir, na seção **Opções de consumo do serviço Web** , clique no ícone do Excel.

**Usando a pasta de trabalho**

1. Abra a pasta de trabalho.
2. Um aviso de segurança é exibido; Clique no botão **Habilitar edição** .

    ![Habilitar edição para remover o aviso de segurança de exibição protegida](./media/consuming-from-excel/enableeditting.png)
3. Um aviso de segurança é exibido. Clique no botão **habilitar conteúdo** para executar macros em sua planilha.

    ![Habilitar conteúdo para ignorar o aviso de segurança desabilitando macros](./media/consuming-from-excel/enablecontent.png)
4. Depois que as macros são habilitadas, uma tabela é gerada. As colunas em azul são necessárias como entrada no serviço Web RRS ou **parâmetros**. Observe a saída do serviço RRS, os **valores previstos** em verde. Quando todas as colunas de uma determinada linha são preenchidas, a pasta de trabalho chama automaticamente a API de Pontuação e exibe os resultados pontuados.

    ![Tabela para entradas de parâmetro e os valores previstos resultantes](./media/consuming-from-excel/sampletable.png)
5. Para pontuar mais de uma linha, preencha a segunda linha com os dados e os valores previstos serão produzidos. Você pode até mesmo colar várias linhas de uma só vez.

Você pode usar qualquer um dos recursos do Excel (grafos, Power MAP, formatação condicional, etc.) com os valores previstos para ajudar a visualizar os dados.

## <a name="sharing-your-workbook"></a>Compartilhando sua pasta de trabalho
Para que as macros funcionem, sua chave de API deve fazer parte da planilha. Isso significa que você deve compartilhar a pasta de trabalho somente com entidades/indivíduos confiáveis.

## <a name="automatic-updates"></a>Atualizações automáticas
Uma chamada RRS é feita nessas duas situações:

1. Na primeira vez em que uma linha tem conteúdo em todos os seus **parâmetros**
2. Sempre que qualquer um dos **parâmetros** for alterado em uma linha que tenha todos os seus **parâmetros** inseridos.