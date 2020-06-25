---
title: Renovação de certificados para utilizadores do Office 365 e AZure AD ! Microsoft Docs
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
ms.topic: how-to
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04f523a2615892268d56c167a682987453dc997c
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85359743"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Renovar certificados da federação para o Office 365 e o Azure Ative Directory
## <a name="overview"></a>Descrição geral
Para uma federação bem sucedida entre o Azure Ative Directory (Azure AD) e o Ative Directory Federation Services (AD FS), os certificados utilizados pela AD FS para assinar fichas de segurança para a Azure AD devem corresponder ao que está configurado no Azure AD. Qualquer incompatibilidade pode levar a uma quebra de confiança. O Azure AD garante que estas informações são mantidas sincronizadas quando implementa o AD FS e o Proxy da Aplicação Web (para acesso à extranet).

Este artigo fornece-lhe informações adicionais para gerir os seus certificados de assinatura simbólica e mantê-los em sintonia com a Azure AD, nos seguintes casos:

* Não está a implementar o Proxy da Aplicação Web e, portanto, os metadados da federação não estão disponíveis na extranet.
* Não está a utilizar a configuração padrão de AD FS para certificados de assinatura simbólica.
* Está a usar um fornecedor de identidade de terceiros.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configuração padrão de AD FS para certificados de assinatura simbólica
Os certificados de assinatura simbólica e de desencriptação são geralmente certificados auto-assinados, e são bons para um ano. Por predefinição, o AD FS inclui um processo de renovação automática chamado **AutoCertificateRollover**. Se estiver a utilizar o AD FS 2.0 ou mais tarde, o Office 365 e o AZure AD atualizam automaticamente o certificado antes de expirar.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Notificação de renovação do centro de administração microsoft 365 ou um e-mail
> [!NOTE]
> Se recebeu um e-mail ou uma notificação de portal pedindo-lhe para renovar o seu certificado de Escritório, consulte [a Gestão de alterações aos certificados de assinatura de fichas para](#managecerts) verificar se precisa de tomar alguma ação. A Microsoft está ciente de um possível problema que pode levar ao envio de notificações para renovação de certificados, mesmo quando não é necessária qualquer ação.
>
>

A Azure AD tenta monitorizar os metadados da federação e atualizar os certificados de assinatura de fichas como indicado por estes metadados. 30 dias antes do termo dos certificados de assinatura de fichas, a Azure AD verifica se novos certificados estão disponíveis através da sondagem dos metadados da federação.

* Se conseguir sondar com sucesso os metadados da federação e recuperar os novos certificados, não é emitida nenhuma notificação de e-mail ou aviso no centro de administração microsoft 365.
* Se não conseguir recuperar os novos certificados de assinatura de fichas, quer porque os metadados da federação não são alcançáveis ou a capotagem automática do certificado não está ativada, a Azure AD emite uma notificação de e-mail e um aviso no centro de administração microsoft 365.

![Notificação do portal do Office 365](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Se estiver a utilizar o AD FS, para garantir a continuidade do negócio, verifique se os seus servidores têm as seguintes atualizações para que não ocorram falhas de autenticação para problemas conhecidos. Isto atenua os problemas conhecidos do servidor proxy da AD FS para este período de renovação e renovação futuro:
>
> Servidor 2012 R2 - [Windows Server May 2014 rollup](https://support.microsoft.com/kb/2955164)
>
> Servidor 2008 R2 e 2012 - [A autenticação através de falhas de procuração no Windows Server 2012 ou no Windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446)
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Verifique se os certificados precisam de ser atualizados<a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Passo 1: Verifique o estado de AutoCertateRollover
No seu servidor AD FS, abra o PowerShell. Verifique se o valor AutoCertateRollover está definido para True.

    Get-Adfsproperties

![AutoCertateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Se estiver a utilizar o AD FS 2.0, primeiro executar Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Passo 2: Confirmar que a AD FS e a Azure AD estão sincronizadas
No seu servidor AD FS, abra o pedido MSOnline PowerShell e ligue-se ao Azure AD.

> [!NOTE]
> Os CMDlets MSOL fazem parte do módulo MSOnline PowerShell.
> Pode baixar o Módulo MSOnline PowerShell diretamente da PowerShell Gallery.
> 
>

    Install-Module MSOnline

Ligue-se ao Azure AD utilizando o Módulo MSOnline PowerShell.

    Import-Module MSOnline
    Connect-MsolService

Verifique os certificados configurados em propriedades fidedignos AD FS e Azure AD para o domínio especificado.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Se as impressões digitais em ambas as saídas coincidirem, os seus certificados estão sincronizados com a Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Passo 3: Verifique se o seu certificado está prestes a expirar
Na saída de Get-MsolFederationProperty ou Get-AdfsCertificate, verifique a data em "Not After". Se a data for a menos de 30 dias, deve tomar medidas.

| AutoCertateRollover | Certificados em sincronização com Azure AD | Metadados da Federação são acessíveis ao público | Validade | Ação |
|:---:|:---:|:---:|:---:|:---:|
| Sim |Sim |Sim |- |Não é necessária uma ação. Consulte [automaticamente o certificado de assinatura de fichas de renovação](#autorenew). |
| Yes |Não |- |Menos de 15 dias |Renovar imediatamente. Consulte [manualmente o certificado de assinatura de fichas de renovação](#manualrenew). |
| No |- |- |Menos de 30 dias |Renovar imediatamente. Consulte [manualmente o certificado de assinatura de fichas de renovação](#manualrenew). |

\[-] Não importa

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>Renovar automaticamente o certificado de assinatura simbólica (recomendado)<a name="autorenew"></a>
Não precisa de realizar quaisquer passos manuais se ambos forem verdadeiros:

* Implementou o Proxy de Aplicação Web, que pode permitir o acesso aos metadados da federação a partir da extranet.
* Está a utilizar a configuração padrão AD FS (AutoCertificateRollover está ativado).

Verifique o seguinte para confirmar se o certificado pode ser atualizado automaticamente.

**1. A propriedade AD FS AutoCertificateRollover deve ser definida como True.** Isto indica que o AD FS gerará automaticamente novos certificados de assinatura simbólica e de desencriptação, antes que os antigos expirem.

**2. Os metadados da federação de FS da AD são acessíveis ao público.** Verifique se os metadados da federação são acessíveis ao público navegando para o seguinte URL a partir de um computador na internet pública (fora da rede corporativa):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

onde `(your_FS_name)` é substituído pelo nome de anfitrião do serviço da federação que a sua organização usa, como fs.contoso.com.  Se conseguir verificar ambas as definições com sucesso, não terá de fazer mais nada.  

Exemplo: `https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml`
## <a name="renew-the-token-signing-certificate-manually"></a>Renovar manualmente o certificado de assinatura simbólica<a name="manualrenew"></a>
Pode optar por renovar manualmente os certificados de assinatura de fichas simbólicas. Por exemplo, os seguintes cenários podem funcionar melhor para a renovação manual:

* Os certificados de assinatura token não são certificados auto-assinados. A razão mais comum para isso é que a sua organização gere certificados AD FS matriculados a partir de uma autoridade de certificados organizacionais.
* A segurança da rede não permite que os metadados da federação estejam disponíveis ao público.

Nestes cenários, sempre que atualizar os certificados de assinatura de token, também deve atualizar o seu domínio Office 365 utilizando o comando PowerShell, Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Passo 1: Certifique-se de que a AD FS tem novos certificados de assinatura simbólica
**Configuração não padrão**

Se estiver a utilizar uma configuração não padrão de AD FS (onde **o AutoCertateRollover** está definido como **Falso),** provavelmente está a utilizar certificados personalizados (não auto-assinados). Para obter mais informações sobre como renovar os certificados de assinatura de fichas AD FS, consulte [Orientação para clientes que não utilizem certificados auto-assinados da AD FS](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Metadados da Federação não estão disponíveis publicamente**

Por outro lado, se o **AutoCertificateRollover** estiver definido para **True**, mas os metadados da federação não são acessíveis ao público, primeiro certifique-se de que novos certificados de assinatura de fichas foram gerados pela AD FS. Confirme que tem novos certificados de assinatura de fichas, tomando os seguintes passos:

1. Verifique se está ligado ao servidor FS AD primário.
2. Verifique os certificados de assinatura atuais em AD FS abrindo uma janela de comando PowerShell e executando o seguinte comando:

    PS C: \> Get-ADFSCertificate –CertificateType token-signing

   > [!NOTE]
   > Se estiver a utilizar o AD FS 2.0, deverá executar o Add-Pssnapin Microsoft.Adfs.Powershell primeiro.
   >
   >
3. Veja a saída de comando em quaisquer certificados listados. Se a AD FS tiver gerado um novo certificado, deverá ver dois certificados na saída: um para o qual o valor **Do IsPrimary** é **Verdadeiro** e a data **notAfter** é dentro de 5 dias, e um para o qual **o IsPrimary** é **Falso** e **NotAfter** é de cerca de um ano no futuro.
4. Se vir apenas um certificado, e a data **"Não Depois"** for dentro de 5 dias, terá de gerar um novo certificado.
5. Para gerar um novo certificado, execute o seguinte comando num pedido de comando PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing` .
6. Verifique novamente a atualização executando o seguinte comando: PS C: \> Get-ADFSCertificate –CertificateType token-signing

Dois certificados devem ser listados agora, um dos quais tem uma data **de NotAfter** de aproximadamente um ano no futuro, e para o qual o valor Do Ensino **Superior** é **Falso**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Passo 2: Atualizar os novos certificados de assinatura de fichas para o Fundo 365
Atualizar o Office 365 com os novos certificados de assinatura de fichas a serem utilizados para o fundo, da seguinte forma.

1. Abra o Módulo de Diretório Ativo Microsoft Azure para Windows PowerShell.
2. Executar $cred=Get-Credential. Quando este cmdlet lhe pedir credenciais, escreva as credenciais de conta do administrador de serviço na nuvem.
3. Executar Connect-MsolService –$cred credenciais. Este cmdlet liga-o ao serviço de nuvem. É necessário criar um contexto que o ligue ao serviço de nuvem antes de executar qualquer um dos cmdlets adicionais instalados pela ferramenta.
4. Se estiver a executar estes comandos num computador que não seja o servidor da federação primária do FS AD, executar Set-MSOLAdfscontext -Computer &lt; AD FS, &gt; onde o servidor primário &lt; AD FS &gt; é o nome interno FQDN do servidor principal AD FS. Este cmdlet cria um contexto que o liga ao AD FS.
5. Executar Actualização-MSOLFederatedDomain – Domínio Dedomínio do Domínio &lt; &gt; . Este cmdlet atualiza as definições do AD FS para o serviço de nuvem e configura a relação de confiança entre os dois.

> [!NOTE]
> Se precisar de suportar vários domínios de alto nível, tais como contoso.com e fabrikam.com, deve utilizar o interruptor **SupportMultipleDomain** com quaisquer cmdlets. Para obter mais informações, consulte [suporte para vários domínios de nível superior.](how-to-connect-install-multiple-domains.md)
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Reparar a confiança da AZure AD utilizando o Azure AD Connect<a name="connectrenew"></a>
Se configurar a sua quinta AD FS e a confiança AD AD através da utilização do Azure AD Connect, pode utilizar o Azure AD Connect para detetar se precisa de tomar qualquer ação para os seus certificados de assinatura simbólica. Se precisar de renovar os certificados, pode utilizar o Azure AD Connect para o fazer.

Para mais informações, consulte [reparar o fundo.](how-to-connect-fed-management.md)

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Etapas de atualização de certificado ad FS e Azure AD
Os certificados de assinatura token são certificados X509 padrão que são usados para assinar de forma segura todos os tokens que o servidor da federação emite. Os certificados de desencriptação token são certificados X509 padrão que são usados para desencriptar quaisquer fichas de entrada. 

Por predefinição, o AD FS é configurado para gerar automaticamente certificados de assinatura simbólica e de desencriptação simbólica, tanto na hora inicial de configuração como quando os certificados se aproximam da sua data de validade.

A Azure AD tenta obter um novo certificado dos metadados de serviço da federação 30 dias antes do termo do certificado atual. Caso não esteja disponível um novo certificado nessa altura, o Azure AD continuará a monitorizar os metadados em intervalos diários regulares. Assim que o novo certificado estiver disponível nos metadados, as definições da federação para o domínio são atualizadas com as novas informações do certificado. Pode utilizar `Get-MsolDomainFederationSettings` para verificar se vê o novo certificado no NextSigningCertificate / SigningCertificate.

Para obter mais informações sobre certificados de assinatura token em AD FS consulte [Obter e Configurar Certificados de Assinatura token e de desencriptação token para FS AD](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
