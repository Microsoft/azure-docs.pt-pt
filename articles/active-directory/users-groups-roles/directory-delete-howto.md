---
title: Excluir um diretório do AD do Azure-Azure Active Directory | Microsoft Docs
description: Explica como preparar um diretório do Azure AD para exclusão, incluindo diretórios de autoatendimento
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961822"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Excluir um diretório no Azure Active Directory

Quando um diretório do Azure AD é excluído, todos os recursos contidos no diretório também são excluídos. Prepare sua organização minimizando seus recursos associados antes de excluir. Somente um administrador global Azure Active Directory (AD do Azure) pode excluir um diretório do Azure AD do Portal.

## <a name="prepare-the-directory"></a>Preparar o diretório

Você não pode excluir um diretório no Azure AD até que ele passe por várias verificações. Essas verificações reduzem o risco de que a exclusão de um diretório do Azure AD afete negativamente o acesso do usuário, como a capacidade de entrar no Office 365 ou acessar recursos no Azure. Por exemplo, se o diretório associado a uma assinatura for excluído de forma não intencional, os usuários não poderão acessar os recursos do Azure para essa assinatura. Verificam-se as seguintes condições:

* Não pode haver nenhum usuário no diretório, exceto um administrador global, que deve excluir o diretório. Deverão ser eliminados todos os outros utilizadores antes de eliminar o diretório. Se os usuários estiverem sincronizados no local, a sincronização deverá primeiro ser desativada e os usuários deverão ser excluídos no diretório de nuvem usando os cmdlets portal do Azure ou Azure PowerShell.
* Não podem existir aplicações no diretório. Todos os aplicativos devem ser removidos antes que o diretório possa ser excluído.
* Não pode haver nenhum provedor de autenticação multifator vinculado ao diretório.
* Não podem existir subscrições para quaisquer Serviços Online da Microsoft, como o Microsoft Azure, o Office 365 ou o Azure AD Premium, associadas ao diretório. Por exemplo, se tiver criado um diretório predefinido no Azure, não o poderá eliminar se a sua subscrição do Azure ainda depender desse diretório para a autenticação. Do mesmo modo, não pode eliminar um diretório se outro utilizador tiver associado uma subscrição ao mesmo.

## <a name="delete-the-directory"></a>Excluir o diretório

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com uma conta que seja o administrador global da sua organização.

2. Selecione **Azure Active Directory**.

3. Alterne para o diretório que você deseja excluir.
  
   ![Confirmar organização antes de excluir](./media/directory-delete-howto/delete-directory-command.png)

4. Selecione **excluir diretório**.
  
   ![Selecione o comando para excluir a organização](./media/directory-delete-howto/delete-directory-list.png)

5. Se o diretório não passar por uma ou mais verificações, você receberá um link para obter mais informações sobre como passar. Depois de passar todas as verificações, selecione **excluir** para concluir o processo.

## <a name="if-you-cant-delete-the-directory"></a>Se você não puder excluir o diretório

Quando você configurou seu diretório do Azure AD, você também pode ter ativado assinaturas baseadas em licença para sua organização, como Azure AD Premium P2, Office 365 Business Premium ou Enterprise Mobility + Security e5. Para evitar a perda acidental de dados, não é possível excluir um diretório até que as assinaturas sejam totalmente excluídas. As assinaturas devem estar em um estado **desprovisionado** para permitir a exclusão do diretório. Uma assinatura **expirada** ou **cancelada** é movida para o estado **desabilitado** e o estágio final é o estado **desprovisionado** .

Para o que esperar quando uma assinatura de avaliação do Office 365 expira (não incluindo parceiro/CSP, Enterprise Agreement ou licenciamento de volume pago), consulte a tabela a seguir. Para obter mais informações sobre a retenção de dados do Office 365 e o ciclo de vida da assinatura, consulte [o que acontece com meus dados e acesso quando a minha assinatura do Office 365 for Business termina?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Estado da assinatura | Dados | Acesso aos dados
----- | ----- | -----
Ativo (30 dias para avaliação) | Dados acessíveis a todos | Os usuários têm acesso normal aos arquivos do Office 365 ou aos aplicativos<br>Os administradores têm acesso normal para Microsoft 365 centro de administração e recursos 
Expirado (30 dias) | Dados acessíveis a todos| Os usuários têm acesso normal aos arquivos do Office 365 ou aos aplicativos<br>Os administradores têm acesso normal para Microsoft 365 centro de administração e recursos
Desabilitado (30 dias) | Dados acessíveis somente para administrador | Os usuários não podem acessar arquivos do Office 365 ou aplicativos<br>Os administradores podem acessar o centro de administração do Microsoft 365, mas não podem atribuir licenças para ou atualizar usuários
Desprovisionado (30 dias após desabilitado) | Dados excluídos (excluídos automaticamente se nenhum outro serviço estiver em uso) | Os usuários não podem acessar arquivos do Office 365 ou aplicativos<br>Os administradores podem acessar o centro de administração do Microsoft 365 para comprar e gerenciar outras assinaturas

## <a name="delete-a-subscription"></a>Excluir uma assinatura

Você pode colocar uma assinatura no estado **desprovisionado** para ser excluída em três dias usando o centro de administração Microsoft 365.

1. Entre no centro de [Administração do Microsoft 365](https://admin.microsoft.com) com uma conta que seja um administrador global em sua organização. Se você estiver tentando excluir o diretório "contoso" que tem o domínio padrão inicial contoso.onmicrosoft.com, entre com um UPN, como admin@contoso.onmicrosoft.com.

2. Visualize o novo centro de administração do Microsoft 365 verificando se a opção **experimentar o novo centro de administração** está habilitada.

   ![Visualizar a nova experiência do centro de administração do M365](./media/directory-delete-howto/preview-toggle.png)

3. Depois que o novo centro de administração estiver habilitado, você precisará cancelar uma assinatura para poder excluí-la. Selecione **cobrança** e selecione **produtos & serviços**e, em seguida, selecione **cancelar assinatura** para a assinatura que você deseja cancelar. Você será levado a uma página de comentários.

   ![Escolha uma assinatura para cancelar](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Preencha o formulário de comentários e selecione **cancelar assinatura** para cancelar a assinatura.

   ![Comando Cancel na versão prévia da assinatura](./media/directory-delete-howto/cancel-command.png)

5. Agora você pode excluir a assinatura. Selecione **excluir** para a assinatura que você deseja excluir. Se você não conseguir encontrar a assinatura na página **produtos & Services** , verifique se você tem o **status da assinatura** definido como **todos**.

   ![Excluir o link para excluir a assinatura](./media/directory-delete-howto/delete-command.png)

6. Selecione **excluir assinatura** para excluir a assinatura e aceitar os termos e condições. Todos os dados são excluídos permanentemente dentro de três dias. Você pode [reativar a assinatura](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) durante o período de três dias se mudar de ideia.
  
   ![Leia atentamente os termos e condições](./media/directory-delete-howto/delete-terms.png)

7. Agora, o estado da assinatura foi alterado e a assinatura está marcada para exclusão. A assinatura entra no estado **desprovisionado** 72 horas depois.

8. Depois de ter excluído uma assinatura em seu diretório e 72 horas decorridos, você pode entrar novamente no centro de administração do Azure AD e não deve haver nenhuma ação necessária e nenhuma assinatura bloqueando a exclusão do diretório. Você deve ser capaz de excluir com êxito seu diretório do Azure AD.
  
   ![aprovação da verificação da assinatura na tela de exclusão](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Tenho uma assinatura de avaliação que bloqueia a exclusão

Há [produtos de inscrição de autoatendimento](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) como Microsoft Power BI, Rights Management Services, Microsoft Power Apps ou Dynamics 365, usuários individuais podem se inscrever por meio do Office 365, que também cria um usuário convidado para autenticação em seu diretório do Azure AD. Esses produtos de autoatendimento bloqueiam exclusões de diretório até que elas sejam totalmente excluídas do diretório, para evitar a perda de dados. Eles podem ser excluídos somente pelo administrador do Azure AD se o usuário se inscreveu individualmente ou foi atribuído ao produto.

Há dois tipos de produtos de inscrição de autoatendimento em como eles são atribuídos: 

* Atribuição de nível de organização: um administrador do Azure AD atribui o produto a toda a organização e um usuário pode estar usando ativamente o serviço com essa atribuição de nível de organização, mesmo que eles não sejam licenciados individualmente.
* Atribuição de nível de usuário: um usuário individual durante a inscrição de autoatendimento essencialmente atribui o produto a si mesmo sem um administrador. Depois que a organização for gerenciada por um administrador (consulte [tomada de administrador de um diretório não gerenciado](domains-admin-takeover.md), o administrador poderá atribuir diretamente o produto aos usuários sem inscrição de autoatendimento.  

Quando você inicia a exclusão do produto de inscrição de autoatendimento, a ação exclui permanentemente os dados e remove todo o acesso do usuário ao serviço. Qualquer usuário que tenha sido atribuído à oferta individualmente ou no nível da organização será impedido de entrar ou acessar os dados existentes. Se você quiser evitar a perda de dados com o produto de inscrição de autoatendimento, como o [Microsoft Power bi dashboards](https://docs.microsoft.com/power-bi/service-export-to-pbix) ou a [configuração de política de serviços de Rights Management](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), verifique se os dados são copiados em backup e salvos em outro lugar.

Para obter mais informações sobre produtos e serviços de inscrição de autoatendimento disponíveis no momento, consulte [programas de autoatendimento disponíveis](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Para o que esperar quando uma assinatura de avaliação do Office 365 expira (não incluindo parceiro/CSP, Enterprise Agreement ou licenciamento de volume pago), consulte a tabela a seguir. Para obter mais informações sobre a retenção de dados do Office 365 e o ciclo de vida da assinatura, consulte [o que acontece com meus dados e acesso quando a minha assinatura do Office 365 for Business termina?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Estado do produto | Dados | Acesso aos dados
------------- | ---- | --------------
Ativo (30 dias para avaliação) | Dados acessíveis a todos | Os usuários têm acesso normal ao produto de inscrição por autoatendimento, arquivos ou aplicativos<br>Os administradores têm acesso normal para Microsoft 365 centro de administração e recursos
Eliminado | Dados excluídos | Os usuários não podem acessar o produto, arquivos ou aplicativos de inscrição de autoatendimento<br>Os administradores podem acessar o centro de administração do Microsoft 365 para comprar e gerenciar outras assinaturas

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Como posso excluir um produto de inscrição de autoatendimento no portal do Azure?

Você pode colocar um produto de inscrição de autoatendimento como o Microsoft Power BI ou o Azure Rights Management Services em um estado de **exclusão** para ser imediatamente excluído no portal do AD do Azure.

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que seja um administrador global na organização. Se você estiver tentando excluir o diretório "contoso" que tem o domínio padrão inicial contoso.onmicrosoft.com, faça logon com um UPN, como admin@contoso.onmicrosoft.com.

2. Selecione **licenças**e, em seguida, selecione **produtos de inscrição por autoatendimento**. Você pode ver todos os produtos de inscrição de autoatendimento separadamente das assinaturas baseadas em assento. Escolha o produto que você deseja excluir permanentemente. Veja um exemplo no Microsoft Power BI:

    ![o nome de usuário está digitado ou não foi encontrado](./media/directory-delete-howto/licenses-page.png)

3. Selecione **excluir** para excluir o produto e aceitar os termos que os dados são excluídos imediatamente e irrevogávels. Esta ação de exclusão removerá todos os usuários e removerá o acesso da organização ao produto. Clique em Sim para avançar com a exclusão.  

    ![o nome de usuário está digitado ou não foi encontrado](./media/directory-delete-howto/delete-product.png)

4. Quando você selecionar **Sim**, a exclusão do produto de autoatendimento será iniciada. Há uma notificação informando sobre a exclusão em andamento.  

    ![o nome de usuário está digitado ou não foi encontrado](./media/directory-delete-howto/progress-message.png)

5. Agora, o estado do produto de inscrição de autoatendimento foi alterado para **excluído**. Quando você atualiza a página, o produto deve ser removido da página de **produtos de inscrição de autoatendimento** .  

    ![o nome de usuário está digitado ou não foi encontrado](./media/directory-delete-howto/product-deleted.png)

6. Depois de excluir todos os produtos, você pode entrar novamente no centro de administração do Azure AD e não deve haver nenhuma ação necessária e nenhum produto bloqueando a exclusão do diretório. Você deve ser capaz de excluir com êxito seu diretório do Azure AD.

    ![o nome de usuário está digitado ou não foi encontrado](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Passos seguintes

[Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
