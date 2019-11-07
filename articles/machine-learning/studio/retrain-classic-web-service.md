---
title: Treinar novamente um serviço Web clássico
titleSuffix: ML Studio (classic) Azure
description: Saiba como treinar novamente um modelo e atualizar um serviço Web clássico para usar o modelo treinado recentemente no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: 17d15de3df73592c3ce410535235559c8c6fb584
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670523"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Readaptação e implantação de um serviço Web clássico do Studio (clássico)

Treinar novamente os modelos de aprendizado de máquina é uma maneira de garantir que eles permaneçam precisos e com base nos dados mais relevantes disponíveis. Este artigo mostrará como readaptar um serviço Web clássico do Studio (clássico). Para obter um guia sobre como treinar novamente um novo serviço Web de estúdio (clássico), [Veja este artigo de instruções.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha um teste de treinamento e um experimento de previsão. Essas etapas são explicadas em [readaptação e implantação de um modelo de aprendizado de máquina.](/azure/machine-learning/studio/retrain-machine-learning-model) No entanto, em vez de implantar o modelo de aprendizado de máquina como um novo serviço Web, você implantará seu experimento de previsão como um serviço Web clássico.
     
## <a name="add-a-new-endpoint"></a>Adicionar um novo ponto de extremidade

O serviço Web de previsão implantado contém um ponto de extremidade de Pontuação padrão que é mantido em sincronia com o treinamento original e os experimentos de Pontuação treinados modelo. Para atualizar seu serviço Web para com um novo modelo treinado, você deve criar um novo ponto de extremidade de pontuação.

Há duas maneiras pelas quais você pode adicionar um novo ponto de extremidade a um serviço Web:

* Através de programação
* Usando o portal de serviços Web do Azure

> [!NOTE]
> Certifique-se de que você está adicionando o ponto de extremidade ao serviço Web de previsão, não ao serviço Web de treinamento. Se você tiver implantado corretamente um treinamento e um serviço Web de previsão, verá dois serviços Web separados listados. O serviço Web de previsão deve terminar com "[exp. de previsão]".
>

### <a name="programmatically-add-an-endpoint"></a>Adicionar programaticamente um ponto de extremidade

Você pode adicionar pontos de extremidade de Pontuação usando o código de exemplo fornecido neste [repositório GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Usar o portal de serviços Web do Azure para adicionar um ponto de extremidade

1. Em Machine Learning Studio (clássico), na coluna de navegação à esquerda, clique em serviços Web.
1. Na parte inferior do painel do serviço Web, clique em **gerenciar visualização de pontos de extremidade**.
1. Clique em **Adicionar**.
1. Digite um nome e uma descrição para o novo ponto de extremidade. Selecione o nível de log e se os dados de exemplo estão habilitados. Para obter mais informações sobre registro em log, consulte [habilitar o log para serviços web Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Atualizar o modelo treinado do ponto de extremidade adicionado

### <a name="retrieve-patch-url"></a>Recuperar URL do PATCH

### <a name="option-1-programmatically"></a>Opção 1: programaticamente

Para obter a URL correta do PATCH programaticamente, siga estas etapas:

1. Execute o código de exemplo [addendpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
1. Na saída de addendpoint, localize o valor *HelpLocation* e copie a URL.

   ![HelpLocation na saída do exemplo addendpoint.](./media/retrain-classic/addEndpoint-output.png)
1. Cole a URL em um navegador para navegar até uma página que fornece links de ajuda para o serviço Web.
1. Clique no link **Atualizar recurso** para abrir a página de ajuda do patch.

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>Opção 2: usar o Azure Machine Learning Portal de serviços Web

Siga estas etapas para obter a URL correta do PATCH usando o portal da Web:

1. Entre no portal de [Serviços Web do Azure Machine Learning](https://services.azureml.net/) .
1. Clique em **Serviços Web** ou **Serviços Web clássicos** na parte superior.
1. Clique no serviço Web de pontuação com o qual você está trabalhando (se você não modificou o nome padrão do serviço Web, ele terminará em "[pontuação exp.]").
1. Clique em **+ novo**.
1. Depois que o ponto de extremidade for adicionado, clique no nome do ponto de extremidade.
1. Na URL do **patch** , clique em **ajuda da API** para abrir a página de ajuda de aplicação de patch.

> [!NOTE]
> Se você adicionou o ponto de extremidade ao serviço Web de treinamento em vez do serviço Web de previsão, receberá o seguinte erro ao clicar no link **Atualizar recurso** : "Desculpe, mas esse recurso não tem suporte ou está disponível neste contexto. Este serviço Web não tem recursos atualizáveis. Pedimos desculpas pelo inconveniente e estamos trabalhando para aprimorar esse fluxo de trabalho. "
>

A página de ajuda do PATCH contém a URL do PATCH que você deve usar e fornece um código de exemplo que você pode usar para chamá-la.

![URL do patch.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Atualizar o ponto de extremidade

Agora você pode usar o modelo treinado para atualizar o ponto de extremidade de pontuação que você criou anteriormente.

O código de exemplo a seguir mostra como usar o *BaseLocation*, o *RelativeLocation*, o *SASBLOBTOKEN*e a URL de patch para atualizar o ponto de extremidade.

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
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
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

O *apiKey* e o *endpointUrl* para a chamada podem ser obtidos no painel do ponto de extremidade.

O valor do parâmetro *Name* nos *recursos* deve corresponder ao nome do recurso do modelo treinado salvo no experimento de previsão. Para obter o nome do recurso:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. No menu à esquerda, clique em **Machine Learning**.
1. Em nome, clique em seu espaço de trabalho e clique em **Serviços Web**.
1. Em nome, clique em **modelo de censo [EXP de previsão.]** .
1. Clique no novo ponto de extremidade adicionado.
1. No painel do ponto de extremidade, clique em **Atualizar recurso**.
1. Na página de documentação atualizar a API do recurso para o serviço Web, você pode encontrar o **nome do recurso** em **recursos atualizáveis**.

Se o token SAS expirar antes de concluir a atualização do ponto de extremidade, você deverá executar um GET com a ID do trabalho para obter um novo token.

Quando o código for executado com êxito, o novo ponto de extremidade deverá começar a usar o modelo retreinado em aproximadamente 30 segundos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como gerenciar serviços Web ou controlar várias execuções de experimentos, consulte os seguintes artigos:

* [Explore o portal de serviços Web](manage-new-webservice.md)
* [Gerenciar iterações de experimento](manage-experiment-iterations.md)