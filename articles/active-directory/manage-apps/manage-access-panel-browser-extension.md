---
title: Resolução de problemas a extensão do Painel de Acesso Azure para iE Microsoft Docs
description: Como utilizar a política de grupo para implementar o addon do Internet Explorer para o portal My Apps.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67723921"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Resolução de problemas na extensão do painel de acesso para o Internet Explorer

Este artigo ajuda-o a resolver os seguintes problemas:

* Não consegue aceder às suas aplicações através do portal My Apps enquanto utiliza o Internet Explorer.
* Vê a mensagem "Instalar software" mesmo que já tenha instalado o software.

Se é administrador, consulte [como implementar a extensão do painel de acesso para o Internet Explorer utilizando](deploy-access-panel-browser-extension.md)a Política de Grupo .

## <a name="run-the-diagnostic-tool"></a>Executar a ferramenta de diagnóstico

Pode diagnosticar problemas de instalação com a extensão do Painel de Acesso, descarregando e executando a ferramenta de diagnóstico do Painel de Acesso. 

Para descarregar e instalar a ferramenta de diagnóstico:

1. [Selecione este link para descarregar a ferramenta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Abra o ficheiro e extrai o conteúdo para o seu computador.
1. Para executar a ferramenta, clique no ficheiro chamado *AccessPanelExtensionDiagnosticTool.js* e selecione **Open com** > o**Microsoft Windows Script Host**.

    ![Abra com > Microsoft Windows Script Host](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Reveja os resultados de diagnóstico que aparecem e selecione **Sim** para corrigir os problemas. A caixa de diálogo **Check Results** aparece com informações sobre o que fazer se a extensão não funcionar.  
1. Leia a mensagem e selecione **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verifique se a extensão do painel de acesso está ativada

Para verificar se ativou a extensão do Painel de Acesso no Internet Explorer:

1. No Internet Explorer, selecione o **ícone Gear** no canto superior direito da janela e selecione **opções**de Internet .
1. Vá ao separador **Programas** e selecione **Gerir add-ons**.
1. Selecione **a extensão** do painel de acesso na secção **Microsoft Corporation** e selecione **Enable**.
1. Para evitar as alterações, feche todas as janelas do navegador Internet Explorer que tem aberta. A mudança entra em vigor da próxima vez que abrir o Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Ativar extensões para navegação privada

Para ativar extensões para navegação privada:

1. No Internet Explorer, selecione o **ícone Gear** no canto superior direito da janela e selecione **opções**de Internet .
1. Vá ao separador **Privacidade** e verifique se as **barras de ferramentas e extensões de desativação quando a navegação inprivada começar** a verificar a caixa de verificação está livre.
1. Para evitar as alterações, feche todas as janelas do navegador Internet Explorer que tem aberta. A mudança entra em vigor da próxima vez que abrir o Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Desinstalar a extensão do painel de acesso

Para desinstalar a extensão do painel de acesso do seu computador:

1. No Painel de Controlo, procure *desinstalar.*
1. Nos resultados da pesquisa, selecione **Desinstalar um programa**.

    ![Selecione a desinstalação de uma opção de programa no Painel de Controlo](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Na lista, selecione **a extensão** do painel de acesso e selecione **Desinstalar**.

    ![Desinstalar a extensão do painel de acesso](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Em seguida, pode tentar instalar a extensão novamente para ver se o problema foi resolvido.

Se tiver problemas de desinstalação da extensão, também pode removê-la utilizando a ferramenta [Microsoft Fix It.](https://go.microsoft.com/?linkid=9779673)

## <a name="related-articles"></a>Artigos relacionados

* [Acesso à aplicação e inscrição única com diretório ativo azure](what-is-single-sign-on.md)
* [Como implementar a extensão do painel de acesso para o Internet Explorer usando a política do grupo](deploy-access-panel-browser-extension.md)
