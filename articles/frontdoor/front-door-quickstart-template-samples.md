---
title: Amostras de modelo de gestor de recursos azure - Porta da frente azure
description: Amostras de modelo do Gestor de Recursos Azure para a porta da frente do Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: sharadag
ms.openlocfilehash: de3b13162e7828542331e7e7a34d0101f7feaf71
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79471662"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Modelos de implementação do Azure Resource Manager para o Front Door

A tabela seguinte inclui links para modelos de modelo de implementação do Gestor de Recursos Azure para a Porta Frontal Azure. 

| | |
| ---| ---|
| [Create a basic Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic) (Criar um Front Door básico)| Cria uma configuração básica do Front Door com um único back-end. |
| [Create a Front Door with multiple backends and backend pools and URL based routing](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends) (Criar um Front Door com vários back-ends e conjuntos de back-end e encaminhamento baseado em URL)| Cria um Front Door com balanceamento de carga configurado para vários back-ends no conjunto de back-end TA e também em conjuntos de back-end no caminho do URL. |
| [Onboard a custom domain with HTTPS (Front Door managed cert) with Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain) (Carregar um domínio personalizado com HTTPS (certificados geridos pelo Front Door) com o Front Door)| Adicione um domínio personalizado ao Front Door e ative o tráfego HTTPS com um certificado gerido pelo Front Door gerado através do DigiCert. |
| [Criar porta da frente com filtragem geo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Crie uma Porta Da Frente que permita/bloqueie o tráfego de certos países/regiões. |
| [Control Health Probes for your backends on Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes) (Controlar as Sondas de Estado de Funcionamento dos back-ends no Front Door)| Atualize o Front Door para alterar as definições da sonda de estado de funcionamento ao atualizar o caminho da sonda e também os intervalos nos quais as sondas serão enviadas. |
| [Create Front Door with Active/Standby backend configuration](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb) (Criar o Front Door com a configuração de back-end Ativa/Inativa)| Cria um Front Door que demonstra um encaminhamento com base na prioridade para a topologia de aplicação Ativa/Inativa, ou seja, por predefinição, envia todo o tráfego para o back-end principal (com a prioridade mais alta) até se tornar indisponível. |
| [Create Front Door with caching enabled for certain routes](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching) (Criar o Front Door com a colocação em cache ativada para determinadas rotas)| Cria um Front Door com colocação em cache ativada para a configuração de encaminhamento definida, portanto, coloca em cache todos os recursos estáticos da carga de trabalho. |
| [Configure Session Affinity for your Front Door host names](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) (Configurar a Afinidade de Sessão para os nomes de anfitrião do Front Door) | Atualiza um Front Door para ativar a afinidade de sessão para o anfitrião de front-end e envia assim o tráfego subsequente da mesma sessão do utilizador para o mesmo back-end. |
| [Configure Front Door for client IP whitelisting or blacklisting](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip) (Configurar o Front Door para a lista de bloqueio ou a lista de permissões de IPs do cliente)| Configura um Front Door para restringir o tráfego de determinados IPs do cliente através do controlo de acesso personalizado com IPs de cliente. |
| [Configure a porta da frente para tomar medidas com parâmetros http específicos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Configura um Front Door para permitir ou bloquear determinado tráfego com base nos parâmetros http no pedido recebido com regras personalizadas para o controlo de acesso através de parâmetros http. |
| [Configure a limitação da tarifa da porta da frente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Configura um Front Door para limitar as taxas do tráfego recebido para um anfitrião de front-end especificado. |
| | |

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).