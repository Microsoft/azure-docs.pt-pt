---
title: 'Quickstart: Tráfego web direto usando um modelo de Gestor de Recursos'
titleSuffix: Azure Application Gateway
description: Aprenda a usar um modelo de Gestor de Recursos para criar um Gateway de Aplicação Azure que direciona o tráfego web para máquinas virtuais numa piscina de backend.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/23/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: cce3ef20a93c6d7a24bfa312501d2f8cc8ed9273
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604905"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---resource-manager-template"></a>Quickstart: Tráfego web direto com Gateway de aplicação Azure - modelo de Gestor de Recursos

Neste arranque rápido, você usa um modelo de Gestor de Recursos para criar um Gateway de Aplicação Azure. Em seguida, testa a porta de entrada de aplicação para se certificar de que funciona corretamente.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também pode completar este quickstart utilizando o [portal Azure,](quick-create-portal.md) [Azure PowerShell](quick-create-powershell.md)ou [Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Por uma questão de simplicidade, este modelo cria uma configuração simples com um IP frontal público, um ouvinte básico para hospedar um único site na porta de aplicação, uma regra básica de encaminhamento de pedidos, e duas máquinas virtuais na piscina de backend.

### <a name="review-the-template"></a>Reveja o modelo

O modelo usado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

Vários recursos Azure são definidos no modelo:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresss**](/azure/templates/microsoft.network/publicipaddresses) : um para o gateway da aplicação e dois para as máquinas virtuais.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : duas máquinas virtuais
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : dois para as máquinas virtuais
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : para configurar o IIS e as páginas web


### <a name="deploy-the-template"></a>Implementar o modelo

Implementar modelo de Gestor de Recursos para Azure:

1. Selecione **Implementar para Azure** para iniciar sessão no Azure e abrir o modelo. O modelo cria um portal de aplicação, a infraestrutura de rede e duas máquinas virtuais na piscina de backend que funciona o IIS.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Selecione ou crie o seu grupo de recursos, escreva o nome de utilizador e a palavra-passe do administrador da máquina virtual.
3. Selecione **Concordo com os termos e condições acima indicados** e, em seguida, selecione **Comprar**. A implantação pode demorar 20 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implementação

Embora o IIS não seja obrigado a criar o gateway da aplicação, está instalado para verificar se o Azure criou com sucesso o portal de aplicações. Utilize o IIS para testar o gateway de aplicação:

1. Encontre o endereço IP público para o gateway da aplicação na sua página **'Visão Geral'.** ![Registre o endereço](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) IP público da aplicação Ou, pode selecionar **Todos os recursos,** introduzir *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecioná-lo nos resultados da pesquisa. O Azure exibe o endereço IP público na página **'Overview'.**
2. Copie o endereço IP público e, em seguida, cole-o na barra de endereços do seu navegador para navegar nesse endereço IP.
3. Verifique a resposta. Uma resposta válida verifica que o gateway da aplicação foi criado com sucesso e pode ligar-se com sucesso ao backend.

   ![Testar o gateway de aplicação](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Refresque o navegador várias vezes e deverá ver ligações tanto para myVM1 como myVM2.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa dos recursos que criou com o portal de aplicação, elimine o grupo de recursos. Isto remove o portal de aplicação e todos os recursos conexos.

Para eliminar o grupo `Remove-AzResourceGroup` de recursos, ligue para o cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
