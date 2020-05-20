---
title: Conecte o cliente web do Windows Virtual Desktop - Azure
description: Como ligar ao Windows Virtual Desktop utilizando o cliente web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8b73a6bf2c8b853d9e3e939b6c8ff6fc05ac7021
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657270"
---
# <a name="connect-with-the-web-client"></a>Ligar com o cliente web

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure. Se está a tentar gerir os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows introduzidos na atualização da primavera de 2020, consulte [este artigo](../connect-web.md).

O cliente web permite-lhe aceder aos seus recursos de Desktop Virtual do Windows a partir de um navegador web sem o longo processo de instalação.

>[!NOTE]
>O cliente web não tem atualmente suporte para os Sistemas operativos móveis.

## <a name="supported-operating-systems-and-browsers"></a>Browsers e sistemas operativos suportados

Embora qualquer navegador com capacidade html5 funcione, suportamos oficialmente os seguintes sistemas operativos e navegadores.

| Browser           | Os Suportado                     | Notas               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Janelas, macOS, Linux            | Versão 55 ou mais tarde |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Aceder a recursos remotos feed

Num browser, navegue para o cliente web do Windows Virtual Desktop <https://rdweb.wvd.microsoft.com/webclient> e inscreva-se na sua conta de utilizador.

>[!NOTE]
>Se estiver a utilizar o lançamento do Windows Virtual Desktop Spring 2020 com integração do Gestor de Recursos Azure, ligue-se aos seus <https://rdweb.wvd.microsoft.com/arm/webclient> recursos.

>[!NOTE]
>Se já assinou com uma conta de Diretório Ativo Azure diferente daquela que pretende utilizar para o Windows Virtual Desktop, deve assinar ou utilizar uma janela de navegador privada.

Depois de se inscrever, deve agora ver uma lista de recursos. Pode lançar recursos selecionando-os como se fosse uma aplicação normal no separador **All Resources.**

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente web, confira [Get started with the Web client](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
