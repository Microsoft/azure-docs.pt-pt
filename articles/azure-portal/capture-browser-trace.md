---
title: Capturar um rastreamento de navegador para solução de problemas | Microsoft Docs
description: Capturar informações de rede de um rastreamento de navegador para ajudar a solucionar problemas com o portal do Azure.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310701"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Capturar um rastreamento de navegador para solução de problemas

Se você estiver solucionando um problema com o portal do Azure e precisar entrar em contato com o suporte da Microsoft, recomendamos que você capture primeiro um rastreamento de navegador e algumas informações adicionais. As informações coletadas podem fornecer detalhes importantes sobre o portal no momento em que o problema ocorre. Siga as etapas neste artigo para as ferramentas de desenvolvedor no navegador que você usa: Google Chrome ou Microsoft Edge (Chromium), o Microsoft Edge (EdgeHTML) ou o Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome e Microsoft Edge (Chromium)

O Google Chrome e o Microsoft Edge (Chromium) são baseados no [projeto de código-fonte aberto do Chromium](https://www.chromium.org/Home). As etapas a seguir mostram como usar as ferramentas de desenvolvedor, que são muito semelhantes nos dois navegadores. Para obter mais informações, consulte [Chrome devtools](https://developers.google.com/web/tools/chrome-devtools) e [Microsoft Edge (Chromium) ferramentas para desenvolvedores](/microsoft-edge/devtools-guide-chromium).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). É importante entrar _antes_ de iniciar o rastreamento para que o rastreamento não contenha informações confidenciais relacionadas à sua entrada. 

1. Inicie a gravação das etapas executadas no portal, usando o [gravador de etapas](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. No portal, navegue até a etapa logo antes de onde o problema ocorre.

1. Pressione F12 ou selecione ![captura de tela do ícone de configurações do navegador](media/capture-browser-trace/chromium-icon-settings.png) > **mais ferramentas** > **ferramentas de desenvolvedor**.

1. Por padrão, o navegador mantém informações de rastreamento somente para a página que está carregada no momento. Defina as seguintes opções para que o navegador mantenha todas as informações de rastreamento, mesmo que sua reprodução exija que vá para mais de uma página:

    1. Selecione a guia **rede** e, em seguida, selecione **preservar log**.

          ![Captura de tela de "preservar log"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Selecione a guia **console** , selecione **configurações do console**e, em seguida, selecione **preservar log**. Selecione **as configurações do console** novamente para fechar o painel configurações.

          ![Captura de tela de "preservar log"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Selecione a guia **rede** e, em seguida, selecione **parar a gravação do log de rede** e **limpar**.

    ![Captura de tela de "parar a gravação do log de rede" e "limpar"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Selecione **gravar log de rede**e, em seguida, reproduza o problema no Portal.

    ![Captura de tela de "iniciar sessão de criação de perfil"](media/capture-browser-trace/chromium-start-session.png)

    Você verá uma saída de sessão semelhante à imagem a seguir.

    ![Captura de tela dos resultados do rastreamento do navegador](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Depois de reproduzir o comportamento de portal inesperado, selecione **parar de gravar o log de rede**, em seguida, selecione **Exportar Har** e salve o arquivo.

    ![Captura de tela de "exportar HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Pare o gravador de etapas e salve o arquivo.

1. De volta ao painel Ferramentas de desenvolvedor do navegador, selecione a guia **console** . Clique com o botão direito do mouse e selecione **salvar como...** e salve a saída do console em um arquivo de texto.

    ![Captura de tela da saída do console](media/capture-browser-trace/chromium-console-select.png)

1. Empacote o arquivo HAR, a saída do console e a gravação de tela em um formato compactado como. zip e compartilhe isso com o suporte da Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

As etapas a seguir mostram como usar as ferramentas de desenvolvedor no Microsoft Edge (EdgeHTML). Para obter mais informações, consulte [Microsoft Edge (EdgeHTML) ferramentas para desenvolvedores](/microsoft-edge/devtools-guide).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). É importante entrar _antes_ de iniciar o rastreamento para que o rastreamento não contenha informações confidenciais relacionadas à sua entrada. 

1. Inicie a gravação das etapas executadas no portal, usando o [gravador de etapas](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. No portal, navegue até a etapa logo antes de onde o problema ocorre.

1. Pressione F12 ou selecione ![captura de tela do ícone de configurações do navegador](media/capture-browser-trace/edge-icon-settings.png) > **mais ferramentas** > **ferramentas de desenvolvedor**.

1. Por padrão, o navegador mantém informações de rastreamento somente para a página que está carregada no momento. Defina as seguintes opções para que o navegador mantenha todas as informações de rastreamento, mesmo que sua reprodução exija que vá para mais de uma página:

    1. Selecione a guia **rede** e desmarque a opção **Limpar entradas ao navegar**.

          ![Captura de tela de "Limpar entradas ao navegar"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Selecione a guia **console** e, em seguida, selecione **preservar log**.

          ![Captura de tela de "preservar log"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Selecione a guia **rede** e, em seguida, selecione **parar sessão de criação de perfil** e **limpar sessão**.

    ![Captura de tela de "parar sessão de criação de perfil" e "limpar sessão"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Selecione **iniciar sessão de criação de perfil**e, em seguida, reproduza o problema no Portal.

    ![Captura de tela de "iniciar sessão de criação de perfil"](media/capture-browser-trace/edge-start-session.png)

    Você verá uma saída de sessão semelhante à imagem a seguir.

    ![Captura de tela dos resultados do rastreamento do navegador](media/capture-browser-trace/edge-browser-trace-results.png)

1. Depois de reproduzir o comportamento do portal inesperado, selecione **parar sessão de criação de perfil**e, em seguida, selecione **exportar como Har** e salve o arquivo.

    ![Captura de tela de "exportar como HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Pare o gravador de etapas e salve o arquivo.

1. De volta ao painel Ferramentas de desenvolvedor do navegador, selecione a guia **console** e expanda a janela. Coloque o cursor no início da saída do console e, em seguida, arraste e selecione todo o conteúdo da saída. Clique com o botão direito do mouse, selecione **copiar**e salve a saída do console em um arquivo de texto.

    ![Captura de tela da saída do console](media/capture-browser-trace/edge-console-select.png)

1. Empacote o arquivo HAR, a saída do console e a gravação de tela em um formato compactado como. zip e compartilhe isso com o suporte da Microsoft.

## <a name="apple-safari"></a>Apple Safari

As etapas a seguir mostram como usar as ferramentas de desenvolvedor no Apple Safari. Para obter mais informações, consulte [Safari ferramentas para desenvolvedores Overview](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Habilite as ferramentas de desenvolvedor no Apple Safari:

    1. Selecione **Safari**e, em seguida, selecione **preferências**.

        ![Captura de tela das preferências do Safari](media/capture-browser-trace/safari-preferences.png)

    1. Selecione a guia **avançado** e, em seguida, selecione **Mostrar menu desenvolver na barra de menus**.

        ![Captura de tela das preferências avançadas do Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). É importante entrar _antes_ de iniciar o rastreamento para que o rastreamento não contenha informações confidenciais relacionadas à sua entrada. 

1. Comece a gravar as etapas executadas no Portal. Para obter mais informações, consulte [como registrar a tela no seu Mac](https://support.apple.com/HT208721).

1. No portal, navegue até a etapa logo antes de onde o problema ocorre.

1. Selecione **desenvolver**e, em seguida, selecione **Mostrar inspetor da Web**.

    ![Captura de tela de "mostrar inspetor da Web"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Por padrão, o navegador mantém informações de rastreamento somente para a página que está carregada no momento. Defina as seguintes opções para que o navegador mantenha todas as informações de rastreamento, mesmo que sua reprodução exija que vá para mais de uma página:

    1. Selecione a guia **rede** e, em seguida, selecione **preservar log**.

          ![Captura de tela de "preservar log"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Selecione a guia **console** e, em seguida, selecione **preservar log**.

          ![Captura de tela de "preservar log"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Selecione a guia **rede** e, em seguida, selecione **limpar itens de rede**.

    ![Captura de tela de "limpar itens de rede"](media/capture-browser-trace/safari-clear-session.png)

1. Reproduza o problema no Portal. Você verá uma saída de sessão semelhante à imagem a seguir.

    ![Captura de tela dos resultados do rastreamento do navegador](media/capture-browser-trace/safari-browser-trace-results.png)

1. Depois de reproduzir o comportamento de portal inesperado, selecione **Exportar** e salve o arquivo.

    ![Captura de tela de "exportar"](media/capture-browser-trace/safari-network-export-har.png)

1. Interrompa o gravador de tela e salve o arquivo.

1. De volta ao painel Ferramentas de desenvolvedor do navegador, selecione a guia **console** e expanda a janela. Coloque o cursor no início da saída do console e, em seguida, arraste e selecione todo o conteúdo da saída. Use Command-C para copiar a saída e salvá-la em um arquivo de texto.

    ![Captura de tela da saída do console](media/capture-browser-trace/safari-console-select.png)

1. Empacote o arquivo HAR, a saída do console e a gravação de tela em um formato compactado como. zip e compartilhe isso com o suporte da Microsoft.

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do portal do Azure](azure-portal-overview.md)