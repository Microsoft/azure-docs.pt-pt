---
title: 'Quickstart: Encarar a biblioteca de clientes da Python'
description: Este quickstart irá ajudá-lo a começar com a biblioteca de clientes Face para Python detetar, encontrar similares, identificar, verificar e muito mais.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 7ac1b85ee44627990931cfc7b3a65f6f7a661b3f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76165821"
---
# <a name="quickstart-face-client-library-for-python"></a>Quickstart: Encarar a biblioteca de clientes da Python

Começa com a biblioteca de clientes da Face para python. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. O serviço Face fornece-lhe acesso a algoritmos avançados para detetar e reconhecer rostos humanos em imagens.

Use a biblioteca de clientes Face para Python para:

* Detetar rostos numa imagem
* Encontrar rostos semelhantes
* Criar e treinar um grupo de pessoas
* Identifique um rosto
* Verificar rostos
* Tire uma foto para a migração de dados

[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | Pacote[de código fonte](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | da biblioteca[(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [Samples](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configuração

### <a name="create-a-face-azure-resource"></a>Criar um recurso Face Azure

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para face utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias de graça. Depois de se inscrever, estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Veja o seu recurso no [portal Azure](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso `FACE_SUBSCRIPTION_KEY`experimental, [crie uma variável ambiental](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, chamada .
 
### <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Crie um&mdash;novo roteiro Python*quickstart-file.py,* por exemplo. Em seguida, abra-a no seu editor preferido ou IDE e importe as seguintes bibliotecas.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Em seguida, crie variáveis para o ponto final e chave Azure do seu recurso. Pode ser necessário alterar a primeira parte`westus`do ponto final para corresponder à sua subscrição.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE, ou a shell executá-la para aceder à variável.

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Pode instalar a biblioteca do cliente com:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas e interfaces lidam com algumas das principais características do Face Python SDK.

|Nome|Descrição|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Esta classe representa a sua autorização para usar o serviço Face, e você precisa dele para todas as funcionalidades Face. Você instantaneamente com a sua informação de subscrição, e você usa-o para produzir casos de outras classes. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Esta classe lida com as tarefas básicas de deteção e reconhecimento que você pode fazer com rostos humanos. |
|[Detetou Face](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Esta classe representa todos os dados que foram detetados a partir de um único rosto numa imagem. Pode usá-lo para obter informações detalhadas sobre o rosto.|
|[FacelistOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Esta classe gere as construções **FaceList** armazenadas em nuvem, que armazenam um conjunto variado de rostos. |
|[Operações pessoais](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Esta classe gere as construções da **Pessoa** armazenada na nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[Operações pessoais](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Esta classe gere as construções do **PersonGroup** armazenados em nuvem, que armazenam um conjunto de objetos **pessoas** variados. |
|[Operações Shapshot](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Esta classe gere a funcionalidade Snapshot; pode usá-lo para guardar temporariamente todos os seus dados faciais baseados na nuvem e migrar esses dados para uma nova subscrição do Azure. |

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Face para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Detetar rostos numa imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identifique um rosto](#identify-a-face)
* [Verificar rostos](#verify-faces)
* [Tire uma foto para a migração de dados](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart assume que [criaste uma variável](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) ambiental `FACE_SUBSCRIPTION_KEY`para a tua chave Face, chamada .

Instantifique um cliente com o seu ponto final e chave. Crie um objeto [CognitiveServicesCredenciais](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com a sua chave e use-o com o seu ponto final para criar um objeto [FaceClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

O seguinte código deteta um rosto numa imagem remota. Imprime o ID do rosto detetado para a consola e também armazena-o na memória do programa. Depois, deteta os rostos numa imagem com várias pessoas e imprime também as suas iDs para a consola. Alterando os parâmetros no método [detect_with_url,](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) pode devolver informações diferentes a cada objeto [DetectedFace.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Consulte o código da amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) para obter mais cenários de deteção.

### <a name="display-and-frame-faces"></a>Rostos de exibição e moldura

O código seguinte produz a imagem dada para o visor e desenha retângulos em torno dos rostos, utilizando a propriedade DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Uma jovem mulher com um retângulo vermelho desenhado ao redor do rosto](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código seguinte pega num único rosto detetado e procura um conjunto de outros rostos para encontrar fósforos. Quando encontra uma correspondência, imprime as coordenadas de retângulo da face compatível com a consola. 

### <a name="find-matches"></a>Encontre fósforos

Primeiro, execute o código na secção acima[(Detete os rostos numa imagem](#detect-faces-in-an-image)) para guardar uma referência a um único rosto. Em seguida, executar o seguinte código para obter referências a várias faces em uma imagem de grupo.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Em seguida, adicione o seguinte bloco de código para encontrar instâncias da primeira face no grupo. Consulte o método [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) para aprender a modificar este comportamento.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Imprimir fósforos

Utilize o seguinte código para imprimir os dados da partida à consola.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

O seguinte código cria um **PersonGroup** com três objetos **pessoadiferentes.** Associa cada **Pessoa** a um conjunto de imagens de exemplo, e depois treina para ser capaz de reconhecer cada pessoa. 

### <a name="create-persongroup"></a>Criar persongroup

Para ultrapassar este cenário, é necessário guardar as seguintes imagens https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagespara o diretório raiz do seu projeto: .

Este grupo de imagens contém três conjuntos de imagens faciais correspondentes a três pessoas diferentes. O código definirá três objetos **pessoais** e `woman` `man`associá-los-á a ficheiros de imagem que começam com , e `child`.

Depois de configurar as suas imagens, defina um rótulo no topo do seu script para o objeto **PersonGroup** que irá criar.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Em seguida, adicione o seguinte código na parte inferior do seu script. Este código cria um **PersonGroup** e três objetos **pessoais.**

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Atribuir rostos a pessoas

O código seguinte classifica as suas imagens pelo seu prefixo, deteta rostos e atribui os rostos a cada objeto **da Pessoa.**

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Grupo de Pessoas do Comboio

Uma vez atribuídos rostos, deve treinar o **PersonGroup** para que possa identificar as características visuais associadas a cada um dos seus objetos **Pessoais.** O código seguinte chama o método do **comboio** assíncrono e vota o resultado, imprimindo o estado à consola.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identifique um rosto

O código seguinte pega numa imagem com múltiplas faces e procura encontrar a identidade de cada pessoa na imagem. Compara cada rosto detetado a um **PersonGroup,** uma base de dados de diferentes objetos **da Pessoa** cujas características faciais são conhecidas.

> [!IMPORTANT]
> Para executar este exemplo, primeiro deve executar o código em [Create e treinar um grupo de pessoas](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Obtenha uma imagem de teste

O código seguinte está na raiz do seu projeto para um teste de _imagem-imagem-grupo.jpg_ e deteta os rostos na imagem. Pode encontrar esta imagem com as imagens https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesutilizadas para a gestão do **PersonGroup:** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identificar rostos

O método **de identificação** pega num conjunto de rostos detetados e compara-os a um **PersonGroup**. Se conseguir combinar um rosto detetado com uma **Pessoa,** poupa o resultado. Este código imprime os resultados de correspondência detalhados para a consola.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Verificar rostos

A operação Verificar tem um ID facial e outro id facial ou um objeto **de pessoa** e determina se pertencem à mesma pessoa.

O código seguinte deteta rostos em duas imagens de origem e verifica-os depois contra um rosto detetado a partir de uma imagem de alvo.

### <a name="get-test-images"></a>Obtenha imagens de teste

Os seguintes blocos de código declaram variáveis que apontam para a origem e imagens-alvo para a operação de verificação.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Detetar rostos para verificação

O código seguinte deteta rostos na origem e imagens-alvo e guarda-os em variáveis.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Obtenha resultados de verificação

O código seguinte compara cada uma das imagens de origem à imagem alvo e imprime uma mensagem indicando se pertencem à mesma pessoa.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Tire uma foto para a migração de dados

A funcionalidade Snapshots permite mover os dados do rosto guardados, como um **PersonGroup**treinado, para uma subscrição diferente do Face dos Serviços Cognitivos Azure. Pode querer utilizar esta funcionalidade se, por exemplo, tiver criado um objeto **PersonGroup** usando uma subscrição de teste gratuito e agora quiser emigrar para uma subscrição paga. Consulte os dados do [seu rosto e migrar](../Face-API-How-to-Topics/how-to-migrate-face-data.md) para obter uma visão geral ampla da funcionalidade Snapshots.

Neste exemplo, irá migrar o **PersonGroup** que criou na [Create e formar um grupo de pessoas.](#create-and-train-a-person-group) Pode completar a secção primeiro, ou utilizar a sua própria construção de dados Face.

### <a name="set-up-target-subscription"></a>Configurar a subscrição do alvo

Em primeiro lugar, deve ter uma segunda subscrição Azure com recurso Face; pode fazê-lo seguindo os passos na secção [Configuração.](#setting-up) 

Em seguida, crie as seguintes variáveis perto do topo do seu script. Também terá de criar novas variáveis ambientais para o ID de subscrição da sua conta Azure, bem como a chave, ponto final e id de subscrição da sua nova conta (target). 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Autenticar cliente-alvo

Mais tarde no seu script, guarde o seu objeto de cliente atual como cliente de origem e, em seguida, autenticar um novo objeto de cliente para a sua subscrição alvo. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Use uma foto

As restantes operações instantâneas ocorrem dentro de uma função assíncrona. 

1. O primeiro passo é **tirar** o instantâneo, que guarda os dados faciais da sua subscrição original para uma localização temporária na nuvem. Este método devolve um ID que utiliza para consultar o estado da operação.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Em seguida, consultar o ID até que a operação esteja concluída.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Este código utiliza `wait_for_operation` a função, que deve definir separadamente:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Volte para a sua função assíncrona. Utilize a operação **de aplicação** para escrever os dados do seu rosto na subscrição do alvo. Este método também devolve uma identificação.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Mais uma `wait_for_operation` vez, utilize a função para consultar o ID até que a operação esteja concluída.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Assim que tiver concluído estes passos, poderá aceder às construções de dados do seu rosto a partir da sua nova subscrição (alvo).

## <a name="run-the-application"></a>Executar a aplicação

Execute o `python` pedido com o comando no seu ficheiro de arranque rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Se criou um **PersonGroup** neste arranque rápido e pretende eliminá-lo, execute o seguinte código no seu script:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Se migrar dados utilizando a funcionalidade Snapshot neste arranque rápido, também terá de eliminar o **PersonGroup** guardado para a subscrição do alvo.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Face para python para fazer tarefas de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência face API (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [O que é o serviço Face?](../overview.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
