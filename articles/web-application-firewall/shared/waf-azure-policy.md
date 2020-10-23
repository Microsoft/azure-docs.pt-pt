---
title: Firewall de aplicação web Azure e Política Azure
description: A azure Web Application Firewall (WAF) combinada com a Azure Policy pode ajudar a impor padrões organizacionais e avaliar o cumprimento em escala dos recursos da WAF
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 564af59c473d5c3f55bef059905500496adf060c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165915"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Firewall de aplicação web Azure e Política Azure

O Azure Web Application Firewall (WAF) combinado com a Azure Policy pode ajudar a impor os padrões organizacionais e avaliar o cumprimento em escala dos recursos da WAF. A política Azure é um instrumento de governação que proporciona uma visão agregada para avaliar o estado geral do ambiente, com a capacidade de aprofundar a granularidade por recurso, por política. A política da Azure também ajuda a levar os seus recursos ao cumprimento através da reparação a granel dos recursos existentes e da remediação automática de novos recursos.

## <a name="azure-policies-for-web-application-firewall"></a>Políticas Azure para firewall de aplicações web

Existem várias políticas de Azure incorporadas para gerir os recursos da WAF. Uma repartição das políticas e das suas funcionalidades são as seguintes:

1. **A Firewall de Aplicação Web (WAF) deve ser ativada para o Serviço de Porta Frontal Azure**: Os serviços da porta frontal azure são avaliados se houver um WAF presente na criação de recursos. A apólice tem três efeitos: Auditoria, Negação e Deficiente. A auditoria rastreia quando um Serviço de Porta Frontal Azure não tem um WAF e permite que os utilizadores vejam o que o Serviço de Porta Frontal Azure não cumpre. O Deny impede que qualquer Serviço de Porta Frontal Azure seja criado se um WAF não estiver ligado. Os deficientes desligam esta apólice.

2. **A Firewall de Aplicação Web (WAF) deve ser ativada para o Gateway de Aplicação**: Os gateways de aplicações são avaliados se houver um WAF presente na criação de recursos. A apólice tem três efeitos: Auditoria, Negação e Deficiente. A auditoria rastreia quando um Gateway de aplicações não tem um WAF e permite que os utilizadores vejam o que o Gateway de Aplicação não cumpre. O Deny impede que qualquer Gateway de aplicação seja criado se um WAF não estiver ligado. Os deficientes desligam esta apólice.

3. **A Firewall de Aplicação Web (WAF) deve utilizar o modo especificado para o Serviço de Porta Frontal Azure**: Determina a utilização do modo 'Detecção' ou 'Prevenção' para estar ativo em todas as políticas de Firewall de Aplicações Web para o Serviço de Porta Frontal Azure. A apólice tem três efeitos: Auditoria, Negação e Deficiente. A auditoria rastreia quando um WAF não se encaixa no modo especificado. O Deny impede que qualquer WAF seja criado se não estiver no modo correto. Os deficientes desligam esta apólice.

4. **O Firewall de Aplicação Web (WAF) deve utilizar o modo especificado para o Gateway**de Aplicação : Obriga à utilização do modo 'Detecção' ou 'Prevenção' para estar ativo em todas as políticas de Firewall de Aplicação Web para Gateway de aplicações. A apólice tem três efeitos: Auditoria, Negação e Deficiente. A auditoria rastreia quando um WAF não se encaixa no modo especificado. O Deny impede que qualquer WAF seja criado se não estiver no modo correto. Os deficientes desligam esta apólice.


## <a name="launch-an-azure-policy"></a>Lançar uma Política Azure


1.  Na página inicial do Azure, escreva Política na barra de pesquisa e clique no ícone Azure Policy

2.  No serviço de política Azure, em **Autoria,** selecione **Atribuições**.

[!div class="mx-imgBorder"]
![Firewall de aplicação web Azure](../media/waf-azure-policy/policy-home.png)

3.  Na página Atribuições, selecione o ícone **de política de atribuir** no topo.

[!div class="mx-imgBorder"]
![Screenshot que mostra o separador Básico na página de política de Atribuir.](../media/waf-azure-policy/assign-policy.png)

4.  No separador básicos da página 'Atribuir', atualize os seguintes campos:
    1.  **Âmbito**: Selecione o que as assinaturas E os grupos de recursos do Azure devem ser impactados pela Política Azure.
    2.  **Exclusões**: Selecione quaisquer recursos do âmbito de aplicação para excluir da política 
    3.  **Definição de Política**: Selecione a Política Azure para aplicar ao âmbito com exclusões. Digite "Firewall de aplicação web" na barra de pesquisa para escolher a política de Azure de Firewall de Aplicação Web relevante.

[!div class="mx-imgBorder"]
![Screenshot que mostra as definições de política disponíveis.](../media/waf-azure-policy/policy-listing.png)


5.  Selecione o **separador Parâmetros** e atualize os parâmetros de políticas. Para clarificar melhor o que o parâmetro faz, paire sobre o ícone de informação ao lado do nome do parâmetro para mais esclarecimentos.

6.  Selecione **Rever + criar** para finalizar a sua política de Azure. A política de Azure levará aproximadamente 15 minutos até estar ativa para novos recursos.
