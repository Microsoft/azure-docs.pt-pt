---
title: Integre os Hubs de Eventos Azure com o Azure Private Link Service
description: Saiba como integrar os Hubs de Eventos Azure com o Azure Private Link Service
ms.date: 08/22/2020
ms.topic: article
ms.openlocfilehash: 996779e103dae2d2d950f447d2ac72667fc9e754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94427756"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-via-private-endpoints"></a>Permitir o acesso aos espaços de nome do Azure Event Hubs através de pontos finais privados 
O Azure Private Link Service permite-lhe aceder aos Serviços Azure (por exemplo, Azure Event Hubs, Azure Storage e Azure Cosmos DB) e a Azure acolheu serviços de cliente/parceiro sobre um **ponto final privado** na sua rede virtual.

Um ponto final privado é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir da sua rede virtual, efetivamente trazendo o serviço para a sua rede virtual. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acessos.

Para mais informações, consulte [o que é a Azure Private Link?](../private-link/private-link-overview.md)

> [!WARNING]
> Permitir pontos finais privados pode impedir que outros serviços Azure interajam com os Centros de Eventos.  Os pedidos que estão bloqueados incluem os de outros serviços Azure, do portal Azure, de serviços de registo e métricas, e assim por diante. Como exceção, pode permitir o acesso aos recursos do Event Hubs a partir de determinados serviços fidedignos, mesmo quando os pontos finais privados estão ativados. Para obter uma lista de serviços fidedignos, consulte [serviços Fidedignos.](#trusted-microsoft-services)

>[!NOTE]
> Esta funcionalidade é suportada tanto para níveis **standard** como **dedicado.** Não é suportado no nível **básico.**

## <a name="add-a-private-endpoint-using-azure-portal"></a>Adicione um ponto final privado usando o portal Azure

### <a name="prerequisites"></a>Pré-requisitos

Para integrar um espaço de nomes do Event Hubs com a Azure Private Link, necessitará das seguintes entidades ou permissões:

- Um espaço de nomes de Event Hubs.
- Uma rede virtual Azure.
- Uma sub-rede na rede virtual. Pode utilizar a sub-rede **predefinida.** 
- Permissões de proprietário ou contribuinte tanto para o espaço de nome como para a rede virtual.

O seu ponto final privado e rede virtual devem estar na mesma região. Quando selecionar uma região para o ponto final privado utilizando o portal, irá filtrar automaticamente apenas redes virtuais que se encontrem nessa região. Seu espaço pode estar em uma região diferente.

O seu ponto final privado utiliza um endereço IP privado na sua rede virtual.

### <a name="steps"></a>Passos
Se já tem um espaço de nomes do Event Hubs, pode criar uma ligação de ligação privada seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, escreva nos **centros de eventos.**
3. Selecione o **espaço de nomes** da lista à qual pretende adicionar um ponto final privado.
4. Selecione **rede em** **Definições** no menu esquerdo.

    > [!NOTE]
    > Você vê o **separador Networking** apenas para espaços de nome **padrão** ou **dedicados.** 

    :::image type="content" source="./media/private-link-service/selected-networks-page.png" alt-text="Separador de redes - opção de redes selecionada" lightbox="./media/private-link-service/selected-networks-page.png":::    

    > [!NOTE]
    > Por predefinição, a opção **de rede Selecionada** é selecionada. Se não especificar uma regra de firewall IP ou adicionar uma rede virtual, o espaço de nome pode ser acedido através da internet pública. 
1. Selecione o **separador de ligações de ponto final privado** no topo da página. 
1. Selecione o botão **+ Ponto final privado** na parte superior da página.

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text="Página de rede - Separador de ligações de ponto final privado - Adicionar link de ponto final privado":::
7. Na página **Basics,** siga estes passos: 
    1. Selecione a **subscrição Azure** na qual pretende criar o ponto final privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto final privado.
    3. Insira um **nome** para o ponto final privado. 
    5. Selecione uma **região** para o ponto final privado. O seu ponto final privado deve estar na mesma região que a sua rede virtual, mas pode estar numa região diferente do recurso de ligação privada a que está a ligar. 
    6. Selecione **seguinte: O** botão >de recursos na parte inferior da página.

        ![Criar Ponto Final Privado - Página básica](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página **de Recursos,** siga estes passos:
    1. Para o método de ligação, se selecionar **Ligar a um recurso Azure no meu diretório,** siga estes passos: 
        1. Selecione a **subscrição Azure** na qual existe o seu **espaço de nomes De Event Hubs.** 
        2. Para **o tipo de recurso**, selecione **Microsoft.EventHub/namespaces** para o tipo de **recurso**.
        3. Para **obter recursos**, selecione um espaço de nomes de Centros de Eventos da lista de drop-down. 
        4. Confirme que o **subresource do Target** está definido para **o espaço de nomes**.
        5. Selecione **Seguinte: Configuração >** botão na parte inferior da página. 
        
            ![Criar Ponto Final Privado - Página de recursos](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se selecionar **Ligar a um recurso Azure por ID ou pseudónimo** de recurso, siga estes passos:
        1. Introduza o ID ou **o pseudónimo do** **recurso.** Pode ser o ID de recursos ou pseudónimo que alguém partilhou consigo. A maneira mais fácil de obter o ID de recurso é navegar para o espaço de nomes do Event Hubs no portal Azure e copiar a porção de URI a partir de `/subscriptions/` . Veja a seguinte imagem para um exemplo. 
        2. Para **sub-recurso target,** insira **o espaço de nomes**. É o tipo de sub-recurso a que o seu ponto final privado pode aceder.
        3. (opcional) Introduza uma **mensagem de pedido**. O proprietário do recurso vê esta mensagem enquanto gere a ligação privada de ponto final.
        4. Em seguida, selecione **Seguinte: Configuração >** botão na parte inferior da página.

            ![Criar Ponto Final Privado - Conecte-se usando iD de recursos](./media/private-link-service/connect-resource-id.png)
9. Na página **Configuração,** seleciona a sub-rede numa rede virtual para onde pretende implantar o ponto final privado. 
    1. Selecione uma **rede virtual.** Apenas as redes virtuais na subscrição e localização atualmente selecionadas estão listadas na lista de drop-down. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **Seguinte: Tags >** botão na parte inferior da página. 

        ![Criar Ponto Final Privado - Página de configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **Tags,** crie quaisquer tags (nomes e valores) que pretenda associar ao recurso de ponto final privado. Em seguida, **selecione Rever + criar** botão na parte inferior da página. 
11. No **Review + criar,** rever todas as definições e selecionar **Criar** para criar o ponto final privado.
    
    ![Criar ponto final privado - Rever e criar página](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Confirme que vê a ligação de ponto final privado que criou aparece na lista de pontos finais. Neste exemplo, o ponto final privado é auto-aprovado porque ligou a um recurso Azure no seu diretório e tem permissões suficientes. 

    ![Ponto final privado criado](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

Para permitir que serviços fidedignos acedam ao seu espaço de nome, mude para o separador **Firewalls e redes virtuais** na página **de Networking** e selecione **Sim** para Permitir que **os serviços fidedignos da Microsoft contornem esta firewall?** 

## <a name="add-a-private-endpoint-using-powershell"></a>Adicione um ponto final privado usando PowerShell
O exemplo a seguir mostra como utilizar o Azure PowerShell para criar uma ligação de ponto final privado. Não cria um aglomerado dedicado para ti. Siga os passos [neste artigo](event-hubs-dedicated-cluster-create-portal.md) para criar um cluster dedicado de Centros de Eventos. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

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

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>Configure a zona privada do DNS
Crie uma zona privada de DNS para o domínio de Event Hubs e crie uma ligação de associação com a rede virtual:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Gerir pontos finais privados usando o portal Azure

Quando criar um ponto final privado, a ligação tem de ser aprovada. Se o recurso para o qual está a criar um ponto final privado estiver no seu diretório, pode aprovar o pedido de ligação desde que tenha permissões suficientes. Se estiver a ligar-se a um recurso Azure noutro diretório, tem de esperar que o proprietário desse recurso aprove o seu pedido de ligação.

Existem quatro estados de provisionamento:

| Ação do serviço | Estado de ponto final privado do consumidor de serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A ligação é criada manualmente e está pendente de aprovação do proprietário do recurso Private Link. |
| Aprovar | Aprovado | A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada. |
| Rejeitar | Rejeitado | A ligação foi rejeitada pelo proprietário de recursos de ligação privada. |
| Remover | Desligado | A ligação foi removida pelo proprietário do recurso de ligação privada, o ponto final privado torna-se informativo e deve ser eliminado para limpeza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprovar, rejeitar ou remover uma ligação de ponto final privado

1. Inicie sessão no portal do Azure.
2. Na barra de pesquisa, escreva nos **centros de eventos.**
3. Selecione o **espaço de nomes** que pretende gerir.
4. Selecione o separador **Rede**.
5. Vá à secção abaixo com base na operação que pretende: aprovar, rejeitar ou remover.

### <a name="approve-a-private-endpoint-connection"></a>Aprovar uma ligação privada de ponto final
1. Se houver alguma ligação pendente, verá uma ligação listada com **pendente** no estado de provisionamento. 
2. Selecione o **ponto final privado** que deseja aprovar
3. Selecione o botão **Aprovar.**

    ![Aprovar ponto final privado](./media/private-link-service/approve-private-endpoint.png)
4. Na página **de ligação Aprovar,** adicione um comentário (opcional) e selecione **Sim**. Se **escolheres o Nº,** nada acontece. 
5. Deverá ver o estado da ligação de ponto final privado na lista alterada para **Aprovado**. 

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma ligação privada de ponto final

1. Se houver alguma ligação de ponto final privado que pretenda rejeitar, seja um pedido pendente ou uma ligação existente, selecione a ligação e clique no botão **Rejeitar.**

    ![Rejeitar ponto final privado](./media/private-link-service/private-endpoint-reject-button.png)
2. Na página **de ligação Rejeitar,** insira um comentário (opcional) e selecione **Sim**. Se **escolheres o Nº,** nada acontece. 
3. Deverá ver o estado da ligação de ponto final privado na lista alterada para **Rejeitado**. 

### <a name="remove-a-private-endpoint-connection"></a>Remover uma ligação de ponto final privado

1. Para remover uma ligação de ponto final privado, selecione-a na lista e selecione **Remover** na barra de ferramentas.
2. Na página **de ligação Eliminar,** selecione **Sim** para confirmar a eliminação do ponto final privado. Se **escolheres o Nº,** nada acontece.
3. Deve ver o estado alterado para **Desligado**. Então, verá o ponto final desaparecer da lista.

## <a name="validate-that-the-private-link-connection-works"></a>Validar que a ligação de ligação privada funciona

Você deve validar que os recursos dentro da rede virtual do ponto final privado estão conectando-se ao seu espaço de nomes Event Hubs em um endereço IP privado, e que eles têm a integração de zona de DNS privada correta.

Primeiro, crie uma máquina virtual seguindo os passos na [Criar uma máquina virtual Windows no portal Azure](../virtual-machines/windows/quick-create-portal.md)

No **separador Networking:** 

1. Especificar **rede virtual** e **sub-rede.** Tem de selecionar a Rede Virtual na qual implementou o ponto final privado.
2. Especifique um recurso **IP público.**
3. Para **o grupo de segurança da rede NIC**, selecione **Nenhum**.
4. Para **equilibrar a carga**, selecione **Nº**.

Ligue ao VM, abra a linha de comando e execute o seguinte comando:

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

Devia ver um resultado que se pareça com o seguinte. 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e considerações de design

**Preços**: Para obter informações sobre preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Limitações**: Esta funcionalidade está disponível em todas as regiões públicas do Azure.

**Número máximo de pontos finais privados por espaço de nomes de Event Hubs:** 120.

Para mais informações, consulte [o serviço Azure Private Link: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Private Link](../private-link/private-link-service-overview.md)
- Saiba mais sobre [os Azure Event Hubs](event-hubs-about.md)
