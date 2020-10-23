---
title: 'Tutorial: Explore o básico com uma aplicação de cliente de amostra'
titleSuffix: Azure Digital Twins
description: Tutorial para explorar os SDKs Azure Digital Twins usando uma aplicação de linha de comando de amostra
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: d7c95317667999ac17803f08575e68641100b967
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460789"
---
# <a name="tutorial-explore-azure-digital-twins-with-a-sample-client-app"></a>Tutorial: Explore a Azure Digital Twins com uma aplicação de cliente de amostra

Este tutorial introduz uma aplicação de amostra que implementa uma aplicação de cliente de linha de comando, para interagir com uma instância Azure Digital Twins. A aplicação do cliente é semelhante à escrita em [*Tutorial: Código uma aplicação de cliente*](tutorial-code.md).

Você pode usar esta amostra para executar ações essenciais da Azure Digital Twins, como carregar modelos, criar e modificar gémeos e criar relacionamentos. Você também pode olhar para o código da amostra para aprender sobre as APIs das Gémeas Digitais Azure, e praticar implementar os seus próprios comandos modificando o projeto da amostra como quiser.

Neste tutorial, vai...
> [!div class="checklist"]
> * Configurar um exemplo de Gémeos Digitais Azure
> * Configure a app de linha de comando da amostra para interagir com a instância
> * Utilize a app de linha de comando para explorar as Gémeas Digitais Azure, incluindo **modelos,** **gémeos digitais,** **relacionamentos**e **consultas**

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>Explore com a solução de amostra

Agora que a aplicação de exemplo e de exemplo está configurada, você usará o projeto de amostra e algum código de exemplo pré-escrito para construir e explorar uma solução básica de Azure Digital Twins. Os principais componentes da solução são **os modelos,** **gémeos digitais,** e **relacionamentos,** resultando num **gráfico gémeo** questionável de um ambiente.

### <a name="model-a-physical-environment-with-dtdl"></a>Modelar um ambiente físico com DTDL

O primeiro passo para criar uma solução Azure Digital Twins é definir [**modelos gémeos**](concepts-models.md) para o seu ambiente. 

Os modelos são semelhantes às aulas em linguagens de programação orientadas a objetos; fornecem modelos definidos pelo utilizador para [que gémeos digitais](concepts-twins-graph.md) sigam e instantaneamente mais tarde. São escritas numa linguagem semelhante a JSON chamada Linguagem de Definição de **Gémeos Digitais (DTDL)** e podem definir *as propriedades*de um gémeo, *telemetria,* *relacionamentos*e *componentes.*

> [!NOTE]
> O DTDL também permite a definição de comandos em *gémeos* digitais. No entanto, os comandos não são atualmente suportados no serviço Azure Digital Twins.

Na janela do Seu Estúdio Visual onde o projeto _**AdtE2ESample**_ está aberto, utilize o painel *Solution Explorer* para navegar na pasta *AdtSampleApp\SampleClientApp\Models.* Esta pasta contém modelos de amostra.

Selecione *Room.js* para abri-lo na janela de edição e alterá-lo das seguintes formas:

* **Atualize o número da versão**, para indicar que está a fornecer uma versão mais atualizada deste modelo. Faça-o alterando o *1* no final do `@id` valor para um *2*. Qualquer número maior do que o número atual da versão também funcionará.
* **Editar uma propriedade.** Mude o nome da `Humidity` propriedade para *HumidityLevel* (ou algo diferente, se quiser. Se utilizar algo diferente do *HumidityLevel,* lembre-se do que utilizou e continue a usá-lo em vez de *HumidityLevel* durante todo o tutorial).
* **Adicione uma propriedade.** Por baixo da `HumidityLevel` propriedade que termina na linha 15, cole o seguinte código para adicionar uma propriedade ao `RoomName` quarto:

    ```json
    ,
    {
      "@type": "Property",
      "name": "RoomName",
      "schema": "string"
    }
    ```
* **Adicione uma relação.** Por baixo da `RoomName` propriedade que acabou de adicionar, cole o seguinte código para adicionar a capacidade deste tipo de *gémeos de* formar relações com outros gémeos:

    ```json
    ,
    {
      "@type": "Relationship",
      "name": "contains",
    }
    ```

Quando terminar, o modelo atualizado deve ser assim:

:::image type="content" source="media/tutorial-command-line-app/room-model.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento" border="false":::

Certifique-se de que guarda o ficheiro antes de seguir em frente.

> [!TIP]
> Se quiser tentar criar o seu próprio modelo, pode colar o código do modelo *Room* num novo ficheiro que guarde com uma extensão *.json* na pasta *AdtSampleApp\SampleClientApp\Models.* Depois, brinque com a adição de propriedades e relacionamentos para representar o que quiser. Também pode olhar para os outros modelos de amostra desta pasta para ideias.

> [!TIP] 
> Existe uma [amostra de DTDL Validador](/samples/azure-samples/dtdl-validator/dtdl-validator) agnóstico que pode usar para verificar documentos de modelo para se certificar de que o DTDL é válido. É construído sobre a biblioteca de parser DTDL, sobre a qual pode ler mais em [*Como-a: Parse e validar modelos.*](how-to-parse-models.md)

### <a name="get-started-with-the-command-line-app"></a>Começar com a app de linha de comando

Agora que definiu um modelo, os passos restantes envolvem usar a app da amostra para interagir com a sua instância Azure Digital Twins. Executar o projeto com este botão na barra de ferramentas:

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento":::

Uma janela da consola abrir-se-á, procederá à autenticação e esperará por um comando. 
* A autenticação é tratada através do navegador: o seu navegador padrão abrirá com uma solicitação de autenticação. Utilize este pedido para iniciar súmis com as suas credenciais Azure. Em seguida, pode fechar o separador ou janela do navegador.

Aqui está uma imagem de como é a consola do projeto:

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento":::

> [!TIP]
> Para obter uma lista de todos os comandos possíveis que pode utilizar com este projeto, `help` insira na consola do projeto e volte a pressionar.
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento":::

Mantenha a consola do projeto em funcionamento para o resto dos passos neste tutorial.

#### <a name="upload-models-to-azure-digital-twins"></a>Upload de modelos para Azure Digital Twins

Depois de desenhar modelos, precisa de os enviar para a sua instância Azure Digital Twins. Isto configura a sua instância de serviço Azure Digital Twins com o seu próprio vocabulário de domínio personalizado. Depois de ter carregado os modelos, pode criar casos duplos que os utilizem.

Na janela da consola do projeto, execute o seguinte comando para carregar o seu modelo *de Quarto* atualizado, bem como um modelo *de piso* que também utilizará na secção seguinte para criar diferentes tipos de gémeos.

```cmd/sh
CreateModels Room Floor
```

A saída deve indicar que os modelos foram criados com sucesso.

> [!TIP]
> Se concebeu o seu próprio modelo mais cedo, também pode carregá-lo aqui, adicionando o seu nome de ficheiro (pode deixar de fora a extensão) à `Room Floor` lista no comando acima.

Verifique se os modelos foram criados executando o comando `GetModels true` . Isto irá consultar a instância Azure Digital Twins para todos os modelos que foram carregados, e imprimir toda a sua informação. Procure o modelo *de quarto* editado nos resultados:

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento":::

#### <a name="errors"></a>Erros

A aplicação da amostra também lida com erros do serviço. 

Re-executar o `CreateModels` comando para tentar re-carregar um dos mesmos modelos que acabou de carregar, pela segunda vez:

```cmd/sh
CreateModels Room
```

Como os modelos não podem ser substituídos, isto irá agora devolver um erro de serviço.
Para obter os detalhes sobre como eliminar os modelos existentes, consulte [*Como-a-fazer: Gerir modelos personalizados*](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

### <a name="create-digital-twins"></a>Criar gémeos digitais

Agora que alguns modelos foram enviados para a sua instância Azure Digital Twins, pode criar [**gémeos digitais**](concepts-twins-graph.md) com base nas definições do modelo. Os gémeos digitais representam as entidades dentro do seu ambiente de negócios — coisas como sensores numa quinta, quartos num edifício ou luzes num carro. 

Para criar um gémeo digital, usa-se o `CreateDigitalTwin` comando. Deve fazer referência ao modelo em que o gémeo se baseia e pode definir opcionalmente valores iniciais para quaisquer propriedades do modelo. Não tens de passar nenhuma informação sobre relacionamentos nesta fase.

Executa este código na consola do projeto em execução para criar vários gémeos, com base no modelo *Room* que atualizou anteriormente e outro modelo, *Floor*. Lembre-se que *o Quarto* tem três propriedades, para que possa fornecer argumentos com os valores iniciais para estes.

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> Se carregou o seu próprio modelo mais cedo, tente fazer o seu próprio `CreateDigitalTwin` comando com base nos comandos acima para adicionar um gémeo do seu próprio tipo de modelo.

A saída destes comandos deve indicar que os gémeos foram criados com sucesso. 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento":::

Também pode verificar se os gémeos foram criados com o `Query` comando. Este comando consulta a sua instância Azure Digital Twins para todos os gémeos digitais que contém. Procure o *piso 0,* *o piso1,* o quarto *0,* e *o quarto1* gémeos nos resultados.

#### <a name="modify-a-digital-twin"></a>Modificar um gémeo digital

Também pode modificar as propriedades de um gémeo que criou. Tente executar este comando para mudar *o*QuartoName da *sala0* para o *PresidencialSuite:*

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

A saída deve indicar que o gémeo foi atualizado com sucesso.

Também pode verificar executando este comando para ver as informações da *sala0:*

```cmd/sh
GetDigitalTwin room0
```

A saída deve refletir o nome atualizado.

> [!NOTE]
> A API REST subjacente utiliza o JSON Patch para definir atualizações a um gémeo. A aplicação da linha de comando reflete este formato, para que possa experimentar com o que as APIs subjacentes realmente esperam.

### <a name="create-a-graph-by-adding-relationships"></a>Criar um gráfico adicionando relacionamentos

Em seguida, pode criar algumas **relações** entre estes gémeos, para ligá-los a um [**gráfico gémeo.**](concepts-twins-graph.md) Os gráficos gémeos são usados para representar um ambiente inteiro. 

Para adicionar uma relação, use o `CreateRelationship` comando. Especifique o gémeo de onde a relação está a vir, o tipo de relação a adicionar, e o gémeo a que a relação está ligada. Por último, forneça um nome (ID) para a relação.

Execute o seguinte código para adicionar uma relação "contém" de cada um dos gémeos *Floor* que criou anteriormente a um *quarto* gémeo correspondente. Note que deve haver uma relação *de contenção* definida no modelo *Floor* para que isso seja possível.

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

A saída destes comandos confirma que as relações foram criadas com sucesso:

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento":::

Também pode verificar as relações com qualquer um dos seguintes comandos, que consultam as relações na sua instância Azure Digital Twins.
* Para ver todas as relações saindo de cada andar (vendo as relações de um lado),
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* Para ver todas as relações que chegam a cada quarto (vendo a relação do lado "do outro"),
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* Para consultar estas relações individualmente, 
    ```cmd/sh
    GetRelationship floor0 relationship0
    GetRelationship floor1 relationship1
    ```

As gémeas e relações que criou neste formato tutorial são o seguinte gráfico conceptual:

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento" border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>Consultar o gráfico gémeo para responder a questões ambientais

Uma das principais características da Azure Digital Twins é a capacidade [de consultar](concepts-query-language.md) o seu gráfico gémeo de forma fácil e eficiente para responder a questões sobre o seu ambiente. Executa os seguintes comandos na consola do projeto em execução para ter uma ideia de como isto é.

* **Quais são todas as entidades do meu ambiente representadas no Azure Digital Twins?** (consulta a todos)

    ```cmd/sh
    Query
    ```

    Isto permite-lhe fazer um balanço do seu ambiente num ápice, e certifique-se de que tudo está representado como gostaria que estivesse dentro da Azure Digital Twins. O resultado disto é uma saída contendo cada gémeo digital com os seus detalhes. Aqui está um excerto:

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento":::

    >[!NOTE]
    >No projeto da amostra, o comando `Query` sem argumentos adicionais é o equivalente a `Query SELECT * FROM DIGITALTWINS` . Para consultar todos os gémeos no seu caso utilizando as [APIs de Consulta](/rest/api/digital-twins/dataplane/query) ou os [comandos CLI,](how-to-use-cli.md)utilize a consulta mais longa (completa).

* **Quais são todos os quartos do meu ambiente?** (consulta por modelo)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Pode restringir a sua consulta a gémeos de um certo tipo, para obter informações mais específicas sobre o que está representado. O resultado deste mostra *a sala0* e *a sala1,* mas **não** mostra o *piso 0* ou *o piso 1* (uma vez que são pisos, não quartos).
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento":::

* **Quais são todos os quartos no *andar0?*** (consulta por relação)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Você pode consultar com base em relacionamentos no seu gráfico, para obter informações sobre como os gémeos estão conectados ou para restringir a sua consulta a uma determinada área. Só *o quarto 0* é no *andar 0,* por isso é o único quarto no resultado.

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento":::

* **Quais são os gémeos no meu ambiente com uma temperatura superior a 75?** (consulta por propriedade)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Você pode consultar o gráfico com base em propriedades para responder a uma variedade de perguntas, incluindo descobrir outliers no seu ambiente que podem precisar de atenção. Outros operadores de comparação *<* *>* (, *=* , , , ou *!=*) também são apoiados. *quarto1* aparece nos resultados aqui, porque tem uma temperatura de 80.

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento":::

* **Quais são todos os quartos no *andar0* com uma temperatura superior a 75?** (consulta composta)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Também pode combinar as consultas anteriores como em SQL, utilizando operadores de combinação `AND` `OR` como, `NOT` . . Esta consulta `AND` utiliza-se para tornar a consulta anterior sobre temperaturas duplas mais específica. O resultado agora só inclui quartos com temperaturas acima dos 75 que estão no *chão0*— o que, neste caso, não é nenhum deles. O resultado está vazio.

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="Editado Room.jscom o número de versão atualizado, propriedades De HumidityLevel e RoomName, e contém relacionamento":::

## <a name="clean-up-resources"></a>Limpar os recursos

O projeto neste tutorial constitui a base para o próximo tutorial, [*Tutorial: Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md). Se pretender continuar para o próximo tutorial, pode manter os recursos que criou aqui para continuar a usar esta instância Azure Digital Twins e uma aplicação de amostra configurada.
* Neste caso, pode utilizar a aplicação da amostra `DeleteAllTwins` e `DeleteAllModels` os comandos para limpar os gémeos e modelos no seu caso, respectivamente. Isto vai dar-lhe uma ficha limpa para o próximo tutorial.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Por fim, elimine a pasta de amostras de projeto que descarregou para a sua máquina local.

## <a name="next-steps"></a>Passos seguintes 

Neste tutorial, começou com a Azure Digital Twins, criando uma instância e uma aplicação de cliente para interagir com o caso. Usou a aplicação do cliente para explorar a Azure Digital Twins, criando modelos, gémeos digitais e relacionamentos. Também fez algumas perguntas sobre a solução, para ter uma ideia do tipo de perguntas que a Azure Digital Twins pode responder sobre um ambiente.

Continue até ao próximo tutorial para utilizar a aplicação da linha de comando da amostra em combinação com outros serviços Azure para completar um cenário de ponta a ponta orientado por dados:
> [!div class="nextstepaction"]
> [*Tutorial: Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md)