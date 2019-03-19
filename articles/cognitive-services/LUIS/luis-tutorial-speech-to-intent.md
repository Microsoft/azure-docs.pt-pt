---
title: Utilize a voz C# SDK
titleSuffix: Azure Cognitive Services
description: O Serviço de Voz permite-lhe utilizar um pedido simples para receber áudio e devolve objetos JSON de predição de LUIS. Neste artigo, vai transferir e utilizar um projeto C# no Visual Studio para verbalizar uma expressão para um microfone e receber informações de predição de LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 6f775ffaf53019cc50bc38c294b4d5f40c8eca90
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076756"
---
# <a name="integrate-speech-service-with-your-language-understanding-app"></a>Integre o serviço de voz com a sua aplicação de compreensão de idiomas
O [serviço de Voz](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) permite-lhe utilizar um pedido simples para receber áudio e devolve objetos JSON de predição de LUIS. Neste artigo, vai transferir e utilizar um projeto C# no Visual Studio para verbalizar uma expressão para um microfone e receber informações de predição de LUIS. O projeto utiliza o pacote [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) de Voz, já incluído como referência. 

Para este artigo, necessita de uma conta gratuita do Web site [LUIS][LUIS] para importar a aplicação.

## <a name="create-luis-endpoint-key"></a>Criar uma chave de ponto final de LUIS
No portal do Azure, [crie](luis-how-to-azure-subscription.md) uma chave do **Language Understanding** (LUIS). 

## <a name="import-human-resources-luis-app"></a>Importar a aplicação de Recursos Humanos LUIS
Os objetivos e expressões com para este artigo são a partir da aplicação de recursos humanos LUIS disponível a partir da [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding) repositório do GitHub. Transfira o ficheiro [HumanResources.json](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources.json), guarde-o com a extensão `.json` e [importe-o](luis-how-to-start-new-app.md#import-new-app) para LUIS. 

Esta aplicação contém intenções, entidades e expressões relacionadas com o domínio dos Recursos Humanos. Entre as expressões de exemplo incluem-se:

|Expressões de exemplo|
|--|
|Quem é o gestor de John Smith?|
|Quem é que John Smith gere?|
|Onde está o Formulário 123456?|
|Tenho direito a folgas remuneradas?|


## <a name="add-keyphrase-prebuilt-entity"></a>Adicionar entidade pré-compilada de KeyPhrase
Depois de importar a aplicação, selecione **Entidades** e, em seguida, **Adicionar entidade pré-compilada**. Adicione a entidade **KeyPhrase**. A entidade KeyPhrase extrai assuntos-chave da expressão.

## <a name="train-and-publish-the-app"></a>Preparar e publicar a aplicação
1. Na barra de navegação superior direita, selecione o botão **Preparar** para preparar a aplicação LUIS.

2. Selecione **Gerir** na barra superior direita e, em seguida, selecione **Chaves e pontos finais** na barra de navegação esquerda. 

3. Na página **Chaves e pontos finais**, atribua a chave de LUIS criada na secção [Criar chave de ponto final de LUIS](#create-luis-endpoint-key).

   Nesta página, recolha o ID da aplicação, a região de publicação e o ID de subscrição da chave de LUIS criado na secção [Criar chave de ponto final de LUIS](#create-luis-endpoint-key). Terá de modificar o código para utilizar estes valores mais adiante neste artigo. 
  
   **Não** utilize a chave gratuita de Starter para este exercício. Apenas uma chave de **Language Understanding** criada no portal do Azure irá funcionar para este exercício. 

   https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=


4. Publique aplicação LUIS, através da seleção do botão **Publicar** na barra superior direita. 

## <a name="audio-device"></a>Dispositivo de áudio
Este artigo utiliza o dispositivo de áudio do seu computador. Pode ser um headset com microfone ou um dispositivo de áudio incorporado. Verifique os níveis de entrada de áudio para ver se deve falar mais alto do que normalmente faria para que a sua voz seja detetada pelo dispositivo de áudio. 

## <a name="download-the-luis-sample-project"></a>Transferir o projeto de Exemplo de LUIS
 Clonar ou transferir os [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding) repositório. Abra o [Projeto de conversão de voz em intenção](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-speech-intent-recognition) com o Visual Studio e restaure os pacotes de NuGet. O ficheiro de solução de VS é documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

O SDK de Voz já está incluído como referência. 

[![Pacote de Microsoft.CognitiveServices.Speech NuGet mostrando de captura de ecrã do Visual Studio 2017](./media/luis-tutorial-speech-to-intent/nuget-package.png "pacote de Microsoft.CognitiveServices.Speech NuGet mostrando de captura de ecrã do Visual Studio 2017")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Modificar o código C#
Abrir o ficheiro `Program.cs` e alterar as variáveis seguintes:

|Nome da variável|Objetivo|
|--|--|
|LUIS_assigned_endpoint_key|Corresponde ao valor da chave de subscrição atribuído ao URL de ponto final da página Publicar|
|LUIS_endpoint_key_region|Corresponde ao primeiro subdomínio do URL de ponto final, por exemplo `westus`|
|LUIS_app_ID|Corresponde à rota do URL de ponto final a seguir a **aplicações/**|

O ficheiro `Program.cs` já contém as intenções de Recursos Humanos mapeadas.

Compile e execute a aplicação. 

## <a name="test-code-with-utterance"></a>Código de teste com expressão
Fale para o microfone "Quais são os dentistas recomendados em Redmond?".

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

Foi encontrada a intenção correta, **GetEmployeeBenefits**, com uma confiança de 85%. Foi devolvida a entidade KeyPhrase. 

O SDK de Voz devolve a resposta completa de LUIS. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não necessitar da aplicação LUIS HumanResources, elimine-a. Para o fazer, selecione a aplicação e, em seguida, na barra de ferramentas contextual por cima da lista, selecione **Eliminar**. Na caixa de diálogo de pop-up **Delete app?** (Eliminar aplicação?), selecione **OK**.

Não se esqueça de eliminar o diretório quando tiver terminado com o código de exemplo.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Integrar LUIS com um Bot](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
