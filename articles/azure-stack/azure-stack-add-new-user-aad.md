---
title: Adicionar uma nova conta de inquilino do Azure Stack no Azure Active Directory | Documentos da Microsoft
description: Depois de implementar o Development Kit do Microsoft Azure Stack, terá de criar a conta de utilizador, pelo menos, um inquilino para que possa explorar o portal de inquilinos.
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 40b26429cf4e7f218c2b5a276ae936f60bd6abe4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453240"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Adicionar uma nova conta de inquilino do Azure Stack no Azure Active Directory

Após [implementar o Development Kit do Azure Stack](azure-stack-run-powershell-script.md), terá de uma conta de utilizador do inquilino para que possa explorar o portal de inquilinos e suas ofertas e planos de teste. Pode criar uma conta de inquilino por [com o portal do Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) ou com o PowerShell.

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Criar uma conta de inquilino do Azure Stack com o portal do Azure

Tem de ter uma subscrição do Azure para utilizar o portal do Azure.

1. Inicie sessão no [Azure](https://portal.azure.com).
2. Na barra de navegação esquerdo, selecione **do Active Directory** e mude para o diretório que pretende utilizar para o Azure Stack ou criar um novo.
3. Selecione **do Azure Active Directory** > **utilizadores** > **novo utilizador**.

    ![Utilizadores - página de todos os utilizadores com o novo utilizador realçado](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Sobre o **utilizador** página, preencha as informações necessárias.

    ![Adicionar novo utilizador, a página de utilizador com informações do utilizador](media/azure-stack-add-new-user-aad/new-user-user.png)

    - **Nome (obrigatório).** O nome próprio e apelido do utilizador novo. Por exemplo, Mary Parker.
    - **Nome de utilizador (obrigatório).** O nome de utilizador do utilizador novo. Por exemplo, mary@contoso.com.
        A parte do domínio do nome do utilizador tem de utilizar qualquer um do predefinido inicial nome de domínio <_NomeDominio_>. onmicrosoft.com ou um nome de domínio personalizado, como contoso.com. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte [como adicionar um nome de domínio personalizado ao Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    - **Perfil.** Opcionalmente, pode adicionar mais informações sobre o utilizador. Também pode adicionar informações de utilizador num momento posterior. Para obter mais informações sobre como adicionar informações do utilizador, consulte [como adicionar ou alterar as informações de perfil do usuário](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).
    - **Função de diretório.**  escolher **utilizador**.

5. Verifique **mostrar palavra-passe** e copie o gerado automaticamente palavra-passe fornecida no **palavra-passe** caixa. Precisará esta palavra-passe para o processo de início de sessão inicial.

6. Selecione **Criar**.

    O utilizador é criado e adicionado ao inquilino do Azure AD.

7. Inicie sessão no portal do Microsoft Azure com a nova conta. Altere a palavra-passe quando lhe for pedido.
8. Inicie sessão no `https://portal.local.azurestack.external` com a nova conta para ver o portal de inquilinos.

## <a name="create-an-azure-stack-user-account-using-powershell"></a>Criar uma conta de utilizador do Azure Stack com o PowerShell

Se não tiver uma subscrição do Azure, não é possível utilizar o portal do Azure para adicionar uma conta de utilizador do inquilino. Neste caso, pode utilizar em vez disso, o módulo Azure Active Directory para Windows PowerShell.

> [!NOTE]
> Se estiver a utilizar Account Microsoft (Live ID) para implementar o Development Kit do Azure Stack, é possível utilizar o AAD PowerShell para criar a conta de inquilino. 

1. Instalar o [Assistente de início de sessão para os profissionais de TI RTW Microsoft Online Services](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Instalar o [módulo Azure Active Directory para Windows PowerShell (versão de 64 bits)](https://go.microsoft.com/fwlink/p/?linkid=236297) e abri-lo.
3. Execute os seguintes cmdlets:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Inicie sessão no Microsoft Azure com a nova conta. Altere a palavra-passe quando lhe for pedido.
2. Inicie sessão no `https://portal.local.azurestack.external` com a nova conta para ver o portal de inquilinos.

## <a name="next-steps"></a>Passos Seguintes

[Adicionar utilizadores do Azure Stack no AD FS](azure-stack-add-users-adfs.md)
