---
title: O que é a azure AD Connect provisioning cloud. | Microsoft Docs
description: Descreve o fornecimento de nuvem Azure AD Connect.
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
ms.openlocfilehash: 0acef468aa53e456cd6fb416fe45558aee064699
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355822"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>O que é o aprovisionamento na cloud do Azure AD Connect?
O Azure AD Connect cloud provisioning é um novo agente da Microsoft projetado para cumprir e cumprir os seus objetivos de identidade híbrida para a sincronização de utilizadores, grupos e contactos com a Azure AD.  Pode ser usado ao lado da sincronização Azure AD Connect e proporciona os seguintes benefícios:
    
- Apoio à sincronização a um inquilino da AD Azure a partir de um ambiente florestal ativo desligado multi-floresta: Os cenários comuns incluem a fusão & aquisição, onde as florestas de AD da empresa adquiridas estão isoladas das florestas de AD da empresa-mãe e empresas que historicamente tiveram múltiplas florestas de AD.
- Instalação simplificada com agentes de provisionamento leve: Os agentes funcionam como uma ponte de AD a AZure AD, com toda a configuração de sincronização gerida na nuvem. 
- Vários agentes de provisionamento podem ser usados para simplificar as implementações de alta disponibilidade, particularmente críticas para organizações que dependem da sincronização de hash de palavra-passe de AD a Azure AD.


![O que é o Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Como é que o Azure AD Connect está a provisão em nuvem diferente do Azure AD Connect?
Com o fornecimento de nuvem AZure AD Connect, o fornecimento de AD a Azure AD é orquestrado em Serviços Online da Microsoft. Uma organização só precisa de implantar, no seu ambiente no local e no iaaS, um agente leve que atua como uma ponte entre a Azure AD e a AD. A configuração de provisionamento é armazenada em Azure AD e gerida como parte do serviço.

## <a name="azure-ad-connect-cloud-provisioning-video"></a>Vídeo de provisão de nuvem Azure AD Connect
O vídeo curto seguinte fornece uma excelente visão geral do provisionamento em nuvem AZure AD Connect:

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-provisioning"></a>Comparação entre Azure AD Connect e provisionamento em nuvem

A tabela a seguir fornece uma comparação entre o Azure AD Connect e o Azure AD Connect:

| Funcionalidade | Sincronização Azure Ative Directory Connect| Azure Ative Directory Connect fornecimento de nuvem |
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
| Suporte para mais de 50.000 objetos por domínio AD |● | |
| Referências de domínio transversal|● | |

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [Instalar provisão de nuvens](how-to-install.md)
