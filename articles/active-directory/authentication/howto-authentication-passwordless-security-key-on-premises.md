---
title: Chave de segurança sem palavras-passe sem palavras-passe para os recursos no local (pré-visualização) - Diretório Ativo Azure
description: Saiba como ativar o acesso à chave de segurança sem palavras-passe para os recursos no local utilizando o Diretório Ativo do Azure (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 181e8192170cd7394d6817edd655f4e8257b48a4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654047"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Ativar o acesso à chave de segurança sem palavras-passe para os recursos no local com o Diretório Ativo Azure (pré-visualização)

Este documento centra-se em permitir a autenticação sem palavras-passe para os recursos no local para ambientes com a **AD Azure aderiu** e **o Azure AD híbrido juntou-se a** dispositivos Windows 10. Esta funcionalidade fornece um único sinal de entrada (SSO) sem emenda aos recursos no local utilizando chaves de segurança compatíveis com a Microsoft.

|     |
| --- |
| As chaves de segurança FIDO2 são uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para mais informações sobre pré-visualizações, consulte [Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>SSO para recursos no local usando chaves FIDO2

O Azure Ative Directory (AD) pode emitir bilhetes de concessão de bilhetes Kerberos (TGTs) para um ou mais dos seus domínios de Diretório Ativo. Esta funcionalidade permite que os utilizadores assinem no Windows com credenciais modernas como chaves de segurança FIDO2 e acedam aos recursos tradicionais baseados em Directórioactivo. Os bilhetes de serviço Kerberos e a autorização continuam a ser controlados pelos seus controladores de domínio Ative Diretório no local.

Um objeto Azure AD Kerberos Server é criado no seu Diretório Ativo no local e, em seguida, publicado de forma segura para o Azure Ative Directory. O objeto não está associado a nenhum servidor físico. É simplesmente um recurso que pode ser usado pelo Azure Ative Directory para gerar TGTs Kerberos para o seu Domínio de Diretório Ativo.

![Obtenção de um bilhete de concessão de bilhetes (TGT) da Azure AD e DaD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. O utilizador insere-se no seu dispositivo Windows 10 com uma chave de segurança FIDO2 e autentica-se no Azure AD.
1. A Azure AD verifica o diretório de uma chave de servidor Kerberos que corresponda ao domínio AD do utilizador no local.
   1. A Azure AD gera um Kerberos TGT para o domínio ad da empresa no local. O TGT inclui apenas o SID do utilizador. Não estão incluídos dados de autorização no TGT.
1. O TGT é devolvido ao cliente juntamente com o seu Azure AD Primary Refresh Token (PRT).
1. A máquina cliente contacta um controlador de domínio AD no local e comercializa o TGT parcial para um TGT totalmente formado.
1. A máquina cliente tem agora um Azure AD PRT e um TGT de Diretório Ativo completo e pode aceder tanto à nuvem como aos recursos no local.

## <a name="requirements"></a>Requisitos

As organizações devem completar os passos para ativar o sinal de chave de [segurança sem palavras-passe para dispositivos Windows 10 (pré-visualização)](howto-authentication-passwordless-security-key.md) antes de completar os passos deste artigo.

As organizações também devem satisfazer os seguintes requisitos de software.

- Os dispositivos devem estar a executar o Windows 10 Insider Build 18945 ou mais recentes.
- Deve ter a versão 1.4.32.0 ou mais tarde do [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Para obter mais informações sobre as opções de autenticação híbrida Azure AD disponíveis, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](../../security/fundamentals/choose-ad-authn.md) e [selecione qual o tipo de instalação a utilizar para o Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md).
- Os controladores de domínio do Windows Server devem ter as seguintes correções instaladas:
    - Para o Windows Server 2016 -https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Para windows Server 2019 -https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Cenários suportados

O cenário suporta uma única inscrição (SSO) em ambos os seguintes cenários:

- Para recursos na nuvem como o Office 365 e outras aplicações ativadas pela SAML.
- Para recursos no local e autenticação integrada no Windows para sites web. Os recursos podem incluir web sites e sites SharePoint que requerem autenticação IIS, e/ou recursos que usam a autenticação NTLM.

### <a name="unsupported-scenarios"></a>Cenários não suportados

Os seguintes cenários não são suportados:

- O domínio dos Serviços de Domínio ativo do Windows Server (AD DS) juntou-se à implementação (apenas dispositivos no local).
- Cenários RDP, VDI e Citrix usando uma chave de segurança.
- S/MIME usando uma chave de segurança.
- "Corra como" usando uma chave de segurança.
- Inicie sessão num servidor utilizando a chave de segurança.

## <a name="create-kerberos-server-object"></a>Criar objeto de servidor Kerberos

Os administradores utilizam ferramentas PowerShell do seu servidor Azure AD Connect para criar um objeto Azure AD Kerberos Server no seu diretório no local. Execute os seguintes passos em cada domínio e floresta na sua organização que contenham utilizadores de AD Azure:

1. Atualize para a versão mais recente do Azure AD Connect. As instruções assumem que já configuraram o Azure AD Connect para suportar o seu ambiente híbrido.
1. No Azure AD Connect Server, abra um pedido de PowerShell elevado e navegue para`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Execute os seguintes comandos PowerShell para criar um novo objeto de servidor Azure AD Kerberos em ambos os seus domínios de Diretório Ativo no local e inquilino do Diretório Ativo Azure.

> [!NOTE]
> Substitua `contoso.corp.com` no seguinte exemplo o nome de domínio ative diretório no local.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Visualização e verificação do Servidor Azure AD Kerberos

Pode visualizar e verificar o recém-criado Servidor Azure AD Kerberos utilizando o seguinte comando:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Este comando produz as propriedades do Servidor Azure AD Kerberos. Pode rever as propriedades para verificar se está tudo em ordem.

| Propriedade | Descrição |
| --- | --- |
| ID | A identificação única do objeto AD DS DC. Esta identificação é por vezes referida como "slot" ou "ID da filial". |
| DomainDnsName | O nome de domínio DNS do Domínio do Diretório Ativo. |
| Conta informática | O objeto da conta do computador do objeto do Servidor Azure AD Kerberos (o DC). |
| Conta de utilizador | O objeto de conta de utilizador desativado que detém a chave de encriptação TGT do Azure AD AD Kerberos Server. O DN desta conta é`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| Versão-chave | A versão chave da chave de encriptação Azure AD Kerberos Server TGT. A versão é atribuída quando a chave é criada. A versão é então incrementada cada vez que a chave é girada. Os incrementos baseiam-se em meta-dados de replicação e provavelmente maiores do que um. Por exemplo, a versão inicial da *KeyVersion* pode ser *192272*. A primeira vez que a chave é girada, a versão pode avançar para *212621*. O importante a verificar é que a *Versão Chave* para o objeto no local e a *CloudKeyVersion* para o objeto de nuvem são as mesmas. |
| Keyupdatedon | A data e hora em que a chave de encriptação Azure AD Kerberos Server TGT foi atualizada ou criada. |
| KeyUpdated From | O DC onde a chave de encriptação Azure AD Kerberos Server TGT foi atualizada pela última vez. |
| Nuvem | A identificação do Objeto AD Azure. Deve combinar com a identificação acima. |
| CloudDomainDnsName | O *Nome de Domínio* do Objeto AD Azure. Deve coincidir com o *Nome de Domínio* acima. |
| Versão CloudKey | A *Versão Chave* do Objeto AD Azure. Deve corresponder à *Versão Chave* acima. |
| CloudKeyupdatedon | O *KeyUpdatedOn* do Objeto AD Azure. Deve coincidir com o *KeyUpdatedOn* acima. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Girando a chave Azure AD Kerberos Server

As teclas krbtgt de encriptação Azure AD Kerberos Server devem ser rodadas regularmente. Recomenda-se que siga o mesmo horário que usa para rodar todas as outras teclas krbtgt do Controlador de Domínio do Diretório Ativo.

> [!WARNING]
> Existem outras ferramentas que podem rodar as teclas krbtgt, no entanto, deve utilizar as ferramentas mencionadas neste documento para rodar as teclas krbtgt do seu Servidor Azure AD Kerberos. Isto garante que as chaves são atualizadas tanto no local AD como na Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Remoção do Servidor Azure AD Kerberos

Se quiser reverter o cenário e remover o Servidor Azure AD Kerberos tanto no local como no Diretório Ativo Azure, execute o seguinte comando:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Cenários multiflorestais e multi-domínios

O objeto do servidor Azure AD Kerberos está representado em Azure AD como um objeto *KerberosDomain.* Cada domínio de Diretório Ativo no local é representado como um único objeto *KerberosDomain* em Azure AD.

Por exemplo, a sua organização tem uma floresta `contoso.com` `fabrikam.com`de Diretório Ativo com dois domínios, e . Se optar por permitir que a AD Azure emita TGTs Kerberos para toda a floresta, existem dois objetos *KerberosDomain* em Azure AD. Um objeto *KerberosDomain* `contoso.com`para, `fabrikam.com`e um para . Se tiver várias florestas de Diretório Ativo, existe um objeto *KerberosDomain* para cada domínio em cada floresta.

Você precisa executar os passos para criar objeto de [servidor Kerberos](#create-kerberos-server-object) em cada domínio e floresta na sua organização que contenha utilizadores de Anúncios Azure.

## <a name="known-behavior"></a>Comportamento conhecido

O acesso ao FIDO está bloqueado se a sua palavra-passe tiver expirado. A expectativa é que o utilizador reinicie a sua palavra-passe antes de poder iniciar sessão utilizando o FIDO.

## <a name="troubleshooting-and-feedback"></a>Resolução de problemas e feedback

Se quiser partilhar problemas de feedback ou de encontro durante a pré-visualização desta funcionalidade, partilhe através da aplicação Windows Feedback Hub utilizando os seguintes passos:

1. Inicie o **Feedback Hub** e certifique-se de que está inscrito.
1. Envie feedback sob a seguinte categorização:
   - Categoria: Segurança e Privacidade
   - Subcategoria: FIDO
1. Para capturar registos, use a opção para **recriar** o meu Problema

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-this-work-in-my-on-premises-environment"></a>Isto funciona no meu ambiente no local?

Esta funcionalidade não funciona para um ambiente puro no local Ative Directory Domain Services (AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>A minha organização requer dois fatores de autenticação para aceder aos recursos. O que posso fazer para apoiar esta exigência?

As chaves de segurança vêm numa variedade de fatores de forma. Contacte o fabricante do dispositivo de interesse para discutir como os seus dispositivos podem ser ativados com um PIN ou biométrico como segundo fator.

### <a name="can-admins-set-up-security-keys"></a>Os administradores podem configurar as chaves de segurança?

Estamos a trabalhar nesta capacidade de disponibilidade geral (GA) desta funcionalidade.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Onde posso ir procurar chaves de segurança compatíveis?

[Chaves de segurança FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>O que faço se perder a chave de segurança?

Pode remover as chaves do portal Azure navegando para a página de **informações** de Segurança e removendo a chave de segurança.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Não posso usar o FIDO imediatamente depois de criar uma máquina híbrida azure ad

Se instalar uma máquina híbrida Azure AD, depois de o processo de adesão e reinício do domínio, deve iniciar sessão com uma palavra-passe e esperar que a política se sincronize antes de poder utilizar o FIDO para iniciar sessão.

- Verifique o estado `dsregcmd /status` atual digitando uma janela de comando e verifique se tanto *azureAdJoined* como *DomainJoined* estão mostrando *SIM*.
- Este atraso é uma limitação conhecida para dispositivos de domínio e não é específico do FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Não consigo levar o SSO ao meu recurso de rede NTLM depois de assinar com a FIDO e obter um pedido de credencial.

Certifique-se de que os controladores de domínio suficientes são remendados para responder a tempo de atender o seu pedido de recursos. Para verificar se consegue ver um controlador de domínio que `nltest /dsgetdc:contoso /keylist /kdc`está a executar a funcionalidade, reveja a saída de .

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre sem palavras-passe](concept-authentication-passwordless.md)
