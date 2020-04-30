---
title: Renovação de certificado para utilizadores do Office 365 e Azure AD [ Microsoft Docs
description: Este artigo explica aos utilizadores do Office 365 como resolver problemas com e-mails que os notificam sobre a renovação de um certificado.
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
ms.openlocfilehash: 89de1495dc6bb411d5d43986177f11abb016cf15
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200892"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Renovar os certificados da federação para o Office 365 e o Azure Ative Directory
## <a name="overview"></a>Descrição geral
Para a federação de sucesso entre o Azure Ative Directory (Azure AD) e os Serviços da Federação de Diretórios Ativos (AD FS), os certificados utilizados pela AD FS para assinar fichas de segurança para a Azure AD devem corresponder ao que está configurado em Azure AD. Qualquer desfasamento pode levar a uma confiança quebrada. A Azure AD garante que esta informação é mantida em sincronização quando implementa AD FS e Web Application Proxy (para acesso extranet).

Este artigo fornece-lhe informações adicionais para gerir os seus certificados de assinatura simbólicas e mantê-los em sintonia com a Azure AD, nos seguintes casos:

* Não está a implementar o Proxy de Aplicação Web e, portanto, os metadados da federação não estão disponíveis na extranet.
* Não está a utilizar a configuração padrão do AD FS para certificados de assinatura simbólicos.
* Está a usar um fornecedor de identidade de terceiros.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configuração padrão de AD FS para certificados de assinatura de token
Os certificados de assinatura simbólica e dedesencriptação simbólica são geralmente certificados auto-assinados, e são bons por um ano. Por predefinição, a AD FS inclui um processo de renovação automática chamado **AutoCertificateRollover**. Se estiver a utilizar AD FS 2.0 ou mais tarde, o Office 365 e o Azure AD atualizam automaticamente o seu certificado antes de expirar.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Notificação de renovação do centro de administração da Microsoft 365 ou um e-mail
> [!NOTE]
> Se recebeu um e-mail ou uma notificação do portal pedindo-lhe para renovar o seu certificado para o Office, consulte [a Managing alterações nos certificados](#managecerts) de assinatura simbólicas para verificar se precisa de tomar alguma ação. A Microsoft está ciente de um possível problema que pode levar a notificações para a renovação do certificado ser enviada, mesmo quando não é necessária qualquer ação.
>
>

A Azure AD tenta monitorizar os metadados da federação e atualizar os certificados de assinatura simbólicas, conforme indicado por estes metadados. 30 dias antes da expiração dos certificados de assinatura simbólica, a AD Azure verifica se existem novos certificados através da votação dos metadados da federação.

* Se conseguir fazer uma sondagem com sucesso aos metadados da federação e recuperar os novos certificados, não é emitida nenhuma notificação de e-mail ou aviso no centro de administração da Microsoft 365 ao utilizador.
* Se não conseguir recuperar os novos certificados de assinatura simbólica, quer porque os metadados da federação não são acessíveis ou que o capotamento automático do certificado não está ativado, a Azure AD emite uma notificação por e-mail e um aviso no centro de administração da Microsoft 365.

![Notificação do portal do Office 365](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Se estiver a utilizar a AD FS, para garantir a continuidade do negócio, verifique se os seus servidores têm as seguintes atualizações para que não ocorram falhas de autenticação para problemas conhecidos. Isto atenua os conhecidos problemas do servidor proxy AD FS para este período de renovação e renovação futura:
>
> Server 2012 R2 - [Windows Server May 2014 rollup](https://support.microsoft.com/kb/2955164)
>
> Servidor 2008 R2 e 2012 - Autenticação através de [falhas de procuração no Windows Server 2012 ou Windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446)
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Verifique se os certificados precisam de ser atualizados<a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Passo 1: Verifique o estado autoCertificateRollover
No seu servidor AD FS, abra a PowerShell. Verifique se o valor AutoCertificateRollover está definido para True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Se estiver a utilizar AD FS 2.0, primeiro executar Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Passo 2: Confirme que AD FS e Azure AD estão em sincronização
No seu servidor AD FS, abra o pedido MSOnline PowerShell e ligue-se ao Azure AD.

> [!NOTE]
> Os MSOL-Cmdlets fazem parte do módulo MSOnline PowerShell.
> Pode descarregar o Módulo MSOnline PowerShell diretamente da PowerShell Gallery.
> 
>

    Install-Module MSOnline

Ligue-se ao Azure AD utilizando o Módulo PowerShell MSOnline.

    Import-Module MSOnline
    Connect-MsolService

Verifique os certificados configurados nas propriedades fiduciários AD FS e Azure AD para o domínio especificado.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Se as impressões digitais em ambas as saídas coincidirem, os seus certificados estão sincronizados com o Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Passo 3: Verifique se o seu certificado está prestes a expirar
Na saída da Get-MsolFederationProperty ou da Get-AdfsCertificate, verifique a data em "Not After". Se a data for inferior a 30 dias, deve tomar medidas.

| AutoCertificateRollover | Certificados em sincronização com a Azure AD | Metadados da Federação são acessíveis ao público | Validade | Ação |
|:---:|:---:|:---:|:---:|:---:|
| Sim |Sim |Sim |- |Não é necessária uma ação. Consulte o certificado de [assinatura de fichas Renovar automaticamente](#autorenew). |
| Sim |Não |- |Menos de 15 dias |Renovar imediatamente. Consulte o certificado de [assinatura de símbolos Renovar manualmente](#manualrenew). |
| Não |- |- |Menos de 30 dias |Renovar imediatamente. Consulte o certificado de [assinatura de símbolos Renovar manualmente](#manualrenew). |

\[-] Não importa

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>Renovar automaticamente o certificado de assinatura simbólica (recomendado)<a name="autorenew"></a>
Não precisa de realizar quaisquer passos manuais se ambos forem verdadeiros:

* Implementou o Proxy de Aplicação Web, que pode permitir o acesso aos metadados da federação a partir da extranet.
* Está a utilizar a configuração predefinida AD FS (o AutoCertificateRollover está ativado).

Verifique o seguinte para confirmar se o certificado pode ser atualizado automaticamente.

**1. A propriedade AD FS AutoCertificateRollover deve ser definida para True.** Isto indica que o AD FS gerará automaticamente novos certificados de assinatura e desencriptação simbólica, antes de expirarem os antigos.

**2. Os metadados da federação AD FS são acessíveis ao público.** Verifique se os metadados da sua federação são acessíveis ao público navegando para o seguinte URL a partir de um computador na internet pública (fora da rede corporativa):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

onde `(your_FS_name)` é substituído pelo nome de anfitrião do serviço da federação que a sua organização usa, como fs.contoso.com.  Se conseguir verificar ambas as definições com sucesso, não tem de fazer mais nada.  

Exemplo: `https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml`
## <a name="renew-the-token-signing-certificate-manually"></a>Renovar manualmente o certificado de assinatura simbólica<a name="manualrenew"></a>
Pode optar por renovar manualmente os certificados de assinatura de símbolos. Por exemplo, os seguintes cenários podem funcionar melhor para a renovação manual:

* Os certificados de assinatura simbólicanão são certificados auto-assinados. A razão mais comum para isso é que a sua organização gere certificados AD FS matriculados numa autoridade de certificadoorganizacional.
* A segurança da rede não permite que os metadados da federação estejam disponíveis ao público.

Nestes cenários, sempre que atualizar os certificados de assinatura de token, também deve atualizar o seu domínio Office 365 utilizando o comando PowerShell, Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Passo 1: Certifique-se de que a AD FS tem novos certificados de assinatura simbólicas
**Configuração não predefinida**

Se estiver a utilizar uma configuração não predefinida de AD FS (onde o **AutoCertificateRollover** está definido para **Falso),** provavelmente está a usar certificados personalizados (não auto-assinados). Para obter mais informações sobre como renovar os certificados de assinatura de token AD FS, consulte [Orientação para clientes que não utilizem certificados auto-assinados AD FS](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Metadados da Federação não estão disponíveis ao público**

Por outro lado, se o **AutoCertificateRollover** estiver definido para **True**, mas os metadados da federação não forem acessíveis ao público, certifique-se primeiro de que os novos certificados de assinatura de símbolos foram gerados pela AD FS. Confirme que tem novos certificados de assinatura simbólica, tomando as seguintes etapas:

1. Verifique se está ligado ao servidor principal do AD FS.
2. Verifique os certificados de assinatura em AD FS abrindo uma janela de comando PowerShell e executando o seguinte comando:

    PS C:\>Get-ADFSCertificate -CertificateType token-signing

   > [!NOTE]
   > Se estiver a utilizar AD FS 2.0, deve executar add-Pssnapin Microsoft.Adfs.Powershell primeiro.
   >
   >
3. Olhe para a saída de comando em quaisquer certificados listados. Se a AD FS tiver gerado um novo certificado, deverá ver dois certificados na saída: um para o qual o valor **IsPrimary** é **verdadeiro** e a data **NotAfter** é dentro de 5 dias, e um para o qual a **IsPrimary** é **Falsa** e **notAfter** é de cerca de um ano no futuro.
4. Se você vir apenas um certificado, e a data **NotAfter** é dentro de 5 dias, você precisa gerar um novo certificado.
5. Para gerar um novo certificado, execute o seguinte `PS C:\>Update-ADFSCertificate –CertificateType token-signing`comando num pedido de comando PowerShell: .
6. Verifique a atualização executando novamente\>o seguinte comando: PS C: Get-ADFSCertificate –CertificateType token-signing

Dois certificados devem ser listados agora, um dos quais tem uma data **notAfter** de aproximadamente um ano no futuro, e para o qual o valor **IsPrimary** é **falso**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Passo 2: Atualizar os novos certificados de assinatura simbólica para o fundo office 365
Atualizar o Office 365 com os novos certificados de assinatura simbólica a utilizar para o fundo, da seguinte forma.

1. Abra o Módulo de Diretório Ativo Microsoft Azure para windows PowerShell.
2. Executar $cred=Get-Credential. Quando este cmdlet lhe pedir credenciais, digite as credenciais de conta de administrador de serviço na nuvem.
3. Executar Connect-MsolService – Credenciais $cred. Este cmdlet liga-o ao serviço de nuvem. É necessário criar um contexto que o ligue ao serviço de nuvem antes de executar qualquer um dos cmdlets adicionais instalados pela ferramenta.
4. Se estiver a executar estes comandos num computador que não seja o servidor da federação primária AD FS, execute o set-MSOLAdfscontext &lt;-Computer AD FS primary&gt;server , onde &lt;o servidor&gt; primário AD FS é o nome interno fQDN do servidor principal do AD FS. Este cmdlet cria um contexto que o liga ao AD FS.
5. Executar Actualização-MSOLFederatedDomain &lt;–Domínio&gt;domínio domínio . Este cmdlet atualiza as definições da AD FS para o serviço de nuvem, e configura a relação de confiança entre os dois.

> [!NOTE]
> Se precisar de suportar vários domínios de alto nível, como contoso.com e fabrikam.com, deve utilizar o interruptor **SupportMultipleDomain** com quaisquer cmdlets. Para mais informações, consulte [Suporte para Domínios de Nível Superior Múltiplo](how-to-connect-install-multiple-domains.md).
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Reparação da confiança da AD Azure utilizando o Azure AD Connect<a name="connectrenew"></a>
Se configurar a sua quinta AD FS e a confiança azure AD utilizando o Azure AD Connect, pode utilizar o Azure AD Connect para detetar se precisa de tomar alguma medida para os seus certificados de assinatura simbólicas. Se precisar de renovar os certificados, pode utilizar o Azure AD Connect para o fazer.

Para mais informações, consulte [Reparar o fundo.](how-to-connect-fed-management.md)

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Etapas de atualização de certificados AD FS e Azure AD
Os certificados de assinatura token são certificados X509 padrão que são usados para assinar de forma segura todos os tokens que o servidor da federação emite. Os certificados de desencriptação token são certificados X509 padrão que são usados para desencriptar quaisquer fichas de entrada. 

Por predefinição, o AD FS está configurado para gerar automaticamente certificados de assinatura simbólica e dedesencriptação simbólica, tanto no momento inicial de configuração como quando os certificados se aproximam da data de validade.

A Azure AD tenta obter um novo certificado dos metadados do seu serviço da federação 30 dias antes do termo do certificado atual. Caso não esteja disponível um novo certificado nessa altura, a Azure AD continuará a monitorizar os metadados em intervalos diários regulares. Assim que o novo certificado estiver disponível nos metadados, as definições da federação para o domínio são atualizadas com a nova informação do certificado. Pode utilizar `Get-MsolDomainFederationSettings` para verificar se vê o novo certificado no Certificado de Assinatura Seguinte/ Certificado de Assinatura.

Para obter mais informações sobre certificados de assinatura token em AD FS ver Obter e Configurar Certificados de Assinatura de [Token e Token dedesencriptação para AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
