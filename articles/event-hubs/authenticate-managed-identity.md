---
title: Autenticação de identidade gerida com Diretório Ativo Azure
description: Este artigo fornece informações sobre a autenticação de uma identidade gerida com o Azure Ative Directory para aceder aos recursos do Azure Event Hubs
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: b23fc0a59f9accc70c69c1096c1a15f1313ee2e0
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332455"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos Recursos dos Hubs de Eventos
A Azure Event Hubs suporta a autenticação do Azure Ative Directory (Azure AD) com [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md). Identidades geridas para recursos Azure podem autorizar o acesso aos recursos do Event Hubs utilizando credenciais AD Azure a partir de aplicações executadas em Azure Virtual Machines (VMs), apps de funções, conjuntos de escala de máquina virtual e outros serviços. Ao utilizar identidades geridas para recursos Azure juntamente com a autenticação Azure AD, pode evitar armazenar credenciais com as suas aplicações que funcionam na nuvem.

Este artigo mostra como autorizar o acesso a um centro de eventos utilizando uma identidade gerida a partir de um VM Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Permitir identidades geridas num VM
Antes de utilizar identidades geridas para a Azure Resources para autorizar os recursos do Event Hubs a partir do seu VM, tem primeiro de ativar identidades geridas para recursos Azure no VM. Para aprender como permitir identidades geridas para recursos Azure, consulte um destes artigos:

- [Portal do Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerida em Azure AD
Para autorizar um pedido ao serviço Desembos hubs a partir de uma identidade gerida na sua aplicação, configurar primeiro as definições de controlo de acesso baseado em funções (Azure RBAC) para essa identidade gerida. Azure Event Hubs define funções Azure que englobam permissões de envio e leitura de Centros de Eventos. Quando o papel de Azure é atribuído a uma identidade gerida, a identidade gerida é concedida acesso aos dados do Event Hubs no âmbito apropriado.

Para obter mais informações sobre a atribuição de funções Azure, consulte [Authenticate com Azure Ative Directory para acesso aos recursos do Event Hubs](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Utilizar Hubs de Eventos com identidades geridas
Para utilizar os Centros de Eventos com identidades geridas, é necessário atribuir à identidade o papel e o âmbito adequado. O procedimento nesta secção utiliza uma aplicação simples que funciona sob uma identidade gerida e acede aos recursos do Event Hubs.

Aqui estamos a usar uma aplicação web de amostras hospedada no [Azure App Service](https://azure.microsoft.com/services/app-service/). Para obter instruções passo a passo para criar uma aplicação web, consulte [Criar uma aplicação web core ASP.NET em Azure](../app-service/quickstart-dotnetcore.md)

Assim que a aplicação for criada, siga estes passos: 

1. Vá a **Definições** e selecione **Identidade**. 
1. Selecione o **Estado** a ser **ligado**. 
1. Selecione **Guardar** para guardar a definição. 

    ![Identidade gerida para uma aplicação web](./media/authenticate-managed-identity/identity-web-app.png)

Uma vez ativada esta definição, é criada uma nova identidade de serviço no seu Azure Ative Directory (Azure AD) e configurada no anfitrião do Serviço de Aplicações.

Agora, atribua esta identidade de serviço a um papel no âmbito exigido nos recursos do Event Hubs.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Atribuir funções de Azure utilizando o portal Azure
Para atribuir um papel aos recursos do Event Hubs, navegue para esse recurso no portal Azure. Mostrar as definições do Controlo de Acesso (IAM) para o recurso e seguir estas instruções para gerir as atribuições de funções:

> [!NOTE]
> Os passos seguintes atribuem uma função de identidade de serviço aos espaços de nome dos Centros de Eventos. Pode seguir os mesmos passos para atribuir uma função a qualquer recurso do Event Hubs. 

1. No portal Azure, navegue no espaço de nomes do Event Hubs e exiba a **Visão Geral** para o espaço de nomes. 
1. Selecione **Access Control (IAM)** no menu esquerdo para exibir as definições de controlo de acesso para o centro de eventos.
1.  Selecione o **separador funções** para ver a lista de atribuições de funções.
3.  **Selecione Adicionar** para adicionar um novo papel.
4.  Na página **de atribuição de funções Adicionar,** selecione as funções De Centros de Evento que pretende atribuir. Em seguida, procure para localizar a identidade de serviço que tinha registado para atribuir a função.
    
    ![Adicionar página de atribuição de funções](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Selecione **Guardar**. A identidade a quem atribuiu o papel aparece listada nessa função. Por exemplo, a imagem a seguir mostra que a identidade do serviço tem o proprietário de Dados do Event Hubs.
    
    ![Identidade atribuída a um papel](./media/authenticate-managed-identity/role-assigned.png)

Uma vez atribuído o papel, a aplicação web terá acesso aos recursos do Event Hubs sob o âmbito definido. 

### <a name="test-the-web-application"></a>Testar a aplicação Web
1. Crie um espaço de nomes de Event Hubs e um centro de eventos. 
2. Implemente a aplicação web para Azure. Consulte a secção de ação a seguir para obter links para a aplicação web no GitHub. 
3. Certifique-se de que o SendReceive.aspx é definido como o documento padrão para a aplicação web. 
3. Ativar a **identidade** para a aplicação web. 
4. Atribua esta identidade à função **de Proprietário de Dados do Event Hubs** ao nível do espaço de nome ou ao nível do centro de eventos. 
5. Executar a aplicação web, inserir o nome do nome do espaço e o nome do centro do evento, uma mensagem e selecionar **Enviar**. Para receber o evento, **selecione Receber**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs (mais recente)](#tab/latest)
Agora pode lançar-lhe a aplicação web e apontar o seu navegador para a página aspx da amostra. Pode encontrar a aplicação web de amostra que envia e recebe dados dos recursos do Event Hubs no [repo GitHub.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

Instale o mais recente pacote do [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)e comece a enviar eventos para Os Centros de Eventos utilizando **o EventHubProducerClient** e recebendo eventos utilizando **o EventHubConsumerClient**. 

> [!NOTE]
> Para uma amostra de Java que usa uma identidade gerida para publicar eventos num centro de eventos, consulte [eventos de publicação com amostra de identidade Azure no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (legado)](#tab/old)
Agora pode lançar-lhe a aplicação web e apontar o seu navegador para a página aspx da amostra. Pode encontrar a aplicação web de amostra que envia e recebe dados dos recursos do Event Hubs no [repo GitHub.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)

Instale o mais recente pacote do [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)e comece a enviar e receber dados dos centros de eventos utilizando o EventHubClient, tal como mostrado no seguinte código: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Hubs de Eventos para Kafka
Pode utilizar as aplicações Apache Kafka para enviar mensagens e receber mensagens dos Azure Event Hubs utilizando a identidade gerida OAuth. Consulte a seguinte amostra no GitHub: [Event Hubs for Kafka - envie e receba mensagens utilizando a identidade gerida OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Amostras
- **Amostras de Azure.Messaging.EventHubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Amostras microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Estas amostras utilizam a antiga biblioteca **Microsoft.Azure.EventHubs,** mas pode atualizá-la facilmente para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.** Para mover a amostra da utilização da antiga biblioteca para uma nova, consulte o [Guia para migrar do Microsoft.Azure.EventHubs para Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Esta amostra foi atualizada para utilizar a mais recente biblioteca **Azure.Messaging.EventHubs.**
- [Event Hubs for Kafka - enviar e receber mensagens usando identidade gerida OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Passos seguintes
- Veja o seguinte artigo para conhecer identidades geridas para recursos Azure: [O que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Consulte os seguintes artigos relacionados:
    - [Autenticar pedidos aos Azure Event Hubs a partir de uma aplicação utilizando o Azure Ative Directory](authenticate-application.md)
    - [Autenticar pedidos para Azure Event Hubs usando Assinaturas de Acesso Partilhado](authenticate-shared-access-signature.md)
    - [Autorizar o acesso aos recursos do Event Hubs utilizando o Azure Ative Directory](authorize-access-azure-active-directory.md)
    - [Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md)
