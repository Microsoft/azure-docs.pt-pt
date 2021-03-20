---
title: O que é Azure AD Connect cloud sync. | Microsoft Docs
description: Descreve a sincronização de nuvem Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3b880d70baf7c160f8235bd01a5000f83825c36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614865"
---
# <a name="what-is-azure-ad-connect-cloud-sync"></a>O que é a sincronização de nuvem AZure AD Connect?
A azure AD Connect cloud sync é uma nova oferta da Microsoft projetada para cumprir e cumprir os seus objetivos de identidade híbrida para a sincronização de utilizadores, grupos e contactos com a Azure AD.  Consegue-o utilizando o agente de provisão de nuvem Azure AD em vez da aplicação Azure AD Connect.  No entanto, pode ser usado ao lado da sincronização Azure AD Connect e fornece os seguintes benefícios:
    
- Apoio à sincronização a um inquilino da AD Azure a partir de um ambiente florestal ativo desligado multi-floresta: Os cenários comuns incluem a fusão & aquisição, onde as florestas de AD da empresa adquiridas estão isoladas das florestas de AD da empresa-mãe e empresas que historicamente tiveram múltiplas florestas de AD.
- Instalação simplificada com agentes de provisionamento leve: Os agentes funcionam como uma ponte de AD a AZure AD, com toda a configuração de sincronização gerida na nuvem. 
- Vários agentes de provisionamento podem ser usados para simplificar as implementações de alta disponibilidade, particularmente críticas para organizações que dependem da sincronização de hash de palavra-passe de AD a Azure AD.
- Apoio a grandes grupos com até 50mil membros. Recomenda-se a utilização apenas do filtro de deteção de OU quando sincronizar grandes grupos.


![O que é o Azure AD Connect](media/what-is-cloud-sync/architecture-1.png)

## <a name="how-is-azure-ad-connect-cloud-sync-different-from-azure-ad-connect-sync"></a>Como é que o Azure AD Connect sync de nuvem é diferente da sincronização do Azure AD Connect?
Com a sincronização em nuvem Azure AD Connect, o fornecimento de AD a Azure AD é orquestrado nos Serviços Online da Microsoft. Uma organização só precisa de implantar, no seu ambiente no local e no iaaS, um agente leve que atua como uma ponte entre a Azure AD e a AD. A configuração de provisionamento é armazenada em Azure AD e gerida como parte do serviço.

## <a name="azure-ad-connect-cloud-sync-video"></a>Vídeo sincronizado em nuvem Azure AD Connect
O vídeo curto seguinte fornece uma excelente visão geral da sincronização da nuvem AZure AD Connect:

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-sync"></a>Comparação entre Azure AD Connect e cloud sync

A tabela a seguir fornece uma comparação entre a sincronização de nuvem AZure AD Connect e Azure AD Connect:

| Funcionalidade | Sincronização Azure Ative Directory Connect| Azure Ative Directory Connect cloud sync |
|:--- |:---:|:---:|
|Ligar a uma única floresta do AD no local|● |● |
| Ligar a várias florestas do AD no local |● |● |
| Ligar a múltiplas florestas de AD desligadas no local | |● |
| Modelo de instalação de agente leve | |● |
| Múltiplos agentes ativos para alta disponibilidade | |● |
| Ligue-se aos diretórios LDAP|●| | 
| Suporte para objetos de utilizador |● |● |
| Suporte para objetos de grupo |● |● |
| Suporte para objetos de contacto |● |● |
| Suporte para objetos do dispositivo |● | |
| Permitir a personalização básica para fluxos de atributos |● |● |
| Synchronize Trocar atributos online |● |● |
| Synconize atributos de extensão 1-15 |● |● |
| Sincronizar atributos de AD definidos pelo cliente (extensões de diretório) |● | |
| Suporte para Password Hash Sync |●|●|
| Apoio à Autenticação Pass-Through |●||
| Apoio à federação |●|●|
| Início de Sessão Único totalmente integrado|● |●|
| Suporta a instalação num Controlador de Domínio |● |● |
| Suporte para Windows Server 2012 e Windows Server 2012 R2 |● |● |
| Filtro em Domínios/OUs/grupos |● |● |
| Filtrar os valores de atributos dos objetos |● | |
| Permitir que um conjunto mínimo de atributos seja sincronizado (MinSync) |● |● |
| Permitir a remoção de atributos no sentido do AD para o Azure AD |● |● |
| Permitir a personalização avançada de fluxos de atributos |● | |
| Suporte para writeback (palavras-passe, dispositivos, grupos) |● | |
| Suporte a serviços de domínio Azure AD|● | |
| [Troca de writeback híbrido](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Suporte para até 150.000 objetos por domínio AD |● |● |
| Grande apoio do grupo - grupos com até 50.000 membros |● |● |
| Referências de domínio transversal|● | |
| Aprovisionamento a pedido| |● |

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [Instalar sincronização de nuvem](how-to-install.md)
