---
title: Tutorial - Integre a nuvem de primavera do Azure com soluções de equilíbrio de carga azure
description: Como integrar a nuvem de primavera do Azure com soluções de equilíbrio de carga azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: cd0b9d1369fb1c0e662de83b7056da0ff7c83bd1
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090833"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Integrar o Azure Spring Cloud nas Soluções de Balanceamento de Carga do Azure

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

A Azure Spring Cloud suporta microserviços em Azure.  O aumento do negócio pode exigir vários centros de dados com a gestão de múltiplas instâncias da Azure Spring Cloud.

O Azure já fornece diferentes soluções de equilíbrio de carga. Existem três opções para integrar a Azure Spring Cloud com soluções de equilíbrio de carga Azure:

1.  Integre a nuvem de primavera do Azure com o Gestor de Tráfego Azure
2.  Integre a nuvem de primavera do Azure com gateway de aplicações Azure
3.  Integre a nuvem de primavera Azure com a porta frontal Azure

## <a name="prerequisites"></a>Pré-requisitos

* Azure Spring Cloud: [Como criar um serviço de nuvem de primavera Azure](./spring-cloud-quickstart.md)
* Gestor de Tráfego Azure: [Como criar um gestor de tráfego](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* Azure App Gateway: [Como criar um gateway de aplicações](../application-gateway/quick-create-portal.md)
* Porta frontal Azure: [Como criar uma porta da frente](../frontdoor/quickstart-create-front-door.md)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Integre a nuvem de primavera do Azure com o Gestor de Tráfego Azure

Para integrar a nuvem de primavera do Azure com o Traffic Manager, adicione os seus pontos finais públicos como pontos finais do gestor de tráfego e, em seguida, configuure o domínio personalizado tanto para o gestor de tráfego como para a nuvem de mola Azure.

### <a name="add-endpoint-in-traffic-manager"></a>Adicionar ponto final no gestor de tráfego
Adicione pontos finais no gestor de tráfego:
1.  Especifique o **tipo** para ser *ponto final externo.*
1.  Inserir o nome de domínio totalmente qualificado (FQDN) de cada ponto final público em nuvem de mola Azure.
1. Clique em **OK**.

    ![Gestor de Tráfego 1 ](media/spring-cloud-load-balancers/traffic-manager-1.png) ![ Gestor de Tráfego 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>Configure o domínio personalizado
Para terminar a configuração:
1.  Inscreva-se no site do seu fornecedor de domínio e crie um mapeamento de registo CNAME do seu domínio personalizado para o nome de domínio padrão do gestor de tráfego Azure.
1.  Siga as instruções [Como adicionar domínio personalizado à Nuvem de primavera de Azure](spring-cloud-tutorial-custom-domain.md).
1. Adicione acima a ligação de domínio personalizado ao gestor de tráfego para o serviço de aplicações correspondente da nuvem de mola Azure e carrevar o certificado SSL lá.

    ![Gestor de Tráfego 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Integre a nuvem de primavera do Azure com gateway de aplicações Azure

Para integrar com o serviço Azure Spring Cloud, complete as seguintes configurações:

### <a name="configure-backend-pool"></a>Configure Backend Pool
1. Especifique **o tipo de alvo** como endereço *IP* ou *FQDN*.
1. Insira os seus pontos finais públicos da nuvem de primavera Azure.

    ![App Gateway 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>Adicionar sonda personalizada
1. Selecione **sondas de saúde** **e** adicione para abrir o diálogo personalizado **da sonda.** 
1. O ponto-chave é selecionar *Sim* para Escolher o nome do anfitrião a partir da opção **backend HTTP.**

    ![App Gateway 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Configuração http configuração de configuração de configuração
1.  Selecione **Definições de Http** e, em **seguida, Adicione** para adicionar uma definição HTTP.
1.  **Substituição com novo nome de anfitrião:** selecione *Sim*.
1.  **Sobreposição do nome do anfitrião**: selecione **Escolha o nome do anfitrião do alvo de backend**.
1.  **Use sonda personalizada**: selecione *Sim* e escolha a sonda personalizada criada acima.

    ![App Gateway 3](media/spring-cloud-load-balancers/app-gateway-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Integre a nuvem de primavera Azure com a porta frontal Azure

Para integrar com o serviço Azure Spring Cloud e configurar a piscina de backend, 
1. **Adicione piscina de backend**.
1. Especifique o ponto final de backend adicionando o hospedeiro.

    ![Porta da frente 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  Especifique **o tipo de anfitrião de backend** como *anfitrião personalizado*.
1.  Inserir FQDN dos seus pontos finais públicos Azure Spring Cloud no **nome do anfitrião backend**.
1.  Aceite o **cabeçalho do anfitrião backend,** que é o mesmo que o **nome do anfitrião de backend**.

    ![Porta da frente 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>Passos seguintes
* [Como criar um gestor de tráfego](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* [Como criar um portal de aplicações](../application-gateway/quick-create-portal.md)
* [Como criar uma porta da frente](../frontdoor/quickstart-create-front-door.md)