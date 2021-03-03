---
title: Problemas conhecidos e resolução de problemas para chaves de segurança híbridas FIDO2 - Azure Ative Directory
description: Conheça alguns problemas e formas de resolução de problemas híbridos fido2 chave de segurança híbrido usando O Diretório Ativo Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 435b27255ce58a3541d6b0d3a76bdf4080aa3962
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648785"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad"></a>Resolução de problemas para implantações híbridas de chaves de segurança FIDO2 em Azure AD 

Este artigo abrange perguntas frequentes para dispositivos híbridos Azure AD unidos e entrada sem palavra-passe para recursos on-prem. Com esta funcionalidade sem palavras-passe, pode ativar a autenticação AD do Azure em dispositivos do Windows 10 para dispositivos híbridos Azure AD ligados utilizando chaves de segurança FIDO2. Os utilizadores podem assinar no Windows nos seus dispositivos com credenciais modernas como chaves FIDO2 e aceder aos tradicionais Serviços de Domínio do Diretório Ativo (AD DS) com uma experiência de assinatura única (SSO) sem emenda aos seus recursos on-prem.

São suportados os seguintes cenários para os utilizadores num ambiente híbrido:

* Inscreva-se em dispositivos híbridos Azure AD que utilizem chaves de segurança FIDO2 e obtenha acesso SSO a recursos on-prem.
* Inscreva-se em dispositivos ligados ao Azure AD utilizando chaves de segurança FIDO2 e obtenha acesso SSO a recursos on-prem.

Para começar com as chaves de segurança FIDO2 e acesso híbrido aos recursos no local, consulte os seguintes artigos:

* [Chaves de segurança sem palavra-passe](howto-authentication-passwordless-security-key.md)
* [Windows 10 sem palavra-passe](howto-authentication-passwordless-security-key-windows.md)
* [Sem palavra-passe no local](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="known-issues"></a>Problemas conhecidos

* [Os utilizadores não conseguem iniciar sação usando as teclas de segurança FIDO2, uma vez que o Windows Hello Face é demasiado rápido e é o mecanismo de inscrição padrão](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [Os utilizadores não podem usar chaves de segurança FIDO2 imediatamente após criarem uma máquina híbrida Azure AD](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [Os utilizadores não conseguem obter SSO no meu recurso de rede NTLM depois de iniciar sessão com uma chave de segurança FIDO2 e receber uma solicitação de credencial](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>Os utilizadores não conseguem iniciar sação usando as teclas de segurança FIDO2, uma vez que o Windows Hello Face é demasiado rápido e é o mecanismo de inscrição padrão

O Windows Hello Face é a melhor experiência pretendida para um dispositivo onde um utilizador está matriculado. As chaves de segurança FIDO2 destinam-se a ser utilizadas em dispositivos partilhados ou onde a inscrição no Windows Hello for Business é uma barreira.

Se o Windows Hello Face impedir que os utilizadores experimentem o cenário de inscrição na chave de segurança FIDO2, os utilizadores podem desligar o sinal de "Hello Face" removendo a inscrição facial em **Opções > Sign-In .**

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>Os utilizadores não podem usar chaves de segurança FIDO2 imediatamente após criarem uma máquina híbrida Azure AD

Depois do processo de união de domínios e reinicie o processo numa instalação limpa de uma máquina híbrida Azure AD, tem de iniciar súmis com uma palavra-passe e aguardar que a política se sincronize antes de poder utilizar uma chave de segurança FIDO2 para iniciar sinte.

Este comportamento é uma limitação conhecida para dispositivos de união de domínios, e não é específico para chaves de segurança FIDO2.

Para verificar o estado atual, utilize o `dsregcmd /status` comando. Verifique se tanto *a AzureAdJoined* como *a DomainJoined* mostram *SIM*.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>Os utilizadores não conseguem obter SSO no meu recurso de rede NTLM depois de iniciar sessão com uma chave de segurança FIDO2 e receber uma solicitação de credencial

Certifique-se de que dCs suficientes são remendados para responder a tempo para atender o seu pedido de recursos. Para verificar se consegue ver um servidor que está a executar a funcionalidade, reveja a saída de `nltest /dsgetdc:<dc name> /keylist /kdc`

Se conseguir ver um DC com esta funcionalidade, a palavra-passe do utilizador pode ter mudado desde que se inscrevia, ou há outro problema. Colete registos conforme detalhado na secção seguinte para a equipa de suporte da Microsoft depurar.

## <a name="troubleshoot"></a>Resolução de problemas

Existem duas áreas para resolver problemas - [problemas de clientes de janelas,](#windows-client-issues)ou [problemas de implantação.](#deployment-issues)

### <a name="windows-client-issues"></a>Problemas com o cliente do Windows

Para recolher dados que ajudem a resolver problemas com a inscrição no Windows ou o acesso a recursos no local a partir de dispositivos Windows 10, complete os seguintes passos:

1. Abra a aplicação **Feedback hub.** Certifique-se de que o seu nome está na parte inferior esquerda da aplicação e, em seguida, selecione **Criar um novo item de feedback**.

    Para o tipo de artigo de feedback, escolha *Problema*.

1. Selecione a categoria *segurança e privacidade* e, em seguida, a subcategoria *FIDO.*
1. Alternar a caixa de verificação para *Enviar ficheiros e diagnósticos anexados à Microsoft juntamente com o meu feedback*.
1. Selecione *Recrie os meus problemas* e, em seguida, *inicie a captura*.
1. Bloqueie e desbloqueie a máquina com a chave de segurança FIDO2. Se o problema ocorrer, tente desbloquear com outras credenciais.
1. Devolva ao **Feedback Hub,** selecione **Stop capture** e envie o seu feedback.
1. Vá à página *de Feedback* e depois ao *separador 'Feedback'.* Selecione o seu feedback recentemente submetido.
1. Selecione o botão *Partilhar* no canto superior direito para obter um link para o feedback. Inclua este link quando abrir um caso de apoio ou responda ao engenheiro designado para um caso de suporte existente.

São recolhidos os seguintes registos de eventos e informações chave de registo:

**Chaves de registo**

* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO \* .*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* \* .*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* \* .*

**Informação de diagnóstico**

* Despejo de kernel vivo
* Recolher informações do pacote AppX
* Ficheiros de contexto UIF

**Registos de eventos**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>Questões de Implantação

Para resolver problemas com a implementação do Azure AD Kerberos Server, utilize o novo módulo PowerShell incluído no Azure AD Connect.

#### <a name="viewing-the-logs"></a>Visualização dos registos

Os cmdlets Azure AD Kerberos Server PowerShell utilizam o mesmo registo que o Assistente de Ligação AD AD padrão. Para visualizar informações ou detalhes de erro dos cmdlets, complete os seguintes passos:

1. No Azure AD Connect Server, navegue por `C:\ProgramData\AADConnect\` . Esta pasta está oculta por predefinição.
1. Abra e veja o ficheiro mais recente `trace-*.log` localizado no diretório.

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Visualização dos objetos do servidor Azure AD Kerberos

Para ver os objetos do servidor Azure AD Kerberos e verificar se estão em boa ordem, complete os seguintes passos:

1. No Azure AD Connect Server, abra o PowerShell e navegue para `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Executar os seguintes comandos PowerShell para ver o Azure AD Kerberos Server tanto a AD AD como no local AD DS.

    Substitua *corp.contoso.com* pelo nome do seu domínio AD DS no local.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

O comando produz as propriedades do Azure AD Kerberos Server tanto a AD AD como no local AD DS. Reveja as propriedades para verificar se está tudo em ordem. Utilize a tabela abaixo para verificar as propriedades.

O primeiro conjunto de propriedades é a partir dos objetos no ambiente AD DS no local. A segunda metade (as propriedades que começam com *Cloud**) são do objeto Kerberos Server em Azure AD:

| Propriedade           | Descrição  |
|--------------------|--------------|
| Id                 | O *ID* único do objeto controlador de domínio AD DS. |
| DomainDnsName      | O nome de domínio DNS do domínio DS AD. |
| Contagem de computadores    | O objeto da conta do computador do objeto Azure AD Kerberos Server (o DC). |
| Contagem de utilizadores        | O objeto de conta de utilizador desativado que detém a chave de encriptação Azure AD Kerberos Server TGT. O DN desta conta é *CN=krbtgt_AzureAD,CN=Utilizadores,<Domain-DN>* |
| Chaveversão         | A versão chave da chave de encriptação Azure AD Kerberos Server TGT. A versão é atribuída quando a chave é criada. A versão é então incrementada cada vez que a chave é rodada. Os incrementos são baseados em meta-dados de replicação e provavelmente serão maiores do que um.<br /><br /> Por exemplo, a versão inicial *da KeyVersion* pode ser *192272*. A primeira vez que a chave é girada, a versão pode avançar para *212621*.<br /><br /> O importante a verificar é que a *Versão Chave* para o objeto no local e a *CloudKeyVersion* para o objeto de nuvem são as mesmas. |
| KeyUpdatedOn       | A data e a hora em que a chave de encriptação Azure AD Kerberos Server TGT foi atualizada ou criada. |
| KeyUpdatedFrom     | O DC onde a chave de encriptação Azure AD Kerberos Server TGT foi atualizada pela última vez. |
| CloudId            | O *ID* do Ad Object Azure. Deve coincidir com o *ID* acima. |
| CloudDomainDnsName | *O Nome DomainDns* do Azure AD Object. Deve coincidir com o *Nome DomainDns* acima. |
| CloudKeyVersion    | A *Versão Chave* do Objeto AD Azure. Deve coincidir com a *Versão Chave acima.* |
| CloudKeyUpdatedOn  | O *KeyUpdatedOn* do Azure AD Object. Deve coincidir com a *ChaveUpdatedOn* acima. |

## <a name="next-steps"></a>Passos seguintes

Para começar com as chaves de segurança FIDO2 e acesso híbrido aos recursos no local, consulte os seguintes artigos:

* [Chaves de segurança FIDO2 sem palavra-passe](howto-authentication-passwordless-security-key.md)
* [Windows 10 sem palavra-passe](howto-authentication-passwordless-security-key-windows.md)
* [Sem palavra-passe no local](howto-authentication-passwordless-security-key-on-premises.md)
