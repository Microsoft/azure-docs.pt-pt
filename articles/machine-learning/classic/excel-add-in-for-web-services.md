---
title: 'ML Studio (clássico): Excel add-in para serviços web - Azure'
description: Como utilizar os serviços web de aprendizagem automática Azure diretamente no Excel sem escrever qualquer código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 45d9e494b9f885cfa0680bec595aefcd4074d41e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520005"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Excel Add-in para Azure Machine Learning Studio (clássico) serviços web

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


O Excel facilita a chamada diretamente dos serviços web sem a necessidade de escrever qualquer código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Passos para utilizar um serviço web existente no Livro

1. Abra o ficheiro Excel da [amostra,](https://aka.ms/amlexcel-sample-2)que contém o add-in excel e dados sobre passageiros no Titanic. 
 
    > [!NOTE]
    > - Verá a lista dos Serviços Web relacionados com o ficheiro e na parte inferior uma caixa de verificação para "Auto-previsão". Se ativar automaticamente as previsões de **todos os** seus serviços serão atualizadas sempre que houver uma alteração nas entradas. Se não for verificado, terá de clicar em "Predict All" para atualização. Para permitir a previsão automática a um nível de serviço, vá para o passo 6.
    > - O add-in Azure Machine Learning Excel irá ligar para a loja de add-ins do Office para carregar. Se a sua organização proibir o acesso à loja de add-ins do Office, verá erro ao carregar o add-in. Para este caso, por favor, implemente o add-in Azure Machine Learning Excel do Microsoft 365. Em seguida, invoque o add-in e adicione o serviço web manualmente, pastando a tecla URL e API.

 

2. Escolha o serviço web clicando nele - "Titanic Survivor Predictor (Excel Add-in Sample) [Score]" neste exemplo.
   
    ![Selecione serviço Web](./media/excel-add-in-for-web-services/image1.png)
3. Isto leva-o à secção **de Previsão.**  Este livro já contém dados de amostra, mas para um livro em branco pode selecionar uma célula no Excel e clicar em **Utilizar dados de amostras.**
4. Selecione os dados com cabeçalhos e clique no ícone de gama de dados de entrada.  Certifique-se de que a caixa "Os meus dados têm cabeçalhos" está verificada.
5. Em **Saída**, introduza o número de célula onde deseja que a saída esteja, por exemplo , "H1" aqui.
6. Clique **em Prever**. Se selecionar a caixa de verificação "auto-prever" qualquer alteração nas áreas selecionadas (as especificadas como entrada) irá desencadear um pedido e uma atualização das células de saída sem a necessidade de premir o botão de previsão.
   
    ![Previsão de secção](./media/excel-add-in-for-web-services/image1.png)

Implemente um serviço web ou utilize um serviço Web existente. Para obter mais informações sobre a implementação de um serviço web, consulte [Tutorial 3: Implementar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).

Obtenha a chave API para o seu serviço web. Onde executa esta ação depende se publicou um serviço web Classic Machine Learning de um serviço web New Machine Learning.

**Use um serviço web clássico** 

1. No Machine Learning Studio (clássico), clique na secção **WEB SERVICES** no painel esquerdo e, em seguida, selecione o serviço web.
   
    ![Estúdio selecione um serviço Web](./media/excel-add-in-for-web-services/image4.png)
2. Copie a chave API para o serviço web.
   
    ![Chave API do estúdio](./media/excel-add-in-for-web-services/image5.png)
3. No **separador DASHBOARD** para o serviço web, clique no link **REQUEST/RESPONSE.**
4. Procure a secção **URI do Pedido.**  Copie e guarde a URL.

> [!NOTE]
> Agora é possível assinar no portal [Azure Machine Learning Web Services](https://services.azureml.net) para obter a chave API para um serviço web Classic Machine Learning.
> 
> 

**Use um novo serviço web**

1. No portal [Azure Machine Learning Web Services,](https://services.azureml.net) clique em **Serviços Web** e, em seguida, selecione o seu serviço web. 
2. Clique **em Consumir**.
3. Procure a secção **de informações básicas sobre consumo.** Copie e guarde a **Chave Primária** e o URL de Resposta **a Pedidos.**

## <a name="steps-to-add-a-new-web-service"></a>Passos para adicionar um novo serviço web

1. Implemente um serviço web ou utilize um serviço Web existente. Para obter mais informações sobre a implementação de um serviço web, consulte [Tutorial 3: Implementar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).
2. Clique **em Consumir**.
3. Procure a secção **de informações básicas sobre consumo.** Copie e guarde a **Chave Primária** e o URL de Resposta **a Pedidos.**
4. No Excel, aceda à secção **Serviços Web** (se estiver na secção **'Prever',** clique na seta traseira para ir à lista de serviços web).
   
    ![Ir para a seleção de serviços Web](./media/excel-add-in-for-web-services/image3.png)
5. Clique **em Adicionar Serviço Web**.
6. Cole o URL na caixa de texto do Excel com a etiqueta **URL**.
7. Cole a chave API/Primária na chave **API** com a etiqueta de texto .
8. Clique em **Adicionar**.
   
    ![Chave URL e API para um serviço Web clássico.](./media/excel-add-in-for-web-services/image6.png)
9. Para utilizar o serviço web, siga as instruções anteriores, "Passos para utilizar um serviço web existente".

## <a name="sharing-your-workbook"></a>Partilhar O Seu Livro
Se guardar o seu livro, a chave API/Primária para os serviços web que adicionou também é guardada. Isso significa que só deves partilhar o livro com indivíduos em quem confias.

Faça qualquer pergunta na secção de comentários a seguir ou no nosso [fórum.](/answers/topics/azure-machine-learning.html)