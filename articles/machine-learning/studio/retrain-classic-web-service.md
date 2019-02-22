---
title: Preparar novamente e implementar um serviço Web clássico
titleSuffix: Azure Machine Learning Studio
description: Saiba como voltar a preparar um modelo e atualizar um serviço web clássico a utilizar o modelo treinado recentemente no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: peterlu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: 73ab816c7043c7fb0f572677c6ceeb722653dec1
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650775"
---
# <a name="retrain-and-deploy-a-classic-studio-web-service"></a>Voltar a preparar e implementar um serviço web clássico do Studio

Reparametrizar modelos do machine learning é uma forma de garantir que permanecem precisas e com base nos dados mais relevantes disponíveis. Este artigo irá mostrar como voltar a preparar um serviço web clássico do Studio. Para obter um guia sobre como voltar a preparar um novo serviço de web do Studio, [exibir este artigo de procedimento.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já possui uma experimentação de reparametrização e uma experimentação preditiva. Estes passos são explicados em [voltar a preparar e implementar um modelo de aprendizagem automática.](retrain-models-programmatically.md) No entanto, em vez de implementar o modelo de aprendizagem automática como um novo serviço web, irá implementar sua experimentação preditiva como um serviço web clássico.
     
## <a name="add-a-new-endpoint"></a>Adicionar um novo ponto final

O serviço web preditivo que implementou contém um padrão de ponto final que é mantido em sincronia com a formação original de classificação e modelo treinado de experimentações de classificação. Para atualizar o seu serviço web com um novo modelo preparado, tem de criar um novo ponto de final de classificação.

Existem duas formas na qual pode adicionar um novo ponto final a um serviço web:

* Através de programação
* Com o portal de serviços Web do Azure

> [!NOTE]
> Certifique-se de que está a adicionar o ponto de extremidade ao serviço Web preditivo, não o serviço da Web de treinamento. Se tiver implementado um treinamento e um serviço Web preditivo corretamente, deverá ver dois serviços da web separado listados. O serviço Web preditivo deve terminar com "[exp preditiva.]".
>

### <a name="programmatically-add-an-endpoint"></a>Adicionar programaticamente um ponto final

Pode adicionar pontos finais de classificação usando o código de exemplo fornecido neste [repositório do GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Utilizar o portal de serviços Web do Azure para adicionar um ponto final

1. No Machine Learning Studio, na coluna de navegação esquerda, clique em serviços da Web.
1. Na parte inferior do dashboard do serviço web, clique em **pré-visualização de pontos finais de gerir**.
1. Clique em **Adicionar**.
1. Escreva um nome e descrição para o novo ponto final. Selecione o nível de registo e se os dados de exemplo estão ativados. Para obter mais informações sobre o registo, consulte [ativar o registo de serviços web Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Atualizar o modelo treinado do ponto de extremidade adicionado

### <a name="retrieve-patch-url"></a>Obter URL de PATCH

### <a name="option-1-programmatically"></a>Opção 1: Através de programação

Para obter o URL correto de aplicar um PATCH por meio de programação, siga estes passos:

1. Executar o [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) código de exemplo.
1. A partir da saída de AddEndpoint, localize a *HelpLocation* valor e copie o URL.

   ![HelpLocation na saída do exemplo addEndpoint.](./media/retrain-classic/addEndpoint-output.png)
1. Cole o URL num browser para navegar para uma página que fornece ligações de ajuda para o serviço web.
1. Clique nas **recursos de atualização** link para abrir a página de ajuda do patch.

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>Opção 2: Utilizar o portal de serviços da Web do Azure Machine Learning

Siga estes passos para obter o URL de aplicar PATCHES corretos com o portal web:

1. Inicie sessão para o [serviços da Web do Azure Machine Learning](https://services.azureml.net/) portal.
1. Clique em **serviços Web** ou **serviços Web clássicos** na parte superior.
1. Clique em serviço web de pontuação que está a trabalhar com (se não modificar o nome predefinido do serviço web, ele termina em "[Scoring Exp.]").
1. Clique em **+ novo**.
1. Depois do ponto final é adicionado, clique no nome do ponto final.
1. Sob o **Patch** URL, clique em **API ajuda** para abrir a página de ajuda de aplicação de patches.

> [!NOTE]
> Se tiver adicionado o ponto final para o serviço da Web de treinamento em vez do serviço Web preditivo, receberá o erro seguinte ao clicar o **recursos de atualização** ligação: "Desculpe, mas esta funcionalidade não é suportado ou disponíveis neste contexto. Este serviço Web tem não existem recursos atualizáveis. Podemos desculpa pelo inconveniente e estão a trabalhar na melhoria este fluxo de trabalho."
>

A página de ajuda de PATCH contém o URL de aplicar o PATCH de mensagens em fila tem de utilizar e fornece o código de exemplo, que pode usar para chamá-lo.

![URL de patch.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Atualizar o ponto final

Agora, pode utilizar o modelo preparado para atualizar o ponto de final de classificação que criou anteriormente.

O código de exemplo seguinte mostra como utilizar o *BaseLocation*, *RelativeLocation*, *SasBlobToken*e o URL de aplicar o PATCH para atualizar o ponto final.

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

O *apiKey* e o *endpointUrl* para a chamada pode ser obtida a partir do dashboard de ponto final.

O valor do *Name* parâmetro na *recursos* deve corresponder ao nome do recurso do modelo guardado preparado na experimentação preditiva. Para obter o nome do recurso:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, clique em **Machine Learning**.
1. No nome, clique em sua área de trabalho e, em seguida, clique em **serviços da Web**.
1. Em nome, clique em **recenseamento modelo [exp preditiva].** .
1. Clique em novo ponto final que adicionou.
1. No dashboard do ponto final, clique em **recursos de atualização**.
1. Na página de documentação de API do recurso de atualização para o serviço web, pode encontrar os **nome do recurso** sob **recursos Atualizáveis**.

Se o token SAS expirar antes de terminar de atualizar o ponto final, tem de executar um GET com o ID da tarefa para obter um novo token.

Quando o código tenha sido executada com êxito, o novo ponto final deve começar a utilizar o modelo retrained em aproximadamente 30 segundos.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como gerir os serviços web ou mantenha um registo de várias execuções de experimentações, veja os artigos seguintes:

* [Explorar o portal de serviços da Web](manage-new-webservice.md)
* [Gerir iterações de experimentação](manage-experiment-iterations.md)