---
title: Criar um Gateway de Aplicação Azure - ClI clássico azure
description: Saiba como criar um Gateway de Aplicação utilizando o CLI clássico do Azure em Gestor de Recursos
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: ed7bbbf55956d655115b94c8ac46432b3e36c49b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312638"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Criar um portal de aplicação utilizando o Azure CLI

O Application Gateway do Azure é um balanceador de carga de 7 camadas. Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local. O gateway da aplicação tem as seguintes funcionalidades de entrega de aplicações: equilíbrio de carga HTTP, afinidade de sessão baseada em cookies e Segurança da Camada de Transporte (TLS), anteriormente conhecida como Secure Sockets Layer (SSL), descarregamento, sondas de saúde personalizadas e suporte para multi-site.

## <a name="prerequisite-install-the-azure-cli"></a>Pré-requisito: Instalar o Azure CLI

Para realizar os passos neste artigo, precisa de [instalar o Azure CLI](../xplat-cli-install.md) e precisa de [assinar no Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Se não tem uma conta Azure, precisa de uma. Subscreva [uma avaliação gratuita aqui](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Cenário

Neste cenário, aprende-se a criar um portal de aplicação utilizando o portal Azure.

Este cenário irá:

* Crie um gateway de aplicação média com duas instâncias.
* Crie uma rede virtual chamada ContosoVNET com um bloco CIDR reservado de 10.0.0.0.0/16.
* Crie uma subnet chamada subnet01 que utilize 10.0.0.0.0/28 como o seu bloco CIDR.

> [!NOTE]
> Configuração adicional do gateway da aplicação, incluindo sondas de saúde personalizadas, endereços de piscina de backend, e regras adicionais são configuradas após a configuração do gateway da aplicação e não durante a implementação inicial.

## <a name="before-you-begin"></a>Antes de começar

O Portal de Aplicação Azure requer a sua própria sub-rede. Ao criar uma rede virtual, certifique-se de que deixa espaço de endereço suficiente para ter várias subredes. Uma vez implementada uma porta de aplicação para uma subneta, apenas os gateways de aplicação adicionais são capazes de ser adicionados à sub-rede.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra o **Microsoft Azure Command Prompt**e inscreva-se.

```azurecli-interactive
az login
```

Uma vez que digite o exemplo anterior, é fornecido um código. Navegue https://aka.ms/devicelogin para um browser para continuar o sinal no processo.

![cmd mostrando login dispositivo][1]

No navegador, introduza o código que recebeu. É redirecionado para uma página de inscrição.

![navegador para introduzir código][2]

Uma vez que o código tenha sido introduzido, você é assinado, feche o navegador para continuar com o cenário.

![assinado com sucesso em][3]

## <a name="switch-to-resource-manager-mode"></a>Mude para o modo de gestor de recursos

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar o gateway de aplicação, é criado um grupo de recursos para conter o gateway da aplicação. O seguinte mostra o comando.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Uma vez criado o grupo de recursos, é criada uma rede virtual para o gateway da aplicação.  No exemplo seguinte, o espaço de endereço foi de 10.0.0.0.0/16, tal como definido nas notas de cenário anteriores.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Criar uma sub-rede

Após a criação da rede virtual, é adicionada uma subnet para o gateway da aplicação.  Se planeia utilizar o gateway da aplicação com uma aplicação web alojada na mesma rede virtual que o gateway da aplicação, certifique-se de deixar espaço suficiente para outra subnet.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicação

Uma vez criada a rede virtual e a subnet, os pré-requisitos para o gateway da aplicação estão completos. Adicionalmente, um certificado .pfx previamente exportado e a palavra-passe para o certificado são necessários para a seguinte etapa: Os endereços IP utilizados para o backend são os endereços IP para o seu servidor de backend. Estes valores podem ser iPs privados na rede virtual, ips públicos ou nomes de domínio totalmente qualificados para os seus servidores de backend.

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
> Para uma lista de parâmetros que podem ser fornecidos durante a criação executar o seguinte comando: **azure network application-gateway criar --help**.

Este exemplo cria um gateway de aplicação básico com definições padrão para o ouvinte, piscina de backend, configurações de vídeo de backend e regras. Pode modificar estas definições de acordo com a sua implementação uma vez que o fornecimento seja bem sucedido.
Se já tem a sua aplicação web definida com o backend pool nos passos anteriores, uma vez criado, começa o equilíbrio de carga.

## <a name="next-steps"></a>Passos seguintes

Saiba como criar sondas de saúde personalizadas visitando [Criar uma sonda de saúde personalizada](application-gateway-create-probe-portal.md)

Saiba como configurar o Descarregamento de TLS e tirar a dispendiosa desencriptação do TLS dos seus servidores web visitando a [Configure TLS Offload](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
