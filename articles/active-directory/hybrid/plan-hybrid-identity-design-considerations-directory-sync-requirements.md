---
title: Design de identidade híbrida - requisitos de sincronização de diretórios Azure / Microsoft Docs
description: Identifique quais os requisitos necessários para sincronizar todos os utilizadores entre on=premissas e nuvem para a empresa.
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
ms.openlocfilehash: 500d226fcb60646becc49144f206dcb0dee49bd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89278399"
---
# <a name="determine-directory-synchronization-requirements"></a>Determinar os requisitos de sincronização do diretório
A sincronização tem tudo a ver com fornecer aos utilizadores uma identidade na nuvem com base na sua identidade no local. Quer utilizem ou não uma conta sincronizada para autenticação ou autenticação federada, os utilizadores ainda terão de ter uma identidade na nuvem.  Esta identidade terá de ser mantida e atualizada periodicamente.  As atualizações podem assumir muitas formas, desde alterações de títulos a alterações de palavras-passe.  

Comece por avaliar a solução de identidade das organizações no local e os requisitos do utilizador. Esta avaliação é importante para definir os requisitos técnicos para a forma como as identidades dos utilizadores serão criadas e mantidas na nuvem.  Para a maioria das organizações, o Ative Directory está no local e será o diretório no local que os utilizadores irão por sincronização, no entanto, em alguns casos, não será esse o caso.  

Certifique-se de responder às seguintes perguntas:

* Tem uma floresta de AD, múltipla, ou nenhuma?
  
  * A quantos diretórios da AD da Azure vai sincronizar?
    
    1. Está a usar filtragem?
    2. Tem vários servidores AD Connect Azure planeados?
* Tem atualmente uma ferramenta de sincronização no local?
  
  * Se sim, os seus utilizadores se os utilizadores tiverem um diretório virtual/integração de identidades?
* Tem outros diretórios no local que pretenda sincronizar (por exemplo, Diretório LDAP, base de dados de RH, etc)?
  * Vais fazer alguma GALSync?
  * Qual é o estado atual das UPNs na sua organização? 
  * Tem um diretório diferente contra o que os utilizadores autenticam?
  * A sua empresa utiliza o Microsoft Exchange?
    * Planeiam ter uma distribuição de troca híbrida?

Agora que tem uma ideia sobre os seus requisitos de sincronização, precisa determinar qual a ferramenta correta para satisfazer estes requisitos.  A Microsoft fornece várias ferramentas para realizar a integração e sincronização de diretórios.  Consulte a [tabela de ferramentas de integração de diretórios de identidade híbrida](plan-hybrid-identity-design-considerations-tools-comparison.md) para obter mais informações. 

Agora que tem os seus requisitos de sincronização e a ferramenta que irá realizar isso para a sua empresa, precisa de avaliar as aplicações que utilizam estes serviços de diretório. Esta avaliação é importante para definir os requisitos técnicos para integrar estas aplicações na nuvem. Certifique-se de responder às seguintes perguntas:

* Estas aplicações serão transferidas para a nuvem e utilizarão o diretório?
* Existem atributos especiais que precisam de ser sincronizados na nuvem para que estas aplicações possam usá-las com sucesso?
* Será que estas aplicações precisam de ser reescritas para tirar partido do auth cloud?
* Estas aplicações continuarão a viver no local enquanto os utilizadores acedem a elas utilizando a identidade da nuvem?

Também precisa determinar os requisitos de segurança e a sincronização do diretório. Esta avaliação é importante para obter uma lista dos requisitos que serão necessários para criar e manter as identidades dos utilizadores na nuvem. Certifique-se de responder às seguintes perguntas:

* Onde será localizado o servidor de sincronização?
* Será que o domínio se junta?
* O servidor será localizado numa rede restrita atrás de uma firewall, como um DMZ?
  * Poderá abrir as portas de firewall necessárias para suportar a sincronização?
* Tem um plano de recuperação de desastres para o servidor de sincronização?
* Tem uma conta com as permissões corretas para todas as florestas com as que pretende sincronizar?
  * Se a sua empresa não sabe a resposta para esta pergunta, reveja a secção "Permissões para sincronização de passwords" no artigo [Instale o Serviço de Sincronização de Diretório Ativo Azure](/previous-versions/azure/azure-services/dn757602(v=azure.100)#BKMK_CreateAnADAccountForTheSyncService) e determine se já tem uma conta com estas permissões ou se precisa de criar uma.
* Se tiver sincronização de floresta mutli é capaz de chegar a cada floresta?

> [!NOTE]
> Certifique-se de que anota cada resposta e que compreende os fundamentos das mesmas. [Determinar os requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md) irá sobre as opções disponíveis. Ao responder a essas perguntas, irá selecionar qual a opção que melhor se adequa às necessidades do seu negócio.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar requisitos de autenticação de vários fatores](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](plan-hybrid-identity-design-considerations-overview.md)