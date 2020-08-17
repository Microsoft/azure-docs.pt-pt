---
title: Tutorial para configurar o Azure Data Box Heavy| Microsoft Docs
description: Neste tutorial, saiba como instalar os cabos, ligar e ativar o Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: c38ff7e642017afe5f220ae26d3a04c2c0b706ee
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920947"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Tutorial: Instalar os cabos e ligar ao Azure Data Box Heavy

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Introdução ao Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

Este tutorial descreve como instalar os cabos, ligar e ativar o Azure Data Box Heavy.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instalar os cabos do Data Box Heavy
> * Ligar o Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Este guia fornece instruções sobre como rever os pré-requisitos, instalar os cabos, ligar o dispositivo, copiar dados, carregar para o Azure e, em seguida, verificar os dados carregados.

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Encomendar o Azure Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Recebeu o Data Box Heavy e o estado da encomenda no portal é **Entregue**.
3. Reviu as [diretrizes de segurança do Data Box Heavy](data-box-safety.md).
4. Tem de ter acesso a um local plano no datacenter próximo de uma ligação de rede disponível que possa acomodar um dispositivo com estas dimensões. Este dispositivo não pode ser montado num bastidor.
5. Recebeu quatro cabos de alimentação ligados à terra para utilizar com o dispositivo de armazenamento.
6. Deve ter um computador anfitrião ligado à rede do datacenter. O Data Box Heavy irá copiar os dados desse computador. O computador anfitrião deve ser executado num [Sistema operativo suportado](data-box-heavy-system-requirements.md).
7. O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. 
8. Precisa de um portátil com cabo RJ-45 para se ligar à IU local e configurar o dispositivo. Utilize o portátil para configurar cada nó do dispositivo uma vez.
9. Precisa de um cabo de 40 Gbps ou de 10 Gbps por nó de dispositivo.
    - Escolha cabos que sejam compatíveis com a interface de rede [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html).
    - Para o cabo de 40 Gbps, a extremidade do cabo que liga ao dispositivo tem de ser QSFP+.
    - Para o cabo de 10 Gbps, precisa de um cabo SFP+ que se ligue a um comutador de 10 Gbps numa extremidade, com um adaptador QSFP+ para SFP+ (ou o adaptador QSA) para a extremidade que liga ao dispositivo.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Ligar os cabos do dispositivo à energia

Execute os seguintes passos para ligar os cabos do dispositivo.

1. Inspecione o dispositivo em busca de qualquer evidência de adulteração ou outros danos óbvios. Se o dispositivo estiver adulterado ou gravemente danificado, não continue. [Contacte o Suporte da Microsoft](data-box-disk-contact-microsoft-support.md) de imediato para o ajudar a avaliar se o dispositivo está a funcionar corretamente e se é preciso enviar um de substituição.
2. Mova o dispositivo para o local de instalação.

    ![Local de instalação do dispositivo Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Bloqueie os rodízios traseiros do dispositivo, conforme mostrado abaixo.

    ![Rodízios do dispositivo Data Box Heavy bloqueados](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Localize os botões que desbloqueiam as portas dianteira e traseira do dispositivo. Desbloqueie e mova a porta dianteira até que esta esteja alinhada com a parte lateral do dispositivo. Repita este passo com a porta traseira.
    Ambas as portas devem permanecer abertas quando o dispositivo estiver em funcionamento para permitir a circulação ideal do fluxo de ar da parte da frente para a parte traseira do dispositivo.

    ![Portas abertas do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. A bandeja na parte traseira do dispositivo deve ter quatro cabos de alimentação. Remova todos os cabos da bandeja e coloque-os de lado.

    ![Cabos de alimentação do Data Box Heavy na bandeja](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. O próximo passo é identificar as várias portas na parte traseira do dispositivo. Há dois nós do dispositivo, **NODE1** e **NODE2**. Cada nó tem quatro interfaces de rede, **MGMT**, **DATA1**, **DATA2**, **DATA3**. O **MGMT** é utilizado para configurar a gestão durante a configuração inicial do dispositivo. **DATA1**-**DATA3** são portas de dados. As portas **MGMT** e **DATA3** são de 1 Gbps, enquanto as portas **DATA1** e **DATA2** podem funcionar como portas de 40 Gbps ou portas de 10 Gbps. Na parte inferior dos dois nós do dispositivo, existem quatro PSUs (unidades de alimentação) que são partilhadas entre os dois nós do dispositivo. De frente para o dispositivo, as **PSUs** são **PSU1**, **PSU2**, **PSU3** e **PSU4** da esquerda para a direita.

    ![Portas do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Ligue os quatro cabos de alimentação às fontes de energia do dispositivo. Os LEDs verdes acedem e piscam.
8. Utilize os botões para ligar/desligar no plano frontal para ativar os nós do dispositivo. Mantenha o botão para ligar/desligar premido durante alguns segundos até que as luzes azuis acendam. Todos os LEDs verdes das fontes de alimentação na parte traseira do dispositivo devem ser estar acesos. O painel de operações frontal do dispositivo também contém LEDs de falha. Quando um LED de falha está aceso, indica uma PSU ou uma ventoinha com falha ou um problema com as unidades de disco.  

    ![Painel de operações frontal do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Ligar o cabo do primeiro nó à rede

Num dos nós do dispositivo, execute os passos a seguir para ligar o cabo à rede.

1. Utilize um cabo de rede CAT 6 RJ-45 (cabo na parte superior direita na imagem, anexado para ligar o MGMT) para ligar o computador anfitrião à porta de gestão de 1 Gbps.
2. Utilize um cabo QSFP+ (fibra ou cobre) para ligar, pelo menos, uma interface de rede de 40 Gbps (em vez da de 1 Gbps) para os dados. Se estiver a utilizar um comutador de 10 Gbps, utilize um cabo SFP+ com um adaptador QSFP+ para SFP+ (o adaptador QSA) para ligar a interface de rede de 40 Gbps para os dados.

    ![Portas do Data Box Heavy com os cabos ligados](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > As portas DATA1 e DATA2 estão comutadas e não correspondem ao que é apresentado na IU da Web local.
    > O adaptador de cabo de 40 Gbps liga-se quando introduzido da forma apresentada abaixo.

    ![Adaptador de cabo de 40 Gbps do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Configurar o primeiro nó

Execute os passos seguintes para configurar o dispositivo com a configuração local e o portal do Azure.

1. Transfira as credenciais do dispositivo a partir do portal. Aceda a **Geral > Detalhes do dispositivo**. Copie a **Palavra-passe do dispositivo**. Estas palavras-passe estão associadas a uma ordem específica no portal. Os dois números de série do dispositivo correspondem aos dois nós no Data Box Heavy. A palavra-passe do administrador do dispositivo para ambos os nós é a mesma.

    ![Credenciais do dispositivo Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Ligue a estação de trabalho cliente ao dispositivo através de um cabo de rede CAT6 RJ-45.
3. Configure o adaptador Ethernet no computador que está a utilizar para se ligar ao dispositivo com um endereço IP estático de `192.168.100.5` e uma sub-rede `255.255.255.0`.

    ![O Data Box Heavy liga-se à IU da Web local](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Ligue-se à IU da Web local do dispositivo através do seguinte URL: `http://192.168.100.10`. Clique em **Avançado** e, em seguida, clique em **Prosseguir para 192.168.100.10 (não seguro)** .
5. Verá uma página para **Iniciar sessão** na IU da Web local.
    
    - Um dos números de série do nó nesta página corresponde à IU do portal e à IU da Web local. Anote o número do nó para o mapeamento do número de série. Existem dois nós e dois números de série do dispositivo no portal. Este mapeamento ajuda a entender que nó corresponde a que número de série.
    - O dispositivo está bloqueado neste momento.
    - Forneça a palavra-passe do administrador do dispositivo obtida no passo anterior para iniciar sessão no dispositivo. Clique em **Iniciar sessão**.

    ![Iniciar sessão na IU da Web local do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. No Dashboard, confirme se as interfaces de rede estão configuradas. Há quatro interfaces de rede no nó do dispositivo, duas de 1 Gbps e duas de 40 Gbps. Uma das interfaces de 1 Gbps é uma interface de gestão e, portanto, não pode ser configurada pelo utilizador. As três interfaces de rede restantes são dedicadas aos dados e podem ser configuradas pelo utilizador.

- Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente.
- Se o DHCP não estiver ativado, aceda a Definir interfaces de rede e atribua IPs estáticos, se necessário.

    ![Nó 1 do dashboard do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Configurar o segundo nó

Siga os passos detalhados na secção [Configurar o primeiro nó](#configure-first-node) para o segundo nó do dispositivo.

![Nó 2 do dashboard do Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Quando a configuração do dispositivo estiver concluída, pode ligar às partilhas do dispositivo e copiar os dados do seu computador para o dispositivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Heavy, como:

> [!div class="checklist"]
> * Instalar os cabos do Data Box Heavy
> * Ligar o Data Box Heavy

Avance para o próximo tutorial para saber como copiar dados no Data Box Heavy.

> [!div class="nextstepaction"]
> [Copiar dados para o Azure Data Box](./data-box-heavy-deploy-copy-data.md)

::: zone-end
