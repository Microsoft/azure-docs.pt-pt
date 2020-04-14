---
title: Use identidade gerida para autenticar o seu trabalho de Azure Stream Analytics para a saída de Power BI
description: Este artigo descreve como usar identidades geridas para autenticar o seu trabalho de Azure Stream Analytics à saída do Power BI.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 31a5195038ef25acadc08e2acbedf8471b25833c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261419"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Use identidade gerida para autenticar o seu trabalho de Analytics Azure Stream para Power BI

[A autenticação de identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para saída para Power BI dá aos trabalhos stream Analytics acesso direto a um espaço de trabalho dentro da sua conta Power BI. Esta funcionalidade permite que as implementações de trabalhos stream analytics sejam totalmente automatizadas, uma vez que já não é necessário que um utilizador inicie sessão interativamente no Power BI através do portal Azure. Além disso, os trabalhos de longa duração que escrevem para o Power BI são agora mais bem apoiados, uma vez que não precisará de reautorizar periodicamente o trabalho.

Este artigo mostra-lhe como ativar a Identidade Gerida para a saída(s) do Power BI de um trabalho de Stream Analytics através do portal Azure e através de uma implementação do Gestor de Recursos Azure.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes para a utilização desta funcionalidade:

- Uma conta Power BI com uma [licença Pro.](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)

- Um espaço de trabalho atualizado dentro da sua conta Power BI. Consulte o anúncio desta funcionalidade por [parte do Power BI](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) para mais detalhes.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Crie um trabalho de Stream Analytics usando o portal Azure

1. Crie um novo trabalho stream analytics ou abra um emprego existente no portal Azure. A partir da barra de menu seletiva do lado esquerdo do ecrã, selecione **Identidade Gerida** localizada em **Configuração**. Certifique-se de que "Use a Identidade Gerida atribuída pelo sistema" e, em seguida, selecione o botão **Guardar** na parte inferior do ecrã.

   ![Identidade gerida por Configure Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Antes de configurar a saída, dê ao trabalho do Stream Analytics acesso ao seu espaço de trabalho Power BI seguindo as instruções na [secção de trabalho Do Stream Analytics para a sua](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) secção de espaço de trabalho Power BI deste artigo.

3. Navegue para a secção **de saídas** do trabalho da Stream Analytic, selecione **+ Adicionar,** e depois escolha **Power BI**. Em seguida, selecione o botão **Autorizar** e inicie sessão com a sua conta Power BI.

   ![Autorizar com conta Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Uma vez autorizado, uma lista de dropdown será povoada com todos os espaços de trabalho a que você tem acesso. Selecione o espaço de trabalho que autorizou no passo anterior. Em seguida, selecione **Identidade Gerida** como o "modo de autenticação". Por fim, selecione o botão **Guardar.**

   ![Configure a saída de Power BI com identidade gerida](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Implementação do Azure Resource Manager

O Gestor de Recursos Azure permite-lhe automatizar totalmente a implementação do seu trabalho em Stream Analytics. Pode implementar modelos de Gestor de Recursos utilizando o Azure PowerShell ou o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Os exemplos abaixo utilizam o Azure CLI.


1. Pode criar um recurso **Microsoft.StreamAnalytics/streamingjobs** com uma Identidade Gerida, incluindo a seguinte propriedade na secção de recursos do seu modelo de Gestor de Recursos:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Esta propriedade diz ao Azure Resource Manager para criar e gerir a identidade para o seu trabalho de Stream Analytics. Abaixo está um modelo de Gestor de Recursos de exemplo que implementa um trabalho de Stream Analytics com identidade gerida ativada e um lavatório de saída power BI que usa identidade gerida:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Implemente o trabalho acima para o grupo de recursos **ExemploGroup** utilizando o comando CLI do Azure abaixo:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Após a criação do trabalho, utilize o Gestor de Recursos Azure para recuperar a definição completa do trabalho.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    O comando acima devolverá uma resposta como a seguinte:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Se planeia utilizar a API REST REST do Power BI para adicionar o trabalho de Stream Analytics ao seu espaço de trabalho Power BI, tome nota do "principado" devolvido.

3. Agora que o trabalho é criado, continue a [dar ao stream analytics acesso ao seu espaço](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) de trabalho Power BI deste artigo.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Dê ao stream Analytics acesso ao seu espaço de trabalho Power BI

Agora que o trabalho do Stream Analytics foi criado, pode ter acesso a um espaço de trabalho power BI.

### <a name="use-the-power-bi-ui"></a>Use o Power BI UI

   > [!Note]
   > Para adicionar o trabalho stream Analytics ao seu espaço de trabalho Power BI utilizando o UI, também tem de ativar o acesso principal do serviço nas **definições** do Developer no portal de administração Power BI. Ver [Começar com um diretor](https://docs.microsoft.com/power-bi/developer/embed-service-principal) de serviço para mais detalhes.

1. Navegue para as definições de acesso do espaço de trabalho. Consulte este artigo para mais detalhes: [Dê acesso ao seu espaço de trabalho.](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)

2. Digite o nome do seu trabalho stream analytics na caixa de texto e selecione **Contributor** como nível de acesso.

3. Selecione **Adicionar** e fechar o painel.

   ![Adicione trabalho de Stream Analytics ao espaço de trabalho power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Utilize os cmdlets Power BI PowerShell

1. Instale os `MicrosoftPowerBIMgmt` cmdlets Power BI PowerShell.

   > [!Important]
   > Certifique-se de que está a utilizar a versão 1.0.821 ou mais tarde dos cmdlets.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Inicie sessão no Power BI.

```powershell
Login-PowerBI
```

3. Adicione o seu trabalho de Stream Analytics como Colaborador ao espaço de trabalho.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Use a API POWER BI REST

O trabalho stream Analytics também pode ser adicionado como Contribuinte para o espaço de trabalho utilizando diretamente a API REST "Add Group User". Documentação completa para esta API pode ser encontrada aqui: [Grupos - Adicionar Utilizador de Grupo](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser).

**Pedido de Amostra**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Corpo do Pedido
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>Limitações
Abaixo estão as limitações desta funcionalidade:

- Os espaços de trabalho clássicos do Power BI não são suportados.

- Contas Azure sem Diretório Ativo Azure.

- O acesso a vários inquilinos não é suportado. O diretor de serviço criado para um determinado trabalho stream analytics deve residir no mesmo inquilino azure Ative Directory em que o trabalho foi criado, e não pode ser usado com um recurso que reside em um inquilino azure Ative Directory diferente.

- [A Identidade Atribuída](../active-directory/managed-identities-azure-resources/overview.md) ao Utilizador não é suportada. Isto significa que não pode entrar no seu próprio serviço principal para ser usado pelo seu trabalho de Stream Analytics. O diretor de serviço deve ser gerado pelo Azure Stream Analytics.

## <a name="next-steps"></a>Passos seguintes

* [Integração do painel power BI com o Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Compreender as saídas do Azure Stream Analytics](./stream-analytics-define-outputs.md)
