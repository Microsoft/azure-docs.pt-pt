---
title: Criar um novo pacote de acesso na gestão de direitos do Azure AD (pré-visualização) - Azure Active Directory
description: Saiba como criar um novo pacote de acesso de recursos que pretende partilhar na gestão de direitos do Azure Active Directory (pré-visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 312658bd653d94f3e1a32204b3c5ae4ae290558e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65832862"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Criar um novo pacote de acesso na gestão de direitos do Azure AD (pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um pacote de acesso permite-lhe fazer uma configuração única de recursos e as políticas que administra automaticamente o acesso para o ciclo de vida do pacote de acesso. Este artigo descreve como criar um novo pacote de acesso.

## <a name="overview"></a>Descrição geral

Todos os pacotes de acesso tiver de ser colocados num contêiner chamado um catálogo. Um catálogo define quais os recursos que pode adicionar ao seu pacote de acesso. Se não especificar um catálogo, será colocado seu pacote de acesso para o catálogo geral. Atualmente, não é possível mover um pacote de acesso existente para um catálogo diferente.

Todos os pacotes de acesso tem de ter pelo menos uma política. As políticas de especificar quem pode pedir o pacote de acesso e também as definições de aprovação e expiração. Quando cria um novo pacote de acesso, pode criar uma política inicial para os utilizadores no seu diretório, para os utilizadores não no seu diretório, existência de atribuições diretas administrador apenas, ou pode optar por criar a política mais tarde.

O diagrama seguinte mostra o processo de alto nível para criar um novo pacote de acesso.

![Criar um processo de empacotamento de acesso](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Iniciar novo pacote de acesso

**Função de pré-requisitos:** Administrador de utilizador ou proprietário de catálogo

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **aceder a pacotes**.

    ![Gestão de direitos no portal do Azure](./media/entitlement-management-shared/elm-access-packages.png)

1. Clique em **novo pacote de acesso**.

## <a name="basics"></a>Noções básicas

Sobre o **Noções básicas** separador, dê um nome ao pacote de acesso e especificar qual catálogo para criar o pacote de acesso no.

1. Introduza um nome a apresentar e uma descrição para o pacote de acesso. Os utilizadores verão estas informações quando submeter um pedido para o pacote de acesso.

1. Na **catálogo** na lista pendente, selecione o pacote no catálogo que pretende criar o acesso. Por exemplo, pode ter um proprietário de catálogo que gerencia todos os recursos de marketing que podem ser solicitados. Neste caso, pode selecionar o catálogo de marketing.

    Apenas verá catálogos de que tem permissão para criar pacotes de acesso no. Para criar o pacote de acesso num catálogo existente, tem de ser, pelo menos, um administrador de utilizadores, o proprietário de catálogo ou o Gestor de pacotes de acesso.

    ![Pacote de acesso - Noções básicas](./media/entitlement-management-access-package-create/basics.png)

    Se gostaria de criar seu pacote de acesso num novo catálogo, clique em **criar novo**. Introduza o nome do catálogo e a descrição e, em seguida, clique em **criar**.

    O pacote de acesso que está a criar e todos os recursos incluídos na mesma serão adicionados ao catálogo de novo. Além disso, tornará automaticamente o primeiro proprietário do catálogo. Pode adicionar os proprietários de catálogo adicionais.

    Para criar um novo catálogo, tem de ser, pelo menos, um utilizador administrador ou o criador de catálogo.

1. Clique em **Seguinte**.

## <a name="resource-roles"></a>Funções de recursos

Sobre o **funções de recursos** separador, selecionar os recursos para incluir no pacote de acesso.

1. Clique no tipo de recurso que pretende adicionar (**grupos**, **aplicativos**, ou **sites do SharePoint**).

1. No painel de Select que aparece, selecione um ou mais recursos da lista.

    ![Pacote - acesso a funções de recursos](./media/entitlement-management-access-package-create/resource-roles.png)

    Se estiver a criar o pacote de acesso no catálogo de geral ou um novo catálogo, poderá escolher qualquer recurso do diretório ao qual é proprietário. Tem de ser, pelo menos, um administrador de utilizadores ou de criador do catálogo.

    Se estiver a criar o pacote de acesso num catálogo existente, pode selecionar qualquer recurso que já se encontra no catálogo sem proprietário-lo.

    Se for um administrador de utilizador ou proprietário de catálogo, tem a opção adicional de seleção de recursos é proprietário, que não são ainda no catálogo. Se selecionar recursos não estão atualmente no catálogo de selecionada, esses recursos também serão adicionados ao catálogo de outros administradores de catálogo criar pacotes de acesso com. Se pretender selecionar recursos que estão atualmente no catálogo de selecionada, verifique os **ver apenas** caixa de verificação na parte superior do pan selecione.

1. Assim que tiver selecionado os recursos, o **função** , selecione a função que pretende que os utilizadores a ser atribuído para o recurso.

    ![Pacote de acesso - a seleção da função de recursos](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Clique em **Seguinte**.

## <a name="policy"></a>Política

Sobre o **política** guia, vai criar a primeira diretiva para especificar quem pode pedir o pacote de acesso e as definições de aprovação e expiração também. Mais tarde, pode criar políticas mais para permitir que os grupos de utilizadores para pedir o pacote de acesso com suas próprias definições de aprovação e expiração adicionais. Também é possível criar a política mais tarde.

1. Definir o **criar a primeira diretiva** alternar para **agora** ou **mais tarde**.

    ![Pacote - política de acesso](./media/entitlement-management-access-package-create/policy.png)

1. Se selecionou **mais tarde**, avance para o [revisão + criar](#review--create) secção para criar seu pacote de acesso.

1. Se selecionou **agora**, execute os passos de uma das seguintes secções de política.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Rever + criar

Sobre o **rever + criar** separador, pode rever as suas definições e verificação de erros de validação.

1. Reveja as definições do pacote de acesso

    ![Pacote de acesso - a definição de política de política de ativação](./media/entitlement-management-access-package-create/review-create.png)

1. Clique em **criar** para criar o pacote de acesso.

    O novo pacote de acesso é apresentado na lista de pacotes de acesso.

## <a name="next-steps"></a>Passos Seguintes

- [Editar e gerir um pacote de acesso existente](entitlement-management-access-package-edit.md)
- [Criar e gerir um catálogo](entitlement-management-catalog-create.md)
