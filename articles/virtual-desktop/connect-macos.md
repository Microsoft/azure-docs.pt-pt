---
title: Ligue ao Windows Virtual Desktop a partir de macOS - Azure
description: Como ligar-se ao Windows Virtual Desktop utilizando o cliente macOS.
author: Heidilohr
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f437c43c1e9ad960910e7576db4b3ddf3f6623ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230879"
---
# <a name="connect-to-windows-virtual-desktop-with-the-macos-client"></a>Conecte-se ao Windows Virtual Desktop com o cliente macOS

> Aplica-se a: macOS 10.12 ou posterior

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/connect-macos-2019.md).

Pode aceder aos recursos do Windows Virtual Desktop a partir dos seus dispositivos macOS com o nosso cliente transferível. Este guia dir-lhe-á como configurar o cliente.

## <a name="install-the-client"></a>Instalar o cliente

Para começar, [faça o download](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) e instale o cliente no seu dispositivo macOS.

## <a name="subscribe-to-a-feed"></a>Subscreva um feed

Subscreva o feed que o seu administrador lhe deu para obter a lista de recursos geridos disponíveis para si no seu dispositivo macOS.

Para subscrever um feed:

1. Selecione **Adicionar Espaço de Trabalho** na página principal para ligar ao serviço e recuperar os seus recursos.
2. Insira o URL de alimentação. Isto pode ser um URL ou endereço de e-mail:
   - Se usar um URL, use o que o seu administrador lhe deu. Normalmente, o URL <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> é.
   - Para utilizar o e-mail, insira o seu endereço de e-mail. Isto diz ao cliente para procurar um URL associado ao seu endereço de e-mail se o seu administrador configurar o servidor dessa forma.
   - Para ligar através do portal Gov dos EUA, utilize <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Selecione **Adicionar**.
4. Inscreva-se na sua conta de utilizador quando solicitado.

Depois de se inscrever, deve ver uma lista de recursos disponíveis.

Uma vez subscrito um feed, o conteúdo do feed será atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base em alterações efetuadas pelo seu administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o cliente macOS, confira o Get start com a documentação [do cliente macOS.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)
