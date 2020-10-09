---
title: Tutorial `:` Utilize uma identidade gerida para aceder ao Cofre da Chave Azure - Linux - Azure AD
description: Um tutorial que o orienta pelo processo de utilização de uma identidade gerida atribuída pelo sistema de uma VM do Linux para aceder ao Azure Resource Manager.
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
ms.date: 11/20/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6756d66f176314ad5abd0c94f7bdf96c42a460a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89255314"
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

Com as identidades de serviço geridas para recursos do Azure, o seu código pode obter tokens de acesso para autenticação em recursos que suportam a autenticação do Azure Active Directory.No entanto, nem todos os serviços do Azure suportam a autenticação do Azure AD.Para utilizar identidades geridas para recursos do Azure com esses serviços, armazene as credenciais do serviço no Azure Key Vault e utilize as identidades geridas para recursos do Azure para aceder ao Key Vault para obter as credenciais. 

Primeiro, é preciso criar um Key Vault e conceder o acesso de identidade gerida atribuída pelo sistema da nossa VM ao Key Vault.   

1. Na parte superior da barra de navegação esquerda, **selecione Criar um**cofre de segurança de recursos + chave de  >  **identidade**  >  **Key Vault**.  
2. Indique um **Nome** para o novo Key Vault. 
3. Localize o Key Vault na mesma subscrição e grupo de recursos da VM que criou anteriormente. 
4. Selecione **Políticas de acesso** e clique em **Adicionar novo**. 
5. Em Configurar a partir do modelo, selecione **Gestão de Segredos**. 
6. Selecione **Selecionar Principal** e, no campo de pesquisa, introduza o nome da VM que criou anteriormente.Selecione a VM na lista de resultados e clique em **Selecionar**. 
7. Clique em **OK** para concluir a adição da nova política de acesso e em **OK** para concluir a seleção da política de acesso. 
8. Clique em **Criar** para concluir a criação do Key Vault. 

    ![Texto alternativo da imagem](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Em seguida, adicione um segredo ao Key Vault para que possa mais tarde obter o segredo com o código em execução na sua VM: 

1. Selecione **Todos os Recursos** e localize e selecione o Key Vault que criou. 
2. Selecione **Segredos** e clique em **Adicionar**. 
3. Selecione **Manual** em **Opções de carregamento**. 
4. Introduza o nome e o valor do segredo.O valor pode ser o que quiser. 
5. Deixe as datas de ativação e expiração claras e mantenha **Ativado** como **Sim**. 
6. Clique em **Criar** para criar o segredo. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obter um token de acesso com a identidade da VM e utilizá-lo para obter o segredo a partir do Key Vault  

Para concluir estes passos, precisa de um cliente SSH.Se estiver a utilizar o Windows, pode utilizar o cliente SSH no [Subsistema Windows para Linux](/windows/wsl/about). Se precisar de ajuda para configurar as chaves do seu cliente SSH, veja [Como utilizar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md) ou [Como criar e utilizar um par de chaves SSH públicas e privadas para VMs do Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. No portal, navegue para a VM do Linux e, em **Descrição Geral**, clique em **Ligar**. 
2. Clique em **Ligar** para ligar à VM com o cliente SSH que escolheu. 
3. Na janela de terminal, através do CURL, envie um pedido às identidades geridas no local para que o ponto final dos recursos do Azure obtenha um token de acesso ao Azure Key Vault.  
 
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
    
    Pode utilizar este token de acesso para autenticação no Azure Key Vault.O pedido CURL seguinte mostra como ler um segredo do Key Vault com o CURL e a API REST do Key Vault.Precisará do URL do Key Vault, que está na secção **Informações Básicas** da página **Descrição Geral** do Key Vault.Também precisará do token de acesso que obteve na chamada anterior. 
        
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
>[Azure Key Vault](../../key-vault/general/overview.md)