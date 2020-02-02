---
title: 'Quickstart: Encarar a biblioteca de clientes para go [ Go ] Microsoft Docs'
description: Começa com a biblioteca de clientes do Face para o Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 2db40150167a8f16242b2feb15b77820fa1970a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76941431"
---
# <a name="quickstart-face-client-library-for-go"></a>Quickstart: Encarar a biblioteca de clientes para Go

Começa com a biblioteca de clientes do Face para o Go. Siga estas etapas para instalar a biblioteca e experimentar nossos exemplos para tarefas básicas. O serviço de face fornece acesso a algoritmos avançados para detectar e reconhecer rostos humanos em imagens.

Utilize a biblioteca de clientes do serviço Face para ir:

* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Localizar faces semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar uma face](#identify-a-face)
* [Tirar um instantâneo para a migração de dados](#take-a-snapshot-for-data-migration)

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [código fonte da Biblioteca](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | download do [SDK](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão mais recente do [go](https://golang.org/dl/)

## <a name="set-up"></a>Configuração

### <a name="create-a-face-azure-resource"></a>Criar um recurso facial do Azure 

Comece a utilizar o serviço Face criando um recurso Azure. Escolha o tipo de recurso certo para si:

* Um [recurso experimental](https://azure.microsoft.com/try/cognitive-services/#decision) (não é necessária subscrição Azure): 
    * Válido por sete dias, gratuitamente. Depois de se inscrever, uma chave de avaliação e um ponto de extremidade estarão disponíveis no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Esta é uma ótima opção se quiser experimentar o serviço Face, mas não tem uma subscrição Azure.
* Um [recurso de serviço Face:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
    * Disponível por meio do portal do Azure até que você exclua o recurso.
    * Use o tipo de preço gratuito para experimentar o serviço e atualizar mais tarde para uma camada paga para produção.
* Um [recurso multi-serviço:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)
    * Disponível por meio do portal do Azure até que você exclua o recurso.  
    * Use a mesma chave e ponto de extremidade para seus aplicativos em vários serviços cognitivas.

### <a name="create-an-environment-variable"></a>Criar uma variável de ambiente

>[!NOTE]
> Os pontos de extremidade para recursos de não avaliação criados após 1º de julho de 2019 usam o formato de subdomínio personalizado mostrado abaixo. Para obter mais informações e uma lista completa de pontos de extremidade regionais, consulte [nomes de subdomínio personalizados para serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usando a chave e o ponto de extremidade do recurso que você criou, crie duas variáveis de ambiente para autenticação:
* `FACE_SUBSCRIPTION_KEY` - A chave de recursos para autenticar os seus pedidos.
* `FACE_ENDPOINT` - O ponto final do recurso para o envio de pedidos de API. Ele terá a seguinte aparência: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Use as instruções para seu sistema operacional.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

Depois de adicionar a variável de ambiente, reinicie a janela do console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite o seu `.bash_profile`e adicione a variável ambiental:

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.
***

### <a name="create-a-go-project-directory"></a>Criar um diretório do projeto Go

Em uma janela de console (cmd, PowerShell, terminal, bash), crie um novo espaço de trabalho para seu projeto Go, chamado `my-app`e navegue até ele.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Seu espaço de trabalho conterá três pastas:

* **src** -esse diretório conterá o código-fonte e os pacotes. Quaisquer pacotes instalados com o comando `go get` estarão nesta pasta.
* **pkg** -esse diretório conterá os objetos compilados do pacote go. Todos estes ficheiros têm uma extensão `.a`.
* **compartimento** -esse diretório conterá os arquivos executáveis binários que são criados quando você executa o `go install`.

> [!TIP]
> Para saber mais sobre a estrutura de um espaço de trabalho Go, consulte a [documentação do idioma go](https://golang.org/doc/code.html#Workspaces). Este guia inclui informações para definir `$GOPATH` e `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Instalar a biblioteca de cliente para o go

Em seguida, instale a biblioteca de cliente para o Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

ou, se você usar o Dep, na execução do seu repositório:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Criar um aplicativo go

Em seguida, crie um arquivo no diretório **src** chamado `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Abra `sample-app.go` em seu IDE ou editor de texto preferencial. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Em seguida, começará a adicionar código para realizar diferentes operações de serviço Face.

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas e interfaces lidam com algumas das principais características do serviço Face Go SDK.

|Nome|Descrição|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Essa classe representa sua autorização para usar o serviço de face e você precisa dela para todas as funcionalidades de face. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes. |
|[Cliente](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Essa classe manipula as tarefas básicas de detecção e reconhecimento que você pode fazer com as faces humanas. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Essa classe representa todos os dados que foram detectados de uma única face em uma imagem. Você pode usá-lo para recuperar informações detalhadas sobre a face.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Essa classe gerencia os constructos da **facelist** armazenados na nuvem, que armazenam um conjunto de faces classificado. |
|[PessoagroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Essa classe gerencia as construções de **pessoas** armazenadas na nuvem, que armazenam um conjunto de faces que pertencem a uma única pessoa.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Essa classe gerencia as construções de grupo de **pessoas** armazenadas na nuvem, que armazenam um conjunto de objetos de **pessoa** asclassificadas. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Essa classe gerencia a funcionalidade de instantâneo. Você pode usá-lo para salvar temporariamente todos os dados de face baseados em nuvem e migrar esses dados para uma nova assinatura do Azure. |

## <a name="code-examples"></a>Exemplos de código

Estas amostras de código mostram-lhe como completar tarefas básicas utilizando a biblioteca de clientes do serviço Face para Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Localizar faces semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar uma face](#identify-a-face)
* [Tirar um instantâneo para a migração de dados](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE] 
> Este quickstart assume que [criou variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a sua chave Face e ponto final, nomeado`FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT` respectivamente.

Crie uma função **principal** e adicione-lhe o seguinte código para instantaneamente um cliente com o seu ponto final e chave. Cria um objeto **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** com a sua chave e usa-o com o seu ponto final para criar um objeto **[Cliente.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** Este código também instantaneamente um objeto de contexto, que é necessário para a criação de objetos de cliente. Também define um local remoto onde algumas das imagens da amostra neste arranque rápido são encontradas.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

Adicione o seguinte código no seu método **principal.** Este código define uma imagem remota da amostra e especifica quais as características faciais a extrair da imagem. Especifica igualmente qual o modelo de IA a utilizar para extrair dados do ou dos rostos detetados. Consulte [especificar um modelo de reconhecimento](../Face-API-How-to-Topics/specify-recognition-model.md) para obter informações sobre essas opções. Por fim, o método **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** faz o funcionamento de deteção facial na imagem e guarda os resultados na memória do programa.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Exibir dados de face detectados

O próximo bloco de código leva o primeiro elemento na matriz de objetos **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** e imprime os seus atributos à consola. Se usou uma imagem com várias faces, deveria iterar através da matriz.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código a seguir usa uma única face detectada (origem) e pesquisa um conjunto de outras faces (destino) para encontrar correspondências. Quando ele encontra uma correspondência, ele imprime a ID da face correspondente no console.

### <a name="detect-faces-for-comparison"></a>Detectar faces para comparação

Em primeiro lugar, guarde uma referência ao rosto detetado nos [rostos detectantes numa](#detect-faces-in-an-image) secção de imagem. Este rosto será a fonte.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Em seguida, introduza o seguinte código para detetar um conjunto de rostos numa imagem diferente. Estes rostos serão o alvo.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Localizar correspondências

O código seguinte utiliza o método **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** para encontrar todos os rostos do alvo que correspondem à face da fonte.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Imprimir correspondências

O código que se segue imprime os detalhes da partida para a consola.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

Para percorrer esse cenário, você precisa salvar as seguintes imagens no diretório raiz do seu projeto: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Este grupo de imagens contém três conjuntos de imagens de rosto único que correspondem a três pessoas diferentes. O código definirá três objetos pessoas do **PersonGroup** e os associará a ficheiros de imagem que começam com `woman`, `man`e `child`.

### <a name="create-persongroup"></a>Criar um Person

Depois de ter descarregado as suas imagens, adicione o seguinte código na parte inferior do seu método **principal.** Este código autentica um objeto **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** e depois usa-o para definir um novo **PersonGroup**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Criar pessoas do PersonGroup

O próximo bloco de código autentica um **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** e utiliza-o para definir três novos objetos pessoas do **Grupo.** Estes objetos representam cada uma única pessoa no conjunto de imagens.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Atribuir faces a pessoas

O código seguinte classifica as imagens pelo seu prefixo, deteta rostos e atribui as faces a cada objeto pessoa **do Grupo** de Pessoas, com base no nome do ficheiro de imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Treinar o Person

Uma vez atribuídos rostos, treina o **PersonGroup** para que possa identificar as características visuais associadas a cada um dos seus objetos **Pessoais.** O código a seguir chama o método de **treinamento** assíncrono e sonda o resultado, imprimindo o status no console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Identificar uma face

O código a seguir usa uma imagem com várias faces e procura encontrar a identidade de cada pessoa na imagem. Ele compara cada face detectada a um **Person**, um banco de dados de objetos **Person** diferentes cujos recursos facial são conhecidos.

> [!IMPORTANT]
> Para executar este exemplo, você deve primeiro executar o código em [criar e treinar um grupo de pessoas](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Obter uma imagem de teste

O código seguinte está na raiz do seu projeto para um teste de _imagem-imagem-pessoa-grupo.jpg_ e carrega-o na memória do programa. Pode encontrar esta imagem no mesmo repo que as imagens utilizadas no [Create e treinar um grupo de pessoas](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Detetar rostos de origem na imagem de teste

O próximo bloco de código faz a deteção facial comum na imagem de teste para recuperar todos os rostos e guardá-los para uma matriz.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identificar rostos

O método **[Identificar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** pega na matriz de rostos detetados e compara-os com o dado **PersonGroup** (definido e treinado na secção anterior). Se conseguir combinar um rosto detetado com uma **pessoa** do grupo, poupa o resultado.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Este código então imprime resultados de correspondência detalhados para a consola.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Verificar faces

A operação de verificação usa uma ID de face e outra ID de face ou um objeto **Person** e determina se elas pertencem à mesma pessoa.

O código seguinte deteta rostos em duas imagens de origem e verifica cada um deles contra um rosto detetado a partir de uma imagem alvo.

### <a name="get-test-images"></a>Obter imagens de teste

Os seguintes blocos de código declaram variáveis que apontam para o alvo e imagens de origem para a operação de verificação.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Detectar faces para verificação

O código a seguir detecta rostos nas imagens de origem e de destino e as salva em variáveis.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Obter resultados da verificação

O código a seguir compara cada uma das imagens de origem com a imagem de destino e imprime uma mensagem indicando se elas pertencem à mesma pessoa.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Tirar um instantâneo para a migração de dados

O recurso de instantâneos permite que você mova os dados de rosto salvos, como um **usuário**treinado, para uma assinatura de face de serviços cognitivas do Azure diferente. Pode utilizar esta funcionalidade se, por exemplo, tiver criado um objeto **PersonGroup** usando uma subscrição de teste gratuito e agora quiser emigrar para uma subscrição paga. Consulte [migrar seus dados de rosto](../Face-API-How-to-Topics/how-to-migrate-face-data.md) para uma ampla visão geral do recurso de instantâneos.

Neste exemplo, você vai migrar o **PersonGroup** que criou em [Create e treinar um grupo de pessoas](#create-and-train-a-person-group). Você pode concluir essa seção primeiro ou usar suas próprias construções de dados de face.

### <a name="set-up-target-subscription"></a>Configurar assinatura de destino

Em primeiro lugar, deve ter uma segunda subscrição Azure com recurso Face; pode fazê-lo repetindo os passos na secção [Configurar.](#set-up) 

Em seguida, crie as seguintes variáveis perto do topo do seu método **principal.** Você também precisará criar novas variáveis de ambiente para a ID da assinatura da sua conta do Azure, bem como a chave, o ponto de extremidade e a ID da assinatura da sua nova conta (destino).

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Em seguida, coloque o seu valor de ID de subscrição numa matriz para os próximos passos.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Autenticar cliente de destino

Mais tarde no seu script, guarde o seu objeto de cliente original como cliente de origem e, em seguida, autenticar um novo objeto de cliente para a sua subscrição alvo. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Criar um Instantâneo

O próximo passo é tirar a foto com **[take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** , que guarda os dados faciais da sua subscrição original para uma localização temporária na nuvem. Esse método retorna uma ID que você usa para consultar o status da operação.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Em seguida, consulte a ID até que a operação seja concluída.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Aplicar o instantâneo

Utilize a operação **[Apply](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** para escrever os seus dados faciais recentemente carregados para a subscrição do alvo. Esse método também retorna uma ID.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Mais uma vez, consultar o ID até que a operação esteja concluída.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Uma vez concluídas estas etapas, pode aceder às construções de dados faciais a partir da sua nova subscrição (alvo).

## <a name="run-the-application"></a>Executar a aplicação

Execute a sua aplicação Go com o comando `go run [arguments]` do seu diretório de candidatura.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se criou um **PersonGroup** neste arranque rápido e quiser eliminá-lo, ligue para o método **[Eliminar.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** Se você migrou dados usando o recurso de instantâneo neste guia de início rápido, também precisará excluir o **usuário** salvo na assinatura de destino.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Face para ir fazer tarefas de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência face API (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [O que é o serviço de face?](../overview.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
