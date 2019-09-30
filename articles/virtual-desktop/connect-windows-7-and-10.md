---
title: Conectar-se à área de trabalho virtual do Windows do Windows 10 ou Windows 7-Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente de área de trabalho do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
ms.openlocfilehash: 561dad566d7f1caaf609c8013fa075062e4471ea
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676751"
---
# <a name="connect-with-the-windows-desktop-client"></a>Ligar ao cliente de Ambiente de Trabalho do Windows

> Aplica-se a: Windows 7 e Windows 10

Você pode acessar os recursos da área de trabalho virtual do Windows em dispositivos com Windows 7 ou Windows 10 usando o cliente de área de trabalho do Windows.

> [!IMPORTANT]
> A área de trabalho virtual do Windows não dá suporte ao cliente RADC (RemoteApp e área de trabalho) ou ao cliente do Conexão de Área de Trabalho Remota (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Instalar o cliente de área de trabalho do Windows

Estes são os clientes que você pode baixar atualmente:

- [Windows de 64 bits](https://go.microsoft.com/fwlink/?linkid=2068602)

Atualizaremos essa lista à medida que o cliente se tornar disponível para mais versões do Windows.

Você pode instalar o cliente do para o usuário atual, que não requer direitos de administrador, ou o administrador pode instalar e configurar o cliente para que todos os usuários no dispositivo possam acessá-lo.

Uma vez instalado, o cliente pode ser iniciado no menu iniciar pesquisando **área de trabalho remota**.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Obtenha a lista de recursos gerenciados disponíveis para você assinando o feed fornecido pelo administrador. A assinatura disponibiliza os recursos no seu PC local.

Para assinar um feed:

1. Abra o cliente de área de trabalho do Windows.
2. Selecione **assinar** na página principal para se conectar ao serviço e recuperar seus recursos.
3. Entre com sua conta de usuário quando solicitado.

Depois de entrar com êxito, você verá uma lista dos recursos que você pode acessar.

Você pode iniciar recursos por um dos dois métodos.

- Na página principal do cliente, clique duas vezes em um recurso para iniciá-lo.
- Inicie um recurso como faria normalmente com outros aplicativos no menu iniciar.
  - Você também pode pesquisar os aplicativos na barra de pesquisa.

Após a assinatura de um feed, o conteúdo do feed é atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente de área de trabalho do Windows, confira [introdução ao cliente de desktop do Windows](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windowsdesktop).
