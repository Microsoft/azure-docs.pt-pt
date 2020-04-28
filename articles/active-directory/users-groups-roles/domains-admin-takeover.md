---
title: Aquisição de administrador de um diretório não gerido - Azure AD [ Microsoft Docs
description: Como assumir um nome de domínio DNS numa organização não gerida da AD Azure (inquilino-sombra).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09012d93a1f9fd24427cb8b3937b3a36cf75d9e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75834175"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Assuma um diretório não gerido como administrador no Azure Ative Directory

Este artigo descreve duas formas de assumir um nome de domínio DNS num diretório não gerido em Azure Ative Directory (Azure AD). Quando um utilizador autónomo se inscreve num serviço cloud que utiliza o Azure AD, é adicionado a um diretório do Azure AD não gerido, com base no domínio do respetivo e-mail Para mais informações sobre autosserviço ou inscrição "viral" para um serviço, consulte O que é a [inscrição de self-service para o Azure Ative Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Decida como quer assumir um diretório não gerido
Durante o processo de obtenção do controlo administrativo, pode provar a propriedade, conforme descrito em [Add a custom domain name to Azure AD](../fundamentals/add-custom-domain.md) (Adicionar um nome de domínio personalizado ao Azure AD). As secções seguintes explicam a experiência de administração mais detalhadamente, mas pode ver este resumo:

* Quando faz uma [obtenção do controlo administrativo “interna”](#internal-admin-takeover) de um diretório do Azure não gerido, é adicionado como o administrador global desse diretório. Nenhum utilizador, domínio ou plano de serviço é migrado para outro diretório que seja administrado por si.

* Quando faz uma [obtenção do controlo administrativo “externo”](#external-admin-takeover) de um diretório do Azure não gerido, adiciona o nome de domínio DNS desse diretório ao seu diretório do Azure gerido. Quando adiciona o nome de domínio, é criado um mapeamento dos utilizadores para os recursos no diretório do Azure gerido, para que os utilizadores possam continuar a aceder a serviços sem interrupções. 

## <a name="internal-admin-takeover"></a>Aquisição interna de administradores

Alguns produtos que incluem o SharePoint e o OneDrive, como o Office 365, não suportam a aquisição externa. Se esse é o seu cenário, ou se você é um administrador e quer assumir um inquilino não gerido ou "sombra" criado por utilizadores que usaram a inscrição de self-service, você pode fazê-lo com uma aquisição interna de administração.

1. Crie um contexto de utilizador no inquilino não gerido através da inscrição no Power BI. Por conveniência, por exemplo, estes passos assumem esse caminho.

2. Abra o [site power BI](https://powerbi.com) e selecione Start **Free**. Insira uma conta de utilizador que utilize o nome de domínio para a organização; por exemplo, `admin@fourthcoffee.xyz`. Depois de introduzir o código de verificação, consulte o seu e-mail para obter o código de confirmação.

3. No e-mail de confirmação do Power BI, selecione **Sim, sou eu.**

4. Inscreva-se no centro de administração da [Microsoft 365](https://portal.office.com/admintakeover) com a conta de utilizador do Power BI. Você recebe uma mensagem que o instrui a **tornar-se o Administrador** do nome de domínio que já foi verificado no inquilino não gerido. selecione **Sim, quero ser o administrador.**
  
   ![primeira imagem para Become the Admin](./media/domains-admin-takeover/become-admin-first.png)
  
5. Adicione o registo TXT para provar que possui o nome de domínio **quartocafé.xyz** no registo do seu nome de domínio. Neste exemplo, é GoDaddy.com.
  
   ![Adicione um disco txt para o nome de domínio](./media/domains-admin-takeover/become-admin-txt-record.png)

Quando os registos DNS TXT forem verificados no seu registo de nome de domínio, pode gerir o inquilino Azure AD.

Quando completar os passos anteriores, é agora o administrador global do Quarto Inquilino do Café no Escritório 365. Para integrar o nome de domínio com os seus outros serviços Azure, pode removê-lo do Office 365 e adicioná-lo a um inquilino gerido diferente em Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Adicionar o nome de domínio a um inquilino gerido em Azure AD

1. Abra o [centro de administração da Microsoft 365](https://admin.microsoft.com).
2. Selecione o separador **Utilizadores** e crie uma nova conta de utilizador com um nome como *utilizador\@fourthcoffeexyz.onmicrosoft.com* que não utilize o nome de domínio personalizado. 
3. Certifique-se de que a nova conta de utilizador tem privilégios administrativos globais para o inquilino da AD Azure.
4. Abra o separador **Domínios** no centro de administração da Microsoft 365, selecione o nome de domínio e selecione **Remover**. 
  
   ![remover o nome de domínio do Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Se tiver algum utilizador ou grupo no Office 365 que refira o nome de domínio removido, deve ser renomeado para o domínio .onmicrosoft.com. Se forçar a apagar o nome de domínio, todos os utilizadores são automaticamente renomeados, neste exemplo para *o utilizador\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Inscreva-se no centro de [administração da Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que é a administração global do inquilino da AD Azure.
  
7. Selecione nomes de **domínio personalizados**e, em seguida, adicione o nome de domínio. Terá de introduzir os registos DNS TXT para verificar a propriedade do nome de domínio. 
  
   ![domínio verificado como adicionado à AD Azure](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Qualquer utilizador do serviço Power BI ou Azure Rights Management que tenha licenças atribuídas no Office 365 tenant deve guardar os seus dashboards se o nome de domínio for removido. Devem iniciar sessão com um nome de utilizador como *utilizador\@fourthcoffeexyz.onmicrosoft.com* em vez de utilizador *\@quartocafé.xyz*.

## <a name="external-admin-takeover"></a>Aquisição externa de administradores

Se já gere um inquilino com serviços Azure ou Office 365, não pode adicionar um nome de domínio personalizado se já estiver verificado em outro inquilino da AD Azure. No entanto, a partir do seu inquilino gerido em Azure AD você pode assumir um inquilino não gerido como uma aquisição de administrador externo. O procedimento geral segue o artigo [Adicione um domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md).

Quando verifica a propriedade do nome de domínio, a Azure AD remove o nome de domínio do inquilino não gerido e transfere-o para o seu inquilino existente. A aquisição externa de administradores de um diretório não gerido requer o mesmo processo de validação dNS TXT que a aquisição interna de administradores. A diferença é que os seguintes também são movidos com o nome de domínio:

- Utilizadores
- Subscrições
- Atribuição de licenças

### <a name="support-for-external-admin-takeover"></a>Apoio à aquisição de administradores externos
A aquisição de administradores externos é apoiada pelos seguintes serviços online:

- Azure Rights Management
- Exchange Online

Os planos de serviço suportados incluem:

- PowerApps Grátis
- PowerFlow Livre
- RMS para utilizadores
- Microsoft Stream
- Dinâmica 365 teste gratuito

A aquisição de administradores externos não é suportada para qualquer serviço que tenha planos de serviço que incluam SharePoint, OneDrive ou Skype For Business; por exemplo, através de uma subscrição gratuita do Office. 

Você pode opcionalmente usar a [opção **ForceTakeover** ](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) para remover o nome de domínio do inquilino não gerido e verificar no inquilino desejado. 

#### <a name="more-information-about-rms-for-individuals"></a>Mais informações sobre RMS para indivíduos

Para [rms para indivíduos](/azure/information-protection/rms-for-individuals), quando o inquilino não gerido está na mesma região que o inquilino que você possui, a chave de inquilino sinuoso de proteção de [informação azure](/azure/information-protection/plan-implement-tenant-key) criada automaticamente e os modelos de [proteção padrão](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) são adicionalmente transferidos com o nome de domínio.

A chave e os modelos não são movidos quando o inquilino não gerido está em outra região. Por exemplo, se o inquilino não gerido estiver na Europa e a organização que possui está na América do Norte.

Embora o RMS para indivíduos seja projetado para apoiar a autenticação azure AD para abrir conteúdo protegido, isso não impede que os utilizadores também protejam os conteúdos. Se os utilizadores protegiam o conteúdo com o RMS para a subscrição individual, e as chaves e modelos não foram movidos, esse conteúdo não é acessível após a aquisição do domínio.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Cmdlets Azure AD PowerShell para a opção ForceTakeover
Pode ver estes cmdlets usados no [exemplo PowerShell](#powershell-example).

cmdlet | Utilização
------- | -------
`connect-msolservice` | Quando solicitado, inscreva-se no seu inquilino gerido.
`get-msoldomain` | Mostra os nomes de domínio associados ao atual inquilino.
`new-msoldomain –name <domainname>` | Adiciona o nome de domínio ao inquilino como Não verificado (ainda não foi realizada nenhuma verificação de DNS).
`get-msoldomain` | O nome de domínio está agora incluído na lista de nomes de domínio associados ao seu inquilino gerido, mas está listado como **Não verificado**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Fornece a informação para colocar no novo registo DNS TXT para o domínio (MS=xxxxx). A verificação pode não acontecer imediatamente porque leva algum tempo para o registo TXT se propagar, por isso espere alguns minutos antes de considerar a opção **-ForceTakeover.** 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Se o seu nome de domínio ainda não for verificado, pode prosseguir com a opção **-ForceTakeover.** Verifica que o registo TXT foi criado e dá início ao processo de aquisição.<li>A opção **-ForceTakeover** só deve ser adicionada ao cmdlet quando forçar uma aquisição de administração externa, como quando o inquilino não gerido tem serviços do Office 365 que bloqueiam a aquisição.
`get-msoldomain` | A lista de domínios mostra agora o nome de domínio como **Verificado**.

> [!NOTE]
> A organização não gerida azure ad é eliminada 10 dias após o exercício da opção de força de aquisição externa.

### <a name="powershell-example"></a>Exemplo do PowerShell

1. Ligue-se à AD Azure utilizando as credenciais que foram usadas para responder à oferta de self-service:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Obtenha uma lista de domínios:
  
   ```powershell
   Get-MsolDomain
   ```
3. Executar o cmdlet Get-MsolDomainVerificationDns para criar um desafio:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Por exemplo:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Copie o valor (o desafio) que é devolvido deste comando. Por exemplo:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. No seu espaço de nome SN público, crie um registo DNS txt que contenha o valor que copiou no passo anterior. O nome deste registo é o nome do domínio principal, por isso, se criar este registo de recursos utilizando a função DNS do Windows Server, deixe o nome Record em branco e apenas colhe o valor na caixa de texto.
6. Executar o cmdlet Confirm-MsolDomain para verificar o desafio:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Por exemplo:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Um desafio bem sucedido devolve-o à solicitação sem um erro.

## <a name="next-steps"></a>Passos seguintes

* [Adicione um nome de domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md)
* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referência de Cmdlet do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
