---
title: Delegado de gestão de administradores de candidaturas perms - Azure AD / Microsoft Docs
description: Concessão de autorizações para gestão de acesso a aplicações no Diretório Ativo do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251bc1c2277f9e43543f95c49d0b730a5a41c3d9
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77558983"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegar permissões de registo de apps no Diretório Ativo do Azure

Este artigo descreve como utilizar permissões concedidas por funções personalizadas no Azure Ative Directory (Azure AD) para responder às suas necessidades de gestão de aplicações. Em Azure AD, pode delegar a criação de candidaturas e permissões de gestão das seguintes formas:

- [Restringir quem pode criar aplicações](#restrict-who-can-create-applications) e gerir as aplicações que criam. Por predefinição no Azure AD, todos os utilizadores podem registar registos de aplicações e gerir todos os aspetos das aplicações que criam. Isto só pode ser restringido para permitir que pessoas selecionadas permissam.
- [Atribuir um ou mais proprietários a uma aplicação](#assign-application-owners). Esta é uma forma simples de conceder a alguém a capacidade de gerir todos os aspetos da configuração da AD Azure para uma aplicação específica.
- [Atribuir uma função administrativa incorporada](#assign-built-in-application-admin-roles) que concede acesso à gestão da configuração em Azure AD para todas as aplicações. Esta é a forma recomendada de conceder aos peritos em TI acesso para gerir amplas permissões de configuração de aplicações sem conceder acesso para gerir outras partes da AD Azure não relacionadas com a configuração da aplicação.
- [Criar uma função personalizada](#create-and-assign-a-custom-role-preview) que defina permissões muito específicas e atribui-a a alguém, quer ao âmbito de uma única aplicação como proprietário limitado, quer no âmbito do diretório (todas as aplicações) como administrador limitado.

É importante considerar a concessão de acesso usando um dos métodos acima por duas razões. Em primeiro lugar, delegar a capacidade de executar tarefas administrativas reduz a sobrecarga global do administrador. Em segundo lugar, o uso de permissões limitadas melhora a sua postura de segurança e reduz o potencial de acesso não autorizado. As questões da delegação e as orientações gerais são discutidas na [administração de delegados no Diretório Ativo do Azure.](roles-concept-delegation.md)

## <a name="restrict-who-can-create-applications"></a>Restringir quem pode criar aplicações

Por predefinição no Azure AD, todos os utilizadores podem registar registos de aplicações e gerir todos os aspetos das aplicações que criam. Todos também têm a capacidade de consentir com apps que acedam aos dados da empresa em seu nome. Pode optar por conceder seletivamente essas permissões, definindo os interruptores globais para 'Não' e adicionando os utilizadores selecionados à função de Desenvolvedor de Aplicações.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Para desativar a capacidade padrão de criar registos de aplicações ou consentimento para aplicações

1. Inscreva-se na sua organização Azure AD com uma conta que é elegível para o papel de administrador global na sua organização Azure AD.
1. Definir um ou ambos os seguintes:

    - Na página de definições do [Utilizador para a sua organização,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)defina que os Utilizadores podem registar a definição de **aplicações** para Nº. Isto irá desativar a capacidade padrão para os utilizadores criarem registos de aplicações.
    - Nas [definições de utilizador para aplicações empresariais,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)definir os **Utilizadores podem consentir** em aplicações de acesso aos dados da empresa em seu nome definição para Nº. Isto irá desativar a capacidade padrão para os utilizadores consentirem em aplicações que acedam aos dados da empresa em seu nome.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Conceda permissões individuais para criar e consentir com pedidos quando a capacidade padrão é desativada

Atribuir a função de desenvolvedor de aplicações para conceder a capacidade de criar registos de aplicações quando os Utilizadores podem registar a definição de **aplicações** está definida para Nº. Esta função também concede permissão para consentir em nome próprio quando os Utilizadores podem consentir que as aplicações que **acedem aos dados** da empresa em seu nome estão definidas como Nº. Como comportamento do sistema, quando um utilizador cria um novo registo de aplicação, são automaticamente adicionados como primeiro proprietário. As permissões de propriedade dão ao utilizador a capacidade de gerir todos os aspetos de um registo de aplicação ou aplicação empresarial que possuam.

## <a name="assign-application-owners"></a>Atribuir os proprietários de candidaturas

Atribuir proprietários é uma forma simples de conceder a capacidade de gerir todos os aspetos da configuração da AD Azure para um registo de aplicação específico ou aplicação empresarial. Como comportamento do sistema, quando um utilizador cria um novo registo de aplicação são automaticamente adicionados como primeiro proprietário. As permissões de propriedade dão ao utilizador a capacidade de gerir todos os aspetos de um registo de aplicação ou aplicação empresarial que possuam. O proprietário original pode ser removido e os proprietários adicionais podem ser adicionados.

### <a name="enterprise-application-owners"></a>Proprietários de aplicações empresariais

Como proprietário, um utilizador pode gerir a configuração específica da organização da aplicação da empresa, tais como a configuração, provisionamento e atribuição de utilizadores únicos. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais, os proprietários só podem gerir as aplicações empresariais que possuem.

Em alguns casos, as aplicações empresariais criadas a partir da galeria de aplicações incluem tanto uma aplicação empresarial como um registo de candidatura. Quando isso for verdade, adicionar um proprietário à aplicação da empresa adiciona automaticamente o proprietário ao correspondente registo de candidatura como proprietário.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Atribuir um proprietário a uma aplicação empresarial

1. Inscreva-se na [sua organização AD Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que é elegível para o administrador de aplicação ou administrador de aplicação Cloud para a organização.
1. Na página de [registos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) da App para a organização, selecione uma aplicação para abrir a página 'Overview' para a aplicação.
1. Selecione **Proprietários** para ver a lista dos proprietários para a aplicação.
1. Selecione **Adicionar** para selecionar um ou mais proprietários para adicionar à aplicação.

> [!IMPORTANT]
> Os utilizadores e os diretores de serviço podem ser proprietários de registos de aplicações. Apenas os utilizadores podem ser proprietários de aplicações empresariais. Os grupos não podem ser atribuídos como proprietários de qualquer um dos dois.
>
> Os proprietários podem adicionar credenciais a uma aplicação e usar essas credenciais para personificar a identidade da aplicação. A aplicação pode ter mais permissões do que o proprietário, pelo que seria uma elevação de privilégio sobre o que o proprietário tem acesso como utilizador ou diretor de serviço. Um proprietário de aplicação poderia potencialmente criar ou atualizar utilizadores ou outros objetos enquanto se personificava a aplicação, dependendo das permissões da aplicação.

## <a name="assign-built-in-application-admin-roles"></a>Atribuir funções de administrador de aplicação incorporada

A Azure AD tem um conjunto de funções de administrador incorporado para garantir o acesso à configuração em Azure AD para todas as aplicações. Estas funções são a forma recomendada de conceder aos peritos em TI acesso para gerir amplas permissões de configuração de aplicações sem permitir o acesso a outras partes do Azure AD não relacionadas com a configuração da aplicação.

- Administrador de aplicações: Os utilizadores desta função podem criar e gerir todos os aspetos das aplicações da empresa, registos de aplicações e configurações de procuração de aplicações. Esta função também concede a possibilidade de consentir com permissões delegadas, e permissões de aplicação excluindo o Microsoft Graph. Os utilizadores atribuídos a esta função não são adicionados como proprietários na criação de novos registos de aplicações ou aplicações empresariais.
- Administrador de aplicação em nuvem: Os utilizadores desta função têm as mesmas permissões que a função de Administrador de Aplicação, excluindo a capacidade de gerir o proxy da aplicação. Os utilizadores atribuídos a esta função não são adicionados como proprietários na criação de novos registos de aplicações ou aplicações empresariais.

Para mais informações e para ver a descrição destas funções, consulte [as funções disponíveis.](directory-assign-admin-roles.md#available-roles)

Siga as instruções nas [funções de Atribuição aos utilizadores com](../fundamentals/active-directory-users-assign-role-azure-portal.md) o Guia Ativo do Azure como atribuir as funções de Administrador de Aplicação ou Administrador de Aplicação em Nuvem.

> [!IMPORTANT]
> Administradores de aplicações e administradores de aplicações em nuvem podem adicionar credenciais a uma aplicação e usar essas credenciais para personificar a identidade da aplicação. O pedido pode ter permissões que são uma elevação de privilégio sobre as permissões do papel de administrador. Um administrador desta função poderia potencialmente criar ou atualizar utilizadores ou outros objetos enquanto se personificava a aplicação, dependendo das permissões da aplicação.
> Nenhum dos papéis concede a capacidade de gerir as definições de Acesso Condicional.

## <a name="create-and-assign-a-custom-role-preview"></a>Criar e atribuir uma função personalizada (pré-visualização)

Criar papéis personalizados e atribuir papéis personalizados são passos separados:

- [Crie uma *definição* ](roles-create-custom.md) de função personalizada e adicione [permissões a partir de uma lista predefinida](roles-custom-available-permissions.md). Estas são as mesmas permissões usadas nos papéis incorporados.
- [Crie uma atribuição de *funções* ](roles-assign-powershell.md) para atribuir o papel personalizado.

Esta separação permite-lhe criar uma definição de função única e, em seguida, atribuí-la muitas vezes em *diferentes âmbitos*. Um papel personalizado pode ser atribuído no âmbito da organização, ou pode ser atribuído no âmbito se um único objeto Azure AD. Um exemplo de um âmbito de objeto é um único registo de aplicação. Utilizando diferentes âmbitos, a mesma definição de função pode ser atribuída à Sally em todas as inscrições de aplicações na organização e, em seguida, a Naveen apenas sobre o registo da aplicação Contoso Expense Reports.

Dicas ao criar e utilizar funções personalizadas para delegar a gestão de aplicações:
- As funções personalizadas apenas concedem acesso nas lâminas de registo de aplicações mais atuais do portal Azure AD. Não concedem acesso nas lâminas de registo de aplicações antigas.
- As funções personalizadas não concedem acesso ao portal Azure AD quando a definição de utilizador "Restringir o acesso ao portal de administração da AD Azure" está definida para Sim.
- Registos de aplicações o utilizador tem acesso a atribuição de funções apenas aparecem no separador 'Todas as aplicações' na página de registo da App. Não aparecem no separador "Aplicações Próprias".

Para obter mais informações sobre o básico das funções personalizadas, consulte a visão geral das [funções personalizadas,](roles-custom-overview.md)bem como como [criar um papel personalizado](roles-create-custom.md) e como atribuir um [papel](roles-assign-powershell.md).

## <a name="next-steps"></a>Passos seguintes

- [Subtipos e permissões de registo de candidaturas](roles-custom-available-permissions.md)
- [Referência de função de administrador da AD Azure](directory-assign-admin-roles.md)
