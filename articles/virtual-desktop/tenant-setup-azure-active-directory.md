---
title: Criar um inquilino na pré-visualização de ambiente de trabalho virtuais Windows - Azure
description: Descreve como configurar a inquilinos de pré-visualização de ambiente de Trabalho Virtual do Windows no Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 2c9682746201306f1b99a04462819618225caa11
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66164251"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Tutorial: Criar um inquilino na pré-visualização de ambiente de Trabalho Virtual do Windows

A criação de um inquilino na pré-visualização de ambiente de Trabalho Virtual do Windows é o primeiro passo na direção de criar sua solução de Virtualização de área de trabalho. Um inquilino é um grupo de um ou mais agrupamentos de anfitrião. Cada pool de host é composta por vários hosts de sessão, em execução como máquinas virtuais no Azure e registados no serviço de área de Trabalho Virtual do Windows. Cada pool de anfitrião também consiste num ou mais grupos de aplicações que são utilizados para publicar recursos de aplicativo remoto e ambiente de trabalho remoto para os utilizadores. Com um inquilino, pode criar conjuntos de anfitrião, criar grupos de aplicações, atribuir utilizadores e fazer conexões através do serviço.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Permissões de concessão do Azure Active Directory para o serviço de área de Trabalho Virtual do Windows.
> * Atribua a função de aplicação TenantCreator a um utilizador no seu inquilino do Azure Active Directory.
> * Crie um inquilino de área de Trabalho Virtual do Windows.

Eis o que precisa de configurar o seu inquilino de área de Trabalho Virtual do Windows:

* O [do Azure Active Directory](https://azure.microsoft.com/services/active-directory/) ID for Windows Virtual Desktop utilizadores de inquilino.
* Uma conta de administrador global no inquilino do Azure Active Directory.
   * Isto também se aplica a organizações de fornecedor de soluções Cloud (CSP), criação de um inquilino de área de Trabalho Virtual do Windows para seus clientes. Se uma organização de CSP, tem de ser capaz de iniciar sessão como administrador global do Azure Active Directory do cliente.
   * A conta de administrador tem de ser originária a partir do inquilino do Azure Active Directory no qual está a tentar criar o inquilino de área de Trabalho Virtual do Windows. Este processo não suporta o Azure Active Directory B2B contas (convidado).
   * A conta de administrador tem de ser uma conta escolar ou profissional.
* Uma subscrição do Azure

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Permissões de concessão do Azure Active Directory para o serviço de pré-visualização de ambiente de Trabalho Virtual do Windows

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
7. Aceda a **opção consentimento** > **aplicações de cliente**, introduza o mesmo nome de inquilino do Azure Active Directory ou ID de diretório, em seguida, selecione **submeter**.
8. Inicie sessão para a página de consentimento de área de Trabalho Virtual do Windows, como administrador global, como fez no passo 3.
9. Selecione **Aceitar**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Atribuir a função de aplicação TenantCreator a um utilizador no seu inquilino do Azure Active Directory

Atribuir um utilizador do Azure Active Directory a função de aplicação de TenantCreator permite que o utilizador criar um inquilino de área de Trabalho Virtual do Windows associado com o Azure Active Directory. Terá de utilizar a sua conta de administrador global para atribuir a função de TenantCreator.

Para atribuir a função de aplicação TenantCreator com a sua conta de administrador global:

1. Abra um browser e ligue-se para o [portal do Azure](https://portal.azure.com) com a sua conta de administrador global.
   - Se estiver a trabalhar com vários inquilinos do Azure Active Directory, é melhor prática para abrir uma sessão de browser privada e copie e cole os URLs na barra de endereço.
2. Na barra de pesquisa no portal do Azure, procure **aplicações empresariais** e selecione a entrada que aparece sob a **serviços** categoria.
3. Dentro **aplicações empresariais**, procure **área de Trabalho Virtual do Windows**. Verá as duas aplicações que forneceu o consentimento na secção anterior. Uma destas duas aplicações, selecione **área de Trabalho Virtual do Windows**.
        ![Uma captura de ecrã dos resultados da pesquisa ao pesquisar "Área de Trabalho Virtual do Windows" no "aplicações empresariais". A aplicação com o nome "Área de Trabalho Virtual do Windows" é realçada.](media/tenant-enterprise-app.png)
4. Selecionar **Utilizadores e grupos**. Pode ver que o administrador que autoriza a aplicação já está listado com o **acesso predefinido** função atribuída. Isso não é suficiente para criar um inquilino de área de Trabalho Virtual do Windows. Continue a seguir estas instruções para adicionar o **TenantCreator** função a um utilizador.
        ![Uma captura de ecrã dos utilizadores e grupos atribuídos para gerir a aplicação de enterprise "Área de Trabalho Virtual do Windows". A captura de ecrã mostra apenas uma atribuição que é de "Acesso predefinido".](media/tenant-default-access.png)
5. Selecione **adicionar utilizador**, em seguida, **utilizadores e grupos** no **adicionar atribuição** painel.
6. Procurar por uma conta de utilizador que irá criar o seu inquilino de área de Trabalho Virtual do Windows. Para simplificar, isso pode ser a conta de administrador global.

    ![Uma captura de ecrã da seleção de um utilizador para adicionar como "TenantCreator".](media/tenant-assign-user.png)

   > [!NOTE]
   > Tem de selecionar um utilizador (ou um grupo que contém um utilizador) que é obtido a partir deste Azure Active Directory. Não é possível escolher um utilizador convidado (B2B) ou um principal de serviço.

7. Selecione a conta de utilizador, selecione o **selecionar** e, em seguida, selecione **atribuir**.
8. Sobre o **Windows área de Trabalho Virtual - os utilizadores e grupos** página, certifique-se de que vê uma nova entrada com o **TenantCreator** função atribuída ao utilizador que irá criar o inquilino de área de Trabalho Virtual do Windows.
        ![Uma captura de ecrã dos utilizadores e grupos atribuídos para gerir a aplicação de enterprise "Área de Trabalho Virtual do Windows". A captura de tela agora inclui uma segunda entrada de um utilizador atribuído à função de "TenantCreator".](media/tenant-tenant-creator-added.png)

Antes de continuar em criar o seu inquilino de área de Trabalho Virtual do Windows, terá duas informações:
- O ID de inquilino do Azure Active Directory (ou **ID de diretório**)
- O ID de subscrição do Azure

Para localizar o seu ID de inquilino do Active Directory do Azure (ou **ID de diretório**):
1. Na mesma para a sessão no portal do Azure, procure **do Azure Active Directory** na barra de pesquisa e selecione a entrada que aparece sob a **serviços** categoria.
        ![Uma captura de ecrã dos resultados da pesquisa para "Do Azure Active Directory" no portal do Azure. O resultado da pesquisa em "Serviços" é realçado.](media/tenant-search-azure-active-directory.png)
2. Desloque para baixo até encontrar **propriedades**, em seguida, selecioná-lo.
3. Procure **ID de diretório**, em seguida, selecione o ícone de área de transferência. Cole-a num uma localização útil para que possa utilizá-lo posterior como o **AadTenantId**.
        ![Uma captura de ecrã das propriedades do Azure Active Directory. O mouse é passado sobre o ícone de área de transferência para o "ID do diretório" copiar e colar.](media/tenant-directory-id.png)

Para localizar o seu ID de subscrição do Azure:
1. Na mesma para a sessão no portal do Azure, procure **subscrições** na barra de pesquisa e selecione a entrada que aparece sob a **serviços** categoria.
        ![Uma captura de ecrã dos resultados da pesquisa para "Do Azure Active Directory" no portal do Azure. O resultado da pesquisa em "Serviços" é realçado.](media/tenant-search-subscription.png)
2. Selecione a subscrição do Azure que pretende utilizar para receber as notificações de serviços de área de Trabalho Virtual do Windows.
3. Procure **ID de subscrição**, em seguida, coloque o cursor sobre o valor até que é apresentado um ícone de área de transferência. Selecione o ícone de área de transferência e cole-a numa localização conveniente para que possa utilizá-lo posteriormente como o **AzureSubscriptionId**.
        ![Uma captura de ecrã das propriedades da subscrição do Azure. O mouse é passado sobre o ícone de área de transferência para o "ID de subscrição" copiar e colar.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Criar um inquilino de pré-visualização de ambiente de Trabalho Virtual do Windows

Agora que concedidas as permissões de serviço de área de Trabalho Virtual do Windows para consultar o Azure Active Directory e atribuída a função de TenantCreator para uma conta de utilizador, pode criar um inquilino de área de Trabalho Virtual do Windows.

Primeiro, [transferir e importar o módulo de área de Trabalho Virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.

Inicie sessão no Windows área de Trabalho Virtual com a conta de utilizador de TenantCreator com este cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, crie um novo inquilino de área de Trabalho Virtual do Windows associado ao inquilino do Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Os valores entre parênteses devem ser substituídos com valores relevantes para a sua organização e inquilino. O nome que escolher para o novo inquilino de área de Trabalho Virtual do Windows deve ser globalmente exclusivo. Por exemplo, imaginemos que está a TenantCreator de ambiente de Trabalho Virtual do Windows para a organização Contoso. O cmdlet, deverá executar teria o seguinte aspeto:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Passos Seguintes

Assim que tiver criado o seu inquilino, terá de criar um principal de serviço no Azure Active Directory e atribuí-la uma função no ambiente de Trabalho Virtual do Windows. O principal de serviço, poderá implementar com êxito o Windows Virtual Desktop oferta do Azure Marketplace para criar um conjunto de anfitrião. Para saber mais sobre conjuntos de anfitrião, avance para o tutorial para criar um agrupamento de anfitrião na área de Trabalho Virtual do Windows.

> [!div class="nextstepaction"]
> [Criar principais de serviço e atribuições de funções com o PowerShell](./create-service-principal-role-powershell.md)
