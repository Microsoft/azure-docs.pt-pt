---
title: Complemento excel para serviços web
titleSuffix: ML Studio (classic) - Azure
description: Como utilizar os serviços Web de Aprendizagem automática Azure diretamente no Excel sem escrever qualquer código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e30103589c1baf9a165839cd041ff511a119c5ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204380"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Excel Add-in para serviços web Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
O Excel facilita a chamada de serviços web diretamente sem a necessidade de escrever qualquer código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Passos para usar um serviço web existente no Livro

1. Abra o ficheiro Excel da [amostra,](https://aka.ms/amlexcel-sample-2)que contém o add-in Excel e dados sobre passageiros no Titanic. 
 
    > [!NOTE]
    > Verá a lista dos Serviços Web relacionadas com o ficheiro e na parte inferior uma caixa de verificação para "Auto-predict". Se permitir prever automaticamente as previsões de **todos os** seus serviços serão atualizadas sempre que houver uma alteração nas inputs. Se não for verificado, terá de clicar em "Prever Tudo" para ser atualizado. Para permitir a previsão automática a nível de serviço, vá para o passo 6.

2. Escolha o serviço web clicando nele - "Titanic Survivor Predictor (Excel Add-in Sample) [Pontuação]" neste exemplo.
   
    ![Selecione serviço Web](./media/excel-add-in-for-web-services/image1.png)
3. Isto leva-te à secção **Previsão.**  Este livro já contém dados de amostra, mas para um livro em branco pode selecionar uma célula no Excel e clicar **em utilizar dados da amostra**.
4. Selecione os dados com cabeçalhos e clique no ícone da gama de dados de entrada.  Certifique-se de que a caixa "Os meus dados têm cabeçalhos" é verificada.
5. Em **'Saída',** introduza o número de telemóvel onde pretende que a saída esteja, por exemplo "H1" aqui.
6. Clique em **Prever**. Se selecionar a caixa de verificação "auto-prever" qualquer alteração nas áreas selecionadas (as especificadas como entrada) desencadeará um pedido e uma atualização das células de saída sem a necessidade de carregar no botão de previsão.
   
    ![Prever a secção](./media/excel-add-in-for-web-services/image1.png)

Implemente um serviço web ou utilize um serviço Web existente. Para obter mais informações sobre a implementação de um serviço web, consulte [Tutorial 3: Implementar modelo](tutorial-part3-credit-risk-deploy.md)de risco de crédito .

Obtenha a chave API para o seu serviço web. Onde realiza esta ação depende se publicou um serviço web Classic Machine Learning de um serviço web New Machine Learning.

**Use um serviço web clássico** 

1. No Machine Learning Studio (clássico), clique na secção **WEB SERVICES** no painel esquerdo e, em seguida, selecione o serviço web.
   
    ![Estúdio selecione um serviço Web](./media/excel-add-in-for-web-services/image4.png)
2. Copie a tecla API para o serviço web.
   
    ![Chave API do estúdio](./media/excel-add-in-for-web-services/image5.png)
3. No **separador DASHBOARD** para o serviço web, clique no link **REQUEST/RESPONSE.**
4. Procure a secção **Request URI.**  Copie e guarde o URL.

> [!NOTE]
> Agora é possível assinar no portal [Azure Machine Learning Web Services](https://services.azureml.net) para obter a chave API para um serviço web Classic Machine Learning.
> 
> 

**Use um novo serviço web**

1. No portal [Azure Machine Learning Web Services,](https://services.azureml.net) clique em **Web Services**e, em seguida, selecione o seu serviço web. 
2. Clique **em Consumir**.
3. Procure a secção de informação de **consumo básico.** Copie e guarde a **Chave Primária** e o URL de Resposta **a Pedido.**

## <a name="steps-to-add-a-new-web-service"></a>Passos para adicionar um novo serviço web

1. Implemente um serviço web ou utilize um serviço Web existente. Para obter mais informações sobre a implementação de um serviço web, consulte [Tutorial 3: Implementar modelo](tutorial-part3-credit-risk-deploy.md)de risco de crédito .
2. Clique **em Consumir**.
3. Procure a secção de informação de **consumo básico.** Copie e guarde a **Chave Primária** e o URL de Resposta **a Pedido.**
4. No Excel, vá à secção **de Serviços Web** (se estiver na secção **Previsão,** clique na seta traseira para ir à lista de serviços web).
   
    ![Ir à seleção de serviços Web](./media/excel-add-in-for-web-services/image3.png)
5. Clique em **Adicionar Serviço Web**.
6. Colar o URL no URL de caixa de texto do Excel com o rótulo de **URL**.
7. Colar a tecla API/Primary na **tecla API**da caixa de texto marcada .
8. Clique em **Adicionar**.
   
    ![URL e chave API para um serviço Web clássico.](./media/excel-add-in-for-web-services/image6.png)
9. Para utilizar o serviço web, siga as instruções anteriores: "Passos para utilizar um serviço web existente."

## <a name="sharing-your-workbook"></a>Partilhar o Seu Livro
Se guardar o seu livro de trabalho, então a chave API/Primary para os serviços web que adicionou também é guardada. Isso significa que só deves partilhar o livro com indivíduos em quem confias.

Faça quaisquer perguntas na secção de comentários seguintes ou no nosso [fórum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
