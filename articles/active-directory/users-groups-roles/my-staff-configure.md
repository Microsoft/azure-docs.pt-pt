---
title: Use o meu pessoal para delegar a gestão de utilizadores (pré-visualização) - Azure AD / Microsoft Docs
description: Delegar gestão de utilizadores usando o Meu Pessoal e unidades administrativas
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/23/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 282946a023e4e79ee79b05cc2a317efc5a4056e4
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165875"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Gerencie os seus utilizadores com o My Staff (pré-visualização)

O meu pessoal permite-lhe delegar uma figura de autoridade, como um gerente de loja ou um líder de equipa, as permissões para garantir que os seus funcionários tenham acesso às suas contas De AD Azure. Em vez de confiar em um helpdesk central, as organizações podem delegar tarefas comuns, tais como repor palavras-passe ou alterar números de telefone para um gestor de equipa. Com o My Staff, um utilizador que não consegue aceder à sua conta pode recuperar o acesso em apenas alguns cliques, sem necessidade de helpdesk ou pessoal de TI.

Antes de configurar o Meu Pessoal para a sua organização, recomendamos que reveja esta documentação, bem como a [documentação](../user-help/my-staff-team-manager.md) do utilizador para garantir que compreende a funcionalidade e o impacto desta funcionalidade nos seus utilizadores. Pode aproveitar a documentação do utilizador para treinar e preparar os seus utilizadores para a nova experiência e ajudar a garantir um lançamento bem-sucedido.

## <a name="how-my-staff-works"></a>Como funciona o meu pessoal

O meu pessoal baseia-se em unidades administrativas (UA), que são um contentor de recursos que pode ser utilizado para restringir o âmbito do controlo administrativo de uma missão. Em My Staff, as UA são usadas para definir um subconjunto de utilizadores de uma organização, como uma loja ou departamento. Depois, por exemplo, um team manager poderia ser atribuído a um papel cujo âmbito é um ou mais UsA. No exemplo abaixo, foi concedido ao utilizador a função De Autenticação Administrativa, e as três Usa são o âmbito do papel. Para obter mais informações sobre unidades administrativas, consulte [a gestão de unidades administrativas no Diretório Ativo do Azure.](directory-administrative-units.md)

## <a name="how-to-enable-my-staff"></a>Como ativar o Meu Pessoal

Depois de configurado a AUs, pode aplicar este âmbito aos seus utilizadores que acedem ao Meu Pessoal. Só os utilizadores a quem seja atribuída uma função administrativa podem aceder ao Meu Pessoal. Para ativar o meu pessoal, complete os seguintes passos:

1. Inscreva-se no portal Azure como administrador do Utilizador.
2. Navegue nas**definições** > **User feature previews** > **Manage user feature preview settings**de >  **utilizador do Diretório Ativo do Azure**
3. No âmbito **dos Administradores pode aceder ao Meu Pessoal,** pode optar por ativar para todos os utilizadores, utilizadores selecionados ou sem acesso ao utilizador.

> [!Note]
> Só os utilizadores a quem foi atribuída uma função de administrador podem aceder ao Meu Pessoal. Se ativar o Meu Pessoal para um utilizador que não tenha um papel de administrador, não poderá aceder ao Meu Pessoal.

## <a name="conditional-access"></a>Acesso condicional

Pode proteger o portal My Staff utilizando a política de acesso condicional Azure AD. Use-o para tarefas como exigir a autenticação de vários fatores antes de aceder ao Meu Pessoal.

Recomendamos vivamente que proteja o meu pessoal utilizando políticas de [acesso condicional Azure AD.](https://docs.microsoft.com/azure/active-directory/conditional-access/) Para aplicar uma política de acesso condicional ao Meu Pessoal, deve criar manualmente o diretor de serviço my staff utilizando o PowerShell.

### <a name="apply-a-----conditional-access-policy-to-my-staff"></a>Aplicar uma política de acesso condicional ao meu pessoal

1. Instale os [cmdlets Beta PowerShell](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1)do Microsoft Graph .
1. Execute os seguintes comandos:

        Connect-Graph -Scopes "Directory.AccessAsUser.All"
        New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"

1. Crie uma política de Acesso Condicional que se aplique à aplicação my staff cloud.

    ![Criar uma política de acesso condicional para a app My Staff](media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Usando o meu pessoal

Quando um utilizador vai ao Meu Pessoal, é-lhes mostrado os nomes das [unidades administrativas](directory-administrative-units.md) sobre as quais têm permissões administrativas. Na [documentação](../user-help/my-staff-team-manager.md)do utilizador do Meu Pessoal, utilizamos o termo "localização" para se referir às unidades administrativas. Se as permissões de um administrador não tiverem um âmbito de UA, as permissões aplicam-se em toda a organização. Depois de o meu pessoal ter sido ativado, os utilizadores que estão [https://mystaff.microsoft.com](https://mystaff.microsoft.com)habilitados e que lhe foram atribuídos um papel administrativo podem aceder-lhe através de . Podem selecionar uma UA para visualizar os utilizadores nessa UA e selecionar um utilizador para abrir o seu perfil.

## <a name="licenses"></a>Licenças

Cada utilizador habilitado no Meu Pessoal deve ser licenciado, mesmo que não utilize o portal My Staff. Cada utilizador ativado deve ter uma das seguintes licenças Azure AD ou Microsoft 365:

- Azure AD Premium P1 ou P2
- Microsoft 365 F1 ou F3

## <a name="reset-a-users-password"></a>Repor a palavra-passe de um utilizador

As seguintes funções têm permissão para redefinir a palavra-passe do utilizador:

- [Administrador de autenticação](directory-assign-admin-roles.md#authentication-administrator)
- [Administrador de autenticação privilegiado](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrador global](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Administrador de helpdesk](directory-assign-admin-roles.md#helpdesk-administrator)
- [Administrador de utilizadores](directory-assign-admin-roles.md#user-administrator)
- [Administrador de palavras-passe](directory-assign-admin-roles.md#password-administrator)

From **My Staff**, abra o perfil de um utilizador. Selecione **Redefinir a palavra-passe**.

- Se o utilizador estiver apenas na nuvem, pode ver uma senha temporária que pode dar ao utilizador.
- Se o utilizador estiver sincronizado a partir do Diretório Ativo no local, pode introduzir uma palavra-passe que cumpra as suas políticas de AD no local. Em seguida, pode dar essa palavra-passe ao utilizador.

    ![Password redefinir indicador de progresso e notificação de sucesso](media/my-staff-configure/reset-password.png)

O utilizador é obrigado a alterar a sua palavra-passe da próxima vez que iniciar a sua inscrição.

## <a name="manage-a-phone-number"></a>Gerir um número de telefone

From **My Staff**, abra o perfil de um utilizador.

- Selecione Adicionar secção **de números** de telefone para adicionar um número de telefone para o utilizador
- **Selecione Editar número de telefone** para alterar o número de telefone
- **Selecione Remover o número de telefone** para remover o número de telefone do utilizador

Dependendo das definições, o utilizador pode então utilizar o número de telefone que configurapara iniciar sessão com SMS, realizar a autenticação de vários fatores e realizar o reset da palavra-passe self-service.

Para gerir o número de telefone de um utilizador, deve ser-lhe atribuída uma das seguintes funções:

- [Administrador de autenticação](directory-assign-admin-roles.md#authentication-administrator)
- [Administrador de autenticação privilegiado](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrador global](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Pesquisa

Pode pesquisar AUs e utilizadores da sua organização usando a barra de pesquisa em My Staff. Pode pesquisar em todos os AUs e utilizadores da sua organização, mas só pode fazer alterações aos utilizadores que se encontram numa UA sobre as quais lhe foram dadas permissões de administração.

Também pode procurar um utilizador dentro de uma UA. Para tal, utilize a barra de pesquisa no topo da lista de utilizadores.

## <a name="audit-logs"></a>Registos de auditoria

Pode ver registos de auditoria para ações tomadas em My Staff no portal Azure Ative Directory. Se um registo de auditoria foi gerado por uma ação tomada no Meu Pessoal, verá isso indicado em DETALHES ADICIONAIS no evento de auditoria.

## <a name="next-steps"></a>Passos seguintes

[Documentação de](../user-help/my-staff-team-manager.md)
pessoal do utilizador[Unidades administrativas Documentação](directory-administrative-units.md)
