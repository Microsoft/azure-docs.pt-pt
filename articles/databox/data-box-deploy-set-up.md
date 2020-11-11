---
title: Tutorial para configurar o Azure Data Box| Microsoft Docs
description: Neste tutorial, saiba como instalar os cabos, ligar e ativar o Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: ac87d5040cd572635d81be51308f48a57ddd38e3
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335473"
---
::: zone target="docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>Tutorial: Instalar os cabos e ligar ao Azure Data Box

::: zone-end

::: zone target="chromeless"

## <a name="cable-and-connect-to-your-device"></a>Instalar os cabos e ligar ao dispositivo

::: zone-end

::: zone target="docs"

Este tutorial descreve como instalar os cabos, ligar e ativar o Azure Data Box.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instalar os cabos do Data Box
> * Ligar ao Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Fez o pedido para o Azure Data Box.
    - Para um pedido de importação, veja o [Tutorial: Encomendar o Azure Data Box](data-box-deploy-ordered.md).
    - Para um pedido de exportação, veja o [Tutorial: Encomendar o Azure Data Box](data-box-deploy-export-ordered.md)
1. Recebeu o seu Data Box e o estado da encomenda no portal é **Entregue**. 
    - Existe uma etiqueta de envio na bolsa transparente afixada ao dispositivo sob a etiqueta atual. Mantenha esta etiqueta segura, uma vez que a vai utilizar para o envio de devolução.
    - Algumas regiões da Europa podem receber o dispositivo embalado numa caixa. Retire o dispositivo da embalagem e guarde a caixa de envio para devolução.
1. Reviu as [diretrizes de segurança do Data Box](data-box-safety.md).
1. Recebeu um cabo de alimentação ligado à terra para utilizar com o seu dispositivo de armazenamento de 100 TB.
1. Tem um computador anfitrião que é utilizado para copiar dados para (pedido de importação) ou copiar dados de (pedido de exportação) do Data Box. O computador anfitrião tem de
    - Executar um [sistema operativo suportado](data-box-system-requirements.md).
    - Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se não estiver disponível uma ligação de 10 GbE, poderá ser utilizada uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas. 
1. Tem acesso a uma superfície plana onde possa colocar o Data Box. Se pretende colocar o dispositivo numa prateleira de rack padrão, precisará de um bloco de 7U no rack do datacenter. Pode colocar o dispositivo na horizontal ou vertical no rack.
1. Obteve os cabos seguintes para ligar o Data Box ao computador anfitrião.
    - Um ou mais cabos de fibra de ótica SFP+ ou de cobre Twinax SFP+ de 10 GbE (utilize com as interfaces de rede DATA1, DATA2). O Data Box possui Adaptadores 10GBASE-T com Duas Portas Mellanox ConnectX®-3 Pro EN com interface de rede PCI Express 3.0, para que os cabos compatíveis com esta interface funcionem. Por exemplo, foi utilizado um cabo CISCO SFP-H10GB-CU3M 10GBASE-CU TWINAX SFP + 3M para os testes internos. Para obter mais informações, veja a [lista de cabos e comutadores suportados da Mellanox](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf).
    - Um cabo de rede RJ-45 CAT 6 (utilizar com interface de rede MGMT)
    - Um cabo de rede RJ-45 CAT 6A OU RJ-45 CAT 6 (utilizar com a interface de rede DADOS 3, configurada como 10 Gbps ou 1 Gbps, respetivamente)

## <a name="cable-your-device"></a>Instalação dos cabos do dispositivo

Execute os seguintes passos para instalar os cabos do dispositivo.

1. Inspecione o dispositivo em busca de qualquer evidência de adulteração ou outros danos óbvios. Se o dispositivo estiver adulterado ou gravemente danificado, não continue. Entre em contacto com o Suporte da Microsoft de imediato para ajudá-lo a avaliar se o dispositivo está a funcionar corretamente e se é preciso enviar um de substituição.
2. Leve o dispositivo para a localização onde pretende ligá-lo à corrente. Coloque o dispositivo numa superfície plana. O dispositivo também pode ser colocado numa prateleira de rack comum.
3. Ligue os cabos de alimentação e de rede. Apresentamos abaixo o plano posterior de um dispositivo ligado para uma configuração comum. Dependendo do seu ambiente, pode escolher entre outras [opções de cablagem](data-box-cable-options.md).
    
    ![Plano posterior do dispositivo Data Box com os cabos instalados](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. Ligue o cabo de alimentação à localização de entrada de alimentação identificada. A outra extremidade do cabo de alimentação deve ser ligada a uma unidade de distribuição de alimentação.
    2. Utilize o cabo RJ-45 CAT 6 para ligar a porta MGMT numa extremidade e um portátil na outra extremidade.            
    3. Utilize o cabo RJ-45 CAT 6A para ligar à porta DATA 3 numa extremidade. A porta DATA 3 está configurada como 10 GbE se ligar através do cabo RJ-45 CAT 6A e como 1 GbE se ligar através do cabo RJ-45 CAT 6.
    4. Dependendo das interfaces de rede que quer ligar para a transferência de dados, utilize até dois cabos de fibra ótica SFP+ ou de cobre Twinax SFP+ de 10 GbE para ligar as portas DATA1 e DATA2, respetivamente. 
    5. As outras extremidades dos cabos das portas de dados estão ligadas ao computador anfitrião através de um comutador de 10 GbE.

4. Localize o botão para ligar/desligar no painel frontal operativo do dispositivo. Ligue o dispositivo.

    ![Botão para ligar/desligar do Data Box](media/data-box-deploy-set-up/data-box-powered-door-open.png)

::: zone-end

::: zone target="chromeless"

Depois de receber o dispositivo, tem de instalar os cabos e ligar-se ao dispositivo. 

## <a name="cable-your-device"></a>Instalação dos cabos do dispositivo

1. Se existirem quaisquer evidências de que o dispositivo foi adulterado ou está danificado, não prossiga. Contacte o Suporte da Microsoft para enviar-lhe um dispositivo de substituição.
2. Antes de instalar os cabos do dispositivo, certifique-se de que tem os cabos seguintes:
    
    - Cabo de alimentação com terra (incluído), com a classificação 10 A ou superior, com um conector IEC60320 C-13 numa extremidade para ligar ao dispositivo.
    - Um cabo de rede RJ-45 CAT 6 (utilizar com interface de rede MGMT)
    - Dois cabos de cobre SFP + Twinax de 10 GbE (utilizar com interfaces de rede DADOS 1, DADOS 2 de 10 Gbps)
    - Um cabo de rede RJ-45 CAT 6A OU RJ-45 CAT 6 (utilizar com a interface de rede DADOS 3, configurada como 10 Gbps ou 1 Gbps, respetivamente)

3. Remova e coloque o dispositivo numa superfície plana. 
    
4. Instale os cabos do dispositivo conforme mostrado abaixo.  

    ![Plano posterior do dispositivo Data Box com os cabos instalados](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Ligue o cabo de alimentação ao dispositivo.
    2. Utilize o cabo de rede RJ-45 CAT 6 para ligar o seu computador anfitrião à porta de gestão (MGMT) no dispositivo. 
    3. Utilize o cabo de cobre SFP+ Twinax para ligar, uma interface de rede de, pelo menos, 10 Gbps (preferível face a 1 Gbps), DADOS 1 ou DADOS 2 para dados. 
    4. Ligue o dispositivo. O botão de energia está no painel frontal do dispositivo.

::: zone-end

::: zone target="docs"


## <a name="connect-to-your-device"></a>Ligar ao seu dispositivo

Execute os passos seguintes para configurar o dispositivo com a IU da Web local e a IU do portal.

1. Configure o adaptador Ethernet no portátil que está a utilizar para ligar ao dispositivo com um endereço IP estático 192.168.100.5 e sub-rede 255.255.255.0. 
2. Ligue-se à porta MGMT do dispositivo e aceda à IU da Web local em https\://192.168.100.10. Este processo pode demorar até 5 minutos depois de ligar o dispositivo.
3. Clique em **Details** (Detalhes) e, em seguida, clique em **Go on to the webpage** (Avançar para a página Web).

   ![Ligar à IU da Web local](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png)

4. Verá uma página para **Iniciar sessão** na IU da Web local. Certifique-se de que o número de série do dispositivo coincide na IU do portal e na IU da Web local. O dispositivo está bloqueado neste momento.
5. Inicie sessão no [Portal do Azure](https://portal.azure.com).
6. Transfira as credenciais do dispositivo a partir do portal. Aceda a **Geral > Detalhes do dispositivo**. Copie a **Palavra-passe do dispositivo**. A palavra-passe do dispositivo está associada a uma ordem específica no portal. 

    ![Obter as credenciais do dispositivo](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. Indique a palavra-passe do dispositivo que obteve no portal do Azure no passo anterior para iniciar sessão na IU da Web local do dispositivo. Clique em **Iniciar sessão**.
8. No **Dashboard** , certifique-se de que as interfaces de rede estão configuradas. 
   - Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. 
   - Se o DHCP não estiver ativado, aceda a **Definir interfaces de rede** e atribua IPs estáticos, se for necessário.

     ![Dashboard do dispositivo](media/data-box-deploy-set-up/data-box-dashboard-1.png)

Assim que as interfaces de rede de dados estiverem configuradas, também pode utilizar o endereço IP de qualquer uma das interfaces de DADOS 1 - DADOS 3, para aceder à IU da Web local em `https://<IP address of a data network interface>`. 

Quando a configuração do dispositivo estiver concluída, pode ligar às partilhas do dispositivo e copiar os dados. 

::: zone-end

::: zone target="chromeless"

## <a name="connect-your-device"></a>Ligar o dispositivo

1. Para obter a palavra-passe do dispositivo, aceda a **Geral > Detalhes do dispositivo** no [portal do Azure](https://portal.azure.com).
2. Atribua um endereço IP estático 192.168.100.5 e a sub-rede 255.255.255.0 ao adaptador Ethernet no computador que está a utilizar para ligar ao Data Box. Aceda à IU da Web local do dispositivo em `https://192.168.100.10`. A ligação pode demorar até 5 minutos depois de ligar o dispositivo. 
3. Inicie sessão com a palavra-passe do portal do Azure. Verá um erro que indica um problema com o certificado de segurança do site. Siga as instruções específicas do browser para avançar para a página Web.
4. Por predefinição, as definições de rede da interface de dados de 10 Gbps (ou de 1 Gbps) estão configuradas como DHCP. Se for necessário, pode configurar esta interface como estática e fornecer um endereço IP. 

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Instalar os cabos do Data Box
> * Ligar ao Data Box

Avance para o próximo tutorial para saber como copiar dados.

> [!div class="nextstepaction"]
> [Copiar dados para o Azure Data Box para um pedido de importação](./data-box-deploy-copy-data.md)

Ou

> [!div class="nextstepaction"]
> [Copiar dados do Azure Data Box para um pedido de exportação](./data-box-deploy-export-copy-data.md)

::: zone-end

