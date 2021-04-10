---
title: 'Quickstart: Biblioteca de clientes de reconhecimento de caracteres óticos para Go'
titleSuffix: Azure Cognitive Services
description: Começa com a biblioteca de clientes de reconhecimento de caracteres óticos para acompanhar este arranque rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 0ae059054c68da662e05342525987f6925184906
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073593"
---
<a name="HOLTop"></a>

Utilize a biblioteca de clientes OCR para ler texto impresso e manuscrito a partir de imagens.

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)  |  [Pacote](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A última versão de [Go](https://golang.org/dl/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-go-project-directory"></a>Criar um diretório de projeto go

Numa janela de consola (cmd, PowerShell, Terminal, Bash), crie um novo espaço de trabalho para o seu projeto Go, nomeado `my-app` , e navegue até ele.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

O seu espaço de trabalho conterá três pastas:

* **src** - Este diretório conterá código fonte e pacotes. Quaisquer pacotes instalados com o `go get` comando irão para este diretório.
* **pkg** - Este diretório conterá os objetos de embalagem Go compilados. Todos estes ficheiros têm uma `.a` extensão.
* **bin** - Este diretório conterá os ficheiros binários executáveis que são criados quando executa `go install` .

> [!TIP]
> Para saber mais sobre a estrutura de um espaço de trabalho Go, consulte a [documentação da língua Go.](https://golang.org/doc/code.html#Workspaces) Este guia inclui informações para a definição `$GOPATH` e `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Instale a biblioteca do cliente para Go

Em seguida, instale a biblioteca do cliente para Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

ou se utilizar o dep, dentro da sua corrida de repo:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Criar uma aplicação Go 

Em seguida, crie um ficheiro no **diretório src** chamado `sample-app.go` :

```bash
cd src
touch sample-app.go
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)que contém os exemplos de código neste arranque rápido.

Abra `sample-app.go` no seu IDE preferido ou editor de texto.

Declare um contexto na raiz do seu roteiro. Vai precisar deste objeto para executar a maioria das chamadas de função de Visão de Computador.

### <a name="find-the-subscription-key-and-endpoint"></a>Encontre a chave de subscrição e o ponto final

Aceda ao portal do Azure. Se o recurso de Visão De Computador que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a chave de subscrição e o ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 

Crie variáveis para a sua chave de subscrição de Visão de Computador e ponto final. Cole a chave de subscrição e o ponto final no seguinte código, quando indicado. O seu ponto final de visão de computador tem o formulário `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Lembre-se de remover a chave de subscrição do seu código quando terminar e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [cofre de chaves Azure](../../../../key-vault/general/overview.md).

Em seguida, começará a adicionar código para realizar diferentes operações de OCR.

> [!div class="nextstepaction"]
> [Criei o cliente](?success=set-up-client#object-model) [que encontrei um problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do OCR Go SDK.

|Nome|Descrição|
|---|---|
| [BaseCiente](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Esta classe é necessária para todas as funcionalidades de Visão de Computador, como análise de imagem e leitura de texto. Você instantanea-o com as suas informações de subscrição, e você usá-lo para fazer a maioria das operações de imagem.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Este tipo contém os resultados de uma operação de Leitura de Lote. |

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes OCR para Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este passo pressupõe que [criou variáveis ambientais](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a sua chave de Visão de Computador e ponto final, nomeado `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT` respectivamente.

Crie uma `main` função e adicione-lhe o seguinte código para instantaneaizar um cliente com o seu ponto final e chave.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Autensei o cliente](?success=authenticate-client#read-printed-and-handwritten-text) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

O serviço OCR pode ler texto visível numa imagem e convertê-lo num fluxo de caracteres. O código nesta secção define uma função, `RecognizeTextReadAPIRemoteImage` que utiliza o objeto cliente para detetar e extrair texto impresso ou manuscrito na imagem.

Adicione a referência de imagem da amostra e a chamada de função na sua `main` função.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> Também pode extrair texto de uma imagem local. Consulte os métodos [baseClient,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) tais como **BatchReadFileInStream**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) para cenários que envolvam imagens locais.

### <a name="call-the-read-api"></a>Ligue para a API de leitura

Defina a nova função de texto de leitura, `RecognizeTextReadAPIRemoteImage` . Adicione o código abaixo, que chama o método **BatchReadFile** para a imagem dada. Este método devolve um ID de operação e inicia um processo assíncrona para ler o conteúdo da imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Obter resultados de Leitura

Em seguida, obtenha o ID de operação devolvido da chamada **BatchReadFile** e use-o com o método **GetReadOperationResult** para consultar o serviço para obter resultados de operação. O código seguinte verifica a operação em intervalos de um segundo até que os resultados sejam devolvidos. Em seguida, imprime os dados de texto extraídos para a consola.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Mostrar Resultados de leitura

Adicione o seguinte código para analisar e exibir os dados de texto recuperados e terminar a definição de função.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Li um sms](?success=read-printed-handwritten-text#run-the-application) [que encontrei um problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Executar a aplicação

Executar o pedido do seu diretório de candidaturas com o `go run` comando.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Corri o requerimento](?success=run-the-application#clean-up-resources) [que encontrei.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Limpei os recursos](?success=clean-up-resources#next-steps) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a instalar a biblioteca de clientes OCR e a utilizar a API de Leitura. Em seguida, saiba mais sobre as funcionalidades da API de Leitura.

> [!div class="nextstepaction"]
>[Ligue para a API de leitura](../../Vision-API-How-to-Topics/call-read-api.md)

* [Visão geral do OCR](../../overview-ocr.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
