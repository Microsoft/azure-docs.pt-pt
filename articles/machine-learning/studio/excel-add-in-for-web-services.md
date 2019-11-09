---
title: Suplemento do Excel para serviços Web
titleSuffix: ML Studio (classic) - Azure
description: Como usar Azure Machine Learning serviços Web diretamente no Excel sem escrever nenhum código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: b27e1c498aab3cc6c14eaa8a5851c9262535e95a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837792"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Suplemento do Excel para serviços Web Azure Machine Learning Studio (clássico)
O Excel facilita a chamada de serviços da Web diretamente, sem a necessidade de escrever nenhum código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Etapas para usar um serviço Web existente na pasta de trabalho

1. Abra o [arquivo do Excel de exemplo](https://aka.ms/amlexcel-sample-2), que contém o suplemento do Excel e os dados sobre passageiros no Titanic. 
 
    > [!NOTE]
    > Você verá a lista dos serviços Web relacionados ao arquivo e, na parte inferior, uma caixa de seleção para "previsão automática". Se você habilitar a previsão automática, as previsões de **todos** os seus serviços serão atualizadas toda vez que houver uma alteração nas entradas. Se não estiver marcada, você precisará clicar em "prever tudo" para atualizar. Para habilitar a previsão automática em um nível de serviço, vá para a etapa 6.

2. Escolha o serviço Web clicando nele-"Titanic sobrevivente Predict (exemplo de suplemento do Excel) [score]" neste exemplo.
   
    ![Selecionar serviço Web](./media/excel-add-in-for-web-services/image1.png)
3. Isso levará você à seção de **previsão** .  Esta pasta de trabalho já contém dados de exemplo, mas para uma pasta de trabalho em branco, você pode selecionar uma célula no Excel e clicar em **usar dados de exemplo**.
4. Selecione os dados com cabeçalhos e clique no ícone intervalo de dados de entrada.  Verifique se a caixa "meus dados têm cabeçalhos" está marcada.
5. Em **saída**, insira o número da célula onde você deseja que a saída seja, por exemplo, "H1" aqui.
6. Clique em **prever**. Se você marcar a caixa de seleção "previsão automática", qualquer alteração nas áreas selecionadas (aquelas especificadas como entrada) disparará uma solicitação e uma atualização das células de saída sem a necessidade de pressionar o botão de previsão.
   
    ![Seção de previsão](./media/excel-add-in-for-web-services/image1.png)

Implantar um serviço Web ou usar um serviço Web existente. Para obter mais informações sobre como implantar um serviço Web, consulte [tutorial 3: implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).

Obtenha a chave de API para seu serviço Web. O local em que você executa essa ação depende se você publicou um serviço Web clássico Machine Learning de um novo serviço Web Machine Learning.

**Usar um serviço Web clássico** 

1. Em Machine Learning Studio (clássico), clique na seção **Serviços Web** no painel esquerdo e selecione o serviço Web.
   
    ![Selecionar um serviço Web do estúdio](./media/excel-add-in-for-web-services/image4.png)
2. Copie a chave de API para o serviço Web.
   
    ![Chave de API do estúdio](./media/excel-add-in-for-web-services/image5.png)
3. Na guia **painel** do serviço Web, clique no link **solicitação/resposta** .
4. Procure a seção **URI de solicitação** .  Copie e salve a URL.

> [!NOTE]
> Agora é possível entrar no portal de [serviços web Azure Machine Learning](https://services.azureml.net) para obter a chave de API para um serviço Web clássico Machine Learning.
> 
> 

**Usar um novo serviço Web**

1. No portal de [Serviços Web do Azure Machine Learning](https://services.azureml.net) , clique em **Serviços Web**e selecione seu serviço Web. 
2. Clique em **consumir**.
3. Procure a seção **informações básicas de consumo** . Copie e salve a **chave primária** e a URL da **solicitação-resposta** .

## <a name="steps-to-add-a-new-web-service"></a>Etapas para adicionar um novo serviço Web

1. Implantar um serviço Web ou usar um serviço Web existente. Para obter mais informações sobre como implantar um serviço Web, consulte [tutorial 3: implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).
2. Clique em **consumir**.
3. Procure a seção **informações básicas de consumo** . Copie e salve a **chave primária** e a URL da **solicitação-resposta** .
4. No Excel, vá para a seção **Serviços Web** (se você estiver na seção **previsão** , clique na seta para voltar para ir para a lista de serviços Web).
   
    ![Ir para seleção de serviço Web](./media/excel-add-in-for-web-services/image3.png)
5. Clique em **Adicionar serviço Web**.
6. Cole a URL na caixa de texto do suplemento do Excel chamada **URL**.
7. Cole a chave de API/primária na caixa de texto chamada **chave de API**.
8. Clique em **Adicionar**.
   
    ![URL e chave de API para um serviço Web clássico.](./media/excel-add-in-for-web-services/image6.png)
9. Para usar o serviço Web, siga as instruções anteriores, "etapas para usar um serviço Web existente".

## <a name="sharing-your-workbook"></a>Compartilhando sua pasta de trabalho
Se você salvar sua pasta de trabalho, a API/chave primária para os serviços Web que você adicionou também será salva. Isso significa que você só deve compartilhar a pasta de trabalho com indivíduos confiáveis.

Faça perguntas na seguinte seção de comentário ou em nosso [Fórum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
