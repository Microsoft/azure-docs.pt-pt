---
title: Face Python biblioteca cliente quickstart
description: Utilize a biblioteca cliente Face para Python detetar rostos, encontrar semelhantes (procura facial por imagem) e identificar rostos (pesquisa de reconhecimento facial).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/07/2020
ms.author: pafarley
ms.openlocfilehash: 587e702f5c74149542e2fffcf7891b7ea41f4202
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91859637"
---
Começa com o reconhecimento facial usando a biblioteca do cliente Face para Python. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. O serviço Face fornece-lhe acesso a algoritmos avançados para detetar e reconhecer rostos humanos em imagens.

Utilize a biblioteca do cliente Face para Python para:

* Detetar rostos numa imagem
* Encontrar rostos semelhantes
* Criar e treinar um grupo de pessoas
* Identificar um rosto
* Verificar rostos

[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face)  |  [Pacote (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/)  |  [Amostras](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)
* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" crie um recurso Face crie um recurso Face no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API face. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Depois de obter uma chave e ponto final, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e ponto final, nomeado `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` e, respectivamente.

## <a name="setting-up"></a>Configuração
 
### <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Crie um novo script Python &mdash; *quickstart-file.py,* por exemplo. Em seguida, abra-o no seu editor preferido ou IDE e importe as seguintes bibliotecas.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Em seguida, crie variáveis para o ponto final e chave Azure do seu recurso.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Se criou a variável ambiental depois de ter lançado a aplicação, terá de fechar e reabrir o editor, o IDE ou a shell que a executa para aceder à variável.

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Pode instalar a biblioteca do cliente com:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas e interfaces lidam com algumas das principais características da biblioteca cliente Face Python.

|Nome|Descrição|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Esta classe representa a sua autorização para usar o serviço Face, e precisa dele para toda a funcionalidade Face. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes. |
|[Operações faciais](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Esta classe lida com as tarefas básicas de deteção e reconhecimento que pode fazer com rostos humanos. |
|[Face detetado](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Esta classe representa todos os dados que foram detetados a partir de um único rosto numa imagem. Pode usá-lo para obter informações detalhadas sobre o rosto.|
|[Operações FaceList](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Esta classe gere as construções **FaceList** armazenadas em nuvem, que armazenam um conjunto variado de rostos. |
|[PersonGroupPersonOperações](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Esta classe gere as construções **de Pessoas** armazenadas em nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroupOperações](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Esta classe gere as construções **do PersonGroup** armazenadas na nuvem, que armazenam um conjunto de objetos **pessoais** variados. |
|[Operações Shapshot](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Esta classe gere a funcionalidade Snapshot; pode usá-lo para guardar temporariamente todos os seus dados faciais baseados na nuvem e migrar esses dados para uma nova subscrição do Azure. |

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca do cliente Face para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontre rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar um rosto](#identify-a-face)
* [Verificar rostos](#verify-faces)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart assume que [criou uma variável ambiental](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a sua chave Face, chamada `FACE_SUBSCRIPTION_KEY` .

Instantiar um cliente com o seu ponto final e chave. Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com a sua chave e use-o com o seu ponto final para criar um objeto [FaceClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

O seguinte código deteta um rosto numa imagem remota. Imprime o ID do rosto detetado na consola e também o armazena na memória do programa. Depois, deteta os rostos numa imagem com várias pessoas e imprime os seus IDs para a consola também. Ao alterar os parâmetros do método [detect_with_url,](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) pode retornar informações diferentes com cada objeto [DetectedFace.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Consulte o código de amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) para mais cenários de deteção.

### <a name="display-and-frame-faces"></a>Rostos de exibição e moldura

O código seguinte produz a imagem dada para o visor e desenha retângulos em torno dos rostos, utilizando a propriedade DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Uma jovem mulher com um retângulo vermelho desenhado ao redor do rosto](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código seguinte requer um único rosto detetado (fonte) e procura um conjunto de outras faces (alvo) para encontrar fósforos (procura facial por imagem). Quando encontra uma correspondência, imprime o ID da face compatível com a consola.

### <a name="find-matches"></a>Encontrar fósforos

Em primeiro lugar, executar o código na secção acima[(Detetar rostos numa imagem)](#detect-faces-in-an-image)para guardar uma referência a um único rosto. Em seguida, executar o seguinte código para obter referências a várias faces em uma imagem de grupo.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Em seguida, adicione o seguinte bloco de código para encontrar as instâncias da primeira face no grupo. Consulte o método [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) para aprender a modificar este comportamento.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Imprimir fósforos

Utilize o seguinte código para imprimir os detalhes da correspondência na consola.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

O código a seguir cria um **PersonGroup** com três objetos **Pessoais** diferentes. Associa cada **Pessoa a** um conjunto de imagens de exemplo, e depois treina para ser capaz de reconhecer cada pessoa. 

### <a name="create-persongroup"></a>Criar Grupo de Pessoas

Para passar por este cenário, é necessário guardar as seguintes imagens para o diretório de raiz do seu projeto: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

Este grupo de imagens contém três conjuntos de imagens faciais correspondentes a três pessoas diferentes. O código definirá três objetos **pessoais** e associar-os-á a ficheiros de imagem que começam com `woman` , e `man` `child` .

Uma vez configuradas as suas imagens, defina uma etiqueta no topo do seu script para o objeto **PersonGroup** que irá criar.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Em seguida, adicione o seguinte código na parte inferior do seu script. Este código cria um **PersonGroup** e três objetos **pessoais.**

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Atribuir rostos a Pessoas

O código seguinte classifica as suas imagens pelo seu prefixo, deteta rostos e atribui os rostos a cada objeto **Pessoa.**

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Grupo de Pessoas do Comboio

Uma vez atribuídos rostos, deve treinar o **PersonGroup** para que possa identificar as características visuais associadas a cada um dos seus objetos **Pessoais.** O código a seguir chama o método do **comboio** assíncronos e sonda o resultado, imprimindo o estado à consola.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identificar um rosto

A operação Identificar tira uma imagem de uma pessoa (ou de várias pessoas) e procura encontrar a identidade de cada rosto na imagem (pesquisa de reconhecimento facial). Compara cada rosto detetado a um **PersonGroup,** uma base de dados de diferentes objetos **pessoais** cujas características faciais são conhecidas.

> [!IMPORTANT]
> Para executar este exemplo, primeiro deve executar o código em [Criar e treinar um grupo de pessoas.](#create-and-train-a-person-group)

### <a name="get-a-test-image"></a>Obtenha uma imagem de teste

O seguinte código olha na raiz do seu projeto para uma _ imagemtest-image-person-group.jpg_ e deteta os rostos na imagem. Pode encontrar esta imagem com as imagens utilizadas para a gestão **do PersonGroup:** https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identificar rostos

O método **de identificação** leva uma série de rostos detetados e compara-os a um **PersonGroup**. Se conseguir combinar um rosto detetado com uma **Pessoa,** salva o resultado. Este código imprime resultados detalhados da correspondência para a consola.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Verificar rostos

A operação Verificar requer um ID facial e outro ID facial ou um objeto **de Pessoa** e determina se pertencem à mesma pessoa.

O seguinte código deteta rostos em duas imagens de origem e, em seguida, verifica-os contra um rosto detetado a partir de uma imagem do alvo.

### <a name="get-test-images"></a>Obtenha imagens de teste

Os blocos de código que se seguem declaram variáveis que apontam para a origem e imagens-alvo para a operação de verificação.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Detetar rostos para verificação

O código seguinte deteta rostos na origem e imagens-alvo e guarda-os para variáveis.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Obtenha resultados de verificação

O código seguinte compara cada uma das imagens de origem com a imagem do alvo e imprime uma mensagem indicando se pertencem à mesma pessoa.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a sua aplicação de reconhecimento facial a partir do diretório de aplicações com o `python` comando.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Se criou um **PersonGroup** neste arranque rápido e pretende eliminá-lo, execute o seguinte código no seu script:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca de clientes Face para Python para fazer tarefas de reconhecimento facial de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência API face (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [O que é o serviço Face?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
