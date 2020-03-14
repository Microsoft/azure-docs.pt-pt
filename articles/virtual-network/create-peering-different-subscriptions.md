---
title: Criar um vNet peering - diferentes subscrições
titlesuffix: Azure Virtual Network
description: Saiba como criar uma rede virtual de peering entre redes virtuais criadas através do Gestor de Recursos que existem em diferentes subscrições do Azure.
services: virtual-network
documentationcenter: ''
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: anavin
ms.openlocfilehash: d085279167b498b13cfb79b97703cfdff7d6dd8a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245125"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Criar um peering de rede virtual - Gestor de Recursos, diferentes subscrições

Neste tutorial, aprende-se a criar uma rede virtual entre redes virtuais criadas através do Gestor de Recursos. As redes virtuais existem em diferentes subscrições. Espreitar duas redes virtuais permite que os recursos em diferentes redes virtuais se comuniquem entre si com a mesma largura de banda e latência como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [o peering virtual da rede.](virtual-network-peering-overview.md)

Os passos para criar um peering de rede virtual são diferentes, dependendo se as redes virtuais estão nas mesmas, ou diferentes, subscrições, e em que modelo de [implementação azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas através. Saiba como criar uma rede virtual a espreitar noutros cenários, selecionando o cenário a partir da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[Ambos gestores de recursos](tutorial-connect-virtual-networks-portal.md) |O mesmo|
|[Um Gestor de Recursos, um clássico](create-peering-different-deployment-models.md) |O mesmo|
|[Um Gestor de Recursos, um clássico](create-peering-different-deployment-models-subscriptions.md) |Diferente|

Não é possível criar um epeering de rede virtual entre duas redes virtuais implantadas através do modelo clássico de implantação. Se precisar de ligar redes virtuais que foram criadas através do modelo de implementação clássico, pode utilizar um [Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ligar as redes virtuais.

Este tutorial identifica redes virtuais na mesma região. Você também pode peer redes virtuais em [diferentes regiões apoiadas](virtual-network-manage-peering.md#cross-region). Recomenda-se que se familiarize com os [requisitos e constrangimentos](virtual-network-manage-peering.md#requirements-and-constraints) de observação antes de espreitar redes virtuais.

Pode utilizar o [portal Azure,](#portal)a [interface de linha de comando](#cli) Azure (CLI), o Azure [PowerShell](#powershell)ou um modelo de Gestor de [Recursos Azure](#template) para criar um pinvo de rede virtual. Selecione qualquer uma das ligações de ferramentas anteriores para ir diretamente aos passos para criar um peering de rede virtual usando a sua ferramenta de eleição.

Se as redes virtuais estiverem em assinaturas diferentes, e as subscrições estiverem associadas a diferentes inquilinos do Azure Ative Directory, complete os seguintes passos antes de continuar:
1. Adicione o utilizador de cada inquilino do Diretório Ativo como [utilizador convidado](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) no inquilino do Diretório Ativo Azure oposto.
1. Cada utilizador deve aceitar o convite do utilizador convidado do inquilino do Diretório Ativo Azure oposto.

## <a name="portal"></a>Criar o peering - Portal Azure

Os seguintes passos utilizam contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, saltar os passos para sair do portal e saltar os passos para atribuir permissões de outro utilizador às redes virtuais.

1. Inicie sessão no [portal Azure](https://portal.azure.com) como *UserA*. A conta com a sua participação deve ter as permissões necessárias para criar um epeering de rede virtual. Para obter uma lista de permissões, consulte [permissões de verificação](virtual-network-manage-peering.md#permissions)de rede virtual .
2. Selecione **+ Criar um recurso,** selecione **Networking,** e, em seguida, selecione **rede Virtual**.
3. Selecione ou introduza os seguintes valores de exemplo para as seguintes definições e, em seguida, selecione **Criar:**
    - **Nome**: *myVnetA*
    - **Espaço**de endereço : *10.0.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - Gama de **endereços de sub-rede**: *10.0.0.0/24*
    - **Subscrição**: Selecione subscrição A.
    - **Grupo de recursos**: Selecione **Criar novo** e insira *o myResourceGroupA*
    - **Localização**: *Leste dos EUA*
4. Na caixa de **recursos de pesquisa** no topo do portal, digite *myVnetA*. Selecione **myVnetA** quando aparecer nos resultados da pesquisa. 
5. Selecione o controlo de **acesso (IAM)** a partir da lista vertical de opções no lado esquerdo.
6. Sob **myVnetA - Controlo de acesso (IAM)** , selecione + Adicionar atribuição de **funções**.
7. Selecione **Colaborador de Rede** na caixa **Role.**
8. Na caixa **Select,** selecione *UserB*, ou escreva o endereço de e-mail do UserB para o procurar.
9. Selecione **Guardar**.
10. Sob **myVnetA - Controlo de acesso (IAM)** , selecione **Propriedades** da lista vertical de opções no lado esquerdo. Copie o ID de **RECURSO,** que é utilizado num passo posterior. O ID do recurso é semelhante ao seguinte exemplo: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`.
11. Faça login no portal como UserA e, em seguida, inicie sessão como UserB.
12. Complete os passos 2-3, entrando ou selecionando os seguintes valores na etapa 3:

    - **Nome**: *myVnetB*
    - **Espaço**de endereço : *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - Gama de **endereços de sub-rede**: *10.1.0.0/24*
    - **Subscrição**: Selecione subscrição B.
    - **Grupo de recursos**: Selecione **Criar novo** e insira *o myResourceGroupB*
    - **Localização**: *Leste dos EUA*

13. Na caixa de **recursos de pesquisa** no topo do portal, digite *myVnetB*. Selecione **myVnetB** quando aparecer nos resultados da pesquisa.
14. Sob **o myVnetB,** selecione **Propriedades** da lista vertical de opções no lado esquerdo. Copie o ID de **RECURSO,** que é utilizado num passo posterior. O ID do recurso é semelhante ao seguinte exemplo: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`.
15. Selecione o controlo de **acesso (IAM)** sob **o myVnetB**e, em seguida, complete os passos 5-10 para o myVnetB, entrando no **UserA** no passo 8.
16. Faça login no portal como UserB e inicie sessão como UserA.
17. Na caixa de **recursos de pesquisa** no topo do portal, digite *myVnetA*. Selecione **myVnetA** quando aparecer nos resultados da pesquisa.
18. Selecione **myVnetA**.
19. Em **DEFINIÇÕES,** selecione **Peerings**.
20. Sob **myVnetA - Peerings,** selecione **+ Adicionar**
21. Em **adicionar peering,** insira ou selecione, as seguintes opções, em seguida, selecione **OK**:
     - **Nome**: *myVnetAToMyVnetB*
     - **Modelo de implementação de rede virtual**: Select **Resource Manager**.
     - **Conheço o meu iD**de recursos: verifique esta caixa.
     - **ID de recurso**: Introduza o ID do recurso a partir do passo 14.
     - **Permitir o acesso virtual à rede:** Certifique-se de que o **Ativado** está selecionado.
    Não são utilizadas outras definições neste tutorial. Para aprender sobre todas as configurações de peering, leia [Gerir os pares](virtual-network-manage-peering.md#create-a-peering)de rede virtual .
22. O olhar que criou aparece uma pequena espera depois de selecionar **OK** no passo anterior. **Iniciado** está listado na coluna **PEERING STATUS** para o **myVnetAToMyVnetB** que o seu homólogo criou. Você olhou myVnetA para myVnetB, mas agora você deve olhar myVnetB para myVnetA. O epeering deve ser criado em ambas as direções para permitir que os recursos nas redes virtuais se comuniquem entre si.
23. Faça login no portal como UserA e inicie sessão como UserB.
24. Complete os passos 17-21 novamente para o myVnetB. No passo 21, nomeie o *mi-peering myVnetBToMyVnetA*, selecione *myVnetA* para **rede Virtual**, e introduza o ID a partir do passo 10 na caixa de ID de **recursos.**
25. Alguns segundos depois de selecionar **OK** para criar o olhar para myVnetB, o **myVnetBToMyVnetA** que acabou de criar está listado com **Connected** na coluna **PEERING STATUS.**
26. Faça login no portal como UserB e inicie sessão como UserA.
27. Etapas completas 17-19 novamente. O **ESTATUTO DE PEERING** para o meu peering **myVnetAToVNetB** está agora também **ligado**. O epeering é estabelecido com sucesso depois de ver **Conectado** na coluna **PEERING STATUS** para ambas as redes virtuais no peering. Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num epeering, tem de criar o seu próprio servidor DNS. Aprenda a configurar a resolução de [nome utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Opcional**: Embora a criação de máquinas virtuais não esteja coberta neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
29. **Opcional**: Para eliminar os recursos que cria neste tutorial, preencha os passos na secção de [recursos Eliminar](#delete-portal) deste artigo.

## <a name="cli"></a>Criar o peering - Azure CLI

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, saltar os passos para sair do Azure e remover as linhas de script que criam atribuições de funções ao utilizador. Substitua UserA@azure.com e UserB@azure.com em todas as seguintes scripts pelos nomes de utilizador que está a utilizar para userA e UserB. 

Os seguintes scripts:

- Requer a versão Azure CLI 2.0.4 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de fazer o upgrade, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Trabalha numa concha bash. Para obter as opções de execução de scripts da CLI do Azure num cliente Windows, veja [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows).

Em vez de instalar o CLI e as suas dependências, pode utilizar a Casca de Nuvem Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o botão **Experimente** no script que se segue, o que invoca uma Cloud Shell com a qual pode iniciar sessão na sua conta Azure.

1. Abra uma sessão CLI e inicie sessão no Azure como UserA utilizando o comando `azure login`. A conta com a sua participação deve ter as permissões necessárias para criar um epeering de rede virtual. Para obter uma lista de permissões, consulte [permissões de verificação](virtual-network-manage-peering.md#permissions)de rede virtual .
2. Copie o seguinte guião para um editor de texto no seu PC, substitua `<SubscriptionA-Id>` com o ID de SubscriçãoA, em seguida, copie o script modificado, cole-o na sua sessão CLI e pressione `Enter`. Se não conhece o id de subscrição, insira o comando `az account show`. O valor para **id** na saída é o seu ID de subscrição.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Faça login no Azure como UserA utilizando o comando `az logout` e, em seguida, inicie sessão no Azure como UserB. A conta com a sua participação deve ter as permissões necessárias para criar um epeering de rede virtual. Para obter uma lista de permissões, consulte [permissões de verificação](virtual-network-manage-peering.md#permissions)de rede virtual .
4. Crie o myVnetB. Copie o conteúdo do script no passo 2 para um editor de texto no seu PC. Substitua `<SubscriptionA-Id>` com a identificação da SubscriçãoB. Mude 10.0.0.0.0/16 para 10.1.0.0.0/16, mude todos Os Quanto a B, e todos os B para A. Copie o script modificado, cole-o na sua sessão CLI e prima `Enter`.
5. Faça login no Azure como UserB e inicie sessão no Azure como UserA.
6. Crie uma rede virtual que olhe do myVnetA para o myVnetB. Copie os seguintes conteúdos de script para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com a identificação da SubscriçãoB. Para executar o script, copie o script modificado, cole-o na sua sessão CLI e prima Enter.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Veja o estado de observação da myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    O Estado está **iniciado.** Muda para **Connected** assim que cria o olhar para myVnetA a partir do myVnetB.

8. Inicie sessão de UserA a partir do Azure e inicie sessão no Azure como UserB.
9. Crie o olhar do myVnetB para o myVnetA. Copie o conteúdo do script no passo 6 para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com o ID para SubscriçãoA e mude todos os Quantos a B e todos os B para A. Depois de econôl as alterações, copie o script modificado, cole-o na sua sessão CLI e pressione `Enter`.
10. Veja o estado de observação do myVnetB. Copie o conteúdo do script no passo 7 para um editor de texto no seu PC. Mude A a B para o grupo de recursos e nomes de rede virtuais, copie o script, cole o script modificado na sua sessão CLI e, em seguida, pressione `Enter`. O estado de observação está **ligado.** O estado de observação da myVnetA muda para **Connected** depois de ter criado o peering de myVnetB para myVnetA. Pode voltar a entrar no UserA no Azure e completar o passo 7 novamente para verificar o estado de observação do myVnetA. 

    > [!NOTE]
    > O epeering não é estabelecido até que o estado de observação esteja **conectado** para ambas as redes virtuais.

11. **Opcional**: Embora a criação de máquinas virtuais não esteja coberta neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
12. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar os recursos](#delete-cli) neste artigo.

Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num epeering, tem de criar o seu próprio servidor DNS. Aprenda a configurar a resolução de [nome utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="powershell"></a>Criar o peering - PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, saltar os passos para sair do Azure e remover as linhas de script que criam atribuições de funções ao utilizador. Substitua UserA@azure.com e UserB@azure.com em todas as seguintes scripts pelos nomes de utilizador que está a utilizar para userA e UserB.

1. Confirme que tem a versão 1.0.0 do AzurE PowerShell ou superior. Pode fazê-lo executando o `Get-Module -Name Az` Recomendamos a instalação da versão mais recente do módulo PowerShell [Az](/powershell/azure/install-az-ps). Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Inicie uma sessão powerShell.
3. No PowerShell, inicie sessão no Azure como UserA, entrando no comando `Connect-AzAccount`. A conta com a sua participação deve ter as permissões necessárias para criar um epeering de rede virtual. Para obter uma lista de permissões, consulte [permissões de verificação](virtual-network-manage-peering.md#permissions)de rede virtual .
4. Crie um grupo de recursos e uma rede virtual A. Copie o seguinte script para um editor de texto no seu PC. Substitua `<SubscriptionA-Id>` com a identificação da SubscriçãoA. Se não conhece o id de subscrição, insira o comando `Get-AzSubscription` para vê-lo. O valor para **Id** na saída devolvida é o seu ID de subscrição. Para executar o script, copie o script modificado, cole-o no PowerShell e, em seguida, pressione `Enter`.

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Inicie sessão de UserA a partir do Azure e inicie sessão no UserB. A conta com a sua participação deve ter as permissões necessárias para criar um epeering de rede virtual. Para obter uma lista de permissões, consulte [permissões de verificação](virtual-network-manage-peering.md#permissions)de rede virtual .
6. Copie o conteúdo do script no passo 4 para um editor de texto no seu PC. Substitua `<SubscriptionA-Id>` com o ID para assinatura B. Alterar 10.0.0.0.0.0/16 para 10.1.0.0.0.16. Mude todos os Quantos a B e todos os B para A. Para executar o script, copie o script modificado, cole no PowerShell e, em seguida, pressione `Enter`.
7. Inicie sessão do UserB a partir do Azure e inicie sessão no UserA.
8. Crie o olhar da myVnetA para o myVnetB. Copie o seguinte guião para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com a identificação da assinatura B. Para executar o script, copie o script modificado, cole no PowerShell e, em seguida, pressione `Enter`.

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Veja o estado de observação da myVnetA.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O Estado está **iniciado.** Muda para **Connected** assim que configurar o olhar para myVnetA a partir do myVnetB.

10. Inicie sessão de UserA a partir do Azure e inicie sessão no UserB.
11. Crie o olhar do myVnetB para o myVnetA. Copie o conteúdo do script no passo 8 para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com a identificação da assinatura A e mude todos Os a B e todos os B para A. Para executar o script, copie o script modificado, cole-o no PowerShell e, em seguida, pressione `Enter`.
12. Veja o estado de observação do myVnetB. Copie o conteúdo do script no passo 9 para um editor de texto no seu PC. Alterar A a B para o grupo de recursos e nomes de rede virtual. Para executar o script, cola o script modificado no PowerShell e, em seguida, pressione `Enter`. O estado está **ligado.** O estado de observação da **myVnetA** muda para **Connected** depois de ter criado o peering de **myVnetB** para **myVnetA**. Pode voltar a entrar no UserA no Azure e completar o passo 9 novamente para verificar o estado de observação do myVnetA.

    > [!NOTE]
    > O epeering não é estabelecido até que o estado de observação esteja **conectado** para ambas as redes virtuais.

    Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num epeering, tem de criar o seu próprio servidor DNS. Aprenda a configurar a resolução de [nome utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Opcional**: Embora a criação de máquinas virtuais não esteja coberta neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
14. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar os recursos](#delete-powershell) neste artigo.

## <a name="template"></a>Criar o peering - Modelo de Gestor de Recursos

1. Para criar uma rede virtual e atribuir as [permissões](virtual-network-manage-peering.md#permissions)adequadas, preencha os passos nas secções [portal,](#portal) [Azure CLI](#cli)ou [PowerShell](#powershell) deste artigo.
2. Guarde o texto que se segue a um ficheiro no seu computador local. Substitua `<subscription ID>` pelo ID de subscrição do UserA. Pode guardar o ficheiro como vnetpeeringA.json, por exemplo.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Inicie sessão no Azure como UserA e implemente o modelo utilizando o [portal](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template), ou o [Azure CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Especifique o nome do ficheiro que guardou o texto json exemplo no passo 2 para.
4. Copie o exemplo json do passo 2 para um ficheiro no seu computador e faça alterações nas linhas que começam com:
   - **nome**: Alterar *myVnetA/myVnetAToMyVnetB* para *myVnetB/myVnetBToMyVnetA*.
   - **id**: Substitua `<subscription ID>` pelo ID de subscrição do UserB e mude *o myVnetB* para *myVnetA*.
5. Complete novamente o passo 3, iniciando sessão no Azure como UserB.
6. **Opcional**: Embora a criação de máquinas virtuais não esteja coberta neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
7. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos na secção de [recursos Eliminar](#delete) deste artigo, utilizando o portal Azure, powerShell ou o Azure CLI.

## <a name="delete"></a>Eliminar recursos
Quando terminar este tutorial, talvez queira apagar os recursos que criou no tutorial, para que não incorra em taxas de utilização. A eliminação de um grupo de recursos também elimina todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Inicie sessão no portal Azure como UserA.
2. Na caixa de pesquisa do portal, introduza **o myResourceGroupA**. Nos resultados da pesquisa, selecione **myResourceGroupA**.
3. Selecione **Eliminar**.
4. Para confirmar a eliminação, na caixa **TYPE The RESOURCE GROUP NAME,** introduza o **myResourceGroupA**, e, em seguida, selecione **Delete**.
5. Faça login no portal como UserA e inicie sessão como UserB.
6. Etapas completas 2-4 para o myResourceGroupB.

### <a name="delete-cli"></a>CLI do Azure

1. Faça login no Azure como UserA e execute o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Faça login no Azure como UserA e inicie sessão como UserB.
3. Execute o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="delete-powershell"></a>PowerShell

1. Faça login no Azure como UserA e execute o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Faça login no Azure como UserA e inicie sessão como UserB.
3. Execute o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Passos Seguintes

- Familiarize-se completamente com [importantes restrições e comportamentos](virtual-network-manage-peering.md#requirements-and-constraints) de rede virtual antes de criar uma rede virtual que procura o uso da produção.
- Conheça todas as [definições de peering](virtual-network-manage-peering.md#create-a-peering)de rede virtual .
- Aprenda a criar um hub e falou de [topologia](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) de rede com o peering virtual da rede.
