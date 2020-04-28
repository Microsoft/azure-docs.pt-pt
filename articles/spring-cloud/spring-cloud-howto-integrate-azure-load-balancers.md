---
title: Tutorial - Integrar nuvem de primavera azure com soluções de equilíbrio de carga azure
description: Como integrar a Nuvem de primavera Azure com soluções de equilíbrio de carga Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: 7022c4587b425168fc5bd2182ed65c281633aabf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82177084"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Integrar nuvem de primavera azure com soluções de equilíbrio de carga azure

Azure Spring Cloud suporta microserviços no Azure.  O aumento do negócio pode exigir vários centros de dados com gestão de múltiplos casos de Azure Spring Cloud.

O Azure já fornece diferentes soluções de equilíbrio de carga. Existem três opções para integrar a Nuvem de primavera Azure com soluções de equilíbrio de carga Azure:

1.  Integrar nuvem de primavera azure com gestor de tráfego azure
2.  Integrar nuvem de primavera azure com portal de aplicações azure
3.  Integrar nuvem de primavera azure com porta da frente azure

## <a name="prerequisites"></a>Pré-requisitos

* Nuvem de primavera Azure: Como criar um serviço de nuvem de [primavera Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)
* Gestor de Tráfego Azure: [Como criar um gestor de tráfego](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* Portal da Aplicação Azure: [Como criar um gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* Porta da frente azure: [como criar uma porta da frente](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Integrar nuvem de primavera azure com gestor de tráfego azure

Para integrar a nuvem de primavera azure com o Traffic Manager, adicione os seus pontos finais públicos como pontos finais do gestor de tráfego e, em seguida, configure o domínio personalizado tanto para o gestor de tráfego como para a nuvem de mola Azure.

### <a name="add-endpoint-in-traffic-manager"></a>Adicionar Endpoint no Gestor de Tráfego
Adicione pontos finais no gestor de tráfego:
1.  Especificar **Tipo** para ser *ponto final externo*.
1.  Input nome de domínio totalmente qualificado (FQDN) de cada ponto final da nuvem de primavera Azure.
1. Clique em **OK**.

    ![Gestor de](media/spring-cloud-load-balancers/traffic-manager-1.png) ![Tráfego 1 Gestor de Tráfego 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>Configurar domínio personalizado
Para terminar a configuração:
1.  Inscreva-se no site do seu fornecedor de domínio e crie um mapeamento de registo CNAME do seu domínio personalizado para o nome de domínio padrão do gestor de tráfego Azure.
1.  Siga as instruções [Como adicionar domínio personalizado à Nuvem de Mola Azure](spring-cloud-tutorial-custom-domain.md).
1. Adicione o domínio personalizado acima da ligação ao gestor de tráfego ao serviço de aplicações correspondente sinuoso da nuvem de mola Azure e carregue o certificado SSL lá.

    ![Gestor de Tráfego 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Integrar nuvem de primavera azure com portal de aplicações azure

Para integrar com o serviço de nuvem de mola azul, complete as seguintes configurações:

### <a name="configure-backend-pool"></a>Configure Backend Pool
1. Especificar **o tipo de destino** como endereço *IP* ou *FQDN*.
1. Entre nos pontos finais públicos da nuvem de primavera do Azure.

    ![Porta de entrada de aplicativos 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>Adicionar sonda personalizada
1. Selecione **sondas de saúde** **e,** em seguida, adicione para abrir o diálogo personalizado **da sonda.** 
1. O ponto chave é selecionar *Sim* para Escolher o nome do anfitrião da opção de **definições HTTP backend.**

    ![Porta de entrada de aplicativos 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Configurar a definição de http
1.  Selecione **Definições de Http** **e,** em seguida, adicione uma definição HTTP.
1.  **Sobrepor-se com o novo nome de anfitrião:** selecione *Sim*.
1.  **Substituição do nome**do anfitrião : selecione **Escolha o nome do anfitrião do alvo de backend**.
1.  **Utilize a sonda personalizada**: selecione *Sim* e escolha a sonda personalizada criada acima.

    ![Porta de entrada de aplicativos 3](media/spring-cloud-load-balancers/app-gateway-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Integrar nuvem de primavera azure com porta da frente azure

Para integrar com o serviço Azure Spring Cloud e configurar a piscina de backend, 
1. **Adicione piscina de backend**.
1. Especifique o ponto final do endend adicionando o hospedeiro.

    ![Porta da frente 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  Especifique o tipo de hospedeiro de **backend** como *anfitrião personalizado*.
1.  Insere FQDN do seu azure Spring Cloud pontos finais públicos em **nome de anfitrião backend**.
1.  Aceite o padrão do cabeçalho do anfitrião do **backend,** que é o mesmo que o nome do **anfitrião de backend**.

    ![Porta da frente 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>Passos seguintes
* [Como criar um gestor de tráfego](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* [Como criar um gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* [Como criar uma porta da frente](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)
