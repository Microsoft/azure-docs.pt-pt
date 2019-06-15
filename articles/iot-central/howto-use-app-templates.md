---
title: Utilizar modelos de aplicação no Azure IoT Central | Documentos da Microsoft
description: Como um operador, como utilizar o dispositivo define na sua aplicação do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a26f70c5a61f3855a3de991072a7e84103e87b69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499223"
---
# <a name="use-application-templates"></a>Utilizar modelos de aplicação

Este artigo descreve como, como um Gestor de solução, para criar e utilizar modelos de aplicativos.

Quando cria uma aplicação do Azure IoT Central, tem uma opção de modelos de exemplo incorporado. Também pode criar seus próprios modelos de aplicativos de aplicativos existentes do Centro de IoT. Em seguida, pode utilizar seus próprios modelos de aplicação ao criar novos aplicativos.

Quando cria um modelo de aplicativo, ele inclui os seguintes itens do seu aplicativo existente:

- O padrão dashboard de aplicações, incluindo o esquema do dashboard e todos os mosaicos que definiu.
- Modelos de dispositivos, incluindo medidas, definições, propriedades, comandos e o dashboard.
- Regras. Todas as definições de regras são incluídas. No entanto, ações, exceto para as ações de e-mail, não estão incluídas.
- Define o dispositivo, incluindo os respetivos condições e os dashboards.

> [!WARNING]
> Se um dashboard inclui mosaicos que apresentam informações sobre dispositivos específicos, então esses mosaicos mostram **o recurso pedido não foi encontrado** na nova aplicação. Tem de reconfigurar destes mosaicos para apresentar informações sobre os dispositivos na sua nova aplicação.

Quando cria um modelo de aplicativo, ele não inclui os seguintes itens:

- Dispositivos
- Utilizadores
- Definições de tarefa
- Definições de exportação contínua de dados

Adicione manualmente estes itens para todas as aplicações criadas a partir de um modelo de aplicação.

## <a name="create-an-application-template"></a>Criar um modelo de aplicação

Para criar um modelo de aplicação a partir de uma aplicação IoT Central existente:

1. Vá para o **administração** seção em seu aplicativo.
1. Selecione **exportação do modelo de aplicativo**.
1. Sobre o **exportação do modelo de aplicativo** página, introduza um nome e descrição para o seu modelo.
1. Selecione o **exportar** botão para criar o modelo de aplicativo. Agora, pode copiar o **Link partilhável** que permita a alguém criar um novo aplicativo a partir do modelo:

![Criar um modelo de aplicação](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Utilizar um modelo de aplicação

Para utilizar um modelo de aplicação para criar uma nova aplicação de IoT Central, terá um criado anteriormente **Link partilhável**. Colar o **Link partilhável** na barra de endereço do browser. O **criar uma aplicação** página é apresentada com o modelo de aplicativo personalizado selecionado:

![Criar uma aplicação a partir de um modelo](media/howto-use-app-templates/create-app.png)

Selecionar o seu plano de pagamento e preencher os outros campos no formulário. Em seguida, selecione **criar** para criar uma nova aplicação do Centro de IoT a partir do modelo de aplicativo.

## <a name="manage-application-templates"></a>Gerir modelos de aplicativos

Sobre o **exportação do modelo de aplicativo** página, pode eliminar ou atualizar o modelo de aplicativo.

Se eliminar um modelo de aplicação, pode deixar de utilizar a ligação partilhável gerada anteriormente para criar novos aplicativos.

Para atualizar o modelo de aplicativo, altere o nome do modelo ou a descrição sobre o **exportação do modelo de aplicativo** página. Em seguida, selecione o **exportar** novamente no botão. Esta ação gera um novo **link partilhável** e invalida qualquer anterior **ligação partilhável** URL.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar modelos de aplicativos, a próxima etapa sugerida é saber como [gerir IoT Central do portal do Azure](howto-manage-iot-central-from-portal.md)
