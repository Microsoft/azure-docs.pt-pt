---
title: ficheiro de inclusão
titleSuffix: Azure
description: ficheiro de inclusão
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81680936"
---
Esta secção descreve como executar as seguintes operações de modificação para o espreguio direto.

### <a name="add-exchange-peering-connections"></a>Adicionar ligações de persituo de troca

1. Selecione o botão **+ Adicionar ligações** e configuure uma nova ligação de espreitar.
    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de peering](../media/setup-exchange-modify-addconnection.png)
1. Preencha o formulário **de Ligação de Persimento de Troca** e selecione **Guardar**. Para ajudar a configurar uma ligação de espreitamento, reveja os passos na secção "Criar e providenciar um olhar direto".
    > [!div class="mx-imgBorder"]
    > ![Formulário de Conexão de Permuta](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Remover ligações de persimento de troca

1. Selecione uma ligação de esprevamento que pretende eliminar e, em seguida, selecione **...**  >  **Eliminar a ligação**.
    > [!div class="mx-imgBorder"]
    > ![Eliminar botão de ligação](../media/setup-exchange-modify-deleteconnection.png)
1. Introduza o ID de recurso na caixa **'Eliminar' e** selecione **Eliminar**.
    > [!div class="mx-imgBorder"]
    > ![Eliminar confirmação](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Adicione uma sessão IPv4 ou IPv6 em ligações Ativas

1. Selecione uma ligação de esprevamento que pretende modificar e, em seguida, selecione **...**  >  **Editar a ligação**.
    > [!div class="mx-imgBorder"]
    > ![Editar botão de conexão](../media/setup-exchange-modify-editconnection.png)
1. Adicione **informações de endereço IPv4** ou **endereço IPv6** e selecione **Guardar**.
    > [!div class="mx-imgBorder"]
    > ![Alterações de conexão de perso](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Remover uma sessão IPv4 ou IPv6 em ligações ativas

A remoção de uma sessão IPv4 ou IPv6 de uma ligação existente não é suportada atualmente no portal. Para mais informações, contacte [a Microsoft a espreitar.](mailto:peeringexperience@microsoft.com)