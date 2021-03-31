---
title: 'ML Studio (clássico): Consumir serviço web em Excel - Azure'
description: O Azure Machine Learning Studio (clássico) facilita a chamada de serviços web diretamente do Excel sem a necessidade de escrever qualquer código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e8e7404a2b643cf90866253e5a7453381b66f73e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517863"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Consumir um Azure Machine Learning Studio (clássico) Serviço Web do Excel

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

O Azure Machine Learning Studio (clássico) facilita a chamada de serviços web diretamente do Excel sem a necessidade de escrever qualquer código.

Se estiver a utilizar o Excel 2013 (ou mais tarde) ou o Excel Online, recomendamos que utilize o [add-in](excel-add-in-for-web-services.md)Excel Excel .



## <a name="steps"></a>Passos
Publique um serviço web. [Tutorial 3: Implementar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md) explica como fazê-lo. Atualmente, a funcionalidade de livro excel só é suportada para serviços de Pedido/Resposta que tenham uma única saída (isto é, uma única etiqueta de pontuação). 

Assim que tiver um serviço web, clique na secção **WEB SERVICES** à esquerda do estúdio e, em seguida, selecione o serviço web para consumir do Excel.

**Serviço Web Clássico**

1. No **separador DASHBOARD** para o serviço web está uma linha para o serviço **REQUEST/RESPONSE.** Se este serviço tiver uma única saída, deverá ver o link **Download Excel Workbook** nessa linha.

    ![Baixe o Excel Workbook utilizando o portal studio (clássico) do Serviço Web](./media/consuming-from-excel/excellink.png)
2. Clique no **Download Excel Workbook**.

**Novo Serviço Web**

1. No portal Azure Machine Learning Web Service, selecione **Consumir**.
2. Na página Consumir, na secção opções de consumo de **serviço Web,** clique no ícone Excel.

**Usando o livro**

1. Abra o livro.
2. Aparece um aviso de segurança; clique no botão **de edição ativa.**

    ![Habilitar a edição para remover o aviso de segurança de vista protegido](./media/consuming-from-excel/enableeditting.png)
3. Aparece um aviso de segurança. Clique no botão **"Ativar o Conteúdo"** para executar macros na sua folha de cálculo.

    ![Ativar o Conteúdo para descartar o Aviso de Segurança desativando macros](./media/consuming-from-excel/enablecontent.png)
4. Uma vez ativadas as macros, gera-se uma tabela. As colunas em azul são necessárias como entrada no serviço web RRS, ou **PARÂMETROS**. Note a saída do serviço RRS, **VALORES PREVISTOS** em verde. Quando todas as colunas para uma determinada linha são preenchidas, o livro chama automaticamente a API de pontuação e apresenta os resultados pontuados.

    ![Tabela para entradas de parâmetros e os valores previstos resultantes](./media/consuming-from-excel/sampletable.png)
5. Para marcar mais de uma linha, preencha a segunda linha com dados e os valores previstos são produzidos. Pode até colar várias linhas ao mesmo tempo.

Pode utilizar qualquer uma das funcionalidades do Excel (gráficos, mapa de potência, formatação condicional, etc.) com os valores previstos para ajudar a visualizar os dados.

## <a name="sharing-your-workbook"></a>Partilhar o seu livro
Para que as macros funcionem, a sua Chave API deve fazer parte da folha de cálculo. Isto significa que deve partilhar o livro apenas com entidades/indivíduos em quem confia.

## <a name="automatic-updates"></a>Atualizações automáticas
Uma chamada RRS é feita nestas duas situações:

1. A primeira vez que uma linha tem conteúdo em todos os seus **PARÂMETROS**
2. Sempre que algum dos **PARÂMETROS** muda em linha que tenha todos os seus **PARÂMETROS introduzidos.**