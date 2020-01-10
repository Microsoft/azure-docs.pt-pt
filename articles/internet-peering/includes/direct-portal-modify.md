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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775345"
---
Esta seção descreve como executar as seguintes operações de modificação para o emparelhamento direto:

### <a name="add-direct-peering-connections"></a>Adicionar conexões de emparelhamento direto
1. Clique no botão **+ Adicionar conexões** na parte superior e configure uma nova conexão de emparelhamento.
    > [!div class="mx-imgBorder"]
    > ![exibição de recurso de emparelhamento](../media/setup-direct-modify-addconnection.png)
1. Preencha o formulário de **conexão de emparelhamento direto** e clique em **salvar**. Para obter ajuda com a configuração de uma conexão de emparelhamento, examine as etapas na seção "criar e provisionar um emparelhamento direto" acima.
    > [!div class="mx-imgBorder"]
    > ![exibição de recurso de emparelhamento](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Remover conexões de emparelhamento direto

Não há suporte para a remoção de uma conexão no portal no momento. Entre em contato com [o emparelhamento da Microsoft](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou fazer downgrade da largura de banda em conexões ativas
1. Clique em uma conexão de emparelhamento que você deseja modificar e, em seguida, clique no botão **...**  > **Editar conexão** .
    > [!div class="mx-imgBorder"]
    > ![a edição de conexão de emparelhamento](../media/setup-direct-modify-editconnection.png)
1. Modifique a largura de banda conforme mostrado abaixo e clique em **salvar**.
    > [!div class="mx-imgBorder"]
    > ![conexão de emparelhamento modificar largura de banda](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Adicionar sessão IPv4/IPv6 em conexões ativas.
1. Clique em uma conexão de emparelhamento que você deseja modificar e, em seguida, clique no botão **...**  > **Editar conexão** , conforme mostrado acima.
1. Adicione as informações de prefixo IPv4 da sessão ou **prefixo** **IPv6** da sessão e clique em **salvar**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Remova a sessão IPv4/IPv6 em conexões ativas.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
