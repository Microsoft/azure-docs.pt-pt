---
title: Aquisição de administrador de diretório não gerido - Azure AD / Microsoft Docs
description: Como assumir um nome de domínio DNS numa organização AZure AD não gerida (inquilino-sombra).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: how-to
ms.workload: identity
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6a7dcb1d24f3c1ff848e3393687b04d79d28058
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90054709"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Assumir um diretório não gerido como administrador no Azure Active Directory

Este artigo descreve duas formas de assumir um nome de domínio DNS num diretório não gerido em Azure Ative Directory (Azure AD). Quando um utilizador autónomo se inscreve num serviço cloud que utiliza o Azure AD, é adicionado a um diretório do Azure AD não gerido, com base no domínio do respetivo e-mail Para obter mais sobre autosserviço ou inscrição "viral" para um serviço, consulte [o que é a inscrição de self-service para o Azure Ative Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Decida como quer assumir um diretório não gerido
Durante o processo de obtenção do controlo administrativo, pode provar a propriedade, conforme descrito em [Add a custom domain name to Azure AD](../fundamentals/add-custom-domain.md) (Adicionar um nome de domínio personalizado ao Azure AD). As secções seguintes explicam a experiência de administração mais detalhadamente, mas pode ver este resumo:

* Quando realizas uma [aquisição "interna"](#internal-admin-takeover) de um diretório não gerido do Azure, és adicionado como administrador global do diretório não gerido. Nenhum utilizador, domínio ou plano de serviço é migrado para qualquer outro diretório administrado por si.

* Quando executa uma [aquisição "externa"](#external-admin-takeover) de um diretório Azure não gerido, adiciona-se o nome de domínio DNS do diretório não gerido ao seu diretório Azure gerido. Quando adiciona o nome de domínio, é criado um mapeamento dos utilizadores para os recursos no diretório do Azure gerido, para que os utilizadores possam continuar a aceder a serviços sem interrupções. 

## <a name="internal-admin-takeover"></a>Aquisição de administrador interno

Alguns produtos que incluem SharePoint e OneDrive, como o Microsoft 365, não suportam aquisição externa. Se esse é o seu cenário, ou se você é um administrador e quer assumir uma organização AD AZure não gerida ou "sombra" criada por utilizadores que usaram inscrição de autosserviço, você pode fazê-lo com uma aquisição de administração interna.

1. Crie um contexto de utilizador na organização nãogerida através da inscrição no Power BI. Por conveniência de exemplo, estes passos assumem esse caminho.

2. Abra o [site Power BI](https://powerbi.com) e selecione Iniciar **Livre**. Introduza uma conta de utilizador que utilize o nome de domínio para a organização; por exemplo, `admin@fourthcoffee.xyz` . . Depois de introduzir o código de verificação, consulte o seu email para obter o código de confirmação.

3. No e-mail de confirmação do Power BI, selecione **Sim, sou eu.**

4. Inscreva-se no [centro de administração Microsoft 365](https://portal.office.com/admintakeover) com a conta de utilizador Power BI. Recebe uma mensagem que o instrui a **tornar-se o Administrador** do nome de domínio que já foi verificado na organização não gerida. selecionar **Sim, eu quero ser o administrador**.
  
   ![primeira screenshot para Tornar o Administrador](./media/domains-admin-takeover/become-admin-first.png)
  
5. Adicione o registo TXT para provar que possui o nome de domínio **quartocoffee.xyz** no seu registo de nome de domínio. Neste exemplo, é GoDaddy.com.
  
   ![Adicione um registo txt para o nome de domínio](./media/domains-admin-takeover/become-admin-txt-record.png)

Quando os registos DNS TXT forem verificados no seu registo de nomes de domínio, pode gerir a organização AD AZure.

Quando completar os passos anteriores, é agora o administrador global da organização Quarto Café na Microsoft 365. Para integrar o nome de domínio com os seus outros serviços Azure, pode removê-lo da Microsoft 365 e adicioná-lo a uma organização gerida diferente em Azure.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Adicionar o nome de domínio a uma organização gerida em Azure AD

1. Abra o [centro de administração Microsoft 365](https://admin.microsoft.com).
2. Selecione o separador **Utilizadores** e crie uma nova conta de utilizador com um nome como *utilizador \@ fourthcoffeexyz.onmicrosoft.com* que não utilize o nome de domínio personalizado. 
3. Certifique-se de que a nova conta de utilizador tem privilégios de administração global para a organização Azure AD.
4. Abra o **separador Domínios** no centro de administração Microsoft 365, selecione o nome de domínio e selecione **Remover**. 
  
   ![Remova o nome de domínio da Microsoft 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Se tiver algum utilizadores ou grupos no Microsoft 365 que refiram o nome de domínio removido, devem ser renomeados para o domínio .onmicrosoft.com. Se forçar a eliminação do nome de domínio, todos os utilizadores serão automaticamente renomeados, neste exemplo para * \@ fourthcoffeexyz.onmicrosoft.com do utilizador*.
  
6. Inscreva-se no [centro de administração Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que é o administrador global da organização Azure AD.
  
7. Selecione **nomes de domínio personalizados**e, em seguida, adicione o nome de domínio. Terá de introduzir os registos DNS TXT para verificar a propriedade do nome de domínio. 
  
   ![domínio verificado como adicionado ao Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Qualquer utilizadores do serviço Power BI ou Azure Rights Management que tenham licenças atribuídas na organização Microsoft 365 devem guardar os seus dashboards se o nome de domínio for removido. Devem iniciar sposição com um nome de utilizador como * \@ utilizador fourthcoffeexyz.onmicrosoft.com* em vez de utilizador * \@ quartocoffee.xyz*.

## <a name="external-admin-takeover"></a>Aquisição de administrador externo

Se já gere uma organização com serviços Azure ou Microsoft 365, não pode adicionar um nome de domínio personalizado se já estiver verificado noutra organização Azure AD. No entanto, a partir da sua organização gerida em Azure AD você pode assumir uma organização não gerida como uma aquisição de administração externa. O procedimento geral segue o artigo [Adicionar um domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md).

Quando verifica a propriedade do nome de domínio, a Azure AD remove o nome de domínio da organização não gerida e transfere-o para a sua organização existente. A aquisição externa de um diretório não gerido requer o mesmo processo de validação de DNS TXT que a aquisição interna de administração. A diferença é que os seguintes também são movidos com o nome de domínio:

- Utilizadores
- Subscrições
- Atribuições de licenças

### <a name="support-for-external-admin-takeover"></a>Apoio à aquisição de administradores externos
A aquisição de administração externa é suportada pelos seguintes serviços online:

- Azure Rights Management
- Exchange Online

Os planos de serviço apoiados incluem:

- PowerApps Grátis
- PowerFlow Grátis
- RMS para utilizadores
- Microsoft Stream
- Experiência gratuita Dynamics 365

A aquisição de administração externa não é suportada por qualquer serviço que tenha planos de serviço que incluam SharePoint, OneDrive ou Skype For Business; por exemplo, através de uma subscrição gratuita do Office. 

Pode utilizar opcionalmente a [opção **ForceTakeover** ](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) para remover o nome de domínio da organização não gerida e verificar na organização pretendida. 

#### <a name="more-information-about-rms-for-individuals"></a>Mais informações sobre RMS para indivíduos

Para [o RMS para indivíduos](/azure/information-protection/rms-for-individuals), quando a organização não gerido está na mesma região que a organização que possui, a chave de organização de [proteção de informação Azure](/azure/information-protection/plan-implement-tenant-key) criada automaticamente e [os modelos de proteção padrão](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) são ainda mais transferidos com o nome de domínio.

A chave e os modelos não são movidos quando a organização não gerido está em uma região diferente. Por exemplo, se a organização nãogerida está na Europa e a organização que possui está na América do Norte.

Embora o RMS para indivíduos seja projetado para apoiar a autenticação Azure AD para abrir conteúdo protegido, isso não impede que os utilizadores também protejam os conteúdos. Se os utilizadores protegerem o conteúdo com o RMS para subscrição de indivíduos, e a chave e os modelos não foram transferidos, esse conteúdo não é acessível após a tomada de posse do domínio.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Cmdlets Azure AD PowerShell para a opção ForceTakeover
Pode ver estes cmdlets utilizados no [exemplo powerShell](#powershell-example).

cmdlet | Utilização
------- | -------
`connect-msolservice` | Quando solicitado, inscreva-se na sua organização gerida.
`get-msoldomain` | Mostra os nomes de domínio associados à organização atual.
`new-msoldomain –name <domainname>` | Adiciona o nome de domínio à organização como Não verificado (ainda não foi realizada nenhuma verificação de DNS).
`get-msoldomain` | O nome de domínio está agora incluído na lista de nomes de domínio associados à sua organização gerida, mas está listado como **Não verificado**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Fornece as informações para colocar em novo registo DNS TXT para o domínio (MS=xxxxx). A verificação pode não acontecer imediatamente porque leva algum tempo para que o registo TXT se propague, por isso aguarde alguns minutos antes de considerar a opção **-ForceTakeover.** 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Se o seu nome de domínio ainda não estiver verificado, pode prosseguir com a opção **-ForceTakeover.** Verifica que o registo TXT foi criado e inicia o processo de aquisição.<li>A opção **-ForceTakeover** só deve ser adicionada ao cmdlet quando forçar uma aquisição de administração externa, como quando a organização não gerida tem serviços microsoft 365 bloqueando a aquisição.
`get-msoldomain` | A lista de domínios mostra agora o nome de domínio como **Verificado**.

> [!NOTE]
> A organização Azure AD não gerida é eliminada 10 dias após o exercício da opção de força de aquisição externa.

### <a name="powershell-example"></a>Exemplo do PowerShell

1. Ligue-se à Azure AD utilizando as credenciais que foram usadas para responder à oferta de self-service:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Obtenha uma lista de domínios:
  
   ```powershell
   Get-MsolDomain
   ```
3. Executar o Get-MsolDomainVerificationDns cmdlet para criar um desafio:
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
5. No seu espaço público de nomes DNS, crie um registo DNS txt que contenha o valor que copiou no passo anterior. O nome para este registo é o nome do domínio principal, por isso, se criar este registo de recursos utilizando a função DNS do Windows Server, deixe o nome Record em branco e cole o valor na caixa de Texto.
6. Executar o Confirm-MsolDomain cmdlet para verificar o desafio:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Por exemplo:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Um desafio bem sucedido devolve-lhe o pedido sem um erro.

## <a name="next-steps"></a>Passos seguintes

* [Adicionar um nome de domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md)
* [Como instalar e configurar o Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Referência de Cmdlet do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
