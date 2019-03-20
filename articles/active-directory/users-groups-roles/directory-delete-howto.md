---
title: Eliminar um diretório do inquilino - Azure Active Directory | Documentos da Microsoft
description: Explica como preparar um diretório de inquilino do Azure AD para eliminação
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 833c2e460ae306a7673e580aaa304be93c3cd044
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199737"
---
# <a name="delete-an-azure-active-directory-tenant"></a>Eliminar um inquilino do Azure Active Directory

Quando um inquilino é eliminado, também são eliminados todos os recursos que estão contidos no inquilino. Tem de preparar o inquilino ao minimizarem os seus recursos associados antes de eliminar. Apenas um administrador global do Azure Active Directory (Azure AD) pode eliminar um inquilino do Azure AD a partir do portal.

## <a name="prepare-the-tenant-for-deletion"></a>Preparar o inquilino para eliminação

Não é possível eliminar um inquilino no Azure AD, até que ele passa diversas verificações. Essas verificações de reduzem o risco de eliminar um inquilino negativamente a afeta o acesso de utilizador, como a capacidade de iniciar sessão no Office 365 ou acederem a recursos no Azure. Por exemplo, se o inquilino associado uma subscrição for eliminado involuntariamente, os utilizadores não é possível aceder os recursos do Azure para essa subscrição. A seguir explica as condições que são verificadas:

* Não pode haver nenhum utilizador no inquilino, exceto um administrador global que é eliminar o inquilino. Todos os utilizadores têm de ser eliminados antes de pode eliminar o inquilino. Se os utilizadores forem sincronizados no local, em seguida, sincronização deve estar desativada e os utilizadores têm de ser eliminados no inquilino de cloud com o portal do Azure ou cmdlets do PowerShell do Azure. 
* Não podem existir nenhuma aplicação no inquilino. Todas as aplicações têm de ser removidas antes de pode eliminar o inquilino.
* É possível não existem fornecedores de autenticação multifator ligados ao inquilino.
* É possível que não existem subscrições do Microsoft Online Services, como o Microsoft Azure, Office 365 ou Azure AD Premium, associadas ao inquilino. Por exemplo, se um inquilino predefinido foi criado para si no Azure, não é possível eliminar este inquilino se a sua subscrição do Azure ainda se baseia neste inquilino para autenticação. Da mesma forma, não é possível eliminar um inquilino se outro utilizador tiver associado uma subscrição com o mesmo. 

## <a name="delete-an-azure-ad-tenant"></a>Eliminar um inquilino do Azure AD

1. Inicie sessão para o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja administrador Global do inquilino.

2. Selecione **Azure Active Directory**.

3. Mude para a organização que pretende eliminar.
  
   ![Confirmar a organização antes de eliminar](./media/directory-delete-howto/delete-directory-command.png)

4. Selecione **eliminar o diretório**.
  
   ![Selecione o comando para eliminar a organização](./media/directory-delete-howto/delete-directory-list.png)

5. Se o seu inquilino não passar uma ou mais verificações, é apresentada uma ligação para obter mais informações sobre como passar. Depois de passar todas as verificações, selecione **eliminar** para concluir o processo.

## <a name="i-have-an-expired-subscription-but-i-cant-delete-the-tenant"></a>Tenho uma subscrição expirada, mas não consigo eliminar o inquilino

Quando configurou o seu inquilino do Azure AD, pode também ativar as subscrições com base na licença para a sua organização, como o Azure AD Premium P2, Office 365 empresas – versão Premium, ou Enterprise Mobility + Security E5. Estas subscrições bloqueiam a eliminação de diretório até são totalmente eliminadas, para evitar a perda acidental de dados. As subscrições têm de estar num **desaprovisionada** estado para permitir que a eliminação do inquilino. Uma **expirado** ou **cancelado** subscrição move para a **desativado** estado e a etapa final é o **Deprovisoned** estado. 

Para o que esperar quando uma subscrição de avaliação do Office 365 expira (não incluindo paga parceiro/CSP, Enterprise Agreement ou licenciamento por Volume), consulte a tabela seguinte. Para obter mais informações sobre o Office 365 dados retenção e a subscrição do ciclo de vida, consulte [o que acontece aos meus dados e o acesso quando o meu do Office 365 para a subscrição de empresas termina?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Estado da subscrição | Dados | Acesso a dados
----- | ----- | -----
Active Directory (30 dias para avaliação)  | Dados acessíveis a todos    | <li>Os utilizadores têm acesso normal a ficheiros do Office 365, ou aplicações<li>Os administradores têm acesso normal para o Centro de administração do Microsoft 365 e recursos 
Expirada (30 dias)   | Dados acessíveis a todos    | <li>Os utilizadores têm acesso normal a ficheiros do Office 365, ou aplicações<li>Os administradores têm acesso normal para o Centro de administração do Microsoft 365 e recursos
Desativado (30 dias) | Dados acessíveis para apenas administradores  | <li>Os utilizadores não é possível aceder a aplicações ou ficheiros do Office 365<li>Os administradores podem aceder ao centro de administração do Microsoft 365, mas não é possível atribuir licenças para ou atualizar utilizadores
Desaprovisionada (30 dias após desativado) | Dados eliminados (eliminado automaticamente se não existem outros serviços estão em utilização) | <li>Os utilizadores não é possível aceder a aplicações ou ficheiros do Office 365<li>Os administradores podem acessar o Centro de administração do Microsoft 365 para comprar e gerir outras subscrições 

## <a name="delete-a-subscription-in-the-microsoft-365-admin-center"></a>Eliminar uma subscrição no Centro de administração do Microsoft 365

Pode colocar uma subscrição para um **Deprovisoned** estado a eliminar dentro de 3 dias com o Centro de administração do Microsoft 365.

1. Inicie sessão para o [Centro de administração do Microsoft 365](https://admin.microsoft.com) com uma conta que seja um Administrador Global no inquilino. Se estiver a tentar eliminar o inquilino de "Contoso" que tem o contoso.onmicrosoft.com de domínio predefinido inicial, inicie sessão com um UPN como admin@contoso.onmicrosoft.com.

2. Vá para o **faturação** separador e selecione **produtos e serviços**, em seguida, escolha a subscrição que pretende cancelar. Depois de clicar em **Cancelar**, atualize a página.
  
   ![Eliminar a ligação para a eliminar subscrição](./media/directory-delete-howto/delete-command.png)
  
3. Selecione **eliminar** para eliminar a subscrição e aceite os termos e condições. Todos os dados é eliminado permanentemente no prazo de três dias. Pode reativar a subscrição durante o período de três dias, se mudar de ideias.
  
   ![Leia atentamente os termos e condições](./media/directory-delete-howto/delete-terms.png)

4. Agora, o estado da subscrição tiver sido alterado, a subscrição está marcada para eliminação. A subscrição introduz o **desaprovisionada** 72 horas depois de estado.

5. Depois de ter eliminado uma subscrição no seu inquilino e decorridos 72 horas, pode iniciar sessão volta ao centro de administração do Azure AD novamente e lá deve ser nenhuma ação necessária e não existem subscrições bloquear a sua eliminação do inquilino. Deve ser capaz de excluir com êxito o seu inquilino do Azure AD.
  
   ![passaram a verificação de subscrição no ecrã de eliminação](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="next-steps"></a>Passos Seguintes

[Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
