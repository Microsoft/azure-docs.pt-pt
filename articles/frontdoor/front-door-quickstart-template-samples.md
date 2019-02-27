---
title: Exemplos de modelos do Azure Resource Manager – Azure Front Door Service | Microsoft Docs
description: Exemplos de modelos do Azure Resource Manager para o Azure Front Door Service
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: sharadag
ms.openlocfilehash: c0dccdd38f4b945df3702178e7351db58687bf82
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883901"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Modelos de implementação do Azure Resource Manager para o Front Door

A tabela seguinte inclui ligações para modelos de implementação do Azure Resource Manager para o Azure Front Door Service. 

| | |
| ---| ---|
| [Create a basic Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic) (Criar um Front Door básico)| Cria uma configuração básica do Front Door com um único back-end. |
| [Create a Front Door with multiple backends and backend pools and URL based routing](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends) (Criar um Front Door com vários back-ends e conjuntos de back-end e encaminhamento baseado em URL)| Cria um Front Door com balanceamento de carga configurado para vários back-ends no conjunto de back-end TA e também em conjuntos de back-end no caminho do URL. |
| [Onboard a custom domain with HTTPS (Front Door managed cert) with Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain) (Carregar um domínio personalizado com HTTPS (certificados geridos pelo Front Door) com o Front Door)| Adicione um domínio personalizado ao Front Door e ative o tráfego HTTPS com um certificado gerido pelo Front Door gerado através do DigiCert. |
| [Criar a porta da frente com filtragem geográfica](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Crie um Front Door que permite/bloqueia o tráfego de determinados países. |
| [Control Health Probes for your backends on Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes) (Controlar as Sondas de Estado de Funcionamento dos back-ends no Front Door)| Atualize o Front Door para alterar as definições da sonda de estado de funcionamento ao atualizar o caminho da sonda e também os intervalos nos quais as sondas serão enviadas. |
| [Create Front Door with Active/Standby backend configuration](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb) (Criar o Front Door com a configuração de back-end Ativa/Inativa)| Cria um Front Door que demonstra um encaminhamento com base na prioridade para a topologia de aplicação Ativa/Inativa, ou seja, por predefinição, envia todo o tráfego para o back-end principal (com a prioridade mais alta) até se tornar indisponível. |
| [Create Front Door with caching enabled for certain routes](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching) (Criar o Front Door com a colocação em cache ativada para determinadas rotas)| Cria um Front Door com colocação em cache ativada para a configuração de encaminhamento definida, portanto, coloca em cache todos os recursos estáticos da carga de trabalho. |
| [Configure Session Affinity for your Front Door host names](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) (Configurar a Afinidade de Sessão para os nomes de anfitrião do Front Door) | Atualiza um Front Door para ativar a afinidade de sessão para o anfitrião de front-end e envia assim o tráfego subsequente da mesma sessão do utilizador para o mesmo back-end. |
| [Onboard a custom domain with custom SSL certificate (for HTTPS) for your Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-custom-byoc) (Carregar um domínio personalizado com o certificado SSL personalizado (para HTTPS) para o Front Door) | Carrega um domínio personalizado para um Front Door com o certificado SSL personalizado, também denominado de cenário Traga o seu Próprio Certificado. |
| [Configure Front Door for client IP whitelisting or blacklisting](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip) (Configurar o Front Door para a lista de bloqueio ou a lista de permissões de IPs do cliente)| Configura um Front Door para restringir o tráfego de determinados IPs do cliente através do controlo de acesso personalizado com IPs de cliente. |
| [Configurar a porta de entrada para tomar medidas com parâmetros específicos de http](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Configura um Front Door para permitir ou bloquear determinado tráfego com base nos parâmetros http no pedido recebido com regras personalizadas para o controlo de acesso através de parâmetros http. |
| [Configurar a limitação de velocidade de porta de entrada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Configura um Front Door para limitar as taxas do tráfego recebido para um anfitrião de front-end especificado. |
| | |

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).