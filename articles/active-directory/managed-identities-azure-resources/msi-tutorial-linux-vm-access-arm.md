---
title: Utilizar uma identidade gerida atribuída pelo utilizador da VM de Linux para aceder ao Azure Resource Manager
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo utilizador da VM de Linux para aceder ao Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: barclayn
ROBOTS: NOINDEX,NOFOLLOW
ms.collection: M365-identity-device-management
ms.openlocfilehash: 247b3c1b4341eff11069a6af324fff5cf1fba62c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546595"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Tutorial: utilizar uma identidade gerida atribuída pelo utilizador da VM de Linux para aceder ao Azure Resource Manager

Este tutorial explica como criar um identidade gerida atribuída pelo utilizador, atribuí-la a uma Máquina Virtual do Linux (VM do Linux) e, em seguida, utilizar essa identidade para aceder à API do Azure Resource Manager. As identidades geridas para recursos do Azure são geridas automaticamente pelo Azure. Permitem a autenticação em serviços que suportam a autenticação do Azure AD, sem ter de incorporar as credenciais no código. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma identidade gerida atribuída pelo utilizador
> * Atribuir a identidade gerida atribuída pelo utilizador a uma VM do Linux 
> * Conceder o acesso de identidade gerida atribuída pelo utilizador a um Grupo de Recursos no Azure Resource Manager 
> * Obter um token de acesso com a identidade gerida atribuída pelo utilizador e utilizá-lo para chamar o Azure Resource Manager 

## <a name="prerequisites"></a>Pré-requisitos

- Uma compreensão das identidades geridas. Se não estiver familiarizado com a funcionalidade das identidades geridas para os recursos do Azure, veja esta [descrição geral](overview.md). 
- Uma conta Azure, [inscreva-se para uma conta gratuita.](https://azure.microsoft.com/free/)
- Também precisa de uma máquina Virtual Linux. Se precisar de criar uma máquina virtual para este tutorial, pode seguir o artigo intitulado [Criar uma máquina virtual Linux com o portal Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)
- Para executar os scripts de exemplo, tem duas opções:
    - Utilize o [Azure Cloud Shell,](../../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Executar scripts localmente instalando a versão mais recente do [Azure CLI,](/cli/azure/install-azure-cli)em seguida, inicie sessão no Azure usando [login az](/cli/azure/reference-index#az-login).

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Crie uma identidade gerida atribuída pelo utilizador com [az identity create](/cli/azure/identity#az-identity-create). O parâmetro `-g` especifica o grupo de recursos onde a identidade gerida atribuída pelo utilizador é criada e o parâmetro `-n` especifica o respetivo nome. Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>`e `<UAMI NAME>` pelos seus próprios valores:
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

A resposta contém detalhes para a identidade gerida atribuída pelo utilizador criada, semelhante ao seguinte exemplo. Anote o valor `id` da identidade gerida atribuída pelo utilizador, uma vez que vai ser utilizada no próximo passo:

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-an-identity-to-your-linux-vm"></a>Atribua uma identidade ao seu Linux VM

Uma identidade gerida atribuída pelo utilizador pode ser utilizada pelos clientes em vários recursos do Azure. Utilize os seguintes comandos para atribuir a identidade gerida atribuída pelo utilizador a uma única VM. Utilize a propriedade `Id` devolvida no passo anterior para o parâmetro `-IdentityID`.

Atribua a identidade gerida atribuída ao utilizador para o seu VM Linux utilizando [a designação de identidade az vm](/cli/azure/vm). Certifique-se de que substitui os valores de parâmetros `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. Utilize a propriedade `id` devolvida no passo anterior para o valor de parâmetro `--identities`.

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-access-to-a-resource-group-in-azure-resource-manager"></a>Conceder acesso a um Grupo de Recursos em Azure Resource Manager

As identidades geridas dos recursos do Azure disponibilizam identidades que podem ser utilizadas pelo seu código para pedir os tokens de acesso para autenticar para API de recurso que suportem a autenticação do Azure AD. Neste tutorial, o seu código irá aceder à API do Azure Resource Manager.  

Antes de o seu código pode aceder à API, tem de conceder o acesso de identidade a um recurso no Azure Resource Manager. Neste caso, o Grupo de Recursos no qual a VM está contida. Atualize o valor de `<SUBSCRIPTION ID>` e `<RESOURCE GROUP>`, conforme adequado para o seu ambiente. Além disso, substitua `<UAMI PRINCIPALID>` pela propriedade `principalId` devolvida pelo comando `az identity create` em [Criar uma identidade gerida atribuída pelo utilizador](#create-a-user-assigned-managed-identity):

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

A resposta contém detalhes da atribuição de função criada, semelhante ao seguinte exemplo:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obter um token de acesso com a identidade da VM e utilizá-lo para chamar o Resource Manager 

No resto do tutorial, iremos trabalhar a partir da VM que criámos anteriormente.

Para concluir estes passos, precisa de um cliente SSH. Se estiver a utilizar o Windows, pode utilizar o cliente SSH no [Subsistema Windows para Linux](/windows/wsl/about). 

1. Inscreva-se no [portal](https://portal.azure.com)Azure .
2. No portal, navegue para **Máquinas Virtuais**, vá para a máquina virtual do Linux e, na **Descrição Geral**, clique em **Ligar**. Copie a cadeia de ligação para ligar à sua VM.
3. Ligue à VM com o cliente SSH que escolher. Se estiver a utilizar o Windows, pode utilizar o cliente SSH no [Subsistema Windows para Linux](/windows/wsl/about). Se precisar de ajuda para configurar as chaves do seu cliente SSH, veja [Como utilizar chaves SSH com o Windows no Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) ou [Como criar e utilizar um par de chaves SSH públicas e privadas para VMs do Linux no Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. Na janela de terminal, com o CURL, faça um pedido ao ponto final do Instance Metadata Service (IMDS) do Azure para obter um token de acesso para o Azure Resource Manager.  

   O pedido CURL para adquirir um token de acesso é mostrado no exemplo seguinte. Certifique-se de que substitui `<CLIENT ID>` pela propriedade `clientId` devolvida pelo comando `az identity create` em [Criar uma identidade gerida atribuída pelo utilizador](#create-a-user-assigned-managed-identity): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > O valor do parâmetro `resource` tem de ser uma correspondência exata para o que é esperado pelo Azure AD. Ao utilizar o ID de recurso do Resource Manager, tem de incluir a barra à direita no URI. 
    
    A resposta inclui o token de acesso necessário para aceder ao Azure Resource Manager. 
    
    Exemplo de resposta:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. Utilize o token de acesso para aceder ao Azure Resource Manager e ler as propriedades do Grupo de Recursos ao qual concedeu anteriormente o acesso de identidade gerida atribuída pelo utilizador. Certifique-se de que substitui `<SUBSCRIPTION ID>` e `<RESOURCE GROUP>` pelos valores que especificou anteriormente, e `<ACCESS TOKEN>` pelo token devolvido no passo anterior.

    > [!NOTE]
    > O URL é sensível às maiúsculas de minúsculas; por isso, certifique-se de que utiliza as mesmas maiúsculas e minúsculas que utilizou anteriormente, quando atribuiu o nome ao Grupo de Recursos, e a maiúscula “G” em `resourceGroups`.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    A resposta contém as informações específicas do Grupo de Recursos, semelhantes ao seguinte exemplo: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Passos seguintes

Este tutorial explica como criar um identidade gerida atribuída pelo utilizador e anexá-la a uma máquina virtual do Linux para aceder à API do Azure Resource Manager.  Para saber mais sobre o Azure Resource Manager, veja:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
