---
title: Azure AD Connect cloud sync FAQ
description: Este documento descreve perguntas frequentes para sincronização de nuvem.
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
ms.openlocfilehash: 39d1554fd1b6cac1a90a794cfd93def97e494bfe
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613528"
---
# <a name="azure-active-directory-connect-cloud-sync-faq"></a>Azure Ative Directory Connect cloud sync FAQ

Leia sobre perguntas frequentes para Azure Ative Directory (Azure AD) Connect cloud sync.

## <a name="general-installation"></a>Instalação geral

**P: Com que frequência funciona a sincronização da nuvem?**

O fornecimento de nuvens está programado para ser executado a cada 2 minutos. A cada 2 minutos, quaisquer alterações de haxixe de utilizador, grupo e palavra-passe serão adsitadas ao Azure AD.

**P: Ver falhas de sincronização de haxixe de palavra-passe na primeira execução. Porquê?**

Isto era esperado. As falhas devem-se ao objeto do utilizador não presente no Azure AD. Uma vez que o utilizador é adcedido ao Azure AD, as hashes de palavra-passe devem ser a provisionadas na execução subsequente. Aguarde um par de corridas e confirme que a sincronização de haxixe de palavra-passe já não tem os erros.

**P: O que acontece se a instância ative directory tiver atributos que não são suportados por sincronização de nuvem (por exemplo, extensões de diretório)?**

O fornecimento de nuvens será executado e providenciará os atributos suportados. Os atributos não suportados não serão ad provisionados ao Azure AD. Reveja as extensões do diretório no Ative Directory e certifique-se de que não precisa desses atributos para fluir para a Azure AD. Se forem necessários um ou mais atributos, considere utilizar a sincronização Azure AD Connect ou mover as informações necessárias para um dos atributos suportados (por exemplo, atributos de extensão 1-15).

**P: Qual é a diferença entre a sincronização do Azure AD Connect e a sincronização em nuvem?**

Com a sincronização Azure AD Connect, o fornecimento é executado no servidor de sincronização no local. A configuração é armazenada no servidor de sincronização no local. Com a sincronização em nuvem Azure AD Connect, a configuração de provisionamento é armazenada na nuvem e funciona na nuvem como parte do serviço de fornecimento AD Azure. 

**P: Posso usar sincronização de nuvem para sincronizar a partir de múltiplas florestas de Diretório Ativo?**

Yes. O fornecimento de nuvens pode ser usado para sincronizar a partir de múltiplas florestas de Ative Directory. No ambiente multi-florestal, todas as referências (exemplo, gestor) devem estar dentro do domínio.  

**P: Como é que o agente é atualizado?**

Os agentes são atualizados automaticamente pela Microsoft. Para a equipa de TI, isto reduz o fardo de ter de testar e validar novas versões de agente. 

**P: Posso desativar o upgrade automático?**

Não existe uma forma suportada de desativar o upgrade automático.

**P: Posso alterar a âncora de origem para sincronização de nuvens?**

Por predefinição, a sincronização da nuvem utiliza ms-ds-consistência-GUID com um retorno para ObjectGUID como âncora de origem. Não há uma forma suportada de mudar a âncora de origem.

**P: Vejo novos princípios de serviço com o(s) nome(s) de domínio AD ao utilizar sincronização de nuvem. É esperado?**

Sim, a cloud sync cria um principal de serviço para a configuração de provisionamento com o nome de domínio como nome principal do serviço. Não enteda para a configuração principal do serviço.

**P: O que acontece quando um utilizador sincronizado é obrigado a alterar a palavra-passe no próximo logon?**

Se a sincronização de haxixe de palavra-passe estiver ativada em sincronização de nuvem e o utilizador sincronizado for obrigado a alterar a palavra-passe no próximo logo no início da AD, a cloud sync não prevê o hash de palavra-passe "a ser alterado" para Azure AD. Uma vez que o utilizador mude a palavra-passe, o hash da palavra-passe do utilizador é alojado de AD para Azure AD.

**P: A sincronização da nuvem suporta a gravação do MS-ds-consistênciaGUID para qualquer objeto?**

Não, a sincronização em nuvem não suporta a gravação de MS-ds-consistênciaGUID para qualquer objeto (incluindo objetos do utilizador). 

**P: Estou a a provisionar utilizadores usando sincronização de nuvem. Apaguei a configuração. Por que ainda vejo os velhos objetos sincronizados em Azure AD?** 

Quando elimina a configuração, a sincronização em nuvem não remove automaticamente os objetos sincronizados em Azure AD. Para garantir que não tem os objetos antigos, altere o âmbito da configuração para um grupo vazio ou Unidades Organizacionais. Uma vez que o provisionamento é executado e limpa os objetos, desative e elimine a configuração. 

**P: O que significa que o híbrido Exchange não é suportado?**

A funcionalidade De Implementação Híbrida exchange permite a coexistência de caixas de correio Exchange tanto no local como na Microsoft 365. O Azure AD Connect está a sincronizar um conjunto específico de atributos do Azure AD para o diretório no local.  O agente de provisionamento em nuvem não sincroniza estes atributos de volta ao seu diretório no local e, portanto, não é suportado como um substituto para Azure AD Connect.

**P: Posso instalar o agente de provisão de nuvem no Núcleo do Servidor do Windows?**

Não, a instalação do agente no núcleo do servidor não é suportada.

**P: Posso usar um servidor de encenação com o agente de provisão de nuvem?**

Não, os servidores de paragem não são suportados.

**P: Posso sincronizar as contas de utilizadores do Hóspede?**

Não, a sincronização das contas dos utilizadores dos hóspedes não é suportada.

**P: Se eu mover um utilizador de um OU que é telescópio para sincronização de nuvem para um OU que é traçado para Azure AD Connect, o que acontece?**

O utilizador será eliminado e recriada.  Mover um utilizador de um OU que seja testado para sincronização de nuvem será visto como uma operação de eliminação.  Se o utilizador for transferido para um OU que é gerido pelo Azure AD Connect, será re-a provisionado para a Azure AD e um novo utilizador criado.

**P: Se eu mudar o nome ou mover o OU que está no âmbito do filtro de sincronização de nuvem, o que acontece ao utilizador que foi criado em Azure AD?**

Nada.  Os utilizadores não serão eliminados se a U for renomeada ou movida.

**P: A azure AD Connect sincronização de nuvem suporta grandes grupos?**

Yes. Hoje apoiamos até 50K membros do grupo sincronizados usando a filtragem de âmbito OU. Ao mesmo tempo, quando utilizar a filtragem do âmbito de grupo, recomendamos que mantenha o tamanho do grupo a menos de 1500 membros. A razão para isso é que, mesmo que possa sincronizar um grande grupo como parte do filtro de deteção de grupo, quando adicionar membros a esse grupo por lotes superiores a 1500, a sincronização delta falhará. 

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)
