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
ms.openlocfilehash: c3212acd5015edbb639b8904b885c718d654e5b4
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356266"
---
Esta secção descreve como executar as seguintes operações de modificação para o espreguio direto.

### <a name="add-direct-peering-connections"></a>Adicionar ligações de observação direta
1. Selecione o botão **+ Adicionar ligações** e configuure uma nova ligação de espreitar.
    > [!div class="mx-imgBorder"]
    > ![A página AshburnPeering - Connections lista as ligações. Destaca-se o botão + Adicionar ligações.](../media/setup-direct-modify-addconnection.png)

1. Preencha o **formulário de Ligação de Pares Direto** e selecione **Guardar**. Para ajudar a configurar uma ligação de espreitamento, reveja os passos na secção "Criar e providenciar um olhar direto".
    > [!div class="mx-imgBorder"]
    > ![Formulário de Ligação de Pares Direto](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Remover ligações de observação direta

A remoção de uma ligação não é suportada atualmente no portal Azure. Para mais informações, contacte [a Microsoft a espreitar.](mailto:peeringexperience@microsoft.com)

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou desclassificar largura de banda em ligações Ativas
1. Selecione uma ligação de esprevamento que pretende modificar e, em seguida, selecione **...**  >  **Editar a ligação**.
    > [!div class="mx-imgBorder"]
    > ![Editar ligação](../media/setup-direct-modify-editconnection.png)

1. Modifique a largura de banda movendo o slider e, em seguida, **selecione Save**.
    > [!div class="mx-imgBorder"]
    > ![Modificar a largura de banda](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Adicionar informações de sessão IPv4 ou IPv6 sobre ligações Ativas
1. Selecione uma ligação de esprevamento que pretende modificar e, em seguida, selecione **...**  >  **Editar a ligação** como mostrado no passo 1.
1. Introduza as informações **do prefixo IPv4** da sessão ou **do prefixo do Sessão IPv6** e selecione **Guardar**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Remova informações de sessão IPv4 ou IPv6 sobre ligações ativas
A remoção do **prefixo da Sessão IPv4** ou do **prefixo do Sessão IPv6** não está atualmente suportada no portal. Para mais informações, contacte [a Microsoft a espreitar.](mailto:peeringexperience@microsoft.com)
