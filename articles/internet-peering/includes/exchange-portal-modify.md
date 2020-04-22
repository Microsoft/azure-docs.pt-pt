---
title: incluir ficheiro
titleSuffix: Azure
description: incluir ficheiro
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680936"
---
Esta secção descreve como realizar as seguintes operações de modificação para o peering direto.

### <a name="add-exchange-peering-connections"></a>Adicionar ligações de persparo de troca

1. Selecione o botão **+ Adicionar ligações** e configurar uma nova ligação de pares.
    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de observação de recursos](../media/setup-exchange-modify-addconnection.png)
1. Preencha o formulário de ligação de **peering Exchange** e selecione **Guardar**. Para ajudar a configurar uma ligação de pares, reveja os passos na secção "Criar e fornecer um peering direto".
    > [!div class="mx-imgBorder"]
    > ![Formulário de ligação de peering de troca](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Remover ligações de persparo de troca

1. Selecione uma ligação de observação que pretende eliminar e, em seguida, selecione **...**  >  **Eliminar a ligação**.
    > [!div class="mx-imgBorder"]
    > ![Eliminar o botão de ligação](../media/setup-exchange-modify-deleteconnection.png)
1. Introduza o ID de recurso na caixa **Confirmar Excluir** e selecione **Eliminar**.
    > [!div class="mx-imgBorder"]
    > ![Excluir a confirmação](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Adicione uma sessão IPv4 ou IPv6 em conexões ativas

1. Selecione uma ligação de pares que pretende modificar e, em seguida, selecione **...**  >  **Editar ligação**.
    > [!div class="mx-imgBorder"]
    > ![Editar botão de ligação](../media/setup-exchange-modify-editconnection.png)
1. Adicione o **endereço IPv4** ou as informações de endereço **IPv6** e selecione **Guardar**.
    > [!div class="mx-imgBorder"]
    > ![Peering modificações de ligação](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Remova uma sessão IPv4 ou IPv6 sobre ligações ativas

A remoção de uma sessão IPv4 ou IPv6 de uma ligação existente não é suportada no portal. Para mais informações, contacte a [Microsoft a espreitar](mailto:peeringexperience@microsoft.com).