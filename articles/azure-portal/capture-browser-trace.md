---
title: Capture um vestígio de navegador para resolução de problemas Microsoft Docs
description: Capture informações de rede a partir de um traço de navegador para ajudar a resolver problemas com o portal Azure.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 05/11/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 9f09477eb174792d6348a6b9761a4f9e5467ec44
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649495"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Capturar um rastreio de browser para resolução de problemas

Se estiver a resolver um problema com o portal Azure e precisar de contactar o suporte da Microsoft, recomendamos que capture primeiro um rastreio de navegador e algumas informações adicionais. A informação que recolhe pode fornecer detalhes importantes sobre o portal no momento em que o problema ocorre. Siga os passos neste artigo para as ferramentas de desenvolvimento no navegador que utiliza: Google Chrome ou Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML) ou Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome e Microsoft Edge (Crómio)

O Google Chrome e o Microsoft Edge (Chromium) são ambos baseados no projeto de código aberto do [Chromium.](https://www.chromium.org/Home) Os seguintes passos mostram como usar as ferramentas de desenvolvimento, que são muito semelhantes nos dois navegadores. Para mais informações, consulte o [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) e o [Microsoft Edge (Chromium) Developer Tools](/microsoft-edge/devtools-guide-chromium).

1. Inicie sessão no [portal do Azure](https://portal.azure.com). É importante iniciar sessão _antes_ de iniciar o rastreio para que o vestígio não contenha informações confidenciais relacionadas com o seu início de sessão. 

1. Comece a registar os passos que toma no portal, utilizando [o Steps Recorder](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. No portal, navegue até ao degrau pouco antes de onde o problema ocorre.

1. Prima F12 ou selecione ![ Screenshot do ícone de configurações do navegador Mais ](media/capture-browser-trace/chromium-icon-settings.png)  >  **ferramentas**  >  **Ferramentas Developer**.

1. Por padrão, o navegador guarda a informação de rastreio apenas para a página que está atualmente carregada. Delineie as seguintes opções para que o navegador guarde todas as informações sobre o rastreio, mesmo que o seu repro exija ir para mais do que uma página:

    1. Selecione o separador **Rede** e, em seguida, selecione preservar o **registo**.

          ![Screenshot de "Preserve log"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Selecione o separador **Consola,** selecione **as definições**da consola e selecione **"Preservar**" . Selecione **novamente as definições da consola** para fechar o painel de definições.

          ![Screenshot de "Preserve Log"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Selecione o separador **Rede** e, em seguida, selecione **stop recording network log** and **Clear**.

    ![Screenshot de "Stop recording network log" e "Clear"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Selecione o registo da **rede Record**e, em seguida, reproduza o problema no portal.

    ![Screenshot de "Iniciar a sessão de perfis"](media/capture-browser-trace/chromium-start-session.png)

    Verá a saída da sessão semelhante à seguinte imagem.

    ![Screenshot dos resultados do rastreio do navegador](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Depois de reproduzir o comportamento inesperado do portal, selecione **Stop recording network log**, em seguida, selecione Export **HAR** e guarde o ficheiro.

    ![Screenshot de "Export HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Pare o gravador de passos e guarde a gravação.

1. De volta ao painel de ferramentas de desenvolvimento do navegador, selecione o separador **Consola.** Clique bem numa das mensagens e, em seguida, selecione **Guardar como...**, e guarde a saída da consola para um ficheiro de texto.

    ![Screenshot da saída da consola](media/capture-browser-trace/chromium-console-select.png)

1. Embala o ficheiro HAR, a saída da consola e a gravação do ecrã num formato comprimido como .zip, e partilhe-o com o suporte da Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

Os seguintes passos mostram como utilizar as ferramentas de desenvolvimento no Microsoft Edge (EdgeHTML). Para mais informações, consulte as ferramentas de [desenvolvimento do Microsoft Edge (EdgeHTML).](/microsoft-edge/devtools-guide)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). É importante iniciar sessão _antes_ de iniciar o rastreio para que o vestígio não contenha informações confidenciais relacionadas com o seu início de sessão. 

1. Comece a registar os passos que toma no portal, utilizando [o Steps Recorder](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. No portal, navegue até ao degrau pouco antes de onde o problema ocorre.

1. Prima F12 ou selecione ![ Screenshot do ícone de configurações do navegador Mais ](media/capture-browser-trace/edge-icon-settings.png)  >  **ferramentas**  >  **Ferramentas Developer**.

1. Por padrão, o navegador guarda a informação de rastreio apenas para a página que está atualmente carregada. Delineie as seguintes opções para que o navegador guarde todas as informações sobre o rastreio, mesmo que o seu repro exija ir para mais do que uma página:

    1. Selecione o separador **Rede** e, em seguida, limpe a opção **Entradas claras na navegação**.

          ![Screenshot de "Entradas claras na navegação"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Selecione o separador **Consolae** e, em seguida, **selecione Preservar**o Registo .

          ![Screenshot de "Preserve Log"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Selecione o separador **Rede** e, em seguida, selecione parar a **sessão de perfis** e limpar a **sessão**.

    ![Screenshot de "Stop profiling session" e "Clear session"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Selecione **Iniciar a sessão de perfis**e, em seguida, reproduzir o problema no portal.

    ![Screenshot de "Iniciar a sessão de perfis"](media/capture-browser-trace/edge-start-session.png)

    Verá a saída da sessão semelhante à seguinte imagem.

    ![Screenshot dos resultados do rastreio do navegador](media/capture-browser-trace/edge-browser-trace-results.png)

1. Depois de reproduzir o comportamento inesperado do portal, selecione **a sessão de perfis**de parar, em seguida, selecione Exportar como **HAR** e guarde o ficheiro.

    ![Screenshot de "Export as HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Pare o gravador de passos e guarde a gravação.

1. De volta ao painel de ferramentas de desenvolvimento do navegador, selecione o separador **Consolae** e expanda a janela. Coloque o cursor no início da saída da consola e, em seguida, arraste e selecione todo o conteúdo da saída. Clique à direita, em seguida, selecione **Copy**, e guarde a saída da consola para um ficheiro de texto.

    ![Screenshot da saída da consola](media/capture-browser-trace/edge-console-select.png)

1. Embala o ficheiro HAR, a saída da consola e a gravação do ecrã num formato comprimido como .zip, e partilhe-o com o suporte da Microsoft.

## <a name="apple-safari"></a>Apple Safari

Os seguintes passos mostram como usar as ferramentas de desenvolvimento no Apple Safari. Para mais informações, consulte a [visão geral das Ferramentas de Desenvolvimento do Safari](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Ative as ferramentas de desenvolvimento no Apple Safari:

    1. Selecione **Safari**e, em seguida, selecione **Preferências**.

        ![Screenshot das preferências do Safari](media/capture-browser-trace/safari-preferences.png)

    1. Selecione o separador **Advanced** e, em seguida, selecione Mostrar Desenvolver menu na barra de **menus**.

        ![Screenshot das preferências avançadas do Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). É importante iniciar sessão _antes_ de iniciar o rastreio para que o vestígio não contenha informações confidenciais relacionadas com o seu início de sessão. 

1. Comece a gravar os passos que toma no portal. Para mais informações, consulte [Como gravar o ecrã no seu Mac](https://support.apple.com/HT208721).

1. No portal, navegue até ao degrau pouco antes de onde o problema ocorre.

1. Selecione **Desenvolver**e, em seguida, selecione **Mostrar Web Inspetor**.

    ![Screenshot de "Show Web Inspetor"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Por padrão, o navegador guarda a informação de rastreio apenas para a página que está atualmente carregada. Delineie as seguintes opções para que o navegador guarde todas as informações sobre o rastreio, mesmo que o seu repro exija ir para mais do que uma página:

    1. Selecione o separador **Rede** e, em seguida, selecione Preservar o **Registo**.

          ![Screenshot de "Preserve Log"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Selecione o separador **Consolae** e, em seguida, **selecione Preservar**o Registo .

          ![Screenshot de "Preserve Log"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Selecione o separador **Rede** e, em seguida, selecione Itens de **rede claros**.

    ![Screenshot de "Clear Network Items"](media/capture-browser-trace/safari-clear-session.png)

1. Reproduza a questão no portal. Verá a saída da sessão semelhante à seguinte imagem.

    ![Screenshot dos resultados do rastreio do navegador](media/capture-browser-trace/safari-browser-trace-results.png)

1. Depois de reproduzir o comportamento inesperado do portal, selecione **Exportar** e guarde o ficheiro.

    ![Screenshot de "Exportação"](media/capture-browser-trace/safari-network-export-har.png)

1. Pare o gravador de ecrã e guarde a gravação.

1. De volta ao painel de ferramentas de desenvolvimento do navegador, selecione o separador **Consolae** e expanda a janela. Coloque o cursor no início da saída da consola e, em seguida, arraste e selecione todo o conteúdo da saída. Utilize o Comando C para copiar a saída e guardá-la para um ficheiro de texto.

    ![Screenshot da saída da consola](media/capture-browser-trace/safari-console-select.png)

1. Embala o ficheiro HAR, a saída da consola e a gravação do ecrã num formato comprimido como .zip, e partilhe-o com o suporte da Microsoft.

## <a name="firefox"></a>Firefox

Os seguintes passos mostram como utilizar as ferramentas de desenvolvimento no Firefox. Para mais informações, consulte ferramentas de [desenvolvimento do Firefox](https://developer.mozilla.org/docs/Tools).

1. Inicie sessão no [portal do Azure](https://portal.azure.com). É importante iniciar sessão _antes_ de iniciar o rastreio para que o vestígio não contenha informações confidenciais relacionadas com o seu início de sessão. 

1. Comece a gravar os passos que toma no portal. Utilize [o gravador de passos](https://support.microsoft.com/help/22878/windows-10-record-steps) no Windows ou veja como gravar o ecrã no seu [Mac](https://support.apple.com/HT208721).

1. No portal, navegue até ao degrau pouco antes de onde o problema ocorre.

1. Prima F12 ou selecione ![ Screenshot do ícone de definições do navegador Web Developer ](media/capture-browser-trace/firefox-icon-settings.png)  >  Ferramentas de**Web Developer**  >  **alternância**.

1. Por padrão, o navegador guarda a informação de rastreio apenas para a página que está atualmente carregada. Delineie as seguintes opções para que o navegador guarde todas as informações sobre o rastreio, mesmo que o seu repro exija ir para mais do que uma página:

    1. Selecione o separador **Rede** e, em seguida, selecione **Persist Logs**.

          ![Screenshot de "Persist Logs"](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. Selecione o separador **Consola,** selecione **as definições**da consola e, em seguida, selecione **Registos Persist .**

          ![Screenshot de "Persist Logs"](media/capture-browser-trace/firefox-console-persist-logs.png)

1. Selecione o separador **Rede** e, em seguida, selecione **Clear**.

    ![Screenshot de "Clear"](media/capture-browser-trace/firefox-clear-session.png)

1. Reproduza a questão no portal. Verá a saída da sessão semelhante à seguinte imagem.

    ![Screenshot dos resultados do rastreio do navegador](media/capture-browser-trace/firefox-browser-trace-results.png)

1. Depois de reproduzir o comportamento inesperado do portal, selecione **HAR Export/Import e,** em seguida, **salve tudo como HAR**.

    ![Screenshot de "Export HAR"](media/capture-browser-trace/firefox-network-export-har.png)

1. Pare o gravador de passos no Windows ou a gravação do ecrã no Mac e guarde a gravação.

1. De volta ao painel de ferramentas de desenvolvimento do navegador, selecione o separador **Consola.** Clique direito numa das mensagens e, em seguida, selecione **Export Visible Message To**, e guarde a saída da consola para um ficheiro de texto.

    ![Screenshot da saída da consola](media/capture-browser-trace/firefox-console-select.png)

1. Embala o ficheiro HAR, a saída da consola e a gravação do ecrã num formato comprimido como .zip, e partilhe-o com o suporte da Microsoft.

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do portal do Azure](azure-portal-overview.md)