---
title: Complemento excel para serviços web
titleSuffix: ML Studio (classic) - Azure
description: Como utilizar os serviços do Azure Machine Learning Web diretamente no Excel sem escrever nenhum código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e30103589c1baf9a165839cd041ff511a119c5ff
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204380"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Excel Add-in para serviços web Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Excel torna mais fácil chamar serviços web diretamente sem a necessidade de escrever qualquer código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Passos para utilizar um serviço da web existentes no livro

1. Abra o ficheiro Excel da [amostra,](https://aka.ms/amlexcel-sample-2)que contém o add-in Excel e dados sobre passageiros no Titanic. 
 
    > [!NOTE]
    > Verá que a lista dos serviços Web para o ficheiro e relacionados na parte inferior uma caixa de verificação para "Prever automático". Se permitir prever automaticamente as previsões de **todos os** seus serviços serão atualizadas sempre que houver uma alteração nas inputs. Se a opção estiver desmarcada terá de clicar em "Prever All" para a atualização. Para ativar a auto-prever num ir do nível de serviço para o passo 6.

2. Escolha o serviço web ao clicar no mesmo-"Titanic Survivor previsão (suplemento do Excel do exemplo) [pontuação]" neste exemplo.
   
    ![Selecionar serviço Web](./media/excel-add-in-for-web-services/image1.png)
3. Isto leva-te à secção **Previsão.**  Este livro já contém dados de amostra, mas para um livro em branco pode selecionar uma célula no Excel e clicar **em utilizar dados da amostra**.
4. Selecione os dados com cabeçalhos e clique no ícone de intervalo de dados de entrada.  Certifique-se de que a caixa "meus dados têm cabeçalhos" está selecionada.
5. Em **'Saída',** introduza o número de telemóvel onde pretende que a saída esteja, por exemplo "H1" aqui.
6. Clique em **Prever**. Se selecionar a caixa de verificação "predict automática" qualquer alteração nas áreas selecionadas (o que o especificado como entrada) irá acionar um pedido e uma atualização das células saída sem a necessidade de pressionar o botão de predict.
   
    ![Prever a secção](./media/excel-add-in-for-web-services/image1.png)

Implementar um serviço web ou utilizar um serviço Web existente. Para obter mais informações sobre a implementação de um serviço web, consulte [Tutorial 3: Implementar modelo](tutorial-part3-credit-risk-deploy.md)de risco de crédito .

Obtenha a chave de API do web Service. Quando efetuar esta ação depende se publicou um serviço da web clássico Machine Learning de um serviço web do Machine Learning de novo.

**Use um serviço web clássico** 

1. No Machine Learning Studio (clássico), clique na secção **WEB SERVICES** no painel esquerdo e, em seguida, selecione o serviço web.
   
    ![Selecione de Studio um serviço Web](./media/excel-add-in-for-web-services/image4.png)
2. Copie a chave de API para o serviço web.
   
    ![Chave de API do Studio](./media/excel-add-in-for-web-services/image5.png)
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

1. Implementar um serviço web ou utilizar um serviço Web existente. Para obter mais informações sobre a implementação de um serviço web, consulte [Tutorial 3: Implementar modelo](tutorial-part3-credit-risk-deploy.md)de risco de crédito .
2. Clique **em Consumir**.
3. Procure a secção de informação de **consumo básico.** Copie e guarde a **Chave Primária** e o URL de Resposta **a Pedido.**
4. No Excel, vá à secção **de Serviços Web** (se estiver na secção **Previsão,** clique na seta traseira para ir à lista de serviços web).
   
    ![Aceda a seleção de serviço da Web](./media/excel-add-in-for-web-services/image3.png)
5. Clique em **Adicionar Serviço Web**.
6. Colar o URL no URL de caixa de texto do Excel com o rótulo de **URL**.
7. Colar a tecla API/Primary na **tecla API**da caixa de texto marcada .
8. Clique em **Adicionar**.
   
    ![Chave de API e o URL para um serviço Web clássico.](./media/excel-add-in-for-web-services/image6.png)
9. Para utilizar o serviço web, siga as instruções anteriores, o "Passo a passo para utilizar um serviço de web existente."

## <a name="sharing-your-workbook"></a>O livro de partilha
Se guardar o livro, a chave de API/principal para os serviços da web que adicionou também é guardada. Isso significa que apenas devem partilhar a pasta de trabalho com indivíduos que confia.

Faça quaisquer perguntas na secção de comentários seguintes ou no nosso [fórum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
