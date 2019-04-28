---
title: Integrar as funções do Azure com uma rede virtual do Azure
description: Um tutorial passo a passo que lhe mostra como ligar uma função a uma rede virtual do Azure
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125678"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integrar uma aplicação de função com uma rede virtual do Azure

Este tutorial mostra-lhe como utilizar as funções do Azure para se ligar aos recursos numa rede virtual do Azure.

Para este tutorial, iremos implementar um site WordPress numa VM numa rede virtual que não está acessível a partir da internet. Em seguida, vamos implementar uma função com acesso à internet e a rede virtual. Usaremos essa função para acessar os recursos do site do WordPress implementado dentro da rede virtual.

Para obter mais informações sobre como o sistema funciona, solução de problemas e configuração avançada, consulte [integrar a sua aplicação com uma Azure virtual network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). As funções do Azure no plano Premium têm as mesmas funcionalidades de alojamento, como aplicações web, pelo que todas as funcionalidades e limitações nesse artigo, que se aplicam às funções.

## <a name="topology"></a>Topologia

 ![Interface do Usuário para a integração da rede virtual][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>Criar uma VM numa rede virtual

Para começar, vamos criar uma VM pré-configurada que é executado WordPress numa rede virtual. 

Escolhemos WordPress numa VM porque é um dos recursos menos caros que podem ser implementados numa rede virtual. Tenha em atenção que este cenário também pode trabalhar com todos os recursos numa rede virtual, como REST APIs, ambientes de serviço de aplicações e outros serviços do Azure.

1. Aceda ao portal do Azure.
2. Adicionar um novo recurso, abrindo o **criar um recurso** painel.
3. Procure por "[WordPress LEMP7 Max Performance no CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" e abra o painel de criação. 
4. Sobre o **Noções básicas** separador, configurar a VM com as seguintes informações:
    1. Crie um novo grupo de recursos para esta VM fazer a limpeza de recursos mais fácil no final do tutorial. Aqui, estamos a utilizar "Função-VNET-Tutorial" como exemplo.
    1. Dê um nome exclusivo à máquina virtual. Estamos a utilizar "VNET-Wordpress" como exemplo.
    1. Selecione a região mais próxima de si.
    1. Selecione o tamanho como B1s (1 vCPU, 1 GB de memória).
    1. Para a conta de administrador, escolha a autenticação de palavra-passe e introduza um nome de utilizador exclusivo e uma palavra-passe. Para este tutorial, não terá de iniciar sessão na VM, a menos que precisa resolver problemas.
    
        ![Guia de conceitos básicos para criar uma VM](./media/functions-create-vnet/create-vm-1.png)

1. Mover para o **redes** separador e introduza as seguintes informações:
    1.  Crie uma nova rede virtual.
    1.  Introduza um intervalo de endereços privados e uma sub-rede dentro desse intervalo de endereços. O tamanho da sub-rede determinará o número de VMs pode utilizar no plano do serviço de aplicações. Se o endereçamento e de sub-redes IP é novidade para si, existe uma [documento abrange as noções básicas](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Endereçamento IP e sub-redes são importantes neste cenário, pelo que recomendamos que leia alguns artigos e ver alguns vídeos online até que faz sentido. 
    
        Neste exemplo, podemos está optando por utilizar a rede de 10.10.0.0/16 com uma sub-rede de 10.10.1.0/24. Vamos aprovisionar em excesso e usando um /16 sub-rede porque é fácil calcular quais sub-redes estão disponíveis na rede 10.10.0.0/16.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Novamente o **Networking** separador, defina o IP público para **nenhum**. Este passo irá implementar a VM com o acesso apenas a rede virtual.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Crie a VM. O processo demorará cerca de 5 minutos.
8. Depois da VM é criada, aceda ao seu **redes** separador e anote o endereço IP privado para utilizar mais tarde. A VM não deve ter um IP público.

    ![14]

Agora tem um site WordPress implementado inteiramente na sua rede virtual. Este site não está acessível a partir da internet pública.

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar uma aplicação de funções num plano Premium

A próxima etapa é criar uma aplicação de funções num plano Premium. Um plano Premium traz escala sem servidor com todas as vantagens de um plano de serviço de aplicações dedicado. Aplicações de funções criadas com o plano de consumo não suportam a integração da rede virtual.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>Ligar a sua aplicação de função à sua rede virtual

Com um site do WordPress que alojem ficheiros a partir de dentro da sua rede virtual, pode agora ligar a aplicação de funções para a rede virtual.

1.  No portal para a aplicação de funções do passo anterior, selecione **funcionalidades de plataforma**. Em seguida, selecione **redes**.

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Selecione **clique aqui para configurar** sob **integração VNet**.

    ![Estado para a configuração de um recurso de rede](./media/functions-create-vnet/Networking-1.png)

1. Na página de integração de rede virtual, selecione **VNet adicionar (pré-visualização)**.

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Crie uma nova sub-rede para a sua função e o plano de serviço de aplicações a utilizar. Tenha em atenção que o tamanho da sub-rede vai limitar o número total de VMs que podem ser adicionados ao seu plano do serviço de aplicações. A rede virtual encaminha automaticamente o tráfego entre as sub-redes na rede virtual, portanto, não importa que sua função é numa sub-rede diferente da VM. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>Criar uma função que acede a um recurso na sua rede virtual

A aplicação de função agora pode acessar a rede virtual com o nosso site do WordPress. Então, vamos usar a função acedam a esse ficheiro e servi-lo para o utilizador. Neste exemplo, vamos utilizar um site WordPress como a API e um proxy como a função de chamada porque eles são fácil de configurar e visualizar. 

Pode facilmente usar qualquer outra API implementada numa rede virtual. Também pode utilizar outra função com o código que faz chamadas de API para a API implementada na sua rede virtual. Uma instância do SQL Server implementada na sua rede virtual é um exemplo perfeito.

1. No portal, abra a aplicação de funções do passo anterior.
1. Criar um proxy, selecionando **Proxies** > **+**.

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Configure o nome de proxy e a rota. Este exemplo utiliza "/ fábrica" como uma rota.
1. Preencha o IP do seu site WordPress anteriores e defina **URL de back-end** para `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Agora, se tentar visitar o URL de back-end diretamente pelo colá-la num novo separador do browser, a página deve ser tempo limite. Isto acontece porque o site do WordPress está ligado apenas a rede virtual e não na internet. Se colar o URL de proxy no navegador, deverá ver uma imagem de fábrica (obtida a partir do seu site WordPress) no interior da rede virtual. 

A aplicação function app está ligada à internet e a rede virtual. O proxy é receber um pedido através da internet pública e, em seguida, atuando como um proxy HTTP simple para encaminhar essa solicitação ao longo para a rede virtual. O proxy reencaminha, em seguida, a resposta de volta para na Internet pública. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Passos Seguintes

As funções em execução num plano Premium partilham a mesma infra-estrutura subjacente de serviço de aplicações como aplicações web nos planos de PremiumV2. Toda a documentação para aplicações web aplica-se às suas funções de plano Premium.

* [Saiba mais sobre as opções de funcionamento em rede nas funções](./functions-networking-options.md)
* [Ler as funções de FAQ de rede](./functions-networking-faq.md)
* [Saiba mais sobre as redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Ativar mais recursos de rede e controlo com ambientes de serviço de aplicações](../app-service/environment/intro.md)
* [Ligar a recursos locais individuais sem alterações de firewall com ligações híbridas](../app-service/app-service-hybrid-connections.md)
* [Saiba mais sobre os Proxies de funções](./functions-proxies.md)

<!--Image references -->
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
