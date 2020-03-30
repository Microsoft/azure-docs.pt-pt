---
title: FAQ do aprovisionamento na cloud do Azure AD Connect
description: Este documento descreve frequentemente perguntas para o fornecimento de nuvens.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc1baa86bb81c8975587e84427a72ccc044805e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77916579"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Ative Directory Connect FAQ

Leia sobre perguntas frequentes para azure Ative Directory (Azure AD) Conecte o fornecimento de nuvem.

## <a name="general-installation"></a>Instalação geral

**P: Com que frequência funciona o fornecimento de nuvens?**

O fornecimento de nuvens está programado para funcionar a cada 2 minutos. A cada 2 minutos, qualquer utilizador, grupo e alterações de hash de senha serão provisionados para a AD Azure.

**P: Ver falhas de sincronização de hash de palavra-passe na primeira execução. Porquê?**

Isto era esperado. As falhas devem-se ao objeto de utilizador não estar presente no Azure AD. Uma vez que o utilizador é provisionado para AD Azure, as hashes de senha devem fornecer na execução subsequente. Aguarde por algumas corridas e confirme que a sincronização de hash de senha já não tem os erros.

**P: O que acontece se a instância do Diretório Ativo tiver atributos que não são suportados por provisões de nuvem (por exemplo, extensões de diretório)?**

O fornecimento de nuvem será executado e fornecerá os atributos suportados. Os atributos não suportados não serão provisionados à Azure AD. Reveja as extensões de diretório em Diretório Ativo e certifique-se de que não precisa desses atributos para fluir para a AD Azure. Se forem necessários um ou mais atributos, considere utilizar o sincronia Azure AD Connect ou mover as informações necessárias para um dos atributos suportados (por exemplo, atributos de extensão 1-15).

**P: Qual é a diferença entre o sincronizacro azure AD Connect e o fornecimento de nuvens?**

Com a sincronização Azure AD Connect, o fornecimento corre no servidor de sincronização no local. A configuração é armazenada no servidor de sincronização no local. Com o fornecimento de nuvem Azure AD Connect, a configuração de provisionamento é armazenada na nuvem e corre na nuvem como parte do serviço de provisionamento de AD Azure. 

**P: Posso usar o fornecimento de nuvem para sincronizar de várias florestas de Diretório Ativo?**

Sim. O fornecimento de nuvens pode ser usado para sincronizar de várias florestas de Diretório Ativo. No ambiente multi-florestal, todas as referências (exemplo, gestor) devem estar dentro do domínio.  

**P: Como é que o agente é atualizado?**

Os agentes são atualizados automaticamente pela Microsoft. Para a equipa de TI, isto reduz o fardo de ter de testar e validar novas versões de agente. 

**P: Posso desativar a atualização automática?**

Não existe uma forma suportada de desativar a atualização automática.

**P: Posso mudar a âncora de origem para o fornecimento de nuvens?**

Por padrão, o fornecimento de nuvem utiliza ms-ds-consistência-GUID com um recuo para objectGUID como âncora de origem. Não há forma suportada de mudar a âncora de origem.

**P: Vejo novos diretores de serviço com o nome ou s de domínio AD quando se utiliza o fornecimento de nuvens. É esperado?**

Sim, o fornecimento de nuvem cria um principal de serviço para a configuração de provisionamento com o nome de domínio como nome principal do serviço. Não efaça alterações na configuração do diretor do serviço.

**P: O que acontece quando um utilizador sincronizado é obrigado a alterar a palavra-passe no próximo logon?**

Se o sync de hash de palavra-passe estiver ativado no fornecimento de nuvem e o utilizador sincronizado for obrigado a alterar a palavra-passe no próximo login no ad-site, o fornecimento em nuvem não prevê a alteração do hash de palavra-passe para AD Azure. Uma vez que o utilizador altera a palavra-passe, o hash de palavra-passe do utilizador é aprovisionado de AD para Azure AD.

**P: O fornecimento de nuvens suporta a reescrita da ms-ds-consistênciaGUID para qualquer objeto?**

Não, o fornecimento de nuvens não suporta a reutilização da ms-ds-consistênciaGUID para qualquer objeto (incluindo objetos do utilizador). 

**P: Estou a fornecer utilizadores usando o fornecimento de nuvem. Apaguei a configuração. Por que ainda vejo os velhos objetos sincronizados em Azure AD?** 

Ao eliminar a configuração, o fornecimento de nuvens não limpa os objetos sincronizados em Azure AD. Para garantir que não tem os objetos antigos, altere o âmbito da configuração para um grupo vazio ou Unidades Organizacionais. Uma vez que o fornecimento seja executado e limpe os objetos, desative e elimine a configuração. 

**P: O que significa que o Exchange hybrid não é suportado?**

A funcionalidade Implementação Híbrida do Exchange permite a coexistência de caixas de correio do Exchange no local e no Office 365. O Azure AD Connect está a sincronizar um conjunto específico de atributos do Azure AD para o diretório no local.  O agente de fornecimento de nuvem atualmente não sincroniza estes atributos de volta ao seu diretório no local e, portanto, não é suportado como substituto do Azure AD Connect.

**P: Posso instalar o agente de fornecimento de nuvem no Núcleo do Servidor do Windows?**

Não, a instalação do agente no núcleo do servidor não é suportada.

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
