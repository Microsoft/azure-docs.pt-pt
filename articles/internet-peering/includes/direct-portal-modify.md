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
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775345"
---
Esta secção descreve como realizar as seguintes operações de modificação para o peering direto:

### <a name="add-direct-peering-connections"></a>Adicionar ligações de peering direto
1. Clique no botão **+ Adicionar ligações** na parte superior e configurar uma nova ligação de pares.
    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de observação de recursos](../media/setup-direct-modify-addconnection.png)
1. Preencha o formulário de ligação de **pares diretos** e clique em **Guardar**. Para ajudar a configurar uma ligação de pares, reveja os passos em "Criar e fornecer um peering direto" acima.
    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de observação de recursos](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Remover ligações de peering direto

A remoção de uma ligação não é suportada atualmente no portal. Contacte [o peering da Microsoft](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou desvalorizar largura de banda em ligações ativas
1. Clique numa ligação de observação que pretende modificar e, em seguida, clique no **...**  > Editar botão de **ligação.**
    > [!div class="mx-imgBorder"]
    > ![Edição de ligação de pares](../media/setup-direct-modify-editconnection.png)
1. Modifique a largura de banda como mostrado abaixo e, em seguida, clique em **Guardar**.
    > [!div class="mx-imgBorder"]
    > ![Peering Connection Modificar largura de banda](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Adicione a sessão IPv4/IPv6 em ligações ativas.
1. Clique numa ligação de observação que pretende modificar e, em seguida, clique no **...**  > Editar botão de **ligação** como mostrado acima.
1. Adicione a **pré-fixação da sessão IPv4** ou **a pré-fixação iPv6** da sessão e clique em **Guardar**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Remova a sessão IPv4/IPv6 sobre ligações ativas.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
