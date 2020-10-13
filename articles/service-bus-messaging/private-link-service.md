---
title: Integre o ônibus de serviço Azure com o Azure Private Link Service
description: Saiba como integrar o Azure Service Bus com o Azure Private Link Service
author: spelluru
ms.author: spelluru
ms.date: 10/07/2020
ms.topic: article
ms.openlocfilehash: 54649c47a896937a512a6041e485abfb03ca88dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91824967"
---
# <a name="allow-access-to-azure-service-bus-namespaces-via-private-endpoints"></a>Permitir o acesso aos espaços de nomes do Azure Service Bus através de pontos finais privados

O Azure Private Link Service permite-lhe aceder aos serviços Azure (por exemplo, Azure Service Bus, Azure Storage e Azure Cosmos DB) e a Azure acolheu serviços de cliente/parceiro sobre um **ponto final privado** na sua rede virtual.

Um ponto final privado é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acessos.

Para mais informações, consulte [o que é a Azure Private Link?](../private-link/private-link-overview.md)

>[!WARNING]
> A implementação de pontos finais privados pode impedir que outros serviços da Azure interajam com a Service Bus.
>
> Os serviços da Microsoft fidedignos não são suportados quando utilizam redes virtuais.
>
> Cenários de Azure comuns que não funcionam com redes virtuais (note que a lista **NÃO** é exaustiva) -
> - Integração com a grelha de eventos Azure
> - Rotas do Hub Azure IoT
> - Explorador de dispositivos Azure IoT
>
> Os serviços abaixo da Microsoft são necessários para estar em uma rede virtual
> - Serviço de Aplicações do Azure
> - Funções do Azure

> [!IMPORTANT]
> Esta funcionalidade é suportada com o nível **premium** da Azure Service Bus. Para obter mais informações sobre o nível premium, consulte o artigo [de níveis de mensagens Service Bus Premium e Standard.](service-bus-premium-messaging.md)


## <a name="add-a-private-endpoint-using-azure-portal"></a>Adicione um ponto final privado usando o portal Azure

### <a name="prerequisites"></a>Pré-requisitos

Para integrar um espaço de nomes de Service Bus com a Azure Private Link, necessitará das seguintes entidades ou permissões:

- Um espaço de nomes de autocarro de serviço.
- Uma rede virtual Azure.
- Uma sub-rede na rede virtual. Pode utilizar a sub-rede **predefinida.** 
- Permissões de proprietário ou contribuinte tanto para o espaço de nome do Service Bus como para a rede virtual.

O seu ponto final privado e rede virtual devem estar na mesma região. Quando selecionar uma região para o ponto final privado utilizando o portal, irá filtrar automaticamente apenas redes virtuais que se encontrem nessa região. O seu espaço de nomes de ônibus de serviço pode estar numa região diferente. E, o seu ponto final privado utiliza um endereço IP privado na sua rede virtual.

### <a name="steps"></a>passos

Se já tiver um espaço de nome existente, pode criar um ponto final privado seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, escreva em **Service Bus.**
3. Selecione o **espaço de nomes** da lista à qual pretende adicionar um ponto final privado.
2. No menu esquerdo, selecione a opção **de rede** em **Definições**. 

    > [!NOTE]
    > Você vê o **separador Networking** apenas para espaços de nome **premium.**  
    
    Por predefinição, a opção **de rede Selecionada** é selecionada. Se não adicionar pelo menos uma regra de firewall IP ou uma rede virtual nesta página, o espaço de nome pode ser acedido através da internet pública (utilizando a chave de acesso).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Página de rede - padrão" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Se selecionar a opção **Todas as redes,** o seu espaço de nomes Service Bus aceita ligações a partir de qualquer endereço IP (utilizando a chave de acesso). Esta definição predefinida é equivalente a uma regra que aceita o intervalo de endereço IP 0.0.0.0/0. 

    ![Firewall - Todas as opções de redes selecionadas](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
5. Para permitir o acesso ao espaço de nomes através de pontos finais privados, selecione o **separador de ligações de ponto final privado** no topo da página
6. Selecione o botão **+ Ponto final privado** na parte superior da página.

    ![Adicione botão de ponto final privado](./media/private-link-service/private-link-service-3.png)
7. Na página **Basics,** siga estes passos: 
    1. Selecione a **subscrição Azure** na qual pretende criar o ponto final privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto final privado.
    3. Insira um **nome** para o ponto final privado. 
    5. Selecione uma **região** para o ponto final privado. O seu ponto final privado deve estar na mesma região que a sua rede virtual, mas pode estar numa região diferente do recurso de ligação privada a que está a ligar. 
    6. Selecione **seguinte: O ** botão >de recursos na parte inferior da página.

        ![Criar Ponto Final Privado - Página básica](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página **de Recursos,** siga estes passos:
    1. Para o método de ligação, se selecionar **Ligar a um recurso Azure no meu diretório,** siga estes passos:   
        1. Selecione a **subscrição Azure** na qual existe o seu **espaço de nomes service bus.** 
        2. Para **o tipo de recurso**, selecione **Microsoft.ServiceBus/namespaces** para o tipo de **recurso**.
        3. Para **obter recursos**, selecione um espaço de nomes de Service Bus da lista de drop-down. 
        4. Confirme que o **subresource do Target** está definido para **o espaço de nomes**.
        5. Selecione **Seguinte: Configuração >** botão na parte inferior da página. 
        
            ![Criar Ponto Final Privado - Página de recursos](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Se selecionar **Ligar a um recurso Azure por ID ou pseudónimo**de recurso, siga estes passos:
        1. Introduza o ID ou **o pseudónimo do** **recurso.** Pode ser o ID de recursos ou pseudónimo que alguém partilhou consigo. A maneira mais fácil de obter o ID de recurso é navegar para o espaço de nomes do Service Bus no portal Azure e copiar a porção de URI a partir de `/subscriptions/` . Veja a seguinte imagem para um exemplo. 
        2. Para **sub-recurso target,** insira **o espaço de nomes**. É o tipo de sub-recurso a que o seu ponto final privado pode aceder. 
        3. (opcional) Introduza uma **mensagem de pedido**. O proprietário do recurso vê esta mensagem enquanto gere a ligação privada de ponto final. 
        4. Em seguida, selecione **Seguinte: Configuração >** botão na parte inferior da página. 

            ![Create Private Endpoint - conecte-se usando iD de recurso](./media/private-link-service/connect-resource-id.png)
9. Na página **Configuração,** seleciona a sub-rede numa rede virtual para onde pretende implantar o ponto final privado. 
    1. Selecione uma **rede virtual.** Apenas as redes virtuais na subscrição e localização atualmente selecionadas estão listadas na lista de drop-down. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **Seguinte: Tags >** botão na parte inferior da página. 

        ![Criar Ponto Final Privado - Página de configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **Tags,** crie quaisquer tags (nomes e valores) que pretenda associar ao recurso de ponto final privado. Em seguida, **selecione Rever + criar** botão na parte inferior da página. 
11. No **Review + criar,** rever todas as definições e selecionar **Criar** para criar o ponto final privado.
    
    ![Criar ponto final privado - Rever e criar página](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Confirme que o ponto final privado foi criado. Se é o proprietário do recurso e selecionou **Connect a um recurso Azure na minha** opção de diretório para o método De **Ligação,** a ligação de ponto final deve ser **auto-aprovada**. Se estiver no estado **pendente,** consulte os [pontos finais privados da Manage utilizando a secção do portal Azure.](#manage-private-endpoints-using-azure-portal)

    ![Ponto final privado criado](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Adicione um ponto final privado usando PowerShell
O exemplo que se segue mostra-lhe como utilizar o Azure PowerShell para criar uma ligação privada de ponto final a um espaço de nomes de Service Bus.

O seu ponto final privado e rede virtual devem estar na mesma região. O seu espaço de nomes de ônibus de serviço pode estar numa região diferente. E, o seu ponto final privado utiliza um endereço IP privado na sua rede virtual.

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

Quando criar um ponto final privado, a ligação tem de ser aprovada. Se o recurso para o qual está a criar um ponto final privado estiver no seu diretório, pode aprovar o pedido de ligação desde que tenha permissões suficientes. Se estiver a ligar-se a um recurso Azure noutro diretório, tem de esperar que o proprietário desse recurso aprove o seu pedido de ligação.

Existem quatro estados de provisionamento:

| Ação de serviço | Estado de ponto final privado do consumidor de serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A ligação é criada manualmente e está pendente de aprovação do proprietário do recurso Private Link. |
| Aprovar | Aprovado | A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada. |
| Rejeitar | Rejeitado | A ligação foi rejeitada pelo proprietário de recursos de ligação privada. |
| Remover | Desligado | A ligação foi removida pelo proprietário do recurso de ligação privada, o ponto final privado torna-se informativo e deve ser eliminado para limpeza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprovar, rejeitar ou remover uma ligação de ponto final privado

1. Inicie sessão no Portal do Azure.
1. Na barra de pesquisa, escreva em **Service Bus.**
1. Selecione o **espaço de nomes** que pretende gerir.
1. Selecione o **separador 'Rede'.**
5. Vá à secção abaixo com base na operação que pretende: aprovar, rejeitar ou remover. 

### <a name="approve-a-private-endpoint-connection"></a>Aprovar uma ligação privada de ponto final

1. Se houver alguma ligação pendente, verá uma ligação listada com **pendente** no estado de provisionamento. 
2. Selecione o **ponto final privado** que deseja aprovar
3. Selecione o botão **Aprovar.**

    ![Aprovar ponto final privado](./media/private-link-service/private-endpoint-approve.png)
4. Na página **de ligação Aprovar,** insira um **comentário**opcional e selecione **Sim**. Se **escolheres o Nº,** nada acontece. 

    ![Aprovar página de conexão](./media/private-link-service/approve-connection-page.png)
5. Deverá ver o estado da ligação na lista alterado para **Aprovado**. 

    ![Estado de ligação - aprovado](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma ligação privada de ponto final

1. Se houver alguma ligação de ponto final privado que pretenda rejeitar, seja um pedido pendente ou uma ligação existente que tenha sido aprovada anteriormente, selecione a ligação de ponto final e clique no botão **Rejeitar.**

    ![Botão de rejeitar](./media/private-link-service/private-endpoint-reject.png)
2. Na página **de ligação Rejeitar,** insira um comentário opcional e selecione **Sim**. Se **escolheres o Nº,** nada acontece. 

    ![Rejeitar página de ligação](./media/private-link-service/reject-connection-page.png)
3. Deve ver o estado da ligação na lista alterado **Rejeitado**. 

    ![Endpoint rejeitado](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Remover uma ligação de ponto final privado

1. Para remover uma ligação de ponto final privado, selecione-a na lista e selecione **Remover** na barra de ferramentas. 

    ![Remover botão](./media/private-link-service/remove-endpoint.png)
2. Na página **de ligação Eliminar,** selecione **Sim** para confirmar a eliminação do ponto final privado. Se **escolheres o Nº,** nada acontece. 

    ![Eliminar página de ligação](./media/private-link-service/delete-connection-page.png)
3. Deve ver o estado alterado para **Desligado**. Então, verá o ponto final desaparecer da lista. 

## <a name="validate-that-the-private-link-connection-works"></a>Validar que a ligação de ligação privada funciona

Você deve validar que os recursos dentro da rede virtual do ponto final privado estão conectando-se ao seu espaço de nome de Service Bus em um endereço IP privado, e que eles têm a integração de zona de DNS privada correta.

Primeiro, crie uma máquina virtual seguindo os passos na [Criar uma máquina virtual Windows no portal Azure](../virtual-machines/windows/quick-create-portal.md)

No **separador Networking:** 

1. Especificar **rede virtual** e **sub-rede.** Tem de selecionar a Rede Virtual na qual implementou o ponto final privado.
2. Especifique um recurso **IP público.**
3. Para **o grupo de segurança da rede NIC**, selecione **Nenhum**.
4. Para **equilibrar a carga**, selecione **Nº**.

Ligue ao VM, abra a linha de comando e execute o seguinte comando:

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

Devia ver um resultado que se pareça com o seguinte. 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e Considerações de Design

**Preços**: Para obter informações sobre preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Limitações**: Esta funcionalidade está disponível em todas as regiões públicas do Azure.

**Número máximo de pontos finais privados por espaço de identificação do Service Bus:** 120.

Para mais informações, consulte [o serviço Azure Private Link: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [o Azure Private Link](../private-link/private-link-service-overview.md)
- Saiba mais sobre [o Azure Service Bus](service-bus-messaging-overview.md)
