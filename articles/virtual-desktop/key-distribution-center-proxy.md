---
title: Configurar o proxy Do Centro de Distribuição de Chaves Kerberos Windows Virtual Desktop - Azure
description: Como configurar uma piscina de anfitriões virtual do Windows desktop para usar um proxy do Centro de Distribuição de Chaves Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 01/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f123659941eaeb3b8ceeb6a999abf836eb9cf5ea
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798419"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>Configure um proxy do Centro de Distribuição de Chaves Kerberos

Este artigo irá mostrar-lhe como configurar um proxy do Centro de Distribiução de Chave Kerberos (KDC) para a sua piscina de anfitrião. Este proxy permite que as organizações autentem com Kerberos fora dos limites da sua empresa. Por exemplo, pode utilizar o proxy KDC para ativar a autenticação do Smartcard para clientes externos.

## <a name="how-to-configure-the-kdc-proxy"></a>Como configurar o representante da KDC

Para configurar o representante da KDC:

1. Inicie sessão no portal do Azure como administrador.

2. Aceda à página de desktop virtual do Windows.

3. Selecione a piscina hospedeira para a seguinte forma ativar o proxy KDC e, em seguida, selecione **As Propriedades RDP**.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da página do portal Azure mostrando um utilizador selecionando piscinas host, em seguida, o nome da piscina de anfitrião exemplo, em seguida, propriedades RDP.](media/rdp-properties.png)

4. Selecione o separador **Avançado** e, em seguida, introduza um valor no seguinte formato sem espaços:

    > kdcproxyname:s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![Uma imagem mostrando o separador Avançado selecionado, com o valor introduzido como descrito no passo 4.](media/advanced-tab-selected.png)

5. Selecione **Guardar**.

6. O pool de anfitriões selecionado deve agora começar a emitir ficheiros de ligação RDP com o campo kdcproxyname que inseriu.

## <a name="next-steps"></a>Próximos passos

O papel RDGateway em Serviços remotos de desktop inclui um serviço de procuração KDC. Consulte [a função De Gateway RD no Windows Virtual Desktop](rd-gateway-role.md) para saber como configurar um para ser um alvo para o Windows Virtual Desktop.
