---
title: Ligue ao Windows Virtual Desktop Windows 10 ou 7 - Azure
description: Como ligar ao Windows Virtual Desktop utilizando o cliente do Windows Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6436059cc8b3637edfc2a146d0aab3e2beae6a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154343"
---
# <a name="connect-with-the-windows-desktop-client"></a>Ligar ao cliente de Ambiente de Trabalho do Windows

> Aplica-se a: Windows 7, Windows 10 e Windows 10 IoT Enterprise

Pode aceder aos recursos do Windows Virtual Desktop em dispositivos com Windows 7, Windows 10 e Windows 10 IoT Enterprise utilizando o cliente windows Desktop.

> [!IMPORTANT]
> O Windows Virtual Desktop não suporta o cliente remoteApp e Desktop Connections (RADC) ou o cliente de Ligação remota de ambiente de trabalho (MSTSC).

> [!IMPORTANT]
> O Windows Virtual Desktop não suporta atualmente o cliente Remote Desktop a partir da Windows Store. O apoio a este cliente será adicionado num futuro lançamento.

## <a name="install-the-windows-desktop-client"></a>Instale o cliente do Windows Desktop

Escolha o cliente que corresponde à sua versão do Windows:

- [Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Janelas ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Pode instalar o cliente para o utilizador atual, o que não necessita de direitos de administração, ou a sua administração pode instalar e configurar o cliente para que todos os utilizadores do dispositivo possam aceder ao mesmo.

Uma vez instalado, o cliente pode ser lançado a partir do menu Iniciar, procurando por Ambiente de **Trabalho Remoto**.

## <a name="subscribe-to-a-feed"></a>Subscreva um feed

Obtenha a lista de recursos geridos à sua disposição subscrevendo o feed fornecido pela sua administração. A subscrição disponibiliza os recursos no seu PC local.

Para subscrever um feed:

1. Abra o cliente do Windows Desktop.
2. **Selecione Subscrever** na página principal para ligar ao serviço e recuperar os seus recursos.
3. Inscreva-se na sua conta de utilizador quando solicitado.

Depois de iniciar sessão com sucesso, deve ver uma lista dos recursos a que pode aceder.

Pode lançar recursos por um de dois métodos.

- A partir da página principal do cliente, clique duas vezes num recurso para lançá-lo.
- Lance um recurso como normalmente faria com outras aplicações a partir do Menu Iniciar.
  - Pode também pesquisar as aplicações na barra de pesquisa.

Uma vez subscrito a um feed, o conteúdo do feed é atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo seu administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como utilizar o cliente do Windows Desktop, confira [Get started com o cliente do Windows Desktop](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
