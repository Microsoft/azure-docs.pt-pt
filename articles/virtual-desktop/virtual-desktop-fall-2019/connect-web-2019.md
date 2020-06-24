---
title: Conecte o cliente web virtual do Windows Desktop - Azure
description: Como ligar-se ao Windows Virtual Desktop utilizando o cliente web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e95498498fd15d7a6e73630e218f9bdd1b892f09
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85204800"
---
# <a name="connect-with-the-web-client"></a>Ligar com o cliente web

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure. Se está a tentar gerir os objetos virtuais do Azure Resource Manager Windows, introduzidos na atualização da primavera de 2020, consulte [este artigo](../connect-web.md).

O cliente web permite-lhe aceder aos seus recursos de Desktop Virtual do Windows a partir de um navegador web sem o longo processo de instalação.

>[!NOTE]
>O cliente web não tem atualmente suporte para os SO móvel.

## <a name="supported-operating-systems-and-browsers"></a>Browsers e sistemas operativos suportados

Embora qualquer navegador com capacidade HTML5 funcione, apoiamos oficialmente os seguintes sistemas operativos e navegadores.

| Browser           | SoA apoiado                     | Notas               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versão 55 ou posterior |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Aceder a feed de recursos remotos

Num browser, navegue para o cliente web virtual do Windows Desktop <https://rdweb.wvd.microsoft.com/webclient> e inscreva-se na sua conta de utilizador.

>[!NOTE]
>Se estiver a utilizar o desbloqueio Virtual Desktop Spring 2020 do Windows com a integração do Azure Resource Manager, ligue-se aos seus <https://rdweb.wvd.microsoft.com/arm/webclient> recursos.

>[!NOTE]
>Se já assinou com uma conta de Diretório Azure Ative diferente da que pretende utilizar para o Windows Virtual Desktop, deve assinar ou utilizar uma janela de navegador privada.

Depois de iniciar sessão, deve agora ver uma lista de recursos. Pode lançar recursos selecionando-os como se fosse uma aplicação normal no separador **Todos os Recursos.**

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente web, confira [Consulte o cliente web.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)
