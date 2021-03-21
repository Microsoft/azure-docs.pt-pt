---
title: Início rápido da biblioteca de clientes Face Go
description: Utilize a biblioteca do cliente Face para ir detetar rostos, encontrar semelhantes (procura facial por imagem), identificar rostos (pesquisa de reconhecimento facial) e migrar os dados do seu rosto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 2c693c9e972cdfc66cce0d52323c113b6006bd97
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102445215"
---
Começa com o reconhecimento facial usando a biblioteca do cliente Face para Go. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. O serviço Face fornece-lhe acesso a algoritmos avançados para detetar e reconhecer rostos humanos em imagens.

Utilize a biblioteca do cliente de serviço Face para ir a:

* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontre rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar um rosto](#identify-a-face)

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face)  |  [Download SDK](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Pré-requisitos

* A última versão de [Go](https://golang.org/dl/)
* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" crie um recurso Face crie um recurso Face no portal "  target="_blank"> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API face. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Depois de obter uma chave e ponto final, [crie variáveis ambientais](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a chave e ponto final, nomeado `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` e, respectivamente.

## <a name="setting-up"></a>Configuração

### <a name="create-a-go-project-directory"></a>Criar um diretório de projeto go

Numa janela de consola (cmd, PowerShell, Terminal, Bash), crie um novo espaço de trabalho para o seu projeto Go, nomeado `my-app` , e navegue até ele.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

O seu espaço de trabalho conterá três pastas:

* **src** - Este diretório conterá código fonte e pacotes. Quaisquer pacotes instalados com o `go get` comando estarão nesta pasta.
* **pkg** - Este diretório conterá os objetos de embalagem Go compilados. Todos estes ficheiros têm uma `.a` extensão.
* **bin** - Este diretório conterá os ficheiros binários executáveis que são criados quando executa `go install` .

> [!TIP]
> Para saber mais sobre a estrutura de um espaço de trabalho Go, consulte a [documentação da língua Go.](https://golang.org/doc/code.html#Workspaces) Este guia inclui informações para a definição `$GOPATH` e `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Instale a biblioteca do cliente para Go

Em seguida, instale a biblioteca do cliente para Go:

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

ou se utilizar o dep, dentro da sua corrida de repo:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Criar uma aplicação Go

Em seguida, crie um ficheiro no **diretório src** chamado `sample-app.go` :

```bash
cd src
touch sample-app.go
```

Abra `sample-app.go` no seu IDE preferido ou editor de texto. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Em seguida, começará a adicionar código para realizar diferentes operações de serviço Face.

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca de clientes do serviço Face Go.

|Nome|Descrição|
|---|---|
|[BaseCiente](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Esta classe representa a sua autorização para usar o serviço Face, e precisa dele para toda a funcionalidade Face. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes. |
|[Cliente](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Esta classe lida com as tarefas básicas de deteção e reconhecimento que pode fazer com rostos humanos. |
|[Face detetado](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Esta classe representa todos os dados que foram detetados a partir de um único rosto numa imagem. Pode usá-lo para obter informações detalhadas sobre o rosto.|
|[ListCiente](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Esta classe gere as construções **FaceList** armazenadas em nuvem, que armazenam um conjunto variado de rostos. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Esta classe gere as construções **de Pessoas** armazenadas em nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Esta classe gere as construções **do PersonGroup** armazenadas na nuvem, que armazenam um conjunto de objetos **pessoais** variados. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Esta classe gere a funcionalidade Snapshot. Pode usá-lo para guardar temporariamente todos os seus dados face baseados na nuvem e migrar esses dados para uma nova subscrição do Azure. |

## <a name="code-examples"></a>Exemplos de código

Estas amostras de código mostram-lhe como completar tarefas básicas utilizando a biblioteca do cliente de serviço Face para Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontre rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar um rosto](#identify-a-face)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE] 
> Este quickstart pressupõe que [criou variáveis ambientais](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a sua chave Face e ponto final, nomeado `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT` respectivamente.

Crie uma função **principal** e adicione-lhe o seguinte código para instantaneaizar um cliente com o seu ponto final e chave. Cria um objeto **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** com a sua chave e utiliza-o com o seu ponto final para criar um objeto **[Cliente.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** Este código também instantaneamente um objeto de contexto, que é necessário para a criação de objetos do cliente. Também define um local remoto onde algumas das imagens da amostra neste quickstart são encontradas.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

Adicione o seguinte código no seu método **principal.** Este código define uma imagem de amostra remota e especifica quais as características do rosto a extrair da imagem. Especifica igualmente qual o modelo de IA a utilizar para extrair dados do ou dos rostos detetados. Consulte [Especificar um modelo de reconhecimento](../../Face-API-How-to-Topics/specify-recognition-model.md) para obter informações sobre estas opções. Finalmente, o método **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** faz a operação de deteção facial na imagem e guarda os resultados na memória do programa.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

> [!TIP]
> Também pode detetar rostos numa imagem local. Consulte os métodos do [Cliente,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client) tais como **DetectWithStream.**

### <a name="display-detected-face-data"></a>Mostrar dados faciais detetados

O próximo bloco de código pega no primeiro elemento na matriz de objetos **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** e imprime os seus atributos à consola. Se usou uma imagem com múltiplas faces, em vez disso deve iterar através da matriz.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código seguinte requer um único rosto detetado (fonte) e procura um conjunto de outras faces (alvo) para encontrar fósforos (procura facial por imagem). Quando encontra uma correspondência, imprime o ID da face compatível com a consola.

### <a name="detect-faces-for-comparison"></a>Detetar rostos para comparação

Em primeiro lugar, guarde uma referência ao rosto detetado nas [faces de Deteção numa](#detect-faces-in-an-image) secção de imagem. Este rosto será a fonte.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Em seguida, introduza o seguinte código para detetar um conjunto de rostos numa imagem diferente. Estes rostos serão o alvo.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Encontrar fósforos

O código seguinte utiliza o método **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** para encontrar todas as faces do alvo que correspondam à face da fonte.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Imprimir fósforos

O código seguinte imprime os detalhes da correspondência para a consola.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

Para passar por este cenário, é necessário guardar as seguintes imagens para o diretório de raiz do seu projeto: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

Este grupo de imagens contém três conjuntos de imagens de um só rosto que correspondem a três pessoas diferentes. O código definirá três objetos **persongroup person person** e associá-los-á a ficheiros de imagem que começam com `woman` , e `man` `child` .

### <a name="create-persongroup"></a>Criar Grupo de Pessoas

Depois de descarregar as suas imagens, adicione o seguinte código ao fundo do seu método **principal.** Este código autentica um objeto **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** e, em seguida, utiliza-o para definir um novo **PersonGroup**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Criar pessoas do Grupo de Pessoas

O próximo bloco de código autentica um **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** e usa-o para definir três novos objetos **persongroup person person** group. Estes objetos representam cada uma pessoa no conjunto de imagens.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Atribuir rostos a Pessoas

O código a seguir classifica as imagens pelo seu prefixo, deteta rostos e atribui os rostos a cada objeto **persongroup PersonGroup Person,** com base no nome do ficheiro de imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

> [!TIP]
> Também pode criar um **PersonGroup a** partir de imagens remotas referenciadas por URL. Consulte os [métodos PersonGroupPersonClient,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient) tais como **AddFaceFromURL**.

### <a name="train-persongroup"></a>Grupo de Pessoas do Comboio

Uma vez atribuídos rostos, treina o **PersonGroup** para que possa identificar as características visuais associadas a cada um dos seus objetos **Pessoais.** O código a seguir chama o método do **comboio** assíncronos e sonda o resultado, imprimindo o estado à consola.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

> [!TIP]
> A API face funciona num conjunto de modelos pré-construídos que são estáticos por natureza (o desempenho do modelo não vai regredir ou melhorar à medida que o serviço é executado). Os resultados que o modelo produz podem mudar se a Microsoft atualizar o backend do modelo sem migrar para uma versão totalmente nova do modelo. Para tirar partido de uma versão mais recente de um modelo, pode reforçá-lo **como** parâmetro com as mesmas imagens de inscrição.

## <a name="identify-a-face"></a>Identificar um rosto

A operação Identificar tira uma imagem de uma pessoa (ou de várias pessoas) e procura encontrar a identidade de cada rosto na imagem (pesquisa de reconhecimento facial). Compara cada rosto detetado a um **PersonGroup,** uma base de dados de diferentes objetos **pessoais** cujas características faciais são conhecidas.

> [!IMPORTANT]
> Para executar este exemplo, primeiro deve executar o código em [Criar e treinar um grupo de pessoas.](#create-and-train-a-person-group)

### <a name="get-a-test-image"></a>Obtenha uma imagem de teste

O código que se segue olha na raiz do seu projeto para uma imagem _test-image-person-group.jpg_ e carrega-o na memória do programa. Pode encontrar esta imagem no mesmo repo que as imagens utilizadas na [Create e treinar um grupo de pessoas:](#create-and-train-a-person-group) https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Detetar rostos de origem na imagem de teste

O próximo bloco de código faz a deteção facial comum na imagem de teste para recuperar todos os rostos e guardá-los para uma matriz.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identificar rostos

O método **[Identificar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** pega na matriz de rostos detetados e compara-os com o **Grupo de Pessoas** (definido e treinado na secção anterior). Se conseguir combinar um rosto detetado com uma **pessoa** do grupo, guarda o resultado.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Este código imprime então resultados de correspondência detalhados para a consola.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Verificar rostos

A operação Verificar requer um ID facial e outro ID facial ou um objeto **de Pessoa** e determina se pertencem à mesma pessoa.

O código seguinte deteta rostos em duas imagens de origem e, em seguida, verifica cada uma delas contra um rosto detetado a partir de uma imagem do alvo.

### <a name="get-test-images"></a>Obtenha imagens de teste

Os blocos de código que se seguem declaram variáveis que apontam para o alvo e imagens de origem para a operação de verificação.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Detetar rostos para verificação

O código seguinte deteta rostos na origem e imagens-alvo e guarda-os para variáveis.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Obtenha resultados de verificação

O código seguinte compara cada uma das imagens de origem com a imagem do alvo e imprime uma mensagem indicando se pertencem à mesma pessoa.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a sua aplicação de reconhecimento facial a partir do diretório de aplicações com o `go run <app-name>` comando.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se criou um **PersonGroup** neste arranque rápido e pretende eliminá-lo, ligue para o método **[Eliminar.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)**

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca de clientes Face para ir para fazer tarefas de reconhecimento facial de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência API face (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [O que é o serviço Face?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).