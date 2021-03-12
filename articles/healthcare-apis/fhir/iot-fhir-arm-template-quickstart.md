---
title: 'Quickstart: Implementar o conector Azure IoT para FHIR (pré-visualização) utilizando um modelo ARM'
description: Neste arranque rápido, aprenda a implantar o Conector Azure IoT para FHIR (pré-visualização) utilizando um modelo de Gestor de Recursos Azure (modelo ARM).
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 01/21/2021
ms.openlocfilehash: b241484b8e8d981036fff4998d475b8b80fae42d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020577"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>Quickstart: Utilize um modelo Azure Resource Manager (ARM) para implantar o Conector Azure IoT para FHIR (pré-visualização)

Neste arranque rápido, você aprenderá a usar um modelo de Gestor de Recursos Azure (modelo ARM) para implementar o Conector Azure IoT para Recursos de Interoperabilidade de Cuidados rápidos de Saúde (FHIR&#174;)*, uma característica da API Azure para FHIR. Para implementar uma instância de trabalho do Azure IoT Connector para FHIR, este modelo também implementa um API Azure para o serviço FHIR e uma aplicação Azure IoT Central que exporta telemetria de um simulador de dispositivo para Azure IoT Connector para FHIR. Pode executar o modelo ARM para implantar o Conector Azure IoT para FHIR através do portal Azure, PowerShell ou CLI.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo abrir-se-á no portal Azure assim que iniciar seduca.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implementar para Azure um Conector Azure IoT para FHIR utilizando um modelo ARM no portal Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)
* Se quiser executar o código localmente, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)
* Se quiser executar o código localmente:
    * Uma concha bash (como Git Bash, que está incluída em [Git para Windows).](https://gitforwindows.org)
    * [Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Rever o modelo

O [modelo](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json) define os seguintes recursos Azure:

* **Microsoft.HealthcareApis/serviços**
* **Microsoft.HealthcareApis/serviços/iomtconnectors**
* **Microsoft.IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>Implementar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implementar o Conector Azure IoT para FHIR utilizando o modelo ARM no portal Azure:

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implementar para a Azure um Conector Azure IoT para o serviço FHIR utilizando o modelo ARM no portal Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

Na **API de Azure para** a página FHIR:

1. Se desejar, altere a **Subscrição** do padrão para uma subscrição diferente.

2. Para **o grupo de recursos**, selecione Criar **novo,** insira um nome para o novo grupo de recursos e selecione **OK**.

3. Se criou um novo grupo de recursos, selecione uma **Região** para o grupo de recursos.

4. Insira um nome para o seu novo Azure API para a instância FHIR em **Nome de Serviço FHIR**.

5. Escolha a **localização** para a sua AZure API para FHIR. A localização pode ser a mesma que ou diferente da região do grupo de recursos.

6. Forneça um nome para o seu Conector Azure IoT para a instância FHIR em **Iot Connector Name**.

7. Fornecer um nome para uma ligação criada dentro do Conector Azure IoT para FHIR em **Nome de Conexão**. Esta ligação é utilizada pela aplicação Azure IoT Central para empurrar a telemetria do dispositivo simulado para o Conector Azure IoT para FHIR.

8. Insira um nome para a sua nova aplicação Azure IoT Central em **Iot Nome Central**. Esta aplicação utilizará o modelo *de monitorização contínua do paciente* para simular um dispositivo.

9. Escolha a localização da sua aplicação IoT Central a partir do drop-down **IoT Central Location.** 

10. Selecione **Rever + criar**.

11. Leia os termos e condições e, em seguida, **selecione Criar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Se quiser executar os scripts PowerShell localmente, insira primeiro `Connect-AzAccount` para configurar as suas credenciais Azure.

Se o `Microsoft.HealthcareApis` fornecedor de recursos ainda não estiver registado para a sua subscrição, pode registá-lo com o seguinte código interativo. Para executar o código em Azure Cloud Shell, selecione **Experimente-o** no canto superior de qualquer bloco de código.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Se o `Microsoft.IoTCentral` fornecedor de recursos ainda não estiver registado para a sua subscrição, pode registá-lo com o seguinte código interativo. Para executar o código em Azure Cloud Shell, selecione **Experimente-o** no canto superior de qualquer bloco de código.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

Utilize o seguinte código para implantar o Conector Azure IoT para o serviço FHIR utilizando o modelo ARM.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Se o `Microsoft.HealthcareApis` fornecedor de recursos ainda não estiver registado para a sua subscrição, pode registá-lo com o seguinte código interativo. Para executar o código em Azure Cloud Shell, selecione **Experimente-o** no canto superior de qualquer bloco de código.

```azurecli-interactive
az extension add --name healthcareapis
```

Se o `Microsoft.IoTCentral` fornecedor de recursos ainda não estiver registado para a sua subscrição, pode registá-lo com o seguinte código interativo.

```azurecli-interactive
az extension add --name azure-iot
```

Utilize o seguinte código para implantar o Conector Azure IoT para o serviço FHIR utilizando o modelo ARM.

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> A implantação leva alguns minutos para ser concluída. Note os nomes para a AZure API para o serviço FHIR, aplicação Azure IoT Central, e o grupo de recursos, que você usa para rever os recursos implantados mais tarde.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estes passos para ver uma visão geral do seu novo AZure API para o serviço FHIR:

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Azure API para FHIR**.

2. Na lista FHIR, selecione o seu novo serviço. Aparece a **página geral** do novo serviço Azure API para o serviço FHIR.

3. Para validar que a nova conta FHIR API é a forada, selecione o link ao lado do **ponto final de metadados FHIR** para obter a declaração de capacidade da FHIR API. O link tem um formato de `https://<service-name>.azurehealthcareapis.com/metadata` . Se a conta for provisionada, é apresentado um grande ficheiro JSON.

4. Para validar que o novo Conector Azure IoT para FHIR está previsto, selecione o **Conector IoT (pré-visualização)** do menu de navegação à esquerda para abrir a página **IoT Connectors.** A página deve mostrar o conector Azure IoT provisionado para FHIR com valor *de estado* como *Online*, *As ligações* valorizam como *1*, e tanto o *mapeamento do dispositivo* como o ícone de *mapeamento FHIR* mostram *sucesso.*

5. No [portal Azure,](https://portal.azure.com)procure e selecione **aplicações centrais IoT**.

6. Na lista de Aplicações Centrais IoT, selecione o seu novo serviço. A **página geral** para a nova aplicação IoT Central aparece.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Execute o seguinte código interativo para ver detalhes sobre o seu Azure API para o serviço FHIR. Terá de introduzir o nome do novo serviço FHIR e do grupo de recursos.

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> O Conector Azure IoT para FHIR não fornece comandos PowerShell neste momento. Para validar o seu Conector Azure IoT para FHIR foi fornecido corretamente, utilize o processo de validação fornecido no separador **Portal.**

Execute o seguinte código interativo para ver detalhes sobre a sua aplicação Azure IoT Central. Terá de introduzir o nome da nova aplicação IoT Central e do grupo de recursos.

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[CLI](#tab/CLI)

Execute o seguinte código interativo para ver detalhes sobre o seu Azure API para o serviço FHIR. Terá de introduzir o nome do novo serviço FHIR e do grupo de recursos.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> O Conector Azure IoT para FHIR não fornece comandos CLI neste momento. Para validar o seu Conector Azure IoT para FHIR foi fornecido corretamente, utilize o processo de validação fornecido no separador **Portal.**

Execute o seguinte código interativo para ver detalhes sobre a sua aplicação Azure IoT Central. Terá de introduzir o nome da nova aplicação IoT Central e do grupo de recursos.

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Ligue os seus dados IoT ao Conector Azure IoT para FHIR (pré-visualização)
> [!IMPORTANT]
> O modelo de mapeamento do dispositivo fornecido neste guia foi concebido para trabalhar com a Data Export (legado) dentro da IoT Central.

A aplicação IoT Central não fornece atualmente o modelo ARM ou comandos PowerShell e CLI para definir a exportação de dados. Então, siga as instruções abaixo usando o portal Azure.  

Assim que tiver implantado a sua aplicação IoT Central, os seus dois dispositivos simulados fora da caixa começarão a gerar telemetria. Para este tutorial, vamos ingerir a telemetria do simulador *Smart Vitals Patch* em FHIR através do Conector Azure IoT para FHIR. Para exportar os seus dados IoT para o Conector Azure IoT para FHIR, vamos querer [criar uma exportação de dados (legado) dentro da IoT Central](../../iot-central/core/howto-export-data-legacy.md). Na página data export (legado):
- Escolha *a Azure Event Hubs* como destino de exportação.
- Selecione Utilize um valor *de cadeia de ligação* para o campo de espaço de nome de **Centros de Evento.**
- Forneça o Conector Azure IoT para a cadeia de ligação do FHIR obtida num passo anterior para o campo **de cordas de ligação.**
- Mantenha a opção **de telemetria** *no* campo **dados para exportação.**

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Ver dados do dispositivo na Azure API para FHIR

Pode ver os recursos de observação baseados em FHIR criados pelo Azure IoT Connector para FHIR no seu servidor FHIR utilizando o Carteiro. Crie o seu [Carteiro para aceder à Azure API para FHIR](access-fhir-postman-tutorial.md) e faça um `GET` pedido para `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` visualizar os recursos de FHIR de observação com valor de frequência cardíaca.

> [!TIP]
> Certifique-se de que o seu utilizador tem acesso adequado à Azure API para o plano de dados FHIR. Utilize [o controlo de acesso baseado em funções Azure (Azure RBAC)](configure-azure-rbac.md) para atribuir as funções necessárias do plano de dados.

---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, que elimina os recursos do grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com)procure e selecione **grupos de Recursos.**

2. Na lista de grupos de recursos, escolha o nome do seu grupo de recursos.

3. Na página **geral** do seu grupo de recursos, selecione **Delete resource group**.

4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Delete**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

Para um tutorial passo a passo que o guia através do processo de criação de um modelo ARM, consulte o [tutorial para criar e implementar o seu primeiro modelo ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

## <a name="next-steps"></a>Passos seguintes

Neste guia de arranque rápido, implementou o Conector Azure IoT para a funcionalidade FHIR no seu API Azure para recurso FHIR. Selecione a partir dos próximos passos para saber mais sobre o Conector Azure IoT para FHIR:

Compreenda diferentes fases do fluxo de dados dentro do Conector Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Conector Azure IoT para fluxo de dados FHIR](iot-data-flow.md)

Aprenda a configurar o Conector IoT utilizando modelos de mapeamento de dispositivo e FHIR.

>[!div class="nextstepaction"]
>[Conector Azure IoT para modelos de mapeamento FHIR](iot-mapping-templates.md)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização). FHIR é uma marca registada do HL7 e é usada com a permissão do HL7.
