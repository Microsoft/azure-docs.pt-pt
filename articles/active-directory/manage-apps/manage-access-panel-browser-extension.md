---
title: Resolução de problemas da extensão do Painel de Acesso Azure para iE / Microsoft Docs
description: Como utilizar a política de grupo para implementar o addon do Internet Explorer para o portal My Apps.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16abfbeacd972ee8b0ab55f09945e687c95f0093
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763266"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Resolução de problemas da extensão do painel de acesso para o Internet Explorer

Este artigo ajuda-o a resolver os seguintes problemas:

* Não consegue aceder às suas apps através do portal My Apps enquanto utiliza o Internet Explorer.
* Vê a mensagem "Instalar software", apesar de já ter instalado o software.

Se for administrador, consulte [como implementar a extensão do painel de acesso para o Internet Explorer utilizando a Política de Grupo](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Executar a ferramenta de diagnóstico

Pode diagnosticar problemas de instalação com a Extensão do Painel de Acesso descarregando e executando a ferramenta de diagnóstico do Painel de Acesso. 

Para descarregar e instalar a ferramenta de diagnóstico:

1. [Selecione este link para descarregar a ferramenta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Abra o ficheiro e extraia o conteúdo para o seu computador.
1. Para executar a ferramenta, clique com o botão direito no ficheiro nomeado *AccessPanelExtensionDiagnosticTool.js* e selecione **Abrir com**o Microsoft Windows Based  >  **Script Host**.

    ![Abra com > anfitrião de script baseado no Microsoft Windows](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Reveja os resultados de diagnóstico que aparecem e selecione **Sim** para corrigir os problemas. A caixa de diálogo **'Resultados** de Verificação' aparece com informações sobre o que fazer se a extensão não funcionar.  
1. Leia a mensagem e selecione **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verifique se a extensão do painel de acesso está ativada

Para verificar se ativou a extensão do painel de acesso no Internet Explorer:

1. No Internet Explorer, selecione o **ícone De engrenagem** no canto superior direito da janela e selecione **as opções de Internet**.
1. Vá ao separador **Programas** e selecione **Gerir os addons**.
1. Selecione **a extensão do painel de acesso** na secção Microsoft **Corporation** e selecione **Enable**.
1. Para guardar as alterações, feche todas as janelas do navegador Internet Explorer que tem abertas. A mudança entra em vigor da próxima vez que abrir o Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Ativar extensões para navegação inPrivate

Para permitir extensões para navegação inPrivate:

1. No Internet Explorer, selecione o **ícone De engrenagem** no canto superior direito da janela e selecione **as opções de Internet**.
1. Vá ao separador **Privacidade** e verifique se as **barras e extensões de ferramentas desativação quando a Navegação inPrivate começa a** verificar a caixa de verificação está limpa.
1. Para guardar as alterações, feche todas as janelas do navegador Internet Explorer que tem abertas. A mudança entra em vigor da próxima vez que abrir o Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Desinstale a extensão do painel de acesso

Para desinstalar a extensão do painel de acesso a partir do seu computador:

1. No Painel de Controlo, procure *desinstalar*.
1. Nos resultados da pesquisa, selecione **Desinstalar um programa**.

    ![Selecione a opção de programa Desinstalar a partir do Painel de Controlo](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Na lista, selecione **a extensão do painel de acesso** e selecione **Desinstalar**.

    ![Desinstale a extensão do painel de acesso](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Em seguida, pode tentar instalar novamente a extensão para ver se o problema foi resolvido.

Se encontrar problemas de desinstalação da extensão, também pode removê-la utilizando a ferramenta [Microsoft Fix It.](https://go.microsoft.com/?linkid=9779673)

## <a name="related-articles"></a>Artigos relacionados

* [Acesso à aplicação e inscrição única com diretório ativo Azure](what-is-single-sign-on.md)
* [Como implementar a extensão do painel de acesso para o Internet Explorer utilizando a Política de Grupo](deploy-access-panel-browser-extension.md)
