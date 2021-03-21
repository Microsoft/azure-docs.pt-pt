---
title: Integrar com o Gateway de Aplicação
description: Saiba como integrar uma aplicação no seu ILB App Service Environment com um Gateway de aplicações neste walk-through de ponta a ponta.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3b73d528802a8aa33c6122eaf5edfa9d046b6753
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962082"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrar o Ambiente do Serviço de Aplicações ILB no Gateway de Aplicação do Azure #

O [App Service Environment](./intro.md) é uma implementação do Azure App Service na sub-rede da rede virtual Azure de um cliente. Pode ser implantado com um ponto final público ou privado para acesso a aplicações. A implantação do Ambiente de Serviço de Aplicações com um ponto final privado (isto é, um equilibrador de carga interno) é chamada de Ambiente de Serviço de Aplicações ILB.  

As firewalls de aplicações web ajudam a proteger as suas aplicações web inspecionando o tráfego web de entrada para bloquear injeções DE SQL, Scripting cross-Site, uploads de malware & aplicação DDoS e outros ataques. Também inspeciona as respostas dos servidores web de back-end para a Prevenção de Perda de Dados (DLP). Pode obter um dispositivo WAF no mercado Azure ou pode utilizar o [Gateway de Aplicações Azure.][appgw]

O Azure Application Gateway é um aparelho virtual que fornece a camada 7 de equilíbrio de carga, descarregamento de TLS/SSL e proteção de firewall de aplicações web (WAF). Pode ouvir num endereço IP público e encaminhar o tráfego para o seu ponto final de aplicação. As seguintes informações descrevem como integrar um gateway de aplicações configurado pela WAF com uma aplicação num Ambiente de Serviço de Aplicações ILB.  

A integração do gateway de aplicações com o ILB App Service Environment encontra-se ao nível das aplicações. Ao configurar o gateway de aplicações com o seu ILB App Service Environment, está a fazê-lo para aplicações específicas no seu Ambiente de Serviço de Aplicações ILB. Esta técnica permite hospedar aplicações multitenantes seguras num único Ambiente de Serviço de Aplicações ILB.  

![Gateway de aplicações apontando para app em um ILB App Service Environment][1]

Nestas instruções, irá:

* Crie um Gateway de Aplicação Azure.
* Configure o Gateway de aplicações para apontar para uma aplicação no seu Ambiente de Serviço de Aplicações ILB.
* Configure a sua aplicação para honrar o nome de domínio personalizado.
* Edite o nome de anfitrião PÚBLICO DNS que aponta para o seu gateway de aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para integrar o seu Gateway de Aplicações com o seu Ambiente de Serviço de Aplicações ILB, necessita de:

* Um Ambiente de Serviço de Aplicações ILB.
* Uma aplicação em execução no ILB App Service Environment.
* Um nome de domínio de internet para ser usado com a sua aplicação no ILB App Service Environment.
* O endereço ILB que o seu ILB App Service Environment utiliza. Esta informação está no portal Do Ambiente do Serviço de Aplicações em  >  **Definições endereços IP**:

    ![Exemplo lista de endereços IP utilizados pelo ILB App Service Environment][9]
    
* Um nome PÚBLICO DNS que é usado mais tarde para apontar para o seu Gateway de aplicação. 

Para obter detalhes sobre como criar um Ambiente de Serviço de Aplicações ILB, consulte [criar e utilizar um Ambiente de Serviço de Aplicações ILB.][ilbase]

Este artigo pressupõe que pretende um Gateway de aplicações na mesma rede virtual Azure onde o Ambiente de Serviço de Aplicações está implantado. Antes de começar a criar o Gateway de Aplicações, escolha ou crie uma sub-rede que utilizará para hospedar o gateway. 

Devias usar uma sub-rede que não é a chamada GatewaySubnet. Se colocar o Gateway de Aplicações no GatewaySubnet, não poderá criar uma porta de entrada de rede virtual mais tarde. 

Também não é possível colocar o portal na sub-rede que o seu ILB App Service Environment utiliza. O Ambiente de Serviço de Aplicações é a única coisa que pode estar nesta sub-rede.

## <a name="configuration-steps"></a>Passos de configuração ##

1. No portal Azure, aceda ao **Gateway de**  >    >  **Aplicação de Nova** Rede.

2. Na área **de Básicos:**

   a. Para **nome,** insira o nome do Gateway de Aplicação.

   b. Para **Tier**, selecione **WAF**.

   c. Para **Subscrição**, selecione a mesma subscrição que a rede virtual App Service Environment utiliza.

   d. Para **o grupo de recursos,** crie ou selecione o grupo de recursos.

   e. Para **localização**, selecione a localização da rede virtual Do Ambiente do Serviço de Aplicações.

   ![Novos fundamentos de criação de Gateway de Aplicação][2]

3. Na área **definições:**

   a. Para **a rede Virtual**, selecione a rede virtual App Service Environment.

   b. Para **a sub-rede,** selecione a sub-rede onde o Gateway de aplicação precisa de ser implantado. Não utilize gatewaySubnet, pois impedirá a criação de gateways VPN.

   c. Para **o tipo de endereço IP**, selecione **Public**.

   d. Para **o endereço IP público,** selecione um endereço IP público. Se não tem um, crie um agora.

   e. Para **protocolo**, selecione **HTTP** ou **HTTPS**. Se estiver a configurar para HTTPS, tem de fornecer um certificado PFX.

   f. Para **a firewall da aplicação Web,** pode ativar a firewall e também defini-la para **deteção** ou **prevenção** como entender.

   ![Definições de criação de Novo Gateway de Aplicações][3]
    
4. Na secção **Resumo,** reveja as definições e selecione **OK**. O seu Gateway de Aplicação pode demorar um pouco mais de 30 minutos a completar a configuração.  

5. Depois de o seu Gateway de Aplicação concluir a configuração, vá ao portal 'Gateway de aplicações'. Selecione **backend pool**. Adicione o endereço ILB para o seu Ambiente de Serviço de Aplicações ILB.

   ![Piscina de backend configurada][4]

6. Depois de concluído o processo de configuração da sua piscina traseira, selecione **sondas de saúde**. Crie uma sonda de saúde para o nome de domínio que pretende utilizar para a sua aplicação. 

   ![Configurar sondas do estado de funcionamento][5]
    
7. Depois de concluído o processo de configuração das suas sondas de saúde, selecione **as definições HTTP**. Editar as definições existentes, selecione **Use Custom sonda**, e escolha a sonda que configura.

   ![Configurar configurações HTTP][6]
    
8. Vá à secção **de Visão Geral** do Gateway de Aplicação e copie o endereço IP público que o seu Gateway de Aplicação utiliza. Desaprote esse endereço IP como um registo A para o nome de domínio da sua aplicação, ou use o nome DNS para esse endereço num registo CNAME. É mais fácil selecionar o endereço IP público e copiá-lo a partir da UI do endereço IP público em vez de copiá-lo a partir do link na secção **Desvisão** geral do Gateway de Aplicação. 

   ![Portal do Gateway de Aplicação][7]

9. Desaprote o nome de domínio personalizado para a sua aplicação no seu Ambiente de Serviço de Aplicações ILB. Aceda à sua aplicação no portal e em **Definições,** selecione **domínios personalizados**.

   ![Definir nome de domínio personalizado na aplicação][8]

Existem informações sobre a definição de nomes de domínio personalizados para as suas aplicações web no artigo [Definindo nomes de domínio personalizados para a sua aplicação web.][custom-domain] Mas para uma aplicação num ILB App Service Environment, não há qualquer validação no nome de domínio. Como é dono do DNS que gere os pontos finais da aplicação, pode colocar lá o que quiser. O nome de domínio personalizado que adiciona neste caso não precisa de estar no seu DNS, mas ainda precisa de ser configurado com a sua app. 

Após a configuração estar concluída e tiver permitido um curto período de tempo para que as alterações ao DNS se propagassem, pode aceder à sua aplicação utilizando o nome de domínio personalizado que criou. 


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
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md