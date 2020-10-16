---
title: Experiência de utilizador de súmis por SMS para número de telefone (pré-visualização) - Azure AD
description: Saiba mais sobre a experiência do utilizador de sing-in POR SMS para números de telefone novos ou existentes
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: a4dcb6774990c03ce3d55fb6c519b0600a1086f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536890"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Use o seu número de telefone como nome de utilizador (pré-visualização)

Registar um dispositivo dá acesso ao seu telefone aos serviços da sua organização e não permite que a sua organização tenha acesso ao seu telemóvel. Se for administrador, pode encontrar mais informações em [Configurar e ativar a autenticação baseada](../authentication/howto-authentication-sms-signin.md)em SMS.

Se a sua organização não tiver disponibilizado o sing-in por SMS, não verá uma opção para ele ao registar um telefone na sua conta.  

## <a name="when-you-have-a-new-phone-number"></a>Quando se tem um novo número de telefone

Se receber um novo telefone ou um novo número e o registar numa organização para a qual está disponível o sing-in POR SMS, experimenta o processo normal de registo do telefone:

1. Selecione **O método adicionar**.
1. Selecione **Telefone**.
1. Introduza o número de telefone e **selecione Envie-me um código**.
1. Depois de introduzir o código, selecione **Seguinte**.
1. Verá uma solicitação que diz "SMS verificado. O seu telemóvel foi registado com sucesso."

> [!Important]
> Devido a um problema conhecido na pré-visualização, por um curto período de tempo adicionando o número de telefone não registará o número de sing-in POR SMS. Terá de iniciar sing com o número adicional e, em seguida, seguir as indicações para registar o número de sing-in por SMS.

### <a name="when-the-phone-number-is-in-use"></a>Quando o número de telefone estiver a ser utilizado

Se tentar usar um número de telefone que outra pessoa da sua organização está a usar, verá a seguinte mensagem:

![Mensagem de erro quando o seu número de telefone já está usado](media/sms-sign-in-explainer/sms-sign-in-error.png)

Contacte o seu administrador para remediar o problema.

## <a name="when-you-have-an-existing-number"></a>Quando se tem um número existente

Se já estiver a utilizar um número de telefone com uma organização e utilizar o seu número de telefone à medida que um nome de utilizador estiver disponível, os seguintes passos podem ajudá-lo a iniciar sôr-se.

1. Quando o sing-in POR SMS está disponível, é apresentado um banner perguntando se gostaria de ativar o número de telefone para o sôm-in POR SMS:

    :::image type="content" source="media/sms-sign-in-explainer/sms-sign-in-banner.png" alt-text="Screenshot que mostra o banner para ativar o sing-in DE SMS para um número de telefone com a ação 'Enable' selecionada." lightbox="media/sms-sign-in-explainer/sms-sign-in-banner.png":::

1. Além disso, aparece um botão **Enable** se selecionar o cuidado no azulejo do método do telefone:

    [![Banner para ativar o sing-in POR SMS para um número de telefone.](media/sms-sign-in-explainer/sms-sign-in-phone-method.png)](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Para ativar o método, selecione **Ativar**. É-lhe pedido que confirme a ação:

    ![Diálogo de confirmação para ativar o sing-in de SMS para um número de telefone](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Selecione **Ativar**.

## <a name="when-you-remove-your-phone-number"></a>Quando remover o seu número de telefone

1. Para eliminar o número de telefone, selecione o botão de exclusão no telha do método de inscrição de SMS.

    [![Banner para eliminar o sing-in POR SMS para um número de telefone.](media/sms-sign-in-explainer/sms-sign-in-delete-method.png)](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. Quando for solicitado para confirmar a ação, selecione **OK**.

Não é possível remover um número de telefone que esteja a ser utilizado como método de inscrição padrão. Para remover o número, terá de alterar o método de inscrição predefinido e, em seguida, retirar novamente o número de telefone.
