---
title: Tutorial `:` Utilize uma identidade gerida para aceder a Azure Cosmos DB - Linux - Azure AD
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo sistema numa VM do Linux, para aceder ao Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1ece1fbd75c975f549cb9096149c2a2d562dec6
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107571"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Tutorial: Utilizar uma identidade gerida atribuída pelo sistema numa VM do Linux, para aceder ao Azure Cosmos DB 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como utilizar a identidade gerida atribuída pelo sistema para uma máquina virtual (VM) do Linux para aceder ao Azure Cosmos DB. Saiba como:

> [!div class="checklist"]
> * Criar uma conta do Cosmos DB
> * Criar uma coleção na conta do Cosmos DB
> * Conceder acesso de identidade gerida atribuída pelo sistema a uma instância do Azure Cosmos DB
> * Obter o `principalID` da identidade gerida atribuída pelo sistema da VM do Linux
> * Obter um token de acesso e utilizá-lo para chamar o Azure Resource Manager
> * Obter chaves de acesso do Azure Resource Manager para fazer chamadas do Cosmos DB

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com a funcionalidade das identidades geridas para os recursos do Azure, veja esta [descrição geral](overview.md). 
- Se não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar a criação de recursos e gestão de funções, a sua conta precisa de permissões de "Proprietário" no âmbito adequado (a sua subscrição ou grupo de recursos). Se precisar de assistência com a atribuição de função, veja [Utilizar Controlo de Acesso Baseado em Funções para gerir o acesso aos recursos da sua subscrição do Azure](../../role-based-access-control/role-assignments-portal.md).
- Para executar os scripts de exemplo, tem duas opções:
    - Utilize o [Azure Cloud Shell,](../../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Executar scripts localmente instalando a versão mais recente do [Azure CLI,](/cli/azure/install-azure-cli)em seguida, inicie sessão no Azure usando [login az](/cli/azure/reference-index#az-login). Utilize uma conta associada à subscrição do Azure na qual pretende criar recursos.

## <a name="create-a-cosmos-db-account"></a>Criar uma conta do Cosmos DB 

Se ainda não tiver uma, crie uma conta do Cosmos DB. Pode ignorar este passo e utilizar uma conta do Cosmos DB existente. 

1. Clique no botão **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Clique em **Bases de Dados** e, em seguida, **Azure Cosmos DB**, e um painel “Nova conta” é apresentado.
3. Introduza um **ID** para a conta do Cosmos DB, que irá utilizar mais tarde.  
4. A **API** deve ser definida como "SQL". A abordagem descrita neste tutorial pode ser utilizada com os outros tipos de API disponíveis, mas os passos neste tutorial são para a API de SQL.
5. Certifique-se de que a **Subscrição** e o **Grupo de Recursos** correspondem aos perfis que especificou quando criou a VM no passo anterior.  Selecione uma **Localização** em que o Cosmos DB esteja disponível.
6. Clique em **Criar**.

### <a name="create-a-collection-in-the-cosmos-db-account"></a>Criar uma coleção na conta do Cosmos DB

Em seguida, adicione uma coleção de dados à conta do Cosmos DB, que possa consultar em passos posteriores.

1. Navegue até à sua conta do Cosmos DB recentemente criada.
2. No separador **Descrição Geral**, clique no botão **+/Adicionar Coleção**, e um painel “Adicionar Coleção” surge.
3. Dê à a coleção um ID de base de dados, um ID de coleção, selecione uma capacidade de armazenamento, introduza uma chave de partição, introduza um valor de débito e clique em **OK**.  Neste tutorial, é suficiente utilizar a opção "Teste" como o ID de base de dados e o ID de coleção, selecionar uma capacidade de armazenamento fixa e o débito mais baixo (400 RU/s).  

## <a name="grant-access"></a>Conceder acesso

Para obter acesso às chaves de acesso à conta do Cosmos DB do Resource Manager na secção seguinte, terá de obter o `principalID` da identidade gerida atribuída pelo sistema da VM do Linux.  Certifique-se de que substitui o `<SUBSCRIPTION ID>` grupo `<RESOURCE GROUP>` de recursos (grupo de recursos em que reside o seu VM) e `<VM NAME>` os valores dos parâmetros pelos seus próprios valores.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
A resposta inclui os detalhes da identidade gerida atribuída pelo sistema (anote o principalID, uma vez que é utilizado na próxima secção):

```output  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }
```

### <a name="grant-your-linux-vms-system-assigned-identity-access-to-the-cosmos-db-account-access-keys"></a>Conceder acesso à identidade gerida atribuída pelo sistema da VM do Linux às chaves de acesso da conta do Cosmos DB

O Cosmos DB não suporta nativamente a autenticação do Azure AD. No entanto, pode utilizar uma identidade gerida para obter uma chave de acesso do Cosmos DB do Resource Manager e, em seguida, utilizar a chave para aceder ao Cosmos DB. Neste passo, pode conceder o acesso da identidade gerida atribuída pelo sistema às chaves da conta do Cosmos DB.

Para conceder o acesso de identidade gerida atribuída pelo sistema à conta do Cosmos DB no Azure Resource Manager com a CLI do Azure, atualize os valores de `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<COSMOS DB ACCOUNT NAME>` para o seu ambiente. `<MI PRINCIPALID>`Substitua-a `principalId` pela propriedade devolvida pelo comando em Recuperar o `az resource show` principalID do MI do Linux VM.  O Cosmos DB suporta dois níveis de granularidade ao utilizar chaves de acesso: acesso de leitura/escrita à conta e acesso só de leitura à conta.  Atribua a função `DocumentDB Account Contributor` se pretender obter as chaves de leitura/escrita para a conta, ou atribua a função `Cosmos DB Account Reader Role` se quiser obter só as chaves de leitura para a conta:

```azurecli-interactive
az role assignment create --assignee <MI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

A resposta inclui os detalhes da atribuição de funções que criou:

```output
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="access-data"></a>Aceder a dados

Para o resto do tutorial, trabalhe a partir da máquina virtual.

Para concluir estes passos, precisa de um cliente SSH. Se estiver a utilizar o Windows, pode utilizar o cliente SSH no [Subsistema Windows para Linux](/windows/wsl/install-win10). Se precisar de ajuda para configurar as chaves do seu cliente SSH, veja [Como utilizar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md) ou [Como criar e utilizar um par de chaves SSH públicas e privadas para VMs do Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal do Azure, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Linux e, em seguida, na página **Descrição Geral**, clique em **Ligar** na parte superior. Copie a cadeia de ligação para ligar à sua VM. 
2. Ligue à VM através do seu cliente SSH.  
3. Em seguida, é-lhe pedido que introduza a **Palavra-passe** que adicionou ao criar a **VM do Linux**. Deverá iniciar sessão com êxito.  
4. Utilize o CURL para obter um token de acesso para o Azure Resource Manager: 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > No pedido anterior, o valor do parâmetro "recurso" tem de ser uma correspondência exata para o que é esperado pelo Azure AD. Ao utilizar o ID de recurso do Azure Resource Manager, tem de incluir a barra à direita no URI.
    > Na resposta seguinte, o elemento access_token foi abreviado para não ser tão extenso.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
### <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Obter chaves de acesso do Azure Resource Manager para fazer chamadas do Cosmos DB  

Agora, utilize o CURL para chamar o Resource Manager com o token de acesso que obteve na secção anterior para obter a chave de acesso à conta do Cosmos DB. Assim que tivermos a chave de acesso, podemos fazer consultas no Cosmos DB. Certifique-se de que substitui os valores de parâmetros `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<COSMOS DB ACCOUNT NAME>` pelos seus próprios valores. Substitua o valor `<ACCESS TOKEN>` pelo o token de acesso que obteve anteriormente.  Se pretender obter chaves de leitura/escrita, utilize o tipo de operação de chave `listKeys`.  Se pretender obter chaves só de leitura, utilize o tipo de operação de chave `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> O texto no URL anterior é sensível a casos, por isso utilize o caso que corresponda ao caso utilizado em nome do seu grupo de recursos. Além disso, é importante saber que se trata de um pedido POST e não de um pedido GET, e certifique-se de que passa um valor para capturar um limite de comprimento com -d que possa ser NULL.  

A resposta CURL dá-lhe a lista de chaves.  Por exemplo, se obtiver as chaves só de leitura:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Agora que tem a chave de acesso para a conta do Cosmos DB, pode passá-la a um SDK do Cosmos DB e fazer chamadas para aceder à conta.  Para obter um rápido exemplo, pode passar a chave de acesso à CLI do Azure.  Pode obter o `<COSMOS DB CONNECTION URL>` no separador **Descrição Geral** no painel da conta do Cosmos DB no portal do Azure.  Substitua o `<ACCESS KEY>` pelo valor que obteve acima:

```azurecli-interactive
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Este comando da CLI devolve os detalhes da coleção:

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar uma identidade gerida atribuída pelo sistema numa máquina virtual do Linux para aceder ao Cosmos DB.  Para saber mais sobre o Cosmos DB, veja:

> [!div class="nextstepaction"]
>[Visão geral do Azure Cosmos DB](../../cosmos-db/introduction.md)
