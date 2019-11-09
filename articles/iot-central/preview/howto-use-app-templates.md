---
title: Exportar um aplicativo de IoT Central do Azure | Microsoft Docs
description: Como gerente de soluções, quero exportar um modelo de aplicativo para poder reutilizá-lo.
author: dominicbetts
ms.author: dobett
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 81e24712de186e2d5ec03e33ecabe485cc0d9f31
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894763"
---
# <a name="export-your-application-preview-features"></a>Exportar seu aplicativo (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este artigo descreve como, como um Gerenciador de soluções, exportar um aplicativo IoT Central para poder reutilizá-lo.

Tem duas opções:

- Você pode criar uma cópia do seu aplicativo se precisar apenas criar uma cópia duplicada do seu aplicativo.
- Você pode criar um modelo de aplicativo do seu aplicativo se planeja criar várias cópias.

## <a name="copy-your-application"></a>Copiar seu aplicativo

Você pode criar uma cópia de qualquer aplicativo, menos quaisquer instâncias de dispositivo, histórico de dados do dispositivo e dados do usuário. A cópia é um aplicativo pago conforme o uso para o qual você será cobrado. Você não pode criar um aplicativo de avaliação copiando um aplicativo.

Selecione **copiar**. Na caixa de diálogo, insira os detalhes do novo aplicativo pago conforme o uso. Em seguida, selecione **copiar** para confirmar que deseja continuar. Para saber mais sobre os campos no formulário, consulte o guia de início rápido [criar um aplicativo](quick-deploy-iot-central.md) .

![Página Configurações do aplicativo](media/howto-use-app-templates/appcopy2.png)

Depois que a operação de cópia do aplicativo for realizada com sucesso, você poderá navegar para o novo aplicativo usando o link.

![Página Configurações do aplicativo](media/howto-use-app-templates/appcopy3a.png)

Copiar um aplicativo também copia a definição de regras e a ação de email. Algumas ações, como os aplicativos de fluxo e lógico, são vinculadas a regras específicas por meio da ID de regra. Quando uma regra é copiada para um aplicativo diferente, ela obtém sua própria ID de regra. Nesse caso, os usuários terão que criar uma nova ação e associar a nova regra a ela. Em geral, é uma boa ideia verificar as regras e ações para certificar-se de que elas estão atualizadas no novo aplicativo.

> [!WARNING]
> Se um painel inclui blocos que exibem informações sobre dispositivos específicos, esses blocos mostram que **o recurso solicitado não foi encontrado** no novo aplicativo. Você deve reconfigurar esses blocos para exibir informações sobre dispositivos em seu novo aplicativo.

## <a name="create-an-application-template"></a>Criar um modelo de aplicativo

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

Para criar um modelo de aplicativo a partir de um aplicativo de IoT Central existente:

1. Vá para a seção **Administração** em seu aplicativo.
1. Selecione **Exportar modelo de aplicativo**.
1. Na página **exportar do modelo de aplicativo** , insira um nome e uma descrição para o modelo.
1. Selecione o botão **Exportar** para criar o modelo de aplicativo. Agora você pode copiar o **link compartilhável** que permite que alguém crie um novo aplicativo a partir do modelo:

![Criar um modelo de aplicativo](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Usar um modelo de aplicativo

Para usar um modelo de aplicativo para criar um novo aplicativo IoT Central, você precisa de um **link compartilhável**criado anteriormente. Cole o **link compartilhável** na barra de endereços do navegador. A página **criar um aplicativo** é exibida com o modelo de aplicativo personalizado selecionado:

![Criar um aplicativo com base em um modelo](media/howto-use-app-templates/create-app.png)

Selecione seu plano de pagamento e preencha os outros campos no formulário. Em seguida, selecione **criar** para criar um novo aplicativo IOT central no modelo de aplicativo.

### <a name="manage-application-templates"></a>Gerenciar modelos de aplicativos

Na página **exportar do modelo de aplicativo** , você pode excluir ou atualizar o modelo de aplicativo.

Se você excluir um modelo de aplicativo, não poderá mais usar o link compartilhável gerado anteriormente para criar novos aplicativos.

Para atualizar o modelo de aplicativo, altere o nome ou a descrição do modelo na página **exportar do modelo de aplicativo** . Em seguida, selecione o botão **Exportar** novamente. Essa ação gera um novo **link compartilhável** e invalida qualquer URL de **link compartilhável** anterior.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a usar modelos de aplicativos, a próxima etapa sugerida é aprender a [gerenciar IOT central da portal do Azure](../core/howto-manage-iot-central-from-portal.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
