---
title: Suplemento do Excel para serviços da web
titleSuffix: Azure Machine Learning Studio
description: Como utilizar os serviços do Azure Machine Learning Web diretamente no Excel sem escrever nenhum código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 9e801e0d7a26cd4d6c43118959aee1dec7216b1c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120249"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-web-services"></a>Suplemento do Excel para serviços da web do Azure Machine Learning Studio
Excel torna mais fácil chamar serviços web diretamente sem a necessidade de escrever qualquer código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Passos para utilizar um serviço da web existentes no livro

1. Abra o [ficheiro do Excel de exemplo](https://aka.ms/amlexcel-sample-2), que contém o suplemento do Excel e os dados sobre passageiros ao Titanic. 
 
    > [!NOTE]
    > Verá que a lista dos serviços Web para o ficheiro e relacionados na parte inferior uma caixa de verificação para "Prever automático". Se ativar auto-prever as previsões de **todos os** seus serviços serão atualizados sempre que houver uma alteração nas entradas. Se a opção estiver desmarcada terá de clicar em "Prever All" para a atualização. Para ativar a auto-prever num ir do nível de serviço para o passo 6.

2. Escolha o serviço web ao clicar no mesmo-"Titanic Survivor previsão (suplemento do Excel do exemplo) [pontuação]" neste exemplo.
   
    ![Selecionar serviço Web](./media/excel-add-in-for-web-services/image1.png)
3. Isto leva-o para o **Predict** secção.  Este livro já contém dados de exemplo, mas para uma pasta de trabalho em branco que pode selecionar uma célula do Excel e clique em **utilizar dados de exemplo**.
4. Selecione os dados com cabeçalhos e clique no ícone de intervalo de dados de entrada.  Certifique-se de que a caixa "meus dados têm cabeçalhos" está selecionada.
5. Sob **saída**, introduza o número de célula onde pretende que a saída para ser, por exemplo "H1" aqui.
6. Clique em **prever**. Se selecionar a caixa de verificação "predict automática" qualquer alteração nas áreas selecionadas (o que o especificado como entrada) irá acionar um pedido e uma atualização das células saída sem a necessidade de pressionar o botão de predict.
   
    ![Prever a secção](./media/excel-add-in-for-web-services/image1.png)

Implementar um serviço web ou utilizar um serviço Web existente. Para obter mais informações sobre como implementar um serviço da web, consulte [Tutorial 3: Implementar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).

Obtenha a chave de API do web Service. Quando efetuar esta ação depende se publicou um serviço da web clássico Machine Learning de um serviço web do Machine Learning de novo.

**Utilizar um serviço web clássico** 

1. No Machine Learning Studio, clique nas **serviços da WEB** secção no painel esquerdo e, em seguida, selecione o serviço web.
   
    ![Selecione de Studio um serviço Web](./media/excel-add-in-for-web-services/image4.png)
2. Copie a chave de API para o serviço web.
   
    ![Chave de API do Studio](./media/excel-add-in-for-web-services/image5.png)
3. Sobre o **DASHBOARD** para o serviço web, clique no **SOLICITAÇÃO/resposta** ligação.
4. Procure o **Request URI** secção.  Copie e guarde o URL.

> [!NOTE]
> Agora é possível iniciar sessão para o [serviços da Web do Azure Machine Learning](https://services.azureml.net) portal para obter a chave de API para um serviço web clássico Machine Learning.
> 
> 

**Utilizar um novo serviço web**

1. Na [serviços da Web do Azure Machine Learning](https://services.azureml.net) portal, clique **serviços da Web**, em seguida, selecione o seu serviço web. 
2. Clique em **consumir**.
3. Procure o **informações básicas do consumo** secção. Copie e guarde o **chave primária** e o **solicitação-resposta** URL.

## <a name="steps-to-add-a-new-web-service"></a>Passos para adicionar um novo serviço web

1. Implementar um serviço web ou utilizar um serviço Web existente. Para obter mais informações sobre como implementar um serviço da web, consulte [Tutorial 3: Implementar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).
2. Clique em **consumir**.
3. Procure o **informações básicas do consumo** secção. Copie e guarde o **chave primária** e o **solicitação-resposta** URL.
4. No Excel, vá para o **serviços Web** secção (se estiver na **Predict** secção, clique na seta voltar para ir para a lista de serviços da web).
   
    ![Aceda a seleção de serviço da Web](./media/excel-add-in-for-web-services/image3.png)
5. Clique em **Adicionar serviço Web**.
6. Cole o URL para o Excel rotulado como a caixa de texto do suplemento **URL**.
7. Cole a chave de API/primária para a caixa de texto rotulada **chave de API**.
8. Clique em **Adicionar**.
   
    ![Chave de API e o URL para um serviço Web clássico.](./media/excel-add-in-for-web-services/image6.png)
9. Para utilizar o serviço web, siga as instruções anteriores, o "Passo a passo para utilizar um serviço de web existente."

## <a name="sharing-your-workbook"></a>O livro de partilha
Se guardar o livro, a chave de API/principal para os serviços da web que adicionou também é guardada. Isso significa que apenas devem partilhar a pasta de trabalho com indivíduos que confia.

Faça as suas questões na secção seguinte comentário ou no nosso [fórum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
