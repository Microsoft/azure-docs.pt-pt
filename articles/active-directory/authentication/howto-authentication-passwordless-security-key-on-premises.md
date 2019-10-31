---
title: Chave de segurança sem senha entrar em recursos locais (versão prévia)-Azure Active Directory
description: Habilitar a entrada de chave de segurança sem senha para recursos locais (versão prévia)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c42bb1e76d854723c8a88e9c3e2c104464beb0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164864"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-preview"></a>Habilitar a entrada de chave de segurança sem senha para recursos locais (versão prévia)

Este documento se concentra em habilitar a autenticação sem senha para recursos locais para ambientes com dispositivos Windows 10 ingressados no **Azure ad** e no **Azure ad híbrido** . Essa funcionalidade fornece SSO (logon único) contínuo para recursos locais usando chaves de segurança compatíveis com a Microsoft.

|     |
| --- |
| As chaves de segurança do FIDO2 são um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre visualizações, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>SSO para recursos locais usando chaves FIDO2

O Azure Active Directory (AD) pode emitir tíquetes de concessão de tíquete Kerberos (TGTs) para um ou mais dos seus domínios de Active Directory. Essa funcionalidade permite que os usuários entrem no Windows com credenciais modernas, como chaves de segurança FIDO2, acessem recursos tradicionais baseados em Active Directory. Tíquetes e autorização do serviço Kerberos continuarão a ser controlados por seus controladores de domínio Active Directory locais.

Um objeto de servidor Kerberos do Azure AD será criado em seu Active Directory local e, em seguida, será publicado com segurança no Azure Active Directory. O objeto não está associado a nenhum servidor físico. É simplesmente um recurso que pode ser usado por Azure Active Directory para gerar tíquetes de concessão de tíquetes Kerberos (TGTs) para seu Domínio do Active Directory.

![Obtendo um TGT e um PRT do Azure AD e AD DS](./media/howto-authentication-passwordless-on-premises/fido2-tgt-exchange-process.png)

1. O usuário entra em seu dispositivo Windows 10 com uma chave de segurança FIDO2 e se autentica no Azure AD.
1. O Azure AD verifica o diretório em busca de uma chave de servidor Kerberos correspondente ao domínio do AD local do usuário.
   1. O Azure AD gera um TGT Kerberos para o domínio do AD local do usuário. O TGT inclui apenas o SID do usuário. Nenhum dado de autorização está incluído no TGT.
1. O TGT é retornado ao cliente junto com o seu PRT (token de atualização principal) do Azure AD.
1. O computador cliente entra em contato com um controlador de domínio do AD local e troca o TGT parcial por um TGT totalmente formado.
1. O computador cliente agora tem um PRT do Azure AD e um TGT Active Directory completo e pode acessar recursos de nuvem e locais.

## <a name="requirements"></a>Requisitos

As organizações devem concluir as etapas no artigo [habilitar o sinal de chave de segurança sem senha para dispositivos Windows 10 (versão prévia)](howto-authentication-passwordless-security-key.md) antes de concluir as etapas neste artigo.

As organizações devem atender aos seguintes requisitos de software.

- Os dispositivos devem estar executando o Windows 10 Insider Build 18945 ou mais recente
- Atualizar para a versão mais recente do [Azure ad Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)
- Controladores de domínio do Windows Server 2016/2019 totalmente corrigidos para lidar com a carga de solicitação de autenticação local.

### <a name="supported-scenarios"></a>Cenários suportados

- O cenário dá suporte ao SSO (logon único) para ambos:
   - Recursos de nuvem como o Office 365 e outros aplicativos habilitados para SAML.
   - Recursos locais e autenticação integrada do Windows para sites da Web, incluindo sites da Web e sites do SharePoint que exigem autenticação IIS e/ou recursos que usam autenticação NTLM.

### <a name="unsupported-scenarios"></a>Cenários sem suporte

Não há suporte para os seguintes cenários:

- **Não há suporte para**a implantação do Windows Server Active Directory Domain Services (AD DS) ingressada no domínio (dispositivos somente locais).
- Os cenários RDP, VDI e Citrix **não têm suporte** usando a chave de segurança.
- **Não há suporte para** S/MIME usando a chave de segurança.
- **Não há suporte para** "executar como" usando a chave de segurança.
- **Não há suporte**para fazer logon em um servidor usando a chave de segurança.

## <a name="create-kerberos-server-object"></a>Criar objeto de servidor Kerberos

Os administradores usarão as ferramentas do PowerShell de seu servidor Azure AD Connect para criar um objeto de servidor Kerberos do Azure AD em seu diretório local. Você precisará executar essas etapas em cada domínio e floresta em sua organização que contenha usuários do Azure AD.

1. Atualize para a versão mais recente do Azure AD Connect. As instruções pressupõem que você já tenha configurado Azure AD Connect para dar suporte ao seu ambiente híbrido.
1. No Azure AD Connect Server, abra um prompt do PowerShell com privilégios elevados e navegue até C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\
1. Execute os comandos do PowerShell a seguir para criar um novo objeto de servidor Kerberos do Azure AD no domínio Active Directory local e Azure Active Directory locatário.

> [!NOTE]
> Substitua "contoso.corp.com" no exemplo a seguir pelo nome de domínio Active Directory local.

```PowerShell
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

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Exibindo e verificando o servidor Kerberos do Azure AD

Você pode exibir e verificar o servidor Kerberos do Azure AD criado recentemente usando o comando a seguir. 

```PowerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Esse comando produzirá as propriedades do servidor Kerberos do Azure AD. Você pode examinar as propriedades para verificar se tudo está em uma ordem boa.

| Propriedade | Descrição |
| --- | --- |
| ID | A ID exclusiva do objeto do controlador de domínio do AD. Às vezes, essa ID é chamada de "slot" ou é "ID do Branch". |
| DomainDnsName | O nome de domínio DNS do Domínio do Active Directory. |
| ComputerAccount | O objeto de conta de computador do objeto de servidor Kerberos do Azure AD (o DC). |
| UserAccount | O objeto de conta de usuário desabilitado que contém a chave de criptografia de TGT do servidor Kerberos do Azure AD. O DN dessa conta será: <br> CN = krbtgt_AzureAD, CN = Users, < Domain-DN > |
| Keyversion | A versão de chave da chave de criptografia de TGT do servidor Kerberos do Azure AD. A versão é atribuída quando a chave é criada. Em seguida, a versão é incrementada toda vez que a chave é girada. Os incrementos são baseados nos metadados de replicação e provavelmente serão maiores que um. Por exemplo, a keyversion inicial poderia ser 192272. Na primeira vez que a chave é girada, a versão pode avançar para 212621. A coisa importante a ser verificada é que o keyversion para o objeto local e o CloudKeyVersion para o objeto Cloud são os mesmos. |
| Atualização de | A data e a hora em que a chave de criptografia de TGT do servidor Kerberos do Azure AD foi atualizada/criada. |
| KeyUpdatedFrom | O controlador de domínio em que a chave de criptografia de TGT do servidor Kerberos do AD foi atualizada pela última vez. |
| CloudId | A ID do objeto do Azure AD. Deve corresponder à ID acima. |
| CloudDomainDnsName | O DomainDnsName do objeto do Azure AD. Deve corresponder ao DomainDnsName acima. |
| CloudKeyVersion | A keyversion do objeto do Azure AD. Deve corresponder à keyversion acima. |
| CloudKeyUpdatedOn | A atualização do objeto do Azure AD. Deve corresponder ao keyupdated acima. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Girando a chave do servidor Kerberos do Azure AD

As chaves krbtgt de criptografia de servidor Kerberos do Azure AD devem ser giradas regularmente. É recomendável que você siga o mesmo agendamento usado para girar todas as outras chaves krbtgt do controlador de Domínio do Active Directory.

> [!WARNING]
> Há outras ferramentas que podem girar as chaves krbtgt, no entanto, você deve usar as ferramentas mencionadas neste documento para girar as chaves krbtgt do seu servidor Kerberos do Azure AD. Isso garante que as chaves sejam atualizadas no AD local e no Azure AD.

```PowerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Removendo o servidor Kerberos do Azure AD

Se você quiser reverter o cenário e remover o servidor Kerberos do Azure AD do Active Directory local e Azure Active Directory, execute o comando a seguir.  

```PowerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Cenários de várias florestas e vários domínios

O objeto de servidor Kerberos do Azure AD é representado no Azure AD como um objeto KerberosDomain. Cada domínio de Active Directory local é representado como um único objeto KerberosDomain no Azure AD.

Por exemplo, sua organização tem uma floresta Active Directory com dois domínios, contoso.com e fabrikam.com. Se você optar por permitir que o Azure AD emita tíquetes de concessão de tíquete Kerberos (TGT) para toda a floresta, haverá dois objetos KerberosDomain no Azure AD. Um objeto KerberosDomain para contoso.com e outro para fabrikam.com. Se você tiver várias florestas Active Directory, terá um objeto KerberosDomain para cada domínio em cada floresta.

Você precisará executar as etapas em [criar objeto de servidor Kerberos](#create-kerberos-server-object) em cada domínio e floresta em sua organização que contenha usuários do Azure AD.

## <a name="known-behavior"></a>Comportamento conhecido

O logon com FIDO será bloqueado se sua senha tiver expirado. A expectativa é para que o usuário Redefina sua senha antes de poder fazer logon usando o FIDO.

## <a name="troubleshooting-and-feedback"></a>Solução de problemas e comentários

Se você quiser compartilhar comentários ou encontrar problemas ao visualizar esse recurso, compartilhe por meio do aplicativo Hub de comentários do Windows.

1. Inicie o **Hub de comentários** e verifique se você está conectado.
1. Envie comentários sob a seguinte categorização:
   1. Categoria: segurança e privacidade
   1. Subcategoria: FIDO
1. Para capturar logs, use a opção: **recriar meu problema**

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-this-work-in-my-on-premises-environment"></a>Isso funciona em meu ambiente local?

Esse recurso não funciona para um ambiente de Active Directory Domain Services (AD DS) local puro.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Minha organização requer autenticação de dois fatores para acessar recursos, o que posso fazer para dar suporte a esse requisito?

As chaves de segurança vêm em uma variedade de fatores forma. Entre em contato com o fabricante do dispositivo de interesse para discutir como seus dispositivos podem ser habilitados com um PIN ou biométrica como um segundo fator.

### <a name="can-admins-set-up-security-keys"></a>Os administradores podem configurar as chaves de segurança?

Estamos trabalhando nessa funcionalidade para GA (disponibilidade geral) desse recurso.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Onde posso encontrar as chaves de segurança em conformidade?

[Chaves de segurança do FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>O que devo fazer se perder minha chave de segurança?

Você pode remover chaves do portal do Azure, navegando até a página informações de segurança e removendo a chave de segurança.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Não consigo usar o FIDO imediatamente depois de criar um computador ingressado no Azure AD híbrido

Se limpar a instalação de um computador ingressado no Azure AD híbrido, poste o ingresso no domínio e reinicie você deve entrar com uma senha e aguardar a sincronização da política antes de poder usar o FIDO para entrar.

- Verifique seu status atual digitando `dsregcmd /status` em uma janela de comando e verifique se `AzureAdJoined` e `DomainJoined` estão mostrando `YES`.
- Esse atraso é uma limitação conhecida para dispositivos ingressados no domínio e não FIDO específico.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Não consigo obter o SSO para meu recurso de rede NTLM depois de entrar com FIDO e obter uma solicitação de credencial

Certifique-se de que controladores de domínio suficientes sejam corrigidos para responder no tempo de manutenção de sua solicitação de recurso. Para verificar se você pode ver um controlador de domínio que está executando o recurso, verifique a saída de nltest/dsgetdc: contoso/KeyList/KDC.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre senha](concept-authentication-passwordless.md)
