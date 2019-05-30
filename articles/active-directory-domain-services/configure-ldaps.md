---
title: Configurar LDAP seguro (LDAPS) nos serviços de domínio do Azure AD | Documentos da Microsoft
description: Configurar LDAP seguro (LDAPS) para um domínio gerido do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: ergreenl
ms.openlocfilehash: e961b6c4b97103668c591e319a81d20f533b4acb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246378"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar o secure LDAP (LDAPS) para um domínio gerido do Azure AD Domain Services
Este artigo mostra como pode permitir proteger Lightweight Directory Access Protocol (LDAPS) para o seu domínio gerido do Azure AD Domain Services. LDAP seguro também é conhecido como "acesso protocolo LDAP (Lightweight Directory) através de Secure Sockets Layer (SSL) / Transport Layer Security (TLS)".

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, terá de:

1. Válido **subscrição do Azure**.
2. Uma **diretório do Azure AD** -seja sincronizada com um diretório no local ou um diretório apenas na cloud.
3. **O Azure AD Domain Services** tem de estar ativada para o diretório do Azure AD. Se ainda não o fez, siga todas as tarefas descritas a [guia de introdução](create-instance.md).
4. R **certificado a ser utilizado para ativar o LDAP seguro**.

   * **Recomendado** -obter um certificado de uma autoridade de certificação pública fidedigna. Esta opção de configuração é mais segura.
   * Em alternativa, também pode optar por [criar um certificado autoassinado](#task-1---obtain-a-certificate-for-secure-ldap) conforme mostrado posteriormente neste artigo.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Requisitos para o certificado de secure LDAP
Adquira um certificado válido pelas seguintes diretrizes, antes de ativar o LDAP seguro. Encontrar falhas, se tentar ativar o LDAP seguro para o seu domínio gerido com um certificado inválidas/incorretas.

1. **Emissor fidedigno** -o certificado tem de ser emitido por uma autoridade confiável pelos computadores conectando-se o domínio gerido com o secure LDAP. Esta autoridade pode ser uma autoridade de certificação pública (AC) ou uma AC empresarial fidedigno para estes computadores.
2. **Tempo de vida** -o certificado tem de ser válido para, pelo menos, de 3 a 6 meses seguintes. Acesso de Secure LDAP ao seu domínio gerido é interrompido quando o certificado expirar.
3. **Nome do requerente** -o nome do requerente do certificado tem de ser o domínio gerido. Por exemplo, se o seu domínio com o nome 'contoso100.com', nome do requerente do certificado tem de ser 'contoso100.com'. Defina o nome DNS (nome alternativo do requerente) como um nome com carateres universais para o seu domínio gerido.
4. **Utilização de chave** -o certificado tem de ser configurado para o seguinte utiliza - as assinaturas de Digital e cifragem de chaves.
5. **Objetivo do certificado** -o certificado tem de ser válido para autenticação de servidor SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tarefa 1: obter um certificado para o LDAP seguro
A primeira tarefa envolve a obtenção de um certificado utilizado para acesso de secure LDAP para o domínio gerido. Tem duas opções:

* Obter um certificado de uma AC pública ou uma AC empresarial.
* Crie um certificado autoassinado.

> [!NOTE]
> Os computadores cliente que precisam de ligar para o domínio gerido com o secure LDAP tem de confiar o emissor de certificado de secure LDAP.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opção um (recomendado) - obter um certificado de secure LDAP a partir de uma autoridade de certificação
Se sua organização obtém seus certificados de uma AC pública, obter o certificado de secure LDAP a partir dessa AC pública. Se implementar uma AC empresarial, obter o certificado de secure LDAP a partir da AC empresarial.

> [!TIP]
> **Utilizar certificados autoassinados para domínios geridos com ". onmicrosoft.com" sufixos de domínio.**
> Se o nome de domínio DNS do seu domínio gerido termina em ". onmicrosoft.com', não é possível obter um certificado de secure LDAP a partir de uma autoridade de certificação pública. Uma vez que a Microsoft é o proprietário do domínio "onmicrosoft.com", recusam autoridades de certificação pública emitir um certificado de secure LDAP para para um domínio com este sufixo. Neste cenário, crie um certificado autoassinado e usá-lo para configurar o secure LDAP.
>

Certifique-se de que o certificado é obter a partir da autoridade de certificação pública satisfaça todos os requisitos descritos na [os requisitos para o certificado de secure LDAP](#requirements-for-the-secure-ldap-certificate).


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opção B - criar um certificado autoassinado para o LDAP seguro
Se não conta a utilizar um certificado de uma autoridade de certificação pública, pode optar por criar um certificado autoassinado para o LDAP seguro. Escolha esta opção se o nome de domínio DNS do seu domínio gerido terminar em ". onmicrosoft.com".

**Criar um certificado autoassinado com o PowerShell**

No seu computador Windows, abra uma nova janela do PowerShell como **administrador** e escreva os comandos seguintes, para criar um novo certificado autoassinado.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com, contoso100.com
```

No exemplo anterior, substitua 'contoso100.com' com o nome de domínio DNS do seu domínio gerido. Por exemplo, se tiver criado um domínio gerido, chamado 'contoso100.onmicrosoft.com', substitua 'contoso100.com' no atributo assunto 'contoso100.onmicrosoft.com' e ' *. contoso100.com' no atributo DnsName com "* . contoso100.onmicrosoft.com').

![Selecionar o Azure AD Directory](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

O certificado autoassinado acabado de criar é colocado no arquivo de certificados do computador local.


## <a name="next-step"></a>Passo seguinte
[Tarefa 2 - exportar o certificado de secure LDAP para um. Ficheiro PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
