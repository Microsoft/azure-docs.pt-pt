---
title: Integrar o Relé Azure com o Serviço de Ligação Privada Azure
description: Saiba como integrar o Azure Relay com o Serviço de Ligação Privada Azure
services: service-bus-relay
author: spelluru
ms.author: spelluru
ms.date: 05/07/2020
ms.service: service-bus-relay
ms.topic: article
ms.openlocfilehash: ad57ea4ddeab2fb2af0da68d199445d9737a67cd
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210623"
---
# <a name="integrate-azure-relay-with-azure-private-link-preview"></a>Integrar o Relé Azure com link privado Azure (Pré-visualização)
O **Azure Private Link Service** permite-lhe aceder aos serviços Azure (por exemplo, Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage e Azure Cosmos DB) e o Azure acolheu serviços de clientes/parceiros sobre um ponto final privado na sua rede virtual. Para mais informações, consulte [O que é azure private link (Pré-visualização)?](../private-link/private-link-overview.md)

Um **ponto final privado** é uma interface de rede que permite que as suas cargas de trabalho em funcionamento numa rede virtual se conectem de forma privada e segura a um serviço que tem um recurso de **ligação privada** (por exemplo, um espaço de nome retransmissor). O ponto final privado utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ExpressRoute, ligações VPN ou endereços IP públicos. O tráfego entre a sua rede virtual e o serviço atravessa a rede de espinha dorsal da Microsoft, eliminando a exposição da Internet pública. Pode fornecer um nível de granularidade no controlo de acesso, permitindo ligações a espaços específicos de identificação do Relé Azure. 


> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em **pré-visualização**. 
>
> Atualmente apoiamos ligações de ligação privada em clientes remetentes. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Adicione um ponto final privado usando o portal Azure

### <a name="prerequisites"></a>Pré-requisitos
Para integrar um espaço de nome sinuoso Azure relay com Ligação Privada Azure (Pré-visualização), necessitará das seguintes entidades ou permissões:

- Um espaço de nome azure Relay.
- Uma rede virtual Azure.
- Uma sub-rede na rede virtual.
- Permissões do proprietário ou contribuinte na rede virtual.

O seu ponto final privado e a sua rede virtual devem estar na mesma região. Quando se leciona uma região para o ponto final privado utilizando o portal, filtrará automaticamente apenas redes virtuais que se encontram naquela região. O seu espaço de nome pode estar numa região diferente.

O seu ponto final privado utiliza um endereço IP privado na sua rede virtual.

### <a name="steps"></a>Passos
Para instruções passo a passo sobre a criação de um novo espaço de nome sinuoso Azure Relay e entidades nele, consulte Criar um espaço de [nome sinuoso Azure relé utilizando o portal Azure](relay-create-namespace-portal.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Na barra de pesquisa, digite em **Relés**.
3. Selecione o espaço de **nome** da lista à qual pretende adicionar um ponto final privado.
4. Selecione o separador **de rede** em **Definições**.
5. Selecione o separador de **ligações de ponto final privado (pré-visualização)** na parte superior da página
6. Selecione o botão **+ Ponto Final Privado** na parte superior da página.

    ![Adicione botão de ponto final privado](./media/private-link-service/add-private-endpoint-button.png)
7. Na página **Basics,** siga estes passos: 
    1. Selecione a **subscrição Azure** na qual pretende criar o ponto final privado. 
    2. Selecione o **grupo de recursos** para o recurso de ponto final privado.
    3. Insira um **nome** para o ponto final privado. 
    5. Selecione uma **região** para o ponto final privado. O seu ponto final privado deve estar na mesma região que a sua rede virtual, mas pode estar numa região diferente do espaço de nome Supor Azure relé que está a ligar. 
    6. Selecione **Seguinte: O** botão de recursos >na parte inferior da página.

        ![Criar endpoint privado - página básica](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na página **Recurso,** siga estes passos:
    1. Para o método de ligação, se selecionar **O Connect a um recurso Azure no meu diretório,** tem acesso do proprietário ou colaborador ao espaço de nome e esse espaço de nome está no mesmo diretório que o ponto final privado, siga estes passos: 
        1. Selecione a **subscrição Azure** na qual existe o seu espaço de **nome Sinuoso Azure.** 
        2. Para **o tipo de recurso,** selecione **Microsoft.Relay/namespaces** para o **tipo de Recurso**.
        3. Para **Recurso,** selecione um espaço de nome retransmissor da lista de lançamentos. 
        4. Confirme se o **subrecurso Target** está definido para **o espaço de nome**.
        5. Selecione **Seguinte: Configuração >** botão na parte inferior da página. 
        
            ![Criar endpoint privado - página de recursos](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Se selecionar **Ligar a um recurso Azure por id de recurso ou pseudónimo** porque o espaço de nome não está sob o mesmo diretório que o do ponto final privado, siga estes passos:
        1. Introduza o ID ou **pseudónimo**do **recurso.** Pode ser a identificação de recursos ou pseudónimo que alguém partilhou consigo. A maneira mais fácil de obter o ID de recurso é navegar para o espaço de nome de Retransmissão Azure no portal Azure e copiar a parte de URI a partir de `/subscriptions/` . Aqui está um exemplo:`/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. Para **sub-recurso Target,** introduza **o espaço de nome**. É o tipo de sub-recurso a que o seu ponto final privado pode aceder.
        3. (opcional) Introduza uma **mensagem de pedido.** O proprietário do recurso vê esta mensagem enquanto gere a ligação de ponto final privado.
        4. Em seguida, selecione **Seguinte: Configuração >** botão na parte inferior da página.

            ![Criar endpoint privado - Conecte usando id de recurso](./media/private-link-service/connect-resource-id.png)
9. Na página **de Configuração,** selecione a subnet numa rede virtual para onde pretende implementar o ponto final privado. 
    1. Selecione uma **rede virtual**. Apenas redes virtuais na subscrição e localização atualmente selecionadas estão listadas na lista de drop-down. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Ative **integrar com a zona Privada DNS** se quiser integrar o seu ponto final privado com uma zona privada de DNS. 
    
        Para se ligar em privado com o seu ponto final privado, precisa de um registo DNS. Recomendamos que integre o seu ponto final privado com uma **zona privada de DNS.** Também pode utilizar os seus próprios servidores DNS ou criar registos DNS utilizando os ficheiros de hospedas nas suas máquinas virtuais. Para mais informações, consulte a [Configuração DNS do Ponto Final Privado do Azure](../private-link/private-endpoint-dns.md). Neste exemplo, é selecionada a Integração com a opção **de zona DNS privada** e será criada uma zona Privada de DNS para si. 
    3. Selecione **Seguinte: Etiquetas >** botão na parte inferior da página. 

        ![Criar ponto final privado - página de configuração](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na página **Tags,** crie quaisquer tags (nomes e valores) que queira associar ao ponto final privado e à zona privada de DNS (se tivesse ativado a opção). Em seguida, selecione **Review + crie** o botão na parte inferior da página. 
11. No **Review + criar,** reveja todas as definições e selecione **Criar** para criar o ponto final privado.
    
    ![Criar endpoint privado - Rever e Criar página](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Na página **de ponto final privado,** pode ver o estado da ligação de ponto final privado. Se é o proprietário do espaço de nome retransmissor ou tem o acesso de gestão sobre ele e selecionou **o Connect a um recurso Azure na minha** opção de diretório para o método de **Ligação,** a ligação de ponto final deve ser **aprovada automaticamente**. Se estiver no estado **pendente,** consulte os [pontos finais privados manage usando](#manage-private-endpoints-using-azure-portal) a secção do portal Azure.

    ![Página de ponto final privado](./media/private-link-service/private-endpoint-page.png)
13. Navegue de volta para a página **de Networking** do espaço de **nomee**mude para o separador de **ligações de ponto final privado (pré-visualização).** Deviaver o ponto final privado que criou. 

    ![Ponto final privado criado](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Adicione um ponto final privado usando powerShell
O exemplo que se segue mostra como usar o Azure PowerShell para criar uma ligação de ponto final privado a um espaço de nome sinuoso Azure Relay.

O seu ponto final privado e a sua rede virtual devem estar na mesma região. O seu espaço de nome Azure Relay pode estar numa região diferente. E o seu ponto final privado usa um endereço IP privado na sua rede virtual.

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

Quando se cria um ponto final privado, a ligação tem de ser aprovada. Se o recurso (Relay namespace) para o qual está a criar um ponto final privado estiver no seu diretório, pode aprovar o pedido de ligação desde que tenha privilégios de gestão sobre o espaço de nome retransmissor. Se estiver a ligar-se a um espaço de nome retransmissor para o qual não tem acesso de gestão, tem de esperar que o proprietário desse recurso aprove o seu pedido de ligação.

Existem quatro estados de provisionamento:

| Ação de serviço | Estado de ponto final privado do consumidor de serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A ligação é criada manualmente e está pendente de aprovação do proprietário do espaço de nome Supor Azure Relay. |
| Aprovar | Aprovado | A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada. |
| Rejeitar | Rejected | A ligação foi rejeitada pelo proprietário do espaço de nome Sinuoso Azure Relay. |
| Remover | Desligado | A ligação foi removida pelo proprietário do espaço de nome Supor Azure Relay, o ponto final privado torna-se informativo e deve ser eliminado para limpeza. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Aprovar, rejeitar ou remover uma ligação de ponto final privado

1. Inicie sessão no Portal do Azure.
1. Na barra de pesquisa, digite em **Relé**.
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

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma ligação de ponto final privado

1. Se houver alguma ligação de ponto final privado que queira rejeitar, seja um pedido pendente ou uma ligação existente que tenha sido aprovada anteriormente, selecione a ligação de ponto final e clique no botão **Rejeitar.**

    ![Rejeitar botão](./media/private-link-service/private-endpoint-reject.png)
2. Na página de **ligação Rejeitar,** introduza um comentário opcional e selecione **Sim**. Se escolher **"Não",** nada acontece. 

    ![Rejeitar página de ligação](./media/private-link-service/reject-connection-page.png)
3. Deve ver o estado da ligação na lista alterado **Rejeitado**.


### <a name="remove-a-private-endpoint-connection"></a>Remover uma ligação de ponto final privado

1. Para remover uma ligação de ponto final privado, selecione-a na lista e **selecione Remover** na barra de ferramentas. 

    ![Remover o botão](./media/private-link-service/remove-endpoint.png)
2. Na página de **ligação Delete,** selecione **Sim** para confirmar a eliminação do ponto final privado. Se escolher **"Não",** nada acontece. 

    ![Excluir página de ligação](./media/private-link-service/delete-connection-page.png)
3. Deve ver o estado alterado para **Desligado**. Então, verá sairá o ponto final da lista. 

## <a name="validate-that-the-private-link-connection-works"></a>Validar que a ligação de ligação privada funciona
Deverá validar que os recursos dentro da mesma sub-rede do ponto final privado estão a ligar-se ao seu espaço de nome Supor-se Azure Relay sobre o seu endereço IP privado.

Para este teste, crie uma máquina virtual seguindo os passos na [Create a Windows virtual machine no portal Azure](../virtual-machines/windows/quick-create-portal.md)

No separador **Networking:** 

1. Especificar **rede virtual** e **Subnet**. Deve selecionar a Rede Virtual na qual implantou o ponto final privado.
2. Especifique um recurso **IP público.**
3. Para o grupo de **segurança da rede NIC,** selecione **Nenhum**.
4. Para **equilibrar a carga,** selecione **Não**.

Ligue-se ao VM e abra a linha de comando e execute o seguinte comando:

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

Devia ver um resultado que se parece com o seguinte. 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e Considerações de Design

### <a name="design-considerations"></a>Considerações de conceção
- Private Endpoint for Azure Relay está em **pré-visualização pública.** 
- Para obter informações sobre preços, consulte o preço do [Link Privado do Azure (pré-visualização).](https://azure.microsoft.com/pricing/details/private-link/)

### <a name="limitations"></a>Limitações 
- Número máximo de pontos finais privados por espaço de nome de Retransmissão Azure: 64.
- Número máximo de espaços de nome sinuoso Azure relay com pontos finais privados por subscrição: 64.
- As regras do Grupo de Segurança da Rede (NSG) e as rotas definidas pelo utilizador não se aplicam ao Ponto Final Privado. Para mais informações, consulte [o serviço Azure Private Link: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [o Link Privado Azure (Pré-visualização)](../private-link/private-link-service-overview.md)
- Saiba mais sobre [o Relé Azure](relay-what-is-it.md)
