---
title: Utilize modelos de aplicação em Azure IoT Central [ Central De Azure IoT ] Microsoft Docs
description: Como operador, como utilizar os conjuntos de dispositivos na sua aplicação Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3cc6f82676f426240fba4cc4910246073aa9a556
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982465"
---
# <a name="use-application-templates"></a>Utilizar modelos de aplicação

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como gestor de soluções, criar e utilizar modelos de aplicação.

Quando você cria uma aplicação Azure IoT Central, você tem uma escolha de modelos de amostra incorporados. Também pode criar os seus próprios modelos de aplicação a partir de aplicações IoT Central existentes. Em seguida, pode utilizar os seus próprios modelos de aplicação quando criar novas aplicações.

Quando cria um modelo de aplicação, inclui os seguintes itens da sua aplicação existente:

- O painel de aplicações predefinido, incluindo o layout do painel de instrumentos e todos os azulejos que definiu.
- Modelos de dispositivo, incluindo medições, configurações, propriedades, comandos e painel de instrumentos.
- As regras. Todas as definições de regras estão incluídas. No entanto, as ações, exceto as ações de e-mail, não estão incluídas.
- Conjuntos de dispositivos, incluindo as suas condições e tabliers.

> [!WARNING]
> Se um dashboard inclui azulejos que exibem informações sobre dispositivos específicos, então esses azulejos mostram **que o recurso solicitado não foi encontrado** na nova aplicação. Tem de reconfigurar estes azulejos para exibir informações sobre dispositivos na sua nova aplicação.

Quando cria um modelo de aplicação, não inclui os seguintes itens:

- Dispositivos
- Utilizadores
- Definições de emprego
- Definições contínuas de exportação de dados

Adicione estes itens manualmente a quaisquer aplicações criadas a partir de um modelo de aplicação.

## <a name="create-an-application-template"></a>Criar um modelo de aplicação

Para criar um modelo de aplicação a partir de uma aplicação IoT central existente:

1. Vá à secção **de Administração** na sua candidatura.
1. Selecione a exportação do modelo de **aplicação.**
1. Na página de exportação do modelo de **aplicação,** introduza um nome e descrição para o seu modelo.
1. Selecione o botão **Export** para criar o modelo de aplicação. Agora pode copiar o **Link Partilhável** que permite a alguém criar uma nova aplicação a partir do modelo:

![Criar um modelo de aplicação](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Use um modelo de aplicação

Para utilizar um modelo de aplicação para criar uma nova aplicação IoT Central, precisa de um **Link Partilhável**previamente criado. Colhe o **Link Compartilhável** na barra de endereços do seu navegador. A **Criação de uma** página de aplicação exibe com o seu modelo de aplicação personalizado selecionado:

![Criar uma aplicação a partir de um modelo](media/howto-use-app-templates/create-app.png)

Selecione o seu plano de preços e preencha os outros campos no formulário. Em seguida, selecione **Criar** para criar uma nova aplicação IoT Central a partir do modelo de aplicação.

## <a name="manage-application-templates"></a>Gerir modelos de aplicação

Na página de exportação do modelo de **aplicação,** pode eliminar ou atualizar o modelo de aplicação.

Se eliminar um modelo de aplicação, já não pode utilizar o link partilhável anteriormente gerado para criar novas aplicações.

Para atualizar o seu modelo de aplicação, altere o nome do modelo ou descrição na página de exportação do modelo de **aplicação.** Em seguida, selecione novamente o botão **Export.** Esta ação gera um novo **link Partilhável** e invalida qualquer URL de **ligação partilhável** anterior.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar modelos de aplicação, o próximo passo sugerido é aprender a [gerir a IoT Central a partir do portal Azure](howto-manage-iot-central-from-portal.md)
