---
title: Integrar com o Gateway de Aplicação
description: Saiba como integrar um aplicativo em seu ILB Ambiente do Serviço de Aplicativo com um gateway de aplicativo neste passo a passo de ponta a ponta.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: dfb6d72b3f8f61e1350101173ecec6134a614edf
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687144"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrar o Ambiente do Serviço de Aplicativo ILB com o gateway de Aplicativo Azure #

A [ambiente do serviço de aplicativo](./intro.md) é uma implantação do serviço de Azure app na sub-rede da rede virtual do Azure de um cliente. Ele pode ser implantado com um ponto de extremidade público ou privado para acesso ao aplicativo. A implantação do Ambiente do Serviço de Aplicativo com um ponto de extremidade privado (ou seja, um balanceador de carga interno) é chamada de Ambiente do Serviço de Aplicativo ILB.  

Os firewalls do aplicativo Web ajudam a proteger seus aplicativos Web inspecionando o tráfego da Web de entrada para bloquear injeções de SQL, scripts entre sites, carregamentos de malware & DDoS do aplicativo e outros ataques. Ele também inspeciona as respostas dos servidores Web back-end para DLP (prevenção contra perda de dados). Você pode obter um dispositivo WAF do Azure Marketplace ou pode usar o gateway de [aplicativo Azure][appgw].

O gateway de Aplicativo Azure é uma solução de virtualização que fornece balanceamento de carga de camada 7, descarregamento de SSL e proteção WAF (firewall de aplicativo Web). Ele pode escutar em um endereço IP público e rotear o tráfego para o ponto de extremidade do aplicativo. As informações a seguir descrevem como integrar um gateway de aplicativo configurado WAF com um aplicativo em um Ambiente do Serviço de Aplicativo de ILB.  

A integração do gateway de aplicativo com o Ambiente do Serviço de Aplicativo ILB está em um nível de aplicativo. Ao configurar o gateway de aplicativo com sua Ambiente do Serviço de Aplicativo do ILB, você está fazendo isso para aplicativos específicos em seu Ambiente do Serviço de Aplicativo do ILB. Essa técnica permite hospedar aplicativos multilocatários seguros em um único Ambiente do Serviço de Aplicativo ILB.  

![Gateway de aplicativo apontando para o aplicativo em um Ambiente do Serviço de Aplicativo ILB][1]

Nestas instruções, irá:

* Crie um gateway de Aplicativo Azure.
* Configure o gateway de aplicativo para apontar para um aplicativo em seu Ambiente do Serviço de Aplicativo ILB.
* Configure seu aplicativo para honrar o nome de domínio personalizado.
* Edite o nome de host DNS público que aponta para o gateway de aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para integrar o gateway de aplicativo ao seu ILB Ambiente do Serviço de Aplicativo, você precisará de:

* Um Ambiente do Serviço de Aplicativo ILB.
* Um aplicativo em execução no Ambiente do Serviço de Aplicativo ILB.
* Um nome de domínio roteável da Internet a ser usado com seu aplicativo no Ambiente do Serviço de Aplicativo ILB.
* O endereço ILB que seu ILB Ambiente do Serviço de Aplicativo usa. Essas informações estão no portal de Ambiente do Serviço de Aplicativo em **configurações** > **endereços IP**:

    ![Exemplo de lista de endereços IP usados pelo Ambiente do Serviço de Aplicativo ILB][9]
    
* Um nome DNS público que é usado posteriormente para apontar para o gateway de aplicativo. 

Para obter detalhes sobre como criar um Ambiente do Serviço de Aplicativo ILB, consulte [criando e usando um ambiente do serviço de aplicativo de ILB][ilbase].

Este artigo pressupõe que você deseja um gateway de aplicativo na mesma rede virtual do Azure em que o Ambiente do Serviço de Aplicativo é implantado. Antes de começar a criar o gateway de aplicativo, escolha ou crie uma sub-rede que será usada para hospedar o gateway. 

Você deve usar uma sub-rede que não seja uma chamada GatewaySubnet. Se você colocar o gateway de aplicativo no GatewaySubnet, não será possível criar um gateway de rede virtual mais tarde. 

Você também não pode colocar o gateway na sub-rede que seu ILB Ambiente do Serviço de Aplicativo usa. A Ambiente do Serviço de Aplicativo é a única coisa que pode estar nessa sub-rede.

## <a name="configuration-steps"></a>Passos de configuração ##

1. Na portal do Azure, vá para **novo** > **rede** > **Gateway de aplicativo**.

2. Na área **básico** :

   a. Para **nome**, insira o nome do gateway de aplicativo.

   b. Para **camada**, selecione **WAF**.

   c. Para **assinatura**, selecione a mesma assinatura que a rede virtual ambiente do serviço de aplicativo usa.

   d. Para **grupo de recursos**, crie ou selecione o grupo de recursos.

   e. Para **local**, selecione o local da rede virtual ambiente do serviço de aplicativo.

   ![Noções básicas de criação de novo gateway de aplicativo][2]

3. Na área **configurações** :

   a. Para **rede virtual**, selecione o ambiente do serviço de aplicativo rede virtual.

   b. Para **sub-rede**, selecione a sub-rede na qual o gateway de aplicativo precisa ser implantado. Não use GatewaySubnet, pois isso impedirá a criação de gateways de VPN.

   c. Para **tipo de endereço IP**, selecione **público**.

   d. Para **endereço IP público**, selecione um endereço IP público. Se você não tiver um, crie um agora.

   e. Para **protocolo**, selecione **http** ou **https**. Se estiver configurando para HTTPS, você precisará fornecer um certificado PFX.

   f. Para o **Firewall do aplicativo Web**, você pode habilitar o firewall e também defini-lo para **detecção** ou **prevenção** , como se desejar.

   ![Novas configurações de criação do gateway de aplicativo][3]
    
4. Na seção **Resumo** , examine as configurações e selecione **OK**. O gateway de aplicativo pode levar um pouco mais de 30 minutos para concluir a instalação.  

5. Depois que o gateway de aplicativo concluir a instalação, vá para o portal do gateway de aplicativo. Selecione **pool de back-end**. Adicione o endereço ILB para seu Ambiente do Serviço de Aplicativo de ILB.

   ![Configurar pool de back-end][4]

6. Depois que o processo de configuração do pool de back-end for concluído, selecione **investigações de integridade**. Crie uma investigação de integridade para o nome de domínio que você deseja usar para seu aplicativo. 

   ![Configurar sondas do estado de funcionamento][5]
    
7. Depois que o processo de configuração de suas investigações de integridade for concluído, selecione **configurações de http**. Edite as configurações existentes, selecione **usar investigação personalizada**e escolha a investigação que você configurou.

   ![Definir configurações de HTTP][6]
    
8. Vá para a seção **visão geral** do gateway de aplicativo e copie o endereço IP público que o gateway de aplicativo usa. Defina esse endereço IP como um registro para o nome de domínio do aplicativo ou use o nome DNS para esse endereço em um registro CNAME. É mais fácil selecionar o endereço IP público e copiá-lo da interface do usuário do endereço IP público em vez de copiá-lo do link na seção **visão geral** do gateway de aplicativo. 

   ![Portal do gateway de aplicativo][7]

9. Defina o nome de domínio personalizado para seu aplicativo no Ambiente do Serviço de Aplicativo ILB. Vá para seu aplicativo no portal e, em **configurações**, selecione **domínios personalizados**.

   ![Definir nome de domínio personalizado no aplicativo][8]

Há informações sobre como configurar nomes de domínio personalizados para seus aplicativos Web no artigo [Configurando nomes de domínio personalizados para seu aplicativo Web][custom-domain]. Mas, para um aplicativo em um Ambiente do Serviço de Aplicativo ILB, não há nenhuma validação no nome de domínio. Como você possui o DNS que gerencia os pontos de extremidade do aplicativo, você pode colocar o que desejar ali. O nome de domínio personalizado que você adiciona nesse caso não precisa estar no DNS, mas ele ainda precisa ser configurado com seu aplicativo. 

Depois que a instalação for concluída e você tiver permitido um curto período de tempo para as alterações de DNS serem propagadas, você poderá acessar seu aplicativo usando o nome de domínio personalizado que você criou. 


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
