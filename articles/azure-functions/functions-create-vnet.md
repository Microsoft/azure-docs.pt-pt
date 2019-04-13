---
title: Integrar as funções do Azure com uma rede Virtual do Azure
description: Um tutorial passo a passo que mostram como ligar uma função a uma rede virtual do Azure
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f8ddcbcb25dc45ee71304ffa1bc5c0c4d3751b61
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523737"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integrar uma aplicação de função com uma rede virtual do Azure

Este tutorial passo a passo mostra-lhe como utilizar as funções do Azure para se ligar aos recursos numa VNET do Azure.

Para este tutorial, irá implementar um site WordPress numa VM numa privada, não-acessível pela internet, VNET. Em seguida, vamos implementar uma função com acesso à internet e a VNET. Nós usaremos essa função para aceder a recursos no site do WordPress implementado dentro da VNET.

Para obter mais informações sobre como funciona o sistema, a solução de problemas e configuração avançada, consulte o documento [integrar a sua aplicação com uma rede Virtual do Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). As funções do Azure no plano Premium têm as mesmas funcionalidades de alojamento, como aplicações web, pelo que todas as funcionalidades e limitações desse documento se aplicam às funções também.

## <a name="topology"></a>Topologia

 ![Integração de VNet da interface do Usuário][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>Criar uma VM dentro de uma VNET

Para começar, vamos criar uma VM pré-configuradas com o Wordpress dentro de uma VNET. 

Wordpress numa VM foi escolhido por ser um dos recursos menos caros que podem ser implementados dentro de uma VNET. Tenha em atenção que este cenário também pode trabalhar com qualquer recurso numa VNET, como REST APIs, ambientes de serviço de aplicações e outros serviços do Azure.

1. Ir para o portal do Azure
2. Adicionar um novo recurso ao abrir o painel "Criar um recurso"
3. Procure por "[WordPress LEMP7 Max Performance no CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" e abra o painel de criação 
4. No painel de criação de configurar a VM com as seguintes informações:
    1. Crie um novo grupo de recursos para esta VM fazer a limpeza de recursos mais fácil no final do tutorial. Nomeei meu grupo de recursos "Função-VNET-Tutorial"
    1. Dê um nome exclusivo à máquina virtual. Nomeei meu "VNET-Wordpress"
    1. Selecione a região mais próxima de si
    1. Selecione o tamanho como B1s (1 vcpu, memória de 1 GB)
    1. Para a conta de administrador, escolha a autenticação de palavra-passe e introduza um nome de utilizador exclusivo e uma palavra-passe. Para este tutorial, não terá de iniciar sessão na VM, exceto se precisar de resolver.
    
        ![Criar o guia de conceitos básicos de VM](./media/functions-create-vnet/create-vm-1.png)

1. Vá para o separador rede e introduza as seguintes informações:
    1.  Crie uma nova rede virtual
    1.  Introduza o seu intervalo de endereços privados desejada e a sub-rede dentro desse intervalo de endereços. O tamanho da sub-rede determinará o número de VMs pode utilizar no plano do serviço de aplicações. Se o endereçamento e de sub-redes IP é novidade para, há uma [documento que aborda os fundamentos básicos](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Endereçamento IP e sub-redes são importantes neste cenário, portanto, recomendo ler alguns artigos e observar alguns vídeos online até que faz sentido. 
        1. Neste exemplo, eu Optei por usar a rede de 10.10.0.0/16 com uma sub-rede de 10.10.1.0/24. Optei por sobreaprovisionar e utilizar um /16 sub-rede porque é fácil calcular quais sub-redes estão disponíveis na rede 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Novamente no separador rede, defina o IP público para "None". Isto irá implementar a VM com o acesso apenas à vnet.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Crie a VM. Esta ação irá demorar cerca de 5 minutos.
8. Assim que a VM é criada, visite a sua rede guia e tome nota do endereço de IP privado para utilizar mais tarde. A VM não deve ter um IP público.

    ![14]

Tem agora um site wordpress implementado inteiramente na sua rede virtual. Este site não está acessível a partir da internet pública.

## <a name="create-a-premium-plan-function-app"></a>Criar um plano Premium do Function App

A próxima etapa é criar uma aplicação de funções num plano premium. O plano premium é uma nova oferta que traz escala sem servidor com todos os benefícios de um plano de serviço de aplicações dedicado. Aplicações de funções de plano de consumo não suportam a integração de VNet.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-vnet"></a>Ligar a sua aplicação Function App a VNET

Com um site do WordPress que aloja os ficheiros a partir da VNET, pode agora ligar a aplicação de funções para a VNET.

1.  No portal para a aplicação de funções do passo anterior, selecione **funcionalidades de plataforma**, em seguida, selecione **de rede**

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Selecione **clique aqui para configurar** em integração de VNet

    ![Configurar o estado do recurso de rede](./media/functions-create-vnet/Networking-1.png)

1. Na página de integração de VNET, selecione **adicionar VNet (pré-visualização)**

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Crie uma nova sub-rede para o seu plano de função e o serviço de aplicações a utilizar. Tenha em atenção que o tamanho da sub-rede vai limitar o número total de VMs, pode adicionar ao seu plano do serviço de aplicações. A VNET encaminha automaticamente o tráfego entre as sub-redes na sua VNET, portanto, não importa que sua função é numa sub-rede diferente da VM. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>Criar uma função que acede a um recurso na sua VNET

A aplicação de função agora pode aceder a VNET com o nosso site do wordpress, então, vamos usar a função acedam a esse ficheiro e servi-lo para o utilizador. Neste exemplo vamos utilizar um site wordpress como a API e um Proxy de função como as funções de chamada por estarem fácil de configurar e visualizar. Poderia facilmente usar qualquer outra API implementada numa VNET e outra função com o código de API de fazer chamadas para essa API implementada na sua VNET. Um implementado na sua VNET do SQL server é um exemplo perfeito.

1. No portal, abra a aplicação de funções do passo anterior
1. Criar um Proxy, selecionando **Proxies** > **+**

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Configure o nome de Proxy e a rota. Eu escolhi /plant como meu rota.
1. Preencha o IP do seu site wordpress anteriores e definir o URL de back-end `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Agora, se tentar visitar o URL do seu back-end diretamente pelo colá-la num novo separador do browser, a página deve ser tempo limite. Isso é de se esperar, como o site do wordpress está ligado apenas a VNET e não na internet. Se colar o URL de Proxy no navegador, deverá ver uma imagem de fábrica bonito, obtida a partir do seu site do Wordpress na sua VNET. 

A aplicação Function App está ligada à Internet e sua VNET. O proxy é receber um pedido através da internet pública e, em seguida, atuando como um proxy HTTP simple para encaminhar essa solicitação ao longo para a rede virtual. O proxy reencaminha, em seguida, a resposta de volta para na Internet pública. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Próximos Passos

As funções em execução num plano Premium partilham a mesma infra-estrutura subjacente do serviço de aplicações como aplicações Web no PV2 planos. Isso significa que toda a documentação para aplicações Web aplica-se a suas funções de plano Premium.

1. [Saiba mais sobre as opções de funcionamento em rede nas funções aqui](./functions-networking-options.md)
1. [Ler as funções de rede FAQ aqui](./functions-networking-faq.md)
1. [Saiba mais sobre VNETs no Azure](../virtual-network/virtual-networks-overview.md)
1. [Ativar mais recursos de rede e controlo com ambientes de serviço de aplicações](../app-service/environment/intro.md)
1. [Ligar a recursos locais individuais sem alterações de firewall com ligações híbridas](../app-service/app-service-hybrid-connections.md)
1. [Saiba mais sobre os Proxies de funções](./functions-proxies.md)

<!--Image references-->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
