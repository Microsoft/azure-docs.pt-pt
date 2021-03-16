---
title: Permissões de administrador de gestão de pedidos delegados - Azure AD | Microsoft Docs
description: Conceder permissões para gestão de acesso a aplicações no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1e8a0f1919da125a571429e1efff06589c7e85a
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466716"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegar permissões de registo de aplicativos no Azure Ative Directory

Este artigo descreve como utilizar permissões concedidas por funções personalizadas no Azure Ative Directory (Azure AD) para atender às necessidades de gestão da sua aplicação. Em Azure AD, pode delegar permissões de criação e gestão de aplicações das seguintes formas:

- [Restringir quem pode criar aplicações](#restrict-who-can-create-applications) e gerir as aplicações que criam. Por padrão no Azure AD, todos os utilizadores podem registar aplicações e gerir todos os aspetos das aplicações que criam. Isto pode ser restringido apenas para permitir pessoas selecionadas que permissão.
- [Atribuir um ou mais proprietários a uma aplicação.](#assign-application-owners) Esta é uma forma simples de conceder a alguém a capacidade de gerir todos os aspetos da configuração AD Azure para uma aplicação específica.
- [Atribuindo uma função administrativa incorporada](#assign-built-in-application-admin-roles) que concede acesso à configuração de gestão em Azure AD para todas as aplicações. Esta é a forma recomendada de conceder aos especialistas em TI acesso para gerir permissões de configuração de aplicações amplas sem dar acesso à gestão de outras partes do AD Azure não relacionadas com a configuração da aplicação.
- [Criar um papel personalizado](#create-and-assign-a-custom-role-preview) que defina permissões muito específicas e atribuí-la a alguém quer ao âmbito de uma única aplicação como proprietário limitado, quer ao âmbito do diretório (todas as aplicações) como administrador limitado.

É importante considerar a concessão de acesso usando um dos métodos acima por duas razões. Em primeiro lugar, delegar a capacidade de executar tarefas administrativas reduz a sobrecarga global do administrador. Em segundo lugar, a utilização de permissões limitadas melhora a sua postura de segurança e reduz o potencial de acesso não autorizado. Para obter orientações sobre o planeamento da segurança de funções, consulte [garantir o acesso privilegiado para implantações híbridas e em nuvem em Azure AD](security-planning.md).

## <a name="restrict-who-can-create-applications"></a>Restringir quem pode criar aplicações

Por padrão no Azure AD, todos os utilizadores podem registar aplicações e gerir todos os aspetos das aplicações que criam. Todos também têm a capacidade de consentir que as aplicações acedam aos dados da empresa em seu nome. Pode optar por conceder seletivamente essas permissões, definindo os interruptores globais para 'Não' e adicionando os utilizadores selecionados à função de Desenvolvedor de Aplicações.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Desativar a capacidade padrão de criar registos de aplicações ou consentimento para aplicações

1. Inscreva-se na sua organização Azure AD com uma conta que seja elegível para o papel de administrador global na sua organização Azure AD.
1. Desaguise um ou ambos os seguintes:

    - Na página de definições do [Utilizador para a sua organização,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)defina os Utilizadores que podem registar a definição de **aplicações** para Nº. Isto irá desativar a capacidade padrão para os utilizadores criarem registos de aplicações.
    - Nas [definições do utilizador para aplicações empresariais,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)defina que os **Utilizadores podem consentir que as aplicações acedam aos dados da empresa em seu nome, definindo** para Nº. Isto irá desativar a capacidade padrão para os utilizadores consentirem com as aplicações que acedem aos dados da empresa em seu nome.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Conceder permissões individuais para criar e consentir com aplicações quando a capacidade padrão é desativada

Atribuir a função de desenvolvedor de aplicações para conceder a capacidade de criar registos de aplicações quando os **Utilizadores podem registar a** definição de aplicações está definida como Nº. Esta função também permite consentir em nome próprio quando os **Utilizadores podem consentir que as aplicações que acedam aos dados da empresa em seu nome** estão definidas como Nº. Como um comportamento do sistema, quando um utilizador cria um novo registo de aplicações, é automaticamente adicionado como o primeiro proprietário. As permissões de propriedade conferem ao utilizador a capacidade de gerir todos os aspetos de um registo de aplicação ou de uma aplicação empresarial que possuam.

## <a name="assign-application-owners"></a>Atribuir os proprietários de candidaturas

Atribuir os proprietários é uma forma simples de conceder a capacidade de gerir todos os aspetos da configuração AD Azure para um registo de aplicação específico ou aplicação empresarial. Como um comportamento do sistema, quando um utilizador cria um novo registo de aplicações, é automaticamente adicionado como primeiro proprietário. As permissões de propriedade conferem ao utilizador a capacidade de gerir todos os aspetos de um registo de aplicação ou de uma aplicação empresarial que possuam. O proprietário original pode ser removido e proprietários adicionais podem ser adicionados.

### <a name="enterprise-application-owners"></a>Proprietários de aplicações empresariais

Como proprietário, um utilizador pode gerir a configuração específica da organização da aplicação da empresa, como a configuração única de sign-on, provisionamento e atribuição de utilizadores. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais, os proprietários só podem gerir as aplicações da empresa que possuem.

Em alguns casos, os pedidos de empresa criados a partir da galeria de aplicações incluem tanto um pedido de empresa como um registo de pedidos. Quando isso for verdade, adicionar um proprietário à aplicação da empresa adiciona automaticamente o proprietário ao registo de pedido correspondente como proprietário.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Atribuir um proprietário a uma aplicação de empresa

1. Inscreva-se na [sua organização Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que elegível para o administrador de aplicação ou administrador de aplicação cloud para a organização.
1. Na página de [registos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) da App para a organização, selecione uma aplicação para abrir a página Overview para a aplicação.
1. Selecione **Proprietários** para ver a lista dos proprietários para a aplicação.
1. **Selecione Adicionar** para selecionar um ou mais proprietários para adicionar à aplicação.

> [!IMPORTANT]
> Os utilizadores e os principais de serviço podem ser proprietários de registos de candidaturas. Apenas os utilizadores podem ser proprietários de aplicações empresariais. Os grupos não podem ser designados como proprietários de qualquer um dos dois.
>
> Os proprietários podem adicionar credenciais a uma aplicação e usar essas credenciais para personificar a identidade da aplicação. A aplicação pode ter mais permissões do que o proprietário, pelo que seria uma elevação de privilégio sobre o que o proprietário tem acesso como utilizador ou diretor de serviço. Um proprietário de aplicação poderia potencialmente criar ou atualizar utilizadores ou outros objetos enquanto se fazia passar pela aplicação, dependendo das permissões da aplicação.

## <a name="assign-built-in-application-admin-roles"></a>Atribuir funções de administração de aplicações incorporadas

A Azure AD tem um conjunto de funções de administração incorporadas para garantir o acesso à configuração de gestão em Azure AD para todas as aplicações. Estas funções são a forma recomendada de conceder aos especialistas em TI acesso para gerir permissões de configuração de aplicações amplas sem dar acesso à gestão de outras partes do AZure AD não relacionadas com a configuração da aplicação.

- Administrador de aplicação: Os utilizadores nesta função podem criar e gerir todos os aspetos das aplicações empresariais, registos de aplicações e configurações de procuração de aplicações. Esta função também concede a capacidade de consentir com permissões delegadas e permissões de aplicação excluindo o Microsoft Graph. Os utilizadores destacados para esta função não são adicionados como proprietários ao criar novos registos de aplicações ou aplicações empresariais.
- Administrador de aplicação na nuvem: Os utilizadores nesta função têm as mesmas permissões que a função de Administrador de Aplicação, excluindo a capacidade de gerir o proxy da aplicação. Os utilizadores destacados para esta função não são adicionados como proprietários ao criar novos registos de aplicações ou aplicações empresariais.

Para obter mais informações e para ver a descrição destas funções, consulte [as funções incorporadas da AZure AD](permissions-reference.md).

Siga as instruções nas [funções De atribuir aos utilizadores com diretório ativo Azure](../fundamentals/active-directory-users-assign-role-azure-portal.md) como orientar para atribuir as funções de Administrador de Aplicação ou Administrador de Aplicação cloud.

> [!IMPORTANT]
> Os Administradores de Aplicações e Administradores de Aplicações em Nuvem podem adicionar credenciais a uma aplicação e usar essas credenciais para personificar a identidade da aplicação. O pedido pode ter permissões que são uma elevação de privilégio sobre as permissões do papel de administrador. Um administrador nesta função poderia potencialmente criar ou atualizar utilizadores ou outros objetos enquanto se fazia passar pela aplicação, dependendo das permissões da aplicação.
> Nenhuma das funções garante a capacidade de gerir as definições de Acesso Condicional.

## <a name="create-and-assign-a-custom-role-preview"></a>Criar e atribuir uma função personalizada (pré-visualização)

Criar funções personalizadas e atribuir funções personalizadas são passos separados:

- [Crie uma definição de *função* personalizada](custom-create.md) e [adicione-lhe permissões a partir de uma lista pré-configurada](custom-available-permissions.md). Estas são as mesmas permissões usadas nas funções incorporadas.
- [Crie uma *tarefa de função*](custom-assign-powershell.md) para atribuir o papel personalizado.

Esta separação permite-lhe criar uma definição de papel única e, em seguida, atribuí-la muitas vezes em *diferentes âmbitos*. Um papel personalizado pode ser atribuído no âmbito da organização, ou pode ser atribuído no âmbito se um único objeto AD Azure. Um exemplo de um âmbito de objeto é um registo de aplicações únicas. Utilizando diferentes âmbitos, a mesma definição de papel pode ser atribuída à Sally em todos os registos de aplicações na organização e, em seguida, à Naveen sobre apenas o registo da app Contoso Expense Reports.

Dicas ao criar e utilizar funções personalizadas para delegar a gestão de aplicações:
- As funções personalizadas apenas concedem acesso nas lâminas de registo de aplicações mais atuais do portal AD AZure. Não concedem acesso nas lâminas de registo de aplicações antigas.
- As funções personalizadas não concedem acesso ao portal AD Azure quando a definição do utilizador "Restringir o acesso ao portal de administração AD AD" estiver definida como "Sim".
- As inscrições de aplicações que o utilizador tem acesso a atribuições de funções só aparecem no separador 'Todas as aplicações' na página de registo da App. Não aparecem no separador "Aplicações Próprias".

Para obter mais informações sobre o básico das funções personalizadas, consulte a visão geral das [funções personalizadas,](custom-overview.md)bem como como [criar um papel personalizado](custom-create.md) e como atribuir um [papel.](custom-assign-powershell.md)

## <a name="next-steps"></a>Passos seguintes

- [Subtipos e permissões de registo de pedidos](custom-available-permissions.md)
- [Referência de função de administrador da AD Azure](permissions-reference.md)
