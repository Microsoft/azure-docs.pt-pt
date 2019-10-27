---
title: Usar modelos de aplicativos no Azure IoT Central | Microsoft Docs
description: Como um operador, como usar conjuntos de dispositivos em seu aplicativo IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d682c49aa833b9e11dbbddc5e9f6afd52cbb6e84
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952878"
---
# <a name="use-application-templates"></a>Utilizar modelos de aplicação

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como um Gerenciador de soluções, criar e usar modelos de aplicativos.

Ao criar um aplicativo de IoT Central do Azure, você tem a opção de modelos de exemplo internos. Você também pode criar seus próprios modelos de aplicativos de aplicativos IoT Central existentes. Você pode usar seus próprios modelos de aplicativo ao criar novos aplicativos.

Quando você cria um modelo de aplicativo, ele inclui os seguintes itens de seu aplicativo existente:

- O painel do aplicativo padrão, incluindo o layout do painel e todos os blocos que você definiu.
- Modelos de dispositivo, incluindo medições, configurações, propriedades, comandos e painel.
- Regras. Todas as definições de regra estão incluídas. No entanto, as ações, exceto as ações de email, não são incluídas.
- Conjuntos de dispositivos, incluindo suas condições e painéis.

> [!WARNING]
> Se um painel inclui blocos que exibem informações sobre dispositivos específicos, esses blocos mostram que **o recurso solicitado não foi encontrado** no novo aplicativo. Você deve reconfigurar esses blocos para exibir informações sobre dispositivos em seu novo aplicativo.

Quando você cria um modelo de aplicativo, ele não inclui os seguintes itens:

- Dispositivos
- Utilizadores
- Definições de trabalho
- Definições de exportação de dados contínuas

Adicione esses itens manualmente a qualquer aplicativo criado a partir de um modelo de aplicativo.

## <a name="create-an-application-template"></a>Criar um modelo de aplicativo

Para criar um modelo de aplicativo a partir de um aplicativo de IoT Central existente:

1. Vá para a seção **Administração** em seu aplicativo.
1. Selecione **Exportar modelo de aplicativo**.
1. Na página **exportar do modelo de aplicativo** , insira um nome e uma descrição para o modelo.
1. Selecione o botão **Exportar** para criar o modelo de aplicativo. Agora você pode copiar o **link compartilhável** que permite que alguém crie um novo aplicativo a partir do modelo:

![Criar um modelo de aplicativo](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Usar um modelo de aplicativo

Para usar um modelo de aplicativo para criar um novo aplicativo IoT Central, você precisa de um **link compartilhável**criado anteriormente. Cole o **link compartilhável** na barra de endereços do navegador. A página **criar um aplicativo** é exibida com o modelo de aplicativo personalizado selecionado:

![Criar um aplicativo com base em um modelo](media/howto-use-app-templates/create-app.png)

Selecione seu plano de pagamento e preencha os outros campos no formulário. Em seguida, selecione **criar** para criar um novo aplicativo IOT central no modelo de aplicativo.

## <a name="manage-application-templates"></a>Gerenciar modelos de aplicativos

Na página **exportar do modelo de aplicativo** , você pode excluir ou atualizar o modelo de aplicativo.

Se você excluir um modelo de aplicativo, não poderá mais usar o link compartilhável gerado anteriormente para criar novos aplicativos.

Para atualizar o modelo de aplicativo, altere o nome ou a descrição do modelo na página **exportar do modelo de aplicativo** . Em seguida, selecione o botão **Exportar** novamente. Essa ação gera um novo **link compartilhável** e invalida qualquer URL de **link compartilhável** anterior.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a usar modelos de aplicativos, a próxima etapa sugerida é aprender a [gerenciar IOT central da portal do Azure](howto-manage-iot-central-from-portal.md)
