---
title: Boas práticas para Azure AD B2C
titleSuffix: Azure AD B2C
description: Recomendações e boas práticas a ter em conta ao trabalhar com o Azure Ative Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: d8c0a5ce6f3befd41c0e1399363fd73726693837
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96750225"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Recomendações e boas práticas para o Azure Ative Directory B2C

As seguintes boas práticas e recomendações abrangem alguns dos aspetos primários da integração do Azure Ative Directory (Azure AD) B2C em ambientes de aplicação existentes ou novos.

## <a name="fundamentals"></a>Fundamentos

| Melhores práticas | Description |
|--|--|
| Escolha os fluxos de utilizador para a maioria dos cenários | O Quadro de Experiência de Identidade do Azure AD B2C é a força central do serviço. As políticas descrevem totalmente experiências de identidade como inscrição, inscrição ou edição de perfis. Para ajudá-lo a configurar as tarefas de identidade mais comuns, o portal Azure AD B2C inclui políticas predefinidas e configuráveis chamadas fluxos de utilizador. Com os fluxos do utilizador, pode criar excelentes experiências de utilizador em minutos, com apenas alguns cliques. [Saiba quando utilizar fluxos de utilizador vs. políticas personalizadas](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Registos de aplicações | Todas as aplicações (web, nativas) e API que estão a ser protegidas devem estar registadas no Azure AD B2C. Se uma aplicação tiver uma versão web e nativa do iOS e Android, pode registá-las como uma aplicação no Azure AD B2C com o mesmo ID do cliente. Saiba como [registar aplicações OIDC, SAML, web e native.](./tutorial-register-applications.md?tabs=applications) Saiba mais sobre [os tipos de aplicações que podem ser usados em Azure AD B2C](./application-types.md). |
| Passar para faturação mensal de utilizadores ativos | O Azure AD B2C passou de autenticações ativas mensais para faturação mensal de utilizadores ativos (MAU). A maioria dos clientes vai achar este modelo rentável. [Saiba mais sobre a faturação mensal dos utilizadores ativos.](https://azure.microsoft.com/updates/mau-billing/) |

## <a name="planning-and-design"></a>Planeamento e design

Defina a sua arquitetura de aplicação e serviço, os sistemas atuais de inventário e planeie a sua migração para Azure AD B2C.

| Melhores práticas | Description |
|--|--|
| Arquiteto uma solução de ponta a ponta | Inclua todas as dependências das suas aplicações ao planear uma integração Azure AD B2C. Considere todos os serviços e produtos que estão atualmente no seu ambiente ou que possam precisar de ser adicionados à solução, por exemplo, Funções Azure, sistemas de gestão de relacionamento com o cliente (CRM), gateway de gestão API Azure e serviços de armazenamento. Tome em consideração a segurança e escalabilidade de todos os serviços. |
| Documente as experiências dos seus utilizadores | Detalhe todas as viagens de utilizador que os seus clientes podem experimentar na sua aplicação. Inclua todos os ecrãs e quaisquer fluxos de ramificação que possam encontrar ao interagir com os aspetos de identidade e perfil da sua aplicação. Inclua usabilidade, acessibilidade e localização no seu planeamento. |
| Escolha o protocolo de autenticação certo |  Para uma repartição dos diferentes cenários de aplicação e dos seus fluxos de autenticação recomendados, consulte [Cenários e fluxos de autenticação suportados](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Pilote uma experiência de utilizador de prova de conceito (POC) de ponta a ponta | Comece com as [nossas amostras de código da Microsoft](code-samples.md) e [amostras comunitárias.](https://github.com/azure-ad-b2c/samples) |
| Criar um plano de migração |Planear com antecedência pode fazer com que a migração corra mais suavemente. Saiba mais sobre [a migração dos utilizadores.](user-migration.md)|
| Usabilidade vs segurança | A sua solução deve encontrar o equilíbrio certo entre a usabilidade da aplicação e o nível aceitável de risco da sua organização. |
| Mover dependências no local para a nuvem | Para ajudar a garantir uma solução resiliente, considere mover as dependências de aplicações existentes para a nuvem. |
| Migrar aplicativos existentes para b2clogin.com | A depreciação de login.microsoftonline.com entrará em vigor para todos os inquilinos da Azure AD B2C em 04 de dezembro de 2020. [Saiba mais](b2clogin.md). |
| Utilizar proteção de identidade e acesso condicional | Utilize estas capacidades para um controlo significativamente maior sobre autenticações de risco e políticas de acesso. É necessário Azure AD B2C Premium P2. [Saiba mais](conditional-access-identity-protection-overview.md). |

## <a name="implementation"></a>Implementação

Durante a fase de implementação, considere as seguintes recomendações.

| Melhores práticas | Description |
|--|--|
| Editar políticas personalizadas com a extensão AZURE AD B2C para Código de Estúdio Visual | Baixe o Código do Estúdio Visual e esta [extensão](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)construída pela comunidade a partir do Visual Studio Code Marketplace . Embora não seja um produto oficial da Microsoft, a extensão AZURE AD B2C para o Código do Estúdio Visual inclui várias funcionalidades que ajudam a facilitar o trabalho com políticas personalizadas. |
| Saiba como resolver problemas Azure AD B2C | Saiba como [resolver políticas personalizadas](./troubleshoot-custom-policies.md?tabs=applications) durante o desenvolvimento. Saiba como é um fluxo de autenticação normal e use ferramentas para descobrir anomalias e erros. Por exemplo, utilize [o Application Insights](troubleshoot-with-application-insights.md) para rever os registos de saída das viagens do utilizador. |
| Aproveite a nossa biblioteca de padrões de política personalizados comprovados | Encontre [amostras](https://github.com/azure-ad-b2c/samples) para várias viagens de utilizador melhoradas da Azure AD B2C e gestão de acessos (CIAM). |

## <a name="testing"></a>Testar

Teste e automatize a sua implementação Azure AD B2C.

| Melhores práticas | Description |
|--|--|
| Conta para o tráfego global | Utilize fontes de tráfego de diferentes endereços globais para testar os requisitos de desempenho e localização. Certifique-se de que todos os HTMLs, CSS e dependências podem satisfazer as suas necessidades de desempenho. |
| Testes funcionais e de UI | Teste os fluxos do utilizador de ponta a ponta. Adicione testes sintéticos a cada poucos minutos utilizando Selénio, VS Web Test, etc. |
| Teste de pen | Antes de ir em direto com a sua solução, realize exercícios de teste de penetração para verificar se todos os componentes estão seguros, incluindo quaisquer dependências de terceiros. Verifique se garantiu as suas APIs com fichas de acesso e utilizou o protocolo de autenticação certo para o seu cenário de aplicação. Saiba mais sobre [os testes de penetração](../security/fundamentals/pen-testing.md) e as [regras de teste de penetração unificadas da Microsoft Cloud.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) |
| Teste a/B | Envie as suas novas funcionalidades com um pequeno conjunto de utilizadores aleatórios antes de chegar a toda a sua população. Com o JavaScript ativado no Azure AD B2C, pode integrar-se com ferramentas de teste A/B como Optimizely, Clarity, entre outras. |
| Teste de carga | O Azure AD B2C pode escalar, mas a sua aplicação só pode escalar se todas as suas dependências puderem escalar. Teste de carga as suas APIs e CDN. |
| Limitação |  O Azure AD B2C acelera o tráfego se forem enviados demasiados pedidos da mesma fonte num curto espaço de tempo. Utilize várias fontes de tráfego durante os testes de carga e manuseie o `AADB2C90229` código de erro graciosamente nas suas aplicações. |
| Automatização | Utilize gasodutos de integração e entrega contínuas (CI/CD) para automatizar testes e implementações, por exemplo, [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operações

Gerencie o seu ambiente Azure AD B2C.

| Melhores práticas | Description |
|--|--|
| Criar vários ambientes | Para facilitar as operações e implantação, crie ambientes separados para o desenvolvimento, teste, pré-produção e produção. Crie inquilinos Azure AD B2C para cada um. |
| Use o controlo de versão para as suas políticas personalizadas | Considere usar GitHub, Azure Repos ou outro sistema de controlo de versão baseado na nuvem para as suas políticas personalizadas Azure AD B2C. |
| Utilize a Microsoft Graph API para automatizar a gestão dos seus inquilinos B2C | APIs do Gráfico da Microsoft:<br/>Gerir [o Quadro de Experiência de Identidade](/graph/api/resources/trustframeworkpolicy?preserve-view=true&view=graph-rest-beta) (políticas personalizadas)<br/>[Chaves](/graph/api/resources/trustframeworkkeyset?preserve-view=true&view=graph-rest-beta)<br/>[Fluxos de Utilizador](/graph/api/resources/identityuserflow?preserve-view=true&view=graph-rest-beta) |
| Integre com Azure DevOps | Um [gasoduto CI/CD](deploy-custom-policies-devops.md) facilita o código de movimento entre diferentes ambientes e garante sempre a prontidão da produção.   |
| Integre-se com o Azure Monitor | [Os eventos de registo de auditoria](view-audit-logs.md) só são mantidos por sete dias. [Integre-se com o Azure Monitor](azure-monitor.md) para reter os registos para uso a longo prazo, ou integrar-se com ferramentas de segurança de terceiros e de gestão de eventos (SIEM) para obter informações sobre o seu ambiente. |
| Configuração de alerta e monitorização ativa | [Acompanhe o comportamento do utilizador](./analytics-with-application-insights.md) em Azure AD B2C utilizando Insights de Aplicação. |

## <a name="support-and-status-updates"></a>Assistência e atualizações de estado

Mantenha-se atualizado com o estado do serviço e encontre opções de apoio.

| Melhores práticas | Description |
|--|--|
| [Atualizações de serviço](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Mantenha-se atualizado com as atualizações e anúncios de produtos Azure AD B2C. |
| [Suporte da Microsoft](support-options.md) | Apresentar um pedido de apoio para problemas técnicos Azure AD B2C. O suporte de gestão de faturação e subscrição é fornecido sem custos. |
| [Estado do Azure](https://status.azure.com/status) | Veja o estado de saúde atual de todos os serviços da Azure. |