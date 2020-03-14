---
title: Tiroteio de problemas nos Serviços de Domínio do Diretório Ativo azure Microsoft Docs'
description: Aprenda a resolver erros comuns ao criar ou gerir serviços de domínio de diretório ativo azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: e17112cbe2a494a585cd5a09c36cfe449d3d433c
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365820"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Erros comuns e passos de resolução de problemas para os Serviços de Domínio de Diretório Ativo azure

Como parte central da identidade e autenticação para aplicações, o Azure Ative Directory Domain Services (Azure AD DS) tem, por vezes, problemas. Se tiver problemas, existem algumas mensagens de erro comuns e passos associados de resolução de problemas para ajudá-lo a pôr as coisas a funcionar novamente. A qualquer momento, também pode abrir um pedido de [apoio azure][azure-support] para assistência adicional para resolução de problemas.

Este artigo fornece passos de resolução de problemas para questões comuns em Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Não é possível ativar os Serviços de Domínio AD Azure para o seu diretório Azure AD

Se tiver problemas em permitir o Azure AD DS, reveja os seguintes erros e passos comuns para os resolver:

| **Mensagem de erro da amostra** | **Resolução** |
| --- |:--- |
| *O nome aaddscontoso.com já está a ser usado nesta rede. Especifique um nome que não esteja a ser utilizado.* |[Conflito de nome de domínio na rede virtual](troubleshoot.md#domain-name-conflict) |
| *Os Serviços de Domínio não podiam ser ativados neste inquilino da AD Azure. O serviço não dispõe de permissões adequadas à aplicação denominada 'Azure AD Domain Services Sync'. Elimine a aplicação chamada 'Azure AD Domain Services Sync' e tente ativar os Serviços de Domínio para o seu inquilino Azure AD.* |[Os Serviços de Domínio não têm permissões adequadas para a aplicação Desincronização de Serviços de Domínio Da AD Azure](troubleshoot.md#inadequate-permissions) |
| *Os Serviços de Domínio não podiam ser ativados neste inquilino da AD Azure. O pedido de Serviços de Domínio no seu inquilino Azure AD não tem as permissões necessárias para ativar os Serviços de Domínio. Elimine o pedido com o identificador de aplicação d87dcbc6-a371-462e-88e3-28ad15ec4e64 e tente permitir serviços de domínio para o seu inquilino Azure AD.* |[A aplicação De serviços de domínio não está configurada corretamente no seu inquilino Azure AD](troubleshoot.md#invalid-configuration) |
| *Os Serviços de Domínio não podiam ser ativados neste inquilino da AD Azure. A aplicação AD Microsoft Azure está desativada no seu inquilino Azure AD. Ative a aplicação com o identificador de aplicação 00000002-0000-0000-c000-00000000000000000000000000000e e, em seguida, tente ativar os Serviços de Domínio para o seu inquilino Azure AD.* |[A aplicação Microsoft Graph é desativada no seu inquilino Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflito de nome de domínio

**Mensagem de erro**

*O nome aaddscontoso.com já está a ser usado nesta rede. Especifique um nome que não esteja a ser utilizado.*

**Resolução**

Verifique se não tem um ambiente AD DS existente com o mesmo nome de domínio na mesma rede virtual, ou uma rede virtual esparsa. Por exemplo, pode ter um domínio AD DS chamado *aaddscontoso.com* que funciona em VMs Azure. Quando se tenta ativar um domínio gerido pelo Azure AD DS com o mesmo nome de domínio de *aaddscontoso.com* na rede virtual, a operação solicitada falha.

Esta falha deve-se a conflitos de nome para o nome de domínio na rede virtual. Um checkup DNS verifica se um ambiente AD DS existente responde sobre o nome de domínio solicitado. Para resolver esta falha, utilize um nome diferente para configurar o seu domínio gerido pelo Azure AD DS, ou desfornecer o domínio AD DS existente e, em seguida, tente novamente ativar o Azure AD DS.

### <a name="inadequate-permissions"></a>Permissões inadequadas

**Mensagem de erro**

*Os Serviços de Domínio não podiam ser ativados neste inquilino da AD Azure. O serviço não dispõe de permissões adequadas à aplicação denominada 'Azure AD Domain Services Sync'. Elimine a aplicação chamada 'Azure AD Domain Services Sync' e tente ativar os Serviços de Domínio para o seu inquilino Azure AD.*

**Resolução**

Verifique se existe uma aplicação chamada *Azure AD Domain Services Sync* no seu diretório Azure AD. Se esta aplicação existir, elimine-a e tente novamente ativar o Azure AD DS. Para verificar se existe uma aplicação existente e eliminá-la se necessário, complete os seguintes passos:

1. No portal Azure, selecione **Azure Ative Diretório** a partir do menu de navegação à esquerda.
1. Selecione **aplicações Enterprise**. Escolha *todas as aplicações* do menu drop-down do Tipo de **Aplicação** e, em seguida, selecione **Aplicar**.
1. Na caixa de pesquisa, introduza o *Azure AD Domain Services Sync*. Se a aplicação existir, selecione-a e escolha **Eliminar**.
1. Depois de ter apagado a aplicação, tente ativar novamente o Azure AD DS.

### <a name="invalid-configuration"></a>Configuração inválida

**Mensagem de erro**

*Os Serviços de Domínio não podiam ser ativados neste inquilino da AD Azure. O pedido de Serviços de Domínio no seu inquilino Azure AD não tem as permissões necessárias para ativar os Serviços de Domínio. Elimine o pedido com o identificador de aplicação d87dcbc6-a371-462e-88e3-28ad15ec4e64 e tente permitir serviços de domínio para o seu inquilino Azure AD.*

**Resolução**

Verifique se tem uma aplicação existente chamada *AzureActiveDirectoryDomainControllerServices* com um identificador de aplicação de *d87dcbc6-a371-462e-88e3-28ad15ec4e64* no seu diretório Azure AD. Se esta aplicação existir, elimine-a e tente novamente ativar o Azure AD DS.

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

*Os Serviços de Domínio não podiam ser ativados neste inquilino da AD Azure. A aplicação AD Microsoft Azure está desativada no seu inquilino Azure AD. Ative a aplicação com o identificador de aplicação 00000002-0000-0000-c000-00000000000000000000000000000e e, em seguida, tente ativar os Serviços de Domínio para o seu inquilino Azure AD.*

**Resolução**

Verifique se desativou uma aplicação com o identificador *00000002-0000-0000-c000-0000000000000000000000 .* Esta aplicação é a aplicação DaD Microsoft Azure e fornece acesso a API de Gráfico ao seu inquilino Azure AD. Para sincronizar o seu inquilino Azure AD, esta aplicação deve ser ativada.

Para verificar o estado desta aplicação e capacitá-la se necessário, complete os seguintes passos:

1. No portal Azure, selecione **Azure Ative Diretório** a partir do menu de navegação à esquerda.
1. Selecione **aplicações Enterprise**. Escolha *todas as aplicações* do menu drop-down do Tipo de **Aplicação** e, em seguida, selecione **Aplicar**.
1. Na caixa de pesquisa, insira *00000002-0000-0000-c000-00000000000000*. Selecione a aplicação e, em seguida, escolha **Propriedades**.
1. Se **ativado para que os utilizadores sessãom sintetizados,** desloque o valor para *Sim,* esese selecione **Save**.
1. Depois de ativar a aplicação, tente ativar novamente o Azure AD DS.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Os utilizadores não conseguem iniciar sessão no domínio gerido pelo Azure AD Domain Services

Se um ou mais utilizadores do seu inquilino Azure AD não puder empenar no domínio gerido pela Azure AD DS, complete os seguintes passos de resolução de problemas:

* **Formato de credenciais** - Tente utilizar o formato UPN para especificar credenciais, tais como `dee@aaddscontoso.onmicrosoft.com`. O formato UPN é a forma recomendada de especificar credenciais em DS AD Azure. Certifique-se de que esta UPN está corretamente configurada em Azure AD.

    O *Nome SAMAccountName* para a sua conta, como *AADDSCONTOSO\driley* pode ser autogerado se houver vários utilizadores com o mesmo prefixo UPN no seu inquilino ou se o seu prefixo UPN for excessivamente longo. Por isso, o formato *SAMAccountName* para a sua conta pode ser diferente do que espera ou utiliza no seu domínio no local.

* **Sincronização de palavras-passe** - Certifique-se de que ativou a sincronização de palavras-passe para [utilizadores apenas][cloud-only-passwords] na nuvem ou para [ambientes híbridos utilizando o Azure AD Connect][hybrid-phs].
    * **Contas híbridas sincronizadas:** Se as contas de utilizador afetadas forem sincronizadas a partir de um diretório no local, verifique as seguintes áreas:
    
      * Implementou, ou atualizou para a [mais recente versão recomendada do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
      * Configurou o Azure AD Connect para [realizar uma sincronização completa.][hybrid-phs]
      * Dependendo do tamanho do seu diretório, pode demorar algum tempo para que as contas dos utilizadores e hashes credenciais estejam disponíveis no Azure AD DS. Certifique-se de que espera o tempo suficiente antes de tentar autenticar contra o domínio gerido.
      * Se o problema persistir após verificar os passos anteriores, tente reiniciar o Serviço de *Sincronização AD*Microsoft Azure . A partir do seu servidor Azure AD Connect, abra um pedido de comando e execute os seguintes comandos:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * Contas exclusivas na nuvem : Se a conta de utilizador afetada for uma conta de utilizador exclusiva na nuvem, **certifique-se**de que o utilizador alterou a [sua palavra-passe depois de ativar o Azure AD DS][cloud-only-passwords]. Este reset de palavra-passe faz com que sejam gerados os hashes credenciais necessários para os Serviços de Domínio AD Azure.

* **Verifique se a conta de utilizador está ativa**: Por predefinição, cinco tentativas de senha inválidas dentro de 2 minutos no domínio gerido fazem com que uma conta de utilizador seja bloqueada durante 30 minutos. O utilizador não pode iniciar sessão enquanto a conta está bloqueada. Após 30 minutos, a conta de utilizador é automaticamente desbloqueada.
  * As tentativas de senha inválidas no domínio gerido pelo Azure AD DS não bloqueiam a conta de utilizador em Azure AD. A conta de utilizador está bloqueada apenas dentro do domínio gerido. Verifique o estado da conta de utilizador na *Consola Administrativa de Diretório Ativo (ADAC)* utilizando o VM de [gestão,][management-vm]não em Azure AD.
  * Também pode configurar políticas de [senha de grãos finos][password-policy] para alterar o limiar e duração do bloqueio predefinido.

* **Contas externas** - Verifique se a conta de utilizador afetada não é uma conta externa no inquilino da AD Azure. Exemplos de contas externas incluem contas da Microsoft como `dee@live.com` ou contas de utilizador de um diretório externo da AD Azure. O Azure AD DS não armazena credenciais para contas de utilizadores externos, pelo que não podem iniciar sessão no domínio gerido.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Há um ou mais alertas no seu domínio gerido

Se existirem alertas ativos no domínio gerido pelo Azure AD DS, poderá impedir que o processo de autenticação funcione corretamente.

Para ver se existem alertas ativos, [verifique o estado de saúde de um domínio gerido por Azure AD DS][check-health]. Se forem mostrados alertas, [resolva-os e resolva-os.][troubleshoot-alerts]

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Os utilizadores removidos do seu inquilino Azure AD não são removidos do seu domínio gerido

A AD Azure protege contra a eliminação acidental de objetos de utilizador. Ao eliminar uma conta de utilizador de um inquilino DaD Azure, o objeto de utilizador correspondente é transferido para o caixote do lixo de reciclagem. Quando esta operação de eliminação é sincronizada com o seu domínio gerido pelo Azure AD DS, a conta de utilizador correspondente está marcada como desativada. Esta funcionalidade ajuda-o a recuperar ou a eliminar a conta de utilizador.

A conta de utilizador permanece no estado dedesactivado no domínio gerido pelo Azure AD DS, mesmo que recrie uma conta de utilizador com a mesma UPN no diretório Azure AD. Para remover a conta de utilizador do domínio gerido pelo Azure AD DS, é necessário eliminá-la à força do inquilino Azure AD.

Para remover totalmente uma conta de utilizador de um domínio gerido pelo Azure AD DS, elimine o utilizador permanentemente do seu inquilino Azure AD utilizando o cmdlet [Remove-MsolUser][Remove-MsolUser] PowerShell com o parâmetro `-RemoveFromRecycleBin`.

## <a name="next-steps"></a>Passos seguintes

Se continuar a ter problemas, abra um pedido de [apoio azure][azure-support] para assistência adicional para resolução de problemas.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
