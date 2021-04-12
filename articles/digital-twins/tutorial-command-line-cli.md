---
title: 'Tutorial: Criar um gráfico em Azure Digital Twins (CLI)'
titleSuffix: Azure Digital Twins
description: Tutorial para construir um cenário Azure Digital Twins usando o Azure CLI
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 578befe3e26ebb42fa2172976e07d0a5836e3743
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107164"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>Tutorial: Criar um gráfico Azure Digital Twins usando o Azure CLI

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

Neste tutorial, você vai construir um gráfico em Azure Digital Twins usando modelos, gémeos e relacionamentos. A ferramenta para este tutorial é o [conjunto de comando Azure Digital Twins para o **Azure CLI**](how-to-use-cli.md). 

Pode utilizar os comandos CLI para executar ações essenciais da Azure Digital Twins, como carregar modelos, criar e modificar gémeos e criar relacionamentos. Também pode consultar a documentação de referência para o [comando *az dt* definido](/cli/azure/dt) para ver o conjunto completo de comandos CLI.

Neste tutorial, vai...
> [!div class="checklist"]
> * Modelar um ambiente
> * Criar duplos digitais
> * Adicionar relacionamentos para formar um gráfico
> * Consultar o gráfico para responder a perguntas

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste tutorial, você precisará primeiro completar os seguintes pré-requisitos.

Se não tiver uma subscrição do Azure, **crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** antes de começar.

### <a name="download-the-sample-models"></a>Descarregue os modelos de amostra

O tutorial utiliza dois modelos pré-escritos que fazem parte do projeto de [amostra de ponta a ponta](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) C para as Gémeas Digitais Azure. Os ficheiros dos modelos estão localizados aqui: 
* [*Room.js*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [*Floor.js*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

Para obter os ficheiros da sua máquina, utilize os links de navegação acima e copie os corpos de ficheiros em ficheiros locais da sua máquina com os mesmos nomes *(Room.jsligados* e *Floor.js).*

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Configurar a sessão Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Prepare uma instância Azure Digital Twins

Para trabalhar com a Azure Digital Twins neste artigo, primeiro precisa **de configurar uma instância Azure Digital Twins** e as permissões necessárias para a sua utilização. Se já tem uma instância Azure Digital Twins configurada a partir de trabalhos anteriores, pode usar esse caso.

Caso contrário, siga as instruções em [*Como-a-: Configurar uma instância e autenticação*](how-to-set-up-instance-cli.md). As instruções também contêm passos para verificar se completou cada passo com sucesso e estão prontos para passar a usar a sua nova instância.

Depois de configurar a sua instância Azure Digital Twins, tome nota dos seguintes valores que terá de ligar ao caso mais tarde:
* o nome de **_anfitrião_ da instância**
* **a assinatura Azure** que usou para criar o caso. 

Pode obter ambos estes valores, por sua vez, na saída do seguinte comando Azure CLI: 

```azurecli-interactive
az dt show -n <ADT_instance_name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Screenshot da janela do navegador Cloud Shell mostrando a saída do comando az dt show. Destacam-se o campo hostName e o ID de subscrição (parte do campo de id).":::

## <a name="model-a-physical-environment-with-dtdl"></a>Modelar um ambiente físico com DTDL

Agora que o caso CLI e Azure Digital Twins estão configurado, pode começar a construir um gráfico de um cenário. 

O primeiro passo para criar uma solução Azure Digital Twins é definir [**modelos gémeos**](concepts-models.md) para o seu ambiente. 

Os modelos são semelhantes às aulas em linguagens de programação orientadas a objetos; fornecem modelos definidos pelo utilizador para [que gémeos digitais](concepts-twins-graph.md) sigam e instantaneamente mais tarde. São escritas numa linguagem semelhante a JSON chamada Linguagem de Definição de **Gémeos Digitais (DTDL)** e podem definir *as propriedades* de um gémeo, *telemetria,* *relacionamentos* e *componentes.*

> [!NOTE]
> O DTDL também permite a definição de comandos em *gémeos* digitais. No entanto, os comandos não são atualmente suportados no serviço Azure Digital Twins.

Navegue na sua máquina para o *Room.jsno* ficheiro que criou na secção [Pré-Requisitos.](#prerequisites) Abra-o num editor de código e altere-o das seguintes formas:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Upload de modelos para Azure Digital Twins

Depois de desenhar modelos, precisa de os enviar para a sua instância Azure Digital Twins. Isto configura a sua instância de serviço Azure Digital Twins com o seu próprio vocabulário de domínio personalizado. Depois de ter carregado os modelos, pode criar casos duplos que os utilizem.

1. Para adicionar modelos que utilizem a Cloud Shell, terá de enviar os seus ficheiros de modelo para o armazenamento da Cloud Shell para que os ficheiros estejam disponíveis quando executar o comando Cloud Shell que os utiliza. Para isso, selecione o ícone "Carregar/Transferir ficheiros" e escolha "Upload".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Screenshot da janela do navegador Cloud Shell mostrando a seleção do ícone upload.":::
    
    Navegue para a *Room.jsficheiro na* sua máquina e selecione "Abrir". Em seguida, repita este passo para *Floor.js.*

1. Em seguida, utilize o [**modelo az dt criar**](/cli/azure/dt/model#az_dt_model_create) comando como mostrado abaixo para carregar o seu modelo *de Quarto* atualizado para a sua instância Azure Digital Twins. O segundo comando carrega outro modelo, *Floor,* que também utilizará na secção seguinte para criar diferentes tipos de gémeos.

    ```azurecli-interactive
    az dt model create -n <ADT_instance_name> --models Room.json
    az dt model create -n <ADT_instance_name> --models Floor.json
    ```
    
    A saída de cada comando mostrará informações sobre o modelo carregado com sucesso.

    >[!TIP]
    >Também pode carregar todos os modelos dentro de um diretório ao mesmo tempo, utilizando a `--from-directory` opção para o modelo criar comando. Para obter mais informações, consulte [os parâmetros opcionais para *o modelo az dt criar*](/cli/azure/dt/model#az_dt_model_create-optional-parameters).

1. Verifique se os modelos foram criados com o comando [**da lista de modelos az dt,**](/cli/azure/dt/model#az_dt_model_list) como mostrado abaixo. Isto irá imprimir uma lista de todos os modelos que foram enviados para a instância Azure Digital Twins com toda a sua informação. 

    ```azurecli-interactive
    az dt model list -n <ADT_instance_name> --definition
    ```
    
    Procure o modelo *de quarto* editado nos resultados:
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Screenshot de Cloud Shell mostrando o resultado do comando da lista de modelos, que inclui o modelo de Quarto atualizado." lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>Erros

O CLI também lida com erros do serviço. 

Re-executar o `az dt model create` comando para tentar re-carregar um dos mesmos modelos que acabou de carregar, pela segunda vez:

```azurecli-interactive
az dt model create -n <ADT_instance_name> --models Room.json
```

Como os modelos não podem ser substituídos, este irá agora devolver um código de erro de `ModelIdAlreadyExists` .

## <a name="create-digital-twins"></a>Criar duplos digitais

Agora que alguns modelos foram enviados para a sua instância Azure Digital Twins, pode criar [**gémeos digitais**](concepts-twins-graph.md) com base nas definições do modelo. Os gémeos digitais representam as entidades dentro do seu ambiente de negócios — coisas como sensores numa quinta, quartos num edifício ou luzes num carro. 

Para criar um gémeo digital, usa-se o comando [**az dt twin.**](/cli/azure/dt/twin#az_dt_twin_create) Deve fazer referência ao modelo em que o gémeo se baseia e pode definir opcionalmente valores iniciais para quaisquer propriedades do modelo. Não tens de passar nenhuma informação sobre relacionamentos nesta fase.

1. Executar este código na Cloud Shell para criar vários gémeos, com base no modelo *Room* que atualizou anteriormente e outro modelo, *Floor*. Lembre-se que *o Quarto* tem três propriedades, para que possa fornecer argumentos com os valores iniciais para estes. (Inicializar os valores de propriedade é opcional em geral, mas são necessários para este tutorial.)

    ```azurecli-interactive
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":"80", "HumidityLevel":"60"}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > Se estiver a utilizar a Cloud Shell no ambiente PowerShell, poderá ter de escapar aos caracteres da marcação de citação para que o `--properties` valor JSON seja analisado corretamente. Com esta edição, os comandos para criar os gémeos da sala são assim:
    >
    > ```azurecli-interactive
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > Isto reflete-se na imagem abaixo.
    
    A saída de cada comando mostrará informações sobre o gémeo criado com sucesso (incluindo propriedades para os gémeos do quarto que foram inicializados com eles).

1. Pode verificar se os gémeos foram criados com o comando [**de consulta az dt twin,**](/cli/azure/dt/twin#az_dt_twin_query) como mostrado abaixo. A consulta mostrada encontra todos os gémeos digitais na sua instância Azure Digital Twins.
    
    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```
    
    Procure o *quarto 0,* *sala1,* *piso 0,* e *1 4º andar* nos resultados. Aqui está um excerto mostrando parte do resultado desta consulta.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Screenshot de Cloud Shell mostrando resultado parcial de consulta dupla, incluindo quarto0 e quarto1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>Modificar um gémeo digital

Também pode modificar as propriedades de um gémeo que criou. 

1. Executar este comando [**de atualização az dt twin**](/cli/azure/dt/twin#az_dt_twin_update) para mudar o RoomName da *Sala0* para o *PresidentialSuite:* 

    ```azurecli-interactive
    az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > Se estiver a utilizar a Cloud Shell no ambiente PowerShell, poderá ter de escapar aos caracteres da marcação de citação para que o `--json-patch` valor JSON seja analisado corretamente. Com esta edição, o comando para atualizar o gémeo é assim:
    >
    > ```azurecli-interactive
    > az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > Isto reflete-se na imagem abaixo.
    
    A saída deste comando mostrará as informações atuais do gémeo, e deverá ver o novo valor para o `RoomName` resultado.

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="Screenshot de Cloud Shell mostrando o resultado do comando de atualização, que inclui um RoomName de PresidentialSuite." lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. Pode verificar se a atualização foi bem sucedida executando o comando [**de show az dt twin**](/cli/azure/dt/twin#az_dt_twin_show) para ver as informações da *sala0:*

    ```azurecli-interactive
    az dt twin show -n <ADT_instance_name> --twin-id room0
    ```
    
    A saída deve refletir o nome atualizado.

## <a name="create-a-graph-by-adding-relationships"></a>Criar um gráfico adicionando relacionamentos

Em seguida, pode criar algumas **relações** entre estes gémeos, para ligá-los a um [**gráfico gémeo.**](concepts-twins-graph.md) Os gráficos gémeos são usados para representar um ambiente inteiro. 

Os tipos de relacionamentos que pode criar de um gémeo para outro são definidos dentro dos [modelos](#model-a-physical-environment-with-dtdl) que carregou anteriormente. A [definição do modelo *para Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) especifica que os pisos podem ter um tipo de relação chamado *contém*. Isto permite criar uma relação de tipo *contém* de cada *piso* gémeo para a sala correspondente que contém.

Para adicionar uma relação, use a [**relação gémea az dt criar**](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_create) comando. Especifique o gémeo de onde a relação está a vir, o tipo de relacionamento, e o gémeo a que a relação está ligada. Por último, dê à relação uma identificação única. Se uma relação foi definida como tendo propriedades, você pode inicializar as propriedades da relação neste comando também.

1. Executar o seguinte código para adicionar uma relação de tipo *contém* de cada um dos gémeos *Floor* que criou anteriormente para o *quarto* gémeo correspondente. As relações são nomeadas *relação0* e *relação1.*

    ```azurecli-interactive
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >A relação *contém* no modelo [ *Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) também foi definida com duas propriedades, `ownershipUser` `ownershipDepartment` e, assim, você também pode fornecer argumentos com os valores iniciais para estes quando você cria as relações.
    > Para criar uma relação com estas propriedades inicializadas, adicione a `--properties` opção a qualquer um dos comandos acima, como este:
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > Se estiver a utilizar a Cloud Shell no ambiente PowerShell, poderá ter de escapar aos caracteres da marcação de citação para que o `--properties` valor JSON seja analisado corretamente.
    
    A saída de cada comando mostrará informações sobre a relação criada com sucesso.

1. Pode verificar as relações com qualquer um dos seguintes comandos, que consultam as relações na sua instância Azure Digital Twins.
    * Para ver todas as relações saindo de cada andar (vendo as relações de um lado):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor0
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor1
        ```
    * Para ver todas as relações que chegam a cada quarto (vendo a relação do lado "do outro):):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id room0 --incoming
        az dt twin relationship list -n <ADT_instance_name> --twin-id room1 --incoming
        ```
    * Procurar estas relações individualmente, por ID:
        ```azurecli-interactive
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor1 --relationship-id relationship1
        ```

As gémeas e relações que criou neste formato tutorial são o seguinte gráfico conceptual:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Um diagrama mostrando um gráfico conceptual. o piso 0 está ligado através da relação0 com o quarto0, e o piso 1 está ligado através da relação1 com o quarto1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Consultar o gráfico gémeo para responder a questões ambientais

Uma das principais características da Azure Digital Twins é a capacidade [de consultar](concepts-query-language.md) o seu gráfico gémeo de forma fácil e eficiente para responder a questões sobre o seu ambiente. No CLI Azure, isto é feito com o comando [**de consulta az dt twin.**](/cli/azure/dt/twin#az_dt_twin_query)

Execute as seguintes consultas na Cloud Shell para responder a algumas perguntas sobre o ambiente da amostra.

1. **Quais são todas as entidades do meu ambiente representadas no Azure Digital Twins?** (consulta a todos)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```

    Isto permite-lhe fazer um balanço do seu ambiente num ápice, e certifique-se de que tudo está representado como gostaria que estivesse dentro da Azure Digital Twins. O resultado disto é uma saída contendo cada gémeo digital com os seus detalhes. Aqui está um excerto:

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Screenshot de Cloud Shell mostrando resultado parcial de consulta dupla, incluindo quarto0 e quarto1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >Pode reconhecer que este é o mesmo comando que usou na secção [*Criar gémeos digitais*](#create-digital-twins) mais cedo para encontrar todas as Gémeas Digitais Azure no caso.

1. **Quais são todos os quartos do meu ambiente?** (consulta por modelo)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    Pode restringir a sua consulta a gémeos de um certo tipo, para obter informações mais específicas sobre o que está representado. O resultado deste mostra *a sala0* e *a sala1,* mas **não** mostra o *piso 0* ou *o piso 1* (uma vez que são pisos, não quartos).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Screenshot de Cloud Shell mostrando o resultado da consulta do modelo, que inclui apenas o quarto0 e o quarto1." lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. **Quais são todos os quartos no *andar0?*** (consulta por relação)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    Você pode consultar com base em relacionamentos no seu gráfico, para obter informações sobre como os gémeos estão conectados ou para restringir a sua consulta a uma determinada área. Só *o quarto 0* é no *andar 0,* por isso é o único quarto no resultado.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Screenshot de Cloud Shell mostrando o resultado da consulta de relacionamento, que inclui o quarto0." lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > Note que o ID de um gémeo (como o *piso 0* na consulta acima) é consultado utilizando o campo de metadados `$dtId` . 
    >
    >Ao usar a Cloud Shell para executar uma consulta com campos de metadados como este que começa `$` com, você deve escapar `$` com um backtick para deixar cloud Shell saber que não é uma variável e deve ser consumido como um literal no texto de consulta. Isto reflete-se na imagem acima.

1. **Quais são os gémeos no meu ambiente com uma temperatura superior a 75?** (consulta por propriedade)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    Você pode consultar o gráfico com base em propriedades para responder a uma variedade de perguntas, incluindo descobrir outliers no seu ambiente que podem precisar de atenção. Outros operadores de comparação *<* *>* (, *=* , , , ou *!=*) também são apoiados. *quarto1* aparece nos resultados aqui, porque tem uma temperatura de 80.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Screenshot de Cloud Shell mostrando o resultado da consulta de propriedade, que inclui apenas o quarto1." lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **Quais são todos os quartos no *andar0* com uma temperatura superior a 75?** (consulta composta)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    Também pode combinar as consultas anteriores como em SQL, utilizando operadores de combinação `AND` `OR` como, `NOT` . . Esta consulta `AND` utiliza-se para tornar a consulta anterior sobre temperaturas duplas mais específica. O resultado agora só inclui quartos com temperaturas acima dos 75 que estão no *chão0*— o que, neste caso, não é nenhum deles. O resultado está vazio.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Screenshot de Cloud Shell mostrando o resultado de consulta composta, que não inclui quaisquer itens." lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de completar este tutorial, pode escolher quais os recursos que gostaria de remover, dependendo do que gostaria de fazer a seguir.

* **Se pretende continuar para o próximo tutorial,** pode manter os recursos que criou aqui e reutilizar a instância Azure Digital Twins sem esclarecer nada no meio.

* **Se quiser continuar a usar a instância Azure Digital Twins, mas limpe todos os seus modelos, gémeos e relacionamentos**, pode usar a [**relação az dt twin delete**](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_delete), [**az dt twin delete**](/cli/azure/dt/twin#az_dt_twin_delete), e [**az dt model apagar**](/cli/azure/dt/model#az_dt_model_delete) comandos para limpar as relações, gémeos e modelos no seu caso, respectivamente.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Também pode querer eliminar os ficheiros de modelo que criou na sua máquina local.

## <a name="next-steps"></a>Passos seguintes 

Neste tutorial, começou com a Azure Digital Twins construindo um gráfico no seu caso usando o Azure CLI. Criaste modelos, gémeos digitais e relacionamentos para formar um gráfico. Também fez algumas perguntas no gráfico, para ter uma ideia do tipo de perguntas que a Azure Digital Twins pode responder sobre um ambiente.

Continue até ao próximo tutorial para combinar Azure Digital Twins com outros serviços Azure para completar um cenário de ponta a ponta orientado para dados:
> [!div class="nextstepaction"]
> [*Tutorial: Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md)