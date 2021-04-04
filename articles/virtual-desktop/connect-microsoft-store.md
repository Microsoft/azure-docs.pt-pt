---
title: Ligue-se ao cliente da Microsoft Store - Azure
description: Como ligar-se ao Windows Virtual Desktop utilizando o cliente da Microsoft Store.
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9bab2a11ff9e7907621087e5027929e3e42eaf29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91744866"
---
# <a name="connect-with-the-microsoft-store-client"></a>Ligar ao cliente da Microsoft Store

>Aplica-se a: Windows 10.

Pode aceder aos recursos do Windows Virtual Desktop em dispositivos com o Windows 10.

## <a name="install-the-microsoft-store-client"></a>Instale o cliente da Microsoft Store

Pode instalar o cliente para o utilizador atual, o que não requer direitos de administração. Em alternativa, o seu administrador pode instalar e configurar o cliente para que todos os utilizadores do dispositivo possam aceder ao mesmo.

Uma vez instalado, o cliente pode ser lançado a partir do menu Iniciar, procurando por Desktop Remoto.

Para começar, [faça o download e instale o cliente na Microsoft Store](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS).

## <a name="subscribe-to-a-workspace"></a>Subscreva um espaço de trabalho

Subscreva o espaço de trabalho fornecido pelo seu administrador para obter a lista de recursos geridos a que pode aceder no seu PC.

Para subscrever um espaço de trabalho:

1. No ecrã 'Centro de Ligação', toque **+Adicionar,** toque em **Espaços de Trabalho**.
2. Introduza o URL do Espaço de Trabalho no campo URL do Espaço de Trabalho fornecido pela sua administração. O URL do espaço de trabalho pode ser um URL ou um endereço de e-mail.
   
   - Se estiver a utilizar um URL workspace, use o URL que o administrador lhe deu.
   - Se estiver a ligar-se a partir do Windows Virtual Desktop, utilize um dos seguintes URLs dependendo da versão do serviço que está a utilizar:
       - Windows Virtual Desktop (clássico): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` .
       - Windows Virtual Desktop: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` .
  
3. Toque **em Subscrever**.
4. Forneça as suas credenciais quando solicitado.
5. Após a subscrição, os espaços de trabalho devem ser apresentados no Centro de Ligação.

Os espaços de trabalho podem ser adicionados, alterados ou removidos com base em alterações efetuadas pelo seu administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente da Microsoft Store, confira [Consulte o cliente da Microsoft Store.](/windows-server/remote/remote-desktop-services/clients/windows/)