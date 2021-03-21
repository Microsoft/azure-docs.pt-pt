---
title: Use a Azure Resource Health para monitorizar a saúde da base de dados
description: Use a Azure Resource Health para monitorizar a base de dados Azure SQL e a saúde da Azure SQL Managed Instance, ajuda-o a diagnosticar e obter suporte quando um problema de Azure impacta os seus recursos SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 02/26/2019
ms.openlocfilehash: 1a74a2f44ac9b03e39e9fea48a3428b82111a48c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472010"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>Utilize a Saúde dos Recursos para resolver problemas de conectividade para a Base de Dados Azure SQL e para a Azure SQL Gestão de Instâncias
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Resource Health](../../service-health/resource-health-overview.md#get-started) for Azure SQL Database and Azure SQL Managed Instance ajuda-o a diagnosticar e obter suporte quando um problema de Azure impacta os seus recursos SQL. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. A Resource Health fornece suporte técnico quando precisa de ajuda com problemas de serviço da Azure.

![Descrição geral](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Verificações de saúde

A Resource Health determina a saúde do seu recurso SQL examinando o sucesso e falha de logins no recurso. Atualmente, a Saúde dos Recursos para o seu recurso SQL Database apenas examina falhas de login devido a erros do sistema e não erro do utilizador. O estado de saúde dos recursos é atualizado a cada 1 a 2 minutos.

## <a name="health-states"></a>Estados de saúde

### <a name="available"></a>Disponível

Um estado de **Disponíveis** significa que a Resource Health não detetou falhas de login devido a erros de sistema no seu recurso SQL.

![Disponível](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degradado

O estado **Degradado** significa que o Resource Health detetou maioritariamente inícios de sessão com êxito, mas também algumas falhas. Estes são provavelmente erros de login transitórios. Para reduzir o impacto dos problemas de ligação causados por erros transitórios de login, implemente [a lógica de reluto](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) no seu código.

![Degradado](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Indisponível

Um estado de **Indisponíveis** significa que a Resource Health detetou falhas de login consistentes no seu recurso SQL. Se o seu recurso permanecer neste estado por um longo período de tempo, contacte o suporte.

![Indisponível](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Desconhecido

O estado de saúde da **Unknown** indica que a Resource Health não recebe informações sobre este recurso há mais de 10 minutos. Embora este estado não seja uma indicação definitiva do estado do recurso, é um importante ponto de dados no processo de resolução de problemas. Se o recurso estiver a funcionar como esperado, o estado do recurso mudará para Disponível após alguns minutos. Se estiver a ter problemas com o recurso, o estado de saúde desconhecido pode sugerir que um evento na plataforma está a afetar o recurso.

![Desconhecido](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informação histórica

Pode aceder até 14 dias de história da saúde na secção de História da Saúde da Saúde. A secção também conterá a razão de inatividade (quando disponível) para os tempos de inatividade reportados pela Resource Health. Atualmente, a Azure mostra o tempo de inatividade do seu recurso de base de dados com uma granularidade de dois minutos. O período de indisponibilidade real é provavelmente inferior a um minuto. A média é de 8 segundos.

### <a name="downtime-reasons"></a>Razões de inatividade

Quando a sua base de dados experimenta tempo de inatividade, a análise é feita para determinar uma razão. Quando disponível, a razão de inatividade é reportada na secção de História da Saúde da Saúde. As razões de inatividade são normalmente publicadas dentro de 45 minutos após um evento.

#### <a name="planned-maintenance"></a>Manutenção planeada

A infraestrutura Azure realiza periodicamente a manutenção planeada – a atualização de componentes de hardware ou software no datacenter. Enquanto a base de dados passa por manutenção, o Azure SQL pode terminar algumas ligações existentes e recusar novas. As falhas de login experimentadas durante a manutenção planeada são tipicamente transitórias, e [a lógica de retenção](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) ajuda a reduzir o impacto. Se continuar a experimentar erros de login, contacte o suporte.

#### <a name="reconfiguration"></a>Reconfiguração

As reconfigurações são consideradas condições transitórias e são esperadas de tempos a tempos. Estes eventos podem ser desencadeados por falhas de equilíbrio de carga ou software/hardware. Qualquer aplicação de produção de clientes que se conecte a uma base de dados em nuvem deve implementar uma lógica robusta de [relembramento](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)de conexão , uma vez que ajudaria a mitigar estas situações e deve geralmente tornar os erros transparentes para o utilizador final.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a lógica de reorientação para erros transitórios](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- [Resolução de problemas, diagnóstico e prevenção de erros de ligação SQL](troubleshoot-common-connectivity-issues.md).
- Saiba mais sobre [configurar alertas de saúde de recursos.](../../service-health/resource-health-alert-arm-template-guide.md)
- Obtenha uma visão geral da Saúde dos [Recursos.](../../application-gateway/resource-health-overview.md)
- Rever [O Recurso Saúde FAQ](../../service-health/resource-health-faq.md).
