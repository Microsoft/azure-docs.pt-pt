---
title: Retre um serviço web clássico
titleSuffix: ML Studio (classic) - Azure
description: Aprenda a retreinar um modelo e atualizar um serviço web clássico para usar o modelo recém-treinado no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: caf2437b4a4853bc29f094d082a4ea15d2f7a3c9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388475"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Retre e implemente um clássico serviço web Studio (clássico)

Retreinar modelos de aprendizagem automática é uma forma de garantir que se mantêm precisos e baseados nos dados mais relevantes disponíveis. Este artigo irá mostrar-lhe como retreinar um clássico serviço web studio (clássico). Para um guia sobre como retreinar um novo serviço web Studio (clássico), [veja este artigo como fazer.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já tem uma experiência de reconversão e uma experiência preditiva. Estes passos são explicados na [Retrain e implantam um modelo de aprendizagem automática.](/azure/machine-learning/studio/retrain-machine-learning-model) No entanto, em vez de implementar o seu modelo de machine learning como um novo serviço web, irá implementar a sua experiência preditiva como um serviço web clássico.
     
## <a name="add-a-new-endpoint"></a>Adicione um novo ponto final

O serviço web preditivo que implementou contém um ponto final de pontuação predefinido que é mantido em sincronização com o modelo treinado por experiências de treino e pontuação originais. Para atualizar o seu serviço web com um novo modelo treinado, tem de criar um novo ponto final de pontuação.

Existem duas formas de adicionar um novo ponto final a um serviço web:

* Através de programação
* Utilização do portal Azure Web Services

> [!NOTE]
> Certifique-se de que está adicionando o ponto final ao Serviço Web Preditivo, não ao Serviço Web de Formação. Se implementou corretamente um Treino e um Serviço Web Preditivo, deverá ver dois serviços web separados listados. O Serviço Web Preditivo deve terminar com "[exp preditivo.]".
>

### <a name="programmatically-add-an-endpoint"></a>Adicione programáticamente um ponto final

Pode adicionar pontos finais de pontuação utilizando o código de amostra fornecido neste [repositório GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Utilize o portal Azure Web Services para adicionar um ponto final

1. No Machine Learning Studio (clássico), na coluna de navegação à esquerda, clique em Web Services.
1. Na parte inferior do painel de instrumentos do serviço web, clique em **Gerir a pré-visualização de pontos finais**.
1. Clique em **Adicionar**.
1. Escreva um nome e descrição para o novo ponto final. Selecione o nível de registo e se os dados de exemplo estão ativados. Para obter mais informações sobre a exploração madeireira, consulte [A exploração de login para serviços web de aprendizagem automática](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Atualizar o modelo treinado do ponto final

### <a name="retrieve-patch-url"></a>Recuperar URL patch

Siga estes passos para obter o URL PATCH correto utilizando o portal web:

1. Inscreva-se no portal [Azure Machine Learning Web Services.](https://services.azureml.net/)
1. Clique em **Web Services** ou Classic **Web Services** no topo.
1. Clique no serviço web de pontuação com o qual está a trabalhar (se não modificar o nome padrão do serviço web, terminará em "[Scoring Exp.]").
1. Clique **em +Novo**.
1. Depois de adicionado o ponto final, clique no nome final.
1. No url do **Patch,** clique em **Ajuda API** para abrir a página de ajuda de remendos.

> [!NOTE]
> Se adicionar o ponto final ao Serviço Web de Treino em vez do Serviço Web Preditivo, receberá o seguinte erro quando clicar na ligação **'Update Resource':** "Desculpe, mas esta funcionalidade não é suportada ou disponível neste contexto. Este Serviço Web não tem recursos atualizados. Pedimos desculpa pelo inconveniente e estamos a trabalhar para melhorar este fluxo de trabalho."
>

A página de ajuda patch contém o URL patch que deve usar e fornece código de amostra que pode usar para chamá-lo.

![Patch URL.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Atualizar o ponto final

Agora pode utilizar o modelo treinado para atualizar o ponto final de pontuação que criou anteriormente.

O seguinte código de amostra mostra-lhe como utilizar o *Url baseLocation*, *RelativeLocation,* *SasBlobToken*e PATCH para atualizar o ponto final.

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

O *apiKey* e o *endpointUrl* para a chamada podem ser obtidos a partir do painel de instrumentos de ponto final.

O valor do parâmetro *nome* em *Recursos* deve coincidir com o Nome de Recurso do Modelo Treinado salvo na experiência preditiva. Para obter o nome do recurso:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu esquerdo, clique em **Machine Learning**.
1. Em Nome, clique no seu espaço de trabalho e, em seguida, clique em **Web Services**.
1. Em nome, clique **no Census Model [exp preditivo.]** .
1. Clique no novo ponto final que adicionou.
1. No painel de instrumentos de ponto final, clique em **Atualizar Recurso**.
1. Na página de Documentação API de Recurso atualizado para o serviço web, pode encontrar o Nome de **Recurso** em **Recursos Updatable**.

Se o seu token SAS expirar antes de terminar a atualização do ponto final, tem de realizar um GET com o ID de trabalho para obter um token fresco.

Quando o código tiver sido executado com sucesso, o novo ponto final deve começar a utilizar o modelo retreinado em aproximadamente 30 segundos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como gerir os serviços web ou acompanhar as várias experiências, consulte os seguintes artigos:

* [Explore o portal de Serviços Web](manage-new-webservice.md)
* [Gerir iterações de experimentação](manage-experiment-iterations.md)