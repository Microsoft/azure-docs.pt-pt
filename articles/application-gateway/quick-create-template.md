---
title: 'Quickstart: Tráfego web direto usando um modelo de Gestor de Recursos'
titleSuffix: Azure Application Gateway
description: Neste arranque rápido, você aprende a usar um modelo de Gestor de Recursos para criar um Gateway de aplicação Azure que direciona o tráfego web para máquinas virtuais em uma piscina de backend.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 08/27/2020
ms.author: victorh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: 3ea5c891a0e3709c1ce469be2e9101a0825acfcb
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093876"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>Quickstart: Tráfego web direto com Gateway de aplicação Azure - modelo ARM

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure (modelo ARM) para criar um Gateway de aplicação Azure. Em seguida, teste o portal de aplicação para se certificar de que funciona corretamente.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também pode completar este quickstart utilizando o [portal Azure](quick-create-portal.md), [Azure PowerShell,](quick-create-powershell.md)ou [Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>Rever o modelo

Por uma questão de simplicidade, este modelo cria uma configuração simples com um IP frontal público, um ouvinte básico para hospedar um único site no gateway de aplicação, uma regra de encaminhamento de pedido básico, e duas máquinas virtuais na piscina de backend.

O modelo usado neste quickstart é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json":::

Os recursos Azure múltiplos são definidos no modelo:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses:**](/azure/templates/microsoft.network/publicipaddresses) um para o gateway de aplicações e dois para as máquinas virtuais.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : duas máquinas virtuais
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dois para as máquinas virtuais
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : para configurar o IIS e as páginas web

## <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo ARM para Azure:

1. **Selecione Implementar para Azure** para iniciar súm na Azure e abrir o modelo. O modelo cria um gateway de aplicação, a infraestrutura de rede e duas máquinas virtuais na piscina de backend que executa o IIS.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Selecione ou crie o seu grupo de recursos, digite o nome de utilizador e palavra-passe do administrador de máquina virtual.
3. Selecione **'Rever + Criar'** e, em seguida, selecione **Criar.**

   A colocação pode demorar 20 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implementação

Embora o IIS não seja necessário para criar o gateway de aplicações, está instalado para verificar se o Azure criou com sucesso o gateway da aplicação. Utilize o IIS para testar o gateway de aplicações:

1. Encontre o endereço IP público para a porta de aplicação na sua página **de visão geral.** ![ Registar endereço IP público de gateway de aplicações ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) Ou, pode selecionar **Todos os recursos,** inserir *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecioná-lo nos resultados da pesquisa. O Azure exibe o endereço IP público na página **'Vista Geral'.**
2. Copie o endereço IP público e, em seguida, cole-o na barra de endereço do seu navegador para navegar nesse endereço IP.
3. Verifique a resposta. Uma resposta válida verifica que o gateway de aplicações foi criado com sucesso e pode conectar-se com sucesso com o backend.

   ![Testar o gateway de aplicação](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Refresque o navegador várias vezes e deverá ver ligações tanto para o myVM1 como para o myVM2.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o gateway de aplicações, elimine o grupo de recursos. Isto remove o gateway de aplicação e todos os recursos relacionados.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
