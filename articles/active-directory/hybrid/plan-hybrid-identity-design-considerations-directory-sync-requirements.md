---
title: Design de identidade híbrida - requisitos de sincronização de diretório Azure [ Microsoft Docs
description: Identifique quais os requisitos necessários para sincronizar todos os utilizadores entre as instalações on=e cloud para a empresa.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21558c4eccf0cd1f4e9e1d630f0e89dbb6f01c51
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60381166"
---
# <a name="determine-directory-synchronization-requirements"></a>Determinar os requisitos de sincronização do diretório
A sincronização tem tudo a ver com fornecer aos utilizadores uma identidade na nuvem com base na sua identidade no local. Quer utilizem ou não conta sincronizada para autenticação ou autenticação federada, os utilizadores ainda terão de ter uma identidade na nuvem.  Esta identidade terá de ser mantida e atualizada periodicamente.  As atualizações podem assumir muitos formulários, desde alterações de título saem a alterações de palavra-passe.  

Comece por avaliar as organizações no local solução de identidade e requisitos do utilizador. Esta avaliação é importante para definir os requisitos técnicos para a forma como as identidades dos utilizadores serão criadas e mantidas na nuvem.  Para a maioria das organizações, o Ative Directory está no local e será o diretório no local que os utilizadores irão sincronizar, no entanto, em alguns casos, não será esse o caso.  

Certifique-se de responder às seguintes perguntas:

* Tem uma floresta de d.C., múltipla ou nenhuma?
  
  * Quantos diretórios da AD Azure vais sincronizar?
    
    1. Estás a usar a filtragem?
    2. Tem vários servidores Azure AD Connect planeados?
* Tem atualmente uma ferramenta de sincronização no local?
  
  * Se sim, os seus utilizadores têm um diretório/integração virtual de identidades?
* Tem algum outro diretório no local que queira sincronizar (por exemplo, LDAP Diretório, base de dados de RH, etc)?
  * Vais fazer algum GALSync?
  * Qual é o estado atual das UPNs na sua organização? 
  * Tem um diretório diferente contra o que os utilizadores autenticam?
  * A sua empresa usa o Microsoft Exchange?
    * Planeiam ter uma troca híbrida?

Agora que tem uma ideia sobre os seus requisitos de sincronização, precisa determinar qual a ferramenta correta para satisfazer estes requisitos.  A Microsoft fornece várias ferramentas para realizar a integração e sincronização do diretório.  Consulte a tabela de [comparação](plan-hybrid-identity-design-considerations-tools-comparison.md) de ferramentas de integração do diretório identidade híbrida para obter mais informações. 

Agora que tem os seus requisitos de sincronização e a ferramenta que o irá realizar para a sua empresa, precisa de avaliar as aplicações que utilizam estes serviços de diretório. Esta avaliação é importante para definir os requisitos técnicos para integrar estas aplicações na nuvem. Certifique-se de responder às seguintes perguntas:

* Estas aplicações serão transferidas para a nuvem e usarão o diretório?
* Existem atributos especiais que precisam de ser sincronizados na nuvem para que estas aplicações possam usá-los com sucesso?
* Estas aplicações terão de ser reescritas para aproveitar a auth da nuvem?
* Estas aplicações continuarão a viver no local enquanto os utilizadores acedem a elas utilizando a identidade da nuvem?

Também precisa determinar os requisitos de segurança e restrições sincronização do diretório. Esta avaliação é importante para obter uma lista dos requisitos que serão necessários para criar e manter as identidades do utilizador na nuvem. Certifique-se de responder às seguintes perguntas:

* Onde será localizado o servidor de sincronização?
* Será que vai ser o domínio aderido?
* O servidor será localizado numa rede restrita atrás de uma firewall, como um DMZ?
  * Poderá abrir as portas de firewall necessárias para suportar a sincronização?
* Tem um plano de recuperação de desastres para o servidor de sincronização?
* Tem uma conta com as permissões corretas para todas as florestas com as que quer sincronizar?
  * Se a sua empresa não souber a resposta para esta pergunta, reveja a secção "Permissões para sincronização de passwords" no artigo Instale o Serviço de [Sincronização de Sincronização de Diretório Ativo Azure](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) e determine se já tem uma conta com estas permissões ou se precisa de criar uma.
* Se tiver sincronização de floresta silenciosa é o servidor de sincronização capaz de chegar a cada floresta?

> [!NOTE]
> Certifique-se de que anota cada resposta e que compreende os fundamentos das mesmas. [Determine os requisitos](plan-hybrid-identity-design-considerations-incident-response-requirements.md) de resposta a incidentes que irão passar pelas opções disponíveis. Ao responder a essas perguntas, irá selecionar qual a opção que melhor se adequa às suas necessidades de negócio.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar requisitos de autenticação de vários fatores](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral das considerações de conceção](plan-hybrid-identity-design-considerations-overview.md)

