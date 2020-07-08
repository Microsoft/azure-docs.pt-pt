---
title: Configurar regras de equilíbrio e saída de carga utilizando o portal Azure
titleSuffix: Azure Load Balancer
description: Este artigo mostra como configurar as regras de equilíbrio e saída de carga no Balanceador de Carga Padrão utilizando o portal Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 2dff916bf005b307f27264ad7a17864fbba50872
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367398"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Configure as regras de equilíbrio e saída de carga no Balanceador de Carga Padrão utilizando o portal Azure

Este artigo mostra-lhe como configurar as regras de saída no Balanceador de Carga Padrão utilizando o portal Azure.  

O recurso do balançador de carga contém duas extremidades dianteiras e as suas regras associadas. Tem uma frente para o tráfego de entrada e outra frente para o tráfego de saída.  

Cada frontal refere um endereço IP público. Neste cenário, o endereço IP público para o tráfego de entrada é diferente do endereço para tráfego de saída.   A regra de equilíbrio de carga proporciona apenas o equilíbrio da carga de entrada. A regra de saída controla a tradução de endereço de rede de saída (NAT) para o VM.  

O cenário usa duas piscinas traseiras: uma para o tráfego de entrada e outra para o tráfego de saída. Estas piscinas ilustram a capacidade e proporcionam flexibilidade para o cenário.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

Nesta secção, cria-se um equilibrador de carga que carregará máquinas virtuais de equilíbrio. Pode criar um equilibrador de carga pública ou um equilibrador interno de carga. Quando cria um equilibrador de carga público, cria-se um novo endereço IP público configurado como frontend para o equilibrador de carga. O frontend será nomeado **LoadBalancerFrontEnd** por padrão.

1. No lado superior esquerdo do ecrã, selecione **Criar um**  >  equilibrador de carga de**rede de**recursos  >  **Load Balancer**.
2. No separador Básicos da página **'Criar balançador** de carga', insira ou selecione as **seguintes** informações:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Subscrição               | Selecione a sua subscrição.    |    
    | Grupo de recursos         | **Selecione Criar novo** e **digite myResourceGroupSLB** na caixa de texto.|
    | Name                   | **myLoadBalancer**                                   |
    | Região         | Selecione **Europa Ocidental**.                                        |
    | Tipo          | Selecione **Público**.                                        |
    | SKU           | Selecione **Standard**. |
    | Endereço IP público | Selecione **Criar novo**. Se tiver um IP público existente que gostaria de utilizar, selecione **Utilizar a existência**.  O IP público existente deve ser **o Standard** SKU.  Os IPs públicos básicos não são compatíveis com **o balanceador de** carga Standard SKU.  |
    | Nome do endereço IP público              | Digite **o myPublicIP** na caixa de texto.|
    | Zona de disponibilidade | Selecione **Zona redundante** para criar um equilibrador de carga resistente. Para criar um Balanceador de Carga zonal, selecione uma zona específica a partir de 1, 2 ou 3 |

3. Aceite os predefinidos para o resto da configuração.
4. **Selecione Review + criar**

    > [!IMPORTANT]
    > O resto deste quickstart pressupõe que **o Standard** SKU é escolhido durante o processo de seleção SKU acima.

5. No **separador 'Rever + criar',** selecione **Criar.**   

    ![Criar um Balanceador de Carga Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta secção, irá configurar as definições de balanceador de carga de um conjunto de endereços de back-end e de uma sonda de estado de funcionamento, bem como especificar uma regra de balanceador.

### <a name="create-a-backend-pool"></a>Criar um conjunto de back-ends

Um conjunto de endereços backend contém os endereços IP dos NICs virtuais na piscina de backend. Crie o pool de endereços de backend **myBackendPool** para incluir máquinas virtuais para equilibrar o tráfego de internet.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.
2. Em **Definições**, selecione **pools backend**e, em seguida, selecione **Adicionar**.
3. Na página de **piscina de backend,** para nome, escreva **myBackendPool,** como o nome da sua piscina de backend e, em seguida, selecione **Add**.

### <a name="create-a-health-probe"></a>Criar uma sonda de estado de funcionamento

Uma sonda de saúde é usada para monitorizar o estado da sua aplicação. A sonda de saúde adiciona ou remove VMs do equilibrador de carga com base na sua resposta aos controlos de saúde. Crie uma sonda de estado de funcionamento, **myHealthProbe**, para monitorizar o estado de funcionamento das VMs.

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.
2. Em **Definições**, selecione **sondas de saúde**e, em seguida, selecione **Adicionar**.
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **o myHealthProbe**. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Insira **80**.|
    | Intervalo | Introduza **15** para o número de **intervalos** em segundos entre tentativas de sonda. |
    | Limiar com funcionamento incorreto | Selecione **2** para o número de **falhas de sonda insalubres** ou falhas de sonda consecutivas que devem ocorrer antes de um VM ser considerado insalubre.|
    | | |
4. Selecione **OK**.

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga
É utilizada uma regra de balanceador de carga para definir a forma como o tráfego é distribuído pelas VMs. 

Define-se:
 - Configuração IP frontend para o tráfego de entrada.
 - O backend IP pool para receber o tráfego.
 - A fonte necessária e o porto de destino. 

Na seguinte secção, cria-se um:
 - O balanceador de carga regra **myHTTPRule** para ouvir a porta 80.
 - Frontend **LoadBalancerFrontEnd**.
 - Pool de endereços **backend myBackEndPool** também usando a porta 80. 

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.
2. Em **Definições**, selecione **regras de equilíbrio de carga**e, em seguida, selecione **Adicionar**.
3. Utilize estes valores para configurar a regra de equilíbrio de carga:
    
    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **myHTTPRule**. |
    | Protocolo | Selecione **TCP**. |
    | Porta | Insira **80**.|
    | Porto backend | Insira **80**. |
    | Conjunto de back-end | Selecione **myBackendPool**.|
    | Sonda de estado de funcionamento | Selecione **myHealthProbe**. |
    | Criar regras implícitas de saída | Selecione **Não**. Criaremos regras de saída numa secção posterior usando um IP público dedicado. |
4. Deixe o resto dos predefinidos e, em seguida, selecione **OK**.

## <a name="create-outbound-rule-configuration"></a>Criar configuração de regras de saída
As regras de saída do balançador de carga configuram sNAT de saída para VMs na piscina de backend. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Criar um endereço IP público de saída e frontend

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **a configuração IP do frontend**e, em seguida, selecione **Adicionar**.

3. Utilize estes valores para configurar a configuração IP frontend para saída:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **LoadBalancerFrontEndOutbound**. |
    | Versão do IP | Selecione **IPv4**. |
    | Tipo IP | Selecione **o endereço IP**.|
    | Endereço IP público | Selecione **Criar novo**. No **endereço IP público Adicionar,** insira **o myPublicIPOutbound**.  Selecione **OK**. |

4. Selecione **Adicionar**.

### <a name="create-an-outbound-backend-pool"></a>Criar uma piscina de backend de saída

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **pools backend**e, em seguida, selecione **Adicionar**.

3. Na página de **piscina de backend,** para nome, escreva **o myBackendPoolOutbound,** como o nome da sua piscina de backend e, em seguida, selecione **Add**.

### <a name="create-outbound-rule"></a>Criar regra de saída

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione **myLoadBalancer** na lista de recursos.

2. Em **Definições**, selecione **as regras de saída**e, em seguida, selecione **Adicionar**.

3. Utilize estes valores para configurar as regras de saída:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Introduza **o meu OutboundRule**. |
    | Endereço IP de front-end | Selecione **LoadBalancerFrontEndOutbound**. |
    | Tempo de 20 minutos (minutos) | Mova o slider para **15 minutos.|
    | TCP Reset | Selecione **Ativado**.|
    | Conjunto de back-end | Selecione **myBackendPoolOutbound** |
    | Dotação portuária -> Dotação portuária | Selecione **Manualmente o número de portas de saída** |
    | Portas de saída -> Escolha por | Selecione **portas por exemplo** |
    | Portos de saída -> Portos por exemplo | Insira **10.000**. |

4. Selecione **Adicionar**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupSLB** que contém o equilibrador de carga e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Próximos passos

Neste artigo:
 - Criaste um balanceador de carga padrão.
 - Configurado tanto as regras de tráfego de entrada como de saída.
 - Configurar uma sonda de saúde para os VMs na piscina traseira. 

Para saber mais, continue os [tutoriais para O Balancer de Carga Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
