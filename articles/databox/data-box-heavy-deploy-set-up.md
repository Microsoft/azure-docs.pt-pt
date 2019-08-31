---
title: Tutorial para configurar o Azure Data Box Heavy | Microsoft Docs
description: Saiba como cabear e conectar seu Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: b6b353e0e01f3f598048e5fbb2682603045b1037
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164466"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Tutorial: Cabo e conecte-se à sua Azure Data Box Heavy

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Introdução ao Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

Este tutorial descreve como conectar cabos, conexões e ligar o Azure Data Box Heavy.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Cabear seu Data Box Heavy
> * Conectar-se ao seu Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Este guia fornece instruções sobre como revisar os pré-requisitos, cabos e conectar seu dispositivo, copiar dados, carregar no Azure e, em seguida, verificar os dados carregados.

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Você concluiu o [tutorial: Order Azure Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Você recebeu sua Data Box Heavy e o status do pedido no portal é **entregue**.
3. Você analisou as [diretrizes de segurança do data Box Heavy](data-box-safety.md).
4. Você deve ter acesso a um site simples no datacenter com proximidade com uma conexão de rede disponível que possa acomodar um dispositivo com essa superfície. Este dispositivo não pode ser montado em um rack.
5. Você recebeu quatro cabos de alimentação com aterramento para usar com seu dispositivo de armazenamento.
6. Deve ter um computador anfitrião ligado à rede do datacenter. Seu Data Box Heavy copiará os dados deste computador. O computador host deve executar um [sistema operacional com suporte](data-box-heavy-system-requirements.md).
7. O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. 
8. Você precisa ter um laptop com cabo RJ-45 para se conectar à interface do usuário local e configurar o dispositivo. Use o laptop para configurar cada nó do dispositivo uma vez.
9. É necessário um cabo de 1 40 Gbps ou um cabo de 10 Gbps por nó de dispositivo.
    - Escolha os cabos que são compatíveis com a interface de rede [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) .
    - Para o cabo de 40 Gbps, a extremidade do dispositivo do cabo precisa ser QSFP +.
    - Para o cabo de 10 Gbps, você precisa de um cabo SFP + que se conecte a um comutador de 10 Gbps em uma extremidade, com um adaptador QSFP + para SFP + (ou o adaptador QSA) para o final que se conecta ao dispositivo.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Cabear o dispositivo para energia

Execute as etapas a seguir para conectar o dispositivo.

1. Inspecione o dispositivo em busca de qualquer evidência de adulteração ou outros danos óbvios. Se o dispositivo estiver adulterado ou gravemente danificado, não continue. [Entre em contato suporte da Microsoft](data-box-disk-contact-microsoft-support.md) imediatamente para ajudá-lo a avaliar se o dispositivo está em boas condições de funcionamento e se eles precisam enviar uma substituição.
2. Mova o dispositivo para o site de instalação.

    ![Site de instalação do dispositivo Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Bloqueie os conversões posteriores no dispositivo, conforme mostrado abaixo.

    ![Data Box Heavy os convertidores de dispositivo bloqueados](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Localize os botões que desbloqueiam a frente e as portas traseiras do dispositivo. Desbloqueie e mova a porta frontal até que ela seja liberada com o lado do dispositivo. Repita isso com a porta traseira também.
    Ambas as portas devem permanecer abertas quando o dispositivo estiver operacional para permitir o fluxo de ar de frente para trás ideal por meio do dispositivo.

    ![Data Box Heavy portas abertas](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. A bandeja na parte posterior do dispositivo deve ter quatro cabos de alimentação. Remova todos os cabos da bandeja e coloque-os de lado.

    ![Data Box Heavy cabos de alimentação na bandeja](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. A próxima etapa é identificar as várias portas na parte posterior do dispositivo. Há dois nós de dispositivo, **Node1** e **NODE2**. Cada nó tem quatro interfaces de rede, **Gerenciamento**, **data1**, **data2**, **DATA3**. O **MGMT** é usado para configurar o gerenciamento durante a configuração inicial do dispositivo. Data1-**DATA3** são portas de dados. As portas **MGMT** e **DATA3** são 1 Gbps, enquanto **dados1**, **data2** pode funcionar como portas de 40 Gbps ou portas de 10 Gbps. Na parte inferior dos dois nós de dispositivo, há quatro PSUs (unidades de fonte de alimentação) que são compartilhadas entre os dois nós de dispositivo. À medida que você enfrenta esse dispositivo, os **PSUs** são **PSU1**, **PSU2**, **PSU3**e **PSU4** da esquerda para a direita.

    ![Portas Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Conecte todos os quatro cabos de alimentação às fontes de alimentação do dispositivo. Os LEDs verdes acendem e piscam.
8. Use os botões de energia no plano frontal para ativar os nós de dispositivo. Mantenha o botão de energia pressionado por alguns segundos até que as luzes azuis apareçam. Todos os LEDs verdes das fontes de alimentação na parte posterior do dispositivo agora devem ser sólidos. O painel operacional frontal do dispositivo também contém LEDs de falha. Quando um LED de falha está aceso, ele indica uma PSU com falha ou um ventilador ou um problema com as unidades de disco.  

    ![Data Box Heavy painel de operações frontal](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Primeiro nó do cabo para rede

Em um dos nós do dispositivo, execute as etapas a seguir para conectar a rede.

1. Use um cabo de rede CAT 6 RJ-45 (cabo azul na imagem) para conectar o computador host à porta de gerenciamento de 1 Gbps.
2. Use um cabo QSFP + (fibra ou cobre) para conectar pelo menos 1 40-Gbps (preferencial em 1 Gbps) a interface de rede para dados. Se estiver usando uma opção de 10 Gbps, use um cabo SFP + com um adaptador QSFP + para SFP + (o adaptador QSA) para conectar a interface de rede 40 Gbps para dados.

    ![Portas Data Box Heavy conectadas](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DADOS 1 e DATA2 são alternados e não correspondem ao que é exibido na interface do usuário da Web local.
    > O adaptador de cabo de 40 Gbps se conecta quando inserido da maneira mostrada abaixo.

    ![Adaptador de cabo Data Box Heavy 40-Gbps](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Configurar o primeiro nó

Execute as etapas a seguir para configurar seu dispositivo usando a configuração local e a portal do Azure.

1. Transfira as credenciais do dispositivo a partir do portal. Aceda a **Geral > Detalhes do dispositivo**. Copie a **Palavra-passe do dispositivo**. Essas senhas são vinculadas a uma ordem específica no Portal. Correspondendo aos dois nós no Data Box Heavy, você verá os dois números de série do dispositivo. A senha de administrador do dispositivo para ambos os nós é a mesma.

    ![Data Box Heavy credenciais do dispositivo](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Conecte sua estação de trabalho cliente ao dispositivo por meio de um cabo de rede CAT6 RJ-45.
3. Configure o adaptador Ethernet no computador que você está usando para se conectar ao dispositivo com um endereço `192.168.100.5` IP estático e uma sub-rede. `255.255.255.0`

    ![Data Box Heavy conecta-se à interface do usuário da Web local](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Conecte-se à interface do usuário da Web local do dispositivo na seguinte `http://192.168.100.10`URL:. Clique em **avançado** e, em seguida, clique em **prosseguir para 192.168.100.10 (não seguro)** .
5. Verá uma página para **Iniciar sessão** na IU da Web local.
    
    - Um dos números de série do nó nesta página corresponde na interface do usuário do portal e na interface do usuário da Web local. Anote o número do nó para o mapeamento de número de série. Há dois nós e dois números de série de dispositivo no Portal. Esse mapeamento ajuda a entender qual nó corresponde a qual número de série.
    - O dispositivo está bloqueado neste momento.
    - Forneça a senha de administrador do dispositivo que você obteve na etapa anterior para entrar no dispositivo. Clique em **Iniciar sessão**.

    ![Entrar no Data Box Heavy interface do usuário da Web local](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. No painel, verifique se as interfaces de rede estão configuradas. Há quatro interfaces de rede em seu nó de dispositivo, dois 1 Gbps e 2 40 Gbps. Uma das interfaces de 1 Gbps é uma interface de gerenciamento e, portanto, não pode ser configurada pelo usuário. As três interfaces de rede restantes são dedicadas aos dados e podem ser configuradas pelo usuário.

- Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente.
- Se o DHCP não estiver habilitado, vá para definir interfaces de rede e atribua IPs estáticos, se necessário.

    ![Data Box Heavy o nó 1 do painel](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Configurar o segundo nó

Siga as etapas detalhadas em [Configurar o primeiro nó](#configure-first-node) para o segundo nó do dispositivo.

![Nó do painel de Data Box Heavy 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Quando a configuração do dispositivo estiver concluída, pode ligar às partilhas do dispositivo e copiar os dados do seu computador para o dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu sobre tópicos Azure Data Box Heavy como:

> [!div class="checklist"]
> * Cabear seu Data Box Heavy
> * Conectar-se ao seu Data Box Heavy

Avance para o próximo tutorial para saber como copiar dados em seu Data Box Heavy.

> [!div class="nextstepaction"]
> [Copiar dados para o Azure Data Box](./data-box-heavy-deploy-copy-data.md)

::: zone-end
