---
title: Solicitar um pacote de acesso - Gestão de direitos Azure AD
description: Saiba como utilizar o portal My Access para solicitar o acesso a um pacote de acesso na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 726507fa6ea0651b23d46424bda669a2d8ad41e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996649"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Solicitar acesso a um pacote de acesso na gestão de direitos Azure AD

Com a gestão de direitos Azure AD, um pacote de acesso permite uma configuração única de recursos e políticas que administra automaticamente o acesso à vida útil do pacote de acesso. 

Um gestor de pacotes de acesso pode configurar políticas para exigir aprovação para que os utilizadores tenham acesso a pacotes de acesso. Um utilizador que precise de acesso a um pacote de acesso pode submeter um pedido de acesso. Este artigo descreve como enviar um pedido de acesso.

## <a name="sign-in-to-the-my-access-portal"></a>Inscreva-se no portal 'O Acesso'

O primeiro passo é iniciar sedução no portal My Access onde pode solicitar acesso a um pacote de acesso.

**Papel pré-requisito:** Solicitador

1. Procure um e-mail ou uma mensagem do projeto ou gestor de negócios com quem está a trabalhar. O e-mail deve incluir um link para o pacote de acesso a que necessitará de acesso. O link começa com `myaccess` , inclui uma dica de diretório, e termina com um pacote de acesso ID.  (Para o Governo dos EUA, o domínio pode ser `https://myaccess.microsoft.us` em vez disso.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Abre a ligação.

1. Inscreva-se no portal 'Acesso'.

    Certifique-se de que usa a sua conta organizacional (trabalho ou escola). Se não tiver a certeza, consulte o seu projeto ou gestor de negócios.

## <a name="request-an-access-package"></a>Solicite um pacote de acesso

Uma vez encontrado o pacote de acesso no portal 'O Acesso', pode submeter um pedido.

**Papel pré-requisito:** Solicitador

1. Encontre o pacote de acesso na lista.  Se necessário, pode pesquisar digitando uma cadeia de pesquisa e, em seguida, selecionando o filtro **Nome,** **Catálogo** ou **Recursos.**

    ![O meu portal de Acesso - Pesquisa de recursos](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Clique na marca de verificação para selecionar o pacote de acesso.

1. Clique **em Pedir acesso** para abrir o painel de acesso do Pedido.

    ![Meu portal de acesso - Pacotes de acesso](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Se a caixa **de justificação do Negócio** for apresentada, escreva uma justificação para necessitar de acesso.

1. Se **o pedido de período específico** estiver ativado, selecione **Sim** ou **Não**.

1. Se necessário, especifique a data de início e a data de fim.

    ![O meu portal de Acesso - Solicitar acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Quando terminar, clique em **Submeter** para submeter o seu pedido.

1. Clique **em Solicitar o histórico** para ver uma lista dos seus pedidos e o estado.

    Se o pacote de acesso necessitar de aprovação, o pedido encontra-se agora num estado de aprovação pendente.

### <a name="select-a-policy"></a>Selecionar uma política

Se solicitar acesso a um pacote de acesso que tenha várias políticas que se aplicam, poderá ser-lhe pedido que selecione uma política. Por exemplo, um gestor de pacotes de acesso pode configurar um pacote de acesso com duas políticas para dois grupos de funcionários internos. A primeira política poderá permitir o acesso por 60 dias e requer aprovação. A segunda política poderá permitir o acesso durante 2 dias e não requer aprovação. Se encontrar este cenário, deve selecionar a política que pretende utilizar.

![Meu portal de acesso - Solicitar acesso - múltiplas políticas](./media/entitlement-management-request-access/my-access-multiple-policies.png)

### <a name="fill-out-requestor-information"></a>Preencha informações de solicitação

Pode solicitar acesso a um pacote de acesso que exija justificação comercial e informações adicionais de solicitação antes de lhe dar acesso ao pacote de acesso. Preencha todas as informações solicitadas necessárias para aceder ao pacote de acesso.

![Meu portal de Acesso - Solicitar acesso - Preencha informações solicitadoras](./media/entitlement-management-request-access/my-access-requestor-information.png)

## <a name="resubmit-a-request"></a>Reenviar um pedido

Quando solicita o acesso a um pacote de acesso, o seu pedido pode ser negado ou o seu pedido pode expirar se os aprovadores não responderem a tempo. Se precisar de acesso, pode tentar novamente e reenviar o seu pedido. O procedimento que se segue explica como reenviar um pedido de acesso:

**Papel pré-requisito:** Solicitador

1. Inscreva-se no portal **'Acesso'.**

1. Clique **no histórico** de pedido do menu de navegação para a esquerda.

1. Encontre o pacote de acesso para o qual está a enviar um pedido.

1. Clique na marca de verificação para selecionar o pacote de acesso.

1. Clique no link **'Ver** azul' à direita do pacote de acesso selecionado.
    
    ![Selecione pacote de acesso e vista link](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Um painel abrir-se-á à direita com o histórico de pedidos para o pacote de acesso.
    
    ![Selecione botão de resubmissão](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Clique no botão **Resubmit** na parte inferior do painel.

## <a name="cancel-a-request"></a>Cancelar um pedido

Se submeter um pedido de acesso e o pedido ainda estiver no estado **de aprovação pendente,** pode cancelar o pedido.

**Papel pré-requisito:** Solicitador

1. No portal 'O Meu Acesso', à esquerda, clique em **Solicitar o histórico** para ver uma lista dos seus pedidos e o estado.

1. Clique no link **Ver** para o pedido que pretende cancelar.

1. Se o pedido ainda estiver no estado **de aprovação pendente,** pode clicar no **pedido de Cancelamento** para cancelar o pedido.

    ![Meu portal de Acesso - Cancelar pedido](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Clique **no histórico de Pedido** para confirmar que o pedido foi cancelado.

## <a name="next-steps"></a>Passos seguintes

- [Aprovar ou negar pedidos de acesso](entitlement-management-request-approve.md)
- [Processo de pedido e notificações de e-mail](entitlement-management-process.md)
