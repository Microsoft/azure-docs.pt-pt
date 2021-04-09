---
title: Ativar a rotação automática do certificado numa piscina de lote
description: Pode criar um pool de Lote com identidade gerida e um certificado que será automaticamente renovado.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962574"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Ativar a rotação automática do certificado numa piscina de lote

 Pode criar um pool de Lote com um certificado que será automaticamente renovado. Para tal, a sua piscina tem de ser criada com uma [identidade gerida atribuída ao utilizador](managed-identity-pools.md) que terá acesso ao certificado no Cofre da Chave [Azure.](../key-vault/general/overview.md)

> [!IMPORTANT]
> O suporte para piscinas Azure Batch com identidades geridas atribuídas pelo utilizador está atualmente em pré-visualização pública para as seguintes regiões: West US 2, South Central US, East US, US Gov Arizona e US Gov Virginia.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo utilizador

Em primeiro lugar, [crie a sua identidade gerida atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) no mesmo inquilino que a sua conta Batch. Esta identidade gerida não precisa de estar no mesmo grupo de recursos ou mesmo na mesma subscrição.

Certifique-se de que regista a **identificação** do Cliente da identidade gerida atribuída ao utilizador. Vai precisar deste valor mais tarde.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Screenshot mostrando o ID do cliente de uma identidade gerida atribuída pelo utilizador no portal Azure.":::

## <a name="create-your-certificate"></a>Crie o seu certificado

Em seguida, terá de criar um certificado e adicioná-lo ao Cofre da Chave Azure. Se ainda não criou um cofre, terá de o fazer primeiro. Para obter instruções, consulte [Quickstart: set and reconfessione um certificado do Azure Key Vault utilizando o portal Azure](../key-vault/certificates/quick-create-portal.md).

Ao criar o seu certificado, certifique-se de definir **o Tipo de Ação vitalícia** para renovar automaticamente e especificar o número de dias após o qual o certificado deve renovar.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Screenshot do ecrã de criação de certificado no portal Azure.":::

Após a criação do seu certificado, tome nota do seu **Identificador Secreto.** Vai precisar deste valor mais tarde.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="Imagem mostrando o identificador secreto de um certificado.":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Adicione uma política de acesso no Cofre da Chave Azure

No seu cofre chave, atribua uma política de acesso ao Cofre de Chaves que permite à sua identidade gerida atribuída ao utilizador aceder a segredos e certificados. Para obter instruções detalhadas, consulte [atribuir uma política de acesso ao cofre de chaves utilizando o portal Azure](../key-vault/general/assign-access-policy-portal.md).

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>Criar um pool de Lote com uma identidade gerida atribuída ao utilizador

Crie um pool de lote com a sua identidade gerida utilizando a [biblioteca de gestão Batch .NET.](/dotnet/api/overview/azure/batch#management-library) Para obter mais informações, consulte [as identidades geridas configure nas piscinas de Batch](managed-identity-pools.md).

O exemplo a seguir utiliza a API de Gestão de Lotes para criar uma piscina. Certifique-se de que utiliza o **Identificador Secreto** do seu certificado `observedCertificates` e o **ID** do cliente da sua identidade `msiClientId` gerida, substituindo os dados de exemplo abaixo.

URI da API REST

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

Corpo do Pedido

```json
{
    "name": "test2",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "KVExtensions",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>Validar o certificado

Para confirmar que o certificado foi implantado com sucesso, faça login no nó de computação. Deverá ver um resultado semelhante ao seguinte:

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [identidades geridas para recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md)
- Saiba como utilizar [as chaves geridas pelo cliente com identidades geridas pelo utilizador](batch-customer-managed-key.md).
