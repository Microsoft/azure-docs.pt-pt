---
title: Integrar com o Gateway de Aplicação
description: Saiba como integrar uma aplicação no seu ILB App Service Environment com um Gateway de aplicação neste walk-through de ponta a ponta.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e4838597c50898748eb4b33e81ff22eaeea37b30
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80476903"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrar o Ambiente do Serviço de Aplicações ILB no Gateway de Aplicação do Azure #

O [App Service Environment](./intro.md) é uma implantação do Azure App Service na subnet da rede virtual Azure de um cliente. Pode ser implantado com um ponto final público ou privado para acesso à aplicação. A implementação do App Service Environment com um ponto final privado (isto é, um equilibrista interno de carga) é chamada de Ambiente de Serviço de Aplicações ILB.  

As firewalls da aplicação web ajudam a proteger as suas aplicações web inspecionando o tráfego web de entrada para bloquear injeções SQL, Scripting Cross-Site, uploads de malware & aplicação DDoS e outros ataques. Também inspeciona as respostas dos servidores web back-end para prevenção de perdas de dados (DLP). Pode obter um dispositivo WAF no mercado Azure ou pode utilizar o Portal de [Aplicações Azure][appgw].

O Portal de Aplicações Azure é um aparelho virtual que fornece um equilíbrio de carga da camada 7, descarregamento TLS/SSL e proteção de firewall de aplicação web (WAF). Pode ouvir num endereço IP público e o tráfego de rota para o seu ponto final de aplicação. As seguintes informações descrevem como integrar um gateway de aplicação configurado por WAF com uma aplicação num Ambiente de Serviço de Aplicações ILB.  

A integração da porta de entrada de aplicações com o ILB App Service Environment está a nível de aplicações. Ao configurar o portal da aplicação com o seu Ambiente de Serviço de Aplicações ILB, está a fazê-lo para aplicações específicas no seu Ambiente de Serviço de Aplicações ILB. Esta técnica permite hospedar aplicações multiarrendatárias seguras num único Ambiente de Serviço de Aplicações ILB.  

![Gateway de aplicação apontando para app em um ILB App Service Environment][1]

Nestas instruções, irá:

* Crie um Portal de Aplicação Azure.
* Configure o Gateway de Aplicação para apontar para uma aplicação no seu Ambiente de Serviço de Aplicações ILB.
* Configure a sua aplicação para honrar o nome de domínio personalizado.
* Editar o nome de anfitrião público do DNS que aponta para o seu gateway de aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para integrar o seu Gateway de Aplicação com o seu Ambiente de Serviço de Aplicações ILB, precisa de:

* Um ambiente de serviço de aplicativos ILB.
* Uma aplicação em execução no ILB App Service Environment.
* Um nome de domínio resaída à Internet para ser usado com a sua aplicação no ILB App Service Environment.
* O endereço ILB que o seu ILB App Service Environment utiliza. Esta informação encontra-se no portal ambiente do serviço de aplicações em termos de > **Endereços IP** **definições:**

    ![Lista de exemplo de endereços IP utilizados pelo ILB App Service Environment][9]
    
* Um nome público de DNS que é usado mais tarde para apontar para o seu Gateway de Aplicação. 

Para mais detalhes sobre como criar um Ambiente de Serviço de Aplicações ILB, consulte Criar e utilizar um Ambiente de Serviço de [Aplicações ILB][ilbase].

Este artigo assume que pretende um Gateway de aplicação na mesma rede virtual Azure onde o App Service Environment é implementado. Antes de começar a criar o Gateway de Aplicação, escolha ou crie uma sub-rede que utilizará para alojar o portal. 

Devias usar uma sub-rede que não seja a chamada GatewaySubnet. Se colocar o Gateway de Aplicação no GatewaySubnet, não poderá criar um portal de rede virtual mais tarde. 

Também não é possível colocar a porta de entrada na sub-rede que o seu ILB App Service Environment utiliza. O Ambiente de Serviço de Aplicações é a única coisa que pode estar nesta sub-rede.

## <a name="configuration-steps"></a>Passos de configuração ##

1. No portal Azure, vá ao **Gateway de** > **Aplicações**de**Nova Rede.** > 

2. Na área **do Básico:**

   a. Para **Nome,** insira o nome do Gateway de Aplicação.

   b. Para **Tier,** selecione **WAF**.

   c. Para **subscrição,** selecione a mesma subscrição que a rede virtual App Service Environment utiliza.

   d. Para **o grupo Recursos,** crie ou selecione o grupo de recursos.

   e. Para **localização,** selecione a localização da rede virtual App Service Environment.

   ![Novo fundamento de criação de Gateway de aplicação][2]

3. Na área **definições:**

   a. Para **a rede Virtual,** selecione a rede virtual app Service Environment.

   b. Para **sub-rede,** selecione a sub-rede onde o Gateway da Aplicação precisa de ser implantado. Não utilize gatewaySubnet, pois impedirá a criação de gateways VPN.

   c. Para o tipo de **endereço IP,** selecione **Public .**

   d. Para **obter endereços PÚBLICOS,** selecione um endereço IP público. Se não tem um, crie um agora.

   e. Para **Protocolo,** selecione **HTTP** ou **HTTPS**. Se estiver a configurar para HTTPS, precisa de fornecer um certificado PFX.

   f. Para a firewall da **aplicação Web,** pode ativar a firewall e também defini-la para **deteção** ou **prevenção** como entender.

   ![Novas definições de criação de Gateway de Aplicação][3]
    
4. Na secção **Resumo,** reveja as definições e selecione **OK**. O seu Gateway de Aplicação pode demorar pouco mais de 30 minutos a concluir a configuração.  

5. Depois de o Gateway de Aplicação concluir a configuração, vá ao portal Application Gateway. Selecione **piscina de backend**. Adicione o endereço ILB para o seu Ambiente de Serviço de Aplicações ILB.

   ![Configure piscina de backend][4]

6. Após o processo de configuração da sua piscina de back-end estar concluído, selecione **sondas de saúde**. Crie uma sonda de saúde para o nome de domínio que pretende utilizar para a sua aplicação. 

   ![Configurar sondas do estado de funcionamento][5]
    
7. Após o processo de configuração das suas sondas de saúde, selecione **as definições http**. Editar as definições existentes, selecionar **utilizar a sonda Personalizada**e escolher a sonda que configura.

   ![Configurar as definições http][6]
    
8. Vá à secção **de visão geral** do Gateway de aplicações e copie o endereço IP público que o seu Gateway de Aplicação utiliza. Detete esse endereço IP como um registo A para o nome do seu domínio da aplicação, ou use o nome DNS para esse endereço num registo CNAME. É mais fácil selecionar o endereço IP público e copiá-lo a partir do UI do endereço IP público em vez de copiá-lo a partir do link na secção **de visão geral** do Gateway de aplicação. 

   ![Portal gateway de aplicação][7]

9. Detete o nome de domínio personalizado para a sua aplicação no seu Ambiente de Serviço de Aplicações ILB. Vá à sua aplicação no portal e em **Definições,** selecione **domínios Personalizados**.

   ![Definir nome de domínio personalizado na aplicação][8]

Existem informações sobre a definição de nomes de domínio personalizados para as suas aplicações web no artigo [Definindo nomes de domínio personalizados para a sua aplicação web][custom-domain]. Mas para uma aplicação num ILB App Service Environment, não há qualquer validação no nome de domínio. Como é dono do DNS que gere os pontos finais da aplicação, pode colocar o que quiser lá dentro. O nome de domínio personalizado que adiciona neste caso não precisa de estar no seu DNS, mas ainda precisa de ser configurado com a sua aplicação. 

Depois de concluída a configuração e ter permitido que as alterações do DNS se propaguem, pode aceder à sua aplicação utilizando o nome de domínio personalizado que criou. 


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
