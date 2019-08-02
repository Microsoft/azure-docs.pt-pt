---
title: Solicitar acesso a um pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia) – Azure Active Directory
description: Saiba como usar o portal meu acesso para solicitar acesso a um pacote do Access no gerenciamento de direitos Azure Active Directory (versão prévia).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64a81a871d811c68f5e92822ca88446966be4d5c
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678120"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Solicitar acesso a um pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-the-my-access-portal"></a>Entrar no meu portal de acesso

A primeira etapa é entrar no portal meu acesso, em que você pode solicitar acesso a um pacote do Access.

**Função de pré-requisito:** Requerente

1. Procure um email ou uma mensagem do projeto ou gerente de negócios com o qual você está trabalhando. O email deve incluir um link para o pacote de acesso ao qual você precisará de acesso. O link será iniciado com:

    `https://myaccess.microsoft.com`

1. Abra o link.

1. Entre no portal meu acesso.

    Certifique-se de usar sua conta organizacional (corporativa ou de estudante). Se você não tiver certeza, verifique com seu projeto ou gerente de negócios.

## <a name="request-an-access-package"></a>Solicitar um pacote de acesso

Depois de encontrar o pacote de acesso no portal meu acesso, você pode enviar uma solicitação.

**Função de pré-requisito:** Requerente

1. Localize o pacote de acesso na lista.  Se necessário, você pode pesquisar digitando uma cadeia de caracteres de pesquisa e, em seguida, selecionando o **nome**, o **Catálogo**ou o filtro de **recursos** .

    ![Meu Portal de acesso – pesquisa de recursos](./media/entitlement-management-request-access/elm-myaccess-resource-search.png)
1. Clique na marca de seleção para selecionar o pacote de acesso.

    ![Meu Portal de acesso-pacotes de acesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Clique em **solicitar acesso** para abrir o painel solicitar acesso.

1. Se a caixa de justificativa de **negócios** for exibida, digite uma justificativa para a necessidade de acesso.

1. Se **a solicitação de período específico** for habilitada, selecione **Sim** ou **não**.

1. Se necessário, especifique a data de início e a data de término.

    ![Meu Portal de acesso-solicitar acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Quando terminar, clique em **Enviar** para enviar sua solicitação.

1. Clique em **histórico de solicitações** para ver uma lista de suas solicitações e o status.

    Se o pacote de acesso exigir aprovação, a solicitação estará agora em um estado de aprovação pendente.

## <a name="cancel-a-request"></a>Cancelar um pedido

Se você enviar uma solicitação de acesso e a solicitação ainda estiver no estado de **aprovação pendente** , você poderá cancelar a solicitação.

**Função de pré-requisito:** Requerente

1. No portal meu acesso, à esquerda, clique em **histórico de solicitações** para ver uma lista de suas solicitações e o status.

1. Clique no link **Exibir** para a solicitação que você deseja cancelar.

1. Se a solicitação ainda estiver no estado de **aprovação pendente** , você poderá clicar em **Cancelar solicitação** para cancelar a solicitação.

    ![Meu Portal de acesso-Cancelar solicitação](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Clique em **histórico de solicitações** para confirmar que a solicitação foi cancelada.

## <a name="select-a-policy"></a>Selecionar uma política

Se você estiver solicitando acesso a um pacote de acesso que tenha várias políticas aplicáveis, você poderá ser solicitado a selecionar uma política. Por exemplo, um Gerenciador de pacotes do Access pode configurar um pacote de acesso com duas políticas para dois grupos de funcionários internos. A primeira política pode permitir o acesso por 60 dias e exigir aprovação. A segunda política pode permitir o acesso por 2 dias e não requer aprovação. Se você encontrar esse cenário, deverá selecionar a política que deseja usar.

**Função de pré-requisito:** Requerente

## <a name="next-steps"></a>Passos Seguintes

- [Aprovar ou negar solicitações de acesso](entitlement-management-request-approve.md)
- [Solicitar notificações de processo e email](entitlement-management-process.md)
