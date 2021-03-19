---
title: Utilize identidade gerida para autenticar o seu trabalho Azure Stream Analytics para a produção de Power BI
description: Este artigo descreve como usar identidades geridas para autenticar o seu trabalho Azure Stream Analytics para a produção de Power BI.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: how-to
ms.date: 3/10/2020
ms.openlocfilehash: 7c1ddbbbd8198cf769e89cfa824de370184a992c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589689"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Utilize identidade gerida para autenticar o seu trabalho Azure Stream Analytics para Power BI

[A autenticação de identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para a saída para o Power BI dá aos empregos stream Analytics acesso direto a um espaço de trabalho dentro da sua conta Power BI. Esta funcionalidade permite que as implementações de trabalhos stream Analytics sejam totalmente automatizadas, uma vez que já não é necessário que um utilizador faça login interativamente no Power BI através do portal Azure. Além disso, os trabalhos de longa duração que escrevem para o Power BI estão agora mais bem apoiados, uma vez que não precisará de reautorizar periodicamente o trabalho.

Este artigo mostra-lhe como ativar a Identidade Gerida para a(s) saída(s) Power BI(s) de um trabalho stream Analytics através do portal Azure e através de uma implementação do Azure Resource Manager.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes para a utilização desta função:

- Uma conta Power BI com [uma licença Pro](/power-bi/service-admin-purchasing-power-bi-pro).

- Um espaço de trabalho melhorado na sua conta Power BI. Consulte [o anúncio da Power BI](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) desta funcionalidade para mais detalhes.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Crie um trabalho stream analytics usando o portal Azure

1. Crie um novo trabalho stream Analytics ou abra um trabalho existente no portal Azure. A partir da barra de menu localizada no lado esquerdo do ecrã, selecione **Identidade Gerida** localizada em **Configuração**. Certifique-se de que é selecionado "Use System-assigned Managed Identity" e, em seguida, selecione o botão **Guardar** na parte inferior do ecrã.

   ![Configurar a identidade gerida stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Antes de configurar a saída, dê ao stream Analytics acesso ao seu espaço de trabalho Power BI seguindo as instruções do [trabalho Dor the Stream Analytics para a sua](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) secção de espaço de trabalho Power BI deste artigo.

3. Navegue para a secção **saídas** do trabalho da analítica stream, selecione **+ Adicionar**, e, em seguida, escolha **Power BI**. Em seguida, selecione o botão **'Autorizor'** e faça login com a sua conta Power BI.

   ![Autorizar com conta Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Uma vez autorizado, uma lista de dropdown será povoada com todos os espaços de trabalho a que você tem acesso. Selecione o espaço de trabalho que autorizou no passo anterior. Em seguida, **selecione Identidade Gerida** como "Modo de Autenticação". Por fim, selecione o botão **Guardar.**

   ![Configurar a produção de POWER BI com Identidade Gerida](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Implementação do Azure Resource Manager

O Azure Resource Manager permite-lhe automatizar totalmente a implementação do seu trabalho stream Analytics. Pode implementar modelos de Gestor de Recursos utilizando a Azure PowerShell ou o [Azure CLI](/cli/azure/). Os exemplos abaixo utilizam o Azure CLI.


1. Pode criar um recurso **Microsoft.StreamAnalytics/streamingjobs** com uma Identidade Gerida, incluindo a seguinte propriedade na secção de recursos do seu modelo de Gestor de Recursos:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Esta propriedade diz ao Azure Resource Manager para criar e gerir a identidade para o seu trabalho stream Analytics. Abaixo está um modelo de gestor de recursos de exemplo que implementa um trabalho de Stream Analytics com identidade gerida ativada e um lavatório de saída de Power BI que utiliza identidade gerida:

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

    Implementar o trabalho acima no grupo de recursos **ExemploGroup** utilizando o comando Azure CLI abaixo:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
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

    Se planeia utilizar a API REST do Power BI para adicionar o trabalho stream Analytics ao seu espaço de trabalho Power BI, tome nota do "principalId" devolvido.

3. Agora que o trabalho é criado, continue para o [trabalho Do Stream Analytics acesso à sua](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) secção de espaço de trabalho Power BI deste artigo.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Dê ao stream Analytics acesso ao seu espaço de trabalho Power BI

Agora que o trabalho stream Analytics foi criado, pode ser dado acesso a um espaço de trabalho Power BI. Depois de ter dado acesso ao seu trabalho, permita que a identidade se propague.

### <a name="use-the-power-bi-ui"></a>Use o Power BI UI

   > [!Note]
   > Para adicionar o trabalho stream Analytics ao seu espaço de trabalho Power BI utilizando o UI, também tem de permitir o acesso principal do serviço nas **definições do Desenvolvedor** no portal de administração Power BI. Consulte [Começar com um diretor de serviço](/power-bi/developer/embed-service-principal) para mais detalhes.

1. Navegue para as definições de acesso do espaço de trabalho. Consulte este artigo para mais detalhes: [Dê acesso ao seu espaço de trabalho.](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)

2. Digite o nome do seu trabalho stream Analytics na caixa de texto e selecione **o Colaborador** como o nível de acesso.

3. **Selecione Adicione** e feche o painel.

   ![Adicione o trabalho de Stream Analytics ao espaço de trabalho Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Utilize os cmdlets PowerShell PowerShell powershell

1. Instale os `MicrosoftPowerBIMgmt` cmdlets Power BI PowerShell.

   > [!Important]
   > Certifique-se de que está a utilizar a versão 1.0.821 ou posterior dos cmdlets.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Faça login no Power BI.

```powershell
Login-PowerBI
```

3. Adicione o seu trabalho stream Analytics como contribuinte para o espaço de trabalho.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Utilize a Power BI REST API

O trabalho stream Analytics também pode ser adicionado como contribuinte para o espaço de trabalho, utilizando diretamente a API REST "Add Group User". A documentação completa para esta API pode ser consultada aqui: [Grupos - Adicionar Utilizador do Grupo](/rest/api/power-bi/groups/addgroupuser).

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

## <a name="remove-managed-identity"></a>Remover Identidade Gerida

A Identidade Gerida criada para um trabalho stream Analytics só é eliminada quando o trabalho é eliminado. Não há como apagar a Identidade Gerida sem apagar o trabalho. Se já não pretender utilizar a Identidade Gerida, pode alterar o método de autenticação para a saída. A Identidade Gerida continuará a existir até que o trabalho seja eliminado, e será usado se decidir utilizar novamente a autenticação de Identidade Gerida.

## <a name="limitations"></a>Limitações
Abaixo estão as limitações desta característica:

- Os espaços de trabalho clássicos do Power BI não são suportados.

- Contas Azure sem Diretório Ativo Azure.

- O acesso a vários inquilinos não é suportado. O diretor de serviço criado para um determinado trabalho stream Analytics deve residir no mesmo inquilino do Azure Ative Directory em que o trabalho foi criado, e não pode ser usado com um recurso que reside num inquilino azure ative inquilina.

- [A Identidade Atribuída ao Utilizador](../active-directory/managed-identities-azure-resources/overview.md) não é suportada. Isto significa que não pode entrar no seu próprio principal de serviço para ser usado pelo seu trabalho stream Analytics. O diretor de serviço deve ser gerado pela Azure Stream Analytics.

## <a name="next-steps"></a>Passos seguintes

* [Integração do painel power BI com Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Compreender as saídas do Azure Stream Analytics](./stream-analytics-define-outputs.md)
