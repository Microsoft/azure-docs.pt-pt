---
title: Criar um inquilino no Windows Virtual Desktop (clássico) - Azure
description: Descreve como configurar inquilinos virtuais do Windows Desktop (clássico) no Azure Ative Directory.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b2421c8b8c2d74649084f1cc8b75160857f40fc1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444331"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-classic"></a>Tutorial: Criar um inquilino no Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows.

Criar um inquilino no Windows Virtual Desktop é o primeiro passo para a construção da sua solução de virtualização de desktop. Um inquilino é um grupo de uma ou mais piscinas hospedeiras. Cada piscina de anfitriões é composta por vários anfitriões de sessão, funcionando como máquinas virtuais em Azure e registados no serviço De Desktop Virtual do Windows. Cada pool de anfitriões também é composto por um ou mais grupos de aplicações que são usados para publicar recursos remotos de desktop e aplicações remotas para os utilizadores. Com um inquilino, você pode construir piscinas de anfitrião, criar grupos de aplicações, atribuir utilizadores e fazer ligações através do serviço.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceda permissões ao Serviço de Ambiente de Trabalho Virtual do Windows.
> * Atribua a função de candidatura do TenantCreator a um utilizador no seu inquilino Azure Ative Directory.
> * Crie um inquilino virtual do Windows Desktop.

## <a name="what-you-need-to-set-up-a-tenant"></a>O que precisa para criar um inquilino

Antes de começar a configurar o seu inquilino virtual do Windows Desktop, certifique-se de que tem estas coisas:

* O ID do inquilino [do Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) para utilizadores de desktop virtual do Windows.
* Uma conta de administrador global dentro do inquilino do Azure Ative Directory.
   * Isto também se aplica às organizações cloud Solution Provider (CSP) que estão a criar um inquilino virtual windows desktop para os seus clientes. Se estiver numa organização da CSP, deve poder inscrever-se como administrador global da instância do Azure Ative Directory do cliente.
   * A conta do administrador deve ser proveniente do inquilino do Azure Ative Directory no qual está a tentar criar o inquilino virtual do Windows Desktop. Este processo não suporta contas Azure Ative Directory B2B (convidados).
   * A conta do administrador deve ser uma conta de trabalho ou escola.
* Uma subscrição do Azure.

Você deve ter o ID do inquilino, conta de administrador global, e a subscrição Azure pronta para que o processo descrito neste tutorial possa funcionar corretamente.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Conceder permissões ao Windows Virtual Desktop

Se já tiver dado permissões ao Windows Virtual Desktop para esta instância do Azure Ative Directory, salte esta secção.

A concessão de permissões ao serviço De Ambiente de Trabalho Virtual do Windows permite consultar o Azure Ative Directory para tarefas administrativas e de utilizador final.

Para conceder as permissões de serviço:

1. Abra um navegador e inicie o fluxo de consentimento administrativo para a [aplicação](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)do servidor virtual do Windows Desktop .
   > [!NOTE]
   > Se gere um cliente e precisa de conceder o consentimento administrativo para o diretório do cliente, introduza o seguinte URL no navegador e substitua {tenant} pelo nome de domínio AD AZure do cliente. Por exemplo, se a organização do cliente tiver registado o nome de domínio AD AD da contoso.onmicrosoft.com, substitua {tenant} por contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Inscreva-se na página de consentimento virtual do Windows desktop com uma conta de administrador global. Por exemplo, se estivesse com a organização Contoso, a sua conta poderia ser admin@contoso.com ou admin@contoso.onmicrosoft.com .
3. Selecione **Aceitar**.
4. Aguarde um minuto para que a Azure AD possa gravar o consentimento.
5. Abra um navegador e inicie o fluxo de consentimento administrativo para a [aplicação de clientes Virtual Desktop](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)do Windows .
   >[!NOTE]
   > Se gere um cliente e precisa de conceder o consentimento administrativo para o diretório do cliente, introduza o seguinte URL no navegador e substitua {tenant} pelo nome de domínio AD AZure do cliente. Por exemplo, se a organização do cliente tiver registado o nome de domínio AD AD da contoso.onmicrosoft.com, substitua {tenant} por contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Inscreva-se na página de consentimento virtual do Windows como administrador global, como fez no passo 2.
7. Selecione **Aceitar**.

## <a name="assign-the-tenantcreator-application-role"></a>Atribuir a função de candidatura do TenantCreator

Atribuir um utilizador do Azure Ative Directory a função de aplicação TenantCreator permite que o utilizador crie um inquilino virtual do Windows associado à instância do Diretório Ativo Azure. Terá de usar a sua conta de administrador global para atribuir o papel de TenantCreator.

Para atribuir a função de candidatura do TenantCreator:

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a função de candidatura do TenantCreator. Procure e selecione **aplicações Enterprise.** Se você está trabalhando com vários inquilinos do Azure Ative Directory, é uma boa prática abrir uma sessão de navegador privado e copiar e colar os URLs na barra de endereços.

   > [!div class="mx-imgBorder"]
   > ![Screenshot de pesquisa de aplicações da Enterprise no portal Azure](../media/azure-portal-enterprise-applications.png)

2. Dentro **das aplicações da Enterprise,** procure por **Windows Virtual Desktop**. Verá as duas aplicações que forneceu o consentimento na secção anterior. Destas duas aplicações, selecione **Windows Virtual Desktop**.

   > [!div class="mx-imgBorder"]
   > ![Uma imagem dos resultados da pesquisa ao procurar "Windows Virtual Desktop" em "Aplicações da Empresa". A aplicação chamada "Windows Virtual Desktop" está em destaque.](../media/tenant-enterprise-app.png)

3. Selecione **Utilizadores e grupos**. Pode ver que o administrador que concedeu o consentimento ao pedido já está listado com a função **de Acesso Predefinido** atribuída. Isto não é suficiente para criar um inquilino virtual do Windows Desktop. Continue seguindo estas instruções para adicionar o papel **de TenantCreator** a um utilizador.

   > [!div class="mx-imgBorder"]
   > ![Uma imagem dos utilizadores e grupos designados para gerir a aplicação da empresa "Windows Virtual Desktop". A imagem mostra apenas uma atribuição, que é para "Acesso Padrão".](../media/tenant-default-access.png)

4. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no **separador 'Adicionar Atribuição'.**
5. Procure uma conta de utilizador que crie o seu inquilino virtual do Windows Desktop. Para simplificar, esta pode ser a conta de administrador global.
   - Se estiver a utilizar um Fornecedor de Identidade do Microsoft, pode contosoadmin@live.com contosoadmin@outlook.com não conseguir iniciar sôs o seu sôm. Recomendamos a utilização de uma conta específica de domínio, como admin@contoso.com ou admin@contoso.onmicrosoft.com em vez disso.

   > [!div class="mx-imgBorder"]
   > ![Uma imagem de seleção de um utilizador para adicionar como "TenantCreator".](../media/tenant-assign-user.png)

   > [!NOTE]
   > Tem de selecionar um utilizador (ou um grupo que contenha um utilizador) que seja obtido a partir desta instância do Azure Ative Directory. Não pode escolher um utilizador (B2B) ou um principal de serviço.

6. Selecione a conta de utilizador, escolha o botão **Selecione** e, em seguida, **selecione Atribuir**.
7. Na página virtual do **Windows Desktop - Utilizadores e grupos,** verifique se vê uma nova entrada com a função **TenantCreator** atribuída ao utilizador que irá criar o inquilino virtual do Windows.

   > [!div class="mx-imgBorder"]
   > ![Uma imagem dos utilizadores e grupos designados para gerir a aplicação da empresa "Windows Virtual Desktop". A imagem agora inclui uma segunda entrada de um utilizador atribuído à função "TenantCreator".](../media/tenant-tenant-creator-added.png)

Antes de continuar a criar o seu inquilino virtual windows Desktop, precisa de duas informações:

   - Seu ID do seu inquilino Azure Ative (ou **ID do Diretório)**
   - O seu ID de assinatura Azure

Para encontrar o seu ID do inquilino do Azure Ative (ou **ID do Diretório):**
1. Na mesma sessão [do portal Azure,](https://portal.azure.com) procure e selecione **O Diretório Ativo Azure**.

   > [!div class="mx-imgBorder"]
   > ![Uma imagem dos resultados da pesquisa de "Azure Ative Directory" no portal Azure. Destaca-se o resultado da pesquisa em "Serviços".](../media/tenant-search-azure-active-directory.png)

2. Desloque-se para baixo até encontrar **propriedades** e, em seguida, selecione-o.
3. Procure **o ID do Diretório** e, em seguida, selecione o ícone da área de transferência. Cole-o num local útil para que possa usá-lo mais tarde como o valor **AadTenantId.**

   > [!div class="mx-imgBorder"]
   > ![Uma imagem das propriedades do Azure Ative Directory. O rato está a pairar sobre o ícone da área de transferência para que "Diretório ID" copie e cole.](../media/tenant-directory-id.png)

Para encontrar o seu ID de subscrição Azure:
1. Na mesma sessão [do portal Azure,](https://portal.azure.com) procure e selecione **Subscrições.**

   > [!div class="mx-imgBorder"]
   > ![Uma imagem dos resultados da pesquisa de "Azure Ative Directory" no portal Azure. Destaca-se o resultado da pesquisa de "Serviços".](../media/tenant-search-subscription.png)

2. Selecione a subscrição Azure que pretende utilizar para receber notificações de serviço de desktop virtual do Windows.
3. Procure **o ID da subscrição** e, em seguida, sobre o valor até aparecer um ícone de área de transferência. Selecione o ícone da área de transferência e cole-o num local útil para que possa usá-lo mais tarde como o valor **AzureSubscriptionId.**

   > [!div class="mx-imgBorder"]
   > ![Uma imagem das propriedades de subscrição do Azure. O rato está a pairar sobre o ícone da área de transferência para "Subscription ID" para copiar e colar.](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Criar um inquilino virtual do Windows Desktop

Agora que concedeu as permissões do serviço de desktop virtual do Windows para consultar o Azure Ative Directory e atribuiu o papel de TenantCreator a uma conta de utilizador, pode criar um inquilino virtual do Windows Desktop.

Em primeiro lugar, [descarregue e importe o módulo De Ambiente de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para utilizar na sessão PowerShell se ainda não o fez.

Inscreva-se no Windows Virtual Desktop utilizando a conta de utilizador Do TenantCreator com este cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, crie um novo inquilino virtual do Windows Desktop associado ao inquilino do Azure Ative Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Substitua os valores escaldados por valores relevantes para a sua organização e inquilino. O nome que escolher para o seu novo inquilino virtual windows desktop deve ser globalmente único. Por exemplo, digamos que é o Centro de Inquilinos virtuais do Windows para a organização Contoso. O cmdlet que correria seria assim:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

É uma boa ideia atribuir acesso administrativo a um segundo utilizador no caso de se encontrar trancado fora da sua conta, ou sair de férias e precisar de alguém para agir como administrador inquilino na sua ausência. Para atribuir acesso de administrador a um segundo utilizador, executar o seguinte cmdlet e `<TenantName>` substituído pelo nome do seu inquilino e pela `<Upn>` UPN do segundo utilizador.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Passos seguintes
Depois de criar o seu inquilino, terá de criar um diretor de serviço no Azure Ative Directory e atribuir-lhe um papel dentro do Windows Virtual Desktop. O diretor de serviço permitir-lhe-á implementar com sucesso a oferta do Windows Virtual Desktop Azure Marketplace para criar uma piscina de anfitriões. Para saber mais sobre as piscinas de anfitrião, continue o tutorial para criar uma piscina de anfitriões no Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Criar principais de serviço e atribuições de funções com o PowerShell](create-service-principal-role-powershell.md)
