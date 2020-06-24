---
title: Criar um serviço de ligação privada em Azure Private Link
description: Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure para criar um serviço de ligação privada.
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: c9ed628501e8fa02b816a1564b91620404dfc379
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84817624"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-azure-resource-manager-template"></a>Quickstart: Criar um serviço de ligação privada utilizando um modelo de Gestor de Recursos Azure

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure para criar um serviço de ligação privada.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também pode completar este quickstart utilizando o [portal Azure](create-private-link-service-portal.md), [Azure PowerShell,](create-private-link-service-powershell.md)ou o [Azure CLI](create-private-link-service-cli.md).

## <a name="prerequisite"></a>Pré-requisito

Precisa de uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-private-link-service"></a>Criar um serviço de ligação privada

Este modelo cria um serviço de ligação privada.

### <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

Os recursos Azure múltiplos são definidos no modelo:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): Existe uma rede virtual para cada máquina virtual.
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): O equilibrador de carga que expõe as máquinas virtuais que acolhem o serviço.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): Existem duas interfaces de rede, uma para cada máquina virtual.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): Existem duas máquinas virtuais, uma que acolhe o serviço e outra que testa a ligação ao ponto final privado.
- [**Microsoft.Compute/virtualMachines/extensões**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): A extensão que instala um servidor web.
- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): O serviço de ligação privada para expor o serviço.
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): Existem dois endereços IP públicos, um para cada máquina virtual.
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): O ponto final privado para aceder ao serviço.

### <a name="deploy-the-template"></a>Implementar o modelo

Aqui está como implementar o modelo do Gestor de Recursos Azure para Azure:

1. Para iniciar súm na Azure e abrir o modelo, **selecione Implementar para Azure**. O modelo cria uma máquina virtual, balanceador de carga padrão, serviço de ligação privada, ponto final privado, networking e uma máquina virtual para validar.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Selecione ou crie o seu grupo de recursos.
3. Digite o nome de utilizador e palavra-passe do administrador de máquina virtual.
4. Leia a declaração de termos e condições. Se concordar, **selecione concordo com os termos e condições acima**  >  **de Compra.**

## <a name="validate-the-deployment"></a>Validar a implementação

> [!NOTE]
> O modelo Azure Resource Manager gera um nome único para a máquina virtual myConsumerVm<b>{uniqueid}</b> recurso. Substitua o seu valor gerado por **{uniqueid}**.

### <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Ligue-se ao VM _myConsumerVm{uniqueid}_ da internet da seguinte forma:

1.  Na barra de pesquisa do portal, _insira myConsumerVm{uniqueid}_.

2.  Selecione **Ligar**. **Ligue-se à máquina virtual.**

3.  Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop Protocol _(.rdp)_ e transfere-o para o computador.

4.  Abra o ficheiro .rdp transferido.

    a. Se lhe for pedido, selecione **Ligar**.

    b. Introduza o nome de utilizador e a palavra-passe que especificou quando criou o VM.
    
    > [!NOTE]
    > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

5.  Selecione **OK**.

6.  Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

7.  Depois de aparecer o ambiente de trabalho em VM, minimize-o para voltar ao seu ambiente de trabalho local.

### <a name="access-the-http-service-privately-from-the-vm"></a>Aceda ao serviço http em privado a partir do VM

Aqui está como ligar ao serviço http a partir do VM usando o ponto final privado.

1.  Vá ao Ambiente de Trabalho Remoto do _myConsumerVm{uniqueid}_.
2.  Abra um browser e insira o endereço de ponto final privado: http://10.0.0.5/ .
3.  A página IIS predefinida aparece.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o serviço de ligação privada, elimine o grupo de recursos. Isto remove o serviço de ligação privada e todos os recursos conexos.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Azure Private Link](private-link-overview.md).
