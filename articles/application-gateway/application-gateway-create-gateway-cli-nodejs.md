---
title: Criar um Gateway de aplicação do Azure - CLI clássica do Azure
description: Saiba como criar um Gateway de aplicação com a CLI clássica do Azure no Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: 7107f45253c4f13b3378489726bf5034e104fa30
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095987"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Criar um gateway de aplicação com a CLI do Azure

O Application Gateway do Azure é um balanceador de carga de 7 camadas. Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local. Gateway de aplicação tem as seguintes funcionalidades de entrega de aplicação: Balanceamento de carga HTTP, afinidade por sessões com base no cookie e descarga de Secure Sockets Layer (SSL), sondas de estado de funcionamento personalizados e suporte para múltiplos sites.

## <a name="prerequisite-install-the-azure-cli"></a>Pré-requisito: Instalar a CLI do Azure

Para efetuar os passos neste artigo, precisa [instalar a CLI do Azure](../xplat-cli-install.md) e precisa [inicie sessão no Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Se não tiver uma conta do Azure, precisa de um. Subscreva [uma avaliação gratuita aqui](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Cenário

Neste cenário, saiba como criar um gateway de aplicação do portal do Azure.

Neste cenário irão:

* Crie um gateway de aplicação médios com duas instâncias.
* Crie uma rede virtual denominada ContosoVNET com um bloco CIDR reservado de 10.0.0.0/16.
* Crie uma sub-rede denominada subnet01 que utiliza 10.0.0.0/28 como bloco CIDR.

> [!NOTE]
> Sondas de configuração adicional do gateway de aplicação, incluindo o estado de funcionamento personalizado, o conjunto de endereços de back-end e regras adicionais são configurados após a configuração do gateway de aplicação e não durante a implementação inicial.

## <a name="before-you-begin"></a>Antes de começar

O Gateway de aplicação do Azure requer a sua própria sub-rede. Ao criar uma rede virtual, certifique-se de que deixe espaço suficiente no endereço ter várias sub-redes. Depois de implementar um gateway de aplicação a uma sub-rede, gateways de aplicação apenas adicionais podem ser adicionados à sub-rede.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra o **linha de comandos do Microsoft Azure**e iniciar sessão.

```azurecli-interactive
az login
```

Depois de digitar o exemplo anterior, é fornecido um código. Navegue para https://aka.ms/devicelogin num browser para continuar o início de sessão no processo.

![o início de sessão do cmd que mostra dispositivos][1]

No browser, introduza o código que recebeu. Será redirecionado para uma página de início de sessão.

![browser para introduzir o código][2]

Uma vez que foi introduzido o código tem sessão iniciada, feche o navegador para, avance para o cenário.

![iniciar sessão com êxito][3]

## <a name="switch-to-resource-manager-mode"></a>Mudar para o modo do Resource Manager

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar o gateway de aplicação, é criado um grupo de recursos para conter o gateway de aplicação. O seguinte mostra o comando.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Depois de criar o grupo de recursos, uma rede virtual é criada para o gateway de aplicação.  No exemplo a seguir, o espaço de endereços era como 10.0.0.0/16, conforme definido nas notas para o cenário anterior.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Criar uma sub-rede

Depois de criar a rede virtual, é adicionada uma sub-rede para o gateway de aplicação.  Se planeia utilizar o gateway de aplicação com uma aplicação web alojada na mesma rede virtual que o gateway de aplicação, certifique-se de que deixar espaço suficiente para outra sub-rede.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Assim que a rede virtual e sub-rede são criados, os pré-requisitos para o gateway de aplicação estão completos. Além de um certificado. pfx exportado anteriormente e a palavra-passe para o certificado são necessários para o passo seguinte: Os endereços IP utilizados para o back-end são os endereços IP para o seu servidor de back-end. Estes valores podem ser IPs privados na rede virtual, ips públicos ou nomes de domínio completamente qualificado para os servidores de back-end.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> Para obter uma lista de parâmetros que podem ser fornecidas durante a criação, execute o seguinte comando: **criar gateway de aplicação de rede do azure – ajudar**.

Este exemplo cria um gateway de aplicação básico com configurações padrão para o serviço de escuta, o conjunto de back-end, definições de http de back-end e regras. Pode modificar estas definições de acordo com a implementação após o aprovisionamento é efetuada com êxito.
Se já tiver a sua aplicação web definida com o conjunto de back-end nos passos anteriores, uma vez criada, balanceamento de carga começa.

## <a name="next-steps"></a>Passos Seguintes

Saiba como criar sondas de estado de funcionamento personalizadas, visite a página [criar uma sonda de estado de funcionamento personalizados](application-gateway-create-probe-portal.md)

Saiba como configurar a descarga de SSL e tomar a descriptografia SSL dispendiosa para os servidores web, visite a página [configurar a descarga de SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
