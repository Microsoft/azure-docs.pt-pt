---
title: Adicionar destinatários em Azure Data Share
description: Saiba como adicionar destinatários a uma partilha de dados existente na Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680633"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>Como adicionar um destinatário à sua parte

Pode adicionar o destinatário quando criar uma nova parte ou a uma parte existente. A partir do Azure Data Share UI, pode adicionar o destinatário usando o e-mail de login do utilizador.  A partir da API, você pode usar uma combinação de user/service principal e iD inquilino. Quando uma identificação do inquilino é especificada, o convite só pode ser aceite neste inquilino. Também a partir da API, pode criar um convite sem enviar um e-mail ao destinatário. 

## <a name="add-recipient-to-an-existing-share"></a>Adicione o destinatário a uma parte existente

Em Azure Data Share, navegue para a sua partilha enviada e selecione o **separador Convites.** Aqui estão listados todos os destinatários dos convites para esta partilha de dados. Para adicionar um novo, clique em **Adicionar Destinatário**.

![A screenshot mostra adicionar o destinatário selecionado.](./media/how-to/how-to-add-recipients/add-recipient.png)

Será apresentado um painel no lado direito da página. Clique em **Adicionar Destinatário** e, em seguida, preencha o e-mail do seu novo destinatário na linha em branco. Certifique-se de que utiliza o e-mail de login do destinatário (usando o seu pseudónimo de e-mail não funciona). 

![A screenshot mostra o painel Add Recipient onde pode adicionar e enviar convite.](./media/how-to/how-to-add-recipients/add-recipient-side.png)

Clique **em Adicionar e envie convite.** Os novos destinatários serão enviados e-mails de convite para esta parte.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [apagar um convite para uma partilha.](how-to-delete-invitation.md)
