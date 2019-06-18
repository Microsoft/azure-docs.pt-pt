---
title: Integrar o ambiente de serviço de aplicações ILB com o Gateway de aplicação - Azure
description: Passo a passo sobre como integrar uma aplicação no seu ambiente de serviço de aplicações ILB com um Gateway de aplicação
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ea46b5e57e4e508a3311de8633ae61d346b574eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764927"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrar o seu ambiente de serviço de aplicações ILB com o Gateway de aplicação do Azure #

O [ambiente do serviço de aplicações](./intro.md) é uma implementação do serviço de aplicações do Azure na sub-rede da rede virtual do Azure de um cliente. Pode ser implementado com um ponto de extremidade público ou privado para aceder à aplicação. A implementação do ambiente de serviço de aplicações com um ponto final privado (ou seja, um balanceador de carga interno) é chamada de um ambiente de serviço de aplicações ILB.  

A firewalls de aplicações Web ajudam a proteger seus aplicativos web ao inspecionar o tráfego da web de entrada para bloquear os carregamentos de malware de injeções, scripts entre sites, SQL e DDoS de aplicações e outros ataques. Ele também inspeciona as respostas dos servidores web de back-end para prevenção de perda de dados (DLP). Pode obter um dispositivo WAF no Azure marketplace ou pode utilizar o [Gateway de aplicação do Azure][appgw].

O Gateway de aplicação do Azure é uma aplicação virtual que fornece balanceamento de carga de camada 7, a descarga de SSL e a proteção do web application firewall (WAF). Ele pode escutar-se um público IP endereço e encaminhar o tráfego para o ponto de final do aplicativo. As informações seguintes descrevem como integrar um gateway de aplicação WAF-configurado com uma aplicação num ambiente de serviço de aplicações ILB.  

A integração entre o gateway de aplicação com o ambiente de serviço de aplicações ILB é ao nível da aplicação. Quando configurar o gateway de aplicação com o ambiente de serviço de aplicações do ILB, está fazendo isso para aplicações específicas no seu ambiente de serviço de aplicações ILB. Essa técnica permite alojar aplicações multi-inquilino seguras num único ambiente de serviço de aplicações ILB.  

![Gateway de aplicação que aponte para a aplicação num ambiente de serviço de aplicações do ILB][1]

Nestas instruções, irá:

* Crie um Gateway de aplicação do Azure.
* Configure o Gateway de aplicação para apontar para uma aplicação no seu ambiente de serviço de aplicações ILB.
* Configure a sua aplicação que respeite o nome de domínio personalizado.
* Edite o nome de anfitrião DNS público que aponta para o gateway de aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para integrar o Gateway de aplicação com o ambiente de serviço de aplicações do ILB, terá de:

* Um ambiente de serviço de aplicações ILB.
* Uma aplicação em execução no ambiente de serviço de aplicações ILB.
* Um nome de domínio encaminhável da internet a ser utilizado com a sua aplicação no ambiente de serviço de aplicações ILB.
* O endereço ILB que utiliza o ambiente de serviço de aplicações ILB. Estas informações estão no portal do ambiente de serviço de aplicações em **configurações** > **endereços IP**:

    ![Lista de exemplo de endereços IP utilizados pelo ambiente de serviço de aplicações ILB][9]
    
* Um nome DNS público que é utilizado mais tarde para apontar para o Gateway de aplicação. 

Para obter detalhes sobre como criar um ambiente de serviço de aplicações do ILB, veja [criando e usando um ambiente de serviço de aplicações ILB][ilbase].

Este artigo pressupõe que deseja um Gateway de aplicação na mesma rede virtual do Azure em que o ambiente de serviço de aplicações é implementado. Antes de começar a criar o Gateway de aplicação, escolha ou crie uma sub-rede que irá utilizar para alojar o gateway. 

Deve usar uma sub-rede que é não um com o nome GatewaySubnet. Se colocar o Gateway de aplicação no GatewaySubnet, ficará não é possível criar um gateway de rede virtual mais tarde. 

Não é possível colocar o gateway na sub-rede que utiliza o ambiente de serviço de aplicações ILB. O ambiente de serviço de aplicações é a única coisa que pode ser nesta sub-rede.

## <a name="configuration-steps"></a>Passos de configuração ##

1. No portal do Azure, aceda a **New** > **rede** > **Gateway de aplicação**.

2. Na **Noções básicas** área:

   a. Para **nome**, introduza o nome do Gateway de aplicação.

   b. Para **escalão**, selecione **WAF**.

   c. Para **subscrição**, selecione a mesma subscrição que utiliza a rede virtual do ambiente de serviço de aplicações.

   d. Para **grupo de recursos**, crie ou selecione o grupo de recursos.

   e. Para **localização**, selecione a localização da rede virtual do ambiente de serviço de aplicações.

   ![Noções básicas de criação do novo Gateway de aplicação][2]

3. Na **definições** área:

   a. Para **rede Virtual**, selecione a rede virtual do ambiente de serviço de aplicações.

   b. Para **sub-rede**, selecione a sub-rede onde o Gateway de aplicação tem de ser implementado. Não utilize GatewaySubnet, uma vez que ele irá impedir que a criação de gateways de VPN.

   c. Para **tipo de endereço IP**, selecione **público**.

   d. Para **endereço IP público**, selecione um endereço IP público. Se não tiver uma, crie uma agora.

   e. Para **protocolo**, selecione **HTTP** ou **HTTPS**. Se estiver a configurar para HTTPS, terá de fornecer um certificado PFX.

   f. Para **firewall de aplicações Web**, pode ativar a firewall e também defini-lo por qualquer **deteção** ou **prevenção** como quiser.

   ![Novas definições de criação do Gateway de aplicação][3]
    
4. Na **resumo** secção, reveja as definições e selecione **OK**. O Gateway de aplicação pode demorar um pouco mais de 30 minutos para concluir a configuração.  

5. Após a conclusão da configuração do seu Gateway de aplicação, aceda ao seu portal de Gateway de aplicação. Selecione **conjunto back-end**. Adicione o endereço do ILB para o ambiente de serviço de aplicações ILB.

   ![Configurar o conjunto de back-end][4]

6. Depois de concluído o processo de configuração de seu conjunto de back-end, selecione **sondas de estado de funcionamento**. Crie uma sonda de estado de funcionamento para o nome de domínio que pretende utilizar para a sua aplicação. 

   ![Configurar sondas do estado de funcionamento][5]
    
7. Depois de concluído o processo de configuração de seu sondas de estado de funcionamento, selecione **definições de HTTP**. Editar as definições existentes, selecione **sonda de utilização personalizada**e escolha a sonda que configurou.

   ![Configurar as definições de HTTP][6]
    
8. Vá para o Gateway de aplicação **descrição geral** secção e copie o endereço IP público que utiliza o Gateway de aplicação. Definir esse endereço IP como um registo para o seu nome de domínio de aplicação ou utilize o nome DNS para esse endereço num registo CNAME. É mais fácil selecionar o endereço IP público e copiá-lo a partir da IU do endereço IP público em vez de copiá-lo a partir da ligação no Gateway de aplicação **descrição geral** secção. 

   ![Portal de Gateway de aplicação][7]

9. Defina o nome de domínio personalizado para a sua aplicação no seu ambiente de serviço de aplicações ILB. Aceda à sua aplicação no portal e, em **configurações**, selecione **domínios personalizados**.

   ![Defina o nome de domínio personalizado da aplicação][8]

Há informações sobre a definição de nomes de domínio personalizados para as suas aplicações web no artigo [definir nomes de domínio personalizado para a sua aplicação web][custom-domain]. Mas para uma aplicação num ambiente de serviço de aplicações do ILB, não há nenhuma validação no nome de domínio. Uma vez que for o proprietário o DNS que gere os pontos de extremidade do aplicativo, pode colocar que quiser lá. O nome de domínio personalizado que neste caso adicionar não tem de estar no seu DNS, mas ainda precisa ser configurado com a sua aplicação. 

Após a conclusão da configuração e que tenham permitido um curto período de tempo para as alterações DNS se propague, pode aceder à sua aplicação ao utilizar o nome de domínio personalizado que criou. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
