---
title: Configurar identidades geridas em piscinas de Lote
description: Saiba como ativar identidades geridas atribuídas pelo utilizador em piscinas de Batch e como utilizar identidades geridas dentro dos nós.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: 7fab213ac1545c0bff9b74bc46504717b6038e8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950166"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Configurar identidades geridas em piscinas de Lote

[Identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) eliminam a necessidade de os desenvolvedores terem de gerir credenciais fornecendo uma identidade para o recurso Azure Ative (Azure AD) e usando-o para obter fichas do Azure Ative Directory (Azure AD).

Este tópico explica como permitir identidades geridas atribuídas pelo utilizador em piscinas de Batch e como usar identidades geridas dentro dos nós.

> [!IMPORTANT]
> O suporte para piscinas Azure Batch com identidades geridas atribuídas pelo utilizador está atualmente em pré-visualização pública para as seguintes regiões: West US 2, South Central US, East US, US Gov Arizona e US Gov Virginia.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo utilizador

Em primeiro lugar, [crie a sua identidade gerida atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) no mesmo inquilino que a sua conta Batch. Esta identidade gerida não precisa de estar no mesmo grupo de recursos ou mesmo na mesma subscrição.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Criar um pool de lote com identidades geridas atribuídas pelo utilizador

Depois de criar uma ou mais identidades geridas atribuídas pelo utilizador, pode criar um pool de Lote com essa identidade gerida utilizando a [biblioteca de gestão Batch .NET](/dotnet/api/overview/azure/batch#management-library).

> [!IMPORTANT]
> As piscinas devem ser configuradas utilizando a [configuração da máquina virtual](nodes-and-pools.md#virtual-machine-configuration) para utilizar identidades geridas.

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        };
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

> [!NOTE]
> A criação de piscinas com identidades geridas não é atualmente suportada com a biblioteca de [clientes Batch .NET.](/dotnet/api/overview/azure/batch#client-library)

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Utilize identidades geridas atribuídas pelo utilizador em nóns de lote

Depois de criar as suas piscinas, as suas identidades geridas atribuídas ao utilizador podem aceder aos nós da piscina através de Secure Shell (SSH) ou Remote Desktop (RDP). Também pode configurar as suas tarefas para que as identidades geridas possam aceder diretamente aos [recursos da Azure que suportem identidades geridas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

Dentro dos nós batch, pode obter fichas de identidade geridas e usá-las para autenticar através da autenticação AZure AD através do [Serviço de Metadados Azure Instance](../virtual-machines/windows/instance-metadata-service.md).

Para o Windows, o script PowerShell para obter um token de acesso para autenticar é:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Para Linux, o guião bash é:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

Para obter mais informações, consulte [Como utilizar identidades geridas para recursos Azure num VM Azure para adquirir um token de acesso](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [identidades geridas para recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md)
- Saiba como utilizar [as chaves geridas pelo cliente com identidades geridas pelo utilizador](batch-customer-managed-key.md).
- Saiba como [ativar](automatic-certificate-rotation.md)a rotação automática do certificado numa piscina de Lote .
