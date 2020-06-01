---
title: Modelo Azure Private Link Service ARM
description: Modelo ARM de serviço de ligação privada
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 93a66057ddb0034f7ac9ac62578292ca38f2d2fe
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84237130"
---
# <a name="create-a-private-link-service---resource-manager-template"></a>Criar um serviço de ligação privada - Modelo de Gestor de Recursos

Neste arranque rápido, você usa um modelo de Gestor de Recursos para criar um serviço de ligação privada.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também pode completar este quickstart utilizando o [portal Azure](create-private-link-service-portal.md), [Azure PowerShell,](create-private-link-service-powershell.md)ou [Azure CLI](create-private-link-service-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-link-service"></a>Criar um serviço de ligação privada

este modelo cria um serviço de ligação privada.

### <a name="review-the-template"></a>Rever o modelo

O modelo usado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-privatelink-service/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

Os recursos Azure múltiplos são definidos no modelo:

- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices) : serviço de ligação privada para expor o serviço de forma privada
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints) : ponto final privado para aceder privadamente ao serviço
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers) : Balanceador de carga que expõe as máquinas virtuais que acolhem o serviço
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : 2 Máquinas virtuais, uma que acolhe o serviço e outra para testar a ligação ao ponto final privado
- [**Microsoft.Compute/virtualMachines/extensões**](/azure/templates/Microsoft.Compute/virtualMachines/extensions) : Extensão que instala servidor web
- [**Microsoft.Network/virtualNetworks:**](/azure/templates/microsoft.network/virtualnetworks) um para cada Máquina Virtual
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses) : 2 endereço IP público, um para cada Máquina Virtual
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : 2 Interfaces de rede, uma para cada Máquina Virtual

### <a name="deploy-the-template"></a>Implementar o modelo

Implementar o modelo do Gestor de Recursos para Azure:

1. **Selecione Implementar para Azure** para iniciar súm na Azure e abrir o modelo. O modelo cria uma Máquina Virtual, balanceador de carga padrão, serviço de ligação privada, ponto final privado, networking e uma máquina virtual para validar.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Selecione ou crie o seu grupo de recursos,
3. Digite o nome de utilizador e palavra-passe do administrador de máquina virtual.
4. Selecione **Concordo com os termos e condições acima indicados** e, em seguida, selecione **Comprar**.

## <a name="validate-the-deployment"></a>Validar a implementação

> [!NOTE]
> o modelo ARM gera um nome único para o recurso Virtual Machine myConsumerVm<b>{uniqueid}</b> , por favor substitua <b>{uniqueid}</b> pelo seu valor gerado.

### <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Ligue-se ao VM _myConsumerVm{uniqueid}_ da internet da seguinte forma:

1.  Na barra de pesquisa do portal, _insira myConsumerVm{uniqueid}_.

2.  Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **o Connect à máquina virtual** abre-se.

3.  Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop Protocol _(.rdp)_ e transfere-o para o computador.

4.  Abra o ficheiro.rdp \* descarregado.

    a. Se lhe for pedido, selecione **Ligar**.

    b. Introduza o nome de utilizador e a palavra-passe que especificou ao criar o VM.
    
    > [!NOTE]
    > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

5.  Selecione **OK**.

6.  Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

7.  Assim que o ambiente de trabalho em VM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.

### <a name="access-the-http-service-privately-from-the-vm"></a>Aceda ao serviço http em privado a partir do VM

Nesta secção, irá ligar-se ao serviço http a partir do VM utilizando o Ponto Final Privado.

1.  No ambiente de trabalho remoto do _myConsumerVm{uniqueid}_
2.  Abra um navegador e insira o endereço de ponto final privadohttp://10.0.0.5/
3.  Você verá a página padrão do IIS

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o serviço de ligação privada, elimine o grupo de recursos. Isto remove o serviço de ligação privada e todos os recursos conexos.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Private Link](private-link-overview.md)
