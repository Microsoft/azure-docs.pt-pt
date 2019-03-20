---
title: Configurar o Azure Data Box| Microsoft Docs
description: Saiba como instalar os cabos e ligar o Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/08/2019
ms.author: alkohli
ms.openlocfilehash: b601ad5936820e2c237b7b9d37d9af73aa468bbc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895758"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Tutorial: Instalar os cabos e ligue-se para o Azure Data Box

Este tutorial descreve como instalar os cabos, ligar e ativar o Azure Data Box.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instalar os cabos do Data Box
> * Ligar ao Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Encomendar do Azure Data Box](data-box-deploy-ordered.md).
2. Recebeu o seu Data Box e o estado da encomenda no portal é **Entregue**. 
    - Existe uma etiqueta de envio na bolsa transparente afixada ao dispositivo sob a etiqueta atual. Mantenha esta etiqueta segura, uma vez que a vai utilizar para o envio de devolução.
    - Algumas regiões na Europa, poderão receber o dispositivo empacotado numa caixa. Certifique-se de que descompactar o dispositivo e guardar a caixa para devolução.
3. Reviu as [diretrizes de segurança do Data Box](data-box-safety.md).
4. Recebeu um cabo de alimentação ligado à terra para utilizar com o seu dispositivo de armazenamento de 100 TB.
5. Tem um computador anfitrião com os dados que pretende copiar para o Data Box. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se não estiver disponível uma ligação de 10 GbE, poderá ser utilizada uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas. 
6. Tem acesso a uma superfície plana onde possa colocar o Data Box. Se pretende colocar o dispositivo numa prateleira de rack padrão, precisará de um bloco de 7U no rack do datacenter. Pode colocar o dispositivo na horizontal ou vertical no rack.
7. Obteve os cabos seguintes para ligar o Data Box ao computador anfitrião.
    - Um ou mais 10 GbE SFP + Twinax cobre ou SFP + cabos de fibra óptica (utilize com dados 1, 2 de dados de interfaces de rede). Data Box tem adaptadores Mellanox ConnectX®-3 Pro EN porta dupla 10GBASE-T c / 3.0 Express do PCI de interface de rede, para que os cabos que são compatíveis com essa interface devem funcionar. Por exemplo, um SFP do CISCO SFP-H10GB-CU3M 10GBASE CU TWINMAX + 3 M cabo foi utilizado para fins de teste inhouse. Para obter mais informações, consulte a [lista de suportadas cabos e comutadores de Mellanox](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf).
    - Um cabo de rede RJ-45 CAT 6 (utilizar com interface de rede MGMT)
    - Um cabo de rede RJ-45 CAT 6A OU RJ-45 CAT 6 (utilizar com a interface de rede DADOS 3, configurada como 10 Gbps ou 1 Gbps, respetivamente)

## <a name="cable-your-device"></a>Instalação dos cabos do dispositivo

Execute os seguintes passos para instalar os cabos do dispositivo.

1. Inspecione o dispositivo em busca de qualquer evidência de adulteração ou outros danos óbvios. Se o dispositivo estiver adulterado ou gravemente danificado, não continue. Entre em contacto com o Suporte da Microsoft de imediato para ajudá-lo a avaliar se o dispositivo está a funcionar corretamente e se é preciso enviar um de substituição.
2. Leve o dispositivo para a localização onde pretende ligá-lo à corrente. Coloque o dispositivo numa superfície plana. O dispositivo também pode ser colocado numa prateleira de rack comum.
3. Ligue os cabos de alimentação e de rede. Apresentamos abaixo o plano posterior de um dispositivo ligado para uma configuração comum. Dependendo do seu ambiente, pode escolher outros [cablagem opções](data-box-cable-options.md).
    
    ![Plano posterior do dispositivo Data Box com os cabos instalados](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Ligue o cabo de alimentação à localização de entrada de alimentação identificada. A outra extremidade do cabo de alimentação deve ser ligada a uma unidade de distribuição de alimentação.
    2. Utilize o cabo RJ-45 CAT 6 para ligar a porta MGMT numa extremidade e um portátil na outra extremidade.            
    3. Utilize o cabo RJ-45 CAT 6A para ligar à porta DATA 3 numa extremidade. A porta DATA 3 está configurada como 10 GbE se ligar através do cabo RJ-45 CAT 6A e como 1 GbE se ligar através do cabo RJ-45 CAT 6.
    4. Consoante as interfaces de rede que pretende ligar-se para a transferência de dados, utilize até duas 10 GbE SFP + Twinax cobre ou SFP + cabos de fibra óptica para ligar as portas de dados 1 e 2 de dados, respectivamente. 
    5. As outras extremidades dos cabos das portas de dados estão ligadas ao computador anfitrião através de um comutador de 10 GbE.

4. Localize o botão para ligar/desligar no painel frontal operativo do dispositivo. Ligue o dispositivo.

    ![Botão para ligar/desligar do Data Box](media/data-box-deploy-set-up/data-box-powered-door-open.png)

## <a name="connect-to-your-device"></a>Ligar ao seu dispositivo

Execute os passos seguintes para configurar o dispositivo com a IU da Web local e a IU do portal.

1. Configure o adaptador Ethernet no portátil que está a utilizar para ligar ao dispositivo com um endereço IP estático 192.168.100.5 e sub-rede 255.255.255.0. 
2. Ligue a porta MGMT do seu dispositivo e aceder aos respetiva web local da interface do Usuário em https\:/ / 192.168.100.10. Este processo pode demorar até 5 minutos depois de ligar o dispositivo.
3. Clique em **Details** (Detalhes) e, em seguida, clique em **Go on to the webpage** (Avançar para a página Web).

   ![Ligar à IU da Web local](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. Verá uma página para **Iniciar sessão** na IU da Web local. Certifique-se de que o número de série do dispositivo coincide na IU do portal e na IU da Web local. O dispositivo está bloqueado neste momento.
5. Inicie sessão no [Portal do Azure](https://portal.azure.com).
6. Transfira as credenciais do dispositivo a partir do portal. Aceda a **Geral > Detalhes do dispositivo**. Copie a **Palavra-passe do dispositivo**. A palavra-passe do dispositivo está associada a uma ordem específica no portal. 

    ![Obter as credenciais do dispositivo](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Indique a palavra-passe do dispositivo que obteve no portal do Azure no passo anterior para iniciar sessão na IU da Web local do dispositivo. Clique em **Iniciar sessão**.
8. No **Dashboard**, certifique-se de que as interfaces de rede estão configuradas. 
   - Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. 
   - Se o DHCP não estiver ativado, aceda a **Definir interfaces de rede** e atribua IPs estáticos, se for necessário.

     ![Dashboard do dispositivo](media/data-box-deploy-set-up/data-box-dashboard-1.png)

Assim que as interfaces de rede de dados estiverem configuradas, também pode utilizar o endereço IP de qualquer uma das interfaces de DADOS 1 - DADOS 3, para aceder à IU da Web local em `https://<IP address of a data network interface>`. 

Quando a configuração do dispositivo estiver concluída, pode ligar às partilhas do dispositivo e copiar os dados do seu computador para o dispositivo. 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Instalar os cabos do Data Box
> * Ligar ao Data Box

Avance para o próximo tutorial para saber como copiar dados no Data Box.

> [!div class="nextstepaction"]
> [Copiar dados para o Azure Data Box](./data-box-deploy-copy-data.md)

