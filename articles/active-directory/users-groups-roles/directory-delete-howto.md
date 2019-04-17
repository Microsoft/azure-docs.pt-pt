---
title: Eliminar um diretório do Azure AD - Azure Active Directory | Documentos da Microsoft
description: Explica como preparar um diretório do Azure AD para eliminação, incluindo diretórios Self-serviços
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
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607301"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Eliminar um diretório no Azure Active Directory

Quando um diretório do Azure AD é eliminado, também são eliminados todos os recursos que estão contidos no diretório. Prepare a sua organização ao minimizarem os seus recursos associados antes de eliminar. Apenas um administrador global do Azure Active Directory (Azure AD) pode eliminar um diretório do Azure AD a partir do portal.

## <a name="prepare-the-directory"></a>Preparar o diretório

Não é possível eliminar um diretório no Azure AD, até que ele passa diversas verificações. Essas verificações de reduzem o risco de eliminar um diretório do Azure AD negativamente a afeta o acesso de utilizador, como a capacidade de iniciar sessão no Office 365 ou acederem a recursos no Azure. Por exemplo, se o diretório associado uma subscrição for eliminado involuntariamente, os utilizadores não é possível aceder os recursos do Azure para essa subscrição. Verificam-se as seguintes condições:

* Não pode haver nenhum utilizador no diretório, exceto um administrador global que é eliminar o diretório. Deverão ser eliminados todos os outros utilizadores antes de eliminar o diretório. Se os utilizadores forem sincronizados no local, em seguida, sincronizar primeiro deve estar desativada e os utilizadores têm de ser eliminados do diretório na cloud com o portal do Azure ou cmdlets do PowerShell do Azure.
* Não podem existir aplicações no diretório. Todas as aplicações têm de ser removidas antes de pode eliminar o diretório.
* É possível não existem fornecedores de autenticação multifator associados ao diretório.
* Não podem existir subscrições para quaisquer Serviços Online da Microsoft, como o Microsoft Azure, o Office 365 ou o Azure AD Premium, associadas ao diretório. Por exemplo, se tiver criado um diretório predefinido no Azure, não o poderá eliminar se a sua subscrição do Azure ainda depender desse diretório para a autenticação. Do mesmo modo, não pode eliminar um diretório se outro utilizador tiver associado uma subscrição ao mesmo.

## <a name="delete-the-directory"></a>Eliminar o diretório

1. Inicie sessão para o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja administrador Global para a sua organização.

2. Selecione **Azure Active Directory**.

3. Mude para o diretório que pretende eliminar.
  
   ![Confirmar a organização antes de eliminar](./media/directory-delete-howto/delete-directory-command.png)

4. Selecione **eliminar o diretório**.
  
   ![Selecione o comando para eliminar a organização](./media/directory-delete-howto/delete-directory-list.png)

5. Se o seu diretório não passar uma ou mais verificações, é apresentada uma ligação para obter mais informações sobre como passar. Depois de passar todas as verificações, selecione **eliminar** para concluir o processo.

## <a name="if-you-cant-delete-the-directory"></a>Se não é possível eliminar o diretório

Quando tiver configurado o diretório do Azure AD, pode também ativar as subscrições com base na licença para a sua organização, como o Azure AD Premium P2, Office 365 empresas – versão Premium, ou Enterprise Mobility + Security E5. Para evitar a perda acidental de dados, não é possível eliminar um diretório até que as subscrições são totalmente eliminadas. As subscrições têm de estar num **desaprovisionada** estado para permitir a eliminação de diretório. Uma **expirado** ou **cancelado** subscrição move para a **desativado** estado e a etapa final é o **desaprovisionada** estado.

Para o que esperar quando uma subscrição de avaliação do Office 365 expira (não incluindo paga parceiro/CSP, Enterprise Agreement ou licenciamento por Volume), consulte a tabela seguinte. Para obter mais informações sobre o Office 365 dados retenção e a subscrição do ciclo de vida, consulte [o que acontece aos meus dados e o acesso quando o meu do Office 365 para a subscrição de empresas termina?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Estado da subscrição | Dados | Acesso a dados
----- | ----- | -----
Active Directory (30 dias para avaliação) | Dados acessíveis a todos | Os utilizadores têm acesso normal a ficheiros do Office 365, ou aplicações<br>Os administradores têm acesso normal para o Centro de administração do Microsoft 365 e recursos 
Expirada (30 dias) | Dados acessíveis a todos| Os utilizadores têm acesso normal a ficheiros do Office 365, ou aplicações<br>Os administradores têm acesso normal para o Centro de administração do Microsoft 365 e recursos
Desativado (30 dias) | Dados acessíveis para apenas administradores | Os utilizadores não é possível aceder a aplicações ou ficheiros do Office 365<br>Os administradores podem aceder ao centro de administração do Microsoft 365, mas não é possível atribuir licenças para ou atualizar utilizadores
Desaprovisionada (30 dias após desativado) | Dados eliminados (eliminado automaticamente se não existem outros serviços estão em utilização) | Os utilizadores não é possível aceder a aplicações ou ficheiros do Office 365<br>Os administradores podem acessar o Centro de administração do Microsoft 365 para comprar e gerir outras subscrições

## <a name="delete-a-subscription"></a>Eliminar uma subscrição

Pode colocar uma subscrição para o estado de desaprovisionada a eliminar dentro de três dias, com o Centro de administração do Microsoft 365.

1. Inicie sessão para o [Centro de administração do Microsoft 365](https://admin.microsoft.com) com uma conta que seja um administrador global na sua organização. Se estiver a tentar eliminar o diretório "Contoso" que tem o contoso.onmicrosoft.com de domínio predefinido inicial, inicie sessão com um UPN como admin@contoso.onmicrosoft.com.

2. Selecione **faturação** e selecione **subscrições**, em seguida, escolha a subscrição que pretende cancelar. Depois de clicar em **Cancelar**, atualize a página.
  
   ![Eliminar a ligação para a eliminar subscrição](./media/directory-delete-howto/delete-command.png)
  
3. Selecione **eliminar** para eliminar a subscrição e aceite os termos e condições. Todos os dados é eliminado permanentemente no prazo de três dias. Pode reativar a subscrição durante o período de três dias, se mudar de ideias.
  
   ![Leia atentamente os termos e condições](./media/directory-delete-howto/delete-terms.png)

4. Agora, o estado da subscrição tiver sido alterado, a subscrição está marcada para eliminação. A subscrição introduz o **desaprovisionada** 72 horas depois de estado.

5. Depois de ter eliminado uma subscrição no seu diretório e decorridos 72 horas, pode iniciar sessão volta ao centro de administração do Azure AD novamente e lá deve ser nenhuma ação necessária e sem bloquear a sua eliminação do diretório de subscrições. Deverá conseguir eliminar o diretório do Azure AD com êxito.
  
   ![passaram a verificação de subscrição no ecrã de eliminação](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Tenho uma subscrição de avaliação que bloqueia a eliminação

Existem [produtos de inscrição Self-serviços](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) , como o Microsoft Power BI, Rights Management Services, Microsoft Power Apps ou do Dynamics 365, utilizadores individuais podem inscrever-se através do Office 365, o que também cria um utilizador convidado para a autenticação numa diretório do Azure AD. Esses produtos Self-serviços impedir eliminações de diretório, até que eles são totalmente eliminados do diretório, para evitar a perda de dados. Eles podem ser eliminados apenas pelo administrador do Azure AD Se o utilizador inscreveu individualmente ou foi atribuído o produto.

Existem dois tipos de produtos de inscrição Self-serviços na forma como são atribuídos: 

* Atribuição de ao nível da organização: Um administrador do Azure AD atribui o produto em toda a organização e um utilizador pode ser ativamente a utilizar o serviço com esta atribuição ao nível da organização, mesmo que não estão licenciados individualmente.
* Atribuição de nível de usuário: Um utilizador individual durante a inscrição Self-Service atribui, essencialmente, o produto para si próprios sem um administrador. Depois da organização torna-se geridos por um administrador (consulte [obtenção de controlo de administrador de um diretório não gerido](domains-admin-takeover.md), em seguida, o administrador pode atribuir diretamente o produto aos utilizadores sem a inscrição Self-Service.  

Quando iniciar a eliminação do produto inscrição self-service, a ação elimina os dados permanentemente e remove todos os acessos de usuário para o serviço. Qualquer utilizador que foi atribuída a oferta individualmente ou no nível da organização, em seguida, é impedido de iniciar sessão ou aceder a quaisquer dados existentes. Se quiser evitar a perda de dados com o produto de inscrição self-service, como [dashboards do Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) ou [configuração de política de Rights Management Services](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), certifique-se de que os dados de cópia de segurança e guardar noutro local.

Para obter mais informações sobre os serviços e atualmente disponíveis produtos de inscrição Self-serviços, consulte [programas de Self-serviços disponíveis](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Para o que esperar quando uma subscrição de avaliação do Office 365 expira (não incluindo paga parceiro/CSP, Enterprise Agreement ou licenciamento por Volume), consulte a tabela seguinte. Para obter mais informações sobre o Office 365 dados retenção e a subscrição do ciclo de vida, consulte [o que acontece aos meus dados e o acesso quando o meu do Office 365 para a subscrição de empresas termina?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Estado do produto | Dados | Acesso a dados
------------- | ---- | --------------
Active Directory (30 dias para avaliação) | Dados acessíveis a todos | Os utilizadores têm acesso normal para o produto de inscrição self-service, ficheiros ou aplicações<br>Os administradores têm acesso normal para o Centro de administração do Microsoft 365 e recursos
Eliminada | Dados eliminados | Os utilizadores não é possível aceder a aplicações, ficheiros ou produto de inscrição self-service<br>Os administradores podem acessar o Centro de administração do Microsoft 365 para comprar e gerir outras subscrições

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Como posso excluir um produto de inscrição self-service no portal do Azure?

Pode colocar um produto de inscrição self-service como o Microsoft Power BI ou do Azure Rights Management Services num **eliminar** Estado sejam eliminados imediatamente no portal do Azure AD.

1. Inicie sessão para o [Centro de administração do Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que seja um Administrador Global da organização. Se estiver a tentar eliminar o diretório "Contoso" que tem o contoso.onmicrosoft.com de domínio predefinido inicial, inicie sessão com um UPN como admin@contoso.onmicrosoft.com.

2. Selecione **licenças**e, em seguida, selecione **produtos de inscrição Self-serviços**. Pode ver todos os Self-serviços inscrição produtos em separado das subscrições com base em posto de trabalho. Escolha o produto que pretende eliminar permanentemente. Eis um exemplo no Microsoft Power BI:

    ![o nome de utilizador é escrito incorretamente ou não encontrado](./media/directory-delete-howto/licenses-page.png)

3. Selecione **elimine** para eliminar o produto e aceite os termos, os dados serem eliminados imediatamente e é permanente. Esta ação de eliminação irá remover todos os utilizadores e remover o acesso da organização para o produto. Clique em Sim para seguir em frente com a eliminação.  

    ![o nome de utilizador é escrito incorretamente ou não encontrado](./media/directory-delete-howto/delete-product.png)

4. Quando seleciona **Sim**, será iniciada a eliminação do produto self-service. Há uma notificação que irá informá-lo sobre a eliminação em curso.  

    ![o nome de utilizador é escrito incorretamente ou não encontrado](./media/directory-delete-howto/progress-message.png)

5. Agora, o estado de produto de inscrição self-service foi alterado para **Deleted**. Quando atualiza a página, o produto deve ser removido dos **produtos de inscrição Self-serviços** página.  

    ![o nome de utilizador é escrito incorretamente ou não encontrado](./media/directory-delete-howto/product-deleted.png)

6. Depois de ter eliminado todos os produtos, pode iniciar sessão novamente para o Centro de administração do Azure AD novamente e deve haver nenhuma ação necessária e sem bloquear a sua eliminação do diretório de produtos. Deverá conseguir eliminar o diretório do Azure AD com êxito.

    ![o nome de utilizador é escrito incorretamente ou não encontrado](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Passos Seguintes

[Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
