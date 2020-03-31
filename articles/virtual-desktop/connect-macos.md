---
title: Ligue ao Windows Virtual Desktop a partir do macOS - Azure
description: Como ligar ao Windows Virtual Desktop utilizando o cliente macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 673c20355e9ad4abf2c8318b3eb2ebbcc36ed8fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128241"
---
# <a name="connect-with-the-macos-client"></a>Ligar ao cliente de macOS

> Aplica-se a: macOS 10.12 ou posterior

Pode aceder aos recursos do Windows Virtual Desktop a partir dos seus dispositivos macOS com o nosso cliente descarregado. Este guia dir-lhe-á como configurar o cliente.

## <a name="install-the-client"></a>Instalar o cliente

Para começar, [faça o download](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) e instale o cliente no seu dispositivo macOS.

## <a name="subscribe-to-a-feed"></a>Subscreva um feed

Subscreva o feed que o seu administrador lhe deu para obter a lista de recursos geridos disponíveis no seu dispositivo macOS.

Para subscrever um feed:

1. Selecione **Adicionar Feed** na página principal para ligar ao serviço e recuperar os seus recursos.
2. Introduza o URL de Alimentação. Este pode ser um URL ou endereço de e-mail:
   - Se usar um URL, use o que o seu administrador lhe deu. Normalmente, o <https://rdweb.wvd.microsoft.com>URL é .
   - Para utilizar o seu email, insira o seu endereço de e-mail. Isto diz ao cliente para procurar um URL associado ao seu endereço de e-mail se o seu administrador configurar o servidor dessa forma.
3. **Selecione Subscrever**.
4. Inscreva-se na sua conta de utilizador quando solicitado.

Depois de se inscrever, deve ver uma lista de recursos disponíveis.

Depois de subscrever um feed, o conteúdo do feed irá atualizar-se automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo seu administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o cliente macOS, confira o Get iniciado com a documentação [do cliente macOS.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)
