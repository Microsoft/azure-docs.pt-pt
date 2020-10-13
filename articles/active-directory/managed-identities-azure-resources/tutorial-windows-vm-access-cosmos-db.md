---
title: 'Tutorial: Use uma identidade gerida para aceder a Azure Cosmos DB - Windows - Azure AD'
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo sistema numa VM do Windows, para aceder ao Azure Cosmos DB.
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
ms.date: 09/29/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b571b2b8e0d334a02631e3f443ec54398117ee9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532674"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Tutorial: Utilizar uma identidade gerida atribuída pelo sistema numa VM do Windows, para aceder ao Azure Cosmos DB

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como utilizar a identidade gerida atribuída pelo sistema para uma máquina virtual (VM) do Windows para aceder ao Cosmos DB. Saiba como:

> [!div class="checklist"]
> * Criar uma conta do Cosmos DB
> * Conceder acesso da identidade gerida atribuída pelo sistema de uma VM do Windows às chaves de acesso da conta do Cosmos DB
> * Obter um token de acesso com a identidade gerida atribuída pelo sistema da VM do Windows para chamar o Azure Resource Manager
> * Obter chaves de acesso do Azure Resource Manager para fazer chamadas do Cosmos DB

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Instale a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps)


## <a name="enable"></a>Ativar

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Conceder acesso


### <a name="create-a-cosmos-db-account"></a>Criar uma conta do Cosmos DB 

Se ainda não tiver uma, crie uma conta do Cosmos DB. Pode ignorar este passo e utilizar uma conta do Cosmos DB existente. 

1. Clique no botão **+/Criar novo serviço**, no canto superior esquerdo do portal do Azure.
2. Clique em **Bases de Dados** e, em seguida, **Azure Cosmos DB**, e um painel “Nova conta” é apresentado.
3. Introduza um **ID** para a conta do Cosmos DB, que irá utilizar mais tarde.  
4. A **API** deve ser definida como "SQL". A abordagem descrita neste tutorial pode ser utilizada com os outros tipos de API disponíveis, mas os passos neste tutorial são para a API de SQL.
5. Certifique-se de que a **Subscrição** e o **Grupo de Recursos** correspondem aos perfis que especificou quando criou a VM no passo anterior.  Selecione uma **Localização** em que o Cosmos DB esteja disponível.
6. Clique em **Criar**.

### <a name="create-a-collection"></a>Criar uma coleção 

Em seguida, adicione uma coleção de dados à conta do Cosmos DB, que possa consultar em passos posteriores.

1. Navegue até à sua conta do Cosmos DB recentemente criada.
2. No separador **Descrição Geral**, clique no botão **+/Adicionar Coleção**, e um painel “Adicionar Coleção” surge.
3. Dê à a coleção um ID de base de dados, um ID de coleção, selecione uma capacidade de armazenamento, introduza uma chave de partição, introduza um valor de débito e clique em **OK**.  Neste tutorial, é suficiente utilizar a opção "Teste" como o ID de base de dados e o ID de coleção, selecionar uma capacidade de armazenamento fixa e o débito mais baixo (400 RU/s).  


### <a name="grant-access-to-the-cosmos-db-account-access-keys"></a>Conceder acesso às chaves de acesso à conta Cosmos DB

Esta secção mostra como conceder acesso de identidade gerido ao sistema Windows VM às chaves de acesso à conta Cosmos DB. O Cosmos DB não suporta nativamente a autenticação do Azure AD. No entanto, pode utilizar uma identidade gerida atribuída pelo sistema para obter uma chave de acesso do Cosmos DB do Resource Manager e, em seguida, utilizar a chave para aceder ao Cosmos DB. Neste passo, pode conceder o acesso da identidade gerida atribuída pelo sistema da sua VM do Windows às chaves da conta do Cosmos DB.

Para conceder o acesso de identidade gerida atribuída pelo sistema da VM do Windows à conta do Cosmos DB no Azure Resource Manager com o PowerShell, atualize os valores de `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<COSMOS DB ACCOUNT NAME>` para o seu ambiente. O Cosmos DB suporta dois níveis de granularidade ao utilizar chaves de acesso: acesso de leitura/escrita à conta e acesso só de leitura à conta.  Atribua a função `DocumentDB Account Contributor` se pretender obter as chaves de leitura/escrita para a conta, ou atribua a função `Cosmos DB Account Reader Role` se quiser obter só as chaves de leitura para a conta.  Neste tutorial, atribua a `Cosmos DB Account Reader Role`:

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> Tenha em mente que, se não conseguir realizar uma operação, poderá não ter as permissões certas. Se quiser escrever acesso a teclas, tem de utilizar uma função RBAC, como o Colaborador de Conta DocumentDB ou criar uma função personalizada. Para mais informações, revisão [do controlo de acesso baseado em funções em Azure Cosmos DB](../../cosmos-db/role-based-access-control.md)

## <a name="access-data"></a>Aceder a dados

Esta secção mostra como ligar para o Azure Resource Manager utilizando um token de acesso para a identidade gerida atribuída pelo sistema Windows VM. No resto do tutorial, iremos trabalhar a partir da VM que criámos anteriormente. 

Tem de instalar a versão mais recente do [Azure CLI](/cli/azure/install-azure-cli) no seu Windows VM.



### <a name="get-an-access-token"></a>Obter um token de acesso

1. No portal do Azure, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Windows e, em seguida, na página **Descrição Geral**, clique em **Ligar** na parte superior. 
2. Introduza o seu **Nome de Utilizador** e a **Palavra-passe** que adicionou quando criou a VM do Windows. 
3. Agora que criou uma **Ligação de Ambiente de Trabalho Remoto** com a máquina virtual, abra o PowerShell na sessão remota.
4. Através de Invoke-WebRequest do PowerShell, faça um pedido às identidades geridas locais para o ponto final dos recursos do Azure obter um token de acesso para o Azure Resource Manager.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > No pedido anterior, o valor do parâmetro "recurso" tem de ser uma correspondência exata para o que é esperado pelo Azure AD. Ao utilizar o ID de recurso do Azure Resource Manager, tem de incluir a barra à direita no URI.
    
   Em seguida, extraia o elemento "Content", que é armazenado como uma cadeia formatada do JavaScript Object Notation (JSON) no objeto $response. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   Em seguida, extraia o token de acesso da resposta.
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>Obtenha chaves de acesso 

Esta secção mostra como obter chaves de acesso do Azure Resource Manager para fazer chamadas de Cosmos DB. Agora, utilize o PowerShell para chamar o Resource Manager com o token de acesso que obteve na secção anterior para obter a chave de acesso à conta do Cosmos DB. Assim que tivermos a chave de acesso, podemos fazer consultas no Cosmos DB. Certifique-se de que substitui os valores de parâmetros `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` e `<COSMOS DB ACCOUNT NAME>` pelos seus próprios valores. Substitua o valor `<ACCESS TOKEN>` pelo o token de acesso que obteve anteriormente.  Se pretender obter chaves de leitura/escrita, utilize o tipo de operação de chave `listKeys`.  Se pretender obter chaves só de leitura, utilize o tipo de operação de chave `readonlykeys`:

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
A resposta dá-lhe a lista de Chaves.  Por exemplo, se obtiver chaves só de leitura:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Agora que tem a chave de acesso para a conta do Cosmos DB, pode passá-la a um SDK do Cosmos DB e fazer chamadas para aceder à conta.  Para obter um rápido exemplo, pode passar a chave de acesso à CLI do Azure.  Pode obter o `<COSMOS DB CONNECTION URL>` no separador **Descrição Geral** no painel da conta do Cosmos DB no portal do Azure.  Substitua o `<ACCESS KEY>` pelo valor que obteve acima:

```azurecli
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


## <a name="disable"></a>Desativar

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar uma identidade atribuída ao sistema da VM do Windows para aceder ao Cosmos DB.  Para saber mais sobre o Cosmos DB, veja:

> [!div class="nextstepaction"]
>[Visão geral do Azure Cosmos DB](../../cosmos-db/introduction.md)