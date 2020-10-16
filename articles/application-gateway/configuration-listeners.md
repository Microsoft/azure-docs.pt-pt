---
title: Configuração do ouvinte do Azure Application Gateway
description: Este artigo descreve como configurar os ouvintes do Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: ef2ff8924cd8a92c5d2d2e5dd9da6bb74fad1a14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89653224"
---
# <a name="application-gateway-listener-configuration"></a>Configuração do ouvinte do Gateway de Aplicação

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Um ouvinte é uma entidade lógica que verifica os pedidos de ligação recebidas utilizando o endereço porta, protocolo, anfitrião e IP. Ao configurar o ouvinte, deve introduzir valores para estes que correspondam aos valores correspondentes no pedido de entrada no gateway.

Quando cria um gateway de aplicações utilizando o portal Azure, também cria um ouvinte predefinido, escolhendo o protocolo e a porta para o ouvinte. Pode escolher se ativa o suporte HTTP2 no ouvinte. Depois de criar o gateway de aplicações, pode editar as definições desse ouvinte predefinido *(appGatewayHttpListener*) ou criar novos ouvintes.

## <a name="listener-type"></a>Tipo de ouvinte

Quando cria um novo ouvinte, escolhe-se entre [ *o básico* e *o multi-site*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Se quiser que todos os seus pedidos (para qualquer domínio) sejam aceites e reencaminhados para piscinas de backend, escolha o básico. Saiba [como criar uma porta de aplicação com um ouvinte básico.](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- Se pretender encaminhar pedidos para diferentes piscinas de backend com base no cabeçalho do *anfitrião* ou nomes de anfitrião, escolha o ouvinte de vários locais, onde também deve especificar um nome de anfitrião que corresponda ao pedido de entrada. Isto porque o Application Gateway conta com cabeçalhos de anfitrião HTTP 1.1 para hospedar mais de um website no mesmo endereço IP público e porta. Para saber mais, consulte [a hospedagem de vários sites utilizando o Application Gateway](multiple-site-overview.md).

### <a name="order-of-processing-listeners"></a>Ordem de processamento dos ouvintes

Para o V1 SKU, os pedidos são correspondidos de acordo com a ordem das regras e o tipo de ouvinte. Se uma regra com o ouvinte básico vem em primeiro lugar na ordem, é processada primeiro e aceitará qualquer pedido para que a combinação porta e IP. Para evitar isto, configurar as regras com os ouvintes multi-locais primeiro e empurrar a regra com o ouvinte básico para o último da lista.

Para o v2 SKU, os ouvintes multi-sites são processados antes dos ouvintes básicos.

## <a name="front-end-ip-address"></a>Endereço IP de front-end

Escolha o endereço IP frontal que pretende associar a este ouvinte. O ouvinte ouvirá os pedidos de entrada neste IP.

## <a name="front-end-port"></a>Porta frontal

Escolha a porta frontal. Selecione uma porta existente ou crie uma nova. Escolha qualquer valor da [gama permitida de portas.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports) Você pode usar não só portas bem conhecidas, como 80 e 443, mas qualquer porta personalizada permitida que seja adequada. Um porto pode ser usado para ouvintes virados para o público ou para ouvintes de frente para o privado.

## <a name="protocol"></a>Protocolo

Escolha HTTP ou HTTPS:

- Se escolher HTTP, o tráfego entre o cliente e o gateway de aplicação não está encriptado.

- Escolha HTTPS se quiser [a rescisão de TLS](features.md#secure-sockets-layer-ssltls-termination) ou [encriptação TLS de ponta a ponta.](https://docs.microsoft.com/azure/application-gateway/ssl-overview) O tráfego entre o cliente e o portal de aplicações está encriptado. E a ligação TLS termina no portal de aplicação. Se pretender encriptação TLS de ponta a ponta, tem de escolher HTTPS e configurar a definição **HTTP de fundo.** Isto garante que o tráfego é reencrimado quando viaja da porta de entrada da aplicação para a parte de trás.


Para configurar a rescisão de TLS e a encriptação TLS de ponta a ponta, deve adicionar um certificado ao ouvinte para permitir que o gateway da aplicação obtém uma chave simétrica. Isto é ditado pela especificação do protocolo TLS. A chave simétrica é usada para encriptar e desencriptar o tráfego que é enviado para o portal. O certificado gateway deve estar no formato Personal Information Exchange (PFX). Este formato permite-lhe exportar a chave privada que o gateway utiliza para encriptar e desencriptar o tráfego.

## <a name="supported-certificates"></a>Certificados suportados

Ver [visão geral da rescisão de TLS e fim do fim do TLS com Gateway de aplicação](ssl-overview.md#certificates-supported-for-tls-termination)

## <a name="additional-protocol-support"></a>Suporte adicional ao protocolo

### <a name="http2-support"></a>Suporte HTTP2

O suporte ao protocolo HTTP/2 está disponível para clientes que se ligam apenas aos ouvintes do gateway de aplicações. A comunicação para as piscinas de servidor de back-end terminou HTTP/1.1. Por predefinição, o suporte HTTP/2 é desativado. O seguinte corte de código Azure PowerShell mostra como permitir isto:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>Suporte do WebSocket

O suporte webSocket é ativado por padrão. Não existe uma definição configurável para ativar ou desativá-la. Pode utilizar webSockets com ouvintes HTTP e HTTPS.

## <a name="custom-error-pages"></a>Páginas de erro personalizadas

Pode definir erros personalizados a nível global ou ao nível do ouvinte. Mas a criação de páginas de erro personalizadas a nível global a partir do portal Azure não é suportada. Pode configurar uma página de erro personalizada para um erro de firewall de aplicação web 403 ou uma página de manutenção 502 ao nível do ouvinte. Também deve especificar um URL blob acessível ao público para o código de estado de erro dado. Para obter mais informações, consulte [Criar páginas de erro personalizadas do Gateway de Aplicação](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Códigos de erro do Gateway de Aplicação](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Para configurar uma página de erro personalizada global, consulte [a configuração Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

## <a name="tls-policy"></a>Política TLS

Pode centralizar a gestão de certificados TLS/SSL e reduzir a sobrecarga de encriptação-desencriptação para uma fazenda de servidores de back-end. O manuseamento centralizado de TLS também permite especificar uma política central de TLS que se adequa aos seus requisitos de segurança. Pode escolher a política *padrão,* *predefinida*ou *personalizada* TLS.

Configura a política TLS para controlar as versões do protocolo TLS. Pode configurar um gateway de aplicações para utilizar uma versão de protocolo mínimo para apertos de mão TLS de TLS1.0, TLS1.1 e TLS1.2. Por predefinição, os SSL 2.0 e 3.0 estão desativadas e não são configuráveis. Para obter mais informações, consulte a [visão geral da política do Gateway de Aplicação TLS](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Depois de criar um ouvinte, associá-lo a uma regra de encaminhamento de pedidos. Esta regra determina como os pedidos recebidos no ouvinte são encaminhados para a parte de trás.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre as regras de encaminhamento de pedidos](configuration-request-routing-rules.md).