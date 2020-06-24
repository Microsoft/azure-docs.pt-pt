---
title: Ligue ao Windows Virtual Desktop Windows 10 ou 7 - Azure
description: Como ligar-se ao Windows Virtual Desktop utilizando o cliente Windows Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d333b2f8bbb660e9fd71339159d942c0520f4f4
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85201553"
---
# <a name="connect-with-the-windows-desktop-client"></a>Ligar ao cliente de Ambiente de Trabalho do Windows

> Aplica-se a: Windows 7, Windows 10 e Windows 10 IoT Enterprise

Pode aceder aos recursos de Desktop Virtual do Windows em dispositivos com Windows 7, Windows 10 e Windows 10 IoT Enterprise utilizando o cliente Windows Desktop.

>[!NOTE]
>O cliente Windows falha automaticamente na versão virtual do Windows Desktop Fall 2019. No entanto, se o cliente detetar que o utilizador também tem recursos Azure Resource Manager, adiciona automaticamente os recursos ou notifica o utilizador de que está disponível.

> [!IMPORTANT]
> O Windows Virtual Desktop não suporta o cliente RemoteApp e Desktop Connections (RADC) ou o cliente Remote Desktop Connection (MSTSC).

> [!IMPORTANT]
> O Windows Virtual Desktop não suporta atualmente o cliente remote desktop a partir da Windows Store. O suporte a este cliente será adicionado num lançamento futuro.

## <a name="install-the-windows-desktop-client"></a>Instale o cliente windows desktop

Escolha o cliente que corresponda à sua versão do Windows:

- [Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Janelas ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Pode instalar o cliente para o utilizador atual, que não requer direitos de administração, ou o seu administrador pode instalar e configurar o cliente para que todos os utilizadores do dispositivo possam aceder ao mesmo.

Uma vez instalado, o cliente pode ser lançado a partir do menu Iniciar, procurando por **Desktop Remoto**.

## <a name="subscribe-to-a-feed"></a>Subscreva um feed

Obtenha a lista de recursos geridos ao seu dispor, subscrevendo o feed fornecido pelo seu administrador. A subscrição disponibiliza os recursos no seu PC local.

Para subscrever um feed:

1. Abra o cliente windows desktop.
2. **Selecione Subscrever** na página principal para ligar ao serviço e recuperar os seus recursos.
3. Inscreva-se na sua conta de utilizador quando solicitado.

Depois de iniciar sedutada com sucesso, deverá ver uma lista dos recursos a que pode aceder.

Pode lançar recursos por um de dois métodos.

- A partir da página principal do cliente, clique duas vezes num recurso para lançá-lo.
- Lance um recurso como normalmente faria com outras aplicações a partir do Menu Iniciar.
  - Também pode pesquisar as aplicações na barra de pesquisa.

Uma vez subscrito um feed, o conteúdo do feed é atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base em alterações efetuadas pelo seu administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como utilizar o cliente Windows Desktop, confira [Consulte o cliente do Windows Desktop.](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)
