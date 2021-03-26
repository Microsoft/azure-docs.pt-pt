---
title: Capturar um rastreio de browser para resolução de problemas
description: Capture informações de rede a partir de um rastreio de navegador para ajudar a resolver problemas com o portal Azure.
ms.date: 03/25/2021
ms.topic: troubleshooting
ms.openlocfilehash: 63bc3e93187274db607b5f60216de461729801dc
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563497"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Capturar um rastreio de browser para resolução de problemas

Se estiver a resolver um problema com o portal Azure e precisar de contactar o suporte da Microsoft, recomendamos que capture primeiro um vestígio de navegador e algumas informações adicionais. As informações que recolhe podem fornecer detalhes importantes sobre o portal no momento em que o problema ocorre. Siga os passos neste artigo para as ferramentas de desenvolvimento no navegador que utiliza: Google Chrome ou Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML), Apple Safari ou Firefox.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome e Microsoft Edge (Chromium)

O Google Chrome e o Microsoft Edge (Chromium) são ambos baseados no [projeto Chromium open source](https://www.chromium.org/Home). Os passos a seguir mostram como usar as ferramentas de desenvolvimento, que são muito semelhantes nos dois navegadores. Para obter mais informações, consulte as ferramentas de desenvolvimento [do Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) e [Microsoft Edge (Chromium).](/microsoft-edge/devtools-guide-chromium)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). É importante iniciar o sinal _antes_ de iniciar o rastreio para que o vestígio não contenha informações sensíveis relacionadas com o seu início. 

1. Comece a registar os passos que toma no portal, utilizando o [Gravador de Passos](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. No portal, navegue até ao degrau pouco antes do local onde o problema ocorre.

1. Prima F12 ou selecione ![ Screenshot que mostra o ícone de configurações do navegador.](media/capture-browser-trace/chromium-icon-settings.png) > **Mais ferramentas**  >  **Ferramentas de desenvolvimento**.

1. Por padrão, o navegador mantém a informação de rastreio apenas para a página que está atualmente carregada. Desaprova as seguintes opções para que o navegador mantenha todas as informações de rastreio, mesmo que a sua repro exija ir a mais de uma página:

    1. Selecione o separador **'Rede'** e, em seguida, selecione **'Registar'.**

          ![Screenshot que realça a opção de registo de reserva no separador Rede.](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Selecione o separador **Consola,** selecione **as definições de Consola** e, em seguida, selecione Preserve **Log**. Selecione **as definições de consola** novamente para fechar o painel de definições.

          ![Screenshot que realça a opção de registo de reserva no separador Consola.](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Selecione o separador **'Rede'** e, em seguida, selecione **Parar de registar o registo de rede** e **limpar**.

    ![Screenshot de "Stop recording network log" e "Clear"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Selecione **registo de rede** e, em seguida, reproduza o problema no portal.

    ![Screenshot que mostra como gravar o registo de rede.](media/capture-browser-trace/chromium-start-session.png)

    Verá a saída da sessão semelhante à seguinte imagem.

    ![Screenshot que mostra a saída da sessão.](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Depois de reproduzir o comportamento inesperado do portal, selecione **Parar de gravar o registo de rede,** em seguida, selecione Export **HAR** e guarde o ficheiro.

    ![Screenshot que mostra como exportar HAR no separador Rede.](media/capture-browser-trace/chromium-network-export-har.png)

1. Pare o gravador de passos e guarde a gravação.

1. De volta ao painel de ferramentas de desenvolvimento do navegador, selecione o **separador Consola.** Clique com o botão direito numa das mensagens e, em seguida, **selecione Save as...** e guarde a saída da consola para um ficheiro de texto.

    ![Screenshot que realça o separador Consola e o Save como... menu](media/capture-browser-trace/chromium-console-select.png)

1. Embale o ficheiro HAR, a saída da consola e a gravação do ecrã num formato comprimido como .zip, e partilhe-o com o suporte da Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

Os passos a seguir mostram como utilizar as ferramentas de desenvolvimento no Microsoft Edge (EdgeHTML). Para obter mais informações, consulte [as Ferramentas de Desenvolvimento do Microsoft Edge (EdgeHTML).](/microsoft-edge/devtools-guide)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). É importante iniciar o sinal _antes_ de iniciar o rastreio para que o vestígio não contenha informações sensíveis relacionadas com o seu início. 

1. Comece a registar os passos que toma no portal, utilizando o [Gravador de Passos](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. No portal, navegue até ao degrau pouco antes do local onde o problema ocorre.

1. Prima F12 ou selecione ![ Screenshot do ícone de configurações do navegador.](media/capture-browser-trace/edge-icon-settings.png) > **Mais ferramentas**  >  **Ferramentas de desenvolvimento**.

1. Por padrão, o navegador mantém a informação de rastreio apenas para a página que está atualmente carregada. Desaprova as seguintes opções para que o navegador mantenha todas as informações de rastreio, mesmo que a sua repro exija ir a mais de uma página:

    1. Selecione o separador **'Rede'** e, em seguida, limpe a opção **Limpar as entradas na navegação**.

          ![Screenshot de "Entradas claras na navegação"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Selecione o separador **Consola** e, em seguida, selecione **'Registar'.**

          ![Screenshot de "Preserve Log"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Selecione o separador **'Rede'** e, em seguida, selecione **'Stop profiling session'** e **Clear session**.

    ![Screenshot de "Stop profiling session" e "Clear session"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Selecione **Iniciar a sessão de perfis** e, em seguida, reproduzir o problema no portal.

    ![Screenshot de "Iniciar sessão de perfis"](media/capture-browser-trace/edge-start-session.png)

    Verá a saída da sessão semelhante à seguinte imagem.

    ![Screenshot que mostra a saída para a sessão de perfis.](media/capture-browser-trace/edge-browser-trace-results.png)

1. Depois de reproduzir o comportamento inesperado do portal, selecione **Stop profiling session**, em seguida, selecione Export as **HAR** e guarde o ficheiro.

    ![Screenshot de "Exportação como HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Pare o gravador de passos e guarde a gravação.

1. De volta ao painel de ferramentas de desenvolvimento do navegador, selecione o separador **Consola** e expanda a janela. Coloque o cursor no início da saída da consola e, em seguida, arraste e selecione todo o conteúdo da saída. Clique com o botão direito e, em seguida, selecione **Copy** e guarde a saída da consola para um ficheiro de texto.

    ![Screenshot que realça a opção do menu Copy.](media/capture-browser-trace/edge-console-select.png)

1. Embale o ficheiro HAR, a saída da consola e a gravação do ecrã num formato comprimido como .zip, e partilhe-o com o suporte da Microsoft.

## <a name="apple-safari"></a>Apple Safari

Os passos a seguir mostram como usar as ferramentas de desenvolvimento no Apple Safari. Para mais informações, consulte a [visão geral do Safari Developer Tools.](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)

1. Ative as ferramentas de desenvolvimento no Apple Safari:

    1. Selecione **Safari** e, em seguida, selecione **Preferências**.

        ![Screenshot das preferências do Safari](media/capture-browser-trace/safari-preferences.png)

    1. Selecione o separador **Advanced** e, em seguida, selecione **o menu Mostrar Desenvolvimento na barra de menus**.

        ![Screenshot das preferências avançadas do Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). É importante iniciar o sinal _antes_ de iniciar o rastreio para que o vestígio não contenha informações sensíveis relacionadas com o seu início. 

1. Comece a gravar os passos que toma no portal. Para obter mais informações, consulte [Como gravar o ecrã no seu Mac](https://support.apple.com/HT208721).

1. No portal, navegue até ao degrau pouco antes do local onde o problema ocorre.

1. Selecione **Desenvolver,** em seguida, **selecione Show Web Inspetor**.

    ![Screenshot de "Show Web Inspetor"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Por padrão, o navegador mantém a informação de rastreio apenas para a página que está atualmente carregada. Desaprova as seguintes opções para que o navegador mantenha todas as informações de rastreio, mesmo que a sua repro exija ir a mais de uma página:

    1. Selecione o separador **'Rede'** e, em seguida, selecione **'Registar'.**

          ![Screenshot que mostra a opção Preservar Registo.](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Selecione o separador **Consola** e, em seguida, selecione **'Registar'.**

          ![Screenshot que mostra o Registo de Reserva no separador Consola.](media/capture-browser-trace/safari-console-preserve-log.png)

1. Selecione o separador **'Rede'** e, em seguida, selecione **Itens de Rede Limpas**.

    ![Screenshot de "Itens de rede clara"](media/capture-browser-trace/safari-clear-session.png)

1. Reproduza a questão no portal. Verá a saída da sessão semelhante à seguinte imagem.

    ![Screenshot que mostra a saída depois de reproduzir o problema.](media/capture-browser-trace/safari-browser-trace-results.png)

1. Depois de reproduzir o comportamento inesperado do portal, selecione **Export** e guarde o ficheiro.

    ![Screenshot de "Exportação"](media/capture-browser-trace/safari-network-export-har.png)

1. Pare o gravador de ecrã e guarde a gravação.

1. De volta ao painel de ferramentas de desenvolvimento do navegador, selecione o separador **Consola** e expanda a janela. Coloque o cursor no início da saída da consola e, em seguida, arraste e selecione todo o conteúdo da saída. Utilize o Comando C para copiar a saída e guarde-a num ficheiro de texto.

    ![Screenshot que realça que pode ver e copiar a saída.](media/capture-browser-trace/safari-console-select.png)

1. Embale o ficheiro HAR, a saída da consola e a gravação do ecrã num formato comprimido como .zip, e partilhe-o com o suporte da Microsoft.

## <a name="firefox"></a>Firefox

Os passos a seguir mostram como utilizar as ferramentas de desenvolvimento no Firefox. Para mais informações, consulte [as Ferramentas de Desenvolvimento do Firefox.](https://developer.mozilla.org/docs/Tools)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). É importante iniciar o sinal _antes_ de iniciar o rastreio para que o vestígio não contenha informações sensíveis relacionadas com o seu início. 

1. Comece a gravar os passos que toma no portal. Utilize [o gravador de passos](https://support.microsoft.com/help/22878/windows-10-record-steps) no Windows ou veja como gravar o ecrã no seu [Mac](https://support.apple.com/HT208721).

1. No portal, navegue até ao degrau pouco antes do local onde o problema ocorre.

1. Prima F12 ou selecione ![ Screenshot das configurações do navegador ícone ](media/capture-browser-trace/firefox-icon-settings.png)  >  **Web Developer**  >  **Toggle Tools**.

1. Por padrão, o navegador mantém a informação de rastreio apenas para a página que está atualmente carregada. Desaprova as seguintes opções para que o navegador mantenha todas as informações de rastreio, mesmo que a sua repro exija ir a mais de uma página:

    1. Selecione o separador **'Rede'** e, em seguida, **selecione Registos de Persistência**.

          ![Screenshot que realça a opção Persist Logs.](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. Selecione o separador **Consola,** selecione **as definições de Consola** e, em seguida, selecione **Registos de Persistência**.

          ![Screenshot de "Persist Logs"](media/capture-browser-trace/firefox-console-persist-logs.png)

1. Selecione o separador **'Rede'** e, em seguida, selecione **'Limpar'.**

    ![Screenshot de "Clear"](media/capture-browser-trace/firefox-clear-session.png)

1. Reproduza a questão no portal. Verá a saída da sessão semelhante à seguinte imagem.

    ![Screenshot dos resultados do rastreio do navegador](media/capture-browser-trace/firefox-browser-trace-results.png)

1. Depois de reproduzir o comportamento inesperado do portal, **selecione Save All As HAR**.

    ![Screenshot de "Export HAR"](media/capture-browser-trace/firefox-network-export-har.png)

1. Pare o registo de passos no Windows ou a gravação do ecrã no Mac e guarde a gravação.

1. De volta ao painel de ferramentas de desenvolvimento do navegador, selecione o **separador Consola.** Clique com o botão direito numa das mensagens e, em seguida, selecione **Export Visible Messages To** e guarde a saída da consola para um ficheiro de texto.

    ![Screenshot da saída da consola](media/capture-browser-trace/firefox-console-select.png)

1. Embale o ficheiro HAR, a saída da consola e a gravação do ecrã num formato comprimido como .zip, e partilhe-o com o suporte da Microsoft.

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do portal do Azure](azure-portal-overview.md)