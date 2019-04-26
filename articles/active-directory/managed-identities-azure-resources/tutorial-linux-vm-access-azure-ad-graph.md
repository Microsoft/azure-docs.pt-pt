---
title: Utilizar uma identidade gerida atribuída pelo sistema de VM do Linux para aceder ao Graph API do Azure AD
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo sistema de VM do Linux, para aceder ao Graph API do Azure AD.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 481cb560daa26e59de2c78cc64bab9fb168eed58
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307510"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Tutorial: Utilizar uma identidade gerida atribuída pelo sistema de VM do Linux para aceder ao Graph API do Azure AD

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como utilizar uma identidade gerida atribuída pelo sistema para uma máquina virtual (VM) do Linux para aceder à Graph API do Microsoft Azure AD, de modo a obter as associações a grupos. As identidades geridas dos recursos do Azure são geridas automaticamente pelo Azure e permitem que se autentique perante serviços que suportem Autenticação do Azure AD sem que seja necessário inserir as credenciais no seu código.  

Neste tutorial, irá consultar a associação da identidade da sua VM em grupos do Azure AD. As informações de grupo são frequentemente utilizadas em decisões de autorização. Nos bastidores, a identidade gerida da sua VM é representada por um **Principal de Serviço** no Azure AD. 

> [!div class="checklist"]
> * Ligar ao Azure AD
> * Adicionar a identidade da VM a um grupo no Azure AD 
> * Conceder acesso à identidade da VM ao Azure AD Graph 
> * Obter um token de acesso com a identidade da VM e utilizá-lo para chamar o Azure AD Graph

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Instalar a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

- Para conceder acesso à identidade da VM ao Azure AD Graph, tem de atribuir a função **Administrador Global** à conta no Azure AD.

## <a name="connect-to-azure-ad"></a>Ligar ao Azure AD

Tem de ligar ao Azure AD para atribuir a VM a um grupo, bem como conceder à VM permissão para obter as respetivas associações a grupos.

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>Adicionar a identidade da VM a um grupo do Azure AD

Quando ativou a identidade gerida atribuída pelo sistema na VM do Linux, criou um principal de serviço no Azure AD.  Tem de adicionar a VM a um grupo. Veja o artigo seguinte para obter instruções sobre como adicionar a VM a um grupo do Azure AD:

- [Adicionar membros do grupo](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Conceder acesso à VM ao Graph API do Azure AD

Com as identidades geridas para recursos do Azure, o seu código pode obter tokens de acesso para autenticação perante recursos que suportem a Autenticação do Azure AD. A API Graph do Microsoft Azure Active Directory suporta a autenticação do Azure AD. Neste passo, vai conceder acesso ao principal de serviço da identidade da VM ao Azure AD Graph, para que possa consultar as associações a grupos. É concedido acesso aos principais de serviços à Microsoft ou ao Azure AD Graph através das **Permissões de Aplicação**. O tipo de permissão de aplicação que tem de conceder depende da entidade à qual pretende aceder na MS ou no Azure AD Graph.

Neste tutorial, vai conceder à identidade da VM a capacidade de consultar associações a grupos com a permissão de aplicação `Directory.Read.All`. Para conceder esta permissão, irá precisar de uma conta de utilizador que tenha atribuída a função de Administrador Global do Azure AD. Normalmente, concederia uma permissão de aplicação ao visitar o registo da sua aplicação no portal do Azure e adicionaria aí a permissão. No entanto, as identidades geridas de recursos do Azure não registam os objetos da aplicação no Azure AD, só são registados os principais de serviço. Para registar a permissão de aplicação vai utilizar a ferramenta de linha de comandos do Azure AD PowerShell. 

Azure AD Graph:
- AppId de Principal de serviço (utilizado quando a concessão de permissão da aplicação): 00000002-0000-0000-c000-000000000000
- ID do recurso (utilizado ao pedir o token de acesso de identidades geridas dos recursos do Azure): https://graph.windows.net
- Referência de âmbito de permissão: [Referência de permissões do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>Conceder permissões de aplicação com o CURL

1. Obter um token para fazer pedidos CURL:

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. Terá de obter e anotar o `objectId` da sua VM. É utilizado nos passos subsequentes para conceder permissões à VM para ler a respetiva associação a grupos. Substitua `<ACCESS TOKEN>` pelo token de acesso que obteve no passo anterior.

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. Com o appID do Azure AD Graph, 00000002-0000-0000-c000-000000000000, obtenha e anote o `objectId` do `odata.type: Microsoft.DirectoryServices.ServicePrincipal` e o `id` da permissão da função de aplicação `Directory.Read.All`.  Substitua `<ACCESS TOKEN>` pelo token de acesso que obteve anteriormente.

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Resposta:

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. Agora, conceda acesso de leitura ao principal de serviço da VM aos objetos de diretório do Azure AD com o Graph API do Azure AD.  O valor `id` é o valor da permissão da função de aplicação `Directory.Read.All` e o `resourceId` é o `objectId` do principal de serviço `odata.type:Microsoft.DirectoryServices.ServicePrincipal` (os valores que anotou no passo anterior).

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Obter um token de acesso com a identidade da VM para chamar o Microsoft Azure AD Graph 

Para concluir estes passos, precisará de um cliente SSH. Se estiver a utilizar o Windows, pode utilizar o cliente SSH no [Subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Se precisar de ajuda para configurar as chaves do seu cliente SSH, veja [Como utilizar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md) ou [Como criar e utilizar um par de chaves SSH públicas e privadas para VMs do Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. No portal, navegue para a VM do Linux e, em **Descrição Geral**, clique em **Ligar**.  
2. **Ligue** à VM com o cliente SSH que escolher. 
3. Na janela de terminal, com o CURL, fazer um pedido para as locais identidades geridas para o ponto final de recursos do Azure obter um acesso de token para o Azure AD Graph.  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   A resposta inclui o token de acesso necessário para aceder ao Azure AD Graph.

   Resposta:

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. Com o ID de objeto do principal de serviço da VM (o valor que obteve nos passos anteriores), pode consultar o Graph API do Azure AD para obter as respetivas associações a grupos. Substitua `<OBJECT-ID>` com o ID de objeto do principal de serviço da sua VM e `<ACCESS-TOKEN>` com o token de acesso obtido anteriormente:

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   Resposta:

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar uma identidade gerida atribuída pelo sistema de VM do Linux para aceder ao Azure AD Graph.  Para saber mais sobre o Azure AD Graph, veja:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
