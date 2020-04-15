---
title: Integrar hubs de eventos Azure com serviço de link privado Azure
description: Saiba como integrar hubs de eventos azure com o Serviço de Link Privado Azure
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: bcc360bbe4dd58200993b9377317ccb608b3529d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383654"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Integrar hubs de eventos Azure com Link Privado Azure (Pré-visualização)
O Azure Private Link Service permite-lhe aceder aos Serviços Azure (por exemplo, Azure Event Hubs, Azure Storage e Azure Cosmos DB) e o Azure acolheu serviços de clientes/parceiros sobre um **ponto final privado** na sua rede virtual.

Um ponto final privado é uma interface de rede que o conecta de forma privada e segura a um serviço alimentado por Azure Private Link. O ponto final privado utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a uma instância de um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acesso.

Para mais informações, consulte [o que é azure private link?](../private-link/private-link-overview.md)

> [!NOTE]
> Esta funcionalidade é suportada apenas com o nível **dedicado.** Para mais informações sobre o nível dedicado, consulte [a visão geral dos Centros de Eventos Dedicados](event-hubs-dedicated-overview.md). 
>
> Esta funcionalidade encontra-se atualmente em **pré-visualização**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Adicione um ponto final privado usando o portal Azure

### <a name="prerequisites"></a>Pré-requisitos

Para integrar um espaço de nome sinuoso de Event Hubs com Ligação Privada Azure, você precisará das seguintes entidades ou permissões:

- Um espaço de nome de Event Hubs.
- Uma rede virtual Azure.
- Uma sub-rede na rede virtual.
- Permissões do proprietário ou do contribuinte tanto para o espaço de nome como para a rede virtual.

O seu ponto final privado e a sua rede virtual devem estar na mesma região. Quando se leciona uma região para o ponto final privado utilizando o portal, filtrará automaticamente apenas redes virtuais que se encontram naquela região. O seu espaço de nome pode estar numa região diferente.

O seu ponto final privado utiliza um endereço IP privado na sua rede virtual.

### <a name="steps"></a>Passos
Se já tem um espaço de nome sinuoso do Event Hubs, pode criar uma ligação de ligação privada seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, digite em centros de **eventos**.
3. Selecione o espaço de **nome** da lista à qual pretende adicionar um ponto final privado.
4. Selecione o separador **de rede** em **Definições**.
5. Selecione o separador de **ligações de ponto final privado (pré-visualização)** na parte superior da página. Se não estiver a utilizar um nível dedicado de Centros de Eventos, vê uma mensagem: As ligações privadas de ponto final nos Centros de **Eventos são suportadas apenas por espaços**de nome criados sob um cluster dedicado .
6. Selecione o botão **+ Ponto Final Privado** na parte superior da página.

    ![Imagem](./media/private-link-service/private-link-service-3.png)
7. Na página **Basics,** siga estes passos: 
    1. Selecione a **subscrição Azure** na qual pretende criar o ponto final privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto final privado.
    3. Insira um **nome** para o ponto final privado. 
    5. Selecione uma **região** para o ponto final privado. O seu ponto final privado deve estar na mesma região que a sua rede virtual, mas pode estar numa região diferente do recurso de ligação privada a que está a ligar. 
    6. Selecione **Seguinte: O** botão de recursos >na parte inferior da página.

        ![Criar endpoint privado - página básica](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página **Recurso,** siga estes passos:
    1. Para obter o método de ligação, se selecionar **Ligar a um recurso Azure no meu diretório,** siga estes passos: 
        1. Selecione a **subscrição Azure** na qual existe o espaço de nome sinuoso do **Event Hubs.** 
        2. Para **o tipo de recurso,** selecione **Microsoft.EventHub/espaços** de nome para o **tipo de Recurso**.
        3. Para **Recurso**, selecione um espaço de nome de Event Hubs na lista de drop-down. 
        4. Confirme se o **subrecurso Target** está definido para **o espaço de nome**.
        5. Selecione **Seguinte: Configuração >** botão na parte inferior da página. 
        
            ![Criar endpoint privado - página de recursos](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se selecionar **Ligar a um recurso Azure por id ou pseudónimo**de recurso, siga estes passos:
        1. Introduza o ID ou **pseudónimo**do **recurso.** Pode ser a identificação de recursos ou pseudónimo que alguns partilharam consigo.
        2. Para **sub-recurso Target,** introduza **o espaço de nome**. É o tipo de sub-recurso a que o seu ponto final privado pode aceder.
        3. (opcional) Introduza uma **mensagem de pedido.** O proprietário do recurso vê esta mensagem enquanto gere a ligação de ponto final privado.
        4. Em seguida, selecione **Seguinte: Configuração >** botão na parte inferior da página.

            ![Criar endpoint privado - Conecte usando id de recurso](./media/private-link-service/connect-resource-id.png)
9. Na página **de Configuração,** selecione a subnet numa rede virtual para onde pretende implementar o ponto final privado. 
    1. Selecione uma **rede virtual**. Apenas redes virtuais na subscrição e localização atualmente selecionadas estão listadas na lista de drop-down. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **Seguinte: Etiquetas >** botão na parte inferior da página. 

        ![Criar ponto final privado - página de configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **Tags,** crie quaisquer tags (nomes e valores) que queira associar ao recurso de ponto final privado. Em seguida, selecione **Review + crie** o botão na parte inferior da página. 
11. No **Review + criar,** reveja todas as definições e selecione **Criar** para criar o ponto final privado.
    
    ![Criar endpoint privado - Rever e Criar página](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Confirme que vê a ligação de ponto final privado que criou aparece na lista de pontos finais. Neste exemplo, o ponto final privado é aprovado automaticamente porque você conectado a um recurso Azure no seu diretório e você tem permissões suficientes. 

    ![Ponto final privado criado](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Adicione um ponto final privado usando powerShell
O exemplo que se segue mostra como usar o Azure PowerShell para criar uma ligação de ponto final privado. Não cria um aglomerado dedicado para ti. Siga os passos [neste artigo](event-hubs-dedicated-cluster-create-portal.md) para criar um cluster dedicado de Hubs de Eventos. 

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

### <a name="configure-the-private-dns-zone"></a>Configure a Zona Privada dNS
Criar uma zona Privada de DNS para o domínio dos Event Hubs e criar uma ligação de associação com a rede virtual:

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

Quando se cria um ponto final privado, a ligação tem de ser aprovada. Se o recurso para o qual está a criar um ponto final privado estiver no seu diretório, pode aprovar o pedido de ligação desde que tenha permissões suficientes. Se estiver a ligar-se a um recurso Azure noutro diretório, tem de esperar que o proprietário desse recurso aprove o seu pedido de ligação.

Existem quatro estados de provisionamento:

| Ação de serviço | Estado de ponto final privado do consumidor de serviço | Descrição |
|--|--|--|
| Nenhuma | Pendente | A ligação é criada manualmente e está pendente de aprovação do proprietário do recurso Private Link. |
| Aprovar | Aprovado | A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada. |
| Rejeitar | Rejected | A ligação foi rejeitada pelo proprietário de recursos de ligação privada. |
| Remover | Desligado | A ligação foi removida pelo proprietário do recurso de ligação privada, o ponto final privado torna-se informativo e deve ser eliminado para limpeza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprovar, rejeitar ou remover uma ligação de ponto final privado

1. Inicie sessão no Portal do Azure.
2. Na barra de pesquisa, digite em centros de **eventos**.
3. Selecione o espaço de **nome** que pretende gerir.
4. Selecione o separador **Networking.**
5. Vá à secção adequada abaixo com base na operação que pretende: aprovar, rejeitar ou remover.

### <a name="approve-a-private-endpoint-connection"></a>Aprovar uma ligação de ponto final privado
1. Se houver alguma ligação pendente, verá uma ligação listada com **pendente** no estado de provisionamento. 
2. Selecione o **ponto final privado** que deseja aprovar
3. Selecione o botão **'Aprovar'.**

    ![Imagem](./media/private-link-service/approve-private-endpoint.png)
4. Na página de **ligação Aprovar,** adicione um comentário (opcional) e selecione **Sim**. Se escolher **"Não",** nada acontece. 
5. Deve ver o estado da ligação de ponto final privado na lista alterada para **Aprovado**. 

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma ligação de ponto final privado

1. Se existirem ligações de ponto final privados que deseja rejeitar, seja um pedido pendente ou uma ligação existente, selecione a ligação e clique no botão **Rejeitar.**

    ![Imagem](./media/private-link-service/private-endpoint-reject-button.png)
2. Na página de **ligação Rejeitar,** introduza um comentário (opcional) e selecione **Sim**. Se escolher **"Não",** nada acontece. 
3. Deve ver o estado da ligação de ponto final privado na lista alterada para **Rejeitado**. 

### <a name="remove-a-private-endpoint-connection"></a>Remover uma ligação de ponto final privado

1. Para remover uma ligação de ponto final privado, selecione-a na lista e **selecione Remover** na barra de ferramentas.
2. Na página de **ligação Delete,** selecione **Sim** para confirmar a eliminação do ponto final privado. Se escolher **"Não",** nada acontece.
3. Deve ver o estado alterado para **Desligado**. Então, verá sairá o ponto final da lista.

## <a name="validate-that-the-private-link-connection-works"></a>Validar que a ligação de ligação privada funciona

Deve validar que os recursos dentro da mesma sub-rede do recurso de ponto final privado estão ligados ao seu espaço de nome sem nome do Event Hubs sobre um endereço IP privado, e que eles têm a correta integração privada da zona DNS.

Primeiro, crie uma máquina virtual seguindo os passos em [Criar uma máquina virtual Windows no portal Azure](../virtual-machines/windows/quick-create-portal.md)

No separador **Networking:**

1. Especificar **rede virtual** e **sub-rede**. Pode criar uma nova rede virtual ou selecionar uma existente. Se selecionar um existente, certifique-se de que a região corresponde.
1. Especifique um recurso **IP público.**
1. No grupo de segurança da **rede NIC,** selecione **Nenhum**.
1. No equilíbrio de **carga,** selecione **Nº**.

Abra a linha de comando e execute o seguinte comando:

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Se executar o comando de procura ção do NS para resolver o endereço IP de um espaço de nome de Event Hubs sobre um ponto final público, você verá um resultado que se parece com este:

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Se executar o comando de lookup ns para resolver o endereço IP de um espaço de nome de Event Hubs sobre um ponto final privado, você verá um resultado que se parece com este:

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e considerações de design

**Preços**: Para obter informações sobre preços, consulte os preços do [Link Privado do Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Limitações**: Private Endpoint for Azure Event Hubs está em pré-visualização pública. Esta funcionalidade está disponível em todas as regiões públicas do Azure.

**Número máximo de pontos finais privados por espaço de nome Event Hubs:** 120.

Para mais informações, consulte [o serviço Azure Private Link: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Link Privado Azure](../private-link/private-link-service-overview.md)
- Saiba mais sobre os Hubs de [Eventos Azure](event-hubs-about.md)
