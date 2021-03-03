---
title: Problemas no Servidor de Rota de Azure
description: Saiba como resolver problemas para o Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 02dd9aa74da42f0a5d70de4513b88756a97bea1e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680395"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Problemas no servidor de rota de Azure

> [!IMPORTANT]
> O Azure Route Server (Preview) encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bgp-connectivity-issues"></a>Problemas de conectividade BGP

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>Porque é que o BGP está a espreitar entre o meu NVA e o Azure Route Server a subir e a descer ("flapping")?

A causa da agitação pode ser devido à regulação do temporizador BGP. Por predefinição, o temporizador Keep-alive no Azure Route Server está definido para 60 segundos e o temporizador de espera é de 180 segundos.

### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-goes-down"></a>Por que posso fazer o ping do meu NVA para o BGP peer IP no Azure Route Server mas depois de configurar o BGP a espreitar entre eles, não posso mais ping o mesmo IP? Porque é que o olhar do BGP desce?

Em alguns NVA, precisa de adicionar uma rota estática para a sub-rede Azure Route Server. Por exemplo, se o Azure Route Server estiver em 10.0.255.0/27 e o seu NVA estiver em 10.0.0.0/24, tem de adicionar o seguinte percurso à tabela de encaminhamento no NVA:

| Rota | Próximo Hop |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 é o IP de gateway predefinido na sub-rede onde o seu NVA (ou, mais precisamente, um dos NICs) está hospedado.

## <a name="bgp-route-issues"></a>Problemas de rota BGP

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>Porque é que a minha NVA não recebe rotas do Azure Route Server, mesmo que o olhar do BGP esteja em cima?

O ASN que o Azure Route Server utiliza é 65515. Certifique-se de que configura uma ASN diferente para o seu NVA para que possa ser estabelecida uma sessão "eBGP" entre o seu NVA e o Azure Route Server para que a propagação da rota possa acontecer automaticamente.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>O BGP a espreitar entre o meu NVA e o Azure Route Server está em cima. Vejo rotas trocadas corretamente entre eles. Porque é que as rotas da NVA não estão na tabela de encaminhamento eficaz do meu VM? 

* Se o seu VM estiver no mesmo VNet que o seu NVA e Azure Route Server:

     O Azure Route Server expõe dois IPs pares BGP, que estão hospedados em dois VMs que partilham a responsabilidade de enviar as rotas para todos os outros VMs em execução na sua rede virtual. Cada um dos seus NVA deve configurar duas sessões BGP idênticas (por exemplo, utilizar o mesmo número AS, o mesmo caminho AS e anunciar o mesmo conjunto de rotas) para os dois VMs para que os seus VMs na rede virtual possam obter informações consistentes de encaminhamento do Azure Route Server. Veja o diagrama abaixo.

    ![Diagrama mostrando um aparelho virtual de rede com o Route Server.](./media/faq/network-virtual-appliances.png)

    Se tiver duas ou mais instâncias da NVA, *pode* anunciar diferentes caminhos AS para a mesma rota de diferentes instâncias NVA se quiser designar uma instância NVA como ativa e a outra passiva.

* Se o seu VM estiver numa rede virtual diferente da que acolhe o seu NVA e O Azure Route Server. Verifique se o VNet Peering está ativado entre os dois VNets *e* se o Servidor de Rota Remota está ativado no VNet do seu VM.

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar um Servidor de Rota Azure](quickstart-configure-route-server-powershell.md)
