---
title: Firewall de aplicação web Azure e Política Azure
description: A azure Web Application Firewall (WAF) combinada com a Azure Policy pode ajudar a impor padrões organizacionais e avaliar o cumprimento em escala dos recursos da WAF
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 7798d7e960286d4f8aa971eb2eb0b03d24bd6360
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589462"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Firewall de aplicação web Azure e Política Azure

O Azure Web Application Firewall (WAF) combinado com a Azure Policy pode ajudar a impor os padrões organizacionais e avaliar o cumprimento em escala dos recursos da WAF. A política Azure é um instrumento de governação que proporciona uma visão agregada para avaliar o estado geral do ambiente, com a capacidade de aprofundar a granularidade por recurso, por política. A política da Azure também ajuda a levar os seus recursos ao cumprimento através da reparação a granel dos recursos existentes e da remediação automática de novos recursos.

## <a name="azure-policy-for-web-application-firewall"></a>Política de Azure para firewall de aplicações web

Existem várias definições de Política Azure incorporadas para gerir os recursos da WAF. Uma repartição das definições políticas e das suas funcionalidades são as seguintes:

1. **A Firewall de Aplicação Web (WAF) deve ser ativada para o Serviço de Porta Frontal Azure**: Os serviços da porta frontal azure são avaliados se houver um WAF presente na criação de recursos. A apólice tem três efeitos: Auditoria, Negação e Deficiente. A auditoria rastreia quando um Serviço de Porta Frontal Azure não tem um WAF e permite que os utilizadores vejam o que o Serviço de Porta Frontal Azure não cumpre. O Deny impede que qualquer Serviço de Porta Frontal Azure seja criado se um WAF não estiver ligado. Os deficientes desligam esta apólice.

2. **A Firewall de Aplicação Web (WAF) deve ser ativada para o Gateway de Aplicação**: Os gateways de aplicações são avaliados se houver um WAF presente na criação de recursos. A apólice tem três efeitos: Auditoria, Negação e Deficiente. A auditoria rastreia quando um Gateway de aplicações não tem um WAF e permite que os utilizadores vejam o que o Gateway de Aplicação não cumpre. O Deny impede que qualquer Gateway de aplicação seja criado se um WAF não estiver ligado. Os deficientes desligam esta apólice.

3. **A Firewall de Aplicação Web (WAF) deve utilizar o modo especificado para o Serviço de Porta Frontal Azure**: Determina a utilização do modo 'Detecção' ou 'Prevenção' para estar ativo em todas as políticas de Firewall de Aplicações Web para o Serviço de Porta Frontal Azure. A apólice tem três efeitos: Auditoria, Negação e Deficiente. A auditoria rastreia quando um WAF não se encaixa no modo especificado. O Deny impede que qualquer WAF seja criado se não estiver no modo correto. Os deficientes desligam esta apólice.

4. **O Firewall de Aplicação Web (WAF) deve utilizar o modo especificado para o Gateway** de Aplicação : Obriga à utilização do modo 'Detecção' ou 'Prevenção' para estar ativo em todas as políticas de Firewall de Aplicação Web para Gateway de aplicações. A apólice tem três efeitos: Auditoria, Negação e Deficiente. A auditoria rastreia quando um WAF não se encaixa no modo especificado. O Deny impede que qualquer WAF seja criado se não estiver no modo correto. Os deficientes desligam esta apólice.

## <a name="launch-an-azure-policy"></a>Lançar uma Política Azure

1.  Na página inicial do Azure, escreva Política na barra de pesquisa e clique no ícone Azure Policy

2.  No serviço Azure Policy, em **Autoria,** selecione **Atribuições**.

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Separador de atribuições dentro da Política Azure":::

3.  Na página Atribuições, selecione o ícone **de política de atribuir** no topo.

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="Separador básico na página 'Política de Atribuir'":::

4.  No separador básicos da página 'Atribuir', atualize os seguintes campos:
    1.  **Âmbito**: Selecione o que as assinaturas E os grupos de recursos do Azure devem ser impactados pela definição de política.
    2.  **Exclusões**: Selecione quaisquer recursos do âmbito de aplicação para excluir da atribuição de políticas.
    3.  **Definição de Política**: Selecione a definição de política para aplicar ao âmbito com exclusões. Digite "Firewall de aplicação web" na barra de pesquisa para escolher a política de Azure de Firewall de Aplicação Web relevante.

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="Screenshot que mostra o separador 'Definições políticas' na página 'Definições disponíveis'.":::

5.  Selecione o **separador Parâmetros** e atualize os parâmetros de atribuição de políticas. Para clarificar melhor o que o parâmetro faz, paire sobre o ícone de informação ao lado do nome do parâmetro para mais esclarecimentos.

6.  Selecione **Review + criar** para finalizar a sua atribuição de política. A atribuição da política leva aproximadamente 15 minutos até estar ativa para novos recursos.
