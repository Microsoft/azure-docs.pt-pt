---
title: O que é o fornecimento de nuvem Azure AD Connect. | Microsoft Docs
description: Descreve o fornecimento de nuvem Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a86d34fca9a88b0df601533a0f3de1cc97ad1a2f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80050588"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>O que é o aprovisionamento na cloud do Azure AD Connect?
O fornecimento de nuvem Azure AD Connect é um novo agente da Microsoft projetado para cumprir e cumprir os seus objetivos de identidade híbrida para sincronização de utilizadores, grupos e contactos para a AD Azure.  Pode ser usado ao lado da sincronização Azure AD Connect, e proporciona os seguintes benefícios:
    
- Apoio à sincronização de um inquilino da Azure AD de um ambiente florestal de diretório ativo desligado de várias florestas: Os cenários comuns incluem a fusão & aquisição, onde as florestas ad's da empresa adquirida estão isoladas das florestas e empresas da empresa-mãe que historicamente tiveram múltiplas florestas ad.
- Instalação simplificada com agentes de fornecimento de peso leve: Os agentes funcionam como uma ponte de AD para Azure AD, com toda a configuração de sincronização gerida na nuvem. 
- Vários agentes de provisionamento podem ser usados para simplificar implementações de alta disponibilidade, particularmente críticas para organizações que dependem da sincronização de hash de senha de AD para Azure AD.


![O que é o Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Como é que o fornecimento de nuvem Azure AD Connect é diferente do sincronizado Azure AD Connect?
Com o fornecimento de cloud Azure AD Connect, o fornecimento de AD para Azure AD é orquestrado nos Serviços Online da Microsoft. Uma organização só precisa de implantar, no seu local e no ambiente organizado pela IaaS, um agente leve que atua como uma ponte entre a Azure AD e a AD. A configuração de provisionamento é armazenada em Azure AD e gerida como parte do serviço.

O quadro seguinte fornece uma comparação entre o fornecimento de nuvem Azure AD Connect e Azure AD Connect:

| Funcionalidade | Sincronização de ligação de diretório ativo Azure| Fornecimento de nuvem de ligação de diretório ativo Azure |
|:--- |:---:|:---:|
|Ligar a uma única floresta do AD no local|● |● |
| Ligar a várias florestas do AD no local |● |● |
| Ligar a várias florestas ad-locais desligadas | |● |
| Modelo de instalação de agente leve | |● |
| Múltiplos agentes ativos para alta disponibilidade | |● |
| Ligar aos diretórios LDAP|●| | 
| Suporte para objetos de utilizador |● |● |
| Suporte para objetos de grupo |● |● |
| Suporte para objetos de contacto |● |● |
| Suporte para objetos de dispositivo |● | |
| Permitir personalização básica para fluxos de atributos |● |● |
| Sychronize Exchange atributos on-line |● |● |
| Synchronize atributos de extensão 1-15 |● |● |
| Synchronize atributos AD definidos pelo cliente (extensões de diretório) |● | |
| Suporte para Password Hash Sync |●|●|
| Suporte para autenticação pass-through |●||
| Apoio à federação |●|●|
| Início de Sessão Único totalmente integrado|● |●|
| Suporta a instalação num Controlador de Domínio |● |● |
| Suporte para Windows Server 2012 e Windows Server 2012 R2 |● |● |
| Filtro em Domínios/OUs/grupos |● |● |
| Filtrar os valores de atributo dos objetos |● | |
| Permitir que um conjunto mínimo de atributos seja sincronizado (MinSync) |● |● |
| Permitir a remoção de atributos no sentido do AD para o Azure AD |● |● |
| Permitir a personalização avançada de fluxos de atributos |● | |
| Suporte para reprodução (palavras-passe, dispositivos, grupos) |● | |
| Suporte dos Serviços de Domínio da AD Azure|● | |
| [Troca de recoudação híbrida](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Suporte para mais de 50.000 objetos por domínio ad |● | |

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [Instalar o fornecimento de nuvem](how-to-install.md)
