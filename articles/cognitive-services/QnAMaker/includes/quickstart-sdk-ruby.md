---
title: 'Quickstart: Biblioteca de clientes QnA Maker para Ruby'
description: Este quickstart mostra como começar com a biblioteca de clientes QnA Maker para Ruby.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: cfdd1595ffb21b0f567f5299ebd6257b3462b671
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730990"
---
Utilize a biblioteca de clientes QnA Maker para ruby para:

* Criar uma base de conhecimentos
* Atualizar uma base de conhecimentos
* Publicar uma base de conhecimentos
* Obtenha a chave final do tempo de execução da previsão
* Aguarde por uma tarefa de longa duração
* Descarregue uma base de conhecimentos
* Obter resposta
* Eliminar base de conhecimento

[Código fonte da biblioteca (autoria)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated)  |  [Código fonte da biblioteca (tempo de execução)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated)  |  [Pacote (autoria)](https://rubygems.org/gems/azure_cognitiveservices_qnamaker)  |  [Pacote (tempo de execução)](https://rubygems.org/gems/azure_cognitiveservices_qnamakerruntime)  |  [Amostras de rubi](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/ruby/qnamaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Rubi 2.x](https://www.ruby-lang.org/)
* Assim que tiver a sua subscrição Azure, crie um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal Azure para obter a sua chave de autoria e ponto final. Depois de ser implantado, selecione **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Criador de QnA. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-libraries"></a>Instalar as bibliotecas do cliente

Depois de instalar a Ruby, pode instalar as bibliotecas do cliente com:

```console
gem install azure_cognitiveservices_qnamaker
gem install azure_cognitiveservices_qnamakerruntime
```

### <a name="create-a-new-ruby-application"></a>Criar uma nova aplicação Ruby

Crie um novo ficheiro com o nome `quickstart.rb` e importe as seguintes bibliotecas.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Dependencies":::

Crie variáveis para o ponto final e chave Azure do seu recurso.

> [!IMPORTANT]
> Vá ao portal Azure e encontre a chave e o ponto final para o recurso QnA Maker que criou nos pré-requisitos. Estarão localizados na **página chave e endpoint** do recurso, sob **gestão de recursos.**
> Precisa de toda a chave para criar a sua base de conhecimentos. Só precisa do nome de recurso do ponto final. O formato é `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [o cofre de chaves Azure](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguros.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Resourcevariables":::

## <a name="object-models"></a>Modelos de objetos

O Fabricante QnA usa dois modelos de objetos diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto para criar, gerir, publicar e descarregar a base de conhecimentos.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto para consultar a base de conhecimento com a API GenerateAnswer e enviar novas perguntas sugeridas usando a API do comboio (como parte da [aprendizagem ativa).](../how-to/use-active-learning.md)

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modelo de objeto QnAMakerClient

O cliente da Autoria QnA Maker é um objeto [QnAMakerClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/qnamaker_client.rb) que autentica a Azure usando msRest::ServiceClientCredentials, que contém a sua chave.

Assim que o cliente for criado, utilize os métodos da propriedade da base de [conhecimentos](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb) do cliente para criar, gerir e publicar a sua base de conhecimentos.

Para operações imediatas, um método normalmente devolve o resultado, se houver. Para operações de longa duração, a resposta é um objeto [de operação.](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) Ligue para o [método operations.get_details](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/operations.rb#L33) com o valor para determinar o estado `operation.operation_id` do [pedido](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation_state_type.rb).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto QnAMakerRuntimeClient

O cliente QnA Maker é um objeto [QnAMakerRuntimeClient.](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb)

Depois de publicar a sua base de conhecimentos utilizando o cliente autoria, utilize o método [de runtime.generate_answer](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/runtime.rb#L34) do cliente em tempo de execução para obter uma resposta da base de conhecimento.

Quando `generate_answer` ligar, passe um haxixe para o `custom_headers` parâmetro opcional. Este haxixe deve incluir uma chave `Authorization` e um `EndpointKey YOUR_ENDPOINT_KEY` valor. Para o valor de YOUR_ENDPOINT_KEY, utilize o cliente autor da autoria para ligar para [endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32).

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para a autoria da base de conhecimentos

Instantiizar um cliente com a sua chave de fim de autor e subscrição.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationAuthor":::

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/create_kb_dto.rb) de três fontes:

* Para **conteúdos editoriais,** utilize o objeto [QnADTO.](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adto.rb)
    * Para utilizar metadados e pedidos de acompanhamento, utilize o contexto editorial, porque estes dados são adicionados ao nível de pares QnA individuais.
* Para **ficheiros,** utilize o objeto [FileDTO.](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/file_dto.rb) O FileDTO inclui o nome de ficheiro, bem como o URL público para chegar ao ficheiro.
* Para **URLs,** utilize uma lista de cordas para representar URLs disponíveis ao público.

Ligue para o método [de criação](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L554) e, em seguida, passe `operation_id` a propriedade da operação devolvida para o método [operations.get_details](#get-status-of-an-operation) para sondar o estado.

A linha final do seguinte código devolve o ID da base de conhecimento.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="CreateKBMethod":::

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

Pode atualizar uma base de conhecimentos ligando para [a base de conhecimento.update](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L455) e passando no ID da base de conhecimento e num objeto [UpdateKbOperationDTO.](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dto.rb) O objeto, por sua vez, pode conter:
- [adicionar](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoadd.rb)
- [atualizar](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoupdate.rb)
- [delete](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtodelete.rb)

Passe a `operation_id` propriedade da operação devolvida ao [método operations.get_details](#get-status-of-an-operation) para sondar o estatuto.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="UpdateKBMethod":::

## <a name="download-a-knowledge-base"></a>Descarregue uma base de conhecimento

Utilize o método [database.download](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L651) para descarregar a base de dados como uma lista de [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adocuments_dto.rb). Isto _não_ é equivalente à exportação do portal QnA Maker a partir da página **Definições** porque o resultado deste método não é um ficheiro TSV.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DownloadKB":::

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de conhecimento utilizando o método [database.publish.](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L283) Isto pega no modelo atual guardado e treinado, referenciado pela base de conhecimento ID, e publica-o num ponto final.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="PublishKB":::

## <a name="get-query-runtime-key"></a>Obtenha chave de tempo de execução de consulta

Uma vez publicada uma base de conhecimento, você precisa da chave de fim de tempo de execução para consultar o conhecimento. Isto não é o mesmo que a chave de subscrição utilizada para criar o cliente autoria.

Utilize o método [endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32) para obter um objeto [EndpointKeysDTO.](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/endpoint_keys_dto.rb)

Utilize qualquer uma das propriedades chave devolvidas no objeto para consultar a base de conhecimentos.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GetQueryEndpointKey":::

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o tempo de execução para gerar uma resposta

Crie um [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) para consultar a base de conhecimento para gerar uma resposta ou treinar a partir de uma aprendizagem ativa.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationQuery":::

Utilize o QnAMakerRuntimeClient para obter uma resposta do conhecimento ou para enviar novas perguntas sugeridas para a base de conhecimento para [a aprendizagem ativa.](../concepts/active-learning-suggestions.md)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta a partir da base de conhecimento

Gere uma resposta a partir de uma base de conhecimento publicada utilizando o método RuntimeClient.runtime.generateAnswer. Este método aceita o ID da base de conhecimento e o QueryDTO. Aceda a propriedades adicionais do QueryDTO, tal Top e Context para usar no seu chat bot.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GenerateAnswer":::

Este é um simples exemplo de consulta de uma base de conhecimento. Para compreender cenários de consulta avançada, reveja [outros exemplos de consulta.](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)

## <a name="delete-a-knowledge-base"></a>Eliminar uma base de dados de conhecimento

Elimine a base de conhecimento utilizando o método [knowledgebase.delete](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L205) com um parâmetro do ID da base de conhecimento.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DeleteKB":::

## <a name="get-status-of-an-operation"></a>Obter o estado de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de esperar que o processo termine, uma [operação](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) seja devolvida. Utilize o ID de funcionamento da operação para a sondagem (com lógica de repetição) para determinar o estado do método original.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="MonitorOperation":::

## <a name="run-the-application"></a>Executar a aplicação

Aqui está o método principal para a aplicação.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Main":::

Execute a aplicação com o comando rubi no seu ficheiro quickstart.

```console
ruby quickstart.rb
```

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/ruby/qnamaker/sdk/quickstart.rb).
