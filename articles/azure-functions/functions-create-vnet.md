---
title: Utilize pontos finais privados para integrar funções do Azure com uma rede virtual
description: Um tutorial passo a passo que mostra como ligar uma função a uma rede virtual Azure e bloqueá-la com pontos finais privados
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200211"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>Tutorial: Integrar funções do Azure com uma rede virtual Azure utilizando pontos finais privados

Este tutorial mostra-lhe como usar as Funções Azure para se conectar a recursos numa rede virtual Azure com pontos finais privados. Você vai criar uma função com uma conta de armazenamento bloqueada atrás de uma rede virtual que usa um gatilho de fila de ônibus de serviço.

> [!div class="checklist"]
> * Criar uma aplicação de função no plano Premium
> * Criar recursos Azure (Service Bus, Conta de Armazenamento, Rede Virtual)
> * Bloqueie a sua conta de Armazenamento atrás de um ponto final privado
> * Bloqueie o seu ônibus de serviço atrás de um ponto final privado
> * Implemente uma aplicação de função com os gatilhos Service Bus e HTTP.
> * Bloqueie a sua aplicação de função por trás de um ponto final privado
> * Teste para ver se a sua aplicação de função está segura por trás da rede virtual
> * Limpar os recursos

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar uma aplicação de função num plano Premium

Em primeiro lugar, cria-se uma aplicação de função .NET no [plano Premium,] uma vez que este tutorial utilizará C#. Outros idiomas também são suportados no Windows. Este plano fornece escala sem servidor enquanto suporta a integração de rede virtual.

1. A partir do menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na **nova** página, selecione App de Função **computacional**  >  .

1. Na página **Basics,** utilize as definições da aplicação de função conforme especificado no quadro seguinte:

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | A subscrição no âmbito da qual esta nova aplicação de funções é criada. |
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome da Aplicação de Funções** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z` (não sensível a maiúsculas e minúsculas), `0-9` e `-`.  |
    |**Publicar**| Código | Opção para publicar ficheiros de código ou um contentor de Docker. |
    | **Pilha de runtime** | .NET | Este tutorial usa .NET |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou perto de outros serviços a que as suas funções acedam. |

1. Selecione **Seguinte: Hospedagem**. Na página **'Hospedagem',** insira as seguintes definições:

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas de armazenamento têm de ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da [conta de armazenamento.](./storage-considerations.md#storage-account-requirements) |
    |**Sistema operativo**| Windows | Este tutorial usa o Windows |
    | **[Planear](./functions-scale.md)** | Premium | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. Selecione **Premium**. Por padrão, é criado um novo plano de Serviço de Aplicações. O **Sku padrão e o tamanho** é **EP1,** onde EP significa _prémio elástico_. Para saber mais, consulte a [lista de SKUs Premium.](./functions-premium-plan.md#available-instance-skus)<br/>Ao executar funções JavaScript num plano Premium, deve escolher um caso que tenha menos vCPUs. Para obter mais informações, consulte [Escolha os planos Premium de núcleo único.](./functions-reference-node.md#considerations-for-javascript-functions)  |

1. Selecione **Seguinte: Monitorização**. Na página **de Monitorização,** insira as seguintes definições:

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Predefinição | Cria um recurso Application Insights com o mesmo *nome app* na região suportada mais próxima. Ao expandir esta definição, pode alterar o nome de **novo recurso** ou escolher uma **Localização** diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) para armazenar os seus dados. |

1. Selecione **Review + crie** para rever as seleções de configuração da aplicação.

1. Na página **'Rever + criar',** rever as definições e, em seguida, selecionar **Criar** para provisão e implementar a aplicação de função.

1. Selecione o ícone **notificações** no canto superior direito do portal e observe a mensagem **de implementação conseguida.**

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **Pin para painel de instrumentos.** A fixação facilita o regresso a esta função de recurso de aplicação a partir do seu dashboard.

1. Parabéns! Criou com sucesso a sua aplicação de função premium!

## <a name="create-azure-resources"></a>Criar recursos do Azure

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento separada da criada na criação inicial da sua aplicação de função é necessária para redes virtuais.

1. A partir do menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na página Nova, procure por **Conta de Armazenamento** e selecione **Criar**

1. No **separador Básicos,** defina as definições conforme especificado na tabela abaixo. O resto pode ser deixado como padrão:

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que criou com a sua aplicação de função. |
    | **Nome** | mysecurestorage| O nome da sua conta de armazenamento a que será aplicado o ponto final privado. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região em que criou a sua aplicação de função. |

1. Selecione **Rever + criar**. Após a validação concluída, **selecione Criar**.

### <a name="create-a-service-bus"></a>Criar um ônibus de serviço

1. A partir do menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na página Nova, procure **por Service Bus** e selecione **Create**.

1. No **separador Básicos,** defina as definições conforme especificado na tabela abaixo. O resto pode ser deixado como padrão:

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. |
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que criou com a sua aplicação de função. |
    | **Nome** | myServiceBus| O nome do seu ônibus de serviço ao qual será aplicado o ponto final privado. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região em que criou a sua aplicação de função. |
    | **Escalão de preço** | Premium | Escolha este nível para utilizar pontos finais privados com o Service Bus. |

1. Selecione **Rever + criar**. Após a validação concluída, **selecione Criar**.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Os recursos azure neste tutorial integram-se ou são colocados dentro de uma rede virtual. Você usará pontos finais privados para manter o tráfego de rede contido com a rede virtual.

O tutorial cria duas sub-redes:
- **padrão**: Sub-rede para pontos finais privados. Os endereços IP privados são dados a partir desta sub-rede.
- **funções**: Sub-rede para integração de rede virtual Azure Functions. Esta sub-rede é delegada na aplicação de função.

Agora, crie a rede virtual à qual a aplicação de função se integra.

1. A partir do menu do portal do Azure ou a partir da Home page, selecione **Criar um recurso**.

1. Na página Nova, procure por **Rede Virtual** e selecione **Criar**.

1. No **separador Básicos,** utilize as definições de rede virtual conforme especificado abaixo:

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que criou com a sua aplicação de função. |
    | **Nome** | myVirtualNet| O nome da sua rede virtual à qual a sua aplicação de função se irá ligar. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região em que criou a sua aplicação de função. |

1. No separador **endereços IP,** selecione **Adicionar sub-rede**. Utilize as definições conforme especificado abaixo ao adicionar uma sub-rede:

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Screenshot da visão de configuração de rede virtual criar.":::

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Nome da sub-rede** | funções | O nome da sub-rede a que a sua aplicação de função se ligará. | 
    | **Intervalo de endereços da sub-rede** | 10.0.1.0/24 | Note que o nosso espaço de endereço IPv4 na imagem acima é de 10.0.0.0/16. Se o acima foi de 10.1.0.0/16, o intervalo de *endereços recomendado sub-rede* seria de 10.1.1.0/24. |

1. Selecione **Rever + criar**. Após a validação concluída, **selecione Criar**.

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>Bloqueie a sua conta de armazenamento com pontos finais privados

Os Azure Private Endpoints são utilizados para se conectarem a recursos específicos do Azure utilizando um endereço IP privado. Esta ligação garante que o tráfego de rede permanece dentro da rede virtual escolhida, e o acesso está disponível apenas para recursos específicos. Agora, crie os pontos finais privados para o armazenamento de ficheiros Azure e o armazenamento Azure Blob com a sua conta de armazenamento.

1. Na sua nova conta de armazenamento, selecione **Networking** no menu esquerdo.

1. Selecione o **separador de ligações de ponto final privado** e selecione O ponto de **terminação privado**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Screenshot de como navegar para criar pontos finais privados para a conta de armazenamento.":::

1. No **separador Básicos,** utilize as definições de ponto final privado, conforme especificado abaixo:

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que criou com a sua aplicação de função. | |
    | **Nome** | ponto final de ficheiro | O nome do ponto final privado para ficheiros da sua conta de armazenamento. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região onde criou a sua conta de armazenamento. |

1. No separador **Recursos,** utilize as definições de ponto final privado, conforme especificado abaixo:

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.Storage/storageAcontas | Este é o tipo de recurso para contas de armazenamento. |
    | **Recurso** | mysecurestorage | A conta de armazenamento que acabou de criar |
    | **Recurso secundário de destino** | file | Este ponto final privado será utilizado para ficheiros a partir da conta de armazenamento. |

1. No **separador Configuração,** escolha o **predefinição** para a definição de Sub-rede.

1. Selecione **Rever + criar**. Após a validação concluída, **selecione Criar**. Os recursos na rede virtual podem agora falar com ficheiros de armazenamento.

1. Crie outro ponto final privado para bolhas. Para o separador **Recursos,** utilize as definições abaixo. Para todas as outras definições, utilize as mesmas definições dos passos de criação de ponto final privado de ficheiro que acabou de seguir.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.Storage/storageAcontas | Este é o tipo de recurso para contas de armazenamento. |
    | **Recurso** | mysecurestorage | A conta de armazenamento que acabou de criar |
    | **Recurso secundário de destino** | blob | Este ponto final privado será utilizado para bolhas da conta de armazenamento. |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>Bloqueie o seu ônibus de serviço com um ponto final privado

Agora, crie o ponto final privado para o seu Azure Service Bus.

1. No seu novo autocarro de serviço, selecione **Networking** no menu esquerdo.

1. Selecione o **separador de ligações de ponto final privado** e selecione O ponto de **terminação privado**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Screenshot de como navegar para pontos finais privados para ônibus de serviço.":::

1. No **separador Básicos,** utilize as definições de ponto final privado, conforme especificado abaixo:

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha o grupo de recursos que criou com a sua aplicação de função. |
    | **Nome** | sb-endpoint | O nome do ponto final privado para ficheiros da sua conta de armazenamento. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Escolha a região onde criou a sua conta de armazenamento. |

1. No separador **Recursos,** utilize as definições de ponto final privado, conforme especificado abaixo:

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **Tipo de recurso**  | Microsoft.ServiceBus/namespaces | Este é o tipo de recurso para o Service Bus. |
    | **Recurso** | myServiceBus | O Service Bus que criou anteriormente no tutorial. |
    | **Subresource-alvo** | espaço de nomes | Este ponto final privado será utilizado para o espaço de nome do autocarro de serviço. |

1. No **separador Configuração,** escolha o **predefinição** para a definição de Sub-rede.

1. Selecione **Rever + criar**. Após a validação concluída, **selecione Criar**. Os recursos na rede virtual podem agora falar com o autocarro de serviço.

## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

1. Na conta de armazenamento que criou, selecione **ações de ficheiro** no menu esquerdo.

1. Selecione **+ Partilhas de ficheiros**. Forneça **os ficheiros** como o nome da partilha de ficheiros para efeitos deste tutorial.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Screenshot de como criar uma partilha de ficheiros na conta de armazenamento.":::

## <a name="get-storage-account-connection-string"></a>Obtenha o fio de conexão de conta de armazenamento

1. Na conta de armazenamento que criou, selecione **as teclas de acesso** no menu esquerdo.

1. Selecione **as teclas Show**. Copie a cadeia de ligação da chave1 e guarde-a. Vamos precisar desta cadeia de ligação mais tarde ao configurar as definições da aplicação.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Screenshot de como obter uma cadeia de ligação de conta de armazenamento.":::

## <a name="create-a-queue"></a>Criar uma fila

Esta será a fila para a qual o seu Azure Functions Service Bus Trigger receberá eventos.

1. No seu autocarro de serviço, selecione **Queues** no menu esquerdo.

1. Selecione **políticas de acesso compartilhado**. Forneça **a fila** como o nome da fila para efeitos deste tutorial.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Screenshot de como criar uma fila de ônibus de serviço.":::

## <a name="get-service-bus-connection-string"></a>Obter cadeia de conexão de ônibus de serviço

1. No seu autocarro de serviço, selecione **Políticas de acesso partilhado** no menu esquerdo.

1. **Selecione RootManageSharedAccessKey**. Copie a **cadeia de ligação primária** e guarde-a. Vamos precisar desta cadeia de ligação mais tarde ao configurar as definições da aplicação.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Screenshot de como obter uma cadeia de ligação de autocarro de serviço.":::

## <a name="integrate-function-app-with-your-virtual-network"></a>Integrar app de função com a sua rede virtual

Para utilizar a sua aplicação de função com redes virtuais, terá de se juntar a ela a uma sub-rede. Utilizamos uma sub-rede específica para a integração da rede virtual Azure Functions e a sub-rede padrão para todos os outros pontos finais privados criados neste tutorial.

1. Na sua aplicação de função, selecione **Networking** no menu esquerdo.

1. Selecione **Clique aqui para configurar** em Integração VNet.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Screenshot de como navegar para a integração de rede virtual.":::

1. Selecione **Add VNet**

1. Na lâmina que se abre em **Rede Virtual,** selecione a rede virtual que criou anteriormente.

1. Selecione a **Sub-rede** que criamos anteriormente chamadas **funções**. A sua aplicação de função está agora integrada na sua rede virtual!

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Screenshot de como ligar uma aplicação de função a uma sub-rede.":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>Configure as definições da sua aplicação de função para pontos finais privados

1. Na sua aplicação de função, **selecione Configuração** a partir do menu esquerdo.

1. Para utilizar a sua aplicação de função com redes virtuais, as seguintes definições de aplicação terão de ser atualizadas. Selecione **+ Nova definição** de aplicação ou o lápis por **Editar** na coluna mais à direita da tabela de definições da aplicação, conforme apropriado. Quando terminar, selecione **Guardar**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Screenshot de como configurar as definições de aplicações de função para pontos finais privados.":::

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | A cadeia de ligação da conta de armazenamento que criou. Esta é a cadeia de ligação de armazenamento da cadeia de [ligação da conta de armazenamento](#get-storage-account-connection-string). Ao alterar esta definição, a sua aplicação de função irá agora utilizar a conta de armazenamento segura para operações normais em tempo de execução. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | A cadeia de ligação da conta de armazenamento que criou. Ao alterar esta definição, a sua aplicação de função irá agora utilizar a conta de armazenamento segura para ficheiros Azure, que são utilizados durante a implementação. |
    | **WEBSITE_CONTENTSHARE** | ficheiros | O nome da partilha de ficheiros que criou na conta de armazenamento. Esta configuração da aplicação é usada em conjunto com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Crie uma definição de aplicação para a cadeia de ligação do seu autocarro de serviço. Esta é a cadeia de ligação de armazenamento da cadeia de [ligação do autocarro](#get-service-bus-connection-string)de serviço .|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Crie esta configuração de aplicação. Um valor de 1 permite que a sua aplicação de função se dimensione quando tem a sua conta de armazenamento restrita a uma rede virtual. Deve ativar esta definição ao restringir a sua conta de armazenamento a uma rede virtual. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Crie esta configuração de aplicação. Uma vez que a sua aplicação se integre com uma rede virtual, utilizará o mesmo servidor DNS que a rede virtual. Esta é uma das duas configurações necessárias para que a sua aplicação de função funcione com zonas privadas Azure DNS e seja necessária quando se utiliza pontos finais privados. Estas definições enviarão todas as chamadas de saída da sua app para a sua rede virtual. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Crie esta configuração de aplicação. Uma vez que a sua aplicação se integre com uma rede virtual, utilizará o mesmo servidor DNS que a rede virtual. Esta é uma das duas configurações necessárias para que a sua aplicação de função funcione com zonas privadas Azure DNS e seja necessária quando se utiliza pontos finais privados. Estas definições enviarão todas as chamadas de saída da sua app para a sua rede virtual. |

1. Mantendo-se na vista **de Configuração,** selecione o separador **definições de tempo de execução da função.**

1. Desemisso de **escala de execução** para **on**, e selecione **Guardar**. O dimensionamento conduzido pelo tempo de execução permite-lhe ligar funções de gatilho não-HTTP a serviços que funcionam dentro da sua rede virtual.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Screenshot de como ativar o escalonamento conduzido pelo tempo de execução para funções Azure.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>Implementar um gatilho de autocarro de serviço e enviar o gatilho para a sua aplicação de função

1. No GitHub, navegue pelo repositório de amostras a seguir, que contém uma aplicação de função com duas funções, um HTTP Trigger e um Service Bus Queue Trigger.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. No topo da página, selecione o botão **Fork** para criar um garfo deste repositório na sua própria conta ou organização GitHub.

1. Na sua aplicação de função, selecione O Centro de **Implementação** a partir do menu esquerdo. Em seguida, selecione **Definições**.

1. No **separador Definições,** utilize as definições de implementação conforme especificado abaixo:

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Origem** | GitHub | Devias ter criado um repo GitHub com o código de amostra no passo 2. | 
    | **Organização**  | myOrganização | Esta é a organização em que o seu repo é registrado, normalmente a sua conta. |
    | **Repositório** | myRepo | A repo que criaste com o código de amostra. |
    | **Ramo** | main | Este é o repo que acabaste de criar, por isso usa o ramo principal. |
    | **Pilha de runtime** | .NET | O código de amostra está em C#. |

1. Selecione **Guardar**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Screenshot de como implementar código Azure Functions através do portal.":::

1. A sua implantação inicial pode demorar alguns minutos. Verá uma mensagem de Estado **de Sucesso (Ative)** no **separador Registares** quando a sua aplicação for implementada com sucesso. Se necessário, refresque a página. 

1. Parabéns! Implementou com sucesso a sua aplicação de função de amostra.

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>Bloqueie a sua aplicação de função com um ponto final privado

Agora, crie o ponto final privado para a sua aplicação de função. Este ponto final privado ligará a sua aplicação de função de forma privada e segura à sua rede virtual utilizando um endereço IP privado. Para obter mais informações sobre os pontos finais privados, aceda à [documentação dos pontos finais privados.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

1. Na sua aplicação de função, selecione **Networking** no menu esquerdo.

1. Selecione **Clique aqui para configurar** em Ligações de Ponto Final Privado.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Screenshot de como navegar para um Ponto De Final Privado de Aplicação de Função.":::

1. Selecione **Adicionar**.

1. No menu que se abre, utilize as definições de ponto final privado, conforme especificado abaixo:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Screenshot de como criar um ponto de terminamento privado da App de Função.":::

1. Selecione **Ok** para adicionar o ponto final privado. Parabéns! Conseguiu garantir com sucesso a sua aplicação de função, autocarro de serviço e conta de armazenamento com pontos finais privados!

### <a name="test-your-locked-down-function-app"></a>Teste o seu aplicativo de função bloqueado

1. Na sua aplicação de funções, selecione **Funções** a partir do menu esquerdo.

1. Selecione o **ServiceBusQueueTrigger**.

1. A partir do menu esquerdo, selecione **Monitor**. verá que não consegue monitorizar a sua aplicação. Isto porque o seu navegador não tem acesso à rede virtual, pelo que não pode aceder diretamente aos recursos dentro da rede virtual. Vamos agora demonstrar outro método através do qual ainda pode monitorizar a sua função, Application Insights.

1. Na sua aplicação de função, selecione **'Insights de Aplicação'** do menu esquerdo e selecione **os dados 'Insights de aplicação' de visualização.**

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Screenshot de como visualizar insights de aplicações para uma App de funções.":::

1. Selecione **métricas** ao vivo a partir do menu esquerdo.

1. Abra uma nova conta. No seu Service Bus, selecione **Filas** do menu esquerdo.

1. Selecione a sua fila.

1. Selecione **Service Bus Explorer** a partir do menu esquerdo. Em **Enviar**, escolha **Texto/Planície** como **o Tipo de Conteúdo** e introduza uma mensagem. 

1. **Selecione Enviar** para enviar a mensagem.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Screenshot de como enviar mensagens de Service Bus usando o portal.":::

1. No separador com as **métricas live abertas,** deve ver se o gatilho da fila do Service Bus disparou. Se não tiver, reencaia a mensagem do **Service Bus Explorer**

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Screenshot de como visualizar mensagens usando métricas ao vivo para aplicações de função.":::

1. Parabéns! Testou com sucesso a sua aplicação de função configurada com pontos finais privados!

### <a name="private-dns-zones"></a>Zonas privadas de DNS
Utilizar um ponto final privado para ligar aos recursos Azure significa ligar-se a um endereço IP privado em vez do ponto final público. Os serviços Azure existentes estão configurados para utilizar o DNS existente para ligar ao ponto final público. A configuração do DNS terá de ser ultrapassada para ligar ao ponto final privado.

Foi criada uma zona privada de DNS para cada recurso Azure configurado com um ponto final privado. É criado um registo DNS A para cada endereço IP privado associado ao ponto final privado.

As seguintes zonas DNS foram criadas neste tutorial:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma aplicação de função Premium, uma conta de armazenamento e um Service Bus, e garantiu-os a todos atrás de pontos finais privados! Saiba mais sobre as várias funcionalidades de networking disponíveis abaixo:

> [!div class="nextstepaction"]
> [Saiba mais sobre as opções de networking em Funções](./functions-networking-options.md)

[Plano Premium]: functions-premium-plan.md
