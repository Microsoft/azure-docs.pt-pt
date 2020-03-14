---
title: 'Tutorial: Implementar um ambiente de pré-visualização e gráfico espacial - Azure Digital Twins Microsoft Docs'
description: Saiba como implementar a sua instância do duplos Digital do Azure e configurar os seus recursos espaciais, utilizando os passos neste tutorial.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 878b64fe6dd491adbb61c4c74cf4a5fc039858cd
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371414"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Tutorial: Implementar A pré-visualização de gémeos digitais Azure e configurar um gráfico espacial

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Pode utilizar o serviço de pré-visualização de gémeos digitais Azure para reunir pessoas, lugares e dispositivos num sistema espacial coerente. Esta série de tutoriais demonstra como utilizar o gémeos Digital do Azure para detetar a ocupação da sala com condições ideais de qualidade de temperatura e ar. 

Estes tutoriais irão guiá-lo por meio de uma aplicação de consola .NET para criar um cenário de um edifício de escritórios. A criação tem vários andares e salas de cada andar. Os ambientes de contenham os dispositivos com sensores anexados que Deteta movimento, temperatura ambiente e a qualidade de ar. 

Aprenderá como replicar as áreas físicas e entidades no prédio como objetos digital com o serviço de duplos Digital do Azure. Irá simular eventos de dispositivos através de outra aplicação de consola. Em seguida, irá aprender a monitorizar os eventos provenientes destas áreas físicas e entidades em tempo real. 

O administrador dos escritórios pode utilizar estas informações para ajudar os funcionários que trabalham neste edifício a reservarem salas de reuniões com as condições ideais. Um Gestor de recursos do office pode utilizar a configuração para obter tendências de utilização dos ambientes e monitorizar as condições de trabalho para fins de manutenção.

No primeiro tutorial da série, vai aprender a:

> [!div class="checklist"]
> * Implemente o Digital duplos.
> * Conceder permissões à sua aplicação.
> * Modificar uma aplicação de exemplo duplos Digital.
> * Aprovisione a sua criação.

Estes tutoriais utilizam e modificam os mesmos exemplos que o artigo [Quickstart to find available rooms](quickstart-view-occupancy-dotnet.md) (Início rápido para descobrir salas disponíveis) utiliza, para proporcionar uma cobertura mais detalhada e aprofundada dos conceitos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma conta Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

- O SDK .NET Core. Os exemplos de duplos Digital do Azure utilizados nestes tutoriais são escritos em C#. Certifique-se de instalar a [versão 2.1.403 do Core SDK .NET ou posteriormente](https://www.microsoft.com/net/download) na sua máquina de desenvolvimento para construir e executar a amostra. Verifique se a versão certa está instalada na sua máquina executando `dotnet --version` numa janela de comando.

- [Visual Studio Code](https://code.visualstudio.com/), para explorar o código de exemplo. 

## <a name="deploy-digital-twins"></a>Implementar o Digital Twins

Utilize os passos nesta secção para criar uma nova instância do serviço duplos Digital do Azure. Pode ser criada apenas uma instância por subscrição. Se já tiver um executado, avance para a secção seguinte. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Conceder permissões à sua aplicação

A Digital Twins utiliza o [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) para controlar o [acesso de leitura/escrita](../active-directory/develop/v2-permissions-and-consent.md) ao serviço. Qualquer aplicativo que precisa para ligar à sua instância de duplos digitais têm de estar registado com o Azure AD. Os passos nesta secção explicam como registar o exemplo de aplicação.

Se já tiver um registo de aplicações, pode ser reutilizado em seu exemplo. No entanto, leia esta secção para ter a certeza de que esse registo está devidamente configurado.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Configurar o exemplo de duplos Digital

Esta secção acompanha-o através de uma aplicação Azure Digital Twins que comunica com as APIs REST [Das Gémeas Digitais](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Transferir o exemplo

Se já tiver transferido os exemplos para [Quickstart to find available rooms](quickstart-view-occupancy-dotnet.md) (Início rápido para descobrir salas disponíveis), pode ignorar estes passos.

1. Descarregue as [amostras De Gémeos Digitais .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Extraia os conteúdos da pasta zip no seu computador.

### <a name="explore-the-sample"></a>Explorar o exemplo

Na pasta de amostraextra, abra o ficheiro **digital-twins-samples-csharp\digital-twins-samples.code-workspace** in Visual Studio Code. Inclui dois projetos:

* Você pode usar a amostra de **fornecimento de ocupação rápida** para configurar e fornecer um gráfico de inteligência [espacial](concepts-objectmodel-spatialgraph.md#digital-twins-object-models). Este gráfico é a imagem digitais de seus espaços de físicos e os recursos nas mesmas. Usa um modelo de [objeto,](concepts-objectmodel-spatialgraph.md#digital-twins-object-models)que define objetos para um edifício inteligente. Para obter uma lista completa de objetos de Gémeos Digitais e APIs REST, visite [esta documentação REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger) ou o URL da API de Gestão que foi criado para [a sua instância.](#deploy-digital-twins)

   Para explorar a amostra para entender como comunica com a sua instância De Gémeos Digitais, pode começar com a pasta **src\actions.** Os ficheiros nesta pasta implementam os comandos que utilizará estes tutoriais:
    - O ficheiro **provisionSample.cs** mostra como fornecer o seu gráfico espacial.
    - O ficheiro **getSpaces.cs** obtém informações sobre os espaços aprovisionados.
    - O ficheiro **getAvailableAndFreshSpaces.cs** obtém os resultados de uma função personalizada chamada função definida pelo utilizador.
    - O ficheiro **createEndpoints.cs** cria pontos finais para interagir com outros serviços.

* A simulação de conectividade **com** dispositivos simula dados de sensores e envia-os para o centro IoT que está aprovisionado para a sua instância de Gémeos Digitais. Você usará esta amostra [no próximo tutorial depois de fornecer o seu gráfico espacial.](tutorial-facilities-udf.md#simulate-sensor-data) Os identificadores de sensor e do dispositivo que utiliza para configurar este exemplo devem ser o mesmo que o que irá utilizar para aprovisionar o seu gráfico.

### <a name="configure-the-provisioning-sample"></a>Configurar o exemplo de aprovisionamento

1. Abra uma janela de comando e vá para o exemplo transferido. Execute o seguinte comando:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Execute o comando seguinte para restaurar as dependências para o exemplo de projeto:

    ```cmd/sh
    dotnet restore
    ```

1. No Visual Studio Code, abra o ficheiro [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) no projeto de **início rápido de ocupação.** Atualize os valores seguintes:
   * **ClientId**: Introduza o ID de aplicação do registo da sua aplicação Azure AD. Notou este ID na secção onde [definiu permissões de aplicações.](#grant-permissions-to-your-app)
   * **Inquilino**: Insira a identificação do seu [inquilino Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) Também observou este ID na secção onde [definiu permissões de aplicações.](#grant-permissions-to-your-app)
   * **BaseUrl**: introduza o URL da instância do Digital Twins. Para obter este URL, substitua os espaços reservados deste URL por valores para a sua instância: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Também pode obter este URL modificando o URL da API de Gestão [da secção de implementação](#deploy-digital-twins). Substitua **o swagger/** por **api/v1.0/** .

1. Reveja uma lista de funcionalidades de Gémeos Digitais que pode explorar utilizando a amostra. Execute o seguinte comando:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>Compreender o processo de aprovisionamento

Esta secção mostra de que forma é que o exemplo aprovisiona um gráfico espacial de um edifício.

No Visual Studio Code, navegue para a pasta de **ocupação-quickstart\src\actions** e abra o ficheiro **provisionSample.cs**. Repare na função seguinte:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}
```

Esta função utiliza [o provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) na mesma pasta. Abra este arquivo e tome nota da hierarquia de um edifício de escritórios: *Local,* *Piso,* *Área,* e *Salas.* Qualquer um destes espaços físicos pode ter *dispositivos* e *sensores*. Cada entrada tem um `type`pré-definido &mdash;por exemplo, Piso, Quarto.

O ficheiro **de yaml** da amostra mostra um gráfico espacial que usa o modelo de objeto sintetizador `Default` Digital Twins. Esse modelo fornece os nomes genéricos para a maioria dos tipos. Os nomes genéricos são suficientes para um edifício. Exemplos são temperatura para SensorDataType e mapeiam para SpaceBlobType. Um tipo de espaço de exemplo é sala com subtipos FocusRoom ConferenceRoom e assim por diante. 

Se tiver de criar um gráfico espacial para outro tipo de local, como uma fábrica, poderá ter de utilizar um modelo de objetos diferente. Pode descobrir quais os modelos disponíveis para utilização executando o comando `dotnet run GetOntologies` na linha de comando para a amostra de provisionamento. 

Para obter mais informações sobre gráficos espaciais e modelos de objetos, leia [understanding digital twins objeto sôm-se e gráfico](concepts-objectmodel-spatialgraph.md)de inteligência espacial .

### <a name="modify-the-sample-spatial-graph"></a>Modificar o gráfico de geográficos de exemplo

O ficheiro **provisionSample.yaml** contém os seguintes nódosos:

- **recursos**: O nó `resources` cria um recurso Azure IoT Hub para comunicar com os dispositivos na sua configuração. Um hub IoT no nó raiz do seu gráfico pode comunicar com todos os dispositivos e sensores no seu gráfico.  

- **spaces**: no modelo de objetos do Digital Twins, `spaces` representa as localizações físicas. Cada espaço tem um `Type`&mdash;por exemplo, Região, Local ou&mdash;de Clientes e um `Name`amigável. Espaços podem pertencer a outros espaços, criação de uma estrutura hierárquica. O ficheiro de provisionSample.yaml tem um gráfico geográfico de um edifício imaginário. Observe a nidificação lógica de espaços de tipo `Floor` dentro `Venue`, `Area` em um chão, e `Room` nós em uma área. 

- **devices**: os espaços podem conter `devices`, que são entidades físicas ou virtuais que gerem vários sensores. Por exemplo, um dispositivo pode ser um telefone de um utilizador, uma cápsula de sensor Raspberry Pi ou um portal. No edifício imaginário do exemplo, repare que a sala denominada **Focus Room** contém um dispositivo **Raspberry Pi 3 A1**. Cada nó do dispositivo é identificado por um `hardwareId` exclusivo, que está codificado no exemplo. Para configurar este exemplo para uma produção real, substitua estes valores pelos da sua configuração.  

- **sensores**: Um dispositivo pode conter vários `sensors`. Eles podem detectar e registo de alterações físicas como temperatura, o motion e o nível da bateria. Cada nó do sensor é identificado exclusivamente por um `hardwareId`, que está codificado aqui. Para um aplicativo real, substitua as utilizando os identificadores exclusivos dos sensores na sua configuração. O ficheiro provisionSample.yaml tem dois sensores para gravar *Movimento* e *Dióxido de Carbono*. Adicione outro sensor para registar a *Temperature* (Temperatura) ao adicionar as linhas seguintes, abaixo das do sensor CarbonDioxide. Estes são fornecidos em provisionSample.yaml como linhas comentadas. Pode descocomentá-los removendo o `#` carácter na frente de cada linha. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Certifique-se de que as teclas `dataType` e `hardwareId` estão alinhadas com as declarações acima deste corte. Confirme também que o editor não substitui espaços por tabulações. 

Guarde e feche o ficheiro de provisionSample.yaml. O próximo tutorial, irá adicionar obter mais informações para este ficheiro e, em seguida, Aprovisione a construção de exemplo duplos Digital do Azure.

> [!TIP]
> Pode ver e modificar o seu gráfico espacial utilizando o [Telespectador digital Twins Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender parar a explorar duplos Digital do Azure neste momento, fique à vontade eliminar recursos criados neste tutorial:

1. A partir do menu esquerdo no [portal Azure](https://portal.azure.com), selecione **Todos os recursos,** selecione o seu grupo de recursos Digital Twins e selecione **Delete**.

    > [!TIP]
    > Se teve problemas ao eliminar a instância de duplos Digital, uma atualização de serviço capacidade foi implementada com a correção. Volte a tentar eliminar a instância.

1. Se necessário, elimine a aplicação de exemplo no seu computador de trabalho.

## <a name="next-steps"></a>Passos seguintes

Para saber como implementar uma lógica personalizada para monitorizar as condições em seu exemplo de criação, vá para o próximo tutorial da série: 
> [!div class="nextstepaction"]
> [Tutorial: Provision your building and monitor working conditions](tutorial-facilities-udf.md) (Tutorial: Aprovisionar o edifício e monitorizar as condições de funcionamento)