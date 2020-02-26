---
title: Configure regras de equilíbrio de carga e saída utilizando o portal Azure
titleSuffix: Azure Load Balancer
description: Este artigo mostra como configurar regras de equilíbrio de carga e saída no Standard Load Balancer utilizando o portal Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590016"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Configure regras de equilíbrio de carga e saída no Balancer de Carga Padrão utilizando o portal Azure

Este artigo mostra-lhe como configurar regras de saída no Standard Load Balancer utilizando o portal Azure.  

O recurso do equilibrador de carga contém duas extremidades dianteiras e as suas regras associadas. Tens uma frente para o trânsito de entrada e outra frente para o trânsito de saída.  

Cada extremidade frontal faz referência a um endereço IP público. Neste cenário, o endereço IP público para o tráfego de entrada é diferente do endereço para o tráfego de saída.   A regra de equilíbrio de carga proporciona apenas o equilíbrio da carga de entrada. A regra de saída controla a tradução de endereços de rede de saída (NAT) para o VM.  

O cenário utiliza duas piscinas de back-end: uma para tráfego de entrada e outra para tráfego de saída. Estas piscinas ilustram a capacidade e proporcionam flexibilidade para o cenário.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Nesta secção, cria-se um equilibrador de carga que carregará as máquinas virtuais de equilíbrio. Pode criar um equilibrador de carga pública ou um equilibrador interno de carga. Quando cria um equilibrador de carga pública, cria-se um novo endereço IP público que é configurado como o frontend para o equilibrador de carga. O frontend será nomeado **LoadBalancerFrontEnd** por padrão.

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso** > **Networking** > **Load Balancer**.
2. No separador Basics da página de equilíbrio de **carga Criar,** introduza ou selecione as **seguintes** informações:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | Selecione **Criar novo** e digitar **o myResourceGroupSLB** na caixa de texto.|
    | Nome                   | **myLoadBalancer**                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Public**.                                        |
    | SKU           | Selecione **Standard** ou **Basic**. A Microsoft recomenda a Standard para cargas de trabalho de produção. |
    | Endereço IP público | Selecione **Criar novo**. Se tiver um IP público existente, gostaria de utilizar, selecione **Use existente**.  O IP público existente deve ser **standard** SKU.  Os IPs públicos básicos não são compatíveis com o equilíbrio de carga **SKU Padrão.**  |
    | Nome do endereço IP público              | Digite **o meu PublicIP** na caixa de texto.|
    | Zona de disponibilidade | Selecione **Zona redundante** para criar um equilibrador de carga resiliente. Para criar um Balancer de Carga zonal, selecione uma zona específica de 1, 2 ou 3 |

3. Aceite as predefinições para o resto da configuração.
4. Selecione **Review + criar**

    > [!IMPORTANT]
    > O resto deste quickstart assume que o **Standard** SKU é escolhido durante o processo de seleção SKU acima.

5. No **'Rever + criar** separador', selecione **Criar**.   

    ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, irá configurar as definições de balanceador de carga de um conjunto de endereços de back-end e de uma sonda de estado de funcionamento, bem como especificar uma regra de balanceador.

### <a name="create-a-backend-pool"></a>Criar um conjunto de back-ends

Um conjunto de endereços de backend contém os endereços IP dos NICs virtuais na piscina de backend. Crie o conjunto de endereços backend **myBackendPool** para incluir máquinas virtuais para equilibrar o tráfego de internet de equilíbrio de carga.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** da lista de recursos.
2. Em **Definições,** selecione **piscinas backend**e, em seguida, selecione **Adicionar**.
3. Na página adicionar uma página **de piscina de backend,** para nome, digite **myBackendPool,** como o nome para a sua piscina de backend, e, em seguida, selecione **Adicionar**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Uma sonda de saúde é usada para monitorizar o estado da sua aplicação. A sonda de saúde adiciona ou remove VMs do equilibrador de carga com base na sua resposta aos controlos de saúde. Crie uma sonda de estado de funcionamento, **myHealthProbe**, para monitorizar o estado de funcionamento das VMs.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** da lista de recursos.
2. Em **Definições,** selecione **sondas de saúde**e, em seguida, selecione **Adicionar**.
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza **o myHealthProbe**. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira **80**.|
    | Intervalo | Introduza **15** para o número de **intervalos** em segundos entre as tentativas de sonda. |
    | Limiar insalubre | Selecione **2** para o número de falhas de **limiar não saudáveis** ou de sonda consecutivas que devem ocorrer antes de um VM ser considerado insalubre.|
    | | |
4. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Crie uma regra de balanceador de carga
É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. 

Define o:
 - Configuração IP frontend para o tráfego de entrada.
 - A piscina IP de backend para receber o tráfego.
 - A fonte e a porta de destino necessárias. 

Na secção seguinte, cria-se um:
 - Balancer de carga regra **myHTTPRule** para ouvir a porta 80.
 - Frontend **LoadBalancerFrontEnd**.
 - Backend address pool **myBackEndPool** também usando a porta 80. 

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** da lista de recursos.
2. Em **Definições,** selecione regras de **equilíbrio de carga**e, em seguida, selecione **Adicionar**.
3. Utilize estes valores para configurar a regra de equilíbrio de carga:
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza **o meuHTTPRule**. |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira **80**.|
    | Porto backend | Insira **80**. |
    | Piscina de backend | Selecione **myBackendPool**.|
    | Sonda de estado de funcionamento | Selecione **myHealthProbe**. |
    | Criar regras implícitas de saída | Selecione **Não**. Criaremos regras de saída numa secção posterior usando um IP público dedicado. |
4. Deixe o resto das predefinições e, em seguida, selecione **OK**.

## <a name="create-outbound-rule-configuration"></a>Criar configuração de regra de saída
Regras de saída do equilibrador de carga configuram SNAT de saída para VMs na piscina de backend. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Criar um endereço IP público de saída e frontend

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** da lista de recursos.

2. Em **definições,** selecione **a configuração IP frontend**e, em seguida, selecione **Adicionar**.

3. Utilize estes valores para configurar a configuração IP frontal para saída:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza **loadBalancerFrontEndOutbound**. |
    | Versão do IP | Selecione **IPv4**. |
    | Tipo IP | Selecione **endereço IP**.|
    | Endereço IP público | Selecione **Criar novo**. No **endereço Ip do público,** insira **o myPublicIPOutbound**.  Selecione **OK**. |

4. Selecione **Adicionar**.

### <a name="create-an-outbound-backend-pool"></a>Crie uma piscina de backend de saída

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** da lista de recursos.

2. Em **Definições,** selecione **piscinas backend**e, em seguida, selecione **Adicionar**.

3. Na página **de piscina adicionar uma reserva de backend,** para nome, digite **myBackendPoolOutbound,** como o nome para a sua piscina de backend, e, em seguida, selecione **Adicionar**.

### <a name="create-outbound-rule"></a>Criar regra de saída

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** da lista de recursos.

2. Em **Definições,** selecione **as regras de saída**e, em seguida, selecione **Adicionar**.

3. Utilize estes valores para configurar as regras de saída:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza **o myOutboundRule**. |
    | Endereço IP frontend | Selecione **LoadBalancerFrontEndOutbound**. |
    | Tempo limite (minutos) | Mova o slider para **15 minutos.|
    | TCP Reset | Selecione **Ativado**.|
    | Piscina de backend | Selecione **myBackendPoolOutbound** |
    | Atribuição portuária -> Atribuição portuária | **Selecione manualmente, escolha o número de portas de saída** |
    | Portas de saída -> Escolha por | Selecione **Portas por instância** |
    | Portas de saída -> Portos por exemplo | **Insira 10.000.** |

4. Selecione **Adicionar**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupSLB** que contém o equilibrador de carga e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo:
 - Criaste um equilibrador de carga padrão.
 - Configurar as regras de tráfego de equilíbrio de carga e de entrada.
 - Configurou uma sonda de saúde para os VMs na piscina traseira. 

Para saber mais, continue aos [tutoriais do Azure Load Balancer.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
