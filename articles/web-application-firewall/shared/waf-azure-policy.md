---
title: Firewall de aplicação web Azure e Política Azure
description: A azure Web Application Firewall (WAF) combinada com a Azure Policy pode ajudar a impor padrões organizacionais e avaliar o cumprimento em escala dos recursos da WAF
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: rimansdo
ms.openlocfilehash: 4c1fd53eb6ebf1f1aebdfba99b736e26bd6cff2b
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85306962"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Firewall de aplicação web Azure e Política Azure

O Azure Web Application Firewall (WAF) combinado com a Azure Policy pode ajudar a impor os padrões organizacionais e avaliar o cumprimento em escala dos recursos da WAF. A política Azure é um instrumento de governação que proporciona uma visão agregada para avaliar o estado geral do ambiente, com a capacidade de aprofundar a granularidade por recurso, por política. A política da Azure também ajuda a levar os seus recursos ao cumprimento através da reparação a granel dos recursos existentes e da remediação automática de novos recursos.

## <a name="azure-policies-for-web-application-firewall"></a>Políticas Azure para firewall de aplicações web

Existem várias políticas de Azure incorporadas para gerir os recursos da WAF. Uma repartição das políticas e das suas funcionalidades são as seguintes:

1. **O Firewall da Aplicação Web deve ser ativado para o Serviço de Porta Frontal ou Gateway de aplicação Azure**: Os serviços de porta frontal azure e gateways de aplicações são avaliados se houver um WAF presente na criação de recursos. A apólice tem três efeitos: Auditoria, Negação e Deficiente. A auditoria rastreia quando um Serviço de Porta Frontal Azure ou Gateway de aplicação não tem um WAF e permite que os utilizadores vejam o que o Azure Front Door Service ou o Application Gateway não cumprem atualmente. O Deny impede que qualquer Serviço de Porta Frontal Azure ou Gateway de aplicação seja criado se um WAF não estiver ligado. Os deficientes desligam esta apólice.

2. **Web Application Firewall deve ser um modo definido para o Application Gateway e Azure Front Door Service**: Web Application Firewall é avaliado em que modo está, quer prevenção quer deteção. A política garante a consistência do modo através das Firewalls de Aplicações Web. A apólice tem três efeitos: Auditoria, Negação e Deficiente. A auditoria rastreia quando um WAF não se encaixa no modo especificado. O Deny impede que qualquer WAF seja criado se não estiver no modo correto. Os deficientes desligam esta apólice.


## <a name="launch-an-azure-policy"></a>Lançar uma Política Azure


1.  Na página inicial do Azure, escreva Política na barra de pesquisa e clique no ícone Azure Policy

2.  No serviço de política Azure, em **Autoria,** selecione **Atribuições**.

![Firewall de aplicação web Azure](../media/waf-azure-policy/policy-home.png)

3.  Na página Atribuições, selecione o ícone **de política de atribuir** no topo.

![Firewall de aplicação web Azure](../media/waf-azure-policy/assign-policy.png)

4.  No separador básicos da página 'Atribuir', atualize os seguintes campos:
    1.  **Âmbito**: Selecione o que as assinaturas E os grupos de recursos do Azure devem ser impactados pela Política Azure.
    2.  **Exclusões**: Selecione quaisquer recursos do âmbito de aplicação para excluir da política 
    3.  **Definição de Política**: Selecione a Política Azure para aplicar ao âmbito com exclusões. Digite "Firewall de aplicação web" na barra de pesquisa para escolher a política de Azure de Firewall de Aplicação Web relevante.

![Firewall de aplicação web Azure](../media/waf-azure-policy/policy-listings.png)


5.  Selecione o **separador Parâmetros** e atualize os parâmetros de políticas. Para clarificar melhor o que o parâmetro faz, paire sobre o ícone de informação ao lado do nome do parâmetro para mais esclarecimentos.

6.  Selecione **Rever + criar** para finalizar a sua política de Azure. A política de Azure levará aproximadamente 15 minutos até estar ativa para novos recursos.
