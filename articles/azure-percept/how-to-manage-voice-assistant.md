---
title: Configure aplicação de assistente de voz dentro do Azure Percept Studio
description: Configure aplicação de assistente de voz dentro do Azure Percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 5e41dea3b47c608b9e82ac57fa1cfe5247ea6cc2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099017"
---
# <a name="managing-your-voice-assistant"></a>Gerir o seu assistente de voz

Este artigo descreve como configurar a palavra-chave e os comandos da sua aplicação de assistente de voz dentro [do Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Para obter orientações sobre a configuração da sua [palavra-chave](./how-to-configure-voice-assistant.md)dentro do IoT Hub em vez do portal, consulte este artigo de como fazer .

Se ainda não criou uma aplicação de assistente de voz, consulte [Build a no-code voice assistant with Azure Percept Studio e Azure Percept Audio](./tutorial-no-code-speech.md).

## <a name="keyword-configuration"></a>Configuração de palavras-chave

Uma palavra-chave é uma palavra ou frase curta usada para ativar um assistente de voz. Por exemplo, "Hey Cortana" é a palavra-chave para o assistente cortana. A ativação por voz permite que os seus utilizadores comecem a interagir com o seu produto completamente mãos-livres, bastando para dizer a palavra-chave. À medida que o seu produto escuta continuamente a palavra-chave, todo o áudio é processado localmente no dispositivo até que ocorra uma deteção para garantir que os dados do utilizador permanecem o mais privados possível.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Configuração dentro da janela de demonstração do assistente de voz

1. Clique **em alterar** ao lado da **palavra-chave personalizada** na página de demonstração.

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="Imagem de janela de demonstração de hospitalidade.":::

    Se não tiver a página de demonstração aberta, navegue na página do dispositivo (ver abaixo) e clique em **Testar o seu assistente de voz** em **Ações** para aceder à demonstração.

1. Selecione uma das palavras-chave disponíveis e clique em **Guardar** para aplicar alterações.

1. As três luzes LED do dispositivo Azure Percept Audio mudarão para azul brilhante (sem piscar) quando a configuração estiver completa e o seu assistente de voz estiver pronto a ser utilizado.

### <a name="configuration-within-the-device-page"></a>Configuração dentro da página do dispositivo

1. Na página geral do [Azure Percept Studio,](https://go.microsoft.com/fwlink/?linkid=2135819)clique em **Dispositivos** no painel de menus esquerdo.

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="Screenshot da página geral do Azure Percept Studio com dispositivos em destaque.":::

1. Selecione o dispositivo para o qual a sua aplicação de assistente de voz foi implantada.

1. Abra a conta **de discurso.**

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="Screenshot da página do dispositivo de borda com o separador Discurso realçado.":::

1. Clique em **Alterar** ao lado da **palavra-chave**.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="Screenshot das ações de solução de fala disponíveis.":::

1. Selecione uma das palavras-chave disponíveis e clique em **Guardar** para aplicar alterações.

1. As três luzes LED do dispositivo Azure Percept Audio mudarão para azul brilhante (sem piscar) quando a configuração estiver completa e o seu assistente de voz estiver pronto a ser utilizado.

## <a name="create-a-custom-keyword"></a>Crie uma palavra-chave personalizada

Com [o Speech Studio,](https://speech.microsoft.com/)pode criar uma palavra-chave personalizada para o seu assistente de voz. Leva até 30 minutos para treinar um modelo básico de palavras-chave personalizadas.

Siga a [documentação](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) do Estúdio de Discurso para obter orientação sobre a criação de uma palavra-chave personalizada. Uma vez configurada, a sua nova palavra-chave estará disponível no portal do Projeto Santa Cruz para utilização com a sua aplicação de assistente de voz.

## <a name="commands-configuration"></a>Configuração de comandos

Os comandos personalizados facilitam a construção de aplicações de comando de voz ricas otimizadas para experiências de interação voice-first. Os comandos personalizados são mais adequados para a conclusão de tarefas ou cenários de comando e controlo.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Configuração dentro da janela de demonstração do assistente de voz

1. Clique em **Alterar** ao lado **do Comando Personalizado** na página de demonstração. Se não tiver a página de demonstração aberta, navegue na página do dispositivo (ver abaixo) e clique em **Testar o seu assistente de voz** em **Ações** para aceder à demonstração.

1. Selecione um dos comandos personalizados disponíveis e clique em **Guardar** para aplicar alterações.

### <a name="configuration-within-the-device-page"></a>Configuração dentro da página do dispositivo

1. Na página geral do [Azure Percept Studio,](https://go.microsoft.com/fwlink/?linkid=2135819)clique em **Dispositivos** no painel de menus esquerdo.

1. Selecione o dispositivo para o qual a sua aplicação de assistente de voz foi implantada.

1. Abra a conta **de discurso.**

1. Clique em **Alterar** ao lado **do Comando**.

1. Selecione um dos comandos personalizados disponíveis e clique em **Guardar** para aplicar alterações.

## <a name="create-custom-commands"></a>Criar comandos personalizados

Com [o Speech Studio,](https://speech.microsoft.com/)pode criar comandos personalizados para que o seu assistente de voz execute.

Siga a [documentação](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-custom-commands-application) do Estúdio de Discurso para obter orientação sobre a criação de comandos personalizados. Uma vez configurados, os seus novos comandos estarão disponíveis no Azure Percept Studio para utilização com a sua aplicação de assistente de voz.

## <a name="next-steps"></a>Passos seguintes

Depois de construir uma aplicação de assistente de voz, tente desenvolver uma [solução de visão sem código](./tutorial-nocode-vision.md) com o seu Azure Percept DK.