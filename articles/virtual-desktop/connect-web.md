---
title: Conecte-se ao Windows Virtual Desktop com o cliente web - Azure
description: Como ligar-se ao Windows Virtual Desktop utilizando o cliente web.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a4b22966c3d4db268e212bb3f2d1bbb78fee74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400641"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Conecte-se ao Windows Virtual Desktop com o cliente web

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/connect-web-2019.md).

O cliente web permite-lhe aceder aos seus recursos de Desktop Virtual do Windows a partir de um navegador web sem o longo processo de instalação.

>[!NOTE]
>O cliente web não tem atualmente suporte para os SO móvel.

## <a name="supported-operating-systems-and-browsers"></a>Browsers e sistemas operativos suportados

Embora qualquer navegador com capacidade HTML5 funcione, apoiamos oficialmente os seguintes sistemas operativos e navegadores.

| Browser           | SoA apoiado                     | Notas               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Versão 11 ou posterior |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versão 55 ou posterior |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Aceder a feed de recursos remotos

Num browser, navegue para a versão integrada do Azure Resource Manager do cliente web virtual do Windows Desktop <https://rdweb.wvd.microsoft.com/arm/webclient> e inscreva-se na sua conta de utilizador.

>[!NOTE]
>Se estiver a utilizar o Windows Virtual Desktop (clássico) sem integração do Azure Resource Manager, ligue-se aos seus <https://rdweb.wvd.microsoft.com/webclient> recursos.
>
> Se estiver a utilizar o portal Us Gov, utilize <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Se já assinou com uma conta de Diretório Azure Ative diferente da que pretende utilizar para o Windows Virtual Desktop, deve assinar ou utilizar uma janela de navegador privada.

Depois de iniciar sessão, deve agora ver uma lista de recursos. Pode lançar recursos selecionando-os como se fosse uma aplicação normal no separador **Todos os Recursos.**

## <a name="using-an-input-method-editor"></a>Usando um editor de método de entrada

O cliente web suporta a utilização de um Editor de Método de Entrada (IME) na sessão remota na versão **1.0.21.16 ou posterior**. O pacote de idiomas para o teclado que pretende utilizar na sessão remota deve ser instalado na máquina virtual do anfitrião. Para saber mais sobre a configuração de pacotes de idiomas na sessão remota, consulte [os pacotes de idiomas add a uma imagem multi-sessão do Windows 10](language-packs.md).

Para ativar a entrada do IME utilizando o cliente web:

1. Antes de ligar à sessão remota, aceda ao painel **de Definições** do cliente web.

2. Alternar a definição **do Enable Input Method Editor** para **On**.

3. No menu suspenso, selecione o teclado que utilizará na sessão remota.

4. Ligue-se à sessão remota.

O cliente web suprimirá a janela IME local quando estiver focado na sessão remota. A alteração das definições do IME uma vez que já tenha ligado à sessão remota não terá qualquer efeito.

>[!NOTE]
>Se o pacote de idiomas não for instalado na máquina virtual do anfitrião, a sessão remota será padrão para o teclado inglês (Estados Unidos).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente web, confira [Consulte o cliente web.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)
