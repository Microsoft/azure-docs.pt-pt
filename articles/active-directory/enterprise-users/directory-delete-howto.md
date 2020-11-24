---
title: Excluir uma organização AZure AD (inquilino) - Azure Ative Directory / Microsoft Docs
description: Explica como preparar uma organização AZure AD (inquilino) para a eliminação, incluindo organizações de self-service
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: be40a3cea5aa7abfa257c4e5a2db61c514892dd5
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503637"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>Eliminar um inquilino no Azure Ative Directory

Quando uma organização AZure AD (inquilino) é eliminada, todos os recursos que estão contidos na organização também são eliminados. Prepare a sua organização minimizando os seus recursos associados antes de apagar. Apenas um administrador global do Azure Ative Directory (Azure AD) pode eliminar uma organização AD Azure do portal.

## <a name="prepare-the-organization"></a>Preparar a organização

Não pode excluir uma organização em Azure AD até passar vários cheques. Estes controlos reduzem o risco de que a eliminação de uma organização Azure AD tenha um impacto negativo no acesso dos utilizadores, como a capacidade de iniciar sing no Microsoft 365 ou aceder a recursos em Azure. Por exemplo, se a organização associada a uma subscrição for eliminada involuntariamente, então os utilizadores não podem aceder aos recursos Azure para essa subscrição. Verificam-se as seguintes condições:

* Não pode haver utilizadores na organização Azure AD (inquilino) exceto um administrador global que deve apagar a organização. Qualquer outro utilizadores deve ser eliminado antes de a organização poder ser eliminada. Se os utilizadores estiverem sincronizados a partir das instalações, a sincronização deve primeiro ser desligada e os utilizadores devem ser eliminados na organização da nuvem utilizando o portal Azure ou os cmdlets Azure PowerShell.
* Não pode haver aplicações na organização. Quaisquer aplicações devem ser removidas antes de a organização poder ser eliminada.
* Não pode haver fornecedores de autenticação multi-factor ligados à organização.
* Não pode haver subscrições para quaisquer Serviços Microsoft Online como Microsoft Azure, Microsoft 365 ou Azure AD Premium associados à organização. Por exemplo, se uma organização AZure AD padrão foi criada para si em Azure, não poderá eliminar esta organização se a sua subscrição Azure ainda depender desta organização para autenticação. Da mesma forma, não é possível excluir uma organização se outro utilizador tiver associado uma subscrição com ela.

## <a name="delete-the-organization"></a>Apagar a organização

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com) com uma conta que é o Administrador Global da sua organização.

2. Selecione **Azure Active Directory**.

3. Mude para a organização que pretende eliminar.
  
   ![Confirme a organização antes de apagar](./media/directory-delete-howto/delete-directory-command.png)

4. **Selecione Excluir inquilino**.
  
   ![selecionar o comando para eliminar a organização](./media/directory-delete-howto/delete-directory-list.png)

5. Se a sua organização não passar uma ou mais verificações, é-lhe fornecido um link para mais informações sobre como passar. Depois de passar todas as verificações, **selecione Eliminar** para concluir o processo.

## <a name="if-you-cant-delete-the-organization"></a>Se não puder apagar a organização

Quando configurar a sua organização Azure AD, poderá também ter ativado subscrições baseadas em licenças para a sua organização como Azure AD Premium P2, Microsoft 365 Business Standard ou Enterprise Mobility + Security E5. Para evitar perda acidental de dados, não é possível eliminar uma organização até que as subscrições sejam totalmente eliminadas. As subscrições devem estar num estado **desprovisionado** para permitir a eliminação da organização. Uma subscrição **expirada** ou **cancelada** move-se para o estado **desativado,** e a fase final é o estado **desprovisionado.**

Para o que esperar quando um teste a subscrição da Microsoft 365 expirar (sem incluir o Parceiro/CSP pago, o Enterprise Agreement ou o Volume De licenciamento), consulte a tabela seguinte. Para obter mais informações sobre a retenção de dados do Microsoft 365 e o ciclo de vida da subscrição, consulte [o que acontece aos meus dados e acesso quando o meu Microsoft 365 para subscrição de negócios termina?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3) 

Estado de subscrição | Dados | Acesso a dados
----- | ----- | -----
Ativo (30 dias para julgamento) | Dados acessíveis a todos | Os utilizadores têm acesso normal aos ficheiros microsoft 365 ou apps<br>Os administradores têm acesso normal ao Centro de Administração microsoft 365 e recursos 
Expirado (30 dias) | Dados acessíveis a todos| Os utilizadores têm acesso normal aos ficheiros microsoft 365 ou apps<br>Os administradores têm acesso normal ao Centro de Administração microsoft 365 e recursos
Deficiente (30 dias) | Dados acessíveis apenas a administradores | Os utilizadores não podem aceder a ficheiros ou apps do Microsoft 365<br>Os administradores podem aceder ao centro de administração microsoft 365, mas não podem atribuir licenças ou atualizar utilizadores
Desprovisionado (30 dias após desativação) | Dados eliminados (eliminados automaticamente se não houver outros serviços em uso) | Os utilizadores não podem aceder a ficheiros ou apps do Microsoft 365<br>Os administradores podem aceder ao centro de administração microsoft 365 para comprar e gerir outras subscrições

## <a name="delete-a-subscription"></a>Excluir uma subscrição

Pode colocar uma subscrição no estado **Deprovisionado** a ser eliminada em três dias utilizando o centro de administração microsoft 365.

1. Inscreva-se no [centro de administração microsoft 365](https://admin.microsoft.com) com uma conta que é um administrador global na sua organização. Se estiver a tentar eliminar a organização "Contoso" que tem o domínio padrão inicial contoso.onmicrosoft.com, inscreva-se com uma UPN como admin@contoso.onmicrosoft.com .

2. Pré-visualizar o novo centro de administração Microsoft 365 certificando-se de que **o novo sinal central** de administração está ativado.

   ![Pré-visualizar a nova experiência do centro de administração M365](./media/directory-delete-howto/preview-toggle.png)

3. Uma vez ativado o novo centro de administração, tem de cancelar uma subscrição antes de a poder eliminar. Selecione **Billing** e selecione **Produtos & serviços,** em seguida, selecione Cancelar a **subscrição** para a subscrição que pretende cancelar. Será levado para uma página de feedback.

   ![Escolha uma subscrição para cancelar](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Preencha o formulário de feedback e selecione cancelar a **subscrição** para cancelar a subscrição.

   ![Cancelar comando na pré-visualização da subscrição](./media/directory-delete-howto/cancel-command.png)

5. Pode agora eliminar a subscrição. **Selecione Excluir** para a subscrição que pretende eliminar. Se não conseguir encontrar a subscrição na página **de serviços & Produtos,** certifique-se de que tem o **estado de Subscrição** definido para **Todos**.

   ![Eliminar link para eliminar subscrição](./media/directory-delete-howto/delete-command.png)

6. Selecione Eliminar a **subscrição** para eliminar a subscrição e aceitar os termos e condições. Todos os dados são permanentemente eliminados dentro de três dias. Pode [reativar a subscrição](/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) durante o período de três dias se mudar de ideias.
  
   ![ler cuidadosamente termos e condições](./media/directory-delete-howto/delete-terms.png)

7. Agora o estado de subscrição mudou, e a subscrição está marcada para eliminação. A subscrição entra no estado **desprovisionado** 72 horas depois.

8. Uma vez que tenha apagado uma subscrição na sua organização e 72 horas decorridos, pode voltar a entrar no centro de administração Azure AD e não deverá haver nenhuma ação necessária e nenhuma subscrição que bloqueie a eliminação da sua organização. Deverá ser capaz de eliminar com sucesso a sua organização Azure AD.
  
   ![verificação de subscrição de passe no ecrã de eliminação](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Tenho uma assinatura de teste que bloqueia a eliminação.

Existem [produtos de inscrição de self-service](/office365/admin/misc/self-service-sign-up?view=o365-worldwide) como o Microsoft Power BI, Rights Management Services, Microsoft Power Apps ou Dynamics 365, cada um dos utilizadores pode inscrever-se através do Microsoft 365, o que também cria um utilizador convidado para autenticação na sua organização Azure AD. Estes produtos de self-service bloqueiam as eliminações do diretório até que os produtos sejam totalmente eliminados da organização, para evitar a perda de dados. Só podem ser eliminados pelo administrador Azure AD se o utilizador se inscreveu individualmente ou foi designado o produto.

Existem dois tipos de produtos de autosserviço de inscrição na forma como são atribuídos: 

* Atribuição ao nível org: Um administrador AD Azure atribui o produto a toda a organização e um utilizador pode estar ativamente a utilizar o serviço com esta atribuição ao nível org mesmo que não sejam licenciados individualmente.
* Atribuição ao nível do utilizador: Um utilizador individual durante a inscrição de autosserviço atribui essencialmente o produto a si próprio sem administração. Uma vez que a organização é gerida por um administrador (ver [Administrador assumir uma organização não gerida](domains-admin-takeover.md), então o administrador pode atribuir diretamente o produto aos utilizadores sem inscrição de autosserviço.  

Quando inicia a eliminação do produto de inscrição de autosserviço, a ação elimina permanentemente os dados e remove todo o acesso do utilizador ao serviço. Qualquer utilizador que tenha sido atribuído a oferta individualmente ou a nível da organização fica então impedido de iniciar sessão ou aceder a quaisquer dados existentes. Se pretender evitar a perda de dados com o produto de inscrição de autosserviço, como os dashboards Do Microsoft Power BI ou [a configuração da política dos Serviços de Gestão de](/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy) [Direitos,](/power-bi/service-export-to-pbix) certifique-se de que os dados são apoiados e guardados noutro local.

Para obter mais informações sobre produtos e serviços de autosserviço disponíveis, consulte os [programas de self-service disponíveis.](/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)

Para o que esperar quando um teste a subscrição da Microsoft 365 expirar (sem incluir o Parceiro/CSP pago, o Enterprise Agreement ou o Volume De licenciamento), consulte a tabela seguinte. Para obter mais informações sobre a retenção de dados do Microsoft 365 e o ciclo de vida da subscrição, consulte [o que acontece aos meus dados e acesso quando o meu Microsoft 365 para subscrição de negócios termina?](/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)

Estado do produto | Dados | Acesso a dados
------------- | ---- | --------------
Ativo (30 dias para julgamento) | Dados acessíveis a todos | Os utilizadores têm acesso normal a produtos, ficheiros ou apps de inscrição de autosserviço<br>Os administradores têm acesso normal ao Centro de Administração microsoft 365 e recursos
Eliminado | Dados eliminados | Os utilizadores não podem aceder a produtos, ficheiros ou apps de inscrição autosserviço<br>Os administradores podem aceder ao centro de administração microsoft 365 para comprar e gerir outras subscrições

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Como posso eliminar um produto de inscrição de autosserviço no portal Azure?

Pode colocar um produto de inscrição de autosserviço como o Microsoft Power BI ou o Azure Rights Management Services num estado **de Exclusão** para ser imediatamente eliminado no portal AD Azure.

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que é um administrador global na organização. Se estiver a tentar eliminar a organização "Contoso" que tem o domínio padrão inicial contoso.onmicrosoft.com, inscreva-se com uma UPN como admin@contoso.onmicrosoft.com .

2. Selecione **Licenças** e, em seguida, selecione **produtos de inscrição de self-service**. Pode ver todos os produtos de inscrição de self-service separadamente das subscrições baseadas em assentos. Escolha o produto que pretende eliminar permanentemente. Aqui está um exemplo no Microsoft Power BI:

    ![Screenshot que mostra a página "Licenças - Produtos de inscrição de autosserviço".](./media/directory-delete-howto/licenses-page.png)

3. **Selecione Eliminar** para eliminar o produto e aceitar os termos que os dados são eliminados imediatamente e irrevogavelmente. Esta ação de eliminação removerá todos os utilizadores e removerá o acesso da organização ao produto. Clique em Sim para avançar com a eliminação.  

    ![Screenshot que mostra a página "Licenças - Produtos de autosserviço de inscrição" com a janela "Eliminar o produto de inscrição de autosserviço" aberta.](./media/directory-delete-howto/delete-product.png)

4. Quando selecionar **Sim,** iniciar-se-á a supressão do produto self-service. Há uma notificação que lhe dirá a supressão em curso.  

    ![Screenshot que mostra a página "Licenças - Produtos de autosserviço de inscrição" com a notificação de "eliminação em curso" exibida.](./media/directory-delete-howto/progress-message.png)

5. Agora, o estado do produto de autosserviço mudou para **Deleted**. Quando refrescar a página, o produto deve ser removido da página de **produtos de inscrição self-service.**  

    ![Screenshot que mostra a página "Licenças - Produtos de autosserviço de inscrição" com o painel "Produto de inscrição de autosserviço eliminado" no lado direito.](./media/directory-delete-howto/product-deleted.png)

6. Uma vez eliminado todos os produtos, pode voltar a entrar no centro de administração Azure AD e não deverá haver nenhuma ação necessária e nenhum produto que bloqueie a sua eliminação da organização. Deverá ser capaz de eliminar com sucesso a sua organização Azure AD.

    ![o nome de utilizador é mal assinalado ou não encontrado](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Passos seguintes

[Documentação do Azure Active Directory](../index.yml)