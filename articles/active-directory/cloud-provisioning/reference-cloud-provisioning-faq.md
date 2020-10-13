---
title: FAQ do aprovisionamento na cloud do Azure AD Connect
description: Este documento descreve perguntas frequentes para o provisionamento em nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28fb9da04c9c9d3e98b5226e1aee4cf5dde7183b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628677"
---
# <a name="azure-active-directory-connect-cloud-provisioning-faq"></a>Azure Ative Directory Connect cloud provisioning FAQ

Leia sobre perguntas frequentes para O Diretório Ativo Azure (Azure AD) Conecte o fornecimento de nuvem.

## <a name="general-installation"></a>Instalação geral

**P: Com que frequência o fornecimento de nuvens é executado?**

O fornecimento de nuvens está programado para ser executado a cada 2 minutos. A cada 2 minutos, quaisquer alterações de haxixe de utilizador, grupo e palavra-passe serão adsitadas ao Azure AD.

**P: Ver falhas de sincronização de haxixe de palavra-passe na primeira execução. Porquê?**

Isto era esperado. As falhas devem-se ao objeto do utilizador não presente no Azure AD. Uma vez que o utilizador é adcedido ao Azure AD, as hashes de palavra-passe devem ser a provisionadas na execução subsequente. Aguarde um par de corridas e confirme que a sincronização de haxixe de palavra-passe já não tem os erros.

**P: O que acontece se a instância do Ative Directory tiver atributos que não são suportados por provisões em nuvem (por exemplo, extensões de diretório)?**

O fornecimento de nuvens será executado e providenciará os atributos suportados. Os atributos não suportados não serão ad provisionados ao Azure AD. Reveja as extensões do diretório no Ative Directory e certifique-se de que não precisa desses atributos para fluir para a Azure AD. Se forem necessários um ou mais atributos, considere utilizar a sincronização Azure AD Connect ou mover as informações necessárias para um dos atributos suportados (por exemplo, atributos de extensão 1-15).

**P: Qual é a diferença entre a sincronização do Azure AD Connect e o provisionamento em nuvem?**

Com a sincronização Azure AD Connect, o fornecimento é executado no servidor de sincronização no local. A configuração é armazenada no servidor de sincronização no local. Com o fornecimento de nuvem AZure AD Connect, a configuração de provisionamento é armazenada na nuvem e funciona na nuvem como parte do serviço de fornecimento AD Azure. 

**P: Posso utilizar o fornecimento de nuvens para sincronizar a partir de múltiplas florestas de Diretório Ativo?**

Sim. O fornecimento de nuvens pode ser usado para sincronizar a partir de múltiplas florestas de Ative Directory. No ambiente multi-florestal, todas as referências (exemplo, gestor) devem estar dentro do domínio.  

**P: Como é que o agente é atualizado?**

Os agentes são atualizados automaticamente pela Microsoft. Para a equipa de TI, isto reduz o fardo de ter de testar e validar novas versões de agente. 

**P: Posso desativar o upgrade automático?**

Não existe uma forma suportada de desativar o upgrade automático.

**P: Posso alterar a âncora de origem para o provisionamento de nuvens?**

Por predefinição, o provisionamento em nuvem utiliza ms-ds-consistência-GUID com um retorno para ObjectGUID como âncora de origem. Não há uma forma suportada de mudar a âncora de origem.

**P: Vejo novos princípios de serviço com o(s) nome(s) de domínio AD ao utilizar o fornecimento de nuvens. É esperado?**

Sim, o fornecimento em nuvem cria um principal de serviço para a configuração de provisionamento com o nome de domínio como nome principal do serviço. Não enteda para a configuração principal do serviço.

**P: O que acontece quando um utilizador sincronizado é obrigado a alterar a palavra-passe no próximo logon?**

Se a sincronização de haxixe de palavra-passe estiver ativada no fornecimento de nuvem e o utilizador sincronizado for obrigado a alterar a palavra-passe no próximo logo no início da AD, o provisionamento em nuvem não prevê o hash de palavra-passe "a ser alterado" para Azure AD. Uma vez que o utilizador mude a palavra-passe, o hash da palavra-passe do utilizador é alojado de AD para Azure AD.

**P: O suporte de provisão em nuvem suporta a redução do MS-dS-consistênciaGUID para qualquer objeto?**

Não, o provisionamento em nuvem não suporta a redução do MS-dS-consistênciaGUID para qualquer objeto (incluindo objetos do utilizador). 

**P: Estou a a provisionar utilizadores usando o fornecimento de nuvens. Apaguei a configuração. Por que ainda vejo os velhos objetos sincronizados em Azure AD?** 

Quando elimina a configuração, o provisionamento em nuvem não remove automaticamente os objetos sincronizados em AZure AD. Para garantir que não tem os objetos antigos, altere o âmbito da configuração para um grupo vazio ou Unidades Organizacionais. Uma vez que o provisionamento é executado e limpa os objetos, desative e elimine a configuração. 

**P: O que significa que o híbrido Exchange não é suportado?**

A funcionalidade De Implementação Híbrida exchange permite a coexistência de caixas de correio Exchange tanto no local como na Microsoft 365. O Azure AD Connect está a sincronizar um conjunto específico de atributos do Azure AD para o diretório no local.  O agente de provisionamento em nuvem não sincroniza estes atributos de volta ao seu diretório no local e, portanto, não é suportado como um substituto para Azure AD Connect.

**P: Posso instalar o agente de provisão de nuvem no Núcleo do Servidor do Windows?**

Não, a instalação do agente no núcleo do servidor não é suportada.

**P: Posso usar um servidor de encenação com o agente de provisão de nuvem?**

Não, os servidores de paragem não são suportados.

**P: Posso sincronizar as contas de utilizadores do Hóspede?**

Não, a sincronização das contas dos utilizadores dos hóspedes não é suportada.

**P: Se eu mover um utilizador de um OU que é telescópio para provisão de nuvem para um OU que é traçado para Azure AD Connect, o que acontece?**

O utilizador será eliminado e recriada.  Mover um utilizador de um OU que seja previsto para o provisionamento em nuvem será visto como uma operação de eliminação.  Se o utilizador for transferido para um OU que é gerido pelo Azure AD Connect, será re-a provisionado para a Azure AD e um novo utilizador criado.

**P: Se eu mudar o nome ou mover o OU que está no âmbito do filtro de provisionamento em nuvem, o que acontece ao utilizador que foi criado em Azure AD?**

Nada.  Os utilizadores não serão eliminados se a U for renomeada ou movida.

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
