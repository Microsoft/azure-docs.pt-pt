---
title: Integrar o ônibus de serviço azure com o Serviço de Ligação Privada Azure
description: Saiba como integrar o Ônibus de Serviço Azure com o Serviço de Link Privado Azure
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: a78375a3acf5c56d9a59c0f4b6113a063f8c431a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650962"
---
# <a name="integrate-azure-service-bus-with-azure-private-link"></a>Integrar ônibus de serviço Azure com ligação privada Azure

O Azure Private Link Service permite-lhe aceder aos serviços Azure (por exemplo, Azure Service Bus, Azure Storage e Azure Cosmos DB) e o Azure acolheu serviços de clientes/parceiros sobre um **ponto final privado** na sua rede virtual.

Um ponto final privado é uma interface de rede que o conecta de forma privada e segura a um serviço alimentado por Azure Private Link. O ponto final privado utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a uma instância de um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acesso.

Para mais informações, consulte [o que é azure private link?](../private-link/private-link-overview.md)

>[!WARNING]
> A implementação de pontos finais privados pode impedir que outros serviços Azure interajam com o Service Bus.
>
> Os serviços fidedignos da Microsoft não são suportados quando utilizam redes virtuais.
>
> Cenários Comuns Azure que não funcionam com Redes Virtuais (note que a lista **NÃO** é exaustiva) -
> - Integração com a Grelha de Eventos Azure
> - Rotas do Hub Azure IoT
> - Explorador de dispositivos Azure IoT
>
> Os serviços abaixo da Microsoft são obrigados a estar numa rede virtual
> - Serviço de Aplicações do Azure
> - Funções do Azure

> [!IMPORTANT]
> Esta funcionalidade é suportada com o nível **premium** do Azure Service Bus. Para mais informações sobre o nível premium, consulte o artigo service [bus premium e standard de mensagens tiers.](service-bus-premium-messaging.md)


## <a name="add-a-private-endpoint-using-azure-portal"></a>Adicione um ponto final privado usando o portal Azure

### <a name="prerequisites"></a>Pré-requisitos

Para integrar um espaço de nome de ônibus de serviço com Ligação Privada Azure, você precisará das seguintes entidades ou permissões:

- Um espaço de nome de ônibus de serviço.
- Uma rede virtual Azure.
- Uma sub-rede na rede virtual.
- Permissões do proprietário ou do contribuinte tanto para o espaço de nome do Autocarro de Serviço como para a rede virtual.

O seu ponto final privado e a sua rede virtual devem estar na mesma região. Quando se leciona uma região para o ponto final privado utilizando o portal, filtrará automaticamente apenas redes virtuais que se encontram naquela região. O seu espaço de nome service Bus pode estar numa região diferente. E o seu ponto final privado usa um endereço IP privado na sua rede virtual.

### <a name="steps"></a>passos

Se já tem um espaço de nome existente, pode criar um ponto final privado seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, digite em **Service Bus**.
3. Selecione o espaço de **nome** da lista à qual pretende adicionar um ponto final privado.
4. Selecione o separador **de rede** em **Definições**.
5. Selecione o separador de **ligações de ponto final privado** na parte superior da página
6. Selecione o botão **+ Ponto Final Privado** na parte superior da página.

    ![Adicione botão de ponto final privado](./media/private-link-service/private-link-service-3.png)
7. Na página **Basics,** siga estes passos: 
    1. Selecione a **subscrição Azure** na qual pretende criar o ponto final privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto final privado.
    3. Insira um **nome** para o ponto final privado. 
    5. Selecione uma **região** para o ponto final privado. O seu ponto final privado deve estar na mesma região que a sua rede virtual, mas pode estar numa região diferente do recurso de ligação privada a que está a ligar. 
    6. Selecione **Seguinte: O** botão de recursos >na parte inferior da página.

        ![Criar endpoint privado - página básica](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página **Recurso,** siga estes passos:
    1. Para obter o método de ligação, se selecionar **Ligar a um recurso Azure no meu diretório,** siga estes passos:   
        1. Selecione a **subscrição Azure** na qual existe o seu espaço de nome sinuoso do **Ônibus de serviço.** 
        2. Para **o tipo de recurso,** selecione **Microsoft.ServiceBus/espaços** de nome para o **tipo de Recurso**.
        3. Para **Recurso**, selecione um espaço de nome do Ônibus de serviço na lista de lançamentos. 
        4. Confirme se o **subrecurso Target** está definido para **o espaço de nome**.
        5. Selecione **Seguinte: Configuração >** botão na parte inferior da página. 
        
            ![Criar endpoint privado - página de recursos](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Se selecionar **Ligar a um recurso Azure por id ou pseudónimo**de recurso, siga estes passos:
        1. Introduza o ID ou **pseudónimo**do **recurso.** Pode ser a identificação de recursos ou pseudónimo que alguém partilhou consigo. A maneira mais fácil de obter o ID de recurso é navegar para o espaço de nome do Ônibus de serviço no portal Azure e copiar a parte de URI a partir de `/subscriptions/` . Veja a seguinte imagem, por exemplo. 
        2. Para **sub-recurso Target,** introduza **o espaço de nome**. É o tipo de sub-recurso a que o seu ponto final privado pode aceder. 
        3. (opcional) Introduza uma **mensagem de pedido.** O proprietário do recurso vê esta mensagem enquanto gere a ligação de ponto final privado. 
        4. Em seguida, selecione **Seguinte: Configuração >** botão na parte inferior da página. 

            ![Criar endpoint privado - ligue usando id de recurso](./media/private-link-service/connect-resource-id.png)
9. Na página **de Configuração,** selecione a subnet numa rede virtual para onde pretende implementar o ponto final privado. 
    1. Selecione uma **rede virtual**. Apenas redes virtuais na subscrição e localização atualmente selecionadas estão listadas na lista de drop-down. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **Seguinte: Etiquetas >** botão na parte inferior da página. 

        ![Criar ponto final privado - página de configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **Tags,** crie quaisquer tags (nomes e valores) que queira associar ao recurso de ponto final privado. Em seguida, selecione **Review + crie** o botão na parte inferior da página. 
11. No **Review + criar,** reveja todas as definições e selecione **Criar** para criar o ponto final privado.
    
    ![Criar endpoint privado - Rever e Criar página](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Confirme que o ponto final privado é criado. Se é o proprietário do recurso e selecionou **o Connect a um recurso Azure na minha** opção de diretório para o método de **Ligação,** a ligação de ponto final deve ser **aprovada automaticamente**. Se estiver no estado **pendente,** consulte os [pontos finais privados manage usando](#manage-private-endpoints-using-azure-portal) a secção do portal Azure.

    ![Ponto final privado criado](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Adicione um ponto final privado usando powerShell
O exemplo que se segue mostra como usar o Azure PowerShell para criar uma ligação de ponto final privado a um espaço de nome de autocarro de serviço.

O seu ponto final privado e a sua rede virtual devem estar na mesma região. O seu espaço de nome service Bus pode estar numa região diferente. E o seu ponto final privado usa um endereço IP privado na sua rede virtual.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```


## <a name="manage-private-endpoints-using-azure-portal"></a>Gerir pontos finais privados usando o portal Azure

Quando se cria um ponto final privado, a ligação tem de ser aprovada. Se o recurso para o qual está a criar um ponto final privado estiver no seu diretório, pode aprovar o pedido de ligação desde que tenha permissões suficientes. Se estiver a ligar-se a um recurso Azure noutro diretório, tem de esperar que o proprietário desse recurso aprove o seu pedido de ligação.

Existem quatro estados de provisionamento:

| Ação de serviço | Estado de ponto final privado do consumidor de serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A ligação é criada manualmente e está pendente de aprovação do proprietário do recurso Private Link. |
| Aprovar | Aprovado | A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada. |
| Rejeitar | Rejected | A ligação foi rejeitada pelo proprietário de recursos de ligação privada. |
| Remover | Desligado | A ligação foi removida pelo proprietário do recurso de ligação privada, o ponto final privado torna-se informativo e deve ser eliminado para limpeza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprovar, rejeitar ou remover uma ligação de ponto final privado

1. Inicie sessão no Portal do Azure.
1. Na barra de pesquisa, digite em **Service Bus**.
1. Selecione o espaço de **nome** que pretende gerir.
1. Selecione o separador **Networking.**
5. Vá à secção adequada abaixo com base na operação que pretende: aprovar, rejeitar ou remover. 

### <a name="approve-a-private-endpoint-connection"></a>Aprovar uma ligação de ponto final privado

1. Se houver alguma ligação pendente, verá uma ligação listada com **pendente** no estado de provisionamento. 
2. Selecione o **ponto final privado** que deseja aprovar
3. Selecione o botão **'Aprovar'.**

    ![Aprovar ponto final privado](./media/private-link-service/private-endpoint-approve.png)
4. Na página de **ligação Aprovar,** introduza um **comentário**opcional , e selecione **Sim**. Se escolher **"Não",** nada acontece. 

    ![Aprovar página de ligação](./media/private-link-service/approve-connection-page.png)
5. Deve ver o estado da ligação na lista alterado para **Aprovado**. 

    ![Estado de ligação - aprovado](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma ligação de ponto final privado

1. Se houver alguma ligação de ponto final privado que queira rejeitar, seja um pedido pendente ou uma ligação existente que tenha sido aprovada anteriormente, selecione a ligação de ponto final e clique no botão **Rejeitar.**

    ![Rejeitar botão](./media/private-link-service/private-endpoint-reject.png)
2. Na página de **ligação Rejeitar,** introduza um comentário opcional e selecione **Sim**. Se escolher **"Não",** nada acontece. 

    ![Rejeitar página de ligação](./media/private-link-service/reject-connection-page.png)
3. Deve ver o estado da ligação na lista alterado **Rejeitado**. 

    ![Ponto final rejeitado](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Remover uma ligação de ponto final privado

1. Para remover uma ligação de ponto final privado, selecione-a na lista e **selecione Remover** na barra de ferramentas. 

    ![Remover o botão](./media/private-link-service/remove-endpoint.png)
2. Na página de **ligação Delete,** selecione **Sim** para confirmar a eliminação do ponto final privado. Se escolher **"Não",** nada acontece. 

    ![Excluir página de ligação](./media/private-link-service/delete-connection-page.png)
3. Deve ver o estado alterado para **Desligado**. Então, verá sairá o ponto final da lista. 

## <a name="validate-that-the-private-link-connection-works"></a>Validar que a ligação de ligação privada funciona

Deve validar que os recursos dentro da mesma sub-rede do recurso de ponto final privado estão ligados ao seu espaço de nome do Bus de Serviço sobre um endereço IP privado, e que eles têm a correta integração privada da zona DNS.

Primeiro, crie uma máquina virtual seguindo os passos em [Criar uma máquina virtual Windows no portal Azure](../virtual-machines/windows/quick-create-portal.md)

No separador **Networking:** 

1. Especificar **rede virtual** e **Subnet**. Deve selecionar a Rede Virtual na qual implantou o ponto final privado.
2. Especifique um recurso **IP público.**
3. Para o grupo de **segurança da rede NIC,** selecione **Nenhum**.
4. Para **equilibrar a carga,** selecione **Não**.

Ligue-se ao VM, abra a linha de comando e execute o seguinte comando:

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

Devia ver um resultado que se parece com o seguinte. 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e Considerações de Design

**Preços**: Para obter informações sobre preços, consulte os preços do [Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Limitações**: Esta funcionalidade está disponível em todas as regiões públicas do Azure.

**Número máximo de pontos finais privados por espaço de nome do Autocarro de Serviço:** 120.

Para mais informações, consulte [o serviço Azure Private Link: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [o Link Privado Azure](../private-link/private-link-service-overview.md)
- Saiba mais sobre [o Ônibus de Serviço Azure](service-bus-messaging-overview.md)
