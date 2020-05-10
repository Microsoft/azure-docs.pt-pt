---
title: Conecte o cliente web do Windows Virtual Desktop - Azure
description: Como ligar ao Windows Virtual Desktop utilizando o cliente web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: f17d04d94546f31c5613fa9944c7399ea7db10ae
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006052"
---
# <a name="connect-with-the-web-client"></a>Ligar com o cliente web

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/connect-web-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

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

Num browser, navegue para o [cliente web do Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/arm/webclient) e inscreva-se na sua conta de utilizador.

>[!NOTE]
>Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019, utilize a ligação do cliente web [neste artigo](./virtual-desktop-fall-2019/connect-web-2019.md) para se conectar aos seus recursos.

>[!NOTE]
>Se já assinou com uma conta de Diretório Ativo Azure diferente daquela que pretende utilizar para o Windows Virtual Desktop, deve assinar ou utilizar uma janela de navegador privada.

Depois de se inscrever, deve agora ver uma lista de recursos. Pode lançar recursos selecionando-os como se fosse uma aplicação normal no separador **All Resources.**

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente web, confira [Get started with the Web client](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
