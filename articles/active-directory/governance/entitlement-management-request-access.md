---
title: Solicite um pacote de acesso - Azure AD gestão de direitos
description: Saiba como utilizar o portal My Access para solicitar o acesso a um pacote de acesso na gestão de direitos do Diretório Ativo Azure.
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1ccde2f1f92237978ab4e68acaa26393bbb9d8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261752"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Solicitar acesso a um pacote de acesso na gestão de direitos da AD Azure

Com a gestão de direitos da Azure AD, um pacote de acesso permite uma configuração única de recursos e políticas que administraautomaticamente o acesso para a vida útil do pacote de acesso. 

Um gestor de pacotes de acesso pode configurar políticas para exigir aprovação para os utilizadores terem acesso a pacotes de acesso. Um utilizador que necessite de acesso a um pacote de acesso pode submeter um pedido para ter acesso. Este artigo descreve como submeter um pedido de acesso.

## <a name="sign-in-to-the-my-access-portal"></a>Inscreva-se no portal My Access

O primeiro passo é iniciar sessão no portal My Access onde pode solicitar acesso a um pacote de acesso.

**Papel pré-requisito:** Requestor

1. Procure um e-mail ou uma mensagem do projeto ou gestor de negócios com quem está a trabalhar. O e-mail deve incluir um link para o pacote de acesso a que terá acesso. O link começa com `myaccess`, inclui uma dica de diretório, e termina com um id de pacote de acesso.  (Para o Governo dos EUA, o domínio pode ser `https://myaccess.microsoft.us` em vez disso.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Abra o link.

1. Inscreva-se no portal My Access.

    Certifique-se de que utiliza a sua conta organizacional (trabalho ou escola). Se não tiver a certeza, consulte o seu projeto ou gestor de negócios.

## <a name="request-an-access-package"></a>Solicite um pacote de acesso

Depois de ter encontrado o pacote de acesso no portal My Access, pode submeter um pedido.

**Papel pré-requisito:** Requestor

1. Encontre o pacote de acesso na lista.  Se necessário, pode pesquisar digitando uma cadeia de pesquisa e, em seguida, selecionando o **filtro Nome,** **Catálogo**ou **Recursos.**

    ![Meu portal de acesso - Pesquisa de recursos](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Clique na marca de verificação para selecionar o pacote de acesso.

1. Clique em **Solicitar acesso** para abrir o painel de acesso do Pedido.

    ![Meu portal de acesso - Pacotes de acesso](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Se a caixa de **justificação do Negócio** for apresentada, escreva uma justificação para necessitar de acesso.

1. Se **o Pedido para um período específico estiver** ativado, selecione **Sim** ou **Não**.

1. Se necessário, especifique a data de início e a data de fim.

    ![Meu portal de acesso - Solicitar acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Quando terminar, clique em **Submeter** para submeter o seu pedido.

1. Clique no **histórico de solicitar** para ver uma lista dos seus pedidos e o estado.

    Se o pacote de acesso necessitar de aprovação, o pedido encontra-se agora em estado de aprovação pendente.

### <a name="select-a-policy"></a>Selecionar uma política

Se solicitar o acesso a um pacote de acesso que tenha várias políticas aplicáveis, poderá ser-lhe pedido que selecione uma apólice. Por exemplo, um gestor de pacotes de acesso pode configurar um pacote de acesso com duas políticas para dois grupos de funcionários internos. A primeira política pode permitir o acesso por 60 dias e exigir aprovação. A segunda política pode permitir o acesso por 2 dias e não exigir aprovação. Se encontrar este cenário, deve selecionar a política que pretende utilizar.

![Meu portal de acesso - Solicitar acesso - múltiplas políticas](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>Reenviar um pedido

Quando solicita o acesso a um pacote de acesso, o seu pedido pode ser negado ou o seu pedido pode expirar se os aprovadores não responderem a tempo. Se precisar de acesso, pode tentar novamente e reenviar o seu pedido. O seguinte procedimento explica como reenviar um pedido de acesso:

**Papel pré-requisito:** Requestor

1. Inscreva-se no portal **My Access.**

1. Clique no histórico de **pedido** do menu de navegação para a esquerda.

1. Encontre o pacote de acesso para o qual está a reenviar um pedido.

1. Clique na marca de verificação para selecionar o pacote de acesso.

1. Clique no link azul **Ver** à direita do pacote de acesso selecionado.
    
    ![Selecione pacote de acesso e ver link](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Um painel abrirá à direita com o histórico de pedidos para o pacote de acesso.
    
    ![Selecione botão de reenviar](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Clique no botão **De reenviar** na parte inferior do painel.

## <a name="cancel-a-request"></a>Cancelar um pedido

Se submeter um pedido de acesso e o pedido ainda estiver no estado **de aprovação pendente,** pode cancelar o pedido.

**Papel pré-requisito:** Requestor

1. No portal My Access, à esquerda, clique no **histórico de solicitar** para ver uma lista dos seus pedidos e o estado.

1. Clique no link **'Ver'** para o pedido que pretende cancelar.

1. Se o pedido ainda estiver no estado **de aprovação pendente,** pode clicar em **Cancelar o pedido** para cancelar o pedido.

    ![Meu portal de acesso - Cancelamento de pedido](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Clique no **histórico de pedido** para confirmar que o pedido foi cancelado.

## <a name="next-steps"></a>Passos seguintes

- [Aprovar ou negar pedidos de acesso](entitlement-management-request-approve.md)
- [Processo de pedido e notificações de e-mail](entitlement-management-process.md)
