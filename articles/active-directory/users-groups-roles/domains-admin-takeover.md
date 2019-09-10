---
title: Tomada de administrador de um diretório não gerenciado-Azure Active Directory | Microsoft Docs
description: Como assumir um nome de domínio DNS em um diretório não gerenciado (locatário de sombra) em Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a756f0d9fe3669ab9d0f2b4576a35be5d2112a87
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70872207"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Assuma um diretório não gerenciado como administrador no Azure Active Directory

Este artigo descreve duas maneiras de assumir um nome de domínio DNS em um diretório não gerenciado no Azure Active Directory (AD do Azure). Quando um utilizador autónomo se inscreve num serviço cloud que utiliza o Azure AD, é adicionado a um diretório do Azure AD não gerido, com base no domínio do respetivo e-mail Para obter mais informações sobre a inscrição de autoatendimento ou "viral" para um serviço, consulte [o que é a inscrição de autoatendimento para Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Decidir como você deseja assumir um diretório não gerenciado
Durante o processo de obtenção do controlo administrativo, pode provar a propriedade, conforme descrito em [Add a custom domain name to Azure AD](../fundamentals/add-custom-domain.md) (Adicionar um nome de domínio personalizado ao Azure AD). As secções seguintes explicam a experiência de administração mais detalhadamente, mas pode ver este resumo:

* Quando faz uma [obtenção do controlo administrativo “interna”](#internal-admin-takeover) de um diretório do Azure não gerido, é adicionado como o administrador global desse diretório. Nenhum utilizador, domínio ou plano de serviço é migrado para outro diretório que seja administrado por si.

* Quando faz uma [obtenção do controlo administrativo “externo”](#external-admin-takeover) de um diretório do Azure não gerido, adiciona o nome de domínio DNS desse diretório ao seu diretório do Azure gerido. Quando adiciona o nome de domínio, é criado um mapeamento dos utilizadores para os recursos no diretório do Azure gerido, para que os utilizadores possam continuar a aceder a serviços sem interrupções. 

## <a name="internal-admin-takeover"></a>Tomada de administrador interno

Alguns produtos que incluem o SharePoint e o OneDrive, como o Office 365, não dão suporte a tomada externos. Se esse for o seu cenário, ou se você for um administrador e quiser assumir um locatário não gerenciado ou de "sombra" criado por usuários que usaram inscrição de autoatendimento, poderá fazer isso com um tomada de administrador interno.

1. Crie um contexto de usuário no locatário não gerenciado por meio da inscrição para Power BI. Para conveniência do exemplo, essas etapas pressupõem esse caminho.

2. Abra o [site do Power bi](https://powerbi.com) e selecione **Iniciar gratuitamente**. Insira uma conta de usuário que usa o nome de domínio para a organização; por exemplo, `admin@fourthcoffee.xyz`. Depois de inserir o código de verificação, verifique o código de confirmação do seu email.

3. No email de confirmação de Power BI, selecione **Sim, sou eu**.

4. Entre no centro de [Administração do Microsoft 365](https://admin.microsoft.com) com a conta de usuário do Power bi. Você recebe uma mensagem que instrui você a **se tornar o administrador** do nome de domínio que já foi verificado no locatário não gerenciado. Selecione **Sim, quero ser o administrador**.
  
   ![primeira captura de tela para tornar-se o administrador](./media/domains-admin-takeover/become-admin-first.png)
  
5. Adicione o registro TXT para provar que você possui o nome de domínio **fourthcoffee. xyz** em seu registrador de nome de domínio. Neste exemplo, é GoDaddy.com.
  
   ![Adicionar um registro txt para o nome de domínio](./media/domains-admin-takeover/become-admin-txt-record.png)

Quando os registros TXT do DNS são verificados em seu registrador de nome de domínio, você pode gerenciar o locatário do Azure AD.

Quando você concluir as etapas anteriores, agora será o administrador global do locatário da Fourth Coffee no Office 365. Para integrar o nome de domínio com seus outros serviços do Azure, você pode removê-lo do Office 365 e adicioná-lo a um locatário gerenciado diferente no Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Adicionando o nome de domínio a um locatário gerenciado no Azure AD

1. Abra o [centro de administração do Microsoft 365](https://admin.microsoft.com).
2. Selecione a guia **usuários** e crie uma nova conta de usuário com um nome como o *usuário\@fourthcoffeexyz.onmicrosoft.com* que não usa o nome de domínio personalizado. 
3. Verifique se a nova conta de usuário tem privilégios de administrador global para o locatário do Azure AD.
4. Abra a guia **domínios** no centro de administração Microsoft 365, selecione o nome de domínio e selecione **remover**. 
  
   ![remover o nome de domínio do Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Se você tiver usuários ou grupos no Office 365 que referenciem o nome de domínio removido, eles deverão ser renomeados para o domínio. onmicrosoft.com. Se você forçar a exclusão do nome de domínio, todos os usuários serão automaticamente renomeados, neste exemplo, para o *usuário\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Entre no centro de [Administração do Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que seja o administrador global do locatário do Azure AD.
  
7. Selecione **nomes de domínio personalizados**e, em seguida, adicione o nome de domínio. Você precisará inserir os registros TXT do DNS para verificar a propriedade do nome de domínio. 
  
   ![domínio verificado como adicionado ao Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Qualquer usuário do Power BI ou serviço de Rights Management do Azure que tenha licenças atribuídas no locatário do Office 365 deverá salvar seus painéis se o nome de domínio for removido. Eles devem entrar com um nome de usuário como *o\@usuário fourthcoffeexyz.onmicrosoft.com* , em vez de *usuário\@fourthcoffee. xyz*.

## <a name="external-admin-takeover"></a>Tomada de administrador externo

Se você já gerencia um locatário com os serviços do Azure ou o Office 365, não é possível adicionar um nome de domínio personalizado se ele já estiver verificado em outro locatário do Azure AD. No entanto, de seu locatário gerenciado no Azure AD, você pode assumir um locatário não gerenciado como um tomada de administrador externo. O procedimento geral segue o artigo [Adicionar um domínio personalizado ao Azure ad](../fundamentals/add-custom-domain.md).

Quando você verifica a propriedade do nome de domínio, o Azure AD remove o nome de domínio do locatário não gerenciado e o move para o locatário existente. O tomada de administrador externo de um diretório não gerenciado requer o mesmo processo de validação do DNS TXT que o administrador interno tomada. A diferença é que os itens a seguir também são movidos com o nome de domínio:

- Utilizadores
- Subscrições
- Atribuições de licença

### <a name="support-for-external-admin-takeover"></a>Suporte para tomada de administrador externo
O tomada de administrador externo tem suporte pelo seguinte serviços online:

- Power BI
- Azure Rights Management
- Exchange Online

Os planos de serviço com suporte incluem:

- Power BI gratuito
- Power BI Pro
- PowerApps gratuito
- PowerFlow gratuito
- RMS para pessoas físicas
- Microsoft Stream
- Avaliação gratuita do Dynamics 365

O tomada de administração externa não tem suporte para nenhum serviço que tenha planos de serviço que incluam o SharePoint, OneDrive ou Skype for Business; por exemplo, por meio de uma assinatura gratuita do Office. 

Opcionalmente, você pode usar a [opção **ForceTakeover** ](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) para remover o nome de domínio do locatário não gerenciado e verificá-lo no locatário desejado. **A opção ForceTakeover não se move por usuários nem mantém o acesso à assinatura. Essa opção move apenas o nome de domínio.**

#### <a name="more-information-about-rms-for-individuals"></a>Mais informações sobre o RMS para pessoas físicas

Para o [RMS para pessoas físicas](/azure/information-protection/rms-for-individuals), quando o locatário não gerenciado está na mesma região que o locatário que você possui, a chave de [locatário da proteção de informações do Azure](/azure/information-protection/plan-implement-tenant-key) criada automaticamente e os [modelos de proteção padrão](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) também são movidos com o nome de domínio.

A chave e os modelos não são movidos quando o locatário não gerenciado está em uma região diferente. Por exemplo, se o locatário não gerenciado estiver na Europa e a organização que você possui estiver em América do Norte.

Embora o RMS para indivíduos seja projetado para dar suporte à autenticação do Azure AD para abrir o conteúdo protegido, ele não impede que os usuários também protejam o conteúdo. Se os usuários tiverem protegido o conteúdo com a assinatura do RMS para pessoas físicas e a chave e os modelos não tiverem sido movidos, esse conteúdo não poderá ser acessado após o tomada de domínio.

#### <a name="more-information-about-power-bi"></a>Mais informações sobre Power BI

Quando você executa um tomada externo, Power BI conteúdo que foi criado antes da tomada é colocado em um [espaço de trabalho Power bi arquivado](/power-bi/service-admin-power-bi-archived-workspace). Você deve migrar manualmente todo o conteúdo que deseja usar no novo locatário.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Cmdlets do PowerShell do Azure AD para a opção ForceTakeover
Você pode ver esses cmdlets usados no [exemplo do PowerShell](#powershell-example).

Cmdlet | Utilização
------- | -------
`connect-msolservice` | Quando solicitado, entre no seu locatário gerenciado.
`get-msoldomain` | Mostra os nomes de domínio associados ao locatário atual.
`new-msoldomain –name <domainname>` | Adiciona o nome de domínio ao locatário como não verificado (nenhuma verificação de DNS foi executada ainda).
`get-msoldomain` | O nome de domínio agora está incluído na lista de nomes de domínio associados ao seu locatário gerenciado, mas está listado como não **verificado**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Fornece as informações a serem colocadas em um novo registro TXT do DNS para o domínio (MS = xxxxx). A verificação pode não acontecer imediatamente porque leva algum tempo para que o registro TXT se propague, portanto, aguarde alguns minutos antes de considerar a opção **-ForceTakeover** . 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Se o nome de domínio ainda não estiver verificado, você poderá prosseguir com a opção **-ForceTakeover** . Ele verifica se o registro TXT foi criado e inicia o processo tomada.<li>A opção **-ForceTakeover** deve ser adicionada ao cmdlet somente ao forçar um tomada de administrador externo, como quando o locatário não gerenciado tem os serviços do Office 365 bloqueando o tomada.
`get-msoldomain` | A lista de domínios agora mostra o nome de domínio como **verificado**.

> [!NOTE]
> A organização não gerenciada do Azure AD é excluída 10 dias depois que você exercita a opção de força de tomada externa.

### <a name="powershell-example"></a>Exemplo do PowerShell

1. Conecte-se ao Azure AD usando as credenciais que foram usadas para responder à oferta de autoatendimento:
   ```powershell
    Install-Module -Name MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
   ```
2. Obter uma lista de domínios:
  
   ```powershell
    Get-MsolDomain
   ```
3. Execute o cmdlet Get-MsolDomainVerificationDns para criar um desafio:
   ```powershell
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Copie o valor (o desafio) que é retornado deste comando. Por exemplo:
   ```powershell
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Em seu namespace DNS público, crie um registro DNS txt que contenha o valor que você copiou na etapa anterior. O nome desse registro é o nome do domínio pai, portanto, se você criar esse registro de recurso usando a função DNS do Windows Server, deixe o nome do registro em branco e apenas Cole o valor na caixa de texto.
6. Execute o cmdlet Confirm-MsolDomain para verificar o desafio:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
   ```
  
   Por exemplo:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
   ```

Um desafio bem-sucedido retorna ao prompt sem erro.

## <a name="next-steps"></a>Passos Seguintes

* [Adicionar um nome de domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md)
* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referência de Cmdlet do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
