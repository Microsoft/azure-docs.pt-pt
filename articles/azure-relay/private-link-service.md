---
title: Integre o Relé Azure com o Azure Private Link Service
description: Saiba como integrar o Azure Relay com o Azure Private Link Service
ms.date: 09/24/2020
ms.topic: article
ms.openlocfilehash: 13644082160704ba9918e6bd6257fa314bb463a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98134386"
---
# <a name="integrate-azure-relay-with-azure-private-link"></a>Integre o Relé Azure com ligação privada Azure 
O Azure **Private Link Service** permite-lhe aceder aos serviços Azure (por exemplo, Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage e Azure Cosmos DB) e a Azure acolheu serviços de cliente/parceiro sobre um ponto final privado na sua rede virtual. Para mais informações, consulte [o que é a Azure Private Link?](../private-link/private-link-overview.md)

Um **ponto final privado** é uma interface de rede que permite que as suas cargas de trabalho em execução numa rede virtual se conectem de forma privada e segura a um serviço que tenha um recurso de **ligação privada** (por exemplo, um espaço de nome Relay). O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ExpressRoute, ligações VPN ou endereços IP públicos. O tráfego entre a sua rede virtual e o serviço atravessa a rede de espinha dorsal da Microsoft, eliminando a exposição da Internet pública. Pode fornecer um nível de granularidade no controlo de acesso, permitindo ligações a espaços específicos de nomes Azure Relay. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Adicione um ponto final privado usando o portal Azure

### <a name="prerequisites"></a>Pré-requisitos
Para integrar um espaço de nome Azure Relay com a Azure Private Link, necessitará das seguintes entidades ou permissões:

- Um espaço de nome azure relay.
- Uma rede virtual Azure.
- Uma sub-rede na rede virtual.
- Permissões de proprietário ou contribuinte na rede virtual.

O seu ponto final privado e rede virtual devem estar na mesma região. Quando selecionar uma região para o ponto final privado utilizando o portal, irá filtrar automaticamente apenas redes virtuais que se encontrem nessa região. Seu espaço pode estar em uma região diferente.

O seu ponto final privado utiliza um endereço IP privado na sua rede virtual.

### <a name="steps"></a>Passos
Para obter instruções passo a passo sobre a criação de um novo espaço de nome Azure Relay e entidades nele, consulte [Criar um espaço de nomes Azure Relay utilizando o portal Azure](relay-create-namespace-portal.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, escreva em **Relés.**
3. Selecione o **espaço de nomes** da lista à qual pretende adicionar um ponto final privado.
4. Selecione o **separador 'Rede'** em **Definições**.
5. Selecione o **separador de ligações de ponto final privado** no topo da página
6. Selecione o botão **+ Ponto final privado** na parte superior da página.

    ![Adicione botão de ponto final privado](./media/private-link-service/add-private-endpoint-button.png)
7. Na página **Basics,** siga estes passos: 
    1. Selecione a **subscrição Azure** na qual pretende criar o ponto final privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto final privado.
    3. Insira um **nome** para o ponto final privado. 
    5. Selecione uma **região** para o ponto final privado. O seu ponto final privado deve estar na mesma região que a sua rede virtual, mas pode estar numa região diferente do espaço de nomes Azure Relay a que está a ligar. 
    6. Selecione **seguinte: O** botão >de recursos na parte inferior da página.

        ![Criar Ponto Final Privado - Página básica](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página **de Recursos,** siga estes passos:
    1. Para o método de ligação, se selecionar **Ligar a um recurso Azure no meu diretório,** tem acesso ao espaço de nomes ou ao proprietário e esse espaço de nome está no mesmo diretório que o ponto final privado, siga estes passos: 
        1. Selecione a **subscrição Azure** na qual existe o seu **espaço de nome Azure Relay.** 
        2. Para **o tipo de recurso**, selecione **Microsoft.Relay/namespaces** para o **tipo de recurso**.
        3. Para **obter recursos**, selecione um espaço de nome relay da lista de drop-down. 
        4. Confirme que o **subresource do Target** está definido para **o espaço de nomes**.
        5. Selecione **Seguinte: Configuração >** botão na parte inferior da página. 
        
            ![Criar Ponto Final Privado - Página de recursos](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se selecionar **Ligar a um recurso Azure por ID ou pseudónimo** de recurso porque o espaço de nome não está sob o mesmo diretório que o do ponto final privado, siga estes passos:
        1. Introduza o ID ou **o pseudónimo do** **recurso.** Pode ser o ID de recursos ou pseudónimo que alguém partilhou consigo. A maneira mais fácil de obter o ID de recurso é navegar para o espaço de nomeação Azure Relay no portal Azure e copiar a porção de URI a partir de `/subscriptions/` . Aqui está um exemplo: `/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. Para **sub-recurso target,** insira **o espaço de nomes**. É o tipo de sub-recurso a que o seu ponto final privado pode aceder.
        3. (opcional) Introduza uma **mensagem de pedido**. O proprietário do recurso vê esta mensagem enquanto gere a ligação privada de ponto final.
        4. Em seguida, selecione **Seguinte: Configuração >** botão na parte inferior da página.

            ![Criar Ponto Final Privado - Conecte-se usando iD de recursos](./media/private-link-service/connect-resource-id.png)
9. Na página **Configuração,** seleciona a sub-rede numa rede virtual para onde pretende implantar o ponto final privado. 
    1. Selecione uma **rede virtual.** Apenas as redes virtuais na subscrição e localização atualmente selecionadas estão listadas na lista de drop-down. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Ative **integrar-se com a zona privada de DNS** se quiser integrar o seu ponto final privado com uma zona privada de DNS. 
    
        Para se ligar em privado com o seu ponto final privado, precisa de um registo DNS. Recomendamos que integre o seu ponto de terminação privado com uma **zona privada de DNS**. Também pode utilizar os seus próprios servidores DNS ou criar registos DNS utilizando os ficheiros anfitriões nas suas máquinas virtuais. Para obter mais informações, consulte [a Configuração DNS do Ponto Final Privado Azure](../private-link/private-endpoint-dns.md). Neste exemplo, é selecionada a opção **Integra com zona de DNS privada** e será criada uma zona de DNS privada para si. 
    3. Selecione **Seguinte: Tags >** botão na parte inferior da página. 

        ![Criar Ponto Final Privado - Página de configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **Tags,** crie quaisquer tags (nomes e valores) que pretenda associar ao ponto final privado e à zona privada de DNS (se tivesse ativado a opção). Em seguida, **selecione Rever + criar** botão na parte inferior da página. 
11. No **Review + criar,** rever todas as definições e selecionar **Criar** para criar o ponto final privado.
    
    ![Criar ponto final privado - Rever e criar página](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Na página **private endpoint,** pode ver o estado da ligação de ponto final privado. Se você é o proprietário do espaço de nomes Relay ou tem o acesso de gestão sobre ele e tinha selecionado **Ligar a um recurso Azure na minha** opção de diretório para o método De **ligação,** a ligação de ponto final deve ser **auto-aprovada**. Se estiver no estado **pendente,** consulte os [pontos finais privados da Manage utilizando a secção do portal Azure.](#manage-private-endpoints-using-azure-portal)

    ![Página de ponto final privado](./media/private-link-service/private-endpoint-page.png)
13. Volte para a página de **Networking** do **espaço de nomes** e mude para o **separador de ligações de ponto final privado.** Devia ver o ponto final privado que criou. 

    ![Ponto final privado criado](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Adicione um ponto final privado usando PowerShell
O exemplo a seguir mostra-lhe como usar a Azure PowerShell para criar uma ligação de ponto final privado a um espaço de nomes Azure Relay.

O seu ponto final privado e rede virtual devem estar na mesma região. O seu espaço de nome Azure Relay pode estar numa região diferente. E, o seu ponto final privado utiliza um endereço IP privado na sua rede virtual.

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

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

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

Quando criar um ponto final privado, a ligação tem de ser aprovada. Se o recurso (Relay namespace) para o qual está a criar um ponto final privado estiver no seu diretório, pode aprovar o pedido de ligação desde que tenha privilégios de gestão sobre o espaço de nomes Relay. Se estiver a ligar-se a um espaço de nome Relay para o qual não tem acesso à gestão, tem de esperar que o proprietário desse recurso aprove o seu pedido de ligação.

Existem quatro estados de provisionamento:

| Ação do serviço | Estado de ponto final privado do consumidor de serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A ligação é criada manualmente e está pendente de aprovação do proprietário do espaço de identificação Azure Relay. |
| Aprovar | Aprovado | A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada. |
| Rejeitar | Rejeitado | A ligação foi rejeitada pelo proprietário do espaço de nome Azure Relay. |
| Remover | Desligado | A ligação foi removida pelo proprietário do espaço de identificação Azure Relay, o ponto final privado torna-se informativo e deve ser eliminado para limpeza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprovar, rejeitar ou remover uma ligação de ponto final privado

1. Inicie sessão no portal do Azure.
1. Na barra de pesquisa, escreva em **Relay.**
1. Selecione o **espaço de nomes** que pretende gerir.
1. Selecione o separador **Rede**.
5. Vá à secção abaixo com base na operação que pretende: aprovar, rejeitar ou remover. 

### <a name="approve-a-private-endpoint-connection"></a>Aprovar uma ligação privada de ponto final

1. Se houver alguma ligação pendente, verá uma ligação listada com **pendente** no estado de provisionamento. 
2. Selecione o **ponto final privado** que deseja aprovar
3. Selecione o botão **Aprovar.**

    ![Aprovar ponto final privado](./media/private-link-service/private-endpoint-approve.png)
4. Na página **de ligação Aprovar,** insira um **comentário** opcional e selecione **Sim**. Se **escolheres o Nº,** nada acontece. 

    ![Aprovar página de conexão](./media/private-link-service/approve-connection-page.png)
5. Deverá ver o estado da ligação na lista alterado para **Aprovado**.

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma ligação privada de ponto final

1. Se houver alguma ligação de ponto final privado que pretenda rejeitar, seja um pedido pendente ou uma ligação existente que tenha sido aprovada anteriormente, selecione a ligação de ponto final e clique no botão **Rejeitar.**

    ![Botão de rejeitar](./media/private-link-service/private-endpoint-reject.png)
2. Na página **de ligação Rejeitar,** insira um comentário opcional e selecione **Sim**. Se **escolheres o Nº,** nada acontece. 

    ![Rejeitar página de ligação](./media/private-link-service/reject-connection-page.png)
3. Deve ver o estado da ligação na lista alterado **Rejeitado**.


### <a name="remove-a-private-endpoint-connection"></a>Remover uma ligação de ponto final privado

1. Para remover uma ligação de ponto final privado, selecione-a na lista e selecione **Remover** na barra de ferramentas. 

    ![Remover botão](./media/private-link-service/remove-endpoint.png)
2. Na página **de ligação Eliminar,** selecione **Sim** para confirmar a eliminação do ponto final privado. Se **escolheres o Nº,** nada acontece. 

    ![Eliminar página de ligação](./media/private-link-service/delete-connection-page.png)
3. Deve ver o estado alterado para **Desligado**. Então, verá o ponto final desaparecer da lista. 

## <a name="validate-that-the-private-link-connection-works"></a>Validar que a ligação de ligação privada funciona
Deve validar que os recursos dentro da rede virtual do ponto final privado estão a ligar-se ao seu espaço de nomeS Azure Relay sobre o seu endereço IP privado.

Para este teste, crie uma máquina virtual seguindo os passos na [máquina virtual Create a Windows no portal Azure](../virtual-machines/windows/quick-create-portal.md)

No **separador Networking:** 

1. Especificar **rede virtual** e **sub-rede.** Tem de selecionar a Rede Virtual na qual implementou o ponto final privado.
2. Especifique um recurso **IP público.**
3. Para **o grupo de segurança da rede NIC**, selecione **Nenhum**.
4. Para **equilibrar a carga**, selecione **Nº**.

Ligue-se ao VM e abra a linha de comando e execute o seguinte comando:

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

Devia ver um resultado que se pareça com o seguinte. 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e Considerações de Design

### <a name="design-considerations"></a>Considerações de conceção
- Para obter informações sobre preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

### <a name="limitations"></a>Limitações 
- Número máximo de pontos finais privados por espaço de nome do Azure Relay: 64.
- Número máximo de espaços de nomes Azure Relay com pontos finais privados por subscrição: 64.
- As regras do Grupo de Segurança de Rede (NSG) e User-Defined Rotas não se aplicam ao Private Endpoint. Para mais informações, consulte [o serviço Azure Private Link: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [o Azure Private Link](../private-link/private-link-service-overview.md)
- Saiba mais sobre [a Azure Relay](relay-what-is-it.md)
