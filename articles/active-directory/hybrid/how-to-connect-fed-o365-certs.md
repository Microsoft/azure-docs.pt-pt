---
title: Certificado de renovação para utilizadores do Office 365 e o Azure AD | Documentos da Microsoft
description: Este artigo explica como resolver problemas relacionados com e-mails que notificá-los sobre como renovar um certificado para utilizadores do Office 365.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fae036a0860ddb5ee2776f7ed4734492741907f7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58177726"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Renovar certificados de Federação para o Office 365 e Azure Active Directory
## <a name="overview"></a>Descrição geral
Para a Federação com êxito entre o Azure Active Directory (Azure AD) e serviços de Federação do Active Directory (AD FS), os certificados utilizados pelo AD FS para assinar os tokens de segurança para o Azure AD devem corresponder ao que está configurado no Azure AD. Qualquer erro de correspondência pode levar a confiança quebrada. O Azure AD garante que essas informações são mantidas em sincronia ao implementar o AD FS e Proxy de aplicações Web (para acesso à extranet).

Este artigo fornece informações adicionais para gerir os certificados de assinatura de tokens e mantê-los em sincronia com o Azure AD, nos seguintes casos:

* Não está a implementar o Proxy de aplicações Web e, portanto, os metadados de Federação não estão disponível da extranet.
* A configuração predefinida do AD FS não estiver a utilizar para certificados de assinatura de tokens.
* Está a utilizar um fornecedor de identidade de terceiros.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configuração predefinida do AD FS para certificados de assinatura de tokens
A assinatura de tokens e token de desencriptação de certificados são certificados autoassinados, normalmente e são ideais para um ano. Por predefinição, o AD FS inclui um processo de renovação automática chamado **AutoCertificateRollover**. Se estiver a utilizar o AD FS 2.0 ou posterior, do Office 365 e o Azure AD são atualizados automaticamente o certificado antes de expirar.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Notificação de renovação do Centro de administração do Microsoft 365 ou uma mensagem de e-mail
> [!NOTE]
> Se tiver recebido um e-mail ou uma notificação do portal que lhe pede para renovar o certificado para o Office, consulte [gerenciamento de alterações para certificados de assinatura de tokens](#managecerts) para verificar se tem de efetuar qualquer ação. A Microsoft está ciente de um possível problema que pode levar a notificações para a renovação de certificado enviado, mesmo quando é necessária nenhuma ação.
>
>

Tenta do Azure AD monitorizar os metadados de Federação e atualizar o token de certificados de assinatura, conforme indicado por estes metadados. 30 dias antes da expiração do token de assinatura de certificados, do Azure AD verifica se novos certificados estão disponíveis através de consultas de metadados de Federação.

* Se com êxito, pode consultar os metadados de Federação e obter os novos certificados, sem notificação por e-mail ou um aviso no Centro de administração do Microsoft 365 é emitido para o utilizador.
* Se este não é possível obter o token de novos certificados de assinatura, seja porque os metadados de Federação não estão acessível ou automática de certificado de rollover não estiver ativado, o Azure AD emite uma notificação por e-mail e um aviso no Centro de administração do Microsoft 365.

![Notificação do portal do Office 365](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Se estiver a utilizar o AD FS, para garantir a continuidade do negócio, verifique se os servidores têm as seguintes atualizações para que não ocorram falhas de autenticação para problemas conhecidos. Isso reduz os problemas de servidor do AD FS proxy conhecidos para esta renovação e períodos de renovação futuras:
>
> Server 2012 R2 - [do Windows Server rollup de Maio de 2014](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 e 2012 - [autenticação através do proxy de falha no Windows Server 2012 ou Windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446)
>
>

## Verifique se os certificados tem de ser atualizado <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Passo 1: Verificar o estado de AutoCertificateRollover
No servidor do AD FS, abra o PowerShell. Verifique se o valor de AutoCertificateRollover está definido como True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Se estiver a utilizar o AD FS 2.0, primeiro execute Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Passo 2: Confirme que o AD FS e o Azure AD estão em sincronização
No servidor do AD FS, abra a linha de comandos do PowerShell do MSOnline e ligar ao Azure AD.

> [!NOTE]
> MSOL Cmdlets fazem parte do módulo do PowerShell do MSOnline.
> Pode transferir o módulo do PowerShell do MSOnline diretamente a partir da galeria do PowerShell.
> 
>

    Install-Module MSOnline

Ligar ao Azure AD com o módulo PowerShell do MSOnline.

    Import-Module MSOnline
    Connect-MsolService

Verificar os certificados configurados no AD FS e do Azure AD confia propriedades para o domínio especificado.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Se os thumbprints em ambas as saídas corresponderem, seus certificados estão sincronizados com o Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Passo 3: Verifique se o seu certificado está prestes a expirar
Na saída de Get-MsolFederationProperty ou Get-AdfsCertificate, verificar a data em "Not After." Se a data for inferior a daqui a 30 dias, deve tomar uma ação.

| AutoCertificateRollover | Certificados sincronizados com o Azure AD | Metadados de Federação estão acessível ao público | Validade | Ação |
|:---:|:---:|:---:|:---:|:---:|
| Sim |Sim |Sim |- |Não foi necessária nenhuma ação. Ver [assinatura de tokens de renovação de certificados automaticamente](#autorenew). |
| Sim |Não |- |Menos de 15 dias |Renove imediatamente. Ver [assinatura de tokens de renovação de certificado manualmente](#manualrenew). |
| Não |- |- |Menos de 30 dias |Renove imediatamente. Ver [assinatura de tokens de renovação de certificado manualmente](#manualrenew). |

\[Não importa -]

## Renovar o token automaticamente o certificado de assinatura (recomendado) <a name="autorenew"></a>
Não precisa de realizar quaisquer passos manuais se os seguintes elementos forem verdadeiros:

* Implementou o Proxy de aplicações Web, que podem permitir o acesso aos metadados de Federação da extranet.
* Está a utilizar a configuração predefinida do AD FS (o AutoCertificateRollover está ativado).

Verifique o seguinte para confirmar que o certificado pode ser atualizado automaticamente.

**1. A propriedade do AD FS AutoCertificateRollover tem de ser definida como True.** Isto indica que do AD FS irá gerar automaticamente nova assinatura de tokens e certificados de desencriptação de tokens, antes do antigo aqueles expirarem.

**2. Os metadados de Federação do AD FS estão acessível ao público.** Certifique-se de que os seus metadados de Federação estão publicamente acessível ao navegar para o URL seguinte a partir de um computador com a internet pública (fora da rede empresarial):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

onde `(your_FS_name) `é substituído com o nome de anfitrião do serviço de Federação utiliza a sua organização, por exemplo, fs.contoso.com.  Se for capaz de verificar as duas destas definições com êxito, não tem a fazer mais nada.  

Exemplo: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## Renovar o token manualmente o certificado de assinatura <a name="manualrenew"></a>
Pode optar por renovar o token manualmente os certificados de assinatura. Por exemplo, poderão funcionar melhor para renovação manual os seguintes cenários:

* Tokens de assinatura de certificados são certificados autoassinados não. A razão mais comum disso é que a sua organização gere certificados do AD FS inscritos a partir de uma autoridade de certificação organizacional.
* Segurança de rede não permite que os metadados de Federação a estar publicamente disponíveis.

Nestes cenários, sempre que atualizar o token de assinatura de certificados, tem também de atualizar seu domínio do Office 365, utilizando o comando do PowerShell, Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Passo 1: Certifique-se de que o AD FS tem novos certificados de assinatura de tokens
**Configuração de não predefinidos**

Se estiver a utilizar uma configuração não predefinidas do AD FS (em que **AutoCertificateRollover** está definida como **falso**), provavelmente está usando certificados personalizados (não autoassinados). Para obter mais informações sobre como renovar os certificados de assinatura de tokens do AD FS, consulte [orientação para os clientes não utilizar o AD FS auto-assinado certificados](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Metadados de Federação não estão disponível publicamente**

Por outro lado, se **AutoCertificateRollover** está definida como **verdadeiro**, mas os metadados de Federação não estão acessível ao público, primeiro certifique-se de que os certificados de assinatura de token novo tem sido gerados pelo AD FS. Certifique-se que tem o token de novos certificados de assinatura, efetuando os seguintes passos:

1. Certifique-se de que tem sessão iniciada num servidor do AD FS primário.
2. Verificar os certificados de assinatura atuais no AD FS ao abrir uma janela de comando do PowerShell e executar o seguinte comando:

    PS C:\>Get-ADFSCertificate –CertificateType token-signing

   > [!NOTE]
   > Se estiver a utilizar o AD FS 2.0, deve executar Add-Pssnapin Microsoft.Adfs.Powershell pela primeira vez.
   >
   >
3. Examinar a saída do comando em todos os certificados listados. Se o AD FS gerou um novo certificado, deverá ver dois certificados na saída: um para que o **IsPrimary** valor é **True** e o **NotAfter** data é dentro de 5 dias e outro para o qual **IsPrimary** é **False** e **NotAfter** é cerca de um ano no futuro.
4. Se vir apenas um certificado e o **NotAfter** data é dentro de 5 dias, terá de gerar um novo certificado.
5. Para gerar um novo certificado, execute o seguinte comando no prompt de comando do PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. Certifique-se a atualização executando novamente o seguinte comando: PS C:\>Get-ADFSCertificate –CertificateType token-signing

Dois certificados deverá ser listados agora, um dos quais tem um **NotAfter** data de aproximadamente um ano no futuro e para o qual o **IsPrimary** valor é **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Passo 2: Atualizar o novo token de assinatura de certificados para a confiança do Office 365
Atualize o Office 365 com o novo token de assinatura de certificados a serem usados para a fidedignidade, da seguinte forma.

1. Abra o módulo do Microsoft Azure Active Directory para o Windows PowerShell.
2. Run $cred=Get-Credential. Quando este cmdlet pede-lhe as credenciais, escreva as credenciais de conta de administrador de serviço cloud.
3. Executar o Connect-MsolService – $cred de credenciais. Este cmdlet liga-o para o serviço em nuvem. Criar um contexto que liga ao serviço em nuvem é necessária antes de executar qualquer um dos cmdlets adicionais instalados pela ferramenta.
4. Se estiver a executar estes comandos num computador que não é o servidor de Federação principal do AD FS, execute Set-MSOLAdfscontext-computador &lt;servidor primário do AD FS&gt;, onde &lt;servidor primário do AD FS&gt; é o FQDN interno nome do servidor do AD FS primário. Este cmdlet cria um contexto que liga-o para o AD FS.
5. Execute Update-MSOLFederatedDomain-DomainName &lt;domínio&gt;. Este cmdlet atualiza as definições do AD FS para o serviço de nuvem e configura a relação de confiança entre os dois.

> [!NOTE]
> Se precisar de suportar vários domínios de nível superior, como contoso.com e fabrikam.com, tem de utilizar o **SupportMultipleDomain** mudar com qualquer cmdlet. Para obter mais informações, consulte [suporte para múltiplos domínios de nível superior](how-to-connect-install-multiple-domains.md).
>


## Reparar a confiança do Azure AD com o Azure AD Connect <a name="connectrenew"></a>
Se tiver configurado o seu farm do AD FS e a confiança do Azure AD com o Azure AD Connect, pode utilizar o Azure AD Connect para detetar se é necessário efetuar qualquer ação para os certificados de assinatura de tokens. Se precisar de renovar os certificados, pode utilizar o Azure AD Connect para fazê-lo.

Para obter mais informações, consulte [reparar a confiança](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Passos de atualização de certificados do AD FS e o Azure AD
Token de certificados de assinatura são X509 padrão certificados que são utilizados para assinar com segurança todos os tokens que o servidor de Federação emite. Os certificados de desencriptação de tokens são X509 padrão certificados que são utilizados para desencriptar quaisquer entradas de tokens. 

Por predefinição, o AD FS está configurado para gerar automaticamente, assinatura de tokens e certificados de desencriptação de tokens no momento de configuração inicial e quando os certificados estão prestes a atingir a sua data de expiração.

O Azure AD tenta obter um novo certificado de seus metadados do serviço de Federação de 30 dias antes da expiração do certificado atual. No caso de um novo certificado não estiver disponível naquele momento, o Azure AD irá continuar a monitorizar os metadados em intervalos regulares de diários. Assim que o novo certificado está disponível nos metadados, as definições de Federação para o domínio são atualizadas com as novas informações de certificado. Pode usar `Get-MsolDomainFederationSettings` para verificar se ver o novo certificado no NextSigningCertificate / SigningCertificate.

Para obter mais informações sobre a assinatura de tokens certificados no AD FS consulte [obter e configurar a assinatura de tokens e certificados de desencriptação de tokens do AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
