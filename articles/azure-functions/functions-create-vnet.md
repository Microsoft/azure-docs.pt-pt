---
title: Utilize pontos finais privados para integrar funções do Azure com uma rede virtual
description: Este tutorial mostra-lhe como ligar uma função a uma rede virtual Azure e bloqueá-la utilizando pontos finais privados.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: 3dd5e700b3081f1c1ef8e4601385c707a5738321
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630474"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>Tutorial: Integrar funções do Azure com uma rede virtual Azure utilizando pontos finais privados

Este tutorial mostra-lhe como usar as Funções Azure para se conectar a recursos numa rede virtual Azure utilizando pontos finais privados. Irá criar uma função utilizando uma conta de armazenamento que está bloqueada atrás de uma rede virtual. A rede virtual usa um gatilho de fila de autocarros de serviço.

Neste tutorial, você:

> [!div class="checklist"]
> * Crie uma aplicação de função no plano Premium.
> * Crie recursos Azure, como o autocarro de serviço, a conta de armazenamento e a rede virtual.
> * Bloqueie a sua conta de armazenamento atrás de um ponto final privado.
> * Bloqueie o seu autocarro de serviço atrás de um ponto final privado.
> * Implemente uma aplicação de função que utilize tanto o autocarro de serviço como os disparadores HTTP.
> * Bloqueie a sua aplicação de função por trás de um ponto final privado.
> * Teste para ver se a sua aplicação de função está segura dentro da rede virtual.
> * Limpe os recursos.

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar uma aplicação de função num plano Premium

Você vai criar uma aplicação de função .NET no plano Premium porque este tutorial usa C#. Outros idiomas também são suportados no Windows. O plano Premium fornece escala sem servidor enquanto suporta a integração de rede virtual.

1. No menu do portal Azure ou na página **Inicial,** selecione **Criar um recurso**.

1. Na **nova** página, selecione App de Função **computacional**  >  .

1. Na página **Basics,** utilize a tabela seguinte para configurar as definições da aplicação de funções.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | Subscrição sob a qual esta nova aplicação de função é criada. |
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nome para o novo grupo de recursos onde irá criar a sua aplicação de função. |
    | **Nome da Aplicação de Funções** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z` (não sensível a maiúsculas e minúsculas), `0-9` e `-`.  |
    |**Publicar**| Código | Opte por publicar ficheiros de código ou um contentor Docker. |
    | **Pilha de runtime** | .NET | Este tutorial utiliza .NET. |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou perto de outros serviços a que as suas funções acedam. |

1. Selecione **Seguinte: Hospedagem**. Na página **'Hospedagem',** insira as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas de armazenamento devem ter entre 3 e 24 caracteres de comprimento. Podem conter apenas números e letras minúsculas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da [conta de armazenamento.](./storage-considerations.md#storage-account-requirements) |
    |**Sistema operativo**| Windows | Este tutorial usa o Windows. |
    | **[Planear](./functions-scale.md)** | Premium | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. Por padrão, quando seleciona **Premium,** é criado um novo plano de Serviço de Aplicações. O **Sku padrão e o tamanho** é **EP1,** onde *EP* significa _prémio elástico_. Para mais informações, consulte a lista de [SKUs Premium.](./functions-premium-plan.md#available-instance-skus)<br/><br/>Quando executar funções JavaScript num plano Premium, escolha um caso que tenha menos vCPUs. Para obter mais informações, consulte [Escolha os planos Premium de núcleo único.](./functions-reference-node.md#considerations-for-javascript-functions)  |

1. Selecione **Seguinte: Monitorização**. Na página **'Monitor'** insira as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Predefinição | Crie um recurso Application Insights com o mesmo nome de aplicação na região suportada mais próxima. Expanda esta definição se precisar de alterar o nome de **novo recurso** ou armazenar os seus dados numa **localização** diferente numa [geografia Azure.](https://azure.microsoft.com/global-infrastructure/geographies/) |

1. Selecione **Review + crie** para rever as seleções de configuração da aplicação.

1. Na página **'Rever + criar',** reveja as suas definições. Em seguida, **selecione Criar** para a disposição e implementar a aplicação de função.

1. No canto superior direito do portal, selecione o ícone **Notificações** e observe a mensagem **conseguida da Implementação.**

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **Pin para painel de instrumentos.** A fixação facilita o regresso a esta função de recurso de aplicação a partir do seu dashboard.

Parabéns! Criou com sucesso a sua aplicação de função premium.

## <a name="create-azure-resources"></a>Criar recursos do Azure

Em seguida, você vai criar uma conta de armazenamento, um ônibus de serviço, e uma rede virtual. 
### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

As suas redes virtuais vão precisar de uma conta de armazenamento separada da que criou com a sua aplicação de função.

1. No menu do portal Azure ou na página **Inicial,** selecione **Criar um recurso**.

1. Na página **Nova,** procure por *conta de armazenamento.* Em seguida, selecione **Criar**.

1. No **separador Básicos,** utilize a tabela seguinte para configurar as definições da conta de armazenamento. Todas as outras definições podem utilizar os valores predefinidos.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | O grupo de recursos que criou com a sua aplicação de função. |
    | **Nome** | mysecurestorage| O nome da conta de armazenamento a que o ponto final privado será aplicado. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | A região onde criou a sua aplicação de função. |

1. Selecione **Rever + criar**. Após os acabamentos de validação, **selecione Criar**.

### <a name="create-a-service-bus"></a>Criar um ônibus de serviço

1. No menu do portal Azure ou na página **Inicial,** selecione **Criar um recurso**.

1. Na página **Nova,** procure *por autocarro de serviço.* Em seguida, selecione **Criar**.

1. No **separador Basics,** utilize a seguinte tabela para configurar as definições do autocarro de serviço. Todas as outras definições podem utilizar os valores predefinidos.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. |
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | O grupo de recursos que criou com a sua aplicação de função. |
    | **Nome** | myServiceBus| O nome do autocarro de serviço a que o ponto final privado será aplicado. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | A região onde criou a sua aplicação de função. |
    | **Escalão de preço** | Premium | Escolha este nível para utilizar pontos finais privados com o Azure Service Bus. |

1. Selecione **Rever + criar**. Após os acabamentos de validação, **selecione Criar**.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Os recursos Azure neste tutorial integram-se ou são colocados dentro de uma rede virtual. Utilizará pontos finais privados para conter o tráfego de rede dentro da rede virtual.

O tutorial cria duas sub-redes:
- **padrão**: Sub-rede para pontos finais privados. Os endereços IP privados são dados a partir desta sub-rede.
- **funções**: Sub-rede para integração de rede virtual Azure Functions. Esta sub-rede é delegada na aplicação de função.

Criar a rede virtual à qual a aplicação de função se integra:

1. No menu do portal Azure ou na página **Inicial,** selecione **Criar um recurso**.

1. Na página **Nova,** procure rede *virtual.* Em seguida, selecione **Criar**.

1. No **separador Básicos,** utilize a tabela seguinte para configurar as definições de rede virtual.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | O grupo de recursos que criou com a sua aplicação de função. |
    | **Nome** | myVirtualNet| O nome da rede virtual a que a sua aplicação de função se irá ligar. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | A região onde criou a sua aplicação de função. |

1. No separador **endereços IP,** selecione **Adicionar sub-rede**. Utilize a seguinte tabela para configurar as definições da sub-rede.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Screenshot da visualização de configuração de rede virtual Create.":::

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Nome da sub-rede** | funções | O nome da sub-rede a que a sua aplicação de função se ligará. | 
    | **Intervalo de endereços da sub-rede** | 10.0.1.0/24 | O intervalo de endereços da sub-rede. Na imagem anterior, note que o espaço de endereço IPv4 é de 10.0.0.0/16. Se o valor fosse de 10.1.0.0/16, o intervalo de endereços recomendado para a sub-rede seria de 10.1.1.0/24. |

1. Selecione **Rever + criar**. Após os acabamentos de validação, **selecione Criar**.

## <a name="lock-down-your-storage-account"></a>Bloqueie a sua conta de armazenamento

Os pontos finais privados do Azure são utilizados para se conectarem a recursos específicos do Azure utilizando um endereço IP privado. Esta ligação garante que o tráfego de rede permanece dentro da rede virtual escolhida e o acesso está disponível apenas para recursos específicos. 

Crie os pontos finais privados para o armazenamento de Ficheiros Azure e para o Armazenamento Azure Blob utilizando a sua conta de armazenamento:

1. Na sua nova conta de armazenamento, no menu à esquerda, **selecione Networking**.

1. No **separador de ligações de ponto final privado,** selecione **Private endpoint**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Screenshot de como criar pontos finais privados para a conta de armazenamento.":::

1. No **separador Básicos,** utilize as definições de ponto final privado mostradas na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que criou com a sua aplicação de função. | |
    | **Nome** | ponto final de ficheiro | O nome do ponto final privado para ficheiros da sua conta de armazenamento. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região onde criou a sua conta de armazenamento. |

1. No **separador Recursos,** utilize as definições de ponto final privado mostradas na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.Storage/storageAcontas | O tipo de recurso para contas de armazenamento. |
    | **Recurso** | mysecurestorage | A conta de armazenamento que criou. |
    | **Recurso secundário de destino** | file | O ponto final privado que será usado para ficheiros a partir da conta de armazenamento. |

1. No **separador Configuração,** para a definição **sub-rede,** escolha **o predefinido**.

1. Selecione **Rever + criar**. Após os acabamentos de validação, **selecione Criar**. Os recursos na rede virtual podem agora comunicar com ficheiros de armazenamento.

1. Crie outro ponto final privado para bolhas. No separador **Recursos,** utilize as definições mostradas na tabela seguinte. Para todas as outras definições, utilize os mesmos valores utilizados para criar o ponto final privado para ficheiros.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.Storage/storageAcontas | O tipo de recurso para contas de armazenamento. |
    | **Recurso** | mysecurestorage | A conta de armazenamento que criou. |
    | **Recurso secundário de destino** | blob | O ponto final privado que será usado para bolhas da conta de armazenamento. |

## <a name="lock-down-your-service-bus"></a>Bloqueie o seu ônibus de serviço

Crie o ponto final privado para bloquear o seu autocarro de serviço:

1. No seu novo autocarro de serviço, no menu à esquerda, **selecione Networking**.

1. No **separador de ligações de ponto final privado,** selecione **Private endpoint**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Screenshot de como ir a pontos finais privados para o ônibus de serviço.":::

1. No **separador Básicos,** utilize as definições de ponto final privado mostradas na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | O grupo de recursos que criou com a sua aplicação de função. |
    | **Nome** | sb-endpoint | O nome do ponto final privado para ficheiros da sua conta de armazenamento. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | A região onde criou a sua conta de armazenamento. |

1. No **separador Recursos,** utilize as definições de ponto final privado mostradas na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.ServiceBus/namespaces | O tipo de recurso para o autocarro de serviço. |
    | **Recurso** | myServiceBus | O autocarro de serviço que criou anteriormente no tutorial. |
    | **Subresource-alvo** | espaço de nomes | O ponto final privado que será usado para o espaço de nome do ônibus de serviço. |

1. No **separador Configuração,** para a definição **sub-rede,** escolha **o predefinido**.

1. Selecione **Rever + criar**. Após os acabamentos de validação, **selecione Criar**. 

Os recursos na rede virtual podem agora comunicar com o autocarro de serviço.

## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

1. Na conta de armazenamento que criou, no menu à esquerda, selecione **ações de ficheiro**.

1. Selecione **+ Partilhas de ficheiros**. Para efeitos deste tutorial, nomeie os ficheiros de partilha de *ficheiros*.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Screenshot de como criar uma partilha de ficheiros na conta de armazenamento.":::

1. Selecione **Criar**.

## <a name="get-the-storage-account-connection-string"></a>Obtenha o string de conexão de conta de armazenamento

1. Na conta de armazenamento que criou, no menu à esquerda, selecione **as teclas De acesso**.

1. Selecione **as teclas Show**. Copie e guarde a cadeia de ligação da **chave1**. Vai precisar desta cadeia de ligação quando configurar as definições da aplicação.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Screenshot de como obter uma cadeia de ligação de conta de armazenamento.":::

## <a name="create-a-queue"></a>Criar uma fila

Crie a fila onde o seu gatilho de ônibus de serviço Azure Functions terá eventos:

1. No seu autocarro de serviço, no menu à esquerda, selecione **Filas.**

1. Selecione **políticas de acesso compartilhado**. Para efeitos deste tutorial, nomeie a *lista de espera.*

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Screenshot de como criar uma fila de ônibus de serviço.":::

1. Selecione **Criar**.

## <a name="get-a-service-bus-connection-string"></a>Obtenha uma cadeia de conexão de ônibus de serviço

1. No seu autocarro de serviço, no menu à esquerda, selecione **Políticas de acesso compartilhados.**

1. **Selecione RootManageSharedAccessKey**. Copie e guarde a **cadeia de ligação primária.** Vai precisar desta cadeia de ligação quando configurar as definições da aplicação.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Screenshot de como obter uma cadeia de ligação de autocarro de serviço.":::

## <a name="integrate-the-function-app"></a>Integrar a app de função

Para utilizar a sua aplicação de função com redes virtuais, precisa de se juntar a ela a uma sub-rede. Utilizará uma sub-rede específica para a integração virtual da rede Azure Functions. Você usará a sub-rede predefinida para outros pontos finais privados que cria neste tutorial.

1. Na sua aplicação de função, no menu à esquerda, **selecione Networking**.

1. Em **Integração VNet,** selecione **Clique aqui para configurar**.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Screenshot de como ir para a integração de rede virtual.":::

1. **Selecione Adicionar VNet**.

1. Em **Rede Virtual,** selecione a rede virtual que criou anteriormente.

1. Selecione a sub-rede **de funções** que criou anteriormente. A sua aplicação de função está agora integrada na sua rede virtual!

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Screenshot de como ligar uma aplicação de função a uma sub-rede.":::

## <a name="configure-your-function-app-settings"></a>Configure as definições da sua aplicação de função

1. Na sua aplicação de função, no menu à esquerda, selecione **Configuração**.

1. Para utilizar a sua aplicação de função com redes virtuais, atualize as definições da aplicação mostradas na tabela seguinte. Para adicionar ou editar uma definição, selecione **+ Nova definição de aplicação** ou o ícone **editar** na coluna mais à direita da tabela de definições de aplicações. Quando terminar, **selecione Save**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Screenshot de como configurar as definições de aplicações de função para pontos finais privados.":::

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | A cadeia de ligação da conta de armazenamento que criou. Esta cadeia de ligação de armazenamento é da secção [De ligação de conta de armazenamento.](#get-the-storage-account-connection-string) Esta definição permite que a sua aplicação de função utilize a conta de armazenamento segura para operações normais em tempo de execução. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | A cadeia de ligação da conta de armazenamento que criou. Esta definição permite que a sua aplicação de função utilize a conta de armazenamento segura para ficheiros Azure, que é usada durante a implementação. |
    | **WEBSITE_CONTENTSHARE** | ficheiros | O nome da partilha de ficheiros que criou na conta de armazenamento. Utilize esta definição com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Crie esta definição de aplicação para a cadeia de ligação do seu autocarro de serviço. Esta cadeia de ligação de armazenamento é da secção [Get a service connection string.](#get-a-service-bus-connection-string)|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Crie esta configuração de aplicação. Um valor de 1 permite que a sua aplicação de função seja dimensionada quando a sua conta de armazenamento está restrita a uma rede virtual. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Crie esta configuração de aplicação. Quando a sua aplicação se integrar com uma rede virtual, utilizará o mesmo servidor DNS que a rede virtual. A sua aplicação de função precisa desta definição para que possa funcionar com zonas privadas Azure DNS. É necessário quando se usa pontos finais privados. Esta configuração e WEBSITE_VNET_ROUTE_ALL enviarão todas as chamadas de saída da sua app para a sua rede virtual. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Crie esta configuração de aplicação. Quando a sua aplicação se integra com uma rede virtual, utiliza o mesmo servidor DNS que a rede virtual. A sua aplicação de função precisa desta definição para que possa funcionar com zonas privadas Azure DNS. É necessário quando se usa pontos finais privados. Esta configuração e WEBSITE_DNS_SERVER enviarão todas as chamadas de saída da sua app para a sua rede virtual. |

1. Na vista **Configuração,** selecione o separador **definições de tempo de execução da função.**

1. Definir **a monitorização da escala de tempo de execução** para **on**. Em seguida, selecione **Guardar**. O dimensionamento com prazo de execução permite-lhe ligar funções de gatilho não-HTTP a serviços que funcionam dentro da sua rede virtual.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Screenshot de como permitir o dimensionamento de tempo de execução para funções Azure.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Desloque um gatilho de autocarro de serviço e um gatilho HTTP

1. Em GitHub, vá ao seguinte repositório de amostras. Contém uma aplicação de função e duas funções, um gatilho HTTP e um gatilho de fila de autocarros de serviço.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. No topo da página, selecione **Fork** para criar um garfo deste repositório na sua própria conta ou organização GitHub.

1. Na sua aplicação de função, no menu à esquerda, selecione **Centro de Implementação**. Em seguida, selecione **Definições**.

1. No **separador Definições,** utilize as definições de implantação mostradas na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Origem** | GitHub | Devia ter criado um repositório GitHub para o código de amostra no passo 2. | 
    | **Organização**  | myOrganização | A organização em que o seu repo está no check-in. Normalmente é a sua conta. |
    | **Repositório** | myRepo | O repositório que criaste para o código de amostra. |
    | **Ramo** | main | O ramo principal do repositório que criaste. |
    | **Pilha de runtime** | .NET | O código de amostra está em C#. |

1. Selecione **Guardar**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Screenshot de como implementar código Azure Functions através do portal.":::

1. A sua implantação inicial pode demorar alguns minutos. Quando a sua aplicação é implementada com sucesso, no **separador Logs,** vê uma mensagem de estado **de sucesso (Ative).** Se necessário, refresque a página.

Parabéns! Implementou com sucesso a sua aplicação de função de amostra.

## <a name="lock-down-your-function-app"></a>Bloqueie a sua aplicação de função

Agora crie o ponto final privado para bloquear a sua aplicação de função. Este ponto final privado ligará a sua aplicação de função de forma privada e segura à sua rede virtual utilizando um endereço IP privado. 

Para mais informações, consulte a [documentação do ponto final privado.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

1. Na sua aplicação de função, no menu à esquerda, **selecione Networking**.

1. Em **Ligações de Ponto Final Privados**, selecione **Configurar as suas ligações de ponto final privado**.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Screenshot de como navegar para um ponto final privado de aplicação de função.":::

1. Selecione **Adicionar**.

1. No painel que abre, utilize as seguintes definições de ponto final privado:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Screenshot de como criar um ponto final privado de aplicação de função. O nome é ponto final de funções. A subscrição é &quot;Teste Privado Sub CACHHAI&quot;. A rede virtual é myVirtualNet-tutorial. A sub-rede está padrão.":::

1. Selecione **OK** para adicionar o ponto final privado. 
 
Parabéns! Conseguiu garantir com sucesso a sua aplicação de função, autocarro de serviço e conta de armazenamento adicionando pontos finais privados!

### <a name="test-your-locked-down-function-app"></a>Teste a sua aplicação de função bloqueada

1. Na sua aplicação de funções, no menu à esquerda, selecione **Funções**.

1. Selecione **ServiceBusQueueTrigger**.

1. No menu à esquerda, selecione **Monitor**. 
 
Verá que não pode monitorizar a sua aplicação. O seu navegador não tem acesso à rede virtual, pelo que não pode aceder diretamente aos recursos dentro da rede virtual. 
 
Aqui está uma forma alternativa de monitorizar a sua função utilizando insights de aplicação:

1. Na sua aplicação de função, no menu à esquerda, selecione **Application Insights**. Em seguida, selecione **Ver Dados de Insights de Aplicação**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Screenshot de como visualizar insights de aplicações para uma aplicação de função.":::

1. No menu à esquerda, selecione **métricas ao vivo**.

1. Abra uma nova conta. No seu autocarro de serviço, no menu à esquerda, selecione **Filas.**

1. Selecione a sua fila.

1. No menu à esquerda, selecione **Service Bus Explorer**. Em **Enviar**, para **Tipo de Conteúdo,** escolha **Texto/Planície**. Em seguida, insira uma mensagem. 

1. **Selecione Enviar** para enviar a mensagem.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Screenshot de como enviar mensagens de ônibus de serviço usando o portal.":::

1. No separador **métricas Live,** deve ver se o gatilho da fila do autocarro de serviço disparou. Caso não o tenha, reensiem a mensagem do **Service Bus Explorer**.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Screenshot de como visualizar mensagens usando métricas ao vivo para aplicações de função.":::

Parabéns! Testou com sucesso a configuração da sua aplicação de função com pontos finais privados.

## <a name="understand-private-dns-zones"></a>Compreender zonas privadas de DNS
Usaste um ponto final privado para ligar aos recursos do Azure. Está a ligar-se a um endereço IP privado em vez do ponto final público. Os serviços Azure existentes estão configurados para utilizar um DNS existente para ligar ao ponto final público. Tem de substituir a configuração DNS para ligar ao ponto final privado.

Uma zona privada de DNS é criada para cada recurso Azure que foi configurado com um ponto final privado. É criado um registo DNS para cada endereço IP privado associado ao ponto final privado.

As seguintes zonas DNS foram criadas neste tutorial:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma app de função Premium, conta de armazenamento e autocarro de serviço. Garantiu todos estes recursos por detrás de pontos finais privados. 

Utilize os seguintes links para saber mais sobre as funcionalidades de networking disponíveis:

> [!div class="nextstepaction"]
> [Opções de networking em Funções Azure](./functions-networking-options.md)


> [!div class="nextstepaction"]
> [Plano Premium funções Azure](./functions-premium-plan.md)
