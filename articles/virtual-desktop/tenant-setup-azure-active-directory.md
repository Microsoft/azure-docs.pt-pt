---
title: Criar um inquilino no Windows Virtual Desktop (pré-visualização) - Azure
description: Descreve como configurar a inquilinos de área de Trabalho Virtual do Windows no Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 26bcae12ee5e053289dd18c9f378eff43a0b0e04
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368175"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Tutorial: Criar um inquilino na área de Trabalho Virtual do Windows (pré-visualização)

A criação de um inquilino na área de Trabalho Virtual do Windows (pré-visualização) é o primeiro passo na direção de criar sua solução de Virtualização de área de trabalho. Um inquilino é um grupo de um ou mais agrupamentos de anfitrião. Cada pool de host é composta por vários hosts de sessão, em execução como máquinas virtuais no Azure e registados no serviço de área de Trabalho Virtual do Windows. Cada pool de anfitrião também consiste num ou mais grupos de aplicações que são utilizados para publicar recursos de aplicativo remoto e ambiente de trabalho remoto para os utilizadores. Com um inquilino, pode criar conjuntos de anfitrião, criar grupos de aplicações, atribuir utilizadores e fazer conexões através do serviço.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Permissões de concessão do Azure Active Directory para o serviço de área de Trabalho Virtual do Windows.
> * Atribua a função de aplicação TenantCreator a um utilizador no seu inquilino do Azure Active Directory.
> * Crie um inquilino de área de Trabalho Virtual do Windows.

Eis o que precisa de configurar o seu inquilino de área de Trabalho Virtual do Windows:

* O [do Azure Active Directory](https://azure.microsoft.com/services/active-directory/) ID for Windows Virtual Desktop utilizadores de inquilino.
* Uma conta de administrador global no inquilino do Azure Active Directory.
   * Isto também se aplica a organizações de fornecedor de soluções Cloud (CSP), criação de um inquilino de área de Trabalho Virtual do Windows para seus clientes. Se uma organização de CSP, tem de ser capaz de iniciar sessão como administrador global do Azure Active Directory do cliente.
* Um ID de subscrição do Azure

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-service"></a>Permissões de concessão do Azure Active Directory para o serviço de área de Trabalho Virtual do Windows

Se já concedeu permissões para a área de Trabalho Virtual do Windows para este Azure Active Directory, ignore esta secção.

A conceder permissões para o serviço de área de Trabalho Virtual do Windows permite que ele consultar o Azure Active Directory para administrativo e tarefas do utilizador final.

Para conceder as permissões de serviço:

1. Abra um browser e ligue-se para o [página de consentimento de área de Trabalho Virtual do Windows](https://rdweb.wvd.microsoft.com).
2. Para **opção consentimento** > **aplicações de servidor**, introduza o nome de inquilino do Azure Active Directory ou o ID de diretório, em seguida, selecione **submeter**.
        Para os clientes do fornecedor de soluções Cloud, o ID é o ID do cliente da Microsoft do Portal de parceiros. Para os clientes empresariais, o ID está localizado em **do Azure Active Directory** > **propriedades** > **ID de diretório**.
3. Inicie sessão para a página de consentimento de área de Trabalho Virtual do Windows com uma conta de administrador global. Por exemplo, se fosse com a organização Contoso, sua conta poderá ser admin@contoso.com ou admin@contoso.onmicrosoft.com.  
4. Selecione **Aceitar**.
5. Aguarde um minuto.
6. Navegue de volta para o [página de consentimento de área de Trabalho Virtual do Windows](https://rdweb.wvd.microsoft.com).
7. Aceda a **opção consentimento** > **aplicações de cliente**, introduza o mesmo nome de inquilino do Azure AD ou o ID de diretório, em seguida, selecione **submeter**.
8. Inicie sessão para a página de consentimento de área de Trabalho Virtual do Windows, como administrador global, como fez no passo 3.
9. Selecione **Aceitar**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Atribuir a função de aplicação TenantCreator a um utilizador no seu inquilino do Azure Active Directory

Atribuir um utilizador do Azure Active Directory a função de aplicação de TenantCreator permite que o utilizador criar um inquilino de área de Trabalho Virtual do Windows associado com o Azure Active Directory. Terá de utilizar a sua conta de administrador global para atribuir a função de TenantCreator.

Para atribuir a função de aplicação TenantCreator com a sua conta de administrador global:

1. Abra um browser e ligue-se para o [portal do Azure Active Directory](https://aad.portal.azure.com) com a sua conta de administrador global.
   - Se estiver a trabalhar com vários inquilinos do Azure AD, é melhor prática para abrir uma sessão de browser privada e copie e cole URLs no endereço.
2. Selecione **aplicações empresariais**, procure **área de Trabalho Virtual do Windows**. Verá as duas aplicações que forneceu o consentimento na secção anterior. Uma destas duas aplicações, selecione **área de Trabalho Virtual do Windows**.
3. Selecione **utilizadores e grupos**, em seguida, selecione **adicionar utilizador**.
4. Selecionar utilizadores e grupos no **adicionar atribuição** painel
5. Procurar por uma conta de utilizador que irá criar o seu inquilino de área de Trabalho Virtual do Windows.
   - Para simplificar, isso pode ser a conta de administrador global.
6. Selecione a conta de utilizador, clique nas **selecionar** e, em seguida, selecione **atribuir**.

## <a name="create-a-windows-virtual-desktop-tenant"></a>Criar um inquilino de área de Trabalho Virtual do Windows

Agora que concedidas as permissões de serviço de área de Trabalho Virtual do Windows para consultar o Azure Active Directory e atribuída a função de TenantCreator para uma conta de utilizador, pode criar um inquilino de área de Trabalho Virtual do Windows.

Primeiro, [transferir e importar o módulo de área de Trabalho Virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.

Inicie sessão no Windows área de Trabalho Virtual com a conta de utilizador de TenantCreator com este cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
```

Depois disso, crie um novo inquilino de área de Trabalho Virtual do Windows associado ao inquilino do Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Os valores entre parênteses devem ser substituídos com valores relevantes para a sua organização e inquilino. Por exemplo, imaginemos que está a TenantCreator de ambiente de Trabalho Virtual do Windows para a organização Contoso. O cmdlet, deverá executar teria o seguinte aspeto:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Passos Seguintes

Assim que tiver criado o seu inquilino, terá de fazer um pool de anfitrião. Para saber mais sobre conjuntos de anfitrião, avance para o tutorial para criar um agrupamento de anfitrião na área de Trabalho Virtual do Windows.

> [!div class="nextstepaction"]
> [Tutorial de conjunto de anfitrião de área de Trabalho Virtual do Windows](./create-host-pools-azure-marketplace.md)
