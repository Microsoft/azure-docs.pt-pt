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
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: b16c60130836cf0e3b38092b894129f503ee6e83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141674"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Recomendações e boas práticas para o Diretório Ativo Azure B2C

As seguintes boas práticas e recomendações abrangem alguns dos aspetos primários da integração do Azure Ative Directory (Azure AD) B2C em ambientes de aplicação existentes ou novos.

## <a name="fundamentals"></a>Noções Básicas

|  |  |
|--|--|
| Escolha os fluxos de utilizadores para a maioria dos cenários | O Quadro de Experiência de Identidade do Azure AD B2C é a força central do serviço. As políticas descrevem totalmente experiências de identidade como inscrição, inscrição ou edição de perfil. Para ajudá-lo a configurar as tarefas de identidade mais comuns, o portal Azure AD B2C inclui políticas pré-definidas e configuráveis chamadas fluxos de utilizador. Com os fluxos de utilizador, pode criar grandes experiências de utilizador em minutos, com apenas alguns cliques. [Aprenda quando utilizar fluxos de utilizador vs. políticas personalizadas](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Registos de aplicações | Todas as aplicações (web, nativas) e API que estão a ser protegidas devem ser registadas em Azure AD B2C. Se uma aplicação tiver uma versão web e nativa do iOS e Android, pode registá-las como uma aplicação no Azure AD B2C com o mesmo ID do cliente. Saiba como [registar aplicações OIDC, SAML, web e nativas.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications) Saiba mais sobre os tipos de [aplicação que podem ser usados em Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/application-types). |
| Passe para faturação mensal de utilizadores ativos | O Azure AD B2C passou de autenticações ativas mensais para faturação mensal de utilizadores ativos (MAU). A maioria dos clientes achará este modelo rentável. [Saiba mais sobre a faturação mensal](https://azure.microsoft.com/updates/mau-billing/)de utilizadores ativos. |

## <a name="planning-and-design"></a>Planeamento e design

Defina a sua aplicação e arquitetura de serviço, inventário de sistemas atuais e planeje a sua migração para Azure AD B2C.

|  |  |
|--|--|
| Arquiteto uma solução de ponta a ponta | Inclua todas as dependências das suas aplicações ao planear uma integração Azure AD AD B2C. Considere todos os serviços e produtos que estão atualmente no seu ambiente ou que possam precisar de ser adicionados à solução, por exemplo, Funções Azure, sistemas de gestão de relacionamento com o cliente (CRM), gateway de gestão de API Azure e serviços de armazenamento. Ter em conta a segurança e escalabilidade de todos os serviços. |
| Documente as experiências dos seus utilizadores | Detalhe todas as viagens de utilizador que os seus clientes possam experimentar na sua aplicação. Inclua todos os ecrãs e fluxos de ramificação que possam encontrar quando interagirem com os aspetos de identidade e perfil da sua aplicação. Inclua usabilidade, acessibilidade e localização no seu planeamento. |
| Escolha o protocolo de autenticação certo |  Para uma repartição dos diferentes cenários de aplicação e dos seus fluxos de autenticação [recomendados, consulte Cenários e fluxos de autenticação suportados.](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) |
| Pilotar uma experiência de utilizador de ponta a ponta (POC) | Comece com [as nossas amostras](code-samples.md) de código da Microsoft e [amostras comunitárias.](https://github.com/azure-ad-b2c/samples) |
| Criar um plano de migração |Planear o futuro pode fazer a migração correr mais suavemente. Saiba mais sobre [a migração dos utilizadores.](user-migration.md)|
| Usabilidade vs. segurança | A sua solução deve encontrar o equilíbrio certo entre a usabilidade da aplicação e o nível de risco aceitável da sua organização. |
| Mover as dependências no local para a nuvem | Para ajudar a garantir uma solução resiliente, considere mover as dependências de aplicação existentes para a nuvem. |
| Migrar aplicativos existentes para b2clogin.com | A depreciação da login.microsoftonline.com entrará em vigor para todos os inquilinos Da AD B2C do Azure a 04 de dezembro de 2020. [Saiba mais](b2clogin.md). |

## <a name="implementation"></a>Implementação

Durante a fase de implementação, considere as seguintes recomendações.

|  |  |
|--|--|
| Editar políticas personalizadas com a extensão Azure AD B2C para Código de Estúdio Visual | Baixe o Visual Studio Code e esta [extensão](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)construída pela comunidade do Visual Studio Code Marketplace . Apesar de não ser um produto oficial da Microsoft, a extensão Azure AD AD B2C para Visual Studio Code inclui várias funcionalidades que ajudam a facilitar o trabalho com políticas personalizadas. |
| Saiba como resolver problemas Azure AD B2C | Aprenda a [resolver políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) durante o desenvolvimento. Saiba como é um fluxo normal de autenticação e use ferramentas para descobrir anomalias e erros. Por exemplo, utilize [os Insights da Aplicação](troubleshoot-with-application-insights.md) para rever os registos de saída das viagens dos utilizadores. |
| Aproveite a nossa biblioteca de padrões de política personalizados comprovados | Encontre [amostras](https://github.com/azure-ad-b2c/samples) para várias viagens de utilizadores de identidade e acesso de clientes Azure AD B2C melhorados. |


## <a name="testing"></a>Testar

Teste e mate automaticamente a sua implementação Azure AD AD B2C.

|  |  |
|--|--|
| Conta para o tráfego global | Utilize fontes de tráfego de diferentes endereços globais para testar os requisitos de desempenho e localização. Certifique-se de que todos os HTMLs, CSS e dependências podem satisfazer as suas necessidades de desempenho. |
| Testes funcionais e ui | Teste o fluxo do utilizador de ponta a ponta. Adicione testes sintéticos a cada poucos minutos utilizando Selénio, VS Web Test, etc. |
| Teste de canetas | Antes de entrar em direto com a sua solução, realize exercícios de teste de penetração para verificar se todos os componentes estão seguros, incluindo quaisquer dependências de terceiros. Verifique se garantiu as suas APIs com fichas de acesso e utilizou o protocolo de autenticação certo para o seu cenário de candidatura. Saiba mais sobre os testes de [penetração](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) e as [Regras de Teste de Penetração Unificadas da Microsoft Cloud.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) |
| Testes A/B | Voe com as suas novas funcionalidades com um pequeno e aleatório conjunto de utilizadores antes de passar para toda a sua população. Com o JavaScript ativado no Azure AD B2C, pode integrar-se com ferramentas de teste A/B como Optimizely, Clarity, entre outras. |
| Teste de carga | O Azure AD B2C pode escalar, mas a sua aplicação só pode escalar se todas as suas dependências puderem escalar. Teste a carga das suas APIs e CDN. |
| Limitação |  O Azure AD B2C acelera o tráfego se muitos pedidos forem enviados da mesma fonte num curto espaço de tempo. Utilize várias fontes de tráfego `AADB2C90229` durante os testes de carga e manuseie o código de erro graciosamente nas suas aplicações. |
| Automatização | Utilize gasodutos de integração e entrega contínuos (CI/CD) para automatizar testes e implementações, por exemplo, [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operações

Gerencie o seu ambiente Azure AD B2C.

|  |  |
|--|--|
| Criar múltiplos ambientes | Para operações mais fáceis e implantação, crie ambientes separados para desenvolvimento, testes, pré-produção e produção. Crie inquilinos Azure AD B2C para cada um. |
| Utilize o controlo da versão para as suas políticas personalizadas | Considere utilizar o GitHub, Azure Repos ou outro sistema de controlo de versão baseado na nuvem para as suas políticas personalizadas Azure AD B2C. |
| Utilize a Microsoft Graph API para automatizar a gestão dos seus inquilinos B2C | Microsoft Graph APIs:<br/>Gerir [o Quadro de Experiência de Identidade](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) (políticas personalizadas)<br/>[Chaves](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[Fluxos do Utilizador](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Integrar com Azure DevOps | Um [gasoduto CI/CD](deploy-custom-policies-devops.md) facilita o código de deslocação entre diferentes ambientes e garante sempre a prontidão de produção.   |
| Integrar com o Monitor Azure | [Os eventos](view-audit-logs.md) de registo de auditoria só são retidos por sete dias. [Integre o Azure Monitor](azure-monitor.md) para reter os registos para uso a longo prazo, ou integrar-se com ferramentas de informação de segurança de terceiros e gestão de eventos (SIEM) para obter informações sobre o seu ambiente. |
| Configuração de alerta e monitorização ativa | [Acompanhe](active-directory-b2c-custom-guide-eventlogger-appins.md) o comportamento do utilizador no Azure AD B2C utilizando insights de aplicação. |


## <a name="support-and-status-updates"></a>Atualizações de Suporte e Estado

Mantenha-se atualizado com o estado do serviço e encontre opções de suporte.

|  |  |
|--|--|
| [Atualizações de serviço](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Mantenha-se atualizado com as atualizações e anúncios do produto Azure AD B2C. |
| [Suporte da Microsoft](support-options.md) | Apresente um pedido de apoio para questões técnicas Azure AD B2C. O apoio à gestão de faturação e subscrição não é fornecido a qualquer custo. |
| [Estado do Azure](https://status.azure.com/status) | Veja o estado de saúde atual de todos os serviços Azure. |
