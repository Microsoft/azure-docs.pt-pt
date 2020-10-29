---
title: Mudanças importantes que chegam ao Centro de Segurança Azure
description: Próximas alterações ao Azure Security Center que poderá ter de estar atento e para as quais poderá ter de planear
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: 549a95b0b2ffc2b2d2bf5670a961e0454683e33a
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026722"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Mudanças importantes para o Centro de Segurança Azure

> [!IMPORTANT]
> As informações desta página dizem respeito a produtos ou funcionalidades pré-lançamento, que podem ser substancialmente modificadas antes de serem lançadas comercialmente, se é que alguma vez. A Microsoft não compromete ou garante, expressa ou implícita, no que diz respeito às informações fornecidas aqui.

Nesta página, você vai aprender sobre as mudanças que estão planejadas para o Centro de Segurança. Descreve modificações planeadas no produto que podem ter impacto em coisas como a sua pontuação segura ou fluxos de trabalho.

Se procura as últimas notas de lançamento, vai encontrá-las no [What's new in Azure Security Center.](release-notes.md)


## <a name="planned-changes"></a>Alterações planeadas

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>Recomendações relacionadas com o Benchmark de Segurança Azure a adicionar (pré-visualização)

| Aspeto | Detalhes |
|---------|---------|
|Data do anúncio | 26 de outubro de 2020  |
|Data para esta alteração  |  novembro de 2020 |
|Impacto     | Potencialmente, mais recomendações para rever.<br>Sem impacto imediato na pontuação segura - As recomendações de pré-visualização não afetam a sua pontuação segura.<br>Sem impacto imediato no estado de saúde dos seus recursos - As recomendações de pré-visualização não tornam um recurso "insalubre".|
|  |  |

A azure Security Benchmark é o conjunto de diretrizes específicas da Microsoft para a segurança e conformidade das melhores práticas com base em quadros comuns de conformidade. [Saiba mais sobre a Referência de Segurança do Azure](../security/benchmarks/introduction.md).

As seguintes 18 novas recomendações serão adicionadas ao Centro de Segurança para aumentar a cobertura do benchmark.

As recomendações de pré-visualização não tornam um recurso insalubre, e não estão incluídas nos cálculos da sua pontuação segura. Remedia-os sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação. Saiba mais sobre como responder a estas recomendações em [recomendações remedias no Azure Security Center](security-center-remediate-recommendations.md).

- A azure Backup deve ser ativado para máquinas virtuais
- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL
- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL
- Backup geo-redundante deve ser ativado para Azure Database for MariaDB
- Backup geo-redundante deve ser ativado para Azure Database para MySQL
- A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para PostgreSQL
- Java deve ser atualizado para a versão mais recente para a sua aplicação API
- Java deve ser atualizado para a versão mais recente para a sua aplicação de função
- Java deve ser atualizado para a versão mais recente para a sua aplicação web
- PHP deve ser atualizado para a versão mais recente para a sua aplicação API
- PHP deve ser atualizado para a versão mais recente para a sua aplicação web
- O ponto final privado deve ser ativado para servidores MariaDB
- O ponto final privado deve ser ativado para servidores MySQL
- O ponto final privado deve ser ativado para servidores PostgreSQL
- Python deve ser atualizado para a versão mais recente para a sua aplicação API
- Python deve ser atualizado para a versão mais recente para a sua aplicação de função
- Python deve ser atualizado para a versão mais recente para a sua aplicação web
- As aplicações web devem solicitar um certificado SSL para todos os pedidos de entrada

Links relacionados:

- [Saiba mais sobre a Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre as aplicações AZURE API](../app-service/app-service-web-tutorial-rest-api.md)
- [Saiba mais sobre aplicações de função Azure](../azure-functions/functions-overview.md)
- [Saiba mais sobre as aplicações web Azure](../app-service/overview.md)
- [Saiba mais sobre a Azure Database for MariaDB](../mariadb/overview.md)
- [Saiba mais sobre a Base de Dados Azure para o MySQL](../mysql/overview.md)
- [Saiba mais sobre a Base de Dados Azure para PostgreSQL](../postgresql/overview.md)

## <a name="next-steps"></a>Passos seguintes

Para todas as alterações recentes ao produto, veja [o que há de novo no Azure Security Center?](release-notes.md)