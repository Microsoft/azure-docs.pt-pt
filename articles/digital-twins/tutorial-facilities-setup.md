---
title: 'Tutorial: Implementar um ambiente de pré-visualização e gráfico espacial - Azure Digital Twins Microsoft Docs'
description: Aprenda a implementar a sua instância de Gémeos Digitais Azure e configure os seus recursos espaciais utilizando os passos deste tutorial.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 878b64fe6dd491adbb61c4c74cf4a5fc039858cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79371414"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Tutorial: Implementar A pré-visualização de gémeos digitais Azure e configurar um gráfico espacial

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Pode utilizar o serviço de pré-visualização de gémeos digitais Azure para reunir pessoas, lugares e dispositivos num sistema espacial coerente. Esta série de tutoriais demonstra como usar as Gémeas Digitais Azure para detetar a ocupação do quarto com as melhores condições de temperatura e qualidade do ar. 

Estes tutoriais irão acompanhá-lo através de uma aplicação de consola .NET para construir um cenário de um edifício de escritórios. O edifício tem vários pisos e quartos dentro de cada andar. As salas contêm dispositivos com sensores anexados que detetam movimento, temperatura ambiente e qualidade do ar. 

Você vai aprender a replicar as áreas físicas e entidades do edifício como objetos digitais usando o serviço Azure Digital Twins. Simulará eventos de dispositivoutilizando outra aplicação de consola. Depois, aprenderá a monitorizar os eventos que vêm destas áreas físicas e entidades em tempo real. 

O administrador dos escritórios pode utilizar estas informações para ajudar os funcionários que trabalham neste edifício a reservarem salas de reuniões com as condições ideais. Um gerente de instalações de escritório pode usar a sua configuração para obter tendências de uso dos quartos, e monitorizar as condições de trabalho para fins de manutenção.

No primeiro tutorial da série, vai aprender a:

> [!div class="checklist"]
> * Implementar Gémeos Digitais.
> * Conceda permissões à sua aplicação.
> * Modifique uma aplicação de amostra de Gémeos Digitais.
> * Proteja o seu edifício.

Estes tutoriais utilizam e modificam os mesmos exemplos que o artigo [Quickstart to find available rooms](quickstart-view-occupancy-dotnet.md) (início rápido para descobrir salas disponíveis) utiliza, para proporcionar uma cobertura mais detalhada e aprofundada dos conceitos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma conta Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

- O Núcleo .NET SDK. As amostras de Gémeos Digitais Azure utilizadas nestes tutoriais são escritas em C#. Certifique-se de instalar a [versão 2.1.403 do Core SDK .NET ou posteriormente](https://www.microsoft.com/net/download) na sua máquina de desenvolvimento para construir e executar a amostra. Verifique se a versão certa está instalada `dotnet --version` na sua máquina, funcionando numa janela de comando.

- [Visual Studio Code](https://code.visualstudio.com/), para explorar o código de exemplo. 

## <a name="deploy-digital-twins"></a>Implementar o Digital Twins

Utilize os passos nesta secção para criar uma nova instância do serviço Azure Digital Twins. Apenas uma instância pode ser criada por subscrição. Passe para a secção seguinte se já tiver um em execução. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Conceder permissões à sua aplicação

A Digital Twins utiliza o [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) para controlar o [acesso de leitura/escrita](../active-directory/develop/v2-permissions-and-consent.md) ao serviço. Qualquer aplicação que precise de se conectar com a sua instância De Gémeos Digitais deve ser registada no Azure AD. Os passos nesta secção explicam como registar o exemplo de aplicação.

Se já tiver um registo de aplicação, pode reutilizá-lo no seu exemplo. No entanto, leia esta secção para ter a certeza de que esse registo está devidamente configurado.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Configure a amostra de Gémeos Digitais

Esta secção acompanha-o através de uma aplicação Azure Digital Twins que comunica com as APIs REST [Das Gémeas Digitais](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Transferir o exemplo

Se já tiver transferido os exemplos para [Quickstart to find available rooms](quickstart-view-occupancy-dotnet.md) (Início rápido para descobrir salas disponíveis), pode ignorar estes passos.

1. Descarregue as [amostras De Gémeos Digitais .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Extraia o conteúdo da pasta zip na sua máquina.

### <a name="explore-the-sample"></a>Explorar o exemplo

Na pasta de exemplo extraída, abra o ficheiro **digital-twins-samples-csharp\digital-twins-samples.code-workspace** no Visual Studio Code. Inclui dois projetos:

* Você pode usar a amostra de **fornecimento de ocupação rápida** para configurar e fornecer um gráfico de inteligência [espacial](concepts-objectmodel-spatialgraph.md#digital-twins-object-models). Este gráfico é a imagem digitalizada dos seus espaços físicos e os recursos neles. Usa um modelo de [objeto,](concepts-objectmodel-spatialgraph.md#digital-twins-object-models)que define objetos para um edifício inteligente. Para obter uma lista completa dos objetos e das APIs REST do Digital Twins, veja [esta documentação da API REST](https://docs.westcentralus.azuresmartspaces.net/management/swagger) ou o URL API de Gestão que foi criado para [a sua instância](#deploy-digital-twins).

   Para explorar a amostra para entender como comunica com a sua instância De Gémeos Digitais, pode começar com a pasta **src\actions.** Os ficheiros desta pasta implementam os comandos que utilizará nestes tutoriais:
    - O ficheiro **provisionSample.cs** mostra como fornecer o seu gráfico espacial.
    - O ficheiro **getSpaces.cs** obtém informações sobre os espaços aprovisionados.
    - O ficheiro **getAvailableAndFreshSpaces.cs** obtém os resultados de uma função personalizada chamada função definida pelo utilizador.
    - O ficheiro **createEndpoints.cs** cria pontos finais para interagir com outros serviços.

* A simulação de conectividade **com** dispositivos simula dados de sensores e envia-os para o centro IoT que está aprovisionado para a sua instância de Gémeos Digitais. Você usará esta amostra [no próximo tutorial depois de fornecer o seu gráfico espacial.](tutorial-facilities-udf.md#simulate-sensor-data) Os identificadores de sensores e dispositivos que utiliza para configurar esta amostra devem ser os mesmos que utilizarão para fornecer o seu gráfico.

### <a name="configure-the-provisioning-sample"></a>Configurar o exemplo de aprovisionamento

1. Abra uma janela de comando e vá para a amostra descarregada. Execute o seguinte comando:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Execute o comando seguinte para restaurar as dependências para o exemplo de projeto:

    ```cmd/sh
    dotnet restore
    ```

1. No Visual Studio Code, abra o ficheiro [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) no projeto de **início rápido de ocupação.** Atualizar os seguintes valores:
   * **ClientId**: Introduza o ID de aplicação do registo da sua aplicação Azure AD. Notou este ID na secção onde [definiu permissões de aplicações.](#grant-permissions-to-your-app)
   * **Inquilino**: Insira a identificação do seu [inquilino Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) Também observou este ID na secção onde [definiu permissões de aplicações.](#grant-permissions-to-your-app)
   * **BaseUrl**: introduza o URL da instância do Digital Twins. Para obter este URL, substitua os marcadores de posição no mesmo elos valores da sua instância, `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Também pode obter este URL modificando o URL da API de Gestão [da secção de implementação](#deploy-digital-twins). Substitua **o swagger/** por **api/v1.0/**.

1. Reveja uma lista de funcionalidades de Gémeos Digitais que pode explorar utilizando a amostra. Execute o seguinte comando:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>Compreender o processo de provisionamento

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

Esta função utiliza [o provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) na mesma pasta. Abra este arquivo e tome nota da hierarquia de um edifício de escritórios: *Local,* *Piso,* *Área,* e *Salas.* Qualquer um destes espaços físicos pode ter *dispositivos* e *sensores*. Cada entrada tem `type` &mdash;um predefinido, por exemplo, Piso, Quarto.

O ficheiro **de yaml** da amostra `Default` mostra um gráfico espacial que usa o modelo de objeto sinuoso das Gémeas Digitais. Este modelo fornece nomes genéricos para a maioria dos tipos. Nomes genéricos são suficientes para um edifício. Exemplos são Temperatura para SensorDataType e Mapa para SpaceBlobType. Um tipo de espaço exemplo é Sala com subtipos FocusRoom, ConferenceRoom, e assim por diante. 

Se tiver de criar um gráfico espacial para outro tipo de local, como uma fábrica, poderá ter de utilizar um modelo de objetos diferente. Pode descobrir quais os modelos disponíveis para `dotnet run GetOntologies` utilização executando o comando na linha de comando para a amostra de provisionamento. 

Para obter mais informações sobre gráficos espaciais e modelos de objetos, leia [understanding digital twins objeto sôm-se e gráfico](concepts-objectmodel-spatialgraph.md)de inteligência espacial .

### <a name="modify-the-sample-spatial-graph"></a>Modificar o gráfico espacial da amostra

O ficheiro **provisionSample.yaml** contém os seguintes nódosos:

- **recursos**: `resources` O nó cria um recurso Azure IoT Hub para comunicar com os dispositivos na sua configuração. Um hub IoT no nó raiz do seu gráfico pode comunicar com todos os dispositivos e sensores do seu gráfico.  

- **spaces**: no modelo de objetos do Digital Twins, `spaces` representa as localizações físicas. Cada espaço `Type` &mdash;tem, por exemplo, região, local ou cliente&mdash;e um amigável. `Name` Os espaços podem pertencer a outros espaços, criando uma estrutura hierárquica. O ficheiro provisionSample.yaml tem um gráfico espacial de um edifício imaginário. Observe a nidificação lógica `Floor` de `Venue` `Area` espaços de tipo `Room` no interior, num piso, e nós numa área. 

- **devices**: os espaços podem conter `devices`, que são entidades físicas ou virtuais que gerem vários sensores. Por exemplo, um dispositivo pode ser um telefone de um utilizador, uma cápsula de sensor Raspberry Pi ou um portal. No edifício imaginário do exemplo, repare que a sala denominada **Focus Room** contém um dispositivo **Raspberry Pi 3 A1**. Cada nó do dispositivo é identificado por um `hardwareId` exclusivo, que está codificado no exemplo. Para configurar este exemplo para uma produção real, substitua estes valores pelos da sua configuração.  

- **sensores**: Um dispositivo `sensors`pode conter vários . Podem detetar e registar alterações físicas como temperatura, movimento e nível de bateria. Cada nó do sensor é identificado exclusivamente por um `hardwareId`, que está codificado aqui. Para uma aplicação real, substitua-as utilizando os identificadores únicos dos sensores na sua configuração. O ficheiro provisionSample.yaml tem dois sensores para registar *Motion* (Movimento) e *CarbonDioxide* (Dióxido de Carbono). Adicione outro sensor para registar a *Temperature* (Temperatura) ao adicionar as linhas seguintes, abaixo das do sensor CarbonDioxide. Estes são fornecidos em provisionSample.yaml como linhas comentadas. Pode descocomentá-los removendo o `#` personagem na frente de cada linha. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Certifique-se `dataType` `hardwareId` de que as teclas e as teclas estão alinhadas com as declarações acima deste corte. Confirme também que o editor não substitui espaços por tabulações. 

Guarde e feche o ficheiro provisionSample.yaml. No próximo tutorial, você adicionará mais informações a este arquivo, e em seguida, fornecerá o seu edifício de amostras Azure Digital Twins.

> [!TIP]
> Pode ver e modificar o seu gráfico espacial utilizando o [Telespectador digital Twins Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser parar de explorar as Gémeas Digitais Azure neste momento, sinta-se à vontade para apagar os recursos criados neste tutorial:

1. A partir do menu esquerdo no [portal Azure](https://portal.azure.com), selecione **Todos os recursos,** selecione o seu grupo de recursos Digital Twins e selecione **Delete**.

    > [!TIP]
    > Se teve problemas em apagar a sua instância De Gémeos Digitais, foi lançada uma atualização de serviço com a correção. Por favor, tente apagar o seu caso.

1. Se necessário, apague a aplicação da amostra na sua máquina de trabalho.

## <a name="next-steps"></a>Passos seguintes

Para aprender a implementar uma lógica personalizada para monitorizar as condições no seu edifício de amostras, vá ao próximo tutorial da série: 
> [!div class="nextstepaction"]
> [Tutorial: Provision your building and monitor working conditions](tutorial-facilities-udf.md) (Tutorial: Aprovisionar o edifício e monitorizar as condições de funcionamento)