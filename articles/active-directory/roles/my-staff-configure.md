---
title: Use o Meu Pessoal para delegar a gestão de utilizadores - Azure AD | Microsoft Docs
description: Delegar gestão de utilizadores usando o Meu Pessoal e unidades administrativas
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 1a380c8a3d766c3c11d8cba1148383d924f65a1b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225001"
---
# <a name="manage-your-users-with-my-staff"></a>Gerencie os seus utilizadores com a Minha Equipa

O Meu Pessoal permite-lhe delegar permissões a uma figura de autoridade, como um gerente de loja ou um líder de equipa, para garantir que os seus funcionários possam aceder às suas contas AD Azure. Em vez de confiar em um helpdesk central, as organizações podem delegar tarefas comuns, como repor palavras-passe ou alterar números de telefone para um gestor de equipa local. Com o My Staff, um utilizador que não consiga aceder à sua conta pode recuperar o acesso em apenas alguns cliques, sem necessidade de ajuda ou pessoal de TI.

Antes de configurar o My Staff para a sua organização, recomendamos que reveja esta documentação, bem como a [documentação](../user-help/my-staff-team-manager.md) do utilizador para garantir que compreende como funciona e como impacta os seus utilizadores. Pode aproveitar a documentação do utilizador para treinar e preparar os seus utilizadores para a nova experiência e ajudar a garantir um lançamento bem-sucedido.

## <a name="how-my-staff-works"></a>Como funciona o meu pessoal

O meu pessoal baseia-se em unidades administrativas, que constituem um contentor de recursos que pode ser utilizado para restringir o âmbito de controlo administrativo de uma missão. Para mais informações, consulte [a gestão de unidades administrativas no Diretório Ativo Azure.](administrative-units.md) No My Staff, as unidades administrativas podem conter um grupo de utilizadores numa loja ou departamento. Um gestor de equipa pode então ser designado para um cargo administrativo num âmbito de uma ou mais unidades.

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.

  * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição.

  * Se necessário, [crie um inquilino do Azure Ative Directory](../fundamentals/sign-up-organization.md) ou [associe uma assinatura Azure à sua conta.](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
* Precisa de privilégios *de administrador global* no seu inquilino AZure AD para ativar a autenticação baseada em SMS.
* Cada utilizador que esteja ativado na política do método de autenticação de mensagens de texto deve ser licenciado, mesmo que não o utilize. Cada utilizador ativado deve ter uma das seguintes licenças AZure AD ou Microsoft 365:

  * [Azure AD Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 ou F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Mobilidade Empresarial + Segurança (EMS) E3 ou E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) ou [Microsoft 365 (M365) E3 ou E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Como ativar o meu pessoal

Uma vez configuradas unidades administrativas, pode aplicar este âmbito aos seus utilizadores que acedam ao Meu Pessoal. Apenas os utilizadores a quem for atribuída uma função administrativa podem aceder ao Meu Pessoal. Para ativar o Meu Pessoal, complete os seguintes passos:

1. Inscreva-se no portal Azure como administrador do Utilizador.
2. Navegue para as definições do utilizador **do Azure Ative**  >  **Directory**  >    >  User
3. Em **Administradores pode aceder ao My Staff,** pode optar por ativar para todos os utilizadores, utilizadores selecionados ou sem acesso ao utilizador.

> [!Note]
> Apenas os utilizadores a quem foi atribuído um papel de administrador podem aceder ao My Staff. Se ativar o My Staff para um utilizador que não tenha uma função de administração, não poderá aceder ao My Staff.

## <a name="conditional-access"></a>Acesso condicional

Pode proteger o portal My Staff utilizando a política de acesso condicional Azure AD. Use-o para tarefas como exigir a autenticação de vários fatores antes de aceder ao My Staff.

Recomendamos vivamente que proteja o Meu Pessoal utilizando [políticas de acesso condicional Azure AD.](../conditional-access/index.yml) Para aplicar uma política de acesso condicional ao Meu Pessoal, você deve primeiro visitar o site Do Meu Pessoal uma vez por alguns minutos para providenciar automaticamente o diretor de serviço no seu inquilino para uso por Acesso Condicional.

Você verá o principal do serviço quando criar uma política de acesso condicional que se aplica à aplicação my staff cloud.

![Criar uma política de acesso condicional para a aplicação My Staff](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Usando o meu pessoal

Quando um utilizador vai ao My Staff, é-lhes mostrado o nome das [unidades administrativas](administrative-units.md) sobre as quais têm permissões administrativas. Na [documentação](../user-help/my-staff-team-manager.md)do utilizador My Staff, utilizamos o termo "localização" para se referir às unidades administrativas. Se as permissões de um administrador não tiverem um âmbito de unidade administrativa, as permissões aplicam-se em toda a organização. Após a ativação do My Staff, os utilizadores que estão habilitados e lhes foi atribuído uma função administrativa podem aceder-lhe através [https://mystaff.microsoft.com](https://mystaff.microsoft.com) de . Podem selecionar uma unidade administrativa para visualizar os utilizadores nessa unidade e selecionar um utilizador para abrir o seu perfil.

## <a name="reset-a-users-password"></a>Repor a palavra-passe de um utilizador

Antes de poder descansar palavras-passe para utilizadores no local, deve preencher as seguintes condições pré-requisitos. Para obter instruções detalhadas, consulte Ativar o tutorial [de redefinição da palavra-passe de autosserviço.](../authentication/tutorial-enable-sspr-writeback.md)

* Configure permissões para writeback de palavra-passe
* Ativar a gravação de palavra-passe no Azure AD Connect
* Ativar a gravação de palavra-passe no reset da palavra-passe de autosserviço AZure AD (SSPR)

As seguintes funções têm permissão para redefinir a palavra-passe de um utilizador:

* [Administrador de autenticação](permissions-reference.md#authentication-administrator)
* [Administrador de autenticação privilegiada](permissions-reference.md#privileged-authentication-administrator)
* [Administrador global](permissions-reference.md#global-administrator)
* [Administrador da Helpdesk](permissions-reference.md#helpdesk-administrator)
* [Administrador de utilizadores](permissions-reference.md#user-administrator)
* [Administrador de palavras-passe](permissions-reference.md#password-administrator)

Da **My Staff,** abra o perfil de um utilizador. Selecione **Redefinir a palavra-passe**.

* Se o utilizador estiver apenas na nuvem, pode ver uma senha temporária que pode dar ao utilizador.
* Se o utilizador estiver sincronizado a partir do Ative Directory no local, pode introduzir uma palavra-passe que satisfaça as suas políticas de AD no local. Em seguida, pode dar essa palavra-passe ao utilizador.

    ![Indicador de progresso de redefinição de palavra-passe e notificação de sucesso](./media/my-staff-configure/reset-password.png)

O utilizador é obrigado a alterar a sua palavra-passe da próxima vez que iniciar sedu.

## <a name="manage-a-phone-number"></a>Gerir um número de telefone

Da **My Staff,** abra o perfil de um utilizador.

* Selecione Adicionar a secção **de números de telefone** para adicionar um número de telefone para o utilizador
* **Selecione Editar número de telefone** para alterar o número de telefone
* **Selecione Remover O número de telefone** para remover o número de telefone do utilizador

Dependendo das definições, o utilizador pode então utilizar o número de telefone configurado para iniciar sposição com SMS, realizar a autenticação de vários fatores e fazer reiniciar a palavra-passe de autosserviço.

Para gerir o número de telefone de um utilizador, deve ser-lhe atribuída uma das seguintes funções:

* [Administrador de autenticação](permissions-reference.md#authentication-administrator)
* [Administrador de autenticação privilegiada](permissions-reference.md#privileged-authentication-administrator)
* [Administrador global](permissions-reference.md#global-administrator)

## <a name="search"></a>Pesquisar

Pode pesquisar por unidades administrativas e utilizadores da sua organização utilizando a barra de pesquisa em My Staff. Pode pesquisar em todas as unidades administrativas e utilizadores da sua organização, mas só pode escrulá-lo com utilizadores que se encontrem numa unidade administrativa sobre a qual lhe foram dadas permissões administrativas.

Também pode procurar um utilizador dentro de uma unidade administrativa. Para isso, utilize a barra de pesquisa no topo da lista de utilizadores.

## <a name="audit-logs"></a>Registos de auditoria

Pode ver os registos de auditoria das ações tomadas no Meu Pessoal no portal Azure Ative Directory. Se um registo de auditoria foi gerado por uma ação tomada no My Staff, verá isso indicado em DETALHES ADICIONAIS no evento de auditoria.

## <a name="next-steps"></a>Passos seguintes

Documentação do [meu pessoal](../user-help/my-staff-team-manager.md) 
 [Documentação das unidades administrativas](administrative-units.md)
