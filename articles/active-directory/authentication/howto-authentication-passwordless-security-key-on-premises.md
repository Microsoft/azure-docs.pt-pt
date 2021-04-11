---
title: Entrada de chave de segurança sem palavras-passe para recursos no local - Azure Ative Directory
description: Saiba como ativar o acesso à chave de segurança sem palavras-passe para os recursos no local utilizando o Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef56db4ef67515d14f8462db2975e68a1a86f238
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959862"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory"></a>Ativar o acesso à chave de segurança sem palavras-passe para os recursos no local com o Azure Ative Directory 

Este documento centra-se em permitir a autenticação sem palavras-passe para recursos no local para ambientes com **a AZure AD ligado** e **híbrido Azure AD juntou-se a** dispositivos Windows 10. Esta funcionalidade fornece um único sign-on (SSO) sem emenda aos recursos no local utilizando chaves de segurança compatíveis com a Microsoft.

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>SSO para recursos no local usando chaves FIDO2

O Azure Ative Directory (AD) pode emitir bilhetes de concessão de bilhetes kerberos (TGTs) para um ou mais dos seus domínios de Diretório Ativo. Esta funcionalidade permite que os utilizadores assinem o Windows com credenciais modernas como chaves de segurança FIDO2 e acedam aos recursos tradicionais baseados no Ative Directory. Os bilhetes de serviço Kerberos e a autorização continuam a ser controlados pelos controladores de domínio ative diretório.

Um objeto Azure AD Kerberos Server é criado no seu Ative Directory e depois publicado de forma segura no Azure Ative Directory. O objeto não está associado a nenhum servidor físico. É simplesmente um recurso que pode ser usado pelo Azure Ative Directory para gerar TGTs Kerberos para o seu Domínio de Diretório Ativo.

![Obtenção de um bilhete de concessão de bilhete (TGT) da Azure AD e da AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. O utilizador assina no seu dispositivo Windows 10 com uma chave de segurança FIDO2 e autentica-se no AZure AD.
1. A Azure AD verifica o diretório de uma chave de servidor Kerberos correspondente ao domínio AD do utilizador no local.
   1. A Azure AD gera um Kerberos TGT para o domínio AD do utilizador no local. O TGT inclui apenas o SID do utilizador. Nenhum dado de autorização está incluído no TGT.
1. O TGT é devolvido ao cliente juntamente com o seu Azure AD Primary Refreshy Token (PRT).
1. A máquina cliente contacta um controlador de domínio AD no local e comercializa o TGT parcial por um TGT totalmente formado.
1. A máquina cliente tem agora um Azure AD PRT e um TGT completo do Ative Directory e pode aceder tanto aos recursos em nuvem como no local.

## <a name="requirements"></a>Requisitos

As organizações devem completar os passos para ativar o [sinal de chave de segurança sem palavras-passe para dispositivos Windows 10](howto-authentication-passwordless-security-key.md) antes de completar os passos neste artigo.

As organizações também devem cumprir os seguintes requisitos de software.

- Os dispositivos devem estar a executar a versão 10 do Windows 10 ou mais recente.
- Deve ter a versão 1.4.32.0 ou mais tarde do [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Para obter mais informações sobre as opções de autenticação híbrida Azure AD disponíveis, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](../hybrid/choose-ad-authn.md) e [Selecione qual o tipo de instalação a utilizar para Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md).
- Os controladores de domínio do Windows Server devem ter as seguintes correções instaladas:
    - Para Windows Server 2016 - https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Para Windows Server 2019 - https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Cenários suportados

O cenário suporta uma única sinstentação (SSO) em ambos os cenários seguintes:

- Para recursos em nuvem como o Microsoft 365 e outras aplicações ativadas pela SAML.
- Para recursos no local e Windows-Integrated autenticação para sites. Os recursos podem incluir sites web e sites SharePoint que requerem autenticação IIS e/ou recursos que utilizam a autenticação NTLM.

### <a name="unsupported-scenarios"></a>Cenários não suportados

Os seguintes cenários não são suportados:

- O domínio do Serviço de Domínio ativo do Windows Server (AD DS) uniu-se (apenas dispositivos no local).
- Cenários RDP, VDI e Citrix usando uma chave de segurança.
- S/MIME utilizando uma chave de segurança.
- "Corra como" usando uma chave de segurança.
- Faça login num servidor usando a chave de segurança.

## <a name="create-kerberos-server-object"></a>Criar objeto de servidor Kerberos

Os administradores utilizam ferramentas PowerShell do seu servidor Azure AD Connect para criar um objeto Azure AD Kerberos Server no seu diretório no local. Executar os seguintes passos em cada domínio e floresta na sua organização que contenham utilizadores AD Azure:

1. Upgrade para a versão mais recente do Azure AD Connect. As instruções pressupõem que já configuraste o Azure AD Connect para suportar o ambiente híbrido.
1. No Azure AD Connect Server, abra uma pressão de PowerShell elevada e navegue para `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Execute os seguintes comandos PowerShell para criar um novo objeto de servidor Azure AD Kerberos tanto no seu domínio de Ative Directory como no inquilino do Azure Ative Directory.

> [!NOTE]
> Substitua `contoso.corp.com` no exemplo seguinte pelo nome de domínio ative Directory no local.

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

Pode visualizar e verificar o recém-criado Azure AD Kerberos Server utilizando o seguinte comando:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Este comando produz as propriedades do Azure AD Kerberos Server. Pode rever as propriedades para verificar se está tudo em ordem.

| Propriedade | Description |
| --- | --- |
| ID | A identificação única do objeto DS DC. Esta identificação é por vezes referida como "slot" ou "branch ID". |
| DomainDnsName | O nome de domínio DNS do Domínio do Diretório Ativo. |
| Contagem de computadores | O objeto da conta do computador do objeto Azure AD Kerberos Server (o DC). |
| Contagem de utilizadores | O objeto de conta de utilizador desativado que detém a chave de encriptação Azure AD Kerberos Server TGT. O DN desta conta é `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| Chaveversão | A versão chave da chave de encriptação Azure AD Kerberos Server TGT. A versão é atribuída quando a chave é criada. A versão é então incrementada cada vez que a chave é rodada. Os incrementos são baseados em meta-dados de replicação e provavelmente maiores que um. Por exemplo, a versão inicial *da KeyVersion* pode ser *192272*. A primeira vez que a chave é girada, a versão pode avançar para *212621*. O importante a verificar é que a *Versão Chave* para o objeto no local e a *CloudKeyVersion* para o objeto de nuvem são as mesmas. |
| KeyUpdatedOn | A data e a hora em que a chave de encriptação Azure AD Kerberos Server TGT foi atualizada ou criada. |
| KeyUpdatedFrom | O DC onde a chave de encriptação Azure AD Kerberos Server TGT foi atualizada pela última vez. |
| CloudId | A identificação do AD Object Azure. Deve coincidir com a identificação acima. |
| CloudDomainDnsName | *O Nome DomainDns* do Azure AD Object. Deve coincidir com o *Nome DomainDns* acima. |
| CloudKeyVersion | A *Versão Chave* do Objeto AD Azure. Deve coincidir com a *Versão Chave acima.* |
| CloudKeyUpdatedOn | O *KeyUpdatedOn* do Azure AD Object. Deve coincidir com a *ChaveUpdatedOn* acima. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Rotação da chave do servidor Azure AD Kerberos

As teclas krbtgt de encriptação do Azure AD Kerberos Server devem ser rodadas regularmente. Recomenda-se que siga o mesmo horário que usa para rodar todas as outras teclas krbtgt do Controlador de Domínio do Diretório Ativo.

> [!WARNING]
> Existem outras ferramentas que podem rodar as teclas krbtgt, no entanto, deve utilizar as ferramentas mencionadas neste documento para rodar as teclas krbtgt do seu Azure AD Kerberos Server. Isto garante que as chaves são atualizadas tanto no local, aD como a Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Remoção do servidor Azure AD Kerberos

Se quiser reverter o cenário e remover o Servidor Azure AD Kerberos do Diretório Ativo e do Diretório Ativo Azure, executar o seguinte comando:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Cenários multi-floresta e multi-domínio

O objeto do servidor Azure AD Kerberos está representado no Azure AD como um objeto *KerberosDomain.* Cada domínio de Ative Directory no local é representado como um único objeto *KerberosDomain* em Azure AD.

Por exemplo, a sua organização tem uma floresta de Diretório Ativo com dois domínios, `contoso.com` e `fabrikam.com` . Se optar por permitir que a Azure AD emita TGTs Kerberos para toda a floresta, existem dois objetos *KerberosDomain* em Azure AD. Um objeto *KerberosDomain* para `contoso.com` , e um para `fabrikam.com` . Se tiver várias florestas de Ative Directory, existe um objeto *KerberosDomain* para cada domínio em cada floresta.

Você precisa executar os passos para [criar objeto de servidor Kerberos](#create-kerberos-server-object) em cada domínio e floresta na sua organização que contenha utilizadores AD Azure.

## <a name="known-behavior"></a>Comportamento conhecido

O súbb insi por conta de baixo da fido está bloqueado se a sua palavra-passe tiver expirado. A expectativa é que o utilizador reponha a sua palavra-passe antes de poder iniciar sessão usando o FIDO.

## <a name="troubleshooting-and-feedback"></a>Resolução de problemas e feedback

Se quiser partilhar feedback ou encontrar problemas com esta funcionalidade, partilhe através da aplicação Windows Feedback Hub utilizando os seguintes passos:

1. Lance **o Feedback Hub** e certifique-se de que está assinado.
1. Enviar feedback ao abrigo da seguinte categorização:
   - Categoria: Segurança e Privacidade
   - Subcategoria: FIDO
1. Para capturar registos, utilize a opção para **recriar o meu problema.**

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-this-work-in-my-on-premises-environment"></a>Isto funciona no meu ambiente no local?

Esta funcionalidade não funciona para um ambiente puro no local Ative Directory Domain Services (AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>A minha organização requer dois fatores de autenticação para aceder aos recursos. O que posso fazer para apoiar este requisito?

As chaves de segurança vêm numa variedade de fatores de forma. Contacte o fabricante do dispositivo de interesse para discutir como os seus dispositivos podem ser ativados com um PIN ou biométrico como segundo fator.

### <a name="can-admins-set-up-security-keys"></a>Os administradores podem configurar as chaves de segurança?

Estamos a trabalhar nesta capacidade para a disponibilidade geral (GA) desta funcionalidade.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Onde posso encontrar chaves de segurança compatíveis?

[Chaves de segurança FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>O que faço se perder a chave de segurança?

Pode remover as chaves do portal Azure navegando na página **de informações de Segurança** e removendo a chave de segurança.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Não posso usar o FIDO imediatamente depois de criar uma máquina híbrida Azure AD

Se limpar a instalação de uma máquina híbrida Azure AD, após o processo de união e reinicialização do domínio, tem de iniciar sedível com uma palavra-passe e aguardar que a política se sincronize antes de poder utilizar o FIDO para iniciar sing.

- Verifique o seu estado atual digitando `dsregcmd /status` uma janela de comando e verifique se tanto o *AzureAdJoined* como *o DomainJoined* estão a mostrar *SIM*.
- Este atraso é uma limitação conhecida para dispositivos de união de domínios e não é específico do FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Não consigo levar o SSO ao meu recurso de rede NTLM depois de assinar com o FIDO e obter uma solicitação de credencial.

Certifique-se de que controladores de domínio suficientes estão remendados para responder a tempo para atender o seu pedido de recursos. Para verificar se consegue ver um controlador de domínio que está a executar a funcionalidade, reveja a saída de `nltest /dsgetdc:contoso /keylist /kdc` .

Nota: Este interruptor /Keylist no comando nltest pode ser encontrado a partir do computador windows 10 v2004 e superior

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre sem palavras-passe](concept-authentication-passwordless.md)
