---
title: Control Azure Functions outbound IP com uma rede virtual Azure NAT gateway
description: Um tutorial passo a passo que mostra como configurar o NAT para uma função ligada a uma rede virtual Azure
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 5bb491e367ed813f09197a193745c231261c88c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104658162"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Tutorial: Control Azure Functions outbound IP com uma rede virtual Azure NAT gateway

A tradução de endereços de rede virtual (NAT) simplifica a conectividade de internet apenas de saída para redes virtuais. Quando configurados numa sub-rede, toda a conectividade de saída utiliza os seus endereços IP públicos estáticos especificados. Um NAT pode ser útil para Funções Azure ou Aplicações Web que precisam consumir um serviço de terceiros que usa um allowlist de endereço IP como uma medida de segurança. Para saber mais, veja [o que é a Rede Virtual NAT?](../virtual-network/nat-overview.md)

Este tutorial mostra-lhe como usar NATs de rede virtual para encaminhar o tráfego de saída a partir de uma função desencadeada HTTP. Esta função permite-lhe verificar o seu próprio endereço IP de saída. Durante este tutorial, você:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar uma aplicação de função de plano Premium
> * Crie um endereço IP público
> * Criar um portal NAT
> * Configure a app de função para encaminhar o tráfego de saída através do gateway NAT

## <a name="topology"></a>Topologia

O seguinte diagrama mostra a arquitetura da solução que cria:

![UI para integração de gateways NAT](./media/functions-how-to-use-nat-gateway/topology.png)

As funções em execução no plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Azure App Service, que inclui a funcionalidade de Integração VNet. Para saber mais sobre a Integração VNet, incluindo resolução de problemas e configuração avançada, consulte [Integrar a sua aplicação com uma rede virtual Azure.](../app-service/web-sites-integrate-with-vnet.md)

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é importante que compreenda a endereçamento e sub-rede ip. Pode começar por [este artigo que cobre o básico de endereçar e sub-rede](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Muitos mais artigos e vídeos estão disponíveis online.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se já tiver concluído as [Funções de integração com um tutorial de rede virtual Azure,](./functions-create-vnet.md) pode saltar para [criar uma função de gatilho HTTP](#create-function).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. No menu do portal do Azure, selecione **Criar um recurso**. A partir do Mercado Azure, **selecione rede** Virtual de Rede  >  .

1. Na **criação de rede virtual,** introduza ou selecione as definições especificadas como mostrado no quadro seguinte:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | **Selecione Criar novo,** insira *o myResourceGroup* e, em seguida, selecione **OK**. |
    | Name | Insira *myResourceGroup-vnet*. |
    | Localização | Selecione **East US**.|

1. Selecione **Seguinte: Endereços IP**, e para **o espaço de endereço IPv4**, insira *10.10.0.0/16*.

1. **Selecione Adicionar a sub-rede**, em seguida, *introduza Tutorial-Net* para **o nome da sub-rede** e *10.10.1.0/24* para **intervalo de endereços sub-rede**.

    ![Endereços IP separados para criar uma vnet](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. Selecione **Adicionar** e, em seguida, selecione **Rever + criar**. Deixe o resto como padrão e **selecione Criar**.

1. In **Create virtual network,** selecione **Create**.

Em seguida, cria-se uma aplicação de função no [plano Premium.](functions-premium-plan.md) Este plano fornece escala sem servidor enquanto suporta a integração de rede virtual.

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar uma aplicação de função num plano Premium

> [!NOTE]  
> Para obter a melhor experiência neste tutorial, escolha .NET para a pilha de tempo de execução e escolha o Windows para o sistema operativo. Além disso, crie a sua aplicação de função na mesma região que a sua rede virtual.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>Ligue a sua aplicação de função à rede virtual

Agora pode ligar a sua aplicação de função à rede virtual.

1. Na sua aplicação de função, selecione **Networking** no menu esquerdo e, em seguida, em **VNet Integration**, selecione **Clique aqui para configurar**.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="Escolha o networking na aplicação de função":::

1. Na página **de Integração VNET,** selecione **Add VNet**.

1. No **Estado da Funcionalidade de Rede,** utilize as definições na tabela abaixo da imagem:

    ![Defina a rede virtual da aplicação de função](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Rede Virtual** | MyResourceGroup-vnet | Esta rede virtual foi a que criou anteriormente. |
    | **Sub-rede** | Criar nova sub-rede | Crie uma sub-rede na rede virtual para a sua aplicação de função utilizar. A integração VNet deve ser configurada para utilizar uma sub-rede vazia. |
    | **Nome da sub-rede** | Function-Net | Nome da nova sub-rede. |
    | **Bloco de endereço de rede virtual** | 10.10.0.0/16 | Deve ter apenas um bloco de endereços definido. |
    | **Bloco de endereços de sub-rede** | 10.10.2.0/24   | O tamanho da sub-rede restringe o número total de casos que a sua aplicação de função de plano Premium pode escalar para. Este exemplo utiliza uma `/24` sub-rede com 254 endereços de anfitrião disponíveis. Esta sub-rede é excessivamente a provisionada, mas fácil de calcular. |

1. Selecione **OK** para adicionar a sub-rede. Feche as páginas de Estado de Integração e **Funcionalidade de Rede** **VNet** para voltar à página da aplicação da função.

A aplicação de função pode agora aceder à rede virtual. Em seguida, irá adicionar uma função desencadeada por HTTP à aplicação de função.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Criar uma função de gatilho HTTP

1. A partir do menu esquerdo da janela **Funções,** selecione **Funções** e selecione **Adicione** no menu superior. 
 
1. A partir da janela **Nova Função,** selecione **'Desír do gatilho'** e aceite o nome predefinido para **Nova Função**, ou introduza um novo nome. 

1. No **Código + Teste**, substitua o código de script C# gerado pelo modelo (.csx) pelo seguinte código: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Este código chama um website externo que devolve o endereço IP do autor da chamada, que neste caso é esta função. Este método permite-lhe determinar facilmente o endereço IP de saída que está a ser utilizado pela sua aplicação de função.

Agora está pronto para executar a função e verificar os atuais IPs de saída.

## <a name="verify-current-outbound-ips"></a>Verificar iPs de saída corrente

Agora, pode executar a função. Mas primeiro, verifique no portal e veja quais os IPs de saída que estão a ser usados pela aplicação de função.  

1. Na sua aplicação de função, selecione **Propriedades** e reveja o campo **endereços IP de saída.**

    ![Ver endereços IP de saída de aplicativos de função](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. Agora, volte à função de gatilho HTTP, selecione **Código + Teste** e, em seguida, **Teste/Execução**.

    ![Função de teste](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. Selecione **Executar** para executar a função e, em seguida, mudar para a **Saída**. 

    ![Saída da função de teste](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. Verifique se o endereço IP no organismo de resposta HTTP é um dos valores dos endereços IP de saída visualizados anteriormente.

Agora, você pode criar um IP público e usar um gateway NAT para modificar este endereço IP de saída.

## <a name="create-public-ip"></a>Criar IP público

1. A partir do seu grupo de recursos, **selecione Adicionar,** procure no Azure Marketplace para **endereço IP público** e selecione **Criar**. Utilize as definições na tabela abaixo da imagem:

    ![Criar endereço IP público](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Definição      | Valor sugerido  |
    | ------------ | ---------------- |
    | **Versão IP** | IPv4 |
    | **SKU** | Standard |
    | **Escalão de serviço** | Regional |
    | **Nome** | Outbound-IP |
    | **Subscrição** | garantir que a sua subscrição é exibida | 
    | **Grupo de recursos** | myResourceGroup (ou nome que atribuiu ao seu grupo de recursos) |
    | **Localização** | Leste dos EUA (ou localização que atribuiu aos seus outros recursos) |
    | **Zona de Disponibilidade** | Sem Zona |

1. **Selecione Criar** para submeter a implementação.

1. Assim que a implementação estiver concluída, navegue para o seu novo recurso de Endereço IP Público e veja o Endereço IP na **Visão Geral.**

    ![Ver Endereço IP Público](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>Criar um NAT gateway

Agora, vamos criar o portal NAT. Quando se começa com o [tutorial de rede virtual anterior,](functions-create-vnet.md)foi o nome sugerido da `Function-Net` sub-rede e `MyResourceGroup-vnet` foi o nome de rede virtual sugerido nesse tutorial.

1. A partir do seu grupo de recursos, **selecione Add,** procure no Azure Marketplace para **o gateway NAT,** e selecione **Criar**. Utilize as definições na tabela abaixo da imagem para preencher o **separador Básicos:**

    ![Criar um NAT gateway](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Definição      | Valor sugerido  |
    | ------------ | ---------------- |  
    | **Subscrição** | A sua subscrição | 
    | **Grupo de recursos** | myResourceGroup (ou nome que atribuiu ao seu grupo de recursos) |
    | **Nome do gateway NAT** | myNatGateway |
    | **Região** | Leste dos EUA (ou localização que atribuiu aos seus outros recursos) |
    | **Zona de Disponibilidade** | Nenhum |

1. Selecione **Seguinte: OUTBOUND IP**. No campo **dos endereços IP públicos,** selecione o endereço IP público previamente criado. Deixar **prefixos IP públicos** não selecionados.

1. Selecione **Seguinte: Sub-rede**. Selecione o recurso *myResourceGroup-vnet* no campo **de rede Virtual** e na *sub-rede Function-Net.*

    ![Selecione sub-rede](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. Selecione **Rever + Criar** e criar para submeter a implementação. 

Uma vez concluída a implementação, o gateway NAT está pronto para encaminhar o tráfego da sua sub-rede de aplicações de função para a Internet.

## <a name="update-function-configuration"></a>Configuração da função de atualização

Agora, deve adicionar uma definição de aplicação `WEBSITE_VNET_ROUTE_ALL` definida para um valor de `1` .  Esta definição força o tráfego de saída através da rede virtual e gateway NAT associado. Sem esta definição, o tráfego de internet não é encaminhado através da rede virtual integrada, e você verá os mesmos IPs de saída. 

1. Navegue para a sua aplicação de função no portal Azure e **selecione Configuração** a partir do menu da mão esquerda.

1. Nas **definições de Aplicação**, selecione **+ Nova definição de aplicação** e use completamente os seguintes valores para preencher os campos:

    |Nome do Campo  |Valor |
    |---|---|
    |**Nome**    |WEBSITE_VNET_ROUTE_ALL|
    |**Valor**   |1|

1. Selecione **OK** para fechar o novo diálogo de definição de aplicação.

1. **Selecione Guardar** e, em seguida, **Continue** a guardar as definições.

A aplicação de função está agora configurada para encaminhar o tráfego através da sua rede virtual associada.

## <a name="verify-new-outbound-ips"></a>Verificar novos IPs de saída

Repita [os passos mais cedo](#verify-current-outbound-ips) para voltar a executar a função. Deve agora ver o endereço IP de saída que configura na NAT mostrada na saída da função.

## <a name="clean-up-resources"></a>Limpar os recursos

Criou recursos para completar este tutorial. Você será cobrado por estes recursos, dependendo do estado da sua [conta](https://azure.microsoft.com/account/) e [preço do serviço.](https://azure.microsoft.com/pricing/) Para evitar incorrer em custos adicionais, elimine os recursos quando souber que mais tempo precisa deles. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Opções de rede das Funções do Azure](functions-networking-options.md)
