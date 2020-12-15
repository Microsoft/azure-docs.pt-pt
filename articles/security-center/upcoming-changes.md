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
ms.date: 12/14/2020
ms.author: memildin
ms.openlocfilehash: 052758079d8d413f7b0fead2a5abf3b47b9a691e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511336"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Mudanças importantes para o Centro de Segurança Azure

> [!IMPORTANT]
> As informações desta página dizem respeito a produtos ou funcionalidades pré-lançamento, que podem ser substancialmente modificadas antes de serem lançadas comercialmente, se é que alguma vez. A Microsoft não compromete ou garante, expressa ou implícita, no que diz respeito às informações fornecidas aqui.

Nesta página, você vai aprender sobre as mudanças que estão planejadas para o Centro de Segurança. Descreve modificações planeadas no produto que podem ter impacto em coisas como a sua pontuação segura ou fluxos de trabalho.

Se procura as últimas notas de lançamento, vai encontrá-las no [What's new in Azure Security Center.](release-notes.md)


## <a name="planned-changes"></a>Alterações planeadas

- [Recursos "não aplicáveis" a serem reportados como "conformes" nas avaliações da Política Azure](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [35 recomendações de pré-visualização adicionadas para aumentar a cobertura do Benchmark de Segurança Azure](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)

### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>Recursos "não aplicáveis" a serem reportados como "conformes" nas avaliações da Política Azure

**Data estimada para a alteração:** janeiro de 2021

Atualmente, os recursos que são avaliados para uma recomendação e que se considera **não aplicáveis** aparecem na Política Azure como "incompatíveis". Nenhuma ação do utilizador pode alterar o seu estado para "Compliant". A partir desta mudança planeada, serão reportados como "conformes" para uma maior clareza.

O único impacto será visto na Política Azure, onde o número de recursos em conformidade aumentará. Não haverá impacto na sua pontuação segura no Centro de Segurança Azure.

### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>35 recomendações de pré-visualização adicionadas para aumentar a cobertura do Benchmark de Segurança Azure

**Data estimada para a alteração:** dezembro de 2020

A azure Security Benchmark é o conjunto de diretrizes específicas da Microsoft para a segurança e conformidade das melhores práticas com base em quadros comuns de conformidade. [Saiba mais sobre a Referência de Segurança do Azure](../security/benchmarks/introduction.md).

As seguintes 35 recomendações de pré-visualização serão adicionadas ao Centro de Segurança para aumentar a cobertura deste benchmark.

As recomendações de pré-visualização não tornam um recurso insalubre, e não estão incluídas nos cálculos da sua pontuação segura. Remedia-os sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação. Saiba mais sobre como responder a estas recomendações em [recomendações remedias no Azure Security Center](security-center-remediate-recommendations.md).

| Controlo de segurança                     | Novas recomendações                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ativar a encriptação em repouso            | - As contas DB da Azure Cosmos devem usar chaves geridas pelo cliente para encriptar dados em repouso<br>- Os espaços de trabalho de aprendizagem automática Azure devem ser encriptados com uma chave gerida pelo cliente (CMK)<br>- Traga a sua própria proteção de dados chave deve ser ativada para servidores MySQL<br>- Traga a sua própria proteção de dados chave deve ser ativada para servidores PostgreSQL<br>- As contas de Serviços Cognitivos devem permitir a encriptação de dados com uma chave gerida pelo cliente (CMK)<br>- Os registos de contentores devem ser encriptados com uma chave gerida pelo cliente (CMK)<br>- As instâncias geridas pela SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso<br>- Os servidores SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso<br>- As contas de armazenamento devem utilizar a chave gerida pelo cliente (CMK) para encriptação                                                                                                                                                              |
| Implementar as melhores práticas de segurança    | - As subscrições devem ter um endereço de e-mail de contacto para questões de segurança<br> - O provisionamento automático do agente Log Analytics deve ser ativado na sua subscrição<br> - A notificação por e-mail para alertas de alta gravidade deve ser ativada<br> - A notificação por e-mail ao proprietário da subscrição para alertas de alta gravidade deve ser ativada<br> - Os cofres-chave devem ter proteção de purga ativada<br> - As abóbadas-chave devem ter uma eliminação suave ativada |
| Gerir acessos e permissões        | - As aplicações de função devem ter "Certificados de Cliente (certificados de cliente incoming)" habilitados |
| Proteja as aplicações contra ataques de DDoS | - Firewall de aplicação web (WAF) deve ser ativado para Gateway de aplicação<br> - Firewall de aplicação web (WAF) deve ser ativado para o serviço de serviço de porta frontal Azure |
| Restringir o acesso não autorizado à rede | - Firewall deve ser ativado no Cofre de Chaves<br> - O ponto final privado deve ser configurado para o Cofre de Chaves<br> - A Configuração da Aplicação deve usar link privado<br> - Azure Cache for Redis deve residir numa rede virtual<br> - Os domínios da Grelha de Eventos Azure devem utilizar link privado<br> - Os tópicos da Grelha de Eventos Azure devem usar link privado<br> - Espaços de aprendizagem de máquinas Azure devem utilizar link privado<br> - O Serviço Azure SignalR deve utilizar link privado<br> - A azure Spring Cloud deve usar injeção de rede<br> - Os registos de contentores não devem permitir o acesso ilimitado à rede<br> - Os registos de contentores devem utilizar ligações privadas<br> - O acesso à rede pública deve ser desativado para servidores MariaDB<br> - O acesso à rede pública deve ser desativado para servidores MySQL<br> - O acesso à rede pública deve ser desativado para servidores PostgreSQL<br> - A conta de armazenamento deve utilizar uma ligação de ligação privada<br> - As contas de armazenamento devem restringir o acesso à rede utilizando regras de rede virtuais<br> - Modelos de construtores de imagem VM devem usar link privado|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Links relacionados:

- [Saiba mais sobre a Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre a Azure Database for MariaDB](../mariadb/overview.md)
- [Saiba mais sobre a Base de Dados Azure para o MySQL](../mysql/overview.md)
- [Saiba mais sobre a Base de Dados Azure para PostgreSQL](../postgresql/overview.md)





## <a name="next-steps"></a>Passos seguintes

Para todas as alterações recentes ao produto, veja [o que há de novo no Azure Security Center?](release-notes.md)