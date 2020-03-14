---
title: Criar um inquilino no Windows Virtual Desktop - Azure
description: Descreve como configurar os inquilinos do Windows Virtual Desktop no Diretório Ativo Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6a70e52771828de6cc9fad7998097d70c28a64fd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238819"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>Tutorial: Criar um inquilino no Windows Virtual Desktop

Criar um inquilino no Windows Virtual Desktop é o primeiro passo para a construção da sua solução de virtualização no ambiente de trabalho. Um inquilino é um grupo de uma ou mais piscinas de acolhimento. Cada piscina de anfitriões é composta por vários anfitriões de sessões, funcionando como máquinas virtuais em Azure e registadas no serviço de ambiente de trabalho virtual do Windows. Cada piscina de anfitriões também é composta por um ou mais grupos de aplicações que são usados para publicar recursos remotos de desktop e aplicações remotas para os utilizadores. Com um inquilino, você pode construir piscinas de anfitriões, criar grupos de aplicativos, atribuir utilizadores, e fazer ligações através do serviço.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Grant Azure Ative Directory permissões para o serviço de ambiente de trabalho virtual windows.
> * Atribuir a função de candidatura do TenantCreator a um utilizador no seu inquilino azure Ative Directory.
> * Crie um inquilino do Windows Virtual Desktop.

## <a name="what-you-need-to-set-up-a-tenant"></a>O que precisa para criar um inquilino

Antes de começar a configurar o seu inquilino do Windows Virtual Desktop, certifique-se de que tem estas coisas:

* O ID de inquilino [do Diretório Ativo Azure](https://azure.microsoft.com/services/active-directory/) para utilizadores de Ambiente de Trabalho Virtual windows.
* Uma conta de administrador global dentro do inquilino do Azure Ative Directory.
   * Isto também se aplica às organizações Cloud Solution Provider (CSP) que estão a criar um inquilino de ambiente de trabalho virtual Windows para os seus clientes. Se estiver numa organização de CSP, deve poder inscrever-se como administrador global da instância de Diretório Ativo Azure do cliente.
   * A conta de administrador deve ser obtida a partir do inquilino do Diretório Ativo Azure no qual está a tentar criar o inquilino do Windows Virtual Desktop. Este processo não suporta contas azure Ative Directory B2B (hóspede).
   * A conta do administrador deve ser uma conta de trabalho ou escola.
* Uma subscrição do Azure.

Você deve ter o ID do inquilino, a conta de administrador global, e a subscrição Azure pronto para que o processo descrito neste tutorial possa funcionar corretamente.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Conceder permissões para windows virtual desktop

Se já concedeu permissões ao Windows Virtual Desktop para esta instância de Diretório Ativo Azure, ignore esta secção.

A concessão de permissões ao serviço de desktop virtual do Windows permite consultar o Diretório Ativo Azure para tarefas administrativas e de utilizador final.

Para conceder as permissões de serviço:

1. Abra um navegador e inicie o fluxo de consentimento do administrador para a [aplicação](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)do servidor de desktop virtual do Windows .
   > [!NOTE]
   > Se gerir um cliente e precisar de conceder o consentimento do administrador para o diretório do cliente, introduza o seguinte URL no navegador e substitua {tenant} pelo nome de domínio Azure AD do cliente. Por exemplo, se a organização do cliente tiver registado o nome de domínio Azure AD de contoso.onmicrosoft.com, substitua {tenant} por contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Inscreva-se na página de consentimento do Windows Virtual Desktop com uma conta de administrador global. Por exemplo, se estivesse com a organização Contoso, a sua conta poderia ser admin@contoso.com ou admin@contoso.onmicrosoft.com.
3. Selecione **Aceitar**.
4. Espere um minuto para que o Azure AD possa registar o consentimento.
5. Abra um navegador e inicie o fluxo de consentimento do administrador para a [aplicação de cliente](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)do Windows Virtual Desktop .
   >[!NOTE]
   > Se gerir um cliente e precisar de conceder o consentimento do administrador para o diretório do cliente, introduza o seguinte URL no navegador e substitua {tenant} pelo nome de domínio Azure AD do cliente. Por exemplo, se a organização do cliente tiver registado o nome de domínio Azure AD de contoso.onmicrosoft.com, substitua {tenant} por contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Inscreva-se na página de consentimento do Windows Virtual Desktop como administrador global, como fez no passo 2.
7. Selecione **Aceitar**.

## <a name="assign-the-tenantcreator-application-role"></a>Atribuir a função de candidatura do TenantCreator

Atribuir um utilizador do Diretório Ativo Azure, a função de aplicação TenantCreator permite que esse utilizador crie um inquilino de ambiente de trabalho virtual do Windows associado à instância de Diretório Ativo Azure. Terá de usar a sua conta de administrador global para atribuir o papel de Criador de Inquilinos.

Atribuir a função de candidatura do TenantCreator:

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a função de candidatura do TenantCreator. Procure e selecione **aplicações Enterprise**. Se está a trabalhar com vários inquilinos do Azure Ative Diretório, é uma boa prática abrir uma sessão de navegador privada e copiar e colar os URLs na barra de endereços.

   ![Screenshot de pesquisa de aplicações da Enterprise no portal Azure](media/azure-portal-enterprise-applications.png)
2. Dentro das **aplicações da Enterprise,** procure o **Windows Virtual Desktop**. Verá as duas candidaturas para as quais deu consentimento na secção anterior. Destas duas aplicações, selecione **Windows Virtual Desktop**.
   
   ![Uma imagem dos resultados da pesquisa ao procurar "Windows Virtual Desktop" em "Aplicações Enterprise". A aplicação denominada "Windows Virtual Desktop" está em destaque.](media/tenant-enterprise-app.png)
3. Selecionar **Utilizadores e grupos**. Pode ver que o administrador que concedeu o consentimento ao pedido já está listado com a função **de Acesso Predefinido** atribuída. Isto não é suficiente para criar um inquilino do Windows Virtual Desktop. Continue a seguir estas instruções para adicionar a função **TenantCreator** a um utilizador.
   
   ![Uma imagem dos utilizadores e grupos atribuídos para gerir a aplicação da empresa "Windows Virtual Desktop". A imagem mostra apenas uma missão, que é para "Acesso Padrão".](media/tenant-default-access.png)
4. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na lâmina **adicionar assignment.**
5. Procure uma conta de utilizador que crie o seu inquilino do Windows Virtual Desktop. Para a simplicidade, esta pode ser a conta de administrador global.
   - Se estiver a utilizar um Fornecedor de Identidade da Microsoft como contosoadmin@live.com ou contosoadmin@outlook.com, poderá não conseguir iniciar sessão no Windows Virtual Desktop. Recomendamos a utilização de uma conta específica de domínio como admin@contoso.com ou admin@contoso.onmicrosoft.com em vez disso.

   ![Uma imagem de selecionar um utilizador para adicionar como "TenantCreator".](media/tenant-assign-user.png)
   > [!NOTE]
   > Deve selecionar um utilizador (ou um grupo que contenha um utilizador) que seja proveniente desta instância do Diretório Ativo Azure. Não é possível escolher um utilizador de hóspedes (B2B) ou um diretor de serviço.

6. Selecione a conta de utilizador, escolha o botão **Selecionar** e, em seguida, selecione **Atribuir**.
7. No **Windows Virtual Desktop - Página de Utilizadores e grupos,** verifique se vê uma nova entrada com a função **TenantCreator** atribuída ao utilizador que irá criar o inquilino do Windows Virtual Desktop.

   ![Uma imagem dos utilizadores e grupos atribuídos para gerir a aplicação da empresa "Windows Virtual Desktop". A imagem inclui agora uma segunda entrada de um utilizador atribuído à função "TenantCreator".](media/tenant-tenant-creator-added.png)

Antes de continuar a criar o seu inquilino do Windows Virtual Desktop, precisa de duas informações:

   - Id do seu inquilino de diretório ativo Azure (ou **ID do Diretório)**
   - O seu ID de subscrição Azure

Para encontrar o seu ID de inquilino do Diretório Ativo Azure (ou **ID do Diretório):**
1. Na mesma sessão [do portal Azure,](https://portal.azure.com) procure e selecione **Azure Ative Directory**.

   ![Uma imagem dos resultados da pesquisa de "Azure Ative Directory" no portal Azure. O resultado da pesquisa em "Serviços" é destacado.](media/tenant-search-azure-active-directory.png)
2. Desloque-se para baixo até encontrar **propriedades**e, em seguida, selecione-as.
3. Procure id **de diretório**e, em seguida, selecione o ícone de clipboard. Recolhê-lo num local útil para que possa usá-lo mais tarde como valor **AadTenantId.**

   ![Uma imagem das propriedades do Diretório Ativo Azure. O rato está a pairar sobre o ícone da prancheta para que o "Id do Diretório" copie e cole.](media/tenant-directory-id.png)

Para encontrar o seu ID de subscrição Azure:
1. Na mesma sessão [do portal Azure,](https://portal.azure.com) procure e selecione **Subscrições.**
   
   ![Uma imagem dos resultados da pesquisa de "Azure Ative Directory" no portal Azure. O resultado da pesquisa em "Serviços" é destacado.](media/tenant-search-subscription.png)
2. Selecione a subscrição Azure que pretende utilizar para receber notificações do serviço Windows Virtual Desktop.
3. Procure o **ID de subscrição**e, em seguida, sobreaperte o valor até aparecer um ícone de clipboard. Selecione o ícone da prancheta e cola-o num local útil para que possa usá-lo mais tarde como valor **AzureSubscriptionId.**
   
   ![Uma imagem das propriedades de subscrição do Azure. O rato está a pairar sobre o ícone da área de sobre-quadro para "Id de subscrição" copiar e colar.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Criar um inquilino do Windows Virtual Desktop

Agora que concedeu permissões ao serviço de desktop virtual do Windows para consultar o Diretório Ativo do Azure e atribuiu a função TenantCreator a uma conta de utilizador, pode criar um inquilino do Windows Virtual Desktop.

Primeiro, [faça o download e importe o módulo de ambiente](/powershell/windows-virtual-desktop/overview/) de trabalho virtual do Windows para utilizar na sua sessão PowerShell se ainda não o fez.

Inscreva-se no Windows Virtual Desktop utilizando a conta de utilizador do TenantCreator com este cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, crie um novo inquilino do Windows Virtual Desktop associado ao inquilino do Diretório Ativo Azure:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Substitua os valores divididos por valores relevantes para a sua organização e inquilino. O nome que escolher para o seu novo inquilino do Windows Virtual Desktop deve ser globalmente único. Por exemplo, digamos que é o Windows Virtual Desktop TenantCreator para a organização Contoso. O cmdlet que correria seria assim:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

É uma boa ideia atribuir acesso administrativo a um segundo utilizador no caso de se encontrar trancado fora da sua conta, ou sair de férias e precisar de alguém para agir como administrador do inquilino na sua ausência. Para atribuir acesso administrativo a um segundo utilizador, execute o seguinte cmdlet com `<TenantName>` e `<Upn>` substituído pelo nome do seu inquilino e pela UPN do segundo utilizador.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu inquilino, terá de criar um diretor de serviço no Azure Ative Directory e atribuir-lhe um papel no Windows Virtual Desktop. O diretor de serviço permitir-lhe-á implementar com sucesso a oferta do Windows Virtual Desktop Azure Marketplace para criar um pool de anfitriões. Para saber mais sobre as piscinas de anfitriões, continue ao tutorial para criar uma piscina de anfitriões no Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Criar principais de serviço e atribuições de funções com o PowerShell](./create-service-principal-role-powershell.md)
