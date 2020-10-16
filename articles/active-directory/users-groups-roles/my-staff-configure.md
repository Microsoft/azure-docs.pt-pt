---
title: Use o Meu Staff para delegar a gestão do utilizador (pré-visualização) - Azure AD / Microsoft Docs
description: Delegar gestão de utilizadores usando o Meu Pessoal e unidades administrativas
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 05/08/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: da358ecb126dda21d18120bbe698fec234c85e4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798963"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Gerir os seus utilizadores com o Meu Pessoal (pré-visualização)

O Meu Pessoal permite-lhe delegar uma figura de autoridade, como um gerente de loja ou um chefe de equipa, as permissões para garantir que os seus funcionários tenham acesso às suas contas AD Azure. Em vez de confiar em um helpdesk central, as organizações podem delegar tarefas comuns, como repor palavras-passe ou alterar números de telefone para um gestor de equipa. Com o My Staff, um utilizador que não consiga aceder à sua conta pode recuperar o acesso em apenas alguns cliques, sem necessidade de ajuda ou pessoal de TI.

Antes de configurar o My Staff para a sua organização, recomendamos que reveja esta documentação, bem como a [documentação](../user-help/my-staff-team-manager.md) do utilizador para garantir que compreende a funcionalidade e o impacto desta funcionalidade nos seus utilizadores. Pode aproveitar a documentação do utilizador para treinar e preparar os seus utilizadores para a nova experiência e ajudar a garantir um lançamento bem-sucedido.

A autenticação por SMS para os utilizadores é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, consulte [Termos Complementares de Utilização para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="how-my-staff-works"></a>Como funciona o meu pessoal

O meu pessoal baseia-se em unidades administrativas (UA), que são um contentor de recursos que podem ser utilizados para restringir o âmbito de controlo administrativo de uma missão. No My Staff, as UA são usadas para definir um subconjunto de utilizadores de uma organização, como uma loja ou departamento. Então, por exemplo, um gestor de equipa poderia ser atribuído a um papel cujo âmbito é uma ou mais AUs. No exemplo abaixo, foi-lhe atribuída a função de Administração de Autenticação, e as três AUs são o âmbito do papel. Para obter mais informações sobre unidades administrativas, consulte [a gestão de unidades administrativas no Diretório Ativo Azure.](directory-administrative-units.md)

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

Uma vez configuradas UAs, pode aplicar este âmbito aos seus utilizadores que acedam ao Meu Pessoal. Apenas os utilizadores a quem for atribuída uma função administrativa podem aceder ao Meu Pessoal. Para ativar o Meu Pessoal, complete os seguintes passos:

1. Inscreva-se no portal Azure como administrador do Utilizador.
2. Navegue para as definições do utilizador **do Azure Ative**  >  **Directory**  >  **User feature previews**  >  **Manage user feature preview settings**User
3. Em **Administradores pode aceder ao My Staff,** pode optar por ativar para todos os utilizadores, utilizadores selecionados ou sem acesso ao utilizador.

> [!Note]
> Apenas os utilizadores a quem foi atribuído um papel de administrador podem aceder ao My Staff. Se ativar o My Staff para um utilizador que não tenha uma função de administração, não poderá aceder ao My Staff.

## <a name="conditional-access"></a>Acesso condicional

Pode proteger o portal My Staff utilizando a política de acesso condicional Azure AD. Use-o para tarefas como exigir a autenticação de vários fatores antes de aceder ao My Staff.

Recomendamos vivamente que proteja o Meu Pessoal utilizando [políticas de acesso condicional Azure AD.](../conditional-access/index.yml) Para aplicar uma política de acesso condicional ao meu pessoal, tem de criar manualmente o diretor de serviço do My Staff utilizando o PowerShell.

### <a name="apply-a-conditional-access-policy-to-my-staff"></a>Aplicar uma política de acesso condicional ao meu pessoal

1. Instale os [cmdlets Beta PowerShell do Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1).
1. Execute os seguintes comandos:

   ```powershell
   Connect-Graph -Scopes "Directory.AccessAsUser.All"
   New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"
   ```
1. Crie uma política de acesso condicional que se aplique à aplicação da nuvem My Staff.

    ![Criar uma política de acesso condicional para a aplicação My Staff](media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Usando o meu pessoal

Quando um utilizador vai ao My Staff, é-lhes mostrado o nome das [unidades administrativas](directory-administrative-units.md) sobre as quais têm permissões administrativas. Na [documentação](../user-help/my-staff-team-manager.md)do utilizador My Staff, utilizamos o termo "localização" para se referir às unidades administrativas. Se as permissões de um administrador não tiverem um âmbito de UA, as permissões aplicam-se em toda a organização. Após a ativação do My Staff, os utilizadores que estão habilitados e lhes foi atribuído uma função administrativa podem aceder-lhe através [https://mystaff.microsoft.com](https://mystaff.microsoft.com) de . Podem selecionar uma AU para visualizar os utilizadores na UE E, e selecionar um utilizador para abrir o seu perfil.

## <a name="reset-a-users-password"></a>Repor a palavra-passe de um utilizador

As seguintes funções têm permissão para redefinir a palavra-passe de um utilizador:

- [Administrador de autenticação](directory-assign-admin-roles.md#authentication-administrator)
- [Administrador de autenticação privilegiada](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrador global](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Administrador da Helpdesk](directory-assign-admin-roles.md#helpdesk-administrator)
- [Administrador de utilizadores](directory-assign-admin-roles.md#user-administrator)
- [Administrador de palavras-passe](directory-assign-admin-roles.md#password-administrator)

Da **My Staff,** abra o perfil de um utilizador. Selecione **Redefinir a palavra-passe**.

- Se o utilizador estiver apenas na nuvem, pode ver uma senha temporária que pode dar ao utilizador.
- Se o utilizador estiver sincronizado a partir do Ative Directory no local, pode introduzir uma palavra-passe que satisfaça as suas políticas de AD no local. Em seguida, pode dar essa palavra-passe ao utilizador.

    ![Indicador de progresso de redefinição de palavra-passe e notificação de sucesso](media/my-staff-configure/reset-password.png)

O utilizador é obrigado a alterar a sua palavra-passe da próxima vez que iniciar sedu.

## <a name="manage-a-phone-number"></a>Gerir um número de telefone

Da **My Staff,** abra o perfil de um utilizador.

- Selecione Adicionar a secção **de números de telefone** para adicionar um número de telefone para o utilizador
- **Selecione Editar número de telefone** para alterar o número de telefone
- **Selecione Remover O número de telefone** para remover o número de telefone do utilizador

Dependendo das definições, o utilizador pode então utilizar o número de telefone configurado para iniciar sposição com SMS, realizar a autenticação de vários fatores e fazer reiniciar a palavra-passe de autosserviço.

Para gerir o número de telefone de um utilizador, deve ser-lhe atribuída uma das seguintes funções:

- [Administrador de autenticação](directory-assign-admin-roles.md#authentication-administrator)
- [Administrador de autenticação privilegiada](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrador global](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Pesquisa

Pode pesquisar por AUs e utilizadores na sua organização utilizando a barra de pesquisa em My Staff. Pode pesquisar em todos os Utilizadores e Utilizadores da sua organização, mas só pode fazer alterações aos utilizadores que se encontrem numa AU sobre a qual lhe foram dadas permissões de administração.

Também pode pesquisar por um utilizador dentro de uma AU. Para isso, utilize a barra de pesquisa no topo da lista de utilizadores.

## <a name="audit-logs"></a>Registos de auditoria

Pode ver os registos de auditoria das ações tomadas no Meu Pessoal no portal Azure Ative Directory. Se um registo de auditoria foi gerado por uma ação tomada no My Staff, verá isso indicado em DETALHES ADICIONAIS no evento de auditoria.

## <a name="next-steps"></a>Passos seguintes

Documentação do [meu pessoal](../user-help/my-staff-team-manager.md) 
 [Documentação das unidades administrativas](directory-administrative-units.md)