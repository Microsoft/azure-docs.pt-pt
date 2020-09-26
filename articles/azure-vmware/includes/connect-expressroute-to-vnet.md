---
title: Conecte o ExpressRoute ao gateway de rede virtual
description: Passos para ligar o ExpressRoute ao portal de rede virtual.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: cc17de4ad9c16100a75d5ca2527b8cb0e7eef628
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254789"
---
<!-- Used in avs-deployment.md and tutorial-configure-networking.md -->

1. Navegue para a nuvem privada que criou no tutorial anterior e selecione **Conectividade** em **Manage**, selecione o **separador ExpressRoute.**

1. Copie a chave de autorização. Se não houver uma chave de autorização, tem de criar uma, selecione **+ Solicite uma chave de autorização.**

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Copie a chave de autorização. Se não houver uma chave de autorização, precisa de criar uma, selecione + Solicite uma chave de autorização." border="true":::

1. Navegue para o Gateway de Rede Virtual que criou no passo anterior e em **Definições**, selecione **Connections**. Na página **'Ligações',** selecione **+ Adicionar**.

1. Na página **de ligação Adicionar,** forneça valores para os campos e selecione **OK**. 

   | Campo | Valor |
   | --- | --- |
   | **Nome**  | Introduza um nome para a ligação.  |
   | **Tipo de ligação**  | Selecione **ExpressRoute**.  |
   | **Autorização de redentor**  | Certifique-se de que esta caixa está selecionada.  |
   | **Gateway de rede virtual** | A porta de entrada da Rede Virtual que criou anteriormente.  |
   | **Chave de autorização**  | Copie e cole a chave de autorização do separador ExpressRoute para o seu Grupo de Recursos. |
   | **Circuito de pares URI**  | Copie e cole o ID ExpressRoute do separador ExpressRoute para o seu Grupo de Recursos.  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Na página de ligação Adicionar, forneça valores para os campos e selecione OK." border="true":::

A ligação entre o seu circuito ExpressRoute e a sua Rede Virtual é criada.