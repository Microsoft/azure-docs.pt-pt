---
title: Obtenha insights usando o backup center
description: Aprenda a analisar tendências históricas e a obter informações mais profundas sobre as suas cópias de segurança com o Backup Center.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c48173749a9b47be7eeb906e9f8eec716e0cb200
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506021"
---
# <a name="obtain-insights-using-backup-center"></a>Obtenha insights usando o backup center

Para analisar tendências históricas e obter informações mais profundas sobre as suas cópias de segurança, o Backup Center fornece uma interface para [Relatórios de Backup](configure-reports.md), que utiliza [registos de monitores Azure](../azure-monitor/logs/data-platform-logs.md) e [livros de trabalho Azure.](../azure-monitor/visualize/workbooks-overview.md) Os Relatórios de Cópia de Segurança oferecem as seguintes capacidades:

- Alocação e previsão de armazenamento em nuvem consumido.

- Auditoria de backups e restauros.

- Identificar as principais tendências em diferentes níveis de granularidade.

- Ganhar visibilidade e insights sobre oportunidades de otimização de custos para os seus backups.

## <a name="supported-scenarios"></a>Cenários suportados

- Os Relatórios de Cópia de Segurança não estão atualmente disponíveis para a Base de Dados Azure para cópia de segurança do servidor PostgreSQL.

- Consulte a matriz de [suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários suportados e não apoiados.

## <a name="get-started"></a>Introdução

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Configure os seus cofres para enviar dados para um espaço de trabalho Log Analytics

[Saiba como configurar as definições de diagnóstico em escala para os seus cofres](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>Ver Relatórios de Backup no portal do centro de backup

A seleção do item do menu **Relatórios de Cópia de Segurança** no centro de backup abre os relatórios. Escolha um ou mais espaços de trabalho do Log Analytics para visualizar e analisar informações chave nas suas cópias de segurança.

![Relatórios de backup no Backup Center](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

Seguem-se as vistas disponíveis:

1. **Resumo** - Use este separador para obter uma visão geral de alto nível da sua propriedade de reserva. [Saiba mais](./configure-reports.md#summary)

2. **Itens de reserva** - Utilize este separador para ver informações e tendências sobre o armazenamento na nuvem consumidas a um nível de produto de backup. [Saiba mais](./configure-reports.md#backup-items)

3. **Utilização** - Utilize este separador para visualizar os parâmetros de faturação das chaves para as suas cópias de segurança. [Saiba mais](./configure-reports.md#usage)

4. **Empregos** - Utilize este separador para ver as tendências de longo prazo em matéria de empregos, como o número de empregos falhados por dia e as principais causas de insuficiência de emprego. [Saiba mais](./configure-reports.md#jobs)

5. **Políticas** - Utilize este separador para visualizar informações sobre todas as suas políticas ativas, tais como o número de itens associados e o armazenamento total de nuvem consumido por itens apoiados por uma determinada política. [Saiba mais](./configure-reports.md#policies)

6. **Otimize** - Use este separador para ganhar visibilidade em potenciais oportunidades de otimização de custos para as suas cópias de segurança. [Saiba mais](./configure-reports.md#optimize)

7. **Adesão à política** - Utilize este separador para ganhar visibilidade para saber se cada instância de backup teve pelo menos uma cópia de segurança bem sucedida por dia. [Saiba mais](./configure-reports.md#policy-adherence)

Também pode configurar e-mails para quaisquer relatórios usando a funcionalidade [Relatório de E-mail.](backup-reports-email.md)

## <a name="next-steps"></a>Passos seguintes

- [Monitorizar e operar backups](backup-center-monitor-operate.md)
- [Governe a sua propriedade de reserva](backup-center-govern-environment.md)
- [Execute ações usando o backup center](backup-center-actions.md)