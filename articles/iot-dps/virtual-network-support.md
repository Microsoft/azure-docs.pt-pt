---
title: Suporte do Serviço de Provisionamento de Dispositivos Azure IoT (DPS) para redes virtuais
description: Como utilizar o padrão de conectividade das redes virtuais com o Serviço de Provisionamento de Dispositivos Azure IoT (DPS)
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: f1409a931195d236b2729e629e4603c606137593
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94959786"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Suporte do Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS) para redes virtuais

Este artigo introduz o padrão de conectividade da rede virtual (VNET) para dispositivos IoT que adquirem com hubs IoT usando DPS. Este padrão proporciona conectividade privada entre os dispositivos, DPS e o hub IoT dentro de um Azure VNET propriedade do cliente. 

Na maioria dos cenários em que o DPS está configurado com um VNET, o seu IoT Hub também será configurado no mesmo VNET. Para obter informações mais específicas sobre suporte e configuração VNET para IoT Hubs, consulte o [suporte de rede virtual IoT Hub](../iot-hub/virtual-network-support.md).



## <a name="introduction"></a>Introdução

Por padrão, os nomes de anfitriões DPS mapeiam para um ponto final público com um endereço IP publicamente roteado através da Internet. Este ponto final público é visível para todos os clientes. O acesso ao ponto final público pode ser tentado por dispositivos IoT através de redes de vastas áreas, bem como redes no local.

Por várias razões, os clientes podem querer restringir a conectividade aos recursos da Azure, como o DPS. Estas razões incluem:

* Evitar a exposição à ligação através da Internet pública. A exposição pode ser reduzida introduzindo camadas adicionais de segurança através do isolamento do nível de rede para o seu hub IoT e recursos DPS

* Permitir uma experiência de conectividade privada a partir dos seus ativos de rede no local, garantindo que os seus dados e tráfego são transmitidos diretamente para a rede de espinha dorsal Azure.

* Prevenir ataques de exfiltração de redes sensíveis no local. 

* Seguindo padrões de conectividade estabelecidos a nível Azure utilizando [pontos finais privados](../private-link/private-endpoint-overview.md).

As abordagens comuns para restringir a conectividade incluem [regras de filtro IP DPS](./iot-dps-ip-filtering.md) e rede virtual (VNET) com [pontos finais privados](../private-link/private-endpoint-overview.md). O objetivo deste artigo é descrever a abordagem VNET para DPS utilizando pontos finais privados. 

Os dispositivos que operam em redes no local podem utilizar [rede privada virtual (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou um espremiamento privado [ExpressRoute](https://azure.microsoft.com/services/expressroute/) para ligar a um VNET em Azure e aceder a recursos DPS através de pontos finais privados. 

Um ponto final privado é um endereço IP privado atribuído dentro de um VNET propriedade do cliente pelo qual um recurso Azure é acessível. Ao ter um ponto final privado para o seu recurso DPS, poderá permitir que os dispositivos que operam dentro do seu VNET solicitem o fornecimento pelo seu recurso DPS sem permitir o tráfego para o ponto final público.


## <a name="prerequisites"></a>Pré-requisitos

Antes de proceder, assegure-se de que sejam cumpridos os seguintes requisitos:

* O seu recurso DPS já está criado e ligado aos seus hubs IoT. Para obter orientações sobre a criação de um novo recurso DPS, consulte, [Crie o Serviço de Provisionamento de Dispositivos IoT Hub com o portal Azure](./quick-setup-auto-provision.md)

* Você alocou um Azure VNET com uma sub-rede na qual o ponto final privado será criado. Para obter mais informações, consulte, [crie uma rede virtual utilizando o Azure CLI](../virtual-network/quick-create-cli.md).

* Para dispositivos que operam dentro de redes no local, crie [a Rede Privada Virtual (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou a [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privada espreitando para o seu Azure VNET.

## <a name="private-endpoint-limitations"></a>Limitações de pontos finais privados

Note as seguintes limitações atuais para o DPS ao utilizar pontos finais privados:

* Os pontos finais privados não funcionarão com DPS quando o recurso DPS e o Hub ligado estão em diferentes nuvens. Por exemplo, [o Governo Azure e o Azure global.](../azure-government/documentation-government-welcome.md)

* Atualmente, [as políticas de atribuição personalizada com Funções Azure](how-to-use-custom-allocation-policies.md) para DPS não funcionarão quando a função Azure é bloqueada a um VNET e pontos finais privados. 

* O suporte VNET atual é apenas para entrada de dados em DPS. A saída de dados, que é o tráfego de DPS para IoT Hub, utiliza um mecanismo interno de serviço-a-serviço em vez de um VNET dedicado. O suporte para o bloqueio total da saída de Erupção baseada em VNET entre DPS e IoT Hub não está atualmente disponível.

* A política de atribuição de latência mais baixa é usada para atribuir um dispositivo ao centro de IoT com a latência mais baixa. Esta política de atribuição não é fiável num ambiente de rede virtual. 

## <a name="set-up-a-private-endpoint"></a>Criar um ponto final privado

Para configurar um ponto final privado, siga estes passos:

1. No [portal Azure,](https://portal.azure.com/)abra o seu recurso DPS e clique no **separador 'Rede'.** Clique **em ligações de ponto final privado e** + ponto final **privado.**

    ![Adicione um novo ponto final privado para DPS](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. Na página _'Criar'_ Bases de ponto final privado, insira as informações mencionadas na tabela abaixo.

    ![Criar pontos finais privados básicos](./media/virtual-network-support/create-private-endpoint-basics.png)

    | Campo | Valor |
    | :---- | :-----|
    | **Subscrição** | Escolha a subscrição Azure desejada para conter o ponto final privado.  |
    | **Grupo de recursos** | Escolha ou crie um grupo de recursos para conter o ponto final privado |
    | **Nome**       | Insira um nome para o seu ponto final privado |
    | **Região**     | A região escolhida deve ser a mesma que a região que contém o VNET, mas não tem de ser igual ao recurso DPS. |

    Clique **em seguida : Recurso** para configurar o recurso que o ponto final privado irá apontar.

3. Na página _'Criar' 'Recurso' de ponto final privado,_ insira as informações mencionadas na tabela abaixo.

    ![Criar recurso de ponto final privado](./media/virtual-network-support/create-private-endpoint-resource.png)

    | Campo | Valor |
    | :---- | :-----|
    | **Subscrição**        | Escolha a subscrição Azure que contém o recurso DPS que o seu ponto final privado irá apontar.  |
    | **Tipo de recurso**       | Escolha **Microsoft.Devices/ProvisioningServices**. |
    | **Recurso**            | Selecione o recurso DPS para o quais o ponto final privado irá mapear. |
    | **Recurso secundário de destino** | Selecione **iotDps**. |

    > [!TIP]
    > As informações sobre o **'Ligar a um recurso Azure' por ID ou** indicação de pseudónimos são fornecidas na secção ['Pedido' uma](#request-a-private-endpoint) secção de ponto final privado neste artigo.


    Clique **em seguida : Configuração** para configurar o VNET para o ponto final privado.

4. Na página Configuração de _ponto final privado,_ escolha a sua rede virtual e a sub-rede para criar o ponto final privado.
 
    Clique **em seguida : Tags**, e opcionalmente forneça quaisquer tags para o seu recurso.

    ![Configure o ponto final privado](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. Clique **em Rever + criar** e, em seguida, **criar** para criar o seu recurso de ponto final privado.


## <a name="request-a-private-endpoint"></a>Solicite um ponto final privado

Pode solicitar um ponto final privado a um recurso DPS através do ID de recursos. Para fazer este pedido, precisa do proprietário do recurso para lhe fornecer o ID do recurso. 

1. O ID de recursos é fornecido no separador propriedades para o recurso DPS, conforme mostrado abaixo.

    ![Separador de propriedades DPS](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > Esteja ciente de que o ID do recurso contém o ID de assinatura. 

2. Assim que tiver o ID do recurso, siga os passos acima na [Configuração de um ponto final privado](#set-up-a-private-endpoint) para o passo 3 na página De recurso de ponto final _privado._ Clique **em Ligar a um recurso Azure por ID ou pseudónimo** de recurso e introduza as informações na tabela seguinte. 

    | Campo | Valor |
    | :---- | :-----|
    | **ID de recursos ou pseudónimo** | Introduza o ID do recurso para o recurso DPS. |
    | **Recurso secundário de destino** | Insira **iotDps** |
    | **Mensagem de pedido** | Introduza uma mensagem de pedido para o proprietário do recurso DPS.<br>Por exemplo, <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    Clique **em seguida : Configuração** para configurar o VNET para o ponto final privado.

3. Na página De Configuração de _ponto final privado,_ escolha a rede virtual e a sub-rede para criar o ponto final privado.
 
    Clique **em seguida : Tags**, e opcionalmente forneça quaisquer tags para o seu recurso.

4. Clique **em Rever + criar** e, em seguida, **criar** para criar o seu pedido de ponto final privado.

5. O proprietário do DPS verá o pedido de ponto final privado na lista **de ligações de ponto final privado** no separador de rede DPS. Nessa página, o proprietário pode **aprovar** ou **rejeitar** o pedido de ponto final privado, conforme mostrado abaixo.

    ![Aprovação do DPS](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>Preços de pontos finais privados

Para obter detalhes sobre os preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).



## <a name="next-steps"></a>Passos seguintes

Use os links abaixo para saber mais sobre as funcionalidades de segurança DPS:

* [Segurança](./concepts-service.md#attestation-mechanism)
* [Suporte TLS 1.2](tls-support.md)