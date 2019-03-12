---
title: 'Tutorial: Implementar o Azure Digital Twins | Microsoft Docs'
description: Saiba como implementar a sua instância do duplos Digital do Azure e configurar os seus recursos espaciais, utilizando os passos neste tutorial.
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: dkshir
ms.openlocfilehash: 096df62305af91ac85ce9ddbcff5b0160aaa4e8a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537466"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>Tutorial: Implementar o Azure Digital Twins e configurar um gráfico espacial

Pode utilizar o serviço de duplos Digital do Azure para reunir as pessoas, lugares e dispositivos num sistema geográfico coerente. Esta série de tutoriais demonstra como utilizar o gémeos Digital do Azure para detetar a ocupação da sala com condições ideais de qualidade de temperatura e ar. 

Estes tutoriais irão guiá-lo por meio de uma aplicação de consola .NET para criar um cenário de um edifício de escritórios. A criação tem vários andares e salas de cada andar. Os ambientes de contenham os dispositivos com sensores anexados que Deteta movimento, temperatura ambiente e a qualidade de ar. 

Aprenderá como replicar as áreas físicas e entidades no prédio como objetos digital com o serviço de duplos Digital do Azure. Irá simular eventos de dispositivos através de outra aplicação de consola. Em seguida, irá aprender a monitorizar os eventos provenientes destas áreas físicas e entidades em tempo real. 

O administrador dos escritório pode utilizar estas informações para ajudar os funcionários que trabalham neste edifício a reservarem salas de reuniões com as condições ideais. Um Gestor de recursos do office pode utilizar a configuração para obter tendências de utilização dos ambientes e monitorizar as condições de trabalho para fins de manutenção.

No primeiro tutorial da série, vai aprender a:

> [!div class="checklist"]
> * Implemente o Digital duplos.
> * Conceder permissões à sua aplicação.
> * Modificar uma aplicação de exemplo duplos Digital.
> * Aprovisione a sua criação.

Estes tutoriais utilizam e modificam os mesmos exemplos que o artigo [Quickstart to find available rooms](quickstart-view-occupancy-dotnet.md) (início rápido para descobrir salas disponíveis) utiliza, para proporcionar uma cobertura mais detalhada e aprofundada dos conceitos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se ainda não tiver uma conta do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- O SDK .NET Core. Os exemplos de duplos Digital do Azure utilizados nestes tutoriais são escritos em C#. Certifique-se instalar [.NET Core SDK versão 2.1.403 ou posterior](https://www.microsoft.com/net/download) no computador de desenvolvimento para compilar e executar o exemplo. Verifique se a versão correta está instalada no seu computador através da execução `dotnet --version` numa janela de comando.

- [Visual Studio Code](https://code.visualstudio.com/), para explorar o código de exemplo. 

<a id="deploy"></a>

## <a name="deploy-digital-twins"></a>Implementar o Digital Twins

Utilize os passos nesta secção para criar uma nova instância do serviço duplos Digital do Azure. Pode ser criada apenas uma instância por subscrição. Se já tiver um executado, avance para a secção seguinte. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

<a id="permissions"></a>

## <a name="grant-permissions-to-your-app"></a>Conceder permissões à sua aplicação

Utilizações de duplos digital [do Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) para controlar [acesso de leitura/escrita](../active-directory/develop/v1-permissions-and-consent.md) ao serviço. Qualquer aplicativo que precisa para ligar à sua instância de duplos digitais têm de estar registado com o Azure AD. Os passos nesta secção explicam como registar o exemplo de aplicação.

Se já tiver um registo de aplicações, pode ser reutilizado em seu exemplo. No entanto, leia esta secção para ter a certeza de que esse registo está devidamente configurado.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Configurar o exemplo de duplos Digital

Esta secção explica-lhe uma aplicação de duplos Digital do Azure que se comunica com o [APIs de REST de duplos Digital](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Transferir o exemplo

Se já tiver transferido os exemplos para [Quickstart to find available rooms](quickstart-view-occupancy-dotnet.md) (Início rápido para descobrir salas disponíveis), pode ignorar estes passos.

1. Transfira o [amostras do .NET de duplos Digital](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Extraia os conteúdos da pasta zip no seu computador.

### <a name="explore-the-sample"></a>Explorar o exemplo

Na pasta de exemplo extraídos, abra o ficheiro **digital-twins-samples-csharp\digital-twins-samples.code-workspace** no Visual Studio Code. Inclui dois projetos:

* Pode utilizar o exemplo de aprovisionamento **ocupação-quickstart** para configurar e aprovisionar um [gráfico de inteligência geográficos](concepts-objectmodel-spatialgraph.md#graph). Este gráfico é a imagem digitais de seus espaços de físicos e os recursos nas mesmas. Ele usa um [modelo de objeto](concepts-objectmodel-spatialgraph.md#model), que define objetos para um edifício inteligente. Para obter uma lista completa de objetos duplos Digital e REST APIs, visite [esta documentação da REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger) ou o URL da API de gestão que foi criada para [sua instância](#deploy).

   Para explorar o exemplo para ver como ele se comunica com a instância de duplos Digital, pode começar com o **src\actions** pasta. Os ficheiros nesta pasta implementam os comandos que utilizará estes tutoriais:
    - O **provisionSample.cs** ficheiro mostra como aprovisionar o seu gráfico espacial.
    - O **getSpaces.cs** ficheiro obtém informações sobre os espaços aprovisionados.
    - O **getAvailableAndFreshSpaces.cs** ficheiro obtém os resultados de uma função personalizada chamada uma função definida pelo utilizador.
    - O **createEndpoints.cs** ficheiro cria pontos de extremidade para interagir com outros serviços.

* O exemplo de simulação **conectividade do dispositivo** simula dados de sensores e envia-os para o hub IoT que está aprovisionado para a sua instância de duplos Digital. Irá utilizar este exemplo na [o próximo tutorial depois de aprovisionar o seu gráfico geográfico](tutorial-facilities-udf.md#simulate). Os identificadores de sensor e do dispositivo que utiliza para configurar este exemplo devem ser o mesmo que o que irá utilizar para aprovisionar o seu gráfico.

### <a name="configure-the-provisioning-sample"></a>Configurar o exemplo de aprovisionamento

1. Abra uma janela de comando e vá para o exemplo transferido. Execute o seguinte comando:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Execute o comando seguinte para restaurar as dependências para o exemplo de projeto:

    ```cmd/sh
    dotnet restore
    ```

1. No Visual Studio Code, abra a [appSettings](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) de ficheiros a **ocupação-quickstart** projeto. Atualize os valores seguintes:
   * **ClientId**: Introduza o ID da aplicação do seu registo de aplicação do Azure AD. Observou este ID na secção onde [definir permissões de aplicação](#permissions).
   * **Inquilino**: Introduza o ID de diretório da sua [inquilino do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Também observa que este ID na secção onde [definir permissões de aplicação](#permissions).
   * **BaseUrl**: Introduza o URL da sua instância de duplos Digital. Para obter este URL, substitua os marcadores de posição este URL com os valores para a sua instância: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Também pode obter este URL, modificando o URL da API de gestão do [a secção Implementação](#deploy). Substitua **swagger /** com **api/v1.0/**.

1. Ver uma lista das funcionalidades de duplos Digital que pode ser explorado utilizando o exemplo. Execute o seguinte comando:

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces"></a>

## <a name="understand-the-provisioning-process"></a>Compreender o processo de aprovisionamento

Esta secção mostra de que forma é que o exemplo aprovisiona um gráfico espacial de um edifício.

No Visual Studio Code, navegue para o **ocupação quickstart\src\actions** pasta e abra o ficheiro **provisionSample.cs**. Repare na função seguinte:

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

Esta função utiliza [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) na mesma pasta. Abrir este ficheiro e tenha em atenção a hierarquia de um edifício de escritórios: *Na localização*, *piso*, *área*, e *salas*. Qualquer um destes espaços físicos pode ter *dispositivos* e *sensores*. Cada entrada tem um predefinidos `type` &mdash;por exemplo, piso, salas.

O exemplo **yaml** ficheiro mostra um gráfico geográfico que utiliza o `Default` modelo de objeto de duplos Digital. Esse modelo fornece os nomes genéricos para a maioria dos tipos. Os nomes genéricos são suficientes para um edifício. Exemplos são temperatura para SensorDataType e mapeiam para SpaceBlobType. Um tipo de espaço de exemplo é sala com subtipos FocusRoom ConferenceRoom e assim por diante. 

Se tiver de criar um gráfico espacial para outro tipo de local, como uma fábrica, poderá ter de utilizar um modelo de objetos diferente. Pode descobrir quais modelos estão disponíveis para utilizar ao executar o comando `dotnet run GetOntologies` na linha de comandos para o exemplo de aprovisionamento. 

Para obter mais informações sobre gráficos geográficos e modelos de objeto, leia [duplos Digital de compreensão de objeto gráfico de inteligência geográficos e modelos de](concepts-objectmodel-spatialgraph.md).

### <a name="modify-the-sample-spatial-graph"></a>Modificar o gráfico de geográficos de exemplo

O **provisionSample.yaml** ficheiro contém os seguintes nós:

- **recursos**: O `resources` nó cria um recurso do IoT Hub do Azure para comunicar com os dispositivos em sua configuração. Um hub IoT no nó raiz do seu gráfico pode comunicar com todos os dispositivos e sensores no seu gráfico.  

- **spaces**: No modelo de objeto duplos Digital, `spaces` representam os locais físicos. Cada espaço tem um `Type` &mdash;por exemplo, região, local ou Customer&mdash;e amigável `Name`. Espaços podem pertencer a outros espaços, criação de uma estrutura hierárquica. O ficheiro de provisionSample.yaml tem um gráfico geográfico de um edifício imaginário. Tenha em atenção o aninhamento de lógico de espaços do tipo `Floor` dentro `Venue`, `Area` num chão, e `Room` nós numa área. 

- **dispositivos**: Podem conter espaços `devices`, que são entidades de físicas ou virtuais que gerir um número de sensores. Por exemplo, um dispositivo pode ser um telefone do utilizador, um pod de sensor Raspberry Pi ou um gateway. No edifício imaginário do exemplo, repare que a sala denominada **Focus Room** contém um dispositivo **Raspberry Pi 3 A1**. Cada nó do dispositivo é identificado por um `hardwareId` exclusivo, que está codificado no exemplo. Para configurar este exemplo para uma produção real, substitua estes valores pelos da sua configuração.  

- **Sensores**: Um dispositivo pode conter vários `sensors`. Eles podem detectar e registo de alterações físicas como temperatura, o motion e o nível da bateria. Cada nó do sensor é identificado exclusivamente por um `hardwareId`, que está codificado aqui. Para um aplicativo real, substitua as utilizando os identificadores exclusivos dos sensores na sua configuração. O ficheiro de provisionSample.yaml tem dois sensores para registrar *movimento* e *CarbonDioxide*. Adicione outro sensor para registar a *Temperature* (Temperatura) ao adicionar as linhas seguintes, abaixo das do sensor CarbonDioxide. Tenha em atenção que estes são fornecidos no provisionSample.yaml como linhas Comentada. Pode remover os comentários-los ao remover o `#` caráter no início de cada linha. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Certifique-se de que o `dataType` e `hardwareId` chaves se alinham com as instruções acima este trecho de código. Confirme também que o editor não substitui espaços por tabulações. 

Guarde e feche o ficheiro de provisionSample.yaml. O próximo tutorial, irá adicionar obter mais informações para este ficheiro e, em seguida, Aprovisione a construção de exemplo duplos Digital do Azure.

> [!TIP]
> Pode ver e modificar seu gráfico espacial com o [Visualizador de gráfico de duplos do Azure Digital](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender parar a explorar duplos Digital do Azure neste momento, fique à vontade eliminar recursos criados neste tutorial:

1. No menu do lado esquerdo da [portal do Azure](https://portal.azure.com), selecione **todos os recursos**, selecione o grupo de recursos digitais duplos e selecione **eliminar**.

    > [!TIP]
    > Se teve problemas ao eliminar a instância de duplos Digital, uma atualização de serviço capacidade foi implementada com a correção. Volte a tentar eliminar a instância.

1. Se necessário, elimine a aplicação de exemplo no seu computador de trabalho.

## <a name="next-steps"></a>Passos Seguintes

Para saber como implementar uma lógica personalizada para monitorizar as condições em seu exemplo de criação, vá para o próximo tutorial da série: 
> [!div class="nextstepaction"]
> [Tutorial: Aprovisionar a sua criação e o monitor trabalhar condições](tutorial-facilities-udf.md)
