---
title: Conecte o ExpressRoute ao gateway de rede virtual
description: Passos para ligar o ExpressRoute ao portal de rede virtual.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 214ef9c01193b238c8e456ef2809f7a2edbdb6c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598180"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Navegue para a nuvem privada que criou no tutorial anterior e selecione **Conectividade** em **Manage**, selecione o **separador ExpressRoute.**

1. Copie a chave de autorização. Se não houver uma chave de autorização, tem de criar uma, selecione **+ Solicite uma chave de autorização.**

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Copie a chave de autorização. Se não houver uma chave de autorização, precisa de criar uma, selecione + Solicite uma chave de autorização." border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::

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

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Copie a chave de autorização. Se não houver uma chave de autorização, precisa de criar uma, selecione + Solicite uma chave de autorização." border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

A ligação entre o seu circuito ExpressRoute e a sua Rede Virtual é criada.