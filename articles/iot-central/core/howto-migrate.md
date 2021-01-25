---
title: Migrar uma aplicação V2 Azure IoT Central para V3 | Microsoft Docs
description: Como administrador, aprenda a migrar a sua aplicação V2 Azure IoT Central para V3
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3f81ae72af48ec934d1c2c2567ebdd212d8e0499
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763465"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>Migrar a sua aplicação V2 IoT Central para V3

*Este artigo aplica-se aos administradores.*

Atualmente, quando se cria uma nova aplicação IoT Central, é uma aplicação V3. Se criou previamente uma aplicação, então dependendo de quando a criou, pode ser V2. Este artigo descreve como migrar um V2 para uma aplicação V3 para ter certeza de que está a usar as mais recentes funcionalidades do IoT Central.

Para saber como identificar a versão de uma aplicação IoT Central, consulte [Sobre a sua aplicação.](howto-get-app-info.md)

Os passos para migrar uma aplicação de V2 para V3 são:

1. Crie uma nova aplicação V3 a partir da aplicação V2.
1. Configure a aplicação V3.
1. Apague para a aplicação V2.

## <a name="create-a-new-v3-application"></a>Criar uma nova aplicação V3

Utilize o assistente de migração para criar uma nova aplicação V3.

A IoT Central não suporta a migração para uma aplicação V3 existente. Para mover automaticamente os dispositivos existentes, utilize o assistente de migração para criar a sua aplicação V3.

O assistente de migração:

- Cria uma nova aplicação V3.
- Verifique os modelos do seu dispositivo para obter compatibilidade com v3.
- Copia todos os modelos do seu dispositivo para a nova aplicação V3.
- Copia todos os utilizadores e atribuições de funções para a nova aplicação V3.

> [!NOTE]
> Para garantir a compatibilidade do dispositivo com v3, os tipos primitivos do modelo do dispositivo tornam-se propriedades de objetos. Não precisa de fazer alterações ao código do dispositivo.

Deve ser um administrador para migrar uma aplicação para v3.

1. No menu **Administração,** **selecione Migrar para uma aplicação V3**:

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="Screenshot que mostra o assistente de migração da aplicação":::

1. Introduza um novo **nome de aplicação** e altere opcionalmente o  **URL** autogerado . O URL não pode ser o mesmo que o URL da sua aplicação V2 atual. No entanto, pode alterar o URL mais tarde depois de eliminar a sua aplicação V2.

1. Selecione **Criar uma nova aplicação V3**.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="Screenshot que mostra as opções no assistente de migração da aplicação":::

    Dependendo do número e complexidade dos modelos do seu dispositivo, este processo pode demorar vários minutos a ser concluído.

    > [!Warning]
    > A criação da sua aplicação V3 falhará se algum modelo de dispositivo tiver campos que comecem com um número ou contenham qualquer um dos seguintes caracteres ( `+` , `*` `?` `^` `$` `(` `)` `[` `]` `{` `}` `|` `\` O esquema do modelo do dispositivo DTDL que as aplicações V3 utilizam não permite estes caracteres. Atualize o modelo do seu dispositivo para resolver este problema antes de migrar para V3.

1. Quando a sua nova aplicação V3 estiver pronta, selecione **Abra a sua nova aplicação** para abri-la.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="Screenshot que mostra o assistente de migração da aplicação após a migração da aplicação":::

## <a name="configure-the-v3-application"></a>Configure a aplicação V3

Depois da criação da nova aplicação V3, escote quaisquer alterações de configuração antes de mover os seus dispositivos da aplicação V2 para a aplicação V3.

> [!TIP]
> Tenha um momento para [se familiarizar com a V3,](overview-iot-central-tour.md#navigate-your-application) pois tem algumas diferenças em parte da versão anterior.

Aqui estão alguns passos de configuração recomendados a considerar:

- [Configure painéis](howto-add-tiles-to-your-dashboard.md)
- [Configure exportação de dados](howto-export-data.md)
- [Configurar regras e ações](quick-configure-rules.md)
- [Personalize a UI da aplicação](howto-customize-ui.md)

Quando a sua aplicação V3 estiver configurada para satisfazer as suas necessidades, está pronto para mover os seus dispositivos da sua aplicação V2 para a sua aplicação V3.

## <a name="move-your-devices-to-the-v3-application"></a>Mova os seus dispositivos para a aplicação V3

Quando este passo estiver completo, todos os seus dispositivos comunicam apenas com a sua nova aplicação V3.

> [!IMPORTANT]
> Antes de mover os seus dispositivos para a sua aplicação V3, elimine todos os dispositivos que tenha criado na aplicação V3.

Este passo move todos os dispositivos existentes para a sua nova aplicação V3. Os dados do seu dispositivo não são copiados.

Selecione **Moça todos os dispositivos** para começar a mover os seus dispositivos. Este passo pode levar vários minutos para ser concluído.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="Screenshot que mostra o assistente de migração de aplicações com a opção dispositivos de movimento":::

Depois de concluído o movimento, reinicie todos os seus dispositivos para garantir que se ligam à nova aplicação V3.

> [!WARNING]
> Não apague a sua aplicação V3, uma vez que a sua aplicação V2 está agora inoperável.

## <a name="delete-your-old-v2-application"></a>Apague a sua antiga aplicação V2

Depois de validar que tudo funciona como esperado na sua nova aplicação V3, apague a sua antiga aplicação V2. Este passo garante que não é cobrado por uma aplicação que já não utiliza.

> [!Note]
> Para eliminar uma aplicação, tem de ter permissões para eliminar recursos na subscrição Azure que escolheu quando criou a aplicação. Para saber mais, consulte [o controlo de acesso baseado em funções para gerir o acesso aos seus recursos de subscrição Azure.](../../active-directory/role-based-access-control-configure.md)

1. Na sua aplicação V2, selecione o separador **Administração** no menu
2. **Selecione Eliminar** para eliminar permanentemente a sua aplicação IoT Central. Esta opção elimina permanentemente todos os dados associados a esta aplicação.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu a migrar a sua aplicação, o próximo passo sugerido é rever a [UI Central Azure IoT](overview-iot-central-tour.md) para ver o que mudou na V3.
