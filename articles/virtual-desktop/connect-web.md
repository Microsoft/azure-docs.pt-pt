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
ms.openlocfilehash: 4bb54db8a18baa237f1c32c223b5f609638c7523
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080629"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Conecte-se ao Windows Virtual Desktop com o cliente web

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização primavera 2020 com objetos de desktop virtual do Windows Manager do Azure. Se estiver a utilizar o desbloqueio virtual do Windows Desktop Fall 2019 sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/connect-web-2019.md).
>
> A atualização Virtual Desktop Spring 2020 do Windows está atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Num browser, navegue para a versão integrada do Azure Resource Manager do cliente web virtual do Windows Desktop <https://rdweb.wvd.microsoft.com/arm/webclient> e inscreva-se na sua conta de utilizador.

>[!NOTE]
>Se estiver a utilizar o lançamento virtual do Windows Desktop Fall 2019 sem integração do Azure Resource Manager, ligue-se aos seus <https://rdweb.wvd.microsoft.com/webclient> recursos.

>[!NOTE]
>Se já assinou com uma conta de Diretório Azure Ative diferente da que pretende utilizar para o Windows Virtual Desktop, deve assinar ou utilizar uma janela de navegador privada.

Depois de iniciar sessão, deve agora ver uma lista de recursos. Pode lançar recursos selecionando-os como se fosse uma aplicação normal no separador **Todos os Recursos.**

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente web, confira [Consulte o cliente web.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)
