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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76941431"
---
# <a name="quickstart-face-client-library-for-go"></a>Quickstart: Encarar a biblioteca de clientes para Go

Começa com a biblioteca de clientes do Face para o Go. Siga estes passos para instalar a biblioteca e experimente os nossos exemplos para tarefas básicas. O serviço Face fornece-lhe acesso a algoritmos avançados para detetar e reconhecer rostos humanos em imagens.

Utilize a biblioteca de clientes do serviço Face para ir:

* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identifique um rosto](#identify-a-face)
* [Tire uma foto para a migração de dados](#take-a-snapshot-for-data-migration)

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [Biblioteca Código](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | fonte[SDK download](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão mais recente de [Go](https://golang.org/dl/)

## <a name="set-up"></a>Configurar

### <a name="create-a-face-azure-resource"></a>Criar um recurso Face Azure 

Comece a utilizar o serviço Face criando um recurso Azure. Escolha o tipo de recurso certo para si:

* Um [recurso experimental](https://azure.microsoft.com/try/cognitive-services/#decision) (não é necessária subscrição Azure): 
    * Válido por sete dias, de graça. Após a inscrição, uma chave de ensaio e ponto final estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/) 
    * Esta é uma ótima opção se quiser experimentar o serviço Face, mas não tem uma subscrição Azure.
* Um [recurso de serviço Face:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
    * Disponível através do portal Azure até eliminar o recurso.
    * Utilize o nível de preços gratuitos para experimentar o serviço e atualize mais tarde para um nível pago para produção.
* Um [recurso multi-serviço:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)
    * Disponível através do portal Azure até eliminar o recurso.  
    * Use a mesma chave e ponto final para as suas aplicações, através de vários Serviços Cognitivos.

### <a name="create-an-environment-variable"></a>Criar uma variável ambiental

>[!NOTE]
> Os pontos finais dos recursos não experimentais criados após 1 de julho de 2019 utilizam o formato de subdomínio personalizado mostrado abaixo. Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Utilizando a sua chave e ponto final a partir do recurso que criou, crie duas variáveis ambientais para autenticação:
* `FACE_SUBSCRIPTION_KEY`- A chave de recursos para autenticar os seus pedidos.
* `FACE_ENDPOINT`- O ponto final do recurso para o envio de pedidos de API. Vai ficar assim: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Utilize as instruções para o seu sistema operativo.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

Depois de adicionar a variável ambiente, reinicie a janela da consola.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macos"></a>[macOS](#tab/unix)

Edite `.bash_profile`a sua , e adicione a variável ambiental:

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.
***

### <a name="create-a-go-project-directory"></a>Criar um diretório de projeto Go

Numa janela de consola (cmd, PowerShell, Terminal, Bash), crie `my-app`um novo espaço de trabalho para o seu projeto Go, nomeado, e navegue até ele.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

O seu espaço de trabalho conterá três pastas:

* **sRC** - Este diretório conterá código fonte e pacotes. Quaisquer pacotes instalados com o `go get` comando estarão nesta pasta.
* **pkg** - Este diretório conterá os objetos de pacote Go compilados. Todos estes ficheiros têm uma `.a` extensão.
* **bin** - Este diretório conterá os ficheiros binários `go install`executáveis que são criados quando executa .

> [!TIP]
> Para saber mais sobre a estrutura de um espaço de trabalho Go, consulte a [documentação linguística Go](https://golang.org/doc/code.html#Workspaces). Este guia inclui `$GOPATH` informações para a definição e `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Instale a biblioteca de clientes para Go

Em seguida, instale a biblioteca de clientes para Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

ou se utilizar o dep, dentro da sua corrida de repo:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Criar uma aplicação Go

Em seguida, crie um ficheiro `sample-app.go`no diretório **src** chamado:

```bash
cd src
touch sample-app.go
```

Abra `sample-app.go` no seu IDE preferido ou editor de texto. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Em seguida, começará a adicionar código para realizar diferentes operações de serviço Face.

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas e interfaces lidam com algumas das principais características do serviço Face Go SDK.

|Nome|Descrição|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Esta classe representa a sua autorização para usar o serviço Face, e você precisa dele para todas as funcionalidades Face. Você instantaneamente com a sua informação de subscrição, e você usa-o para produzir casos de outras classes. |
|[Cliente](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Esta classe lida com as tarefas básicas de deteção e reconhecimento que você pode fazer com rostos humanos. |
|[Detetou Face](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Esta classe representa todos os dados que foram detetados a partir de um único rosto numa imagem. Pode usá-lo para obter informações detalhadas sobre o rosto.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Esta classe gere as construções **FaceList** armazenadas em nuvem, que armazenam um conjunto variado de rostos. |
|[PessoagroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Esta classe gere as construções da **Pessoa** armazenada na nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Esta classe gere as construções do **PersonGroup** armazenados em nuvem, que armazenam um conjunto de objetos **pessoas** variados. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Esta classe gere a funcionalidade Snapshot. Pode usá-lo para guardar temporariamente todos os seus dados face baseados na nuvem e migrar esses dados para uma nova subscrição do Azure. |

## <a name="code-examples"></a>Exemplos de código

Estas amostras de código mostram-lhe como completar tarefas básicas utilizando a biblioteca de clientes do serviço Face para Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identifique um rosto](#identify-a-face)
* [Tire uma foto para a migração de dados](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE] 
> Este quickstart assume que [criou variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a `FACE_SUBSCRIPTION_KEY` sua `FACE_ENDPOINT` chave Face e ponto final, nomeado e respectivamente.

Crie uma função **principal** e adicione-lhe o seguinte código para instantaneamente um cliente com o seu ponto final e chave. Cria um objeto **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** com a sua chave e usa-o com o seu ponto final para criar um objeto **[Cliente.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** Este código também instantaneamente um objeto de contexto, que é necessário para a criação de objetos de cliente. Também define um local remoto onde algumas das imagens da amostra neste arranque rápido são encontradas.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

Adicione o seguinte código no seu método **principal.** Este código define uma imagem remota da amostra e especifica quais as características faciais a extrair da imagem. Especifica igualmente qual o modelo de IA a utilizar para extrair dados do ou dos rostos detetados. Consulte [especificar um modelo de reconhecimento](../Face-API-How-to-Topics/specify-recognition-model.md) de informações sobre estas opções. Por fim, o método **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** faz o funcionamento de deteção facial na imagem e guarda os resultados na memória do programa.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Mostrar dados faciais detetados

O próximo bloco de código leva o primeiro elemento na matriz de objetos **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** e imprime os seus atributos à consola. Se usou uma imagem com várias faces, deveria iterar através da matriz.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código seguinte pega num único rosto detetado (fonte) e procura um conjunto de outras faces (alvo) para encontrar fósforos. Quando encontra uma correspondência, imprime a identificação da face compatível com a consola.

### <a name="detect-faces-for-comparison"></a>Detetar rostos para comparação

Em primeiro lugar, guarde uma referência ao rosto detetado nos [rostos detectantes numa](#detect-faces-in-an-image) secção de imagem. Este rosto será a fonte.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Em seguida, introduza o seguinte código para detetar um conjunto de rostos numa imagem diferente. Estes rostos serão o alvo.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Encontre fósforos

O código seguinte utiliza o método **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** para encontrar todos os rostos do alvo que correspondem à face da fonte.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Imprimir fósforos

O código que se segue imprime os detalhes da partida para a consola.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

Para ultrapassar este cenário, é necessário guardar as seguintes imagens https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagespara o diretório raiz do seu projeto: .

Este grupo de imagens contém três conjuntos de imagens de rosto único que correspondem a três pessoas diferentes. O código definirá três objetos pessoas do **PersonGroup** e os associará a ficheiros de imagem que começam com `woman` `man`, e `child`.

### <a name="create-persongroup"></a>Criar persongroup

Depois de ter descarregado as suas imagens, adicione o seguinte código na parte inferior do seu método **principal.** Este código autentica um objeto **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** e depois usa-o para definir um novo **PersonGroup**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Criar pessoas do PersonGroup

O próximo bloco de código autentica um **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** e utiliza-o para definir três novos objetos pessoas do **Grupo.** Estes objetos representam cada uma única pessoa no conjunto de imagens.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Atribuir rostos a pessoas

O código seguinte classifica as imagens pelo seu prefixo, deteta rostos e atribui as faces a cada objeto pessoa **do Grupo** de Pessoas, com base no nome do ficheiro de imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Grupo de Pessoas do Comboio

Uma vez atribuídos rostos, treina o **PersonGroup** para que possa identificar as características visuais associadas a cada um dos seus objetos **Pessoais.** O código seguinte chama o método do **comboio** assíncrono e vota o resultado, imprimindo o estado à consola.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Identifique um rosto

O código seguinte pega numa imagem com múltiplas faces e procura encontrar a identidade de cada pessoa na imagem. Compara cada rosto detetado a um **PersonGroup,** uma base de dados de diferentes objetos **da Pessoa** cujas características faciais são conhecidas.

> [!IMPORTANT]
> Para executar este exemplo, primeiro deve executar o código em [Create e treinar um grupo de pessoas](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Obtenha uma imagem de teste

O código seguinte está na raiz do seu projeto para um teste de _imagem-imagem-pessoa-grupo.jpg_ e carrega-o na memória do programa. Pode encontrar esta imagem no mesmo repo que as imagens utilizadas no [Create e treinar um grupo de pessoas:](#create-and-train-a-person-group) https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Detetar rostos de origem na imagem de teste

O próximo bloco de código faz a deteção facial comum na imagem de teste para recuperar todos os rostos e guardá-los para uma matriz.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identificar rostos

O método **[Identificar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** pega na matriz de rostos detetados e compara-os com o dado **PersonGroup** (definido e treinado na secção anterior). Se conseguir combinar um rosto detetado com uma **pessoa** do grupo, poupa o resultado.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Este código então imprime resultados de correspondência detalhados para a consola.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Verificar rostos

A operação Verificar tem um ID facial e outro id facial ou um objeto **de pessoa** e determina se pertencem à mesma pessoa.

O código seguinte deteta rostos em duas imagens de origem e verifica cada um deles contra um rosto detetado a partir de uma imagem alvo.

### <a name="get-test-images"></a>Obtenha imagens de teste

Os seguintes blocos de código declaram variáveis que apontam para o alvo e imagens de origem para a operação de verificação.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Detetar rostos para verificação

O código seguinte deteta rostos na origem e imagens-alvo e guarda-os em variáveis.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Obtenha resultados de verificação

O código seguinte compara cada uma das imagens de origem à imagem alvo e imprime uma mensagem indicando se pertencem à mesma pessoa.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Tire uma foto para a migração de dados

A funcionalidade Snapshots permite mover os dados do rosto guardados, como um **PersonGroup**treinado, para uma subscrição diferente do Face dos Serviços Cognitivos Azure. Pode utilizar esta funcionalidade se, por exemplo, tiver criado um objeto **PersonGroup** usando uma subscrição de teste gratuito e agora quiser emigrar para uma subscrição paga. Consulte os dados do [seu rosto e migrar](../Face-API-How-to-Topics/how-to-migrate-face-data.md) para obter uma visão geral ampla da funcionalidade Snapshots.

Neste exemplo, você vai migrar o **PersonGroup** que criou em [Create e treinar um grupo de pessoas](#create-and-train-a-person-group). Pode completar a secção primeiro, ou utilizar a sua própria construção de dados Face.

### <a name="set-up-target-subscription"></a>Configurar a subscrição do alvo

Em primeiro lugar, deve ter uma segunda subscrição Azure com recurso Face; pode fazê-lo repetindo os passos na secção [Configurar.](#set-up) 

Em seguida, crie as seguintes variáveis perto do topo do seu método **principal.** Também terá de criar novas variáveis ambientais para o ID de subscrição da sua conta Azure, bem como a chave, ponto final e id de subscrição da sua nova conta (target).

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Em seguida, coloque o seu valor de ID de subscrição numa matriz para os próximos passos.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Autenticar cliente-alvo

Mais tarde no seu script, guarde o seu objeto de cliente original como cliente de origem e, em seguida, autenticar um novo objeto de cliente para a sua subscrição alvo. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Criar um Instantâneo

O próximo passo é tirar a foto com **[take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)**, que guarda os dados faciais da sua subscrição original para uma localização temporária na nuvem. Este método devolve um ID que utiliza para consultar o estado da operação.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Em seguida, consultar o ID até que a operação esteja concluída.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Aplicar o instantâneo

Utilize a operação **[Apply](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** para escrever os seus dados faciais recentemente carregados para a subscrição do alvo. Este método também devolve uma identificação.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Mais uma vez, consultar o ID até que a operação esteja concluída.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Uma vez concluídas estas etapas, pode aceder às construções de dados faciais a partir da sua nova subscrição (alvo).

## <a name="run-the-application"></a>Executar a aplicação

Execute a sua `go run [arguments]` aplicação Go com o comando do seu diretório de candidatura.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se criou um **PersonGroup** neste arranque rápido e quiser eliminá-lo, ligue para o método **[Eliminar.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** Se migrar dados utilizando a funcionalidade Snapshot neste arranque rápido, também terá de eliminar o **PersonGroup** guardado para a subscrição do alvo.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Face para ir fazer tarefas de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência face API (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [O que é o serviço Face?](../overview.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
