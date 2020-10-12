---
title: Criar um peering VNet - diferentes subscrições
titlesuffix: Azure Virtual Network
description: Saiba como criar uma rede virtual de observação entre redes virtuais criadas através do Gestor de Recursos que existem em diferentes subscrições do Azure no mesmo ou diferente inquilino do Azure Ative Directory.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: kumud
ms.openlocfilehash: 79062ae45f04b290f6e4120906b98590ce95dbe1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87833271"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions-and-azure-active-directory-tenants"></a>Criar um espreitamento de rede virtual - Gestor de Recursos, diferentes subscrições e inquilinos do Azure Ative Directory

Neste tutorial, aprende-se a criar uma rede virtual de observação entre redes virtuais criadas através do Resource Manager. As redes virtuais existem em diferentes subscrições que podem pertencer a diferentes inquilinos do Azure Ative Directory (Azure AD). Espreitar duas redes virtuais permite que os recursos em diferentes redes virtuais se comuniquem entre si com a mesma largura de banda e latência como se os recursos estivessem na mesma rede virtual. Saiba mais sobre [o espreitamento da rede Virtual.](virtual-network-peering-overview.md)

Os passos para criar um espreguiçadeira de rede virtual são diferentes, dependendo se as redes virtuais estão nas mesmas subscrições, ou diferentes, e que [modelo de implementação Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas através. Saiba como criar uma rede virtual a espreitar outros cenários selecionando o cenário a partir da seguinte tabela:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[Ambas com Resource Manager](tutorial-connect-virtual-networks-portal.md) |Mesma|
|[Uma com Resource Manager, outra com clássica](create-peering-different-deployment-models.md) |Mesma|
|[Uma com Resource Manager, outra com clássica](create-peering-different-deployment-models-subscriptions.md) |Diferente|

Não é possível criar um espreitamento de rede virtual entre duas redes virtuais implantadas através do modelo clássico de implementação. Se precisar de ligar redes virtuais que foram ambas criadas através do modelo de implementação clássico, pode utilizar um Gateway Azure [VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ligar as redes virtuais.

Este tutorial pares redes virtuais na mesma região. Também pode espreitar redes virtuais em [diferentes regiões apoiadas.](virtual-network-manage-peering.md#cross-region) Recomenda-se que se familiarize com os [requisitos e constrangimentos de espreitar](virtual-network-manage-peering.md#requirements-and-constraints) antes de espreitar as redes virtuais.

Pode utilizar o [portal Azure,](#portal)a [interface de linha de comando](#cli) Azure (CLI), Azure [PowerShell](#powershell)ou um [modelo Azure Resource Manager](#template) para criar um esprevamento de rede virtual. Selecione qualquer uma das ligações de ferramentas anteriores para ir diretamente aos passos para criar uma rede virtual espreitando usando a sua ferramenta de eleição.

Se as redes virtuais estiverem em diferentes subscrições, e as subscrições estiverem associadas a diferentes inquilinos do Azure Ative Directory, complete os seguintes passos antes de continuar:
1. Adicione o utilizador de cada inquilino ative como [utilizador convidado](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) no inquilino Azure Ative Directory oposto.
1. Cada utilizador tem de aceitar o convite de utilizador convidado do inquilino do Azure Active Directory oposto.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Criar olhando - Portal Azure

Os seguintes passos utilizam contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todas as etapas, saltar os passos para iniciar sessão fora do portal e saltar os passos para atribuir outras permissões do utilizador às redes virtuais.

1. Faça login no [portal Azure](https://portal.azure.com) como *UserA*. A conta com que inicia sessão deve ter as permissões necessárias para criar uma rede virtual de observação. Para obter uma lista de permissões, consulte [permissões de espreitar rede virtual](virtual-network-manage-peering.md#permissions).
2. Selecione **+ Crie um recurso,** selecione **Networking**e, em seguida, selecione **rede Virtual**.
3. Selecione ou introduza os seguintes valores de exemplo para as seguintes definições e, em seguida, **selecione Criar**:
    - **Nome**: *myVnetA*
    - **Espaço de**endereço : *10.0.0.0/16*
    - **Nome da sub-rede**: *predefinição*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Assinatura**: Selecione subscrição A.
    - **Grupo de recursos**: Selecione **Criar novo** e insira *o myResourceGroupA*
    - **Localização**: *Leste dos EUA*
4. Na caixa **de recursos de busca** no topo do portal, *digite myVnetA*. Selecione **myVnetA** quando aparecer nos resultados da pesquisa. 
5. Selecione o controlo de **acesso (IAM)** da lista vertical de opções no lado esquerdo.
6. No **âmbito do myVnetA - Controlo de acesso (IAM)**, selecione **+ Adicionar a atribuição de funções**.
7. Selecione o **contribuinte de rede** na caixa **Role.**
8. Na caixa **Select,** selecione *UserB*ou digite o endereço de e-mail do UtilizadorB para pesquisar.
9. Selecione **Guardar**.
10. No **âmbito do myVnetA - Controlo de acesso (IAM)**, selecione **Propriedades** da lista vertical de opções no lado esquerdo. Copie o **ID recurso,** que é utilizado num passo posterior. O ID de recurso é semelhante ao seguinte exemplo: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA` .
11. Faça login no portal como UserA e, em seguida, faça login como UserB.
12. Passos completos 2-3, introduzindo ou selecionando os seguintes valores no passo 3:

    - **Nome**: *myVnetB*
    - **Espaço de**endereço : *10.1.0.0/16*
    - **Nome da sub-rede**: *predefinição*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Assinatura**: Selecione subscrição B.
    - **Grupo de recursos**: Selecione **Criar novo** e insira *o myResourceGroupB*
    - **Localização**: *Leste dos EUA*

13. Na caixa **de recursos de busca** no topo do portal, digite *myVnetB*. Selecione **myVnetB** quando aparecer nos resultados da pesquisa.
14. No **myVnetB,** selecione **Propriedades** da lista vertical de opções no lado esquerdo. Copie o **ID recurso,** que é utilizado num passo posterior. O ID de recurso é semelhante ao seguinte exemplo: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB` .
15. Selecione **o controlo de acesso (IAM)** no **myVnetB**e, em seguida, complete os passos 5-10 para o myVnetB, entrando no **UserA** no passo 8.
16. Faça login no portal como UserB e faça login como UserA.
17. Na caixa **de recursos de busca** no topo do portal, *digite myVnetA*. Selecione **myVnetA** quando aparecer nos resultados da pesquisa.
18. Selecione **myVnetA**.
19. Em **DEFINIÇÕES**, **selecione Peerings**.
20. Sob **myVnetA - Peerings,** selecione **+ Adicionar**
21. Em **Adicionar olhando,** introduzir ou selecionar as seguintes opções e, em seguida, selecionar **OK**:
     - **Nome**: *myVnetAToMyVnetB*
     - **Modelo de implementação de rede virtual**: Select **Resource Manager**.
     - **Eu sei a minha identificação de recursos:** verifique esta caixa.
     - **ID de recurso**: Introduza o ID do recurso a partir do passo 14.
     - **Permitir o acesso à rede virtual:** Certifique-se de que **o Enabled** está selecionado.
    Não são utilizadas outras definições neste tutorial. Para saber mais sobre todas as definições de observação, leia [Gerir os olhos de rede virtuais](virtual-network-manage-peering.md#create-a-peering).
22. O espreitamento criado aparece uma curta espera depois de selecionar **OK** no passo anterior. **O Iniciado** está listado na coluna **PEERING STATUS** para o **myVnetAToMyVnetB** que o observa a sua criação. Você olhou myVnetA para myVnetB, mas agora você tem que espreitar myVnetB para myVnetA. O espreitamento deve ser criado em ambas as direções para permitir que os recursos nas redes virtuais se comuniquem entre si.
23. Faça login no portal como UserA e faça login como UserB.
24. Complete os passos 17-21 novamente para o myVnetB. No passo 21, nomeie o *myVnetBToMyVnetA,* selecione *myVnetA* para **rede Virtual**e introduza o ID a partir do passo 10 na caixa **de ID** de recurso.
25. Alguns segundos depois de selecionar **OK** para criar o espreitamento para o myVnetB, o **myVnetBToMyVnetA** que o seu acabou de criar está listado com **Connected** na coluna **STATUS PEERING.**
26. Faça login no portal como UserB e faça login como UserA.
27. Complete os passos 17-19 novamente. O **ESTADO DE PEERING** para o persipecção **myVnetAToVNetB** está agora também **ligado**. O espreitamento é estabelecido com sucesso depois de ver **Conectado** na coluna **STATUS PEERING** para ambas as redes virtuais no espreitamento. Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomeS Azure padrão para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num espreitê-lo, tem de criar o seu próprio servidor DNS. Saiba como configurar a [resolução Name utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Opcional**: Embora a criação de máquinas virtuais não esteja abrangida por este tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
29. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos na secção [de recursos de Eliminar](#delete-portal) deste artigo.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Criar olhando - Azure CLI

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todas as etapas, saltar os passos para iniciar sessão fora do Azure e remover as linhas de script que criam atribuições de funções de utilizador. Substitua UserA@azure.com e em todos os UserB@azure.com seguintes scripts pelos nomes de utilizador que está a utilizar para UserA e UserB. 

Os seguintes scripts:

- Requer a versão Azure CLI 2.0.4 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de atualizar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Trabalha numa concha bash. Para obter as opções de execução de scripts da CLI do Azure num cliente Windows, veja [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows).

Em vez de instalar o CLI e as suas dependências, pode utilizar a Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o botão **'Experimente-o'** no script que se segue, que invoca uma Cloud Shell com a qual pode iniciar sessão na sua conta Azure.

1. Abra uma sessão CLI e faça login no Azure como UserA utilizando o `azure login` comando. A conta com que inicia sessão deve ter as permissões necessárias para criar uma rede virtual de observação. Para obter uma lista de permissões, consulte [permissões de espreitar rede virtual](virtual-network-manage-peering.md#permissions).
2. Copie o seguinte script para um editor de texto no seu PC, `<SubscriptionA-Id>` substitua-o pelo ID da SubscriçãoA, depois copie o script modificado, cole-o na sessão CLI e prima `Enter` . Se não conhece o seu ID de assinatura, insira o `az account show` comando. O valor para **id** na saída é o seu ID de subscrição.

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

3. Faça login no Azure como UserA utilizando o `az logout` comando e, em seguida, inicie sessão no Azure como UserB. A conta com que inicia sessão deve ter as permissões necessárias para criar uma rede virtual de observação. Para obter uma lista de permissões, consulte [permissões de espreitar rede virtual](virtual-network-manage-peering.md#permissions).
4. Crie o myVnetB. Copie os conteúdos do script no passo 2 para um editor de texto no seu PC. `<SubscriptionA-Id>`Substitua-o pelo ID da SubscriçãoB. Alterar 10.0.0.0/16 para 10.1.0.0/16, alterar tudo o que é B, e todos os Bs para A. Copiar o script modificado, colar-o na sessão do CLI e premir `Enter` .
5. Faça login no Azure como UserB e faça login no Azure como UserA.
6. Crie uma rede virtual que espreita do myVnetA para o myVnetB. Copie os seguintes conteúdos de script para um editor de texto no seu PC. `<SubscriptionB-Id>`Substitua-o pelo ID da SubscriçãoB. Para executar o script, copie o script modificado, cole-o na sessão de CLI e prima Enter.

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
          --remote-vnet /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Veja o estado de observação da myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    O Estado é **Iniciado.** Muda para **Connected** assim que criar o espreitamento para o myVnetA a partir do myVnetB.

8. Faça login userA a partir de Azure e faça login no Azure como UserB.
9. Crie o espreitamento do myVnetB para o myVnetA. Copie os conteúdos do script no passo 6 para um editor de texto no seu PC. `<SubscriptionB-Id>`Substitua-se pelo ID para SubscriçãoA e altere tudo o que é Para B e todos os Bs para A. Depois de então, copie o script modificado, cole-o na sessão de CLI e prima `Enter` .
10. Veja o estado de observação do myVnetB. Copie os conteúdos do script no passo 7 para um editor de texto no seu PC. Altere A para B para o grupo de recursos e nomes de rede virtuais, copie o script, cole o script modificado na sua sessão de CLI e, em seguida, prima `Enter` . O estado de observação está **ligado.** O estado de observação do myVnetA muda para **Connected** depois de ter criado o espreitamento do myVnetB para o myVnetA. Pode iniciar o registo do UserA no Azure e completar o passo 7 novamente para verificar o estado de observação da myVnetA. 

    > [!NOTE]
    > O espreitamento não é estabelecido até que o estado de observação esteja **ligado** para ambas as redes virtuais.

11. **Opcional**: Embora a criação de máquinas virtuais não esteja abrangida por este tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
12. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar recursos](#delete-cli) neste artigo.

Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomeS Azure padrão para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num espreitê-lo, tem de criar o seu próprio servidor DNS. Saiba como configurar a [resolução Name utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="create-peering---powershell"></a><a name="powershell"></a>Criar olhando - PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todas as etapas, saltar os passos para iniciar sessão fora do Azure e remover as linhas de script que criam atribuições de funções de utilizador. Substitua UserA@azure.com e em todos os UserB@azure.com seguintes scripts pelos nomes de utilizador que está a utilizar para UserA e UserB.

1. Confirme que tem a versão 1.0.0 ou superior do Azure PowerShell. Pode fazê-lo executando o `Get-Module -Name Az` Recomendamos a instalação da versão mais recente do módulo PowerShell [Az](/powershell/azure/install-az-ps). Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Inicie uma sessão PowerShell.
3. Em PowerShell, faça login no Azure como UserA inserindo o `Connect-AzAccount` comando. A conta com que inicia sessão deve ter as permissões necessárias para criar uma rede virtual de observação. Para obter uma lista de permissões, consulte [permissões de espreitar rede virtual](virtual-network-manage-peering.md#permissions).
4. Crie um grupo de recursos e rede virtual A. Copie o seguinte script para um editor de texto no seu PC. `<SubscriptionA-Id>`Substitua-o pelo ID da SubscriçãoA. Se não conhece o seu ID de assinatura, insira o `Get-AzSubscription` comando para o visualizar. O valor **para id** na saída devolvida é o seu ID de subscrição. Para executar o script, copie o script modificado, cole-o no PowerShell e, em seguida, prima `Enter` .

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

5. Faça login userA a partir de Azure e faça login no UserB. A conta com que inicia sessão deve ter as permissões necessárias para criar uma rede virtual de observação. Para obter uma lista de permissões, consulte [permissões de espreitar rede virtual](virtual-network-manage-peering.md#permissions).
6. Copie os conteúdos do script no passo 4 para um editor de texto no seu PC. `<SubscriptionA-Id>`Substitua-o pelo ID para a assinatura B. Altere 10.0.0.0/16 para 10.1.0.0/16. Mude tudo quanto a B e todos os Bs para A. Para executar o script, copie o script modificado, cole-o em PowerShell e, em seguida, prima `Enter` .
7. Faça login no UserB a partir do Azure e faça login no UserA.
8. Crie o espreitamento do myVnetA para o myVnetB. Copie o seguinte script para um editor de texto no seu PC. `<SubscriptionB-Id>`Substitua-o pelo ID da assinatura B. Para executar o script, copie o script modificado, cole-o no PowerShell e, em seguida, prima `Enter` .

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

    O Estado é **Iniciado.** Muda para **Connected** assim que configurar o espreitamento para o myVnetA a partir do myVnetB.

10. Faça login userA a partir de Azure e faça login no UserB.
11. Crie o espreitamento do myVnetB para o myVnetA. Copie os conteúdos do script no passo 8 para um editor de texto no seu PC. `<SubscriptionB-Id>`Substitua-se pelo ID da subscrição A e altere tudo o que é B e todos os Bs para A. Para executar o script, copie o script modificado, cole-o no PowerShell e, em seguida, prima `Enter` .
12. Veja o estado de observação do myVnetB. Copie os conteúdos do script no passo 9 para um editor de texto no seu PC. Altere de A para B para os nomes do grupo de recursos e da rede virtual. Para executar o script, cole o script modificado no PowerShell e, em seguida, prima `Enter` . O estado está **ligado.** O estado de observação do **myVnetA** muda para **Connected** depois de ter criado o espreitamento do **myVnetB** para **o myVnetA**. Pode iniciar o registo do UserA no Azure e completar o passo 9 novamente para verificar o estado de observação da myVnetA.

    > [!NOTE]
    > O espreitamento não é estabelecido até que o estado de observação esteja **ligado** para ambas as redes virtuais.

    Quaisquer recursos Azure que crie em qualquer rede virtual são agora capazes de comunicar uns com os outros através dos seus endereços IP. Se estiver a utilizar a resolução de nomeS Azure padrão para as redes virtuais, os recursos nas redes virtuais não são capazes de resolver nomes através das redes virtuais. Se quiser resolver nomes através de redes virtuais num espreitê-lo, tem de criar o seu próprio servidor DNS. Saiba como configurar a [resolução Name utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Opcional**: Embora a criação de máquinas virtuais não esteja abrangida por este tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
14. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos em [Eliminar recursos](#delete-powershell) neste artigo.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Criar o peering - Modelo de gestor de recursos

1. Para criar uma rede virtual e atribuir as [permissões](virtual-network-manage-peering.md#permissions)adequadas, complete os passos nas secções [Portal](#portal), [Azure CLI](#cli)ou [PowerShell](#powershell) deste artigo.
2. Guarde o texto que se segue a um ficheiro no seu computador local. `<subscription ID>`Substitua-o pelo ID de subscrição do UserA. Pode guardar o ficheiro à medida que vnetpeeringA.js, por exemplo.

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

3. Faça login no Azure como UserA e implemente o modelo utilizando o [portal](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template), ou o [Azure CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Especifique o nome do ficheiro que guardou o texto de exemplo json no passo 2 para.
4. Copie o exemplo json do passo 2 para um ficheiro no seu computador e faça alterações nas linhas que começam com:
   - **nome**: Altere *myVnetA/myVnetAToMyVnetB* para *myVnetB/myVnetBToMyVnetA*.
   - **id**: `<subscription ID>` Substitua-se pelo ID de subscrição do UtilizadorB e altere *o myVnetB* para *myVnetA*.
5. Complete o passo 3 novamente, iniciando sessão no Azure como UserB.
6. **Opcional**: Embora a criação de máquinas virtuais não esteja abrangida por este tutorial, pode criar uma máquina virtual em cada rede virtual e ligar-se de uma máquina virtual à outra, para validar a conectividade.
7. **Opcional**: Para eliminar os recursos que cria neste tutorial, complete os passos na secção de [recursos delete](#delete) deste artigo, utilizando o portal Azure, PowerShell ou o Azure CLI.

## <a name="delete-resources"></a><a name="delete"></a>Eliminar recursos
Quando terminar este tutorial, talvez queira apagar os recursos que criou no tutorial, para não incorrer em taxas de utilização. A eliminação de um grupo de recursos também elimina todos os recursos que estão no grupo de recursos.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portal do Azure

1. Faça login no portal Azure como UserA.
2. Na caixa de pesquisa do portal, insira **o myResourceGroupA**. Nos resultados da pesquisa, selecione **myResourceGroupA**.
3. Selecione **Eliminar**.
4. Para confirmar a eliminação, na caixa **TYPE THE RESOURCE GROUP NAME,** introduza o **myResourceGroupA**e, em seguida, selecione **Delete**.
5. Faça login no portal como UserA e faça login como UserB.
6. Passos completos 2-4 para o myResourceGroupB.

### <a name="azure-cli"></a><a name="delete-cli"></a>CLI do Azure

1. Faça login no Azure como UserA e execute o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Faça login no Azure como UserA e faça login como UserB.
3. Execute o seguinte comando:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Faça login no Azure como UserA e execute o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Faça login no Azure como UserA e faça login como UserB.
3. Execute o seguinte comando:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Passos seguintes

- Familiarize-se completamente com [importantes restrições e comportamentos de observação de rede virtuais](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar uma rede virtual que espreita para uso de produção.
- Saiba mais sobre todas as [definições de observação de rede virtual](virtual-network-manage-peering.md#create-a-peering).
- Aprenda a [criar um hub e falou topologia de rede](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) com olhando a rede virtual.
