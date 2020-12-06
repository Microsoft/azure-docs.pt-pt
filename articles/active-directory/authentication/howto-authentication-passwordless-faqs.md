---
title: FAQs para implantação híbrida de chaves de segurança FIDO2 - Azure Ative Directory
description: Saiba mais sobre algumas perguntas frequentes para o teste de segurança híbrido sem palavra-passe FIDO2 utilizando o Azure Ative Directory (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98cb990ede7c4d6e261bba05b0b8c97d758e6c32
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743535"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad-preview"></a>Implementação frequentemente feita perguntas (FAQs) para chaves de segurança híbridas FIDO2 em Azure AD (pré-visualização)

Este artigo abrange a implementação de perguntas frequentes (FAQs) para dispositivos híbridos Azure AD unidos e acesso sem palavra-passe a recursos on-prem. Com esta funcionalidade sem palavras-passe, pode ativar a autenticação AD do Azure em dispositivos do Windows 10 para dispositivos híbridos Azure AD ligados utilizando chaves de segurança FIDO2. Os utilizadores podem assinar no Windows nos seus dispositivos com credenciais modernas como chaves FIDO2 e aceder aos tradicionais Serviços de Domínio do Diretório Ativo (AD DS) com uma experiência de assinatura única (SSO) sem emenda aos seus recursos on-prem.

São suportados os seguintes cenários para os utilizadores num ambiente híbrido:

* Inscreva-se em dispositivos híbridos Azure AD que utilizem chaves de segurança FIDO2 e obtenha acesso SSO a recursos on-prem.
* Inscreva-se em dispositivos ligados ao Azure AD utilizando chaves de segurança FIDO2 e obtenha acesso SSO a recursos on-prem.

Para começar com as chaves de segurança FIDO2 e acesso híbrido aos recursos no local, consulte os seguintes artigos:

* [Chaves de segurança FIDO2 sem palavra-passe](howto-authentication-passwordless-security-key.md)
* [Windows 10 sem palavra-passe](howto-authentication-passwordless-security-key-windows.md)
* [Sem palavra-passe no local](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> As chaves de segurança FIDO2 são uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="security-keys"></a>Chaves de segurança

* [A minha organização requer dois fatores de autenticação para aceder aos recursos. O que posso fazer para apoiar este requisito?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [Onde posso encontrar as chaves de segurança FIDO2 compatíveis?](#where-can-i-find-compliant-fido2-security-keys)
* [O que faço se perder a chave de segurança?](#what-if-i-lose-my-security-key)
* [Como estão os dados protegidos na chave de segurança FIDO2?](#how-is-the-data-protected-on-the-fido2-security-key)
* [Como funciona o registo das chaves de segurança FIDO2?](#how-does-the-registering-of-fido2-security-keys-work)
* [Existe uma forma de os administradores providenciarem diretamente as chaves para os utilizadores?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>A minha organização requer autenticação multi-factor para aceder aos recursos. O que posso fazer para apoiar este requisito?

As chaves de segurança FIDO2 vêm em uma variedade de fatores de forma. Contacte o fabricante do dispositivo de interesse para discutir como os seus dispositivos podem ser ativados com um PIN ou biométrico como segundo fator. Para obter uma lista de fornecedores apoiados, consulte [os fornecedores de chaves de segurança FIDO2](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>Onde posso encontrar as chaves de segurança FIDO2 compatíveis?

Para obter uma lista de fornecedores apoiados, consulte [os fornecedores de chaves de segurança FIDO2](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="what-if-i-lose-my-security-key"></a>E se eu perder a chave de segurança?

Pode remover as chaves no portal Azure navegando na página **de informações de Segurança** e removendo a chave de segurança FIDO2.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>Como estão os dados protegidos na chave de segurança FIDO2?

As chaves de segurança FIDO2 têm enclaves seguros que protegem as chaves privadas armazenadas nelas. Uma chave de segurança FIDO2 também tem propriedades anti-martelar incorporadas, como no Windows Hello, onde não é possível extrair a chave privada.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>Como funciona o registo das chaves de segurança FIDO2?

Para obter mais informações sobre como registar e utilizar as teclas de segurança FIDO2, consulte [ativar o sinal de entrada na chave de segurança sem palavras-passe](howto-authentication-passwordless-security-key.md).

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>Existe uma forma de os administradores providenciarem diretamente as chaves para os utilizadores?

Não, não neste momento.

## <a name="prerequisites"></a>Pré-requisitos

* [Esta funcionalidade funciona se não houver conectividade na Internet?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Quais são os pontos finais específicos que são necessários para serem abertos ao Azure AD?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Como identifico o tipo de junção de domínio (Azure AD juntou-se ou híbrido Azure AD) para o meu dispositivo Windows 10?](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [Qual é a recomendação sobre o número de DCs que deve ser remendado?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [Posso colocar o fornecedor de credenciais FIDO2 num dispositivo apenas no local?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [O sinal de segurança fido2 não está a funcionar para o meu Administrador de Domínio ou outras contas de alto privilégio. Porquê?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>Esta funcionalidade funciona se não houver conectividade na Internet?

A conectividade na Internet é um pré-requisito para ativar esta funcionalidade. A primeira vez que um utilizador assina a utilização de chaves de segurança FIDO2, deve ter conectividade com a Internet. Para eventos de entrada subsequentes, o sent-in em cache deve funcionar e deixar o utilizador autenticar sem conectividade com a Internet.

Para uma experiência consistente, certifique-se de que os dispositivos têm acesso à Internet e linha de visão para DCs.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Quais são os pontos finais específicos que são necessários para serem abertos ao Azure AD?

São necessários os seguintes pontos finais para o registo e a autenticação:

* **.microsoftonline.com*
* **.microsoftonline-p.com*
* **.msauth.net*
* **.msauthimages.net*
* **.msecnd.net*
* **.msftauth.net*
* **.msftauthimages.net*
* **.phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

Para obter uma lista completa dos pontos finais necessários para utilizar os produtos online da Microsoft, consulte [as gamas de endereços URLs e IP do Office 365](/microsoft-365/enterprise/urls-and-ip-address-ranges).

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Como identifico o tipo de junção de domínio (Azure AD juntou-se ou híbrido Azure AD) para o meu dispositivo Windows 10?

Para verificar se o dispositivo cliente do Windows 10 tem o tipo de ligação de domínio certo, utilize o seguinte comando:

```console
Dsregcmd/status
```

A seguinte saída da amostra mostra que o dispositivo é Azure AD aderido à medida que *a AzureADJoined* está definida para *SIM*:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

A seguinte saída da amostra mostra que o dispositivo é híbrido Azure AD unidos como *DomainedJoined* também está definido para *SIM*. O *Nome de Domínio* também é mostrado:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

Num controlador de domínio Do Windows Server 2016 ou 2019, verifique se são aplicados os seguintes patches. Se necessário, executar o Windows Update para instalá-los:

* Windows Server 2016 - [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 - [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

A partir de um dispositivo cliente, executar o seguinte comando para verificar a conectividade a um controlador de domínio apropriado com as correções instaladas:

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>Qual é a recomendação sobre o número de DCs que deve ser remendado?

Recomendamos corrigir a maioria dos controladores de domínio do Windows Server 2016 ou 2019 com o patch para garantir que podem lidar com a carga de pedido de autenticação da sua organização.

Num controlador de domínio Do Windows Server 2016 ou 2019, verifique se são aplicados os seguintes patches. Se necessário, executar o Windows Update para instalá-los:

* Windows Server 2016 - [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 - [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>Posso colocar o fornecedor de credenciais FIDO2 num dispositivo apenas no local?

Não, esta funcionalidade não é suportada apenas para dispositivos no local. O fornecedor de credenciais FIDO2 não apareceu.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>O sinal de segurança fido2 não está a funcionar para o meu Administrador de Domínio ou outras contas de alto privilégio. Porquê?

A política de segurança por defeito não concede permissão à Azure AD para assinar contas de alto privilégio em recursos no local.

Para desbloquear as contas, utilize **utilizadores e computadores de Diretório Ativo** para modificar a propriedade *msDS-NeverRevealGroup* do *objeto de computador Azure AD Kerberos (CN=AzureADKerberos,OU=Controladores de domínio, \<domain-DN> )*.

## <a name="under-the-hood"></a>Os bastidores

* [Como é que o Azure AD Kerberos está ligado ao meu ambiente de Serviços de Domínio de Diretório Ativo no local?](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [Onde posso ver estes objetos do servidor Kerberos que são criados em AD e publicados em Azure AD?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [Por que não podemos ter a chave pública registada no local para que não haja dependência na internet?](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Como estão as chaves giradas no objeto do servidor Kerberos?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Por que precisamos do Azure AD Connect? Ele escreve alguma informação para a AD DS da Azure AD?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [Como é o pedido/resposta HTTP ao solicitar o PRT+ TGT parcial?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Como é que o Azure AD Kerberos está ligado ao meu ambiente de Serviços de Domínio de Diretório Ativo no local?

Existem duas partes - o ambiente AD DS no local, e o inquilino AD AZure.

**Serviços de Domínio do Diretório Ativo (DS AD)**

O servidor Azure AD Kerberos está representado num ambiente AD DS no local como um objeto controlador de domínio (DC). Este objeto DC é composto por vários objetos:

* *CN=AzureADKerberos,OU=Controladores de Domínio,\<domain-DN>*
    
    Um objeto *de computador* que representa um controlador de domínio Read-Only (RODC) em DS AD. Não há computador associado a este objeto. Em vez disso, é uma representação lógica de um DC.

* *CN=krbtgt_AzureAD,CN=Utilizadores,\<domain-DN>*

    Um objeto *de utilizador* que representa uma chave de encriptação de bilhete de concessão de bilhetes RODC Kerberos (TGT).

* *CN=900274c4-b7d2-43c8-90ee-00a9f650e335,CN=AzureAD,CN=Sistema,\<domain-DN>*

    Um objeto *ServiceConnectionPoint* que armazena metadados sobre os objetos do Servidor Azure AD Kerberos. As ferramentas administrativas utilizam este objeto para identificar e localizar os objetos do Servidor Azure AD Kerberos.

**Azure Active Directory**

O Azure AD Kerberos Server está representado no Azure AD como um objeto *KerberosDomain.* Cada ambiente AD DS no local é representado como um único objeto *KerberosDomain* no inquilino AZure AD.

Por exemplo, pode ter uma floresta AD DS com dois domínios como *contoso.com* e *fabrikam.com*. Se permitir que a Azure AD emita bilhetes de concessão de bilhetes Kerberos (TGTs) para toda a floresta, existem dois objetos *KerberosDomain* em Azure AD - um objeto para *contoso.com* e um para *fabrikam.com.*

Se tiver várias florestas de DS AD, tem um objeto *KerberosDomain* para cada domínio em cada floresta.

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>Onde posso ver estes objetos do servidor Kerberos que são criados em DS AD e publicados em Azure AD?

Para visualizar todos os objetos, utilize os cmdlets PowerShell do servidor Azure AD Kerberos incluídos na versão mais recente do Azure AD Connect.

Para obter mais informações, incluindo instruções sobre como ver os objetos, consulte [criar objetos do servidor Kerberos](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object).

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>Por que não podemos ter a chave pública registada no local para que não haja dependência na internet?

Recebemos feedback em torno da complexidade do modelo de implementação do Windows Hello for Business, por isso quisemos simplificar o modelo de implementação sem ter de usar certificados e o PKI (o FIDO2 não utiliza certificados).

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Como estão as chaves giradas no objeto do servidor Kerberos?

Como qualquer outro DC, as chaves *krbtgt de* encriptação do Azure AD Kerberos Server devem ser rodadas regularmente. Recomenda-se seguir o mesmo horário que utiliza para rodar todas as outras teclas *de krbtgt AD DS.*

> [!NOTE]
> Embora existam outras ferramentas para rodar as teclas *krbtgt,* deve [utilizar os cmdlets PowerShell para rodar as teclas *krbtgt*](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) do seu Azure AD Kerberos Server. Este método assegura que as teclas sejam atualizadas tanto no ambiente AD DS no local como no Azure AD.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Por que precisamos do Azure AD Connect? Ele escreve alguma informação para a AD DS da Azure AD?

O Azure AD Connect não responde à informação de Azure AD para AD DS. O utilitário inclui o módulo PowerShell para criar o Objeto de Servidor Kerberos em DS AD e publicá-lo em Azure AD.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>Como é o pedido/resposta HTTP ao solicitar o PRT+ TGT parcial?

O pedido HTTP é um pedido padrão de Token (PRT) de Atualização Primária. Este pedido de PRT inclui uma reclamação indicando que é necessário um bilhete de concessão de bilhete kerberos (TGT).

| Afirmação | Valor | Descrição                             |
|-------|-------|-----------------------------------------|
| tgt   | true  | A reclamação indica que o cliente precisa de um TGT. |

A Azure AD combina a chave de cliente encriptada e o tampão de mensagens na resposta prt como propriedades adicionais. A carga útil é encriptada utilizando a tecla de sessão do Dispositivo AD Azure.

| Campo              | Tipo   | Description  |
|--------------------|--------|--------------|
| tgt_client_key     | string | Base64 codificada chave do cliente (segredo). Esta chave é o segredo do cliente usado para proteger o TGT. Neste cenário sem palavra-passe, o segredo do cliente é gerado pelo servidor como parte de cada pedido de TGT e depois devolvido ao cliente na resposta. |
| tgt_key_type       | int    | O tipo de chave AD DS no local utilizado tanto para a chave do cliente como para a chave de sessão Kerberos incluída no KERB_MESSAGE_BUFFER. |
| tgt_message_buffer | string | Base64 codificada KERB_MESSAGE_BUFFER. |

## <a name="next-steps"></a>Passos seguintes

Para começar com as chaves de segurança FIDO2 e acesso híbrido aos recursos no local, consulte os seguintes artigos:

* [Chaves de segurança FIDO2 sem palavra-passe](howto-authentication-passwordless-security-key.md)
* [Windows 10 sem palavra-passe](howto-authentication-passwordless-security-key-windows.md)
* [Sem palavra-passe no local](howto-authentication-passwordless-security-key-on-premises.md)
