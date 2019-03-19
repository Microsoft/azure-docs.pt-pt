---
title: Obtenção de controlo de um diretório não gerido - administrador - Azure Active Directory | Documentos da Microsoft
description: Como assumir um nome de domínio DNS num diretório não gerido (inquilino de cópias de sombra) no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 01/28/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: f166555e2f21ed38e78e659ec181c2d5d90d6bf2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887005"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Assumir um diretório não gerido como administrador no Azure Active Directory
Este artigo descreve duas formas de assumir um nome de domínio DNS num diretório não gerido no Azure Active Directory (Azure AD). Quando um utilizador autónomo se inscreve num serviço cloud que utiliza o Azure AD, é adicionado a um diretório do Azure AD não gerido, com base no domínio do respetivo e-mail Para mais informações sobre o Self-Service ou "viral" inscrever-se de um serviço, consulte [o que é a inscrição self-service do Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Decidir como pretende assumir um diretório não gerido
Durante o processo de obtenção do controlo administrativo, pode provar a propriedade, conforme descrito em [Add a custom domain name to Azure AD](../fundamentals/add-custom-domain.md) (Adicionar um nome de domínio personalizado ao Azure AD). As secções seguintes explicam a experiência de administração mais detalhadamente, mas pode ver este resumo:

* Quando faz uma [obtenção do controlo administrativo “interna”](#internal-admin-takeover) de um diretório do Azure não gerido, é adicionado como o administrador global desse diretório. Nenhum utilizador, domínio ou plano de serviço é migrado para outro diretório que seja administrado por si.

* Quando faz uma [obtenção do controlo administrativo “externo”](#external-admin-takeover) de um diretório do Azure não gerido, adiciona o nome de domínio DNS desse diretório ao seu diretório do Azure gerido. Quando adiciona o nome de domínio, é criado um mapeamento dos utilizadores para os recursos no diretório do Azure gerido, para que os utilizadores possam continuar a aceder a serviços sem interrupções. 

## <a name="internal-admin-takeover"></a>Obtenção de controlo de administrador interno

Alguns produtos que incluem o SharePoint e o OneDrive, como o Office 365, não suportam a obtenção de controlo externa. Se esse for o seu cenário, ou se for um administrador e quiser assumir um não-gerenciado ou o inquilino "sombra" para criar os utilizadores que utilizaram a inscrição Self-Service, pode fazê-lo com uma obtenção de controlo de administrador interno.

1. Crie um contexto de utilizador no inquilino não gerido por meio de inscrever-se com como o Power BI. Para sua comodidade de exemplo, estes passos partem do princípio de que o caminho.

2. Abra o [site do Power BI](https://powerbi.com) e selecione **começar gratuitamente**. Introduza uma conta de utilizador que utiliza o nome de domínio para a organização; Por exemplo, `admin@fourthcoffee.xyz`. Depois de introduzir o código de verificação, verificar o seu e-mail para o código de confirmação.

3. No e-mail de confirmação do Power BI, selecione **Sim, sou eu**.

4. Inicie sessão para o [Centro de administração do Office 365](https://portal.office.com/admintakeover) com a conta de utilizador do Power BI. Receberá uma mensagem que instrui lhe **tornar-se o administrador** de nome de domínio que já foi verificado o inquilino não gerido. Selecione **Sim, pretendo ser o administrador**.
  
   ![captura de ecrã primeiro para tornar-se o administrador](./media/domains-admin-takeover/become-admin-first.png)
  
5. Adicionar o registo TXT para provar que seu nome de domínio **fourthcoffee.xyz** na sua entidade de registo de nome de domínio. Neste exemplo, é GoDaddy.com.
  
   ![Adicione um registo txt para o nome de domínio](./media/domains-admin-takeover/become-admin-txt-record.png)

Quando os registos TXT de DNS são verificados na sua entidade de registo de nome de domínio, pode gerir o inquilino do Azure AD.

Quando concluir os passos anteriores, está agora o administrador global do inquilino café quarto no Office 365. Para integrar o nome de domínio com outros serviços do Azure, pode removê-lo a partir do Office 365 e adicioná-lo para um inquilino gerido diferente no Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Adicionar o nome de domínio para um inquilino gerido no Azure AD

1. Abra o [Centro de administração do Office 365](https://portal.office.com/admintakeover).
2. Selecione **usuários** separador e criar uma nova conta de utilizador com um nome como *utilizador\@fourthcoffeexyz.onmicrosoft.com* que não utiliza o nome de domínio personalizado. 
3. Certifique-se de que a nova conta de utilizador tem privilégios de administrador global do inquilino do Azure AD.
4. Open **domínios** separador no Centro de administração do Office 365, selecione o nome de domínio e selecione **remover**. 
  
   ![Remova o nome de domínio do Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Se tiver quaisquer utilizadores ou grupos do Office 365 que referenciam o nome de domínio removidos, tem de ser mudados para o. domínio onmicrosoft.com. Se forçar a eliminar o nome de domínio, todos os utilizadores são automaticamente renomeou, neste exemplo, para *usuário\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Inicie sessão para o [Centro de administração do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que seja o administrador global do inquilino do Azure AD.
  
7. Selecione **nomes de domínio personalizado**, em seguida, adicione o nome de domínio. Terá de introduzir os registos TXT de DNS para verificar a propriedade do nome do domínio. 
  
   ![domínio adicionado para o Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Qualquer usuário do serviço Power BI ou o Azure Rights Management que possuem licenças atribuídas no inquilino do Office 365 deve salvar seus dashboards, se o nome de domínio for removido. Tem de iniciar sessão com um nome de utilizador, como *usuário\@fourthcoffeexyz.onmicrosoft.com* vez *utilizador\@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Obtenção do controlo administrativo externo

Se já gere um inquilino com os serviços do Azure ou do Office 365, não é possível adicionar um nome de domínio personalizado, se ele já foi verificado noutro inquilino do Azure AD. No entanto, no seu inquilino gerido no Azure AD é possível assumir um inquilino não gerido como uma aquisição de admin externo. O procedimento geral segue o artigo [adicionar um domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md).

Ao verificar a propriedade do nome do domínio, o Azure AD remove o nome de domínio do inquilino não gerido e move-o para o seu inquilino existente. Obtenção do controlo administrativo externos de um diretório não gerido requer o mesmo processo de validação de TXT de DNS como a obtenção de controlo de administrador interno. A diferença é que a seguir é também movida com o nome de domínio:

- Utilizadores
- Subscrições
- Atribuições de licenças

### <a name="support-for-external-admin-takeover"></a>Suporte para obtenção do controlo administrativo externo
Obtenção do controlo administrativo externo é suportada pelos seguintes serviços online:

- Power BI
- Azure Rights Management
- Exchange Online

Os planos de serviço com suporte incluem:

- Power BI gratuito
- Power BI Pro
- Gratuita do PowerApps
- PowerFlow gratuita
- RMS para indivíduos
- Microsoft Stream
- Versão de avaliação gratuita do Dynamics 365

Obtenção do controlo administrativo externo não é suportada para qualquer serviço que tem planos de serviço que incluem o SharePoint, OneDrive ou o Skype para empresas; Por exemplo, por meio de uma subscrição gratuita do Office ou a SKU básica do Office. Opcionalmente, pode utilizar o [ **ForceTakeover** opção](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) para remover o nome de domínio do inquilino não gerido e verificar o inquilino pretendido. Esta opção ForceTakeover não será mover mais usuários, ou manter o acesso à subscrição. Em vez disso, esta opção apenas se move o nome de domínio. 

#### <a name="more-information-about-rms-for-individuals"></a>Obter mais informações sobre o RMS para indivíduos

Para [RMS para indivíduos](/azure/information-protection/rms-for-individuals), quando o inquilino não gerido está na mesma região que o inquilino, que é proprietário, criada automaticamente [chave de inquilino do Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) e [padrão modelos de proteção](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) além disso são movidos com o nome de domínio. 

A chave e os modelos não são movidos através de quando o inquilino não gerido estiver numa região diferente. Por exemplo, o inquilino não gerido está na Europa e o inquilino que for o proprietário é na América do Norte. 

Embora o RMS para indivíduos é projetado para oferecer suporte a autenticação do Azure AD para abrir conteúdo protegido, ele não impede que os utilizadores também proteger conteúdo. Se os utilizadores de proteger conteúdos com a subscrição do RMS para indivíduos e a chave e os modelos não foram movidos ao longo, esse conteúdo não estará acessível após a aquisição de domínio.

#### <a name="more-information-about-power-bi"></a>Obter mais informações sobre o Power BI

Quando efetua um obtenção de controlo, conteúdo do Power BI externo que foi criado antes da obtenção de controlo é colocada numa [área de trabalho do Power BI arquivados](/power-bi/service-admin-power-bi-archived-workspace). Manualmente terá de migrar qualquer conteúdo que pretende utilizar no novo inquilino.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Cmdlets do Azure AD PowerShell para a opção ForceTakeover
Pode ver estes cmdlets utilizados [exemplo de PowerShell](#powershell-example).


cmdlet | Utilização 
------- | -------
`connect-msolservice` | Quando lhe for pedido, inicie sessão no seu inquilino gerido.
`get-msoldomain` | Mostra os nomes de domínio associados ao inquilino atual.
`new-msoldomain –name <domainname>` | Adiciona o nome de domínio de inquilino como verificado (sem a verificação do DNS foi realizada ainda).
`get-msoldomain` | O nome de domínio agora está incluído na lista de nomes de domínio associado ao seu inquilino gerido, mas está listado como **verificado**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Fornece as informações para colocar no novo registo TXT de DNS para o domínio (MS = xxxxx). Verificação pode não acontecer imediatamente porque demora algum tempo para o registo TXT propagar, por isso, aguarde alguns minutos antes de considerar a **- ForceTakeover** opção. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Se o seu nome de domínio ainda não está a ser verificado, pode continuar com o **- ForceTakeover** opção. Verifica se o registo TXT foi criado e inicia o processo de obtenção de controlo.<li>O **- ForceTakeover** opção deve ser adicionada ao cmdlet, apenas quando forçar uma obtenção do controlo administrativo externo, como quando o inquilino não gerido tem serviços do Office 365, a obtenção de controlo de bloqueio.
`get-msoldomain` | A lista de domínio mostra agora o nome de domínio como **verificado**.

### <a name="powershell-example"></a>Exemplo do PowerShell

1. Ligar ao Azure AD com as credenciais que foram usadas para responder à oferta self-service:
   ```
    Install-Module -Name MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
   ```
2. Obter uma lista de domínios:
  
   ```
    Get-MsolDomain
   ```
3. Execute o cmdlet Get-MsolDomainVerificationDns para criar um desafio:
   ```
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Copie o valor (o desafio), que é devolvido a partir deste comando. Por exemplo:
   ```
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. No espaço de nomes DNS público, crie um registo txt DNS que contém o valor que copiou no passo anterior. O nome para este registo é o nome do domínio pai, portanto, se criar este registo de recursos ao utilizar a função DNS do Windows Server, deixe colar registo nome em branco e apenas o valor na caixa de texto.
6. Execute o cmdlet Confirm-MsolDomain para verificar o desafio:
  
   ```
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
   ```
  
   Por exemplo:
  
   ```
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
   ```

Um desafio com êxito retorna à linha de comandos sem erros.

## <a name="next-steps"></a>Passos Seguintes
* [Adicionar um nome de domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md)
* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referência de Cmdlet do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
