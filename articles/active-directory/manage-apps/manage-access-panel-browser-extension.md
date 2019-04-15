---
title: Resolver problemas relacionados com a extensão do painel de acesso do Azure para o IE | Documentos da Microsoft
description: Como utilizar a política de grupo para implementar o suplemento do Internet Explorer para o portal as minhas aplicações.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63dfece713657df4450f18b8a7ce212ce2c41687
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565032"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Resolver problemas relacionados com a extensão do painel de acesso para o Internet Explorer

Este artigo ajuda-o a resolver os problemas seguintes:

* Não for possível aceder às suas aplicações através do portal as minhas aplicações ao utilizar o Internet Explorer.
* Verá a mensagem "A instalar o Software", mesmo que já tiver instalado o software.

Se for um administrador, consulte [como implementar a extensão do painel de acesso para o Internet Explorer usando a diretiva de grupo](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Execute a ferramenta de diagnóstico

Pode diagnosticar problemas de instalação com a extensão do painel de acesso ao transferir e executar a ferramenta de diagnóstico do painel de acesso. 

Para transferir e instalar a ferramenta de diagnóstico:

1. [Selecione esta ligação para transferir a ferramenta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Abra o ficheiro e extraia o conteúdo para o seu computador.
   
3. Para executar a ferramenta, com o botão direito no arquivo chamado *AccessPanelExtensionDiagnosticTool.js* e selecione **aberta com** > **Host de Script com base do Microsoft Windows** .
   
    ![Abrir com > Microsoft Windows com base em Host de Script](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

4. Reveja os resultados de diagnóstico que são apresentados e selecione **Sim** para corrigir os problemas. O **resultados de verificação** é apresentada a caixa de diálogo com informações sobre o que fazer se a extensão não funciona.  

5. Leia a mensagem e selecione **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Verifique que a extensão do painel de acesso está ativada

Para verificar que ativou a extensão do painel de acesso no Internet Explorer:

1. No Internet Explorer, selecione o **ícone de engrenagem** no canto superior direito da janela e selecione **opções da Internet**.
   
2. Vá para o **programas** separador e selecione **gerir suplementos**.
   
3. Selecione **extensão do painel de acesso** no **Microsoft Corporation** secção e selecione **ativar**.
   
4. Para guardar as alterações, feche todas as janelas de navegador do Internet Explorer, precisa abrir. A alteração tem efeito na próxima vez que abrir o Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Ativar extensões para navegação InPrivate

Para ativar extensões para navegação InPrivate:

1. No Internet Explorer, selecione o **ícone de engrenagem** no canto superior direito da janela e selecione **opções da Internet**.
   
2. Vá para o **privacidade** separador e certifique-se de que o **desativar as barras de ferramentas e extensões quando começar a Navegação InPrivate** caixa de verificação está desmarcada.
   
3.  Para guardar as alterações, feche todas as janelas de navegador do Internet Explorer, precisa abrir. A alteração tem efeito na próxima vez que abrir o Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Desinstalar a extensão do painel de acesso

Para desinstalar a extensão do painel de acesso do seu computador:

1. No painel de controlo, procure *desinstalar*. 

2. Nos resultados da pesquisa, selecione **desinstalar um programa**.
   
    ![Procure o programa de desinstalação.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

3. Na lista, selecione **extensão do painel de acesso** e selecione **desinstalação**.

    ![Desinstale a extensão do painel de acesso.](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)
   
4. Pode, em seguida, tentar instalar a extensão novamente para ver se o problema foi resolvido.

Caso se depare com problemas de desinstalar a extensão, pode também removê-lo com o [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) ferramenta.

## <a name="related-articles"></a>Artigos relacionados
* [Acesso a aplicações e início de sessão único com o Azure Active Directory](what-is-single-sign-on.md)
* [Como implementar a extensão do painel de acesso para o Internet Explorer usando a diretiva de grupo](deploy-access-panel-browser-extension.md)

