---
title: Configurar o proxy Do Centro de Distribuição de Chaves Kerberos Windows Virtual Desktop - Azure
description: Como configurar uma piscina de anfitriões virtual do Windows desktop para usar um proxy do Centro de Distribuição de Chaves Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99219660"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Configure um proxy do Centro de Distribuição de Chaves Kerberos (pré-visualização)

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo irá mostrar-lhe como configurar um proxy (preview) do Centro de Distribuição de Chaves Kerberos (KDC) para a sua piscina de anfitrião. Este proxy permite que as organizações autentem com Kerberos fora dos limites da sua empresa. Por exemplo, pode utilizar o proxy KDC para ativar a autenticação do Smartcard para clientes externos.

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

## <a name="next-steps"></a>Passos seguintes

O papel RDGateway em Serviços remotos de desktop inclui um serviço de procuração KDC. Consulte [a função De Gateway RD no Windows Virtual Desktop](rd-gateway-role.md) para saber como configurar um para ser um alvo para o Windows Virtual Desktop.
