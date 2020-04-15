---
title: Experiência de utilizador de sms para número de telefone (pré-visualização) - Azure AD
description: Saiba mais sobre a experiência do utilizador de sms para números de telefone novos ou existentes
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378837"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Utilize o seu número de telefone como nome de utilizador (pré-visualização)

Registar um dispositivo dá acesso ao seu telefone aos serviços da sua organização e não permite o acesso da sua organização ao seu telemóvel. Se for administrador, pode encontrar mais informações no [Configure e ativar os utilizadores para autenticação baseada em SMS](../authentication/howto-authentication-sms-signin.md).

Se a sua organização não disponibilizou o sms, não verá uma opção para isso ao registar um telefone com a sua conta.  

## <a name="when-you-have-a-new-phone-number"></a>Quando tiver um novo número de telefone

Se você obtém um novo telefone ou um novo número e o regista com uma organização para a qual o sms está disponível, você experimenta o processo normal de registo do telefone:

1. Selecione **adicionar método**.
1. Selecione **Telefone**.
1. Introduza o número de telefone e selecione **Envie um código por Sms.**
1. Depois de introduzir o código, selecione **Next**.
1. Verá uma solicitação que diz "SMS verificado. O seu telemóvel foi registado com sucesso."

> [!Important]
> Devido a um problema conhecido na pré-visualização, por um curto período de tempo a adição de número de telefone não registará o número de sms de inscrição. Terá de iniciar sessão com o número adicional e, em seguida, seguir as indicações para registar o número de sms de inscrição.

### <a name="when-the-phone-number-is-in-use"></a>Quando o número de telefone está a ser utilizado

Se tentar usar um número de telefone que alguém da sua organização está a usar, verá a seguinte mensagem:

![Mensagem de erro quando o seu número de telefone já está usado](media/sms-sign-in-explainer/sms-sign-in-error.png)

Contacte o seu administrador para remediar o problema.

## <a name="when-you-have-an-existing-number"></a>Quando se tem um número existente

Se já estiver a utilizar um número de telefone com uma organização e a utilizar o seu número de telefone como nome de utilizador, os seguintes passos podem ajudá-lo a iniciar sessão.

1. Quando o sms de inscrição está disponível, é apresentado um banner perguntando se deseja ativar o número de telefone para sms sms sms:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Além disso, aparece um botão **Enable** se selecionar o cuidado no azulejo do método do telefone:

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Para ativar o método, selecione **Ativar**. Foi-lhe pedido que confirmasse a ação:

    ![Diálogo de confirmação para ativar o log-in SMS para um número de telefone](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Selecione **Ativar**.

## <a name="when-you-remove-your-phone-number"></a>Quando remover o seu número de telefone

1. Para eliminar o número de telefone, selecione o botão de eliminação no toque do telefone de entrada sms.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Quando for solicitado a confirmar a ação, selecione **OK**.

Não é possível remover um número de telefone que esteja a ser utilizado como método de registo predefinido. Para remover o número, teria de alterar o método de registo predefinido e, em seguida, remover o número de telefone novamente.
