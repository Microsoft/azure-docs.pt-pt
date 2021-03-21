---
title: 'Tutorial: Criar um gráfico em Azure Digital Twins (app cliente)'
titleSuffix: Azure Digital Twins
description: Tutorial para construir um cenário Azure Digital Twins usando uma aplicação de linha de comando de amostra
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c18366fd4bc510f32ac0ef255b27709797a3b626
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493714"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>Tutorial: Criar um gráfico Azure Digital Twins usando uma aplicação de cliente de amostra

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

Neste tutorial, você vai construir um gráfico em Azure Digital Twins usando modelos, gémeos e relacionamentos. A ferramenta para este tutorial é a **aplicação de cliente de linha de comando de uma amostra** para interagir com uma instância Azure Digital Twins. A aplicação do cliente é semelhante à escrita em [*Tutorial: Código uma aplicação de cliente*](tutorial-code.md).

Você pode usar esta amostra para executar ações essenciais da Azure Digital Twins, como carregar modelos, criar e modificar gémeos e criar relacionamentos. Você também pode olhar para o [código da amostra](https://github.com/Azure-Samples/digital-twins-samples/tree/master/) para aprender sobre as APIs das Gémeas Digitais Azure, e praticar implementar os seus próprios comandos modificando o projeto da amostra como quiser.

Neste tutorial, vai...
> [!div class="checklist"]
> * Modelar um ambiente
> * Criar duplos digitais
> * Adicionar relacionamentos para formar um gráfico
> * Consultar o gráfico para responder a perguntas

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>Executar o projeto de amostra

Agora que a aplicação e a autenticação estão configurada, execute o projeto com este botão na barra de ferramentas:

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Screenshot do botão de arranque do Estúdio Visual (projeto SampleClientApp)." lightbox="media/tutorial-command-line/app/start-button-sample.png":::

Uma janela da consola abrir-se-á, procederá à autenticação e esperará por um comando. 
* A autenticação é tratada através do navegador: o seu navegador padrão abrirá com uma solicitação de autenticação. Utilize este pedido para iniciar súmis com as suas credenciais Azure. Em seguida, pode fechar o separador ou janela do navegador.

Aqui está uma imagem de como é a consola do projeto:

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="Screenshot da mensagem de boas-vindas da aplicação da linha de comando." lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> Para obter uma lista de todos os comandos possíveis que pode utilizar com este projeto, `help` insira na consola do projeto e volte a pressionar.

Mantenha a consola do projeto em funcionamento para o resto dos passos neste tutorial.

## <a name="model-a-physical-environment-with-dtdl"></a>Modelar um ambiente físico com DTDL

Agora que a instância Azure Digital Twins e a app de amostras estão configurada, pode começar a construir um gráfico de um cenário. 

O primeiro passo para criar uma solução Azure Digital Twins é definir [**modelos gémeos**](concepts-models.md) para o seu ambiente. 

Os modelos são semelhantes às aulas em linguagens de programação orientadas a objetos; fornecem modelos definidos pelo utilizador para [que gémeos digitais](concepts-twins-graph.md) sigam e instantaneamente mais tarde. São escritas numa linguagem semelhante a JSON chamada Linguagem de Definição de **Gémeos Digitais (DTDL)** e podem definir *as propriedades* de um gémeo, *telemetria,* *relacionamentos* e *componentes.*

> [!NOTE]
> O DTDL também permite a definição de comandos em *gémeos* digitais. No entanto, os comandos não são atualmente suportados no serviço Azure Digital Twins.

Na janela do Seu Estúdio Visual onde o projeto _**AdtE2ESample**_ está aberto, utilize o painel *Solution Explorer* para navegar até à *pasta AdtSampleApp\SampleClientApp\Models*. Esta pasta contém modelos de amostra.

Selecione *Room.js* para abri-lo na janela de edição e alterá-lo das seguintes formas:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Upload de modelos para Azure Digital Twins

Depois de desenhar modelos, precisa de os enviar para a sua instância Azure Digital Twins. Isto configura a sua instância de serviço Azure Digital Twins com o seu próprio vocabulário de domínio personalizado. Depois de ter carregado os modelos, pode criar casos duplos que os utilizem.

1. Na janela da consola do projeto, execute o seguinte comando para carregar o seu modelo *de Quarto* atualizado, bem como um modelo *de piso* que também utilizará na secção seguinte para criar diferentes tipos de gémeos.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    A saída deve indicar que os modelos foram criados com sucesso.

1. Verifique se os modelos foram criados executando o comando `GetModels true` . Isto irá consultar a instância Azure Digital Twins para todos os modelos que foram carregados, e imprimir toda a sua informação. Procure o modelo *de quarto* editado nos resultados:

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="Screenshot do resultado da GetModels, mostrando o modelo de Quarto atualizado." lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>Erros

A aplicação da amostra também lida com erros do serviço. 

Re-executar o `CreateModels` comando para tentar re-carregar um dos mesmos modelos que acabou de carregar, pela segunda vez:

```cmd/sh
CreateModels Room
```

Como os modelos não podem ser substituídos, isto irá agora devolver um erro de serviço.
Para obter os detalhes sobre como eliminar os modelos existentes, consulte [*Como-a-fazer: Gerir os modelos DTDL*](how-to-manage-model.md).
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

## <a name="create-digital-twins"></a>Criar duplos digitais

Agora que alguns modelos foram enviados para a sua instância Azure Digital Twins, pode criar [**gémeos digitais**](concepts-twins-graph.md) com base nas definições do modelo. Os gémeos digitais representam as entidades dentro do seu ambiente de negócios — coisas como sensores numa quinta, quartos num edifício ou luzes num carro. 

Para criar um gémeo digital, usa-se o `CreateDigitalTwin` comando. Deve fazer referência ao modelo em que o gémeo se baseia e pode definir opcionalmente valores iniciais para quaisquer propriedades do modelo. Não tens de passar nenhuma informação sobre relacionamentos nesta fase.

1. Executa este código na consola do projeto em execução para criar vários gémeos, com base no modelo *Room* que atualizou anteriormente e outro modelo, *Floor*. Lembre-se que *o Quarto* tem três propriedades, para que possa fornecer argumentos com os valores iniciais para estes. (Inicializar os valores de propriedade é opcional em geral, mas são necessários para este tutorial.)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    A saída destes comandos deve indicar que os gémeos foram criados com sucesso. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="Screenshot mostrando um excerto do resultado dos comandos CreateDigitalTwin, que inclui piso0, piso1, sala0 e sala1." lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. Pode verificar se os gémeos foram criados com o `Query` comando. Este comando consulta a sua instância Azure Digital Twins para todos os gémeos digitais que contém. Procure o *quarto 0,* *sala1,* *piso 0,* e *1 4º andar* nos resultados.

### <a name="modify-a-digital-twin"></a>Modificar um gémeo digital

Também pode modificar as propriedades de um gémeo que criou. 

> [!NOTE]
> A API REST subjacente utiliza o formato [JSON Patch](http://jsonpatch.com/) para definir atualizações a um gémeo. A aplicação de linha de comando também utiliza este formato, para dar uma experiência mais verdadeira com o que as APIs subjacentes esperam.

1. Executar este comando para mudar *o* QuartoName da *sala0* para *o PresidencialSuite:*
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    A saída deve indicar que o gémeo foi atualizado com sucesso.

1. Pode verificar se a atualização foi bem sucedida executando este comando para ver as informações da *sala0:*

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    A saída deve refletir o nome atualizado.


## <a name="create-a-graph-by-adding-relationships"></a>Criar um gráfico adicionando relacionamentos

Em seguida, pode criar algumas **relações** entre estes gémeos, para ligá-los a um [**gráfico gémeo.**](concepts-twins-graph.md) Os gráficos gémeos são usados para representar um ambiente inteiro. 

Os tipos de relacionamentos que pode criar de um gémeo para outro são definidos dentro dos [modelos](#model-a-physical-environment-with-dtdl) que carregou anteriormente. A [definição do modelo *para Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) especifica que os pisos podem ter um tipo de relação chamado *contém*. Isto permite criar uma relação de tipo *contém* de cada *piso* gémeo para a sala correspondente que contém.

Para adicionar uma relação, use o `CreateRelationship` comando. Especifique o gémeo de onde a relação está a vir, o tipo de relacionamento, e o gémeo a que a relação está ligada. Por último, dê à relação uma identificação única.

1. Execute o seguinte código para adicionar uma relação "contém" de cada um dos gémeos *Floor* que criou anteriormente a um *quarto* gémeo correspondente. As relações são nomeadas *relação0* e *relação1.*

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >A relação *contém* no modelo [ *Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) também foi definida com duas propriedades de cordas, `ownershipUser` `ownershipDepartment` e, assim, você também pode fornecer argumentos com os valores iniciais para estes quando você cria as relações.
    > Aqui está uma versão alternativa do comando acima para criar *relacionamento0* que também especifica valores iniciais para estas propriedades:
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    A saída destes comandos confirma que as relações foram criadas com sucesso:
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="Screenshot de um excerto do resultado dos comandos CreateRelationship, que inclui relacionamentos0 e relacionamento1." lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. Pode verificar as relações com qualquer um dos seguintes comandos, que consultam as relações na sua instância Azure Digital Twins.
    * Para ver todas as relações saindo de cada andar (vendo as relações de um lado):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * Para ver todas as relações que chegam a cada quarto (vendo a relação do lado "do outro):):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * Procurar estas relações individualmente, por ID:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

As gémeas e relações que criou neste formato tutorial são o seguinte gráfico conceptual:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Um diagrama mostrando um gráfico conceptual. o piso 0 está ligado através da relação0 com o quarto0, e o piso 1 está ligado através da relação1 com o quarto1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Consultar o gráfico gémeo para responder a questões ambientais

Uma das principais características da Azure Digital Twins é a capacidade [de consultar](concepts-query-language.md) o seu gráfico gémeo de forma fácil e eficiente para responder a questões sobre o seu ambiente. 

Execute os seguintes comandos na consola do projeto em execução para responder a algumas perguntas sobre o ambiente da amostra.

1. **Quais são todas as entidades do meu ambiente representadas no Azure Digital Twins?** (consulta a todos)

    ```cmd/sh
    Query
    ```

    Isto permite-lhe fazer um balanço do seu ambiente num ápice, e certifique-se de que tudo está representado como gostaria que estivesse dentro da Azure Digital Twins. O resultado disto é uma saída contendo cada gémeo digital com os seus detalhes. Aqui está um excerto:

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="Screenshot mostrando um resultado parcial da consulta dupla, incluindo quarto 0 e piso1.":::

    >[!NOTE]
    >No projeto da amostra, o comando `Query` sem argumentos adicionais é o equivalente a `Query SELECT * FROM DIGITALTWINS` . Para consultar todos os gémeos no seu caso utilizando as [APIs de Consulta](/rest/api/digital-twins/dataplane/query) ou os [comandos CLI,](how-to-use-cli.md)utilize a consulta mais longa (completa).

1. **Quais são todos os quartos do meu ambiente?** (consulta por modelo)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Pode restringir a sua consulta a gémeos de um certo tipo, para obter informações mais específicas sobre o que está representado. O resultado deste mostra *a sala0* e *a sala1,* mas **não** mostra o *piso 0* ou *o piso 1* (uma vez que são pisos, não quartos).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="Screenshot do resultado da consulta do modelo, mostrando apenas o quarto0 e o quarto1.":::

1. **Quais são todos os quartos no *andar0?*** (consulta por relação)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Você pode consultar com base em relacionamentos no seu gráfico, para obter informações sobre como os gémeos estão conectados ou para restringir a sua consulta a uma determinada área. Só *o quarto 0* é no *andar 0,* por isso é o único quarto no resultado.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="Screenshot do resultado da consulta de relacionamento, mostrando espaço 0.":::

1. **Quais são os gémeos no meu ambiente com uma temperatura superior a 75?** (consulta por propriedade)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Você pode consultar o gráfico com base em propriedades para responder a uma variedade de perguntas, incluindo descobrir outliers no seu ambiente que podem precisar de atenção. Outros operadores de comparação *<* *>* (, *=* , , , ou *!=*) também são apoiados. *quarto1* aparece nos resultados aqui, porque tem uma temperatura de 80.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="Screenshot do resultado da consulta de propriedade, mostrando apenas o quarto1.":::

1. **Quais são todos os quartos no *andar0* com uma temperatura superior a 75?** (consulta composta)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Também pode combinar as consultas anteriores como em SQL, utilizando operadores de combinação `AND` `OR` como, `NOT` . . Esta consulta `AND` utiliza-se para tornar a consulta anterior sobre temperaturas duplas mais específica. O resultado agora só inclui quartos com temperaturas acima dos 75 que estão no *chão0*— o que, neste caso, não é nenhum deles. O resultado está vazio.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="Screenshot do resultado da consulta do composto, não mostrando resultados." lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de completar este tutorial, pode escolher quais os recursos que gostaria de remover, dependendo do que gostaria de fazer a seguir.

* **Se pretende continuar para o próximo tutorial,** pode manter os recursos que criou aqui para continuar a usar esta instância Azure Digital Twins e configurar a aplicação de amostra para o próximo tutorial

* **Se quiser continuar a usar a instância Azure Digital Twins, mas desmarcar todos os seus modelos, gémeos e relacionamentos,** pode utilizar a aplicação da amostra `DeleteAllTwins` e os `DeleteAllModels` comandos para limpar os gémeos e modelos no seu caso, respectivamente.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Também pode querer eliminar a pasta do projeto da sua máquina local.

## <a name="next-steps"></a>Passos seguintes 

Neste tutorial, começou com a Azure Digital Twins construindo um gráfico no seu caso usando uma aplicação de cliente de amostra. Criaste modelos, gémeos digitais e relacionamentos para formar um gráfico. Também fez algumas perguntas no gráfico, para ter uma ideia do tipo de perguntas que a Azure Digital Twins pode responder sobre um ambiente.

Continue até ao próximo tutorial para combinar Azure Digital Twins com outros serviços Azure para completar um cenário de ponta a ponta orientado para dados:
> [!div class="nextstepaction"]
> [*Tutorial: Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md)