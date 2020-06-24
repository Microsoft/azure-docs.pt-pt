---
title: Ligue ao Windows Virtual Desktop a partir de macOS - Azure
description: Como ligar-se ao Windows Virtual Desktop utilizando o cliente macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 024a1ab1a7fef58bd5fd8f9e7e0fc743a4ecee71
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213164"
---
# <a name="connect-with-the-macos-client"></a>Ligar ao cliente de macOS

> Aplica-se a: macOS 10.12 ou posterior

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure. Se está a tentar gerir os objetos virtuais do Azure Resource Manager Windows, introduzidos na atualização da primavera de 2020, consulte [este artigo](../connect-macos.md).

Pode aceder aos recursos do Windows Virtual Desktop a partir dos seus dispositivos macOS com o nosso cliente transferível. Este guia dir-lhe-á como configurar o cliente.

## <a name="install-the-client"></a>Instalar o cliente

Para começar, [faça o download](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) e instale o cliente no seu dispositivo macOS.

## <a name="subscribe-to-a-feed"></a>Subscreva um feed

Subscreva o feed que o seu administrador lhe deu para obter a lista de recursos geridos disponíveis para si no seu dispositivo macOS.

Para subscrever um feed:

1. Selecione **Adicionar Espaço de Trabalho** na página principal para ligar ao serviço e recuperar os seus recursos.
2. Insira o URL de alimentação. Isto pode ser um URL ou endereço de e-mail:
   - Se usar um URL, use o que o seu administrador lhe deu. Normalmente, o URL <https://rdweb.wvd.microsoft.com> é.
   - Para utilizar o e-mail, insira o seu endereço de e-mail. Isto diz ao cliente para procurar um URL associado ao seu endereço de e-mail se o seu administrador configurar o servidor dessa forma.
3. Selecione **Adicionar**.
4. Inscreva-se na sua conta de utilizador quando solicitado.

Depois de se inscrever, deve ver uma lista de recursos disponíveis.

Uma vez subscrito um feed, o conteúdo do feed será atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base em alterações efetuadas pelo seu administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o cliente macOS, confira o Get start com a documentação [do cliente macOS.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)