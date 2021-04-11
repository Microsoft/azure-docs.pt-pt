---
title: Conecte-se ao Windows Virtual Desktop (clássico) do iOS - Azure
description: Como ligar-se ao Windows Virtual Desktop (clássico) utilizando o cliente iOS.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 297e2967c4e651c5b75ce232d56c51c07c872443
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445283"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-ios-client"></a>Conecte-se ao Windows Virtual Desktop (clássico) com o cliente iOS

> Aplica-se a: iOS 13.0 ou posterior. Compatível com iPhone, iPad e iPod touch.

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../connect-ios.md).

Pode aceder aos recursos do Windows Virtual Desktop a partir do seu dispositivo iOS com o nosso cliente transferível. Este guia irá dizer-lhe como configurar o cliente iOS.

## <a name="install-the-ios-client"></a>Instalar o cliente iOS

Para começar, [faça o download](https://aka.ms/rdios) e instale o cliente no seu dispositivo iOS.

## <a name="subscribe-to-a-feed"></a>Subscreva um feed

Subscreva o feed fornecido pelo seu administrador para obter a lista de recursos geridos a que pode aceder no seu dispositivo iOS.

Para subscrever um feed:

1. No Centro de Ligação, toque **+** e toque em Add **Workspace**.
2. Introduza o URL de alimentação no campo **URL de alimentação.** O URL de alimentação pode ser um URL ou um endereço de e-mail.
   - Se usar um URL, use o que o seu administrador lhe deu. Normalmente, o URL <https://rdweb.wvd.microsoft.com> é.
   - Para utilizar o e-mail, insira o seu endereço de e-mail. Isto diz ao cliente para procurar um URL associado ao seu endereço de e-mail se o seu administrador configurar o servidor dessa forma.
3. Toque **em seguida**.
4. Forneça as suas credenciais quando solicitado.
   - Para **o nome de utilizador,** forneça ao utilizador a permissão para aceder aos recursos.
   - Para **senha**, forneça a palavra-passe associada ao nome de utilizador.
   - Também pode ser solicitado que forneça fatores adicionais se a sua administração configurar a autenticação dessa forma.
5. Toque em **Guardar**.

Depois disso, o Centro de Ligação deverá exibir os recursos remotos.

Uma vez subscrito um feed, o conteúdo do feed será atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base em alterações efetuadas pelo seu administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente iOS, confira o Get start com a documentação [do cliente iOS.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/)
