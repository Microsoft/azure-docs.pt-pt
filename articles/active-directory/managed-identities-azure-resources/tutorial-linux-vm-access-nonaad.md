---
title: Tutorial`:` Use a managed identity to access Azure Key Vault - Linux - Azure AD
description: Um tutorial que o orienta pelo processo de utilização de uma identidade gerida atribuída pelo sistema de uma VM do Linux para aceder ao Azure Resource Manager.
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
ms.date: 11/20/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdccabf701d4603b8c78f7e23ec1890171603273
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232176"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutorial: utilizar uma identidade gerida atribuída pelo sistema de VM do Linux para aceder ao Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como utilizar a identidade gerida atribuída pelo sistema para uma máquina virtual (VM) do Linux para aceder ao Azure Key Vault. A servir de arranque, o Key Vault permite à sua aplicação cliente utilizar o segredo para aceder aos recursos não protegidos pelo Azure Active Directory (AD). As Identidades geridas para recursos do Azure são geridas automaticamente pelo Azure e permitem-lhe fazer a autenticação em serviços que suportam a autenticação do Azure AD, sem ser necessário inserir credenciais no seu código. 

Saiba como:

> [!div class="checklist"]
> * Conceder o acesso da VM a um segredo armazenado num Key Vault 
> * Obter um token de acesso com a identidade da VM e utilizá-lo para obter o segredo a partir do Key Vault 
 
## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Conceder o acesso da VM a um segredo armazenado num Key Vault  

Com as identidades de serviço geridas para recursos do Azure, o seu código pode obter tokens de acesso para autenticação em recursos que suportam a autenticação do Azure Active Directory. However, not all Azure services support Azure AD authentication. To use managed identities for Azure resources with those services, store the service credentials in Azure Key Vault, and use managed identities for Azure resources to access Key Vault to retrieve the credentials. 

Primeiro, é preciso criar um Key Vault e conceder o acesso de identidade gerida atribuída pelo sistema da VM ao Key Vault.   

1. Na parte superior da barra de navegação esquerda, selecione **Criar um recurso** > **Segurança + Identidade** > **Key Vault**.  
2. Indique um **Nome** para o novo Key Vault. 
3. Localize o Key Vault na mesma subscrição e grupo de recursos da VM que criou anteriormente. 
4. Selecione **Políticas de acesso** e clique em **Adicionar novo**. 
5. Em Configurar a partir do modelo, selecione **Gestão de Segredos**. 
6. Selecione **Selecionar Principal** e, no campo de pesquisa, introduza o nome da VM que criou anteriormente.  Select the VM in the result list and click **Select**. 
7. Clique em **OK** para concluir a adição da nova política de acesso e em **OK** para concluir a seleção da política de acesso. 
8. Clique em **Criar** para concluir a criação do Key Vault. 

    ![Texto alternativo da imagem](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Em seguida, adicione um segredo ao Key Vault para que possa mais tarde obter o segredo com o código em execução na sua VM: 

1. Selecione **Todos os Recursos** e localize e selecione o Key Vault que criou. 
2. Selecione **Segredos** e clique em **Adicionar**. 
3. Selecione **Manual** em **Opções de carregamento**. 
4. Introduza o nome e o valor do segredo.  The value can be anything you want. 
5. Deixe as datas de ativação e expiração claras e mantenha **Ativado** como **Sim**. 
6. Clique em **Criar** para criar o segredo. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obter um token de acesso com a identidade da VM e utilizá-lo para obter o segredo a partir do Key Vault  

Para concluir estes passos, precisa de um cliente SSH.  If you are using Windows, you can use the SSH client in the [Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about). Se precisar de ajuda para configurar as chaves do seu cliente SSH, veja [Como utilizar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md) ou [Como criar e utilizar um par de chaves SSH públicas e privadas para VMs do Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. No portal, navegue para a VM do Linux e, em **Descrição Geral**, clique em **Ligar**. 
2. **Ligue** à VM com o cliente SSH que escolher. 
3. In the terminal window, using CURL, make a request to the local managed identities for Azure resources endpoint to get an access token for Azure Key Vault.  
 
    O pedido CURL para o token de acesso encontra-se abaixo.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    A resposta inclui o token de acesso necessário para aceder ao Resource Manager. 
    
    Resposta:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Pode utilizar este token de acesso para autenticação no Azure Key Vault.  The next CURL request shows how to read a secret from Key Vault using CURL and the Key Vault REST API.  You’ll need the URL of your Key Vault, which is in the **Essentials** section of the **Overview** page of the Key Vault.  You will also need the access token you obtained on the previous call. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    A resposta terá o seguinte aspeto: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Depois de recuperar o segredo do Key Vault, pode utilizá-lo para autenticação num serviço que requeira um nome e uma palavra-passe.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar uma identidade gerida atribuída pelo sistema de VM do Linux para aceder ao Azure Key Vault.  Para saber mais sobre o Azure Key Vault, veja:

> [!div class="nextstepaction"]
>[Cofre de Chaves do Azure](/azure/key-vault/key-vault-overview)




