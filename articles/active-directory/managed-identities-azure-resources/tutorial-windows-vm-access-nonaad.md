---
title: Tutorial `:` Utilize uma identidade gerida para aceder ao Cofre da Chave Azure - Windows - Azure AD
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo sistema numa VM do Windows, para aceder ao Azure Key Vault.
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
ms.date: 01/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2890eb2211ac0a105363742a0e900e52a577ed27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89255830"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutorial: Utilizar uma identidade gerida atribuída pelo sistema de VM do Windows para aceder ao Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como utilizar a identidade gerida atribuída pelo sistema para uma máquina virtual (VM) do Windows para aceder ao Azure Key Vault. A servir de arranque, o Key Vault permite à sua aplicação cliente utilizar o segredo para aceder aos recursos não protegidos pelo Azure Active Directory (AD). As Identidades de Serviço Geridas são geridas automaticamente pelo Azure e permitem-lhe fazer a autenticação em serviços que suportam a autenticação do Azure AD, sem ser necessário inserir as credenciais no seu código. 

Saiba como:


> [!div class="checklist"]
> * Conceder o acesso da VM a um segredo armazenado num Key Vault 
> * Obter um token de acesso com a identidade da VM e utilizá-lo para obter o segredo a partir do Key Vault 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="enable"></a>Ativar

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Conceder acesso  
 
Esta secção mostra como conceder o acesso ao seu VM a um Segredo armazenado num Cofre de Chaves. Com as identidades geridas para recursos do Azure, o seu código pode obter tokens de acesso para autenticação perante recursos que suportem a Autenticação do Azure AD.No entanto, nem todos os serviços do Azure suportam a autenticação do Azure AD.Para utilizar identidades geridas para recursos do Azure com esses serviços, armazene as credenciais do serviço no Azure Key Vault e utilize a identidade gerida da VM para aceder ao Key Vault para obter as credenciais. 

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
 
## <a name="access-data"></a>Aceder a dados  

Esta secção mostra como obter um token de acesso usando a identidade VM e usá-lo para recuperar o segredo do Cofre de Chaves. Se não tiver o PowerShell 4.3.1 ou posterior instalado, terá de [transferir e instalar a versão mais recente](/powershell/azure/).

Primeiro, utilizamos a identidade gerida atribuída pelo sistema da VM para obter um token de acesso para autenticação no Key Vault:
 
1. No portal, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Windows e, em **Descrição Geral**, clique em **Ligar**.
2. Introduza o seu **nome de utilizador** e **palavra-passe** para o qual adicionou quando criou o **Windows VM**.  
3. Agora que criou uma **Ligação de Ambiente de Trabalho Remoto** com a máquina virtual, abra o PowerShell na sessão remota.  
4. No PowerShell, invoca o pedido Web no inquilino para obter o token para o anfitrião local na porta específica para a VM.  

    O pedido do PowerShell:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    Em seguida, extraia a resposta completa, que é armazenada como uma cadeia formatada do JavaScript Object Notation (JSON) no objeto $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Em seguida, extraia o token de acesso da resposta.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Por fim, utilize o comando Invoke-WebRequest do PowerShell para obter o segredo que criou anteriormente no Key Vault ao transmitir o token de acesso no cabeçalho de Autorização.Precisará do URL do Key Vault, que está na secção **Informações Básicas** da página **Descrição Geral** do Key Vault.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    A resposta terá o seguinte aspeto: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Depois de recuperar o segredo do Key Vault, pode utilizá-lo para autenticação num serviço que requeira um nome e uma palavra-passe. 


## <a name="disable"></a>Desativar

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar uma identidade gerida atribuída pelo sistema de VM do Windows para aceder ao Azure Key Vault.  Para saber mais sobre o Azure Key Vault, veja:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)