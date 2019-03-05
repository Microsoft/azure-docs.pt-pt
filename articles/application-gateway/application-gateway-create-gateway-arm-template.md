---
title: Criar um Gateway de aplicação do Azure - modelos | Documentos da Microsoft
description: Esta página fornece instruções para criar um gateway de aplicação do Azure com o modelo Azure Resource Manager
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: victorh
ms.openlocfilehash: b41fbc3e834c7740d435e30a571d2a00671bfa64
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316409"
---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Criar um gateway de aplicação com o modelo Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modelo do Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI do Azure](application-gateway-create-gateway-cli.md)

O Application Gateway do Azure é um balanceador de carga de 7 camadas. Fornece ativação pós-falha e pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na cloud ou no local. O Gateway de Aplicação proporciona muitas funcionalidades de controlador de entrega de aplicação (ADC), incluindo o balanceamento de carga HTTP, afinidade por sessões com base em cookies, descarga do SSL (Secure Sockets Layer), sondas de estado de funcionamento personalizadas, suporte multilocal e muitas outras. Para encontrar uma lista completa das funcionalidades suportadas, visite [descrição geral do Gateway de aplicação](application-gateway-introduction.md)

Este artigo orienta a transferir e modificar um existente [modelo Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) partir do GitHub e a implementar o modelo a partir do GitHub, PowerShell e a CLI do Azure.

Se estiver a implementar o modelo diretamente a partir do GitHub sem quaisquer alterações, avance para implementar um modelo a partir do GitHub.

## <a name="scenario"></a>Cenário

Neste cenário, vai:

* Crie um gateway de aplicação com firewall de aplicações web.
* Criar uma rede virtual denominada VirtualNetwork1 com um bloco CIDR reservado de 10.0.0.0/16.
* Criar uma sub-rede denominada Appgatewaysubnet que utiliza 10.0.0.0/28 como bloco CIDR.
* Configurar dois IPs de back-end configurados anteriormente para os servidores Web para os quais pretende balancear o tráfego. Neste exemplo de modelo, os IPs de back-end são 10.0.1.10 e 10.0.1.11.

> [!NOTE]
> Estas definições são os parâmetros para este modelo. Para personalizar o modelo, pode alterar as regras, o serviço de escuta, SSL e outras opções no ficheiro azuredeploy. JSON.

![Cenário](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Transferir e compreender o modelo Azure Resource Manager

Pode transferir o modelo Azure Resource Manager existente para criar uma rede virtual e duas sub-redes a partir do GitHub, efetuar quaisquer alterações que pretenda e reutilizá-lo. Para fazê-lo, siga os seguintes passos:

1. Navegue para [criar Gateway de aplicação com firewall de aplicações web ativado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-waf).
1. Clique em **azuredeploy.json** e em **RAW**.
1. Guarde o ficheiro numa pasta local do computador.
1. Se estiver familiarizado com os modelos Azure Resource Manager, avance para o passo 7.
1. Abra o ficheiro que guardou e observe o conteúdo em **parâmetros** na linha
1. Os parâmetros do modelo Azure Resource Manager fornecem um marcador de posição para valores que podem ser preenchidos durante a implementação.

  | Parâmetro | Descrição |
  | --- | --- |
  | **subnetPrefix** |Bloco CIDR para a sub-rede de gateway de aplicação. |
  | **applicationGatewaySize** | Tamanho do gateway de aplicação.  WAF só permite a médias e grandes. |
  | **backendIpaddress1** |Endereço IP do primeiro servidor web. |
  | **backendIpaddress2** |Endereço IP do segundo servidor web. |
  | **wafEnabled** | Definição para determinar se o WAF está ativada.|
  | **wafMode** | Modo de firewall de aplicações web.  Opções disponíveis são **prevenção** ou **deteção**.|
  | **wafRuleSetType** | Tipo de conjunto de regras da WAF.  Atualmente o OWASP é a única opção suportada. |
  | **wafRuleSetVersion** |Versão do conjunto de regras. OWASP CRS 2.2.9 e 3.0 estão, atualmente, as opções com suporte. |

1. Verifique o conteúdo em **recursos** e tenha em atenção as seguintes propriedades:

   * **tipo**. Tipo de recurso a ser criado pelo modelo. Neste caso, o tipo é `Microsoft.Network/applicationGateways`, que representa um gateway de aplicação.
   * **nome**. Nome do recurso. Observe o uso de `[parameters('applicationGatewayName')]`, que significa que o nome é fornecido como entrada por si ou por um ficheiro de parâmetros durante a implementação.
   * **propriedades**. Lista de propriedades do recurso. Este modelo utiliza a rede virtual e o endereço IP público durante a criação do gateway de aplicação.

1. Navegue de volta para [ https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf/ ](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf).
1. Clique em **azuredeploy-Parameters. JSON**e, em seguida, clique em **RAW**.
1. Guarde o ficheiro numa pasta local do computador.
1. Abra o ficheiro que guardou e edite os valores dos parâmetros. Utilize os seguintes valores para implementar o gateway de aplicação descrito no nosso cenário.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "addressPrefix": {
            "value": "10.0.0.0/16"
            },
            "subnetPrefix": {
            "value": "10.0.0.0/28"
            },
            "applicationGatewaySize": {
            "value": "WAF_Medium"
            },
            "capacity": {
            "value": 2
            },
            "backendIpAddress1": {
            "value": "10.0.1.10"
            },
            "backendIpAddress2": {
            "value": "10.0.1.11"
            },
            "wafEnabled": {
            "value": true
            },
            "wafMode": {
            "value": "Detection"
            },
            "wafRuleSetType": {
            "value": "OWASP"
            },
            "wafRuleSetVersion": {
            "value": "3.0"
            }
        }
    }
    ```

1. Guarde o ficheiro. Pode testar o modelo JSON e o modelo de parâmetros com as ferramentas de validação JSON online como [JSlint.com](https://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Implementar o modelo Azure Resource Manager com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se nunca tiver utilizado o Azure PowerShell, visite: [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) e siga as instruções para iniciar sessão no Azure e selecione a sua subscrição.

1. Início de sessão para o PowerShell

    ```powershell
    Connect-AzAccount
    ```

1. Verifique as subscrições da conta.

    ```powershell
    Get-AzSubscription
    ```

    Ser-lhe-á solicitado para autenticar com as suas credenciais.

1. Escolha qual das subscrições do Azure utilizar.

    ```powershell
    Select-AzSubscription -Subscriptionid "GUID of subscription"
    ```

1. Se necessário, crie um grupo de recursos com o cmdlet **New-AzureResourceGroup**. No exemplo seguinte, vai criar um grupo de recursos denominado AppgatewayRG na localização E.U.A. Leste.

    ```powershell
    New-AzResourceGroup -Name AppgatewayRG -Location "West US"
    ```

1. Executar o **New-AzResourceGroupDeployment** cmdlet para implementar a nova rede virtual com o modelo anterior e o parâmetro de ficheiros que transferiu e alterou.
    
    ```powershell
    New-AzResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Implementar o modelo Azure Resource Manager com a CLI do Azure

Para implementar o modelo Azure Resource Manager que transferiu com a CLI do Azure, siga os passos seguintes:

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and configure the Azure CLI (Instalar e configurar a CLI do Azure)](/cli/azure/install-azure-cli) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.

1. Se necessário, execute o `az group create` comando para criar um grupo de recursos, conforme mostrado no seguinte fragmento de código. Repare no resultado do comando. A lista apresentada depois do resultado explica os parâmetros utilizados.  Para obter mais informações sobre os grupos de recursos, veja [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    ```azurecli
    az group create --location westus --name appgatewayRG
    ```
    
    **-n (ou --name)**. Nome do novo grupo de recursos. Para o nosso cenário, o nome é *appgatewayRG*.
    
    **-l (ou --location)**. Região do Azure onde é criado o novo grupo de recursos. Para o nosso cenário, ele possui *westus*.

1. Execute o `az group deployment create` cmdlet para implementar a nova rede virtual com o modelo e o parâmetro de ficheiros que transferiu e alterou no passo anterior. A lista apresentada depois do resultado explica os parâmetros utilizados.

    ```azurecli
    az group deployment create --resource-group appgatewayRG --name TestAppgatewayDeployment --template-file azuredeploy.json --parameters @azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Implementar o modelo Azure Resource Manager com a função clicar para implementar

A função clicar para implementar é outra forma de utilizar os modelos Azure Resource Manager. É uma forma fácil de utilizar modelos com o Portal do Azure.

1. Aceda a [criar um gateway de aplicação com firewall de aplicações web](https://azure.microsoft.com/documentation/templates/101-application-gateway-waf/).

1. Clique em **Implementar no Azure**.

    ![Implementar no Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)
    
1. Preencha os parâmetros do modelo de implementação no portal e clique em **OK**.

    ![Parâmetros](./media/application-gateway-create-gateway-arm-template/ibiza1.png)
    
1. Selecione **concordo com os termos e condições indicados acima** e clique em **Compra**.

1. No painel Implementação personalizada, clique em **Criar**.

## <a name="providing-certificate-data-to-resource-manager-templates"></a>Fornecer dados de certificado para modelos do Resource Manager

Ao utilizar o SSL com um modelo, o certificado tem de ser fornecido numa cadeia base64 em vez de ser carregada. Para converter um. pfx ou. cer para uma cadeia base64 utilize um dos seguintes comandos. Os seguintes comandos converta o certificado para uma cadeia de base64, o que pode ser fornecida para o modelo. A saída esperada é uma cadeia de caracteres que pode ser armazenada numa variável e colada no modelo.

### <a name="macos"></a>macOS
```bash
cert=$( base64 <certificate path and name>.pfx )
echo $cert
```

### <a name="windows"></a>Windows
```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="delete-all-resources"></a>Eliminar todos os recursos

Para eliminar todos os recursos criados neste artigo, conclua um dos seguintes passos:

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzResourceGroup -Name appgatewayRG
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az group delete --name appgatewayRG
```

## <a name="next-steps"></a>Passos Seguintes

Se quiser configurar a descarga de SSL, visite: [Configurar um gateway de aplicação para descarga de SSL](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interno, visite: [Criar um gateway de aplicação com um balanceador de carga interno (ILB)](application-gateway-ilb.md).

Se quiser mais informações sobre as opções de balanceamento de carga em geral, visite:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestor de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

