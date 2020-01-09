---
title: 'Início rápido: biblioteca de cliente facial para Python'
description: Este guia de início rápido ajudará você a começar a usar a biblioteca de cliente facial para Python a fim de detectar, localizar semelhante, identificar, verificar e muito mais.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: e41817e3409f79aab3fbb3d57195b6b7ff13e7af
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448229"
---
# <a name="quickstart-face-client-library-for-python"></a>Início rápido: biblioteca de cliente facial para Python

Introdução à biblioteca de cliente facial para Python. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O serviço de API de Detecção Facial fornece acesso a algoritmos avançados para detectar e reconhecer rostos humanos em imagens.

Use a biblioteca de cliente facial para Python para:

* Detetar rostos numa imagem
* Encontrar rostos semelhantes
* Criar e treinar um grupo de pessoas
* Identificar uma face
* Verificar faces
* Tirar um instantâneo para a migração de dados

[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) |  | [(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [amostras](https://docs.microsoft.com/samples/browse/?products=azure&term=face) do [código-fonte de biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-face-azure-resource"></a>Criar um recurso facial do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para a face usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exibir seu recurso no [portal do Azure](https://portal.azure.com/)

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave chamada `FACE_SUBSCRIPTION_KEY`.
 
### <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Crie um novo script Python&mdash;*QuickStart-File.py*, por exemplo. Em seguida, abra-o em seu editor preferido ou IDE e importe as bibliotecas a seguir.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Em seguida, crie variáveis para o ponto de extremidade e a chave do Azure do recurso. Talvez seja necessário alterar a primeira parte do ponto de extremidade (`westus`) para corresponder à sua assinatura.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável.

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

Você pode instalar a biblioteca de cliente com:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir tratam de alguns dos principais recursos do SDK do Python facial.

|Nome|Descrição|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Essa classe representa sua autorização para usar o serviço de face e você precisa dela para todas as funcionalidades de face. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Essa classe manipula as tarefas básicas de detecção e reconhecimento que você pode fazer com as faces humanas. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Essa classe representa todos os dados que foram detectados de uma única face em uma imagem. Você pode usá-lo para recuperar informações detalhadas sobre a face.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Essa classe gerencia os constructos da **facelist** armazenados na nuvem, que armazenam um conjunto de faces classificado. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Essa classe gerencia as construções de **pessoas** armazenadas na nuvem, que armazenam um conjunto de faces que pertencem a uma única pessoa.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Essa classe gerencia as construções de grupo de **pessoas** armazenadas na nuvem, que armazenam um conjunto de objetos de **pessoa** asclassificadas. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Essa classe gerencia a funcionalidade de instantâneo; Você pode usá-lo para salvar temporariamente todos os dados de face baseados em nuvem e migrar esses dados para uma nova assinatura do Azure. |

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente facial para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Localizar faces semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar uma face](#identify-a-face)
* [Verificar faces](#verify-faces)
* [Tirar um instantâneo para a migração de dados](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este início rápido pressupõe que você [criou uma variável de ambiente](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para sua chave de face, chamada `FACE_SUBSCRIPTION_KEY`.

Crie uma instância de um cliente com o ponto de extremidade e a chave. Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com sua chave e use-o com seu ponto de extremidade para criar um objeto [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

O código a seguir detecta uma face em uma imagem remota. Ele imprime a ID da face detectada no console e também a armazena na memória do programa. Em seguida, ele detecta as faces em uma imagem com várias pessoas e imprime suas IDs no console também. Ao alterar os parâmetros no método [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) , você pode retornar informações diferentes com cada objeto [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Consulte o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) para obter mais cenários de detecção.

### <a name="display-and-frame-faces"></a>Exibição e rostos de quadro

O código a seguir gera a imagem fornecida para a exibição e desenha retângulos em volta das faces, usando a propriedade DetectedFace. faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Uma moça com um retângulo vermelho desenhada em todo o Mostrador](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código a seguir usa uma única face detectada e pesquisa um conjunto de outras faces para encontrar correspondências. Quando ele encontra uma correspondência, ele imprime as coordenadas de retângulo da face correspondente para o console. 

### <a name="find-matches"></a>Localizar correspondências

Primeiro, execute o código na seção acima ([detectar faces em uma imagem](#detect-faces-in-an-image)) para salvar uma referência a uma única face. Em seguida, execute o código a seguir para obter referências a várias faces em uma imagem de grupo.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Em seguida, adicione o seguinte bloco de código para localizar instâncias da primeira face no grupo. Consulte o método [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) para saber como modificar esse comportamento.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Imprimir correspondências

Use o código a seguir para imprimir os detalhes de correspondência no console do.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

O código a seguir cria um **Person** com três objetos **Person** diferentes. Ele associa cada **pessoa** a um conjunto de imagens de exemplo e, em seguida, treina para ser capaz de reconhecer cada pessoa. 

### <a name="create-persongroup"></a>Criar um Person

Para percorrer esse cenário, você precisa salvar as seguintes imagens no diretório raiz do seu projeto: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Esse grupo de imagens contém três conjuntos de imagens de face correspondentes a três pessoas diferentes. O código definirá três objetos **Person** e os associará a arquivos de imagem que começam com `woman`, `man`e `child`.

Depois de configurar suas imagens, defina um rótulo na parte superior do script para o objeto **Person** que você criará.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Em seguida, adicione o código a seguir à parte inferior do seu script. Esse código cria um objeto de **radiosong** e três objetos **Person** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Atribuir faces a pessoas

O código a seguir classifica suas imagens por seu prefixo, detecta faces e atribui as faces a cada objeto **Person** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Treinar o Person

Depois de atribuir faces, você deve treinar o **Person** para que ele possa identificar os recursos visuais associados a cada um de seus objetos **Person** . O código a seguir chama o método de **treinamento** assíncrono e sonda o resultado, imprimindo o status no console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identificar uma face

O código a seguir usa uma imagem com várias faces e procura encontrar a identidade de cada pessoa na imagem. Ele compara cada face detectada a um **Person**, um banco de dados de objetos **Person** diferentes cujos recursos facial são conhecidos.

> [!IMPORTANT]
> Para executar este exemplo, você deve primeiro executar o código em [criar e treinar um grupo de pessoas](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Obter uma imagem de teste

O código a seguir procura na raiz do seu projeto uma imagem _Test-Image-Person-Group. jpg_ e detecta as faces na imagem. Você pode encontrar essa imagem com as imagens usadas para o gerenciamento do **Person** : https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identificar rostos

O método **Identify** usa uma matriz de faces detectadas e as compara a um **Person**. Se ele puder corresponder a uma face detectada a uma **pessoa**, ele salvará o resultado. Esse código imprime os resultados de correspondência detalhados para o console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Verificar faces

A operação de verificação usa uma ID de face e outra ID de face ou um objeto **Person** e determina se elas pertencem à mesma pessoa.

O código a seguir detecta rostos em duas imagens de origem e as verifica em relação a uma face detectada a partir de uma imagem de destino.

### <a name="get-test-images"></a>Obter imagens de teste

Os blocos de código a seguir declaram variáveis que apontarão para as imagens de origem e de destino da operação de verificação.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Detectar faces para verificação

O código a seguir detecta rostos nas imagens de origem e de destino e as salva em variáveis.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Obter resultados da verificação

O código a seguir compara cada uma das imagens de origem com a imagem de destino e imprime uma mensagem indicando se elas pertencem à mesma pessoa.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Tirar um instantâneo para a migração de dados

O recurso de instantâneos permite que você mova os dados de rosto salvos, como um **usuário**treinado, para uma assinatura de face de serviços cognitivas do Azure diferente. Talvez você queira usar esse recurso se, por exemplo, tiver criado um objeto **Person** usando uma assinatura de avaliação gratuita e agora quiser migrá-lo para uma assinatura paga. Consulte [migrar seus dados de rosto](../Face-API-How-to-Topics/how-to-migrate-face-data.md) para uma ampla visão geral do recurso de instantâneos.

Neste exemplo, você vai migrar o grupo de **pessoas** que criou em [criar e treinar um usuário](#create-and-train-a-person-group). Você pode concluir essa seção primeiro ou usar suas próprias construções de dados de face.

### <a name="set-up-target-subscription"></a>Configurar assinatura de destino

Primeiro, você deve ter uma segunda assinatura do Azure com um recurso de face; Você pode fazer isso seguindo as etapas na seção [configuração](#setting-up) . 

Em seguida, crie as seguintes variáveis próximo à parte superior do seu script. Você também precisará criar novas variáveis de ambiente para a ID da assinatura da sua conta do Azure, bem como a chave, o ponto de extremidade e a ID da assinatura da sua nova conta (destino). 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Autenticar cliente de destino

Posteriormente, em seu script, salve o objeto de cliente atual como o cliente de origem e, em seguida, autentique um novo objeto de cliente para sua assinatura de destino. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Usar um instantâneo

O restante das operações de instantâneo ocorre em uma função assíncrona. 

1. A primeira etapa é **tirar** o instantâneo, que salva os dados de face da assinatura original em um local de nuvem temporário. Esse método retorna uma ID que você usa para consultar o status da operação.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Em seguida, consulte a ID até que a operação seja concluída.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Esse código usa a função `wait_for_operation`, que você deve definir separadamente:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Volte para a função assíncrona. Use a operação **aplicar** para gravar seus dados de rosto em sua assinatura de destino. Esse método também retorna uma ID.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Novamente, use a função `wait_for_operation` para consultar a ID até que a operação seja concluída.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Depois de concluir essas etapas, você poderá acessar suas construções de dados de face da sua assinatura do novo (destino).

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo com o comando `python` em seu arquivo de início rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Se você criou um **Person** neste guia de início rápido e deseja excluí-lo, execute o seguinte código em seu script:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Se você migrou dados usando o recurso de instantâneo neste guia de início rápido, também precisará excluir o **usuário** salvo na assinatura de destino.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a usar a biblioteca de face para o Python para tarefas de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência de API de Detecção Facial (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [O que é o API de Detecção Facial?](../overview.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).