---
title: 'Azure AD Connect sync: Compreenda e personalize a sincronização | Microsoft Docs'
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
ms.topic: how-to
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cea26cb119f64679807bc6c5eaadb41b341e5d5a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89662394"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect sync: Entenda e personalize a sincronização
Os serviços de sincronização Azure Ative Directory Connect (Azure AD Connect sync) são um componente principal do Azure AD Connect. Trata de todas as operações relacionadas com a sincronização de dados de identidade entre o seu ambiente no local e a Azure AD. Azure AD Connect é o sucessor de DirSync, Azure AD Sync e Gestor de Identidade da Vanguarda com o Conector de Diretório Ativo Azure configurado.

Este tópico é a casa da **sincronização Azure AD Connect** (também chamada **de motor de sincronização)** e lista ligações a todos os outros tópicos relacionados com o mesmo. Para obter links para Azure AD Connect, consulte [integrar as suas identidades no local com o Azure Ative Directory](whatis-hybrid-identity.md).

O serviço de sincronização é composto por dois componentes, o componente de **sincronização Azure AD Connect** e o lado de serviço em Azure AD chamado **serviço de sincronização Azure AD Connect**.

## <a name="azure-ad-connect-sync-topics"></a>Azure AD Connect tópicos de sincronização
| Tópico | O que cobre e quando ler |
| --- | --- |
| **Fundamentos de sincronização Azure AD Connect** | |
| [Compreender a arquitetura](concept-azure-ad-connect-sync-architecture.md) |Para aqueles que são novos no motor de sincronização e querem aprender sobre a arquitetura e os termos usados. |
| [Conceitos técnicos](how-to-connect-sync-technical-concepts.md) |Uma versão curta do tema da arquitetura e explica brevemente os termos utilizados. |
| [Topologias do Azure AD Connect](plan-connect-topologies.md) |Descreve as diferentes topologias e cenários que o motor sincroniza. |
| **Configuração personalizada** | |
| [Executando o assistente de instalação novamente](how-to-connect-installation-wizard.md) |Explica quais as opções disponíveis quando voltar a executar o assistente de instalação Azure AD Connect. |
| [Compreender o Aprovisionamento Declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md) |Descreve o modelo de configuração chamado provisionamento declarativo. |
| [Compreender as Expressões de Aprovisionamento Declarativas](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Descreve a sintaxe para a linguagem de expressão usada no provisionamento declarativo. |
| [Entender a configuração predefinida](concept-azure-ad-connect-sync-default-configuration.md) |Descreve as regras fora da caixa e a configuração padrão. Também descreve como as regras funcionam em conjunto para que os cenários fora da caixa funcionem. |
| [Compreender Utilizadores e Contactos](concept-azure-ad-connect-sync-user-and-contacts.md) |Continua sobre o tema anterior e descreve como a configuração para utilizadores e contactos funciona em conjunto, em particular num ambiente multi-floresta. |
| [Como fazer uma alteração na configuração padrão](how-to-connect-sync-change-the-configuration.md) |Acompanha-o como fazer uma alteração de configuração comum para atribuir fluxos. |
| [Melhores práticas para alterar a configuração predefinida](how-to-connect-sync-best-practices-changing-default-configuration.md) |Limitações de suporte e para fazer alterações na configuração fora da caixa. |
| [Filtragem de configuração](how-to-connect-sync-configure-filtering.md) |Descreve as diferentes opções de como limitar quais os objetos que estão a ser sincronizados com AZure AD e passo a passo como configurar estas opções. |
| **Características e cenários** | |
| [Impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Descreve a *função prevent acidental de eliminações* e como configurá-la. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |Descreve o programador incorporado, que está a importar, sincronizar e exportar dados. |
| [Implementar sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md) |Descreve como funciona a sincronização da palavra-passe, como implementar e como operar e resolver problemas. |
| [Repetição de escrita do dispositivo](how-to-connect-device-writeback.md) |Descreve como funciona a gravação do dispositivo no Azure AD Connect. |
| [Extensões de diretórios](how-to-connect-sync-feature-directory-extensions.md) |Descreve como estender o esquema AZure AD com os seus próprios atributos personalizados. |
| [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |Descreve como colocar os recursos microsoft 365 do utilizador na mesma região que o utilizador. |
| **Serviço de Sincronização** | |
| [Funcionalidades do serviço de sincronização do Azure AD Connect](how-to-connect-syncservice-features.md) |Descreve o lado do serviço de sincronização e como alterar as definições de sincronização em Azure AD. |
| [Duplicação da resiliência do atributo](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Descreve como ativar e utilizar **o utilizadorPrincipalName** e **proxyAddresses** duplicam a resiliência dos valores de atributos. |
| **Operações e UI** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Descreve o UI do Gestor de Serviços de Sincronização, incluindo [Operações,](how-to-connect-sync-service-manager-ui-operations.md) [Conectores,](how-to-connect-sync-service-manager-ui-connectors.md) [Metaverse Designer](how-to-connect-sync-service-manager-ui-mvdesigner.md)e separadores [de Pesquisa metaversa.](how-to-connect-sync-service-manager-ui-mvsearch.md) |
| [Tarefas e considerações operacionais](./how-to-connect-sync-staging-server.md) |Descreve preocupações operacionais, como a recuperação de desastres. |
| **Como...** | |
| [Redefinir a conta AD Azure](how-to-connect-azureadaccount.md) |Como redefinir as credenciais da conta de serviço utilizada para ligar a partir da sincronização AZure AD Connect ao Azure AD. |
| **Mais informações e referências** | |
| [Portas](reference-connect-ports.md) |Lista quais as portas que precisa de abrir entre o motor de sincronização e os seus diretórios no local e a Azure AD. |
| [Atributos sincronizados com o Azure Active Directory](reference-connect-sync-attributes-synchronized.md) |Lista todos os atributos que estão a ser sincronizados entre AD no local e Azure AD. |
| [Funções Referência](reference-connect-sync-functions-reference.md) |Lista todas as funções disponíveis no provisionamento declarativo. |

## <a name="additional-resources"></a>Recursos Adicionais
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)