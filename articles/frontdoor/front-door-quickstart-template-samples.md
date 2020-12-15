---
title: Amostras de modelo do Azure Resource Manager - Azure Front Door
description: Saiba mais sobre as amostras de modelo do Gestor de Recursos para a porta frontal Azure, incluindo modelos para criar uma porta frontal básica e configurar a limitação da taxa da porta frontal.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2020
ms.author: duau
ms.openlocfilehash: bac1df020bf2a683fc04a4d05ae73311e149f70c
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511778"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Modelos de implementação do Azure Resource Manager para o Front Door

A tabela seguinte inclui links para modelos de modelos de implementação do Azure Resource Manager para a Porta Frontal Azure. 

| Modelo | Descrição |
| ---| ---|
| [Create a basic Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic) (Criar um Front Door básico)| Cria uma configuração básica do Front Door com um único back-end. |
| [Create a Front Door with multiple backends and backend pools and URL based routing](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends) (Criar um Front Door com vários back-ends e conjuntos de back-end e encaminhamento baseado em URL)| Cria um Front Door com balanceamento de carga configurado para vários back-ends no conjunto de back-end TA e também em conjuntos de back-end no caminho do URL. |
| [A bordo de um domínio personalizado com porta da frente](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Adicione um domínio personalizado à sua Porta da Frente. |
| [Criar porta da frente com geo filtragem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Criar uma Porta frontal que permita/bloqueie o tráfego de determinados países/regiões. |
| [Control Health Probes for your backends on Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes) (Controlar as Sondas de Estado de Funcionamento dos back-ends no Front Door)| Atualize o Front Door para alterar as definições da sonda de estado de funcionamento ao atualizar o caminho da sonda e também os intervalos nos quais as sondas serão enviadas. |
| [Create Front Door with Active/Standby backend configuration](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb) (Criar o Front Door com a configuração de back-end Ativa/Inativa)| Cria um Front Door que demonstra um encaminhamento com base na prioridade para a topologia de aplicação Ativa/Inativa, ou seja, por predefinição, envia todo o tráfego para o back-end principal (com a prioridade mais alta) até se tornar indisponível. |
| [Create Front Door with caching enabled for certain routes](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching) (Criar o Front Door com a colocação em cache ativada para determinadas rotas)| Cria um Front Door com colocação em cache ativada para a configuração de encaminhamento definida, portanto, coloca em cache todos os recursos estáticos da carga de trabalho. |
| [Configure Session Affinity for your Front Door host names](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) (Configurar a Afinidade de Sessão para os nomes de anfitrião do Front Door) | Atualiza um Front Door para ativar a afinidade de sessão para o anfitrião de front-end e envia assim o tráfego subsequente da mesma sessão do utilizador para o mesmo back-end. |
| [Configurar porta frontal para o cliente IP permitir a lista ou bloquear](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Configura um Front Door para restringir o tráfego de determinados IPs do cliente através do controlo de acesso personalizado com IPs de cliente. |
| [Configurar porta frontal para tomar medidas com parâmetros específicos http](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Configura um Front Door para permitir ou bloquear determinado tráfego com base nos parâmetros http no pedido recebido com regras personalizadas para o controlo de acesso através de parâmetros http. |
| [Configurar a taxa da porta frontal limitando](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Configura um Front Door para limitar as taxas do tráfego recebido para um anfitrião de front-end especificado. |
| | |

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
