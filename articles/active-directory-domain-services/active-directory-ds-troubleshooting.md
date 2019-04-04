---
title: 'Azure Active Directory Domain Services: Guia de resolução de problemas | Documentos da Microsoft'
description: Guia de resolução de problemas do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: ergreenl
ms.openlocfilehash: 48831767f72dd1b978fad5b0a9a8f2c7a11ec89d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893117"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Serviços de domínio do Azure AD - guia de resolução de problemas
Este artigo fornece sugestões de resolução de problemas para problemas que poderá encontrar ao configurar ou administração dos serviços de domínio do Azure Active Directory (AD).

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Não é possível ativar o Azure AD Domain Services para o diretório do Azure AD
Esta secção ajuda-o a resolver problemas de erros ao tentar ativar o Azure AD Domain Services no seu diretório.

Escolha os passos de resolução de problemas que correspondem à mensagem de erro que encontrar.

| **Mensagem de Erro** | **Resolução** |
| --- |:--- |
| *O nome contoso100.com já está a ser utilizado nesta rede. Especifique um nome que não está em utilização.* |[Conflito de nomes de domínio na rede virtual](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *Não foi possível ativar os serviços de domínio neste inquilino do Azure AD. O serviço não tem as permissões adequadas para a aplicação designada “Sincronização do Azure AD Domain Services”. Elimine a aplicação designada "Sincronização do Azure AD Domain Services" e, em seguida, tente ativar os serviços de domínio do inquilino do Azure AD.* |[Serviços de domínio não tem as permissões adequadas para a aplicação de sincronização do Azure AD Domain Services](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *Não foi possível ativar os serviços de domínio neste inquilino do Azure AD. A aplicação Serviços de Domínio do seu inquilino do Azure AD não tem as permissões necessárias para ativar os Serviços de Domínio. Eliminar a aplicação com o d87dcbc6-a371-462e-88e3-28ad15ec4e64 de identificador de aplicação e, em seguida, tente ativar os serviços de domínio do inquilino do Azure AD.* |[A aplicação de serviços de domínio não está configurada corretamente no seu inquilino](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *Não foi possível ativar os serviços de domínio neste inquilino do Azure AD. A aplicação do Microsoft Azure AD está desativada no seu inquilino do Azure AD. Permitir que o aplicativo com o 00000002-0000-0000-C000-000000000000 de identificador de aplicação e, em seguida, tente ativar os serviços de domínio do inquilino do Azure AD.* |[A aplicação do Microsoft Graph está desativada no inquilino do Azure AD](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflito de nomes de domínio
**Mensagem de erro:**

*O nome contoso100.com já está a ser utilizado nesta rede. Especifique um nome que não está em utilização.*

**Remediação:**

Certifique-se de que não tem um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, suponha que tem um domínio chamado 'contoso.com' já disponível na rede virtual selecionada. Posteriormente, tenta ativar um domínio gerido do Azure AD Domain Services com o mesmo nome de domínio (ou seja, "contoso.com") na rede virtual. Ocorrer uma falha ao tentar ativar o Azure AD Domain Services.

Esta falha é devido a conflitos de nomes para o nome de domínio na rede virtual. Nesta situação, tem de utilizar um nome diferente para configurar o seu domínio gerido dos Serviços de Domínio do Azure AD. Em alternativa, pode anular o aprovisionamento do domínio existente e, em seguida, prosseguir para ativar os Serviços de Domínio do Azure AD.

### <a name="inadequate-permissions"></a>Permissões inadequadas
**Mensagem de erro:**

*Não foi possível ativar os serviços de domínio neste inquilino do Azure AD. O serviço não tem as permissões adequadas para a aplicação designada “Sincronização do Azure AD Domain Services”. Elimine a aplicação designada "Sincronização do Azure AD Domain Services" e, em seguida, tente ativar os serviços de domínio do inquilino do Azure AD.*

**Remediação:**

Verifique se existe uma aplicação com o nome "Sincronização de serviços de domínio do Azure AD" no diretório do Azure AD. Se esta aplicação existir, elimine-o e, em seguida, volte a ativar os serviços de domínio do Azure AD.

Execute os seguintes passos para verificar a presença da aplicação e eliminá-lo, se o aplicativo existir:

1. Navegue para o **aplicativos** secção do diretório do Azure AD no [portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Selecione **todos os aplicativos** no **mostrar** lista pendente. Selecione **qualquer** no **estado de aplicativos** lista pendente. Selecione **qualquer** no **visibilidade de aplicativo** lista pendente.
3. Tipo **sincronização do Azure AD Domain Services** na caixa de pesquisa. Se o aplicativo existir, clique no mesmo e clique nas **eliminar** botão na barra de ferramentas para eliminá-lo.
4. Depois de ter eliminado o aplicativo, tente ativar novamente o Azure AD Domain Services.

### <a name="invalid-configuration"></a>Configuração inválida
**Mensagem de erro:**

*Não foi possível ativar os serviços de domínio neste inquilino do Azure AD. A aplicação Serviços de Domínio do seu inquilino do Azure AD não tem as permissões necessárias para ativar os Serviços de Domínio. Eliminar a aplicação com o d87dcbc6-a371-462e-88e3-28ad15ec4e64 de identificador de aplicação e, em seguida, tente ativar os serviços de domínio do inquilino do Azure AD.*

**Remediação:**

Verifique se tem uma aplicação com o nome AzureActiveDirectoryDomainControllerServices (com um identificador de aplicação de d87dcbc6-a371-462e-88e3-28ad15ec4e64) no diretório do Azure AD. Se existir esta aplicação, terá de eliminá-lo e, em seguida, volte a ativar os serviços de domínio do Azure AD.

Utilize o seguinte script do PowerShell para localizar a aplicação e eliminá-lo.

> [!NOTE]
> Este script utiliza **do Azure AD PowerShell versão 2** cmdlets. Para obter uma lista completa de todos os cmdlets disponíveis e para transferir o módulo, leia os [documentação de referência do AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).
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

### <a name="microsoft-graph-disabled"></a>Microsoft Graph desativada
**Mensagem de erro:**

Não foi possível ativar os serviços de domínio neste inquilino do Azure AD. A aplicação do Microsoft Azure AD está desativada no seu inquilino do Azure AD. Permitir que o aplicativo com o 00000002-0000-0000-C000-000000000000 de identificador de aplicação e, em seguida, tente ativar os serviços de domínio do inquilino do Azure AD.

**Remediação:**

Verifique se tiver desativado uma aplicação com o identificador 00000002-0000-0000-c000-000000000000. Esse aplicativo é a aplicação do Microsoft Azure AD e fornece acesso à API de gráfico com o seu inquilino do Azure AD. O Azure AD Domain Services precisa esta aplicação seja ativado para sincronizar o seu inquilino do Azure AD ao seu domínio gerido.

Para resolver este erro, ativar esta aplicação e, em seguida, tente ativar os serviços de domínio do inquilino do Azure AD.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Os utilizadores não conseguem iniciar sessão no domínio gerido pelo Azure AD Domain Services
Se um ou mais utilizadores no inquilino do Azure AD não conseguem iniciar sessão no domínio gerido recém-criado, execute os seguintes passos de resolução de problemas:

* **Início de sessão com o formato UPN:** Tente iniciar sessão com o formato UPN (por exemplo, “joeuser@contoso.com”) em vez do formato de SAMAccountName (“CONTOSO\joeuser”). SAMAccountName pode ser gerado automaticamente para os utilizadores cujo prefixo UPN é demasiado longo ou é igual a outro utilizador no domínio gerido. O formato do UPN é a garantia de ser exclusivo dentro de um inquilino do Azure AD.

> [!NOTE]
> Recomendamos que utilize o formato do UPN para iniciar sessão no domínio gerido do Azure AD Domain Services.
>
>

* Certifique-se de que [ativou a sincronização de palavras-passe](active-directory-ds-getting-started-password-sync.md), em conformidade com os passos descritos no Guia de Introdução.
* **Contas externas:** Certifique-se de que a conta de utilizador afetada não é uma conta externa do inquilino do Azure AD. As contas externas exemplos de contas Microsoft (por exemplo, "joe@live.com") ou contas de utilizador a partir de um externo diretório do Azure AD. Uma vez que o Azure AD Domain Services não tem as credenciais para essas contas de utilizador, estes utilizadores não é possível iniciar sessão no domínio gerido.
* **Contas de sincronização:** Se as contas de utilizador afectado são sincronizadas a partir de um diretório no local, certifique-se de que:

  * Implementar ou atualizado para o [mais recente versão recomendada do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
  * Tiver configurado o Azure AD Connect para [efetuar uma sincronização completa](active-directory-ds-getting-started-password-sync.md).
  * Dependendo do tamanho do seu diretório, ele poderá demorar algum tempo para contas de utilizador e fiquem disponíveis no Azure AD Domain Services, os hashes de credenciais. Certifique-se de que espera tempo suficiente antes de repetir a operação de autenticação.
  * Se o problema persistir depois de verificar os passos anteriores, tente reiniciar o serviço de sincronização do Microsoft Azure AD. A partir do computador de sincronização, inicie uma linha de comandos e execute os seguintes comandos:

    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **Contas apenas na cloud**: Se a conta de utilizador afetada for uma conta de utilizador apenas na nuvem, certifique-se de que o utilizador foi alterado a palavra-passe depois de ativado o Azure AD Domain Services. Este passo faz com que sejam gerados os hashes de credencial necessários para o Azure AD Domain Services.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Existem um ou mais alertas no seu domínio gerido

Veja como resolver alertas no seu domínio gerido, visitando a [resolver problemas relacionados com alertas](active-directory-ds-troubleshoot-alerts.md) artigo.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Os utilizadores removidos do seu inquilino do Azure AD não são removidos do seu domínio gerido
O Azure AD impede a eliminação acidental de objetos de utilizador. Quando elimina uma conta de utilizador do inquilino do Azure AD, o objeto de utilizador correspondente é movido para a Reciclagem. Quando esta operação de eliminação é sincronizada com o seu domínio gerido, faz com que a conta de utilizador correspondente seja marcada como desativada. Esta funcionalidade ajuda-o a recuperar ou anular a conta de utilizador mais tarde.

A conta de utilizador permanece no estado desativado no seu domínio gerido, mesmo se voltar a criar uma conta de utilizador com o mesmo UPN no diretório do Azure AD. Para remover a conta de utilizador do seu domínio gerido, terá de eliminá-lo a forçar no seu inquilino do Azure AD.

Para remover completamente a conta de utilizador do seu domínio gerido, elimine permanentemente o utilizador do inquilino do Azure AD. Utilize o `Remove-MsolUser` cmdlet do PowerShell com o `-RemoveFromRecycleBin` opção, conforme descrito neste [artigo do MSDN](/previous-versions/azure/dn194132(v=azure.100)).


## <a name="contact-us"></a>Contacte-nos
Contacte a equipa de produto do Azure Active Directory Domain Services para [partilhar comentários ou para o suporte](active-directory-ds-contact-us.md).
