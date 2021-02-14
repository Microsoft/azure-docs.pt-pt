---
title: 'ML Studio (clássico): retrainte o serviço web clássico - Azure'
description: Aprenda a treinar um modelo e a atualizar um serviço web clássico para utilizar o modelo recém-treinado no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: 90c968ee953e80238775639964cb09a25741b33d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517574"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Retreine e implemente um serviço web clássico do Studio (clássico)

**APLICA-SE A:** ![ Marca verde de verificação. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ X indicando não ](../../../includes/media/aml-applies-to-skus/no.png)[A. Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


A reconversão de modelos de aprendizagem automática é uma forma de garantir que se mantêm precisas e com base nos dados mais relevantes disponíveis. Este artigo irá mostrar-lhe como treinar um serviço web clássico do Studio (clássico). Para um guia sobre como treinar um novo serviço web Studio (clássico), [veja este artigo como-a-dia.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já tem uma experiência de reconversão e uma experiência preditiva. Estes passos são explicados na [Retrain e implementam um modelo de aprendizagem automática.](./retrain-machine-learning-model.md) No entanto, em vez de implementar o seu modelo de machine learning como um novo serviço web, irá implementar a sua experiência preditiva como um serviço web clássico.
     
## <a name="add-a-new-endpoint"></a>Adicione um novo ponto final

O serviço web preditivo que implementou contém um ponto final de pontuação padrão que é mantido em sintonia com o modelo treinado de treino e pontuação original. Para atualizar o seu serviço web com um novo modelo treinado, tem de criar um novo ponto final de pontuação.

Existem duas formas de adicionar um novo ponto final a um serviço web:

* Através de programação
* Utilizando o portal Azure Web Services

> [!NOTE]
> Certifique-se de que está a adicionar o ponto final ao Serviço Web Preditivo, não ao Serviço Web de Formação. Se implementou corretamente um Serviço Web de Formação e Um Serviço Web Preditivo, deverá ver dois serviços web separados listados. O Serviço Web Preditivo deve terminar com "[exp predictive.]".
>

### <a name="programmatically-add-an-endpoint"></a>Programáticamente adicionar um ponto final

Pode adicionar pontos finais de pontuação utilizando o código de amostra fornecido neste [repositório GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Utilize o portal Azure Web Services para adicionar um ponto final

1. No Machine Learning Studio (clássico), na coluna de navegação à esquerda, clique nos Serviços Web.
1. Na parte inferior do painel de instrumentos de serviço web, clique em Gerir a **pré-visualização dos pontos finais**.
1. Clique em **Adicionar**.
1. Digite um nome e descrição para o novo ponto final. Selecione o nível de registo e se os dados da amostra estão ativados. Para obter mais informações sobre o registo, consulte [Ativar o registo de serviços web machine learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Atualizar o modelo treinado do endpoint adicionado

### <a name="retrieve-patch-url"></a>Retrieve PATCH URL

Siga estes passos para obter o URL PATCH correto utilizando o portal web:

1. Inscreva-se no portal [Azure Machine Learning Web Services.](https://services.azureml.net/)
1. Clique em **Serviços Web** ou **Serviços Web Clássicos** no topo.
1. Clique no serviço web de pontuação com o qual está a trabalhar (se não modificar o nome padrão do serviço web, terminará em "[Scoring Exp.]").
1. Clique **em +NEW**.
1. Depois de adicionar o ponto final, clique no nome do ponto final.
1. No URL **patch,** clique em **Ajuda API** para abrir a página de ajuda de remendação.

> [!NOTE]
> Se adicionar o ponto final ao Serviço Web de Formação em vez do Serviço Web Preditivo, receberá o seguinte erro quando clicar no link **'Recurso de actualização':** "Desculpe, mas esta funcionalidade não está suportada ou disponível neste contexto. Este Serviço Web não tem recursos necessários. Pedimos desculpa pelo inconveniente e estamos a trabalhar para melhorar este fluxo de trabalho."
>

A página de ajuda PATCH contém o URL PATCH que deve utilizar e fornece o código de amostra que pode usar para chamá-lo.

![URL de patch.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Atualizar o ponto final

Agora pode utilizar o modelo treinado para atualizar o ponto final de pontuação que criou anteriormente.

O seguinte código de amostra mostra-lhe como utilizar o *BaseLocation*, *RelativeLocation*, *SasBlobToken* e PATCH URL para atualizar o ponto final.

```csharp
private async Task OverwriteModel()
{
    var resourceLocations = new
    {
        Resources = new[]
        {
            new
            {
                Name = "Census Model [trained model]",
                Location = new AzureBlobDataReference()
                {
                    BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                    RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                    SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
                }
            }
        }
    };

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

        using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
        {
            request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
            HttpResponseMessage response = await client.SendAsync(request);

            if (!response.IsSuccessStatusCode)
            {
                await WriteFailedResponse(response);
            }

            // Do what you want with a successful response here.
        }
    }
}
```

O *apiKey* e o *endpointUrl* para a chamada podem ser obtidos a partir do painel de instrumentos de ponta.

O valor do parâmetro *Nome* em *Recursos* deve coincidir com o Nome de Recurso do Modelo Treinado Salvo na experiência preditiva. Para obter o Nome de Recurso:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu esquerdo, clique em **Machine Learning**.
1. Em Nome, clique no seu espaço de trabalho e, em seguida, clique em **Serviços Web**.
1. Em Nome, clique no **Modelo de Censos [exp preditivo.]**.
1. Clique no novo ponto final que adicionou.
1. No painel de instrumentos de ponto final, clique em **Atualizar Recurso**.
1. Na página de documentação da API de recurso de atualização para o serviço web, pode encontrar o **Nome de Recurso** em Recursos **Updatable**.

Se o seu token SAS expirar antes de terminar de atualizar o ponto final, tem de efetuar um GET com o Job ID para obter um novo token.

Quando o código tiver sido executado com sucesso, o novo ponto final deve começar a utilizar o modelo retreinado em aproximadamente 30 segundos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como gerir serviços web ou acompanhar várias experiências, consulte os seguintes artigos:

* [Explore o portal de Serviços Web](manage-new-webservice.md)
* [Gerir iterações das experimentações](manage-experiment-iterations.md)