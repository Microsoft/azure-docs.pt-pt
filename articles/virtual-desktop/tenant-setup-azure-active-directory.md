---
title: Criar um locatário na visualização da área de trabalho virtual do Windows – Azure
description: Descreve como configurar locatários da versão prévia da área de trabalho virtual do Windows no Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: cd80ed3c3db2453a333c87ed706dd358ba248b47
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516195"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Tutorial: Criar um locatário na visualização da área de trabalho virtual do Windows

A criação de um locatário na visualização da área de trabalho virtual do Windows é a primeira etapa para criar sua solução de virtualização de desktops. Um locatário é um grupo de um ou mais pools de hosts. Cada pool de hosts consiste em vários hosts de sessão, executados como máquinas virtuais no Azure e registrados no serviço de área de trabalho virtual do Windows. Cada pool de hosts também consiste em um ou mais grupos de aplicativos que são usados para publicar recursos de área de trabalho remota e aplicativos remotos para os usuários. Com um locatário, você pode criar pools de hosts, criar grupos de aplicativos, atribuir usuários e fazer conexões por meio do serviço.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Conceda permissões de Azure Active Directory ao serviço de área de trabalho virtual do Windows.
> * Atribua a função de aplicativo TenantCreator a um usuário em seu locatário Azure Active Directory.
> * Crie um locatário de área de trabalho virtual do Windows.

Veja o que você precisa para configurar seu locatário de área de trabalho virtual do Windows:

* A ID de locatário [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para usuários da área de trabalho virtual do Windows.
* Uma conta de administrador global dentro do locatário Azure Active Directory.
   * Isso também se aplica às organizações do CSP (provedor de soluções na nuvem) que estão criando um locatário de área de trabalho virtual do Windows para seus clientes. Se você estiver em uma organização CSP, deverá ser capaz de entrar como administrador global da instância de Azure Active Directory do cliente.
   * A conta de administrador deve ser originada no locatário Azure Active Directory no qual você está tentando criar o locatário da área de trabalho virtual do Windows. Esse processo não oferece suporte a Azure Active Directory contas B2B (de convidado).
   * A conta de administrador deve ser uma conta corporativa ou de estudante.
* Uma subscrição do Azure.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Conceder permissões para área de trabalho virtual do Windows

Se você já tiver concedido permissões à área de trabalho virtual do Windows para esta Azure Active Directory instância, ignore esta seção.

Conceder permissões ao serviço de área de trabalho virtual do Windows permite que ele consulte Azure Active Directory para tarefas administrativas e de usuário final.

Para conceder as permissões de serviço:

1. Abra um navegador e inicie o fluxo de consentimento do administrador para o [aplicativo do servidor de área de trabalho virtual do Windows](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Se você gerenciar um cliente e precisar conceder consentimento de administrador para o diretório do cliente, insira a seguinte URL no navegador e substitua {Tenant} pelo nome de domínio do Azure AD do cliente. Por exemplo, se a organização do cliente tiver registrado o nome de domínio do Azure AD de contoso.onmicrosoft.com, substitua {Tenant} por contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Entre na página de consentimento da área de trabalho virtual do Windows com uma conta de administrador global. Por exemplo, se você estava com a organização Contoso, sua conta pode ser admin@contoso.com ou admin@contoso.onmicrosoft.com.  
3. Selecione **Aceitar**.
4. Aguarde um minuto para que o Azure AD possa registrar o consentimento.
5. Abra um navegador e inicie o fluxo de consentimento do administrador para o [aplicativo cliente de área de trabalho virtual do Windows](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Se você gerenciar um cliente e precisar conceder consentimento de administrador para o diretório do cliente, insira a seguinte URL no navegador e substitua {Tenant} pelo nome de domínio do Azure AD do cliente. Por exemplo, se a organização do cliente tiver registrado o nome de domínio do Azure AD de contoso.onmicrosoft.com, substitua {Tenant} por contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Entre na página de consentimento da área de trabalho virtual do Windows como administrador global, como fez na etapa 2.
7. Selecione **Aceitar**.

## <a name="assign-the-tenantcreator-application-role"></a>Atribuir a função de aplicativo TenantCreator

Atribuir um usuário Azure Active Directory a função de aplicativo TenantCreator permite que o usuário crie um locatário de área de trabalho virtual do Windows associado à instância de Azure Active Directory. Você precisará usar sua conta de administrador global para atribuir a função TenantCreator.

Para atribuir a função de aplicativo TenantCreator:

1. Abra um navegador e conecte-se ao [portal do Azure](https://portal.azure.com) com sua conta de administrador global.
   
   Se você estiver trabalhando com vários locatários Azure Active Directory, é uma prática recomendada abrir uma sessão de navegador privada e copiar e colar as URLs na barra de endereços.
2. Na barra de pesquisa dentro do portal do Azure, pesquise **aplicativos empresariais** e selecione a entrada que aparece na categoria **Serviços** .
3. Em **aplicativos empresariais**, pesquise a **área de trabalho virtual do Windows**. Você verá os dois aplicativos que forneceu consentimento para na seção anterior. Desses dois aplicativos, selecione **área de trabalho virtual do Windows**.
   ![Uma captura de tela dos resultados da pesquisa ao procurar por "área de trabalho virtual do Windows" em "aplicativos empresariais". O aplicativo chamado "área de trabalho virtual do Windows" é realçado.](media/tenant-enterprise-app.png)
4. Selecionar **Utilizadores e grupos**. Você pode ver que o administrador que concedeu consentimento ao aplicativo já está listado com a função de **acesso padrão** atribuída. Isso não é suficiente para criar um locatário de área de trabalho virtual do Windows. Continue seguindo estas instruções para adicionar a função **TenantCreator** a um usuário.
   ![Uma captura de tela dos usuários e grupos atribuídos para gerenciar o aplicativo empresarial "área de trabalho virtual do Windows". A captura de tela mostra apenas uma atribuição, que é para "acesso padrão".](media/tenant-default-access.png)
5. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na folha **Adicionar atribuição** .
6. Procure uma conta de usuário que criará seu locatário de área de trabalho virtual do Windows. Para simplificar, essa pode ser a conta de administrador global.

   ![Uma captura de tela de seleção de um usuário a ser adicionado como "TenantCreator".](media/tenant-assign-user.png)

   > [!NOTE]
   > Você deve selecionar um usuário (ou um grupo que contenha um usuário) originado dessa instância de Azure Active Directory. Você não pode escolher um usuário convidado (B2B) ou uma entidade de serviço.

7. Selecione a conta de usuário, escolha o botão **selecionar** e, em seguida, selecione **atribuir**.
8. Na página **área de trabalho virtual do Windows – usuários e grupos** , verifique se você vê uma nova entrada com a função **TenantCreator** atribuída ao usuário que criará o locatário de área de trabalho virtual do Windows.
   ![Uma captura de tela dos usuários e grupos atribuídos para gerenciar o aplicativo empresarial "área de trabalho virtual do Windows". A captura de tela agora inclui uma segunda entrada de um usuário atribuído à função "TenantCreator".](media/tenant-tenant-creator-added.png)

Antes de continuar a criar seu locatário de área de trabalho virtual do Windows, você precisará de duas informações:
- Sua ID de locatário Azure Active Directory (ou **ID de diretório**)
- Sua ID de assinatura do Azure

Para localizar sua ID de locatário Azure Active Directory (ou **ID de diretório**):
1. Na mesma sessão de portal do Azure, procure **Azure Active Directory** na barra de pesquisa e selecione a entrada que aparece na categoria **Serviços** .
   ![Uma captura de tela dos resultados da pesquisa para "Azure Active Directory" no portal do Azure. O resultado da pesquisa em "serviços" é realçado.](media/tenant-search-azure-active-directory.png)
2. Role para baixo até encontrar **Propriedades**e, em seguida, selecione-a.
3. Procure **ID de diretório**e, em seguida, selecione o ícone da área de transferência. Cole-o em um local útil para que você possa usá-lo posteriormente como o valor de **AadTenantId** .
   ![Uma captura de tela das propriedades de Azure Active Directory. O mouse está focalizando o ícone da área de transferência para "ID de diretório" para copiar e colar.](media/tenant-directory-id.png)

Para localizar sua ID de assinatura do Azure:
1. Na mesma sessão de portal do Azure, procure **assinaturas** na barra de pesquisa e selecione a entrada que aparece na categoria **Serviços** .
   ![Uma captura de tela dos resultados da pesquisa para "Azure Active Directory" no portal do Azure. O resultado da pesquisa em "serviços" é realçado.](media/tenant-search-subscription.png)
2. Selecione a assinatura do Azure que você deseja usar para receber notificações do serviço de área de trabalho virtual do Windows.
3. Procure **ID da assinatura**e passe o mouse sobre o valor até que um ícone da área de transferência seja exibido. Selecione o ícone da área de transferência e cole-o em um local útil para que você possa usá-lo mais tarde como o valor de **AzureSubscriptionId** .
   ![Uma captura de tela das propriedades de assinatura do Azure. O mouse está focalizando o ícone da área de transferência para "ID da assinatura" para copiar e colar.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Criar um locatário de visualização da área de trabalho virtual do Windows

Agora que você concedeu as permissões do serviço de área de trabalho virtual do Windows para consultar Azure Active Directory e atribuiu a função TenantCreator a uma conta de usuário, você pode criar um locatário de área de trabalho virtual do Windows.

Primeiro, [Baixe e importe o módulo de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar em sua sessão do PowerShell, se ainda não tiver feito isso.

Entre na área de trabalho virtual do Windows usando a conta de usuário TenantCreator com este cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, crie um novo locatário de área de trabalho virtual do Windows associado ao locatário Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Substitua os valores entre colchetes por valores relevantes para sua organização e locatário. O nome escolhido para seu novo locatário de área de trabalho virtual do Windows deve ser globalmente exclusivo. Por exemplo, digamos que você seja o TenantCreator da área de trabalho virtual do Windows para a organização Contoso. O cmdlet que você executaria ficaria assim:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar seu locatário, você precisará criar uma entidade de serviço em Azure Active Directory e atribuir a ela uma função na área de trabalho virtual do Windows. A entidade de serviço permitirá que você implante com êxito a oferta do Azure Marketplace da área de trabalho virtual do Windows para criar um pool de hosts. Para saber mais sobre pools de hosts, continue no tutorial para criar um pool de hosts na área de trabalho virtual do Windows.

> [!div class="nextstepaction"]
> [Criar principais de serviço e atribuições de funções com o PowerShell](./create-service-principal-role-powershell.md)
