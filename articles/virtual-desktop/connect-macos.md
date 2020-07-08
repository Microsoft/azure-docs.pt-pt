---
title: Ligue ao Windows Virtual Desktop a partir de macOS - Azure
description: Como ligar-se ao Windows Virtual Desktop utilizando o cliente macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b4696886c898eec5895c4ccb59347cf2a49f03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208999"
---
# <a name="connect-with-the-macos-client"></a>Ligar ao cliente de macOS

> Aplica-se a: macOS 10.12 ou posterior

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização primavera 2020 com objetos de desktop virtual do Windows Manager do Azure. Se estiver a utilizar o desbloqueio virtual do Windows Desktop Fall 2019 sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/connect-macos-2019.md).
>
> A atualização Virtual Desktop Spring 2020 do Windows está atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode aceder aos recursos do Windows Virtual Desktop a partir dos seus dispositivos macOS com o nosso cliente transferível. Este guia dir-lhe-á como configurar o cliente.

## <a name="install-the-client"></a>Instalar o cliente

Para começar, [faça o download](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)e   instale o cliente no seu dispositivo macOS.

## <a name="subscribe-to-a-feed"></a>Subscreva um feed

Subscreva o feed que o seu administrador lhe deu para obter a lista de recursos geridos disponíveis para si no seu dispositivo macOS.

Para subscrever um feed:

1. Selecione **Adicionar Espaço de Trabalho** na página principal para ligar ao serviço e recuperar os seus recursos.
2. Insira o URL de alimentação. Isto pode ser um URL ou endereço de e-mail:
   - Se usar um URL, use o que o seu administrador lhe deu. Normalmente, o URL <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> é.
   - Para utilizar o e-mail, insira o seu endereço de e-mail. Isto diz ao cliente para procurar um URL associado ao seu endereço de e-mail se o seu administrador configurar o servidor dessa forma.
3. Selecione **Adicionar**.
4. Inscreva-se na sua conta de utilizador quando solicitado.

Depois de se inscrever, deve ver uma lista de recursos disponíveis.

Uma vez subscrito um feed, o conteúdo do feed será atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base em alterações efetuadas pelo seu administrador.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o cliente macOS, confira o Get start com a documentação [do cliente macOS.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)
