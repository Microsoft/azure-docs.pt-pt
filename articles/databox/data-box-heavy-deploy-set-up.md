---
title: Tutorial para configurar a pesadas de caixa de dados do Azure | Documentos da Microsoft
description: Saiba como instalar os cabos e ligue-se sua pesadas de caixa de dados do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: alkohli
ms.openlocfilehash: d4cdc9b6c31c62080fed5a8528577fef72c01c3f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271720"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy-preview"></a>Tutorial: Instalar os cabos e ligue ao seu Azure dados caixa pesadas (pré-visualização)


Este tutorial descreve como instalar os cabos, ligue-se e ativar o seu pesadas de caixa de dados do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Cabo sua pesado de caixa de dados
> * Ligar ao seu pesado de caixa de dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Ordenar pesado de caixa de dados do Azure](data-box-heavy-deploy-ordered.md).
2. Tiver recebido seu intenso de caixa de dados e é o estado da encomenda no portal **entregues**.
3. Reviu os [diretrizes de segurança de dados de caixa pesada](data-box-safety.md).
4. Tem de ter acesso a um site simples no Centro de dados com a proximidade para uma ligação de rede disponíveis que pode acomodar um dispositivo com esta presença. Este dispositivo não pode ser montado num bastidor.
5. Recebi quatro power mantermos cabos para utilizar com o seu dispositivo de armazenamento.
6. Deve ter um computador anfitrião ligado à rede do datacenter. Sua pesadas de caixa de dados irá copiar os dados deste computador. O computador anfitrião tem de executar uma [sistema de operativo suportados](data-box-heavy-system-requirements.md).
7. O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. 
8. Tem de ter um laptop com o cabo de RJ-45 para ligar à interface do usuário local e configurar o dispositivo. Utilize o laptop para configurar uma vez a cada nó do dispositivo.
9. Precisa de um cabo de 40 Gbps ou cabo de 10 Gbps por nó de dispositivo.
    - Escolha cabos que são compatíveis com o [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) interface de rede.
    - Para o cabo de 40 Gbps, a fim de dispositivo do cabo tem de ser QSFP +.
    - Para o cabo de 10 Gbps, terá de um cabo de SFP + que se conecta um comutador de 10 Gbps no extremo, com um QSFP + SFP + adaptador (ou o adaptador de qsa independente) para o end que se conecta o dispositivo.

## <a name="cable-your-device-for-power"></a>Instalar os cabos do dispositivo para poder

Siga os passos seguintes para instalar os cabos do dispositivo.

1. Inspecione o dispositivo em busca de qualquer evidência de adulteração ou outros danos óbvios. Se o dispositivo estiver adulterado ou gravemente danificado, não continue. [Contacte o Microsoft Support](data-box-disk-contact-microsoft-support.md) imediatamente para o ajudar a avaliar se o dispositivo está em boa ordem funcional e se terá de enviar a uma substituição.
2. Mova o dispositivo para o site de instalação.

    ![Site de instalação de dispositivo dados pesadas de caixa](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Bloquear os casters rear no dispositivo, conforme mostrado abaixo.

    ![Casters dados caixa pesada dispositivo bloqueados](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Localize os recursos que promovem a frente e as portas abertas do dispositivo. Desbloquear e mover desde o início até esta estar alinhada com o lado do dispositivo. Repetir isso com a porta também.
    Ambas as portas têm de permanecer abertas quando o dispositivo está operacional para permitir o fluxo de ar de frente para trás ideal através do dispositivo.

    ![Dados caixa pesada as portas abertas](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. O tabuleiro parte traseira desse dispositivo deve ter quatro cabos de energia. Remover todos os cabos do tabuleiro do e colocá-los à parte.

    ![Dados caixa pesada power cabos na Bandeja](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. A próxima etapa é identificar as várias portas parte traseira desse dispositivo. Existem dois nós de dispositivo, **NODE1** e **NODE2**. Cada nó tem quatro interfaces de rede **MGMT**, **dados1**, **DATA2**, **DATA3**. **MGMT** é utilizado para configurar a gestão durante a configuração inicial do dispositivo. **Dados1**-**DATA3** são portas de dados. **MGMT** e **DATA3** portas são 1 Gbps, enquanto **dados1**, **DATA2** pode funcionar como portas de 40 Gbps ou portas de 10 Gbps. Na parte inferior de nós dois dispositivos, são quatro unidades de fonte de alimentação de energia (PSUs) que são partilhadas entre os nós de dois dispositivos. À medida que se depara neste dispositivo, o **PSUs** são **PSU1**, **PSU2**, **PSU3**, e **PSU4** da esquerda para a direita.

    ![Portas de caixa intensivo de dados](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Ligar os cabos de quatro power a fontes de alimentação dos dispositivo. O verde LEDs ativassem e blink.
8. Use os botões de energia no painel de front-para ativar os nós do dispositivo. Mantenha o botão de energia deprimido por alguns segundos até que as luzes azuis vir na. Todos os LEDs de verde para as fontes de alimentação na minha o dispositivo devem agora ter uma base sólidas. O painel frontal de funcionamento do dispositivo também contém LED de falha. Quando uma falha que LED está ativada, ele indica um PSU com falhas ou um fã ou um problema com as unidades de disco.  

    ![Painel de ops front-caixa intensivo de dados](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Primeiro nó de cabo de rede

Num de nós do dispositivo, siga os passos seguintes para o cabo de rede.

1. Utilize um cabo de rede CAT 6 RJ-45 (cabo azul na imagem) para ligar o computador anfitrião à porta de gestão de 1 Gbps.
2. Utilize um QSFP + cabo (fiber ou cobre) para ligar, pelo menos, uma interface de rede de 40 Gbps (preferidas mais de 1 Gbps) para dados. Se utilizar um comutador de 10 Gbps, utilize um cabo de SFP + com um QSFP + SFP + adaptador (adaptador qsa independente) para ligar a interface de rede Gbps 40 para dados.

    ![Portas de caixa pesada dados instalou os cabos](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DADOS de 1 e DATA2 são mudados e não forem iguais, o que é apresentado na IU da web local.
    > O adaptador de cabo 40 Gbps liga-se quando inserido a forma como mostrado abaixo.

    ![Adaptador de cabo de 40 Gbps dados pesadas de caixa](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Configurar o primeiro nó

Siga os passos seguintes para configurar o seu dispositivo com a configuração local e o portal do Azure.

1. Transfira as credenciais do dispositivo a partir do portal. Aceda a **Geral > Detalhes do dispositivo**. Copie a **Palavra-passe do dispositivo**. Estas palavras-passe está associadas a uma ordem específica no portal. Correspondente a dois nós em dados de caixa pesada, verá os números de série do dispositivo de duas. A palavra-passe de administrador do dispositivo para ambos os nós é o mesmo.

    ![Credenciais de dispositivo caixa pesadas de dados](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Ligue-se a sua estação de trabalho do cliente para o dispositivo através de um cabo de rede CAT6 RJ-45.
3. Configure o adaptador Ethernet no computador que está a utilizar para ligar ao dispositivo com um endereço IP estático de `192.168.100.5` e a sub-rede `255.255.255.0`.

    ![Liga-se dados caixa pesada a IU web local](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Ligar à IU do dispositivo no seguinte URL da web local: `http://192.168.100.10`. Clique em **avançadas** e, em seguida, clique em **avançar para 192.168.100.10 (não seguro)** .
5. Verá uma página para **Iniciar sessão** na IU da Web local.
    
    - Um dos números de série de nó nesta página corresponde entre a IU do portal e IU da web local. Anote o número de nós para o mapeamento do número de série. Existem dois nós e dois números de série do dispositivo no portal. Este mapeamento ajuda-o a compreender o nó que corresponde ao que número de série.
    - O dispositivo está bloqueado neste momento.
    - Forneça a senha de administrador do dispositivo que obteve no passo anterior para iniciar sessão no dispositivo. Clique em **Iniciar sessão**.

    ![Inicie sessão na IU web local pesadas de caixa de dados](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. No Dashboard, certifique-se de que as interfaces de rede estão configuradas. Existem quatro interfaces de rede no seu nó de dispositivo, dois 1 Gbps e dois 40 Gbps. Uma da interface de 1 Gbps é uma interface de gestão e, por conseguinte, não configurável de utilizador. As interfaces de rede de três restantes são dedicadas a dados e podem ser configuradas pelo utilizador.

- Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente.
- Se DHCP não estiver ativado, vá para definir interfaces de rede e atribuir IPs estáticos, se necessário.

    ![Nó caixa pesada do dashboard de dados 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Configurar o segundo nó

Efetue os passos detalhados a [configurar o primeiro nó](#configure-first-node) para o segundo nó do dispositivo.

![Nó caixa pesadas de dashboard 2 de dados](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Quando a configuração do dispositivo estiver concluída, pode ligar às partilhas do dispositivo e copiar os dados do seu computador para o dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu sobre tópicos pesadas de caixa de dados do Azure, tais como:

> [!div class="checklist"]
> * Cabo sua pesado de caixa de dados
> * Ligar ao seu pesado de caixa de dados

Avance para o próximo tutorial para saber como copiar dados no seu intenso de caixa de dados.

> [!div class="nextstepaction"]
> [Copiar dados para o Azure Data Box](./data-box-heavy-deploy-copy-data.md)
