---
title: Conecte-se ao Windows Virtual Desktop com o cliente web - Azure
description: Como ligar-se ao Windows Virtual Desktop utilizando o cliente web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fdbd0ddb33785655cada2600acdecdced4aeb2f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283440"
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

>[!NOTE]
>Se já assinou com uma conta de Diretório Azure Ative diferente da que pretende utilizar para o Windows Virtual Desktop, deve assinar ou utilizar uma janela de navegador privada.

Depois de iniciar sessão, deve agora ver uma lista de recursos. Pode lançar recursos selecionando-os como se fosse uma aplicação normal no separador **Todos os Recursos.**

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente web, confira [Consulte o cliente web.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)
