---
title: Configurar o oleoduto CI/CD com a tarefa de construção do emulador Azure Cosmos DB
description: Tutorial sobre como configurar um fluxo de trabalho de compilação e versão no DevOps do Azure com a tarefa de compilação do emulador do Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 0a705ad81925491fe054d846143472c6e4432b69
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77561907"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Configurar um pipeline CI/CD com a tarefa de compilação do emulador do Azure Cosmos DB no DevOps do Azure

O emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço do Azure Cosmos DB para fins de desenvolvimento. O emulador permite-lhe programar e testar a sua aplicação localmente, sem criar uma subscrição do Azure ou incorrer em custos. 

A tarefa de compilação do emulador do Azure Cosmos DB para o DevOps do Azure permite-lhe fazer o mesmo num ambiente de CI. Com a tarefa de compilação, pode testar o emulador como parte dos seus fluxos de trabalho de compilação e versão. A tarefa acelera um contentor Docker com o emulador a ser executado e fornece um ponto final que pode ser utilizado pelo resto da definição da compilação. Pode criar e iniciar todas as instâncias do emulador que precisar, com cada uma a ser executada num contentor separado. 

Este artigo demonstra como configurar um pipeline de CI no DevOps do Azure para uma aplicação ASP.NET que utiliza a tarefa de compilação do emulador do Azure Cosmos DB para executar testes. Você pode usar uma abordagem semelhante para configurar um oleoduto CI para um Node.js ou uma aplicação Python. 

## <a name="install-the-emulator-build-task"></a>Instalar a tarefa de compilação do emulador

Para utilizar a tarefa de compilação, tem de a instalar primeiro na sua organização do DevOps do Azure. Procure a extensão **Emulador do Azure Cosmos DB** no [Microsoft Azure Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) e clique em **Get it free** (Obter gratuitamente).

![Procure e instale a tarefa de compilação do Emulador do Azure Cosmos DB no Marketplace do DevOps do Azure](./media/tutorial-setup-ci-cd/addExtension_1.png)

Em seguida, selecione a organização em que pretende instalar a extensão. 

> [!NOTE]
> Para instalar uma extensão a uma organização Azure DevOps, deve ser proprietário de conta ou administrador de recolha de projetos. Se não tiver permissões mas for membro de uma conta, terá a opção de pedir extensões. [Saiba mais.](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts)

![Escolha uma organização Azure DevOps para instalar uma extensão](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Criar uma definição de compilação

Agora que a extensão está instalada, inicie sessão na sua conta do Azure DevOps e encontre o seu projeto no dashboard de projetos. Pode adicionar um [pipeline de compilação](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) ao seu projeto ou modificar um pipeline de compilação existente. Se já tem um pipeline de compilação, pode avançar para [Adicionar a tarefa de compilação do Emulador à definição de compilação](#addEmulatorBuildTaskToBuildDefinition).

1. Para criar uma nova definição de compilação, navegue até ao separador **Compilações** no DevOps do Azure. Selecione **+New** (+Novo). \> **Novo pipeline de compilação**

   ![Criar um novo pipeline de compilação](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. Selecione a **origem** desejada, o **Projeto de equipa**, o **Repositório** e o **Ramo predefinido para compilações manuais e programadas**. Depois de escolher as opções exigidas, selecione **Continuar**

   ![Selecione o projeto de equipa, o repositório e o ramo para o pipeline de compilação](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. Por fim, selecione o modelo desejado para o pipeline de compilação. Neste tutorial, vamos selecionar o modelo **ASP.NET**. Agora você tem um pipeline de construção que você pode configurar para usar a tarefa de construção de emulador Azure Cosmos DB. 

> [!NOTE]
> O conjunto de agentes a selecionar para este CI deve ter o Docker para windows instalado a menos que a instalação seja feita manualmente numa tarefa anterior como parte do CI. Consulte o artigo dos [agentes hospedados da Microsoft](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml) para uma seleção de grupos de agentes; recomendamos começar `Hosted VS2017`com .

O emulador Azure Cosmos DB atualmente não suporta o grupo de agentes VS2019 hospedado. No entanto, o emulador já vem com VS2019 instalado e você usa-o iniciando o emulador com os seguintes cmdlets PowerShell. Se tiver algum problema ao utilizar o VS2019, contacte a equipa [Azure DevOps](https://developercommunity.visualstudio.com/spaces/21/index.html) para obter ajuda:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>Adicionar a tarefa a um pipeline de compilação

1. Antes de adicionar uma tarefa ao pipeline de compilação, deve adicionar uma tarefa de agente. Navegue para o seu pipeline de compilação, selecione **...** e escolha **Adicionar uma tarefa de agente**.

1. Em seguida, selecione o **+** símbolo ao lado da função do agente para adicionar a tarefa de construção do emulador. Procure **cosmos** na caixa de pesquisa, selecione **Emulador do Azure Cosmos DB** e adicione-o à tarefa do agente. A tarefa de compilação irá iniciar um contentor com uma instância do emulador do Cosmos DB já em execução no mesmo. A tarefa de Emulador do Azure Cosmos DB deve ser colocada antes de quaisquer outras tarefas que esperam que o emulador esteja em execução.

   ![Adicionar a tarefa de compilação do Emulador à definição de compilação](./media/tutorial-setup-ci-cd/addExtension_3.png)

Neste tutorial, irá adicionar a tarefa ao início para garantir que o emulador está disponível antes de executar os nossos testes.

## <a name="configure-tests-to-use-the-emulator"></a>Configurar testes para utilizar o emulador

Agora serão configurados os testes para utilizar o emulador. A tarefa de compilação do emulador exporta uma variável de ambiente ("CosmosDbEmulator.Endpoint") à qual todas as tarefas no pipeline de compilação podem emitir pedidos. 

Neste tutorial, é utilizada a [tarefa do Visual Studio Test](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) para executar testes de unidades configurados através de um ficheiro **.runsettings**. Para saber mais sobre a configuração de testes de unidades, aceda à [documentação](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017). A amostra completa do código de aplicação Todo que utiliza neste documento está disponível no [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

Abaixo encontra-se o exemplo de um ficheiro **.runsettings** que define parâmetros a ser passados para os testes de unidades de uma aplicação. Tenha em atenção que a variável `authKey` utilizada é a [chave já conhecida](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests) do emulador. A `authKey` é a chave esperada pela tarefa de compilação do emulador e deve ser definida no seu ficheiro **.runsettings**.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Se estiver a criar um oleoduto CI/CD para uma aplicação que utiliza a API do Azure Cosmos DB para o MongoDB, a cadeia de ligação por padrão inclui o número de porta 10255. No entanto, esta porta não está aberta atualmente, como alternativa, deve utilizar a porta 10250 para estabelecer a ligação. A API da Azure Cosmos DB para a cadeia de ligação MongoDB permanece a mesma, exceto que o número de porta suportada é 10250 em vez de 10255.

Estes parâmetros `TestRunParameters` são mencionados através de uma propriedade `TestContext` no projeto de teste da aplicação. Eis um exemplo de um teste que é executado no Cosmos DB.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Navegue até Execution Options (Opções de Execução) na tarefa do Visual Studio Test. Na opção **Settings file** (ficheiro de Definições), especifique que os testes são configurados com o ficheiro **.runsettings**. Na opção **Override test run parameters** (Substituir parâmetros de execução de testes), adicione `-endpoint $(CosmosDbEmulator.Endpoint)`. Este procedimento irá configurar a tarefa Test para se referir ao ponto final da tarefa de compilação do emulador, em vez do ponto final definido no ficheiro **.runsettings**.  

![Substituir uma variável de ponto final pelo ponto final da tarefa de compilação do Emulador](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Executar a compilação

Agora, **Guarde a compilação e coloque-a em fila**. 

![Guardar e executar a compilação](./media/tutorial-setup-ci-cd/runBuild_1.png)

Assim que a compilação iniciar, verá que a tarefa do emulador do Cosmos DB irá começar a puxar para baixo a imagem Docker com o emulador instalado. 

![Guardar e executar a compilação](./media/tutorial-setup-ci-cd/runBuild_4.png)

Após a conclusão da compilação, poderá observar os seus testes a passarem, com todos a serem executados no emulador do Cosmos DB a partir da tarefa de compilação.

![Guardar e executar a compilação](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="set-up-using-yaml"></a>Configurar usando o YAML

Se estiver a configurar o gasoduto CI/CD utilizando uma tarefa YAML, pode definir a tarefa YAML como mostrado no seguinte código:

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a utilização do emulador para desenvolvimento e testes locais, veja [Utilizar o Emulador do Azure Cosmos DB para desenvolvimento e teste locais](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Para exportar certificados SSL do emulador, veja [Exportar os certificados de Emulador do Azure Cosmos DB para utilizar com o Java, o Python e o Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
