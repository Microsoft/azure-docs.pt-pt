---
title: 'Azure Active Directory Domain Services: Guia de solução de problemas | Microsoft Docs'
description: Guia de solução de problemas para Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: c5ec80e81381423bdfdee07b1c020343d14ed559
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617072"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services-guia de solução de problemas
Este artigo fornece dicas de solução de problemas que você pode encontrar ao configurar ou administrar os serviços de domínio do Azure Active Directory (AD).

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Não é possível habilitar Azure AD Domain Services para seu diretório do Azure AD
Esta seção ajuda a solucionar erros quando você tenta habilitar o Azure AD Domain Services para seu diretório.

Escolha as etapas de solução de problemas que correspondem à mensagem de erro que você encontrará.

| **Mensagem de erro** | **Resolução** |
| --- |:--- |
| *O nome contoso.com já está em uso nesta rede. Especifique um nome que não esteja a ser utilizado.* |[Conflito de nome de domínio na rede virtual](troubleshoot.md#domain-name-conflict) |
| *Não foi possível ativar os Serviços de Domínio neste inquilino do Azure AD. O serviço não tem as permissões adequadas para a aplicação designada “Sincronização do Azure AD Domain Services”. Elimine a aplicação designada “Sincronização do Azure AD Domain Services” e, em seguida, tente ativar os Serviços de Domínio do inquilino do Azure AD.* |[Os serviços de domínio não têm as permissões adequadas para o aplicativo de sincronização de Azure AD Domain Services](troubleshoot.md#inadequate-permissions) |
| *Não foi possível ativar os Serviços de Domínio neste inquilino do Azure AD. A aplicação Serviços de Domínio do seu inquilino do Azure AD não tem as permissões necessárias para ativar os Serviços de Domínio. Elimine a aplicação com o identificador de aplicação d87dcbc6-a371-462e-88e3-28ad15ec4e64 e, em seguida, tente ativar os Serviços de Domínio do seu inquilino do Azure AD.* |[O aplicativo de serviços de domínio não está configurado corretamente em seu locatário](troubleshoot.md#invalid-configuration) |
| *Não foi possível ativar os Serviços de Domínio neste inquilino do Azure AD. A aplicação do Microsoft Azure AD está desativada no seu inquilino do Azure AD. Ative a aplicação com o identificador de aplicação 00000002-0000-0000-c000-000000000000 e, em seguida, tente ativar os Serviços de Domínio do seu inquilino do Azure AD.* |[O aplicativo Microsoft Graph está desabilitado no seu locatário do Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflito de nome de domínio
**Mensagem de erro:**

*O nome contoso.com já está em uso nesta rede. Especifique um nome que não esteja a ser utilizado.*

**Corre**

Certifique-se de que você não tenha um domínio existente com o mesmo nome de domínio disponível nessa rede virtual. Por exemplo, suponha que tem um domínio chamado 'contoso.com' já disponível na rede virtual selecionada. Posteriormente, você tenta habilitar um Azure AD Domain Services domínio gerenciado com o mesmo nome de domínio (ou seja, ' contoso.com ') nessa rede virtual. Você encontra uma falha ao tentar habilitar a Azure AD Domain Services.

Essa falha ocorre devido a conflitos de nome para o nome de domínio nessa rede virtual. Nesta situação, tem de utilizar um nome diferente para configurar o seu domínio gerido dos Serviços de Domínio do Azure AD. Em alternativa, pode anular o aprovisionamento do domínio existente e, em seguida, prosseguir para ativar os Serviços de Domínio do Azure AD.

### <a name="inadequate-permissions"></a>Permissões inadequadas
**Mensagem de erro:**

*Não foi possível ativar os Serviços de Domínio neste inquilino do Azure AD. O serviço não tem as permissões adequadas para a aplicação designada “Sincronização do Azure AD Domain Services”. Elimine a aplicação designada “Sincronização do Azure AD Domain Services” e, em seguida, tente ativar os Serviços de Domínio do inquilino do Azure AD.*

**Corre**

Verifique se há um aplicativo com o nome ' Azure AD Domain Services sincronização ' no diretório do Azure AD. Se esse aplicativo existir, exclua-o e reabilite Azure AD Domain Services.

Execute as seguintes etapas para verificar a presença do aplicativo e excluí-lo, se o aplicativo existir:

1. Navegue até a seção de **aplicativos** do seu diretório do Azure AD no [portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Selecione **todos os aplicativos** na lista suspensa **Mostrar** . Selecione **qualquer** no menu suspenso **status de aplicativos** . Selecione **qualquer** no menu suspenso **visibilidade do aplicativo** .
3. Digite **Azure AD Domain Services sincronização** na caixa de pesquisa. Se o aplicativo existir, clique nele e clique no botão **excluir** na barra de ferramentas para excluí-lo.
4. Depois de excluir o aplicativo, tente habilitar Azure AD Domain Services novamente.

### <a name="invalid-configuration"></a>Configuração inválida
**Mensagem de erro:**

*Não foi possível ativar os Serviços de Domínio neste inquilino do Azure AD. A aplicação Serviços de Domínio do seu inquilino do Azure AD não tem as permissões necessárias para ativar os Serviços de Domínio. Elimine a aplicação com o identificador de aplicação d87dcbc6-a371-462e-88e3-28ad15ec4e64 e, em seguida, tente ativar os Serviços de Domínio do seu inquilino do Azure AD.*

**Corre**

Verifique se você tem um aplicativo com o nome ' AzureActiveDirectoryDomainControllerServices ' (com um identificador de aplicativo de d87dcbc6-a371-462e-88e3-28ad15ec4e64) no seu diretório do Azure AD. Se esse aplicativo existir, você precisará excluí-lo e reabilitar Azure AD Domain Services.

Use o script do PowerShell a seguir para localizar o aplicativo e excluí-lo.

> [!NOTE]
> Esse script usa cmdlets **do Azure ad PowerShell versão 2** . Para obter uma lista completa de todos os cmdlets disponíveis e baixar o módulo, leia a [documentação de referência do PowerShell do AzureAD](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph desabilitado
**Mensagem de erro:**

Não foi possível habilitar os serviços de domínio neste locatário do Azure AD. A aplicação do Microsoft Azure AD está desativada no seu inquilino do Azure AD. Habilite o aplicativo com o identificador de aplicativo 00000002-0000-0000-C000-000000000000 e tente habilitar os serviços de domínio para seu locatário do Azure AD.

**Corre**

Verifique se você desabilitou um aplicativo com o identificador 00000002-0000-0000-C000-000000000000. Esse aplicativo é o Microsoft Azure AD aplicativo e fornece acesso API do Graph ao seu locatário do Azure AD. Azure AD Domain Services precisa que este aplicativo seja habilitado para sincronizar seu locatário do Azure AD para seu domínio gerenciado.

Para resolver esse erro, habilite este aplicativo e tente habilitar os serviços de domínio para seu locatário do Azure AD.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Os utilizadores não conseguem iniciar sessão no domínio gerido pelo Azure AD Domain Services
Se um ou mais usuários em seu locatário do Azure AD não conseguirem entrar no domínio gerenciado recém-criado, execute as seguintes etapas de solução de problemas:

* **Entrar usando o formato UPN:** Tente iniciar sessão com o formato UPN (por exemplo, “joeuser@contoso.com”) em vez do formato de SAMAccountName (“CONTOSO\joeuser”). O SAMAccountName pode ser gerado automaticamente para usuários cujo prefixo UPN é muito longo ou é o mesmo que outro usuário no domínio gerenciado. É garantido que o formato UPN seja exclusivo em um locatário do Azure AD.

> [!NOTE]
> É recomendável usar o formato UPN para entrar no Azure AD Domain Services domínio gerenciado.
>
>

* Certifique-se de que [ativou a sincronização de palavras-passe](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds), em conformidade com os passos descritos no Guia de Introdução.
* **Contas externas:** Certifique-se de que a conta de utilizador afetada não é uma conta externa do inquilino do Azure AD. Exemplos de contas externas incluem contas da Microsoft (por exemplo,joe@live.com"") ou contas de usuário de um diretório externo do Azure AD. Como Azure AD Domain Services não tem credenciais para essas contas de usuário, esses usuários não podem entrar no domínio gerenciado.
* **Contas sincronizadas:** Se as contas de usuário afetadas forem sincronizadas de um diretório local, verifique se:

  * Você implantou ou atualizou para a [versão mais recente recomendada do Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594).
  * Você configurou Azure AD Connect para [executar uma sincronização completa](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).
  * Dependendo do tamanho do diretório, pode levar algum tempo para que as contas de usuário e os hashes de credenciais estejam disponíveis no Azure AD Domain Services. Certifique-se de Aguardar tempo suficiente antes de tentar novamente a autenticação.
  * Se o problema persistir depois de verificar as etapas anteriores, tente reiniciar o serviço de sincronização de Microsoft Azure AD. Em seu computador de sincronização, inicie um prompt de comando e execute os seguintes comandos:

    1. net stop ' Microsoft Azure AD sincronizar '
    2. net start ' sincronização de Microsoft Azure AD '
* **Contas somente em nuvem**: Se a conta de usuário afetada for uma conta de usuário somente em nuvem, verifique se o usuário alterou sua senha depois de habilitar a Azure AD Domain Services. Este passo faz com que sejam gerados os hashes de credencial necessários para o Azure AD Domain Services.
* **Verifique se a conta de usuário está ativa**: Se a conta de um usuário estiver bloqueada, ela não poderá entrar até que sua conta esteja ativa novamente. Cinco tentativas de senha inválidas em 2 minutos no domínio gerenciado fazem com que uma conta de usuário seja bloqueada por 30 minutos. Após 30 minutos, a conta de usuário será desbloqueada automaticamente.
  * Tentativas de senha inválidas no domínio gerenciado não bloqueiam a conta de usuário no Azure AD. A conta de usuário é bloqueada somente dentro de seu Azure AD Domain Services domínio gerenciado. Verifique o status da conta de usuário usando o console administrativo do Active Directory (ADAC) para o domínio gerenciado do AD DS do Azure, não no Azure AD.
  * Você também pode [Configurar políticas de senha refinadas que alteram o limite e a duração do bloqueio padrão](https://docs.microsoft.com/azure/active-directory-domain-services/password-policy).

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Há um ou mais alertas em seu domínio gerenciado

Consulte como resolver alertas em seu domínio gerenciado visitando o artigo [solucionar problemas de alertas](troubleshoot-alerts.md) .

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Os usuários removidos do seu locatário do Azure AD não são removidos do seu domínio gerenciado
O Azure AD impede a eliminação acidental de objetos de utilizador. Quando elimina uma conta de utilizador do inquilino do Azure AD, o objeto de utilizador correspondente é movido para a Reciclagem. Quando essa operação de exclusão é sincronizada com o domínio gerenciado, ela faz com que a conta de usuário correspondente seja marcada como desabilitada. Esse recurso ajuda a recuperar ou a restaurar a conta de usuário mais tarde.

A conta de usuário permanece no estado desabilitado em seu domínio gerenciado, mesmo se você recriar uma conta de usuário com o mesmo UPN em seu diretório do Azure AD. Para remover a conta de usuário do seu domínio gerenciado, você precisará forçar a exclusão do seu locatário do Azure AD.

Para remover totalmente a conta de usuário do seu domínio gerenciado, exclua o usuário permanentemente do seu locatário do Azure AD. Use o `Remove-MsolUser` cmdlet do PowerShell com `-RemoveFromRecycleBin` a opção, conforme descrito neste [artigo do MSDN](/previous-versions/azure/dn194132(v=azure.100)).


## <a name="contact-us"></a>Contacte-nos
Entre em contato com a equipe de produto Azure Active Directory Domain Services para [compartilhar comentários ou para obter suporte](contact-us.md).
