---
title: 'Sincronização Azure AD Connect: Compreender e personalizar a sincronização / Microsoft Docs'
description: Explica como funciona a sincronização Azure AD Connect e como personalizar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3b87f40d75d4045155e7dd953dc76ffd9de2b34
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60348744"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Sincronização Azure AD Connect: Compreender e personalizar a sincronização
O Azure Ative Directory Connect (Azure AD Connect sync) é um componente principal do Azure AD Connect. Trata de todas as operações relacionadas com a sincronização de dados de identidade entre o seu ambiente no local e a AD Azure. O sincronizado Azure AD Connect é o sucessor do DirSync, Azure AD Sync e do Gestor de Identidade da Vanguarda com o Connector de Diretório Ativo Azure configurado.

Este tópico é a casa para **o sincronizado Azure AD Connect** (também chamado **motor de sincronização)** e lista links para todos os outros tópicos relacionados com ele. Para ligações ao Azure AD Connect, consulte [Integrando as suas identidades no local com o Diretório Ativo Azure](whatis-hybrid-identity.md).

O serviço de sincronização é composto por dois componentes, o componente de **sincronização Azure AD Connect** no local e o lado de serviço no Azure AD **AD Connect sync**service .

## <a name="azure-ad-connect-sync-topics"></a>Tópicos de sincronização azure AD Connect
| Tópico | O que cobre e quando ler |
| --- | --- |
| **Fundamentos de sincronização azure AD Connect** | |
| [Compreender a arquitetura](concept-azure-ad-connect-sync-architecture.md) |Para aqueles que são novos no motor de sincronização e querem aprender sobre a arquitetura e os termos usados. |
| [Conceitos técnicos](how-to-connect-sync-technical-concepts.md) |Uma versão curta do tema da arquitetura e explica brevemente os termos usados. |
| [Topologias do Azure AD Connect](plan-connect-topologies.md) |Descreve as diferentes topologas e cenários que o motor sincronizado suporta. |
| **Configuração personalizada** | |
| [Executando novamente o assistente de instalação](how-to-connect-installation-wizard.md) |Explica quais as opções que tem disponíveis quando executa novamente o assistente de instalação Azure AD Connect. |
| [Compreender o Aprovisionamento Declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md) |Descreve o modelo de configuração chamado provisionamento declarativo. |
| [Compreender as Expressões de Aprovisionamento Declarativas](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Descreve a sintaxe para a linguagem de expressão utilizada no provisionamento declarativo. |
| [Entender a configuração predefinida](concept-azure-ad-connect-sync-default-configuration.md) |Descreve as regras fora da caixa e a configuração predefinida. Também descreve como as regras funcionam em conjunto para que os cenários fora da caixa funcionem. |
| [Compreender utilizadores e contactos](concept-azure-ad-connect-sync-user-and-contacts.md) |Continua sobre o tema anterior e descreve como a configuração para utilizadores e contactos funciona em conjunto, em particular num ambiente multiflorestal. |
| [Como fazer uma alteração na configuração padrão](how-to-connect-sync-change-the-configuration.md) |Faz-nos passar por como fazer uma mudança de configuração comum para atributos. |
| [Melhores práticas para alterar a configuração predefinida](how-to-connect-sync-best-practices-changing-default-configuration.md) |Limitações de suporte e para fazer alterações na configuração fora da caixa. |
| [Configurar a filtragem](how-to-connect-sync-configure-filtering.md) |Descreve as diferentes opções de como limitar quais os objetos que estão a ser sincronizados com a AD Azure e passo a passo como configurar estas opções. |
| **Características e cenários** | |
| [Impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Descreve a *funcionalidade de eliminação acidental de prevenção* e como configurá-la. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |Descreve o programador incorporado, que importa, sincronizae e exporta dados. |
| [Implementar sincronização de hash de senha](how-to-connect-password-hash-synchronization.md) |Descreve como funciona a sincronização de passwords, como implementar e como operar e resolver problemas. |
| [Repetição de escrita do dispositivo](how-to-connect-device-writeback.md) |Descreve como funciona a reescrita do dispositivo no Azure AD Connect. |
| [Extensões de diretórios](how-to-connect-sync-feature-directory-extensions.md) |Descreve como estender o esquema Azure AD com os seus próprios atributos personalizados. |
| [Escritório 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |Descreve como colocar os recursos do Office 365 do utilizador na mesma região que o utilizador. |
| **Serviço de Sincronização** | |
| [Funcionalidades do serviço de sincronização do Azure AD Connect](how-to-connect-syncservice-features.md) |Descreve o lado do serviço de sincronização e como alterar as definições de sincronização em Azure AD. |
| [Duplicar a resiliência do atributo](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Descreve como ativar e utilizar o **userPrincipalName** e **proxyAddresses** duplicam os valores de resiliência. |
| **Operações e UI** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Descreve o Gestor de Serviços de Sincronização UI, incluindo [Operações,](how-to-connect-sync-service-manager-ui-operations.md) [Conectores,](how-to-connect-sync-service-manager-ui-connectors.md) [Metaverse Designer](how-to-connect-sync-service-manager-ui-mvdesigner.md)e separadores de [Pesquisa Metaverse.](how-to-connect-sync-service-manager-ui-mvsearch.md) |
| [Tarefas e considerações operacionais](how-to-connect-sync-operations.md) |Descreve preocupações operacionais, como a recuperação de desastres. |
| **Como...** | |
| [Redefinir a conta Azure AD](how-to-connect-azureadaccount.md) |Como redefinir as credenciais da conta de serviço utilizada para ligar a partir do sincronde Azure AD Connect ao Azure AD. |
| **Mais informações e referências** | |
| [Portas](reference-connect-ports.md) |Listas que portas você precisa abrir entre o motor de sincronização e os seus diretórios no local e Azure AD. |
| [Atributos sincronizados com o Azure Active Directory](reference-connect-sync-attributes-synchronized.md) |Lista todos os atributos sincronizados entre a AD no local e a AD Azure. |
| [Referência de funções](reference-connect-sync-functions-reference.md) |Lista todas as funções disponíveis no fornecimento declarativo. |

## <a name="additional-resources"></a>Recursos Adicionais
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
