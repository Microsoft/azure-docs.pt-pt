---
title: Eliminar um diretório Azure AD - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Explica como preparar um diretório da AD Azure para a eliminação, incluindo diretórios de self-service
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961822"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Eliminar um diretório em Diretório Ativo Azure

Quando um diretório Azure AD é eliminado, todos os recursos contidos no diretório também são eliminados. Prepare a sua organização minimizando os seus recursos associados antes de eliminar. Apenas um administrador global do Azure Ative Directory (Azure AD) pode eliminar um diretório Azure AD do portal.

## <a name="prepare-the-directory"></a>Preparar o diretório

Não pode apagar um diretório em Azure AD até passar vários cheques. Estes controlos reduzem o risco de a apagar um diretório de Anúncios Azure ter um impacto negativo no acesso dos utilizadores, como a capacidade de iniciar sessão no Office 365 ou os recursos de acesso em Azure. Por exemplo, se o diretório associado a uma subscrição for eliminado involuntariamente, então os utilizadores não podem aceder aos recursos do Azure para essa subscrição. Verificam-se as seguintes condições:

* Não pode haver utilizadores no diretório exceto um administrador global que deve apagar o diretório. Deverão ser eliminados todos os outros utilizadores antes de eliminar o diretório. Se os utilizadores estiverem sincronizados a partir do local, então a sincronização deve primeiro ser desligada e os utilizadores devem ser eliminados no diretório da nuvem utilizando o portal Azure ou os cmdlets Azure PowerShell.
* Não podem existir aplicações no diretório. Quaisquer aplicações devem ser removidas antes de o diretório poder ser eliminado.
* Não pode haver fornecedores de autenticação multifactor ligados ao diretório.
* Não podem existir subscrições para quaisquer Serviços Online da Microsoft, como o Microsoft Azure, o Office 365 ou o Azure AD Premium, associadas ao diretório. Por exemplo, se tiver criado um diretório predefinido no Azure, não o poderá eliminar se a sua subscrição do Azure ainda depender desse diretório para a autenticação. Do mesmo modo, não pode eliminar um diretório se outro utilizador tiver associado uma subscrição ao mesmo.

## <a name="delete-the-directory"></a>Eliminar o diretório

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com) com uma conta que é o Administrador Global da sua organização.

2. Selecione **Azure Active Directory**.

3. Mude para o diretório que pretende eliminar.
  
   ![Confirmar organização antes de apagar](./media/directory-delete-howto/delete-directory-command.png)

4. **Selecione Excluir o diretório**.
  
   ![selecionar o comando para apagar a organização](./media/directory-delete-howto/delete-directory-list.png)

5. Se o seu diretório não passar um ou mais cheques, é-lhe fornecido um link para mais informações sobre como passar. Depois de passar todas as verificações, selecione **Eliminar** para completar o processo.

## <a name="if-you-cant-delete-the-directory"></a>Se não puder apagar o diretório

Quando configurao o seu diretório Azure AD, pode também ter ativado subscrições baseadas em licença para a sua organização, como o Azure AD Premium P2, office 365 Business Premium, ou Enterprise Mobility + Security E5. Para evitar perda acidental de dados, não pode excluir um diretório até que as subscrições sejam totalmente eliminadas. As assinaturas devem estar num estado **dedprovisionamento** para permitir a supressão do diretório. Uma subscrição **expirada** ou **cancelada** move-se para o estado **de deficientes,** e a fase final é o estado **dedprovisionamento.**

Para o que esperar quando expirar uma assinatura do Gabinete de Ensaio 365 (sem incluir o Parceiro/CSP pago, o Acordo de Empresa ou o Licenciamento de Volume), consulte a tabela seguinte. Para obter mais informações sobre o Office 365 data retenção de dados e o ciclo de vida por subscrição, veja o que acontece aos meus dados e acesso quando o [meu Office 365 para subscrição de negócios terminar?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3) 

Estado de subscrição | Dados | Acesso a dados
----- | ----- | -----
Ativo (30 dias para julgamento) | Dados acessíveis a todos | Os utilizadores têm acesso normal a ficheiros do Office 365 ou apps<br>Administradores têm acesso normal ao centro de administração e recursos da Microsoft 365 
Expirado (30 dias) | Dados acessíveis a todos| Os utilizadores têm acesso normal a ficheiros do Office 365 ou apps<br>Administradores têm acesso normal ao centro de administração e recursos da Microsoft 365
Deficientes (30 dias) | Dados acessíveis apenas à administração | Os utilizadores não podem aceder ao Office 365 ficheiros ou aplicações<br>Os administradores podem aceder ao centro de administração da Microsoft 365, mas não podem atribuir licenças ou atualizar utilizadores
Desprovisionado (30 dias após deficiente) | Dados eliminados (eliminados automaticamente se não forem utilizados outros serviços) | Os utilizadores não podem aceder ao Office 365 ficheiros ou aplicações<br>Os administradores podem aceder ao centro de administração da Microsoft 365 para comprar e gerir outras subscrições

## <a name="delete-a-subscription"></a>Eliminar uma subscrição

Pode colocar uma subscrição no estado **dedprovisionamento** a ser eliminada em três dias utilizando o centro de administração da Microsoft 365.

1. Inscreva-se no centro de administração da [Microsoft 365](https://admin.microsoft.com) com uma conta que é um administrador global na sua organização. Se estiver a tentar eliminar o diretório "Contoso" que tem o domínio padrão admin@contoso.onmicrosoft.cominicial contoso.onmicrosoft.com, inscreva-se numa UPN como .

2. Pré-visualizar o novo centro de administração do Microsoft 365, certificando-se de que o novo toggle do **centro de administração** está ativado.

   ![Pré-visualizar a nova experiência do centro de administração M365](./media/directory-delete-howto/preview-toggle.png)

3. Uma vez ativado o novo centro de administração, tem de cancelar uma subscrição antes de a eliminar. Selecione Serviços de **Faturação** e selecione **Produtos &** e, em seguida, selecione cancelar **subscrição** para a subscrição que pretende cancelar. Será levado para uma página de feedback.

   ![Escolha uma subscrição para cancelar](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Complete o formulário de feedback e selecione **Cancelar a subscrição** para cancelar a subscrição.

   ![Cancelar o comando na pré-visualização da subscrição](./media/directory-delete-howto/cancel-command.png)

5. Agora pode apagar a subscrição. Selecione **Excluir** para a subscrição que pretende eliminar. Se não encontrar a subscrição na página de **serviços & Produtos,** certifique-se de que tem o estado de **subscrição** definido para **Todos**.

   ![Eliminar link para excluir subscrição](./media/directory-delete-howto/delete-command.png)

6. **Selecione Excluir subscrição** para eliminar a subscrição e aceitar os termos e condições. Todos os dados são permanentemente eliminados no prazo de três dias. Pode [reativar a subscrição](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) durante o período de três dias se mudar de ideias.
  
   ![leia atentamente termos e condições](./media/directory-delete-howto/delete-terms.png)

7. Agora o estado de subscrição mudou, e a subscrição está marcada para eliminação. A subscrição entra no estado **dedprovisionamento** 72 horas depois.

8. Uma vez eliminada uma subscrição no seu diretório e decorridos 72 horas, pode voltar a entrar no centro de administração da AD Azure e não deve haver nenhuma ação necessária e nenhuma subscrição que bloqueie a sua exclusão do diretório. Deverá ser capaz de eliminar com sucesso o seu diretório Azure AD.
  
   ![passar verificação de subscrição no ecrã de eliminação](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Tenho uma assinatura de julgamento que bloqueia a eliminação.

Existem produtos de inscrição de self-service como Microsoft Power BI, Rights Management Services, Microsoft Power Apps ou Dynamics 365, os utilizadores individuais podem [inscrever-se](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) através do Office 365, que também cria um utilizador convidado para autenticação no seu diretório Azure AD. Estes produtos self-service bloqueiam a eliminação do diretório até serem totalmente eliminados do diretório, para evitar a perda de dados. Só podem ser eliminados pelo administrador da AD Azure se o utilizador se inscreveu individualmente ou foi atribuído o produto.

Existem dois tipos de produtos de inscrição de autosserviço na forma como são atribuídos: 

* Atribuição ao nível org: Um administrador da AD Azure atribui o produto a toda a organização e um utilizador pode estar a utilizar ativamente o serviço com esta atribuição de nível org, mesmo que não sejam licenciados individualmente.
* Atribuição ao nível do utilizador: Um utilizador individual durante o autosserviço atribui essencialmente o produto a si próprio sem um administrador. Assim que a organização for gerida por um administrador (ver [Administrador aquisição de um diretório não gerido](domains-admin-takeover.md), então o administrador pode atribuir diretamente o produto aos utilizadores sem inscrição de autosserviço.  

Quando inicia a eliminação do produto de inscrição de autosserviço, a ação elimina permanentemente os dados e remove todo o acesso do utilizador ao serviço. Qualquer utilizador que tenha sido atribuído à oferta individualmente ou a nível da organização está então impedido de iniciar sessão ou aceder a quaisquer dados existentes. Se pretender evitar a perda de dados com o produto de inscrição de self-service, como [os dashboards Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) ou a configuração da política de Serviços de Gestão de [Direitos,](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)certifique-se de que os dados são apoiados e guardados noutros locais.

Para obter mais informações sobre os produtos e serviços de auto-atendimento atualmente disponíveis, consulte [os programas de self-service disponíveis.](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)

Para o que esperar quando expirar uma assinatura do Gabinete de Ensaio 365 (sem incluir o Parceiro/CSP pago, o Acordo de Empresa ou o Licenciamento de Volume), consulte a tabela seguinte. Para obter mais informações sobre o Office 365 data retenção de dados e o ciclo de vida por subscrição, veja o que acontece aos meus dados e acesso quando o [meu Office 365 para subscrição de negócios terminar?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)

Estado do produto | Dados | Acesso a dados
------------- | ---- | --------------
Ativo (30 dias para julgamento) | Dados acessíveis a todos | Os utilizadores têm acesso normal a produtos de inscrição de self-service, ficheiros ou aplicações<br>Administradores têm acesso normal ao centro de administração e recursos da Microsoft 365
Eliminado | Dados eliminados | Os utilizadores não podem aceder a produtos, ficheiros ou aplicações de auto-atendimento<br>Os administradores podem aceder ao centro de administração da Microsoft 365 para comprar e gerir outras subscrições

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Como posso apagar um produto de inscrição de autosserviço no portal Azure?

Pode colocar um produto de inscrição de autosserviço como o Microsoft Power BI ou o Azure Rights Management Services num estado **De eliminar** imediatamente no portal Azure AD.

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que é administradora global na organização. Se estiver a tentar eliminar o diretório "Contoso" que tem o domínio padrão admin@contoso.onmicrosoft.cominicial contoso.onmicrosoft.com, inscreva-se com uma UPN como .

2. Selecione **Licenças**e, em seguida, selecione **produtos de inscrição self-service**. Pode ver todos os produtos de inscrição de autosserviço separadamente das subscrições baseadas em assentos. Escolha o produto que pretende eliminar permanentemente. Aqui está um exemplo no Microsoft Power BI:

    ![o nome de utilizador é mistyed ou não encontrado](./media/directory-delete-howto/licenses-page.png)

3. **Selecione Excluir** para eliminar o produto e aceitar os termos de que os dados são eliminados imediatamente e irrevogavelmente. Esta ação de eliminação removerá todos os utilizadores e removerá o acesso da organização ao produto. Clique em Sim para avançar com a eliminação.  

    ![o nome de utilizador é mistyed ou não encontrado](./media/directory-delete-howto/delete-product.png)

4. Quando selecionar **Sim,** a eliminação do produto self-service será iniciada. Há uma notificação que lhe dirá a eliminação em curso.  

    ![o nome de utilizador é mistyed ou não encontrado](./media/directory-delete-howto/progress-message.png)

5. Agora o estado do produto de inscrição de self-service mudou para **Apagado**. Quando atualizar a página, o produto deve ser removido da página de produtos de **inscrição self-service.**  

    ![o nome de utilizador é mistyed ou não encontrado](./media/directory-delete-howto/product-deleted.png)

6. Depois de ter eliminado todos os produtos, pode voltar a entrar no centro de administração da Azure AD e não deve haver ação necessária e nenhum produto que bloqueie a sua exclusão do diretório. Deverá ser capaz de eliminar com sucesso o seu diretório Azure AD.

    ![o nome de utilizador é mistyed ou não encontrado](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Passos seguintes

[Documentação do Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/)
