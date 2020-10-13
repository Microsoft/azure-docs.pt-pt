---
title: Azure Ative Directory Domain Services resolução de problemas Microsoft Docs'
description: Saiba como resolver erros comuns quando cria ou gere os Serviços de Domínio do Diretório Ativo do Azure
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 9593fe71fc4a29678d58d7c67699210a4a39f95e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967380"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Erros comuns e etapas de resolução de problemas para os serviços de domínio do diretório ativo Azure

Como parte central da identidade e autenticação para aplicações, o Azure Ative Directory Domain Services (Azure AD DS) às vezes tem problemas. Se tiver problemas, existem algumas mensagens de erro comuns e passos associados de resolução de problemas para ajudá-lo a pôr as coisas a funcionar novamente. A qualquer momento, também pode abrir um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

Este artigo fornece etapas de resolução de problemas para questões comuns em Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Não é possível ativar os Serviços de Domínio AZure AD para o seu diretório AD Azure

Se tiver problemas em permitir o Azure AD DS, reveja os seguintes erros e passos comuns para os resolver:

| **Mensagem de erro da amostra** | **Resolução** |
| --- |:--- |
| *O nome aaddscontoso.com já está em uso nesta rede. Especifique um nome que não esteja a ser utilizado.* |[Conflito de nomes de domínio na rede virtual](troubleshoot.md#domain-name-conflict) |
| *Os Serviços de Domínio não puderam ser ativados neste inquilino AD Azure. O serviço não dispõe de permissões adequadas para a aplicação denominada "Azure AD Domain Services Sync". Elimine a aplicação chamada 'Azure AD Domain Services Sync' e, em seguida, tente ativar os Serviços de Domínio para o seu inquilino AD Azure.* |[Os Serviços de Domínio não têm permissões adequadas para a aplicação Azure AD Domain Services Sync](troubleshoot.md#inadequate-permissions) |
| *Os Serviços de Domínio não puderam ser ativados neste inquilino AD Azure. A aplicação De Serviços de Domínio no seu inquilino Azure AD não tem as permissões necessárias para ativar os Serviços de Domínio. Elimine a aplicação com o identificador de aplicação d87dcbc6-a371-462e-88e3-28ad15ec4e64 e, em seguida, tente ativar serviços de domínio para o seu inquilino AZure AD.* |[A aplicação de Serviços de Domínio não está configurada corretamente no seu inquilino AZure AD](troubleshoot.md#invalid-configuration) |
| *Os Serviços de Domínio não puderam ser ativados neste inquilino AD Azure. A aplicação AD da Microsoft Azure está desativada no seu inquilino AZure AD. Ativar a aplicação com o identificador de aplicação 0000002-0000-000-c000-000000000000000000000 e, em seguida, tentar ativar os Serviços de Domínio para o seu inquilino AZure AD.* |[A aplicação Microsoft Graph está desativada no seu inquilino AZure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflito de nome de domínio

**Mensagem de erro**

*O nome aaddscontoso.com já está em uso nesta rede. Especifique um nome que não esteja a ser utilizado.*

**Resolução**

Verifique se não tem um ambiente DS AD existente com o mesmo nome de domínio no mesmo, ou uma rede virtual esprevada. Por exemplo, pode ter um domínio AD DS nomeado *aaddscontoso.com* que funciona em VMs Azure. Quando tenta ativar um domínio gerido AZure AD DS com o mesmo nome de domínio de *aaddscontoso.com* na rede virtual, a operação solicitada falha.

Esta falha deve-se a conflitos de nomes para o nome de domínio na rede virtual. Uma verificação de DNS verifica se um ambiente DS AD existente responde no nome de domínio solicitado. Para resolver esta falha, utilize um nome diferente para configurar o seu domínio gerido ou desabilitado o domínio DS AD existente e, em seguida, tente novamente ativar Azure AD DS.

### <a name="inadequate-permissions"></a>Permissões inadequadas

**Mensagem de erro**

*Os Serviços de Domínio não puderam ser ativados neste inquilino AD Azure. O serviço não dispõe de permissões adequadas para a aplicação denominada "Azure AD Domain Services Sync". Elimine a aplicação chamada 'Azure AD Domain Services Sync' e, em seguida, tente ativar os Serviços de Domínio para o seu inquilino AD Azure.*

**Resolução**

Verifique se existe uma aplicação chamada *Azure AD Domain Services Sync* no seu diretório AD Azure. Se esta aplicação existir, elimine-a e tente novamente ativar o Azure AD DS. Para verificar uma aplicação existente e eliminá-la se necessário, complete os seguintes passos:

1. No portal Azure, selecione **Azure Ative Directory** a partir do menu de navegação à esquerda.
1. Selecione **aplicações da Enterprise**. Escolha *todas as aplicações* do menu drop-down do Tipo de **Aplicação** e, em seguida, selecione **Aplicar**.
1. Na caixa de pesquisa, insira *o Azure AD Domain Services Sync*. Se a aplicação existir, selecione-a e escolha **Eliminar**.
1. Depois de apagar a aplicação, tente ativar novamente o Azure AD DS.

### <a name="invalid-configuration"></a>Configuração inválida

**Mensagem de erro**

*Os Serviços de Domínio não puderam ser ativados neste inquilino AD Azure. A aplicação De Serviços de Domínio no seu inquilino Azure AD não tem as permissões necessárias para ativar os Serviços de Domínio. Elimine a aplicação com o identificador de aplicação d87dcbc6-a371-462e-88e3-28ad15ec4e64 e, em seguida, tente ativar serviços de domínio para o seu inquilino AZure AD.*

**Resolução**

Verifique se tem uma aplicação existente chamada *AzureActiveDirectoryDomainControllerServices* com um identificador de aplicação de *d87dcbc6-a371-462e-88e3-28ad15ec4e64* no seu diretório AD Azure. Se esta aplicação existir, elimine-a e tente novamente ativar o Azure AD DS.

Utilize o seguinte script PowerShell para procurar uma instância de aplicação existente e eliminá-la se necessário:

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

### <a name="microsoft-graph-disabled"></a>Microsoft Graph desativado

**Mensagem de erro**

*Os Serviços de Domínio não puderam ser ativados neste inquilino AD Azure. A aplicação AD da Microsoft Azure está desativada no seu inquilino AZure AD. Ativar a aplicação com o identificador de aplicação 0000002-0000-000-c000-000000000000000000000 e, em seguida, tentar ativar os Serviços de Domínio para o seu inquilino AZure AD.*

**Resolução**

Verifique se desativou uma aplicação com o identificador *0000002-0000-0000-c000-0000000000000000000000000000*. Esta aplicação é a aplicação AD do Microsoft Azure e fornece acesso a API gráfico ao seu inquilino AD Azure. Para sincronizar o seu inquilino Azure AD, esta aplicação deve ser ativada.

Para verificar o estado desta aplicação e capacitá-la se necessário, complete os seguintes passos:

1. No portal Azure, selecione **Azure Ative Directory** a partir do menu de navegação à esquerda.
1. Selecione **aplicações da Enterprise**. Escolha *todas as aplicações* do menu drop-down do Tipo de **Aplicação** e, em seguida, selecione **Aplicar**.
1. Na caixa de pesquisa, insira *0000002-0000-0000-c000-00000000000000000000*. Selecione a aplicação e, em seguida, escolha **Propriedades.**
1. Se **O pedido de inscrição dos utilizadores** estiver definido como *Nº*, descreva o valor para *Sim,* então selecione **Guardar**.
1. Uma vez ativado a aplicação, tente ativar novamente o Azure AD DS.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Os utilizadores não conseguem iniciar sessão no domínio gerido pelo Azure AD Domain Services

Se um ou mais utilizadores do seu inquilino AD Azure não puderem entrar no domínio gerido, complete as seguintes etapas de resolução de problemas:

* **Formato de credenciais** - Tente utilizar o formato UPN para especificar credenciais, tais como `dee@aaddscontoso.onmicrosoft.com` . O formato UPN é a forma recomendada de especificar credenciais em Azure AD DS. Certifique-se de que este UPN está configurado corretamente no Azure AD.

    O *NOME SAMAccount* para a sua conta, como *a AADDSCONTOSO\driley,* pode ser autogerido se existirem vários utilizadores com o mesmo prefixo UPN no seu inquilino ou se o seu prefixo UPN for demasiado longo. Portanto, o formato *SAMAccountName* para a sua conta pode ser diferente do que espera ou utiliza no seu domínio no local.

* **Sincronização de palavras-passe** - Certifique-se de que ativou a sincronização [de palavras-passe para utilizadores apenas na nuvem][cloud-only-passwords] ou para [ambientes híbridos utilizando o Azure AD Connect][hybrid-phs].
    * **Contas sincronizadas híbridas:** Se as contas de utilizador afetadas forem sincronizadas a partir de um diretório no local, verifique as seguintes áreas:
    
      * Implementou ou atualizou para o [mais recente lançamento recomendado do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
      * Configuraste o Azure AD Connect para [realizar uma sincronização completa.][hybrid-phs]
      * Dependendo do tamanho do seu diretório, pode demorar algum tempo para que as contas de utilizador e as hashes credenciais estejam disponíveis no domínio gerido. Certifique-se de que espera o suficiente antes de tentar autenticar contra o domínio gerido.
      * Se o problema persistir após verificar os passos anteriores, tente reiniciar o *Microsoft Azure AD Sync Service*. A partir do seu servidor Azure AD Connect, abra um pedido de comando e, em seguida, execute os seguintes comandos:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Contas exclusivamente na nuvem**: Se a conta de utilizador afetada for uma conta de utilizador exclusivamente na nuvem, certifique-se de que o utilizador alterou a [sua palavra-passe depois de ter ativado o Azure AD DS][cloud-only-passwords]. Esta redefinição de palavra-passe faz com que os hashes credenciais necessários para o domínio gerido sejam gerados.

* **Verifique se a conta do utilizador está ativa:** Por padrão, cinco tentativas de senha inválidas no prazo de 2 minutos no domínio gerido fazem com que uma conta de utilizador fique bloqueada durante 30 minutos. O utilizador não pode fazer o s.assal com a conta enquanto a conta estiver bloqueada. Após 30 minutos, a conta do utilizador é desbloqueada automaticamente.
  * As tentativas de palavra-passe inválidas no domínio gerido não bloqueiam a conta de utilizador no Azure AD. A conta de utilizador está bloqueada apenas dentro do domínio gerido. Verifique o estado da conta de utilizador na *Consola Administrativa de Diretório Ativo (ADAC)* utilizando o [VM de gestão,][management-vm]não em Azure AD.
  * Também pode [configurar políticas de senha finas][password-policy] para alterar o limiar de bloqueio predefinido e a duração.

* **Contas externas** - Verifique se a conta de utilizador afetada não é uma conta externa no inquilino da AD Azure. Exemplos de contas externas incluem contas da Microsoft como `dee@live.com` contas ou contas de utilizador de um diretório AD Azure externo. O Azure AD DS não armazena credenciais para contas de utilizadores externos, pelo que não podem entrar no domínio gerido.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Há um ou mais alertas no seu domínio gerido

Se existirem alertas ativos no domínio gerido, poderá impedir que o processo de autenticação funcione corretamente.

Para ver se existem alertas ativos, [verifique o estado de saúde de um domínio gerido][check-health]. Se forem apresentados alertas, [resolva-os e resolva-os][troubleshoot-alerts].

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Os utilizadores removidos do seu inquilino do Azure AD não são removidos do seu domínio gerido

A Azure AD protege contra a eliminação acidental de objetos do utilizador. Quando elimina uma conta de utilizador de um inquilino AZure AD, o objeto de utilizador correspondente é movido para o caixote do lixo. Quando esta operação de eliminação é sincronizada ao seu domínio gerido, a conta de utilizador correspondente é marcada como desativada. Esta funcionalidade ajuda-o a recuperar, ou desdelete, a conta do utilizador.

A conta de utilizador permanece no estado desativado no domínio gerido, mesmo que re crie uma conta de utilizador com a mesma UPN no diretório AD Azure. Para remover a conta de utilizador do domínio gerido, é necessário eliminá-la à força do inquilino AD da Azure.

Para remover totalmente uma conta de utilizador de um domínio gerido, elimine o utilizador permanentemente do seu inquilino AD Azure utilizando o cmdlet [Remove-MsolUser][Remove-MsolUser] PowerShell com o `-RemoveFromRecycleBin` parâmetro.

## <a name="next-steps"></a>Passos seguintes

Se continuar a ter problemas, abra um pedido de [apoio ao Azure][azure-support] para assistência adicional à resolução de problemas.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
