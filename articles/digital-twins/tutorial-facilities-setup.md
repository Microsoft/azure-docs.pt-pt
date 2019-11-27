---
title: 'Tutorial: implantar um ambiente de visualização e um grafo espacial – gêmeos digital do Azure | Microsoft Docs'
description: Saiba como implementar a sua instância do duplos Digital do Azure e configurar os seus recursos espaciais, utilizando os passos neste tutorial.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: 20174a4eafb4e72fb62eeff6df2d129b91016b9e
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383031"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Tutorial: implantar o Azure digital gêmeos Preview e configurar um grafo espacial

Você pode usar o serviço de visualização do gêmeos digital do Azure para reunir pessoas, lugares e dispositivos em um sistema espacial coerente. Esta série de tutoriais demonstra como utilizar o gémeos Digital do Azure para detetar a ocupação da sala com condições ideais de qualidade de temperatura e ar. 

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

- Uma subscrição do Azure. Se ainda não tiver uma conta do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- O SDK .NET Core. Os exemplos de duplos Digital do Azure utilizados nestes tutoriais são escritos em C#. Certifique-se de instalar [SDK do .NET Core versão 2.1.403 ou posterior](https://www.microsoft.com/net/download) em seu computador de desenvolvimento para compilar e executar o exemplo. Verifique se a versão correta está instalada em seu computador executando `dotnet --version` em uma janela de comando.

- [Visual Studio Code](https://code.visualstudio.com/), para explorar o código de exemplo. 

## <a name="deploy-digital-twins"></a>Implementar o Digital Twins

Utilize os passos nesta secção para criar uma nova instância do serviço duplos Digital do Azure. Pode ser criada apenas uma instância por subscrição. Se já tiver um executado, avance para a secção seguinte. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Conceder permissões à sua aplicação

O digital gêmeos usa [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (AD do Azure) para controlar o [acesso de leitura/gravação](../active-directory/develop/v1-permissions-and-consent.md) ao serviço. Qualquer aplicativo que precisa para ligar à sua instância de duplos digitais têm de estar registado com o Azure AD. Os passos nesta secção explicam como registar o exemplo de aplicação.

Se já tiver um registo de aplicações, pode ser reutilizado em seu exemplo. No entanto, leia esta secção para ter a certeza de que esse registo está devidamente configurado.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Configurar o exemplo de duplos Digital

Esta seção orienta você por meio de um aplicativo gêmeos digital do Azure que se comunica com as [APIs REST do gêmeos digital](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Transferir o exemplo

Se já tiver transferido os exemplos para [Quickstart to find available rooms](quickstart-view-occupancy-dotnet.md) (Início rápido para descobrir salas disponíveis), pode ignorar estes passos.

1. Baixe os [exemplos do digital gêmeos .net](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Extraia os conteúdos da pasta zip no seu computador.

### <a name="explore-the-sample"></a>Explorar o exemplo

Na pasta de exemplo extraída, abra o arquivo **digital-Twins-Samples-csharp\digital-Twins-Samples.Code-Workspace** em Visual Studio Code. Inclui dois projetos:

* Você pode usar o exemplo de provisionamento de **ocupação-início rápido** para configurar e provisionar um [grafo de inteligência espacial](concepts-objectmodel-spatialgraph.md#digital-twins-object-models). Este gráfico é a imagem digitais de seus espaços de físicos e os recursos nas mesmas. Ele usa um [modelo de objeto](concepts-objectmodel-spatialgraph.md#digital-twins-object-models), que define objetos para um prédio inteligente. Para obter uma lista completa de objetos gêmeos digitais e APIs REST, visite [esta documentação da API REST](https://docs.westcentralus.azuresmartspaces.net/management/swagger) ou a URL da API de gerenciamento que foi criada para [sua instância](#deploy-digital-twins).

   Para explorar o exemplo para ver como ele se comunica com sua instância de gêmeos digital, você pode começar com a pasta **src\actions** . Os ficheiros nesta pasta implementam os comandos que utilizará estes tutoriais:
    - O arquivo **provisionSample.cs** mostra como provisionar o grafo espacial.
    - O arquivo **getSpaces.cs** Obtém informações sobre os espaços provisionados.
    - O arquivo **getAvailableAndFreshSpaces.cs** Obtém os resultados de uma função personalizada chamada de função definida pelo usuário.
    - O arquivo **createEndpoints.cs** cria pontos de extremidade para interagir com outros serviços.

* O **dispositivo de** exemplo de simulação de conectividade simula os dados do sensor e os envia para o Hub IOT provisionado para sua instância de gêmeos digital. Você usará este exemplo no [próximo tutorial depois de provisionar o grafo espacial](tutorial-facilities-udf.md#simulate-sensor-data). Os identificadores de sensor e do dispositivo que utiliza para configurar este exemplo devem ser o mesmo que o que irá utilizar para aprovisionar o seu gráfico.

### <a name="configure-the-provisioning-sample"></a>Configurar o exemplo de aprovisionamento

1. Abra uma janela de comando e vá para o exemplo transferido. Execute o seguinte comando:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Execute o comando seguinte para restaurar as dependências para o exemplo de projeto:

    ```cmd/sh
    dotnet restore
    ```

1. No Visual Studio Code, abra o arquivo [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) no projeto **ocupação-início rápido** . Atualize os valores seguintes:
   * **ClientID**: Insira a ID do aplicativo do registro do aplicativo do Azure AD. Você anotou essa ID na seção em que você [define as permissões do aplicativo](#grant-permissions-to-your-app).
   * **Locatário**: Insira a ID de diretório do seu [locatário do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Você também anotou essa ID na seção em que você [define as permissões do aplicativo](#grant-permissions-to-your-app).
   * **BaseUrl**: introduza o URL da instância do Digital Twins. Para obter essa URL, substitua os espaços reservados nesta URL pelos valores da sua instância: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Você também pode obter essa URL modificando a URL da API de gerenciamento da [seção de implantação](#deploy-digital-twins). Substitua **Swagger/** por **API/v 1.0/** .

1. Ver uma lista das funcionalidades de duplos Digital que pode ser explorado utilizando o exemplo. Execute o seguinte comando:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>Compreender o processo de aprovisionamento

Esta secção mostra de que forma é que o exemplo aprovisiona um gráfico espacial de um edifício.

Em Visual Studio Code, navegue até a pasta **occupancy-quickstart\src\actions** e abra o arquivo **provisionSample.cs**. Repare na função seguinte:

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

Essa função usa [provisionSample. YAML](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) na mesma pasta. Abra esse arquivo e observe a hierarquia de um edifício de escritórios: *local*, *andar*, *área*e *salas*. Qualquer um destes espaços físicos pode ter *dispositivos* e *sensores*. Cada entrada tem um `type`predefinido &mdash;por exemplo, piso, espaço.

O arquivo **YAML** de exemplo mostra um grafo espacial que usa o modelo de objeto `Default` digital gêmeos. Esse modelo fornece os nomes genéricos para a maioria dos tipos. Os nomes genéricos são suficientes para um edifício. Exemplos são temperatura para SensorDataType e mapeiam para SpaceBlobType. Um tipo de espaço de exemplo é sala com subtipos FocusRoom ConferenceRoom e assim por diante. 

Se tiver de criar um gráfico espacial para outro tipo de local, como uma fábrica, poderá ter de utilizar um modelo de objetos diferente. Você pode descobrir quais modelos estão disponíveis para uso executando o comando `dotnet run GetOntologies` na linha de comando para o exemplo de provisionamento. 

Para obter mais informações sobre gráficos espaciais e modelos de objetos, leia [noções básicas sobre modelos de objeto gêmeos digital e grafo de inteligência espacial](concepts-objectmodel-spatialgraph.md).

### <a name="modify-the-sample-spatial-graph"></a>Modificar o gráfico de geográficos de exemplo

O arquivo **provisionSample. YAML** contém os seguintes nós:

- **recursos**: o nó `resources` cria um recurso do Hub IOT do Azure para se comunicar com os dispositivos na sua configuração. Um hub IoT no nó raiz do seu gráfico pode comunicar com todos os dispositivos e sensores no seu gráfico.  

- **spaces**: no modelo de objetos do Digital Twins, `spaces` representa as localizações físicas. Cada espaço tem um `Type`&mdash;por exemplo, região, local ou cliente&mdash;e um `Name`amigável. Espaços podem pertencer a outros espaços, criação de uma estrutura hierárquica. O ficheiro de provisionSample.yaml tem um gráfico geográfico de um edifício imaginário. Observe o aninhamento lógico de espaços do tipo `Floor` em `Venue`, `Area` em um andar e `Room` nós em uma área. 

- **devices**: os espaços podem conter `devices`, que são entidades físicas ou virtuais que gerem vários sensores. Por exemplo, um dispositivo pode ser um telefone do utilizador, um pod de sensor Raspberry Pi ou um gateway. No edifício imaginário do exemplo, repare que a sala denominada **Focus Room** contém um dispositivo **Raspberry Pi 3 A1**. Cada nó do dispositivo é identificado por um `hardwareId` exclusivo, que está codificado no exemplo. Para configurar este exemplo para uma produção real, substitua estes valores pelos da sua configuração.  

- **sensores**: um dispositivo pode conter vários `sensors`. Eles podem detectar e registo de alterações físicas como temperatura, o motion e o nível da bateria. Cada nó do sensor é identificado exclusivamente por um `hardwareId`, que está codificado aqui. Para um aplicativo real, substitua as utilizando os identificadores exclusivos dos sensores na sua configuração. O arquivo provisionSample. YAML tem dois sensores para registrar o *Motion* e o *CarbonDioxide*. Adicione outro sensor para registar a *Temperature* (Temperatura) ao adicionar as linhas seguintes, abaixo das do sensor CarbonDioxide. Tenha em atenção que estes são fornecidos no provisionSample.yaml como linhas Comentada. Você pode remover os comentários removendo o `#` caractere na frente de cada linha. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Verifique se as chaves `dataType` e `hardwareId` estão alinhadas com as instruções acima deste trecho de código. Confirme também que o editor não substitui espaços por tabulações. 

Guarde e feche o ficheiro de provisionSample.yaml. O próximo tutorial, irá adicionar obter mais informações para este ficheiro e, em seguida, Aprovisione a construção de exemplo duplos Digital do Azure.

> [!TIP]
> Você pode exibir e modificar o grafo espacial usando o [Visualizador do grafo do gêmeos digital do Azure](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender parar a explorar duplos Digital do Azure neste momento, fique à vontade eliminar recursos criados neste tutorial:

1. No menu à esquerda na [portal do Azure](https://portal.azure.com), selecione **todos os recursos**, selecione o grupo de recursos digital gêmeos e selecione **excluir**.

    > [!TIP]
    > Se teve problemas ao eliminar a instância de duplos Digital, uma atualização de serviço capacidade foi implementada com a correção. Volte a tentar eliminar a instância.

1. Se necessário, elimine a aplicação de exemplo no seu computador de trabalho.

## <a name="next-steps"></a>Passos seguintes

Para saber como implementar uma lógica personalizada para monitorizar as condições em seu exemplo de criação, vá para o próximo tutorial da série: 
> [!div class="nextstepaction"]
> [Tutorial: Provision your building and monitor working conditions](tutorial-facilities-udf.md) (Tutorial: Aprovisionar o edifício e monitorizar as condições de funcionamento)