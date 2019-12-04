---
title: Perguntas frequentes sobre provisionamento em nuvem Azure AD Connect
description: Este documento descreve as perguntas frequentes sobre o provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93c88b167884c455ffb995f35356b121bce8a207
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793557"
---
# <a name="azure-active-directory-connect-faq"></a>FAQ do Azure Active Directory Connect

Leia sobre as perguntas frequentes sobre o provisionamento de nuvem do Azure Active Directory (Azure AD) Connect.

## <a name="general-installation"></a>Instalação geral

**P: com que frequência o provisionamento de nuvem é executado?**

O provisionamento de nuvem está agendado para ser executado a cada 2 minutos. A cada 2 minutos, qualquer usuário, grupo e alterações de hash de senha serão provisionados para o Azure AD.

**P: vendo falhas de sincronização de hash de senha na primeira execução. Por?**

Isto era esperado. As falhas são devido ao objeto de usuário não presente no Azure AD. Depois que o usuário for provisionado no Azure AD, os hashes de senha deverão ser provisionados na execução subsequente. Aguarde algumas execuções e confirme se a sincronização de hash de senha não tem mais os erros.

**P: Qual é a diferença entre Azure AD Connect sincronização e provisionamento de nuvem?**

Com Azure AD Connect sincronização, o provisionamento é executado no servidor de sincronização local. A configuração é armazenada no servidor de sincronização local. Com o provisionamento Azure AD Connect Cloud, a configuração de provisionamento é armazenada na nuvem e é executada na nuvem como parte do serviço de provisionamento do Azure AD. 

**P: posso usar o provisionamento de nuvem para sincronizar de várias florestas Active Directory?**

Sim. O provisionamento de nuvem pode ser usado para sincronização de várias florestas Active Directory. No ambiente de várias florestas, todas as referências (exemplo, gerente) precisam estar dentro do domínio.  

**P: como o agente é atualizado?**

Os agentes são atualizados automaticamente pela Microsoft. Isso reduz a carga de ti para testar e validar novas versões de agente. 

**P: posso desabilitar a atualização automática?**

Não há nenhuma maneira com suporte para desabilitar a atualização automática.

**P: posso alterar a âncora de origem do provisionamento de nuvem?**

Por padrão, o provisionamento de nuvem usa o MS-DS-Consistency-GUID com um fallback para objectGUID como âncora de origem. Não há nenhuma maneira com suporte para alterar a âncora de origem.

**P: Eu vejo novas entidades de serviço com os nomes de domínio do AD ao usar o provisionamento de nuvem. É esperado?**

Sim, o provisionamento de nuvem cria uma entidade de serviço para a configuração de provisionamento com o nome de domínio como o nome da entidade de serviço. Não faça nenhuma alteração na configuração da entidade de serviço.

**P: o que acontece quando um usuário sincronizado é necessário para alterar a senha no próximo logon?**

Se a sincronização de hash de senha estiver habilitada no provisionamento de nuvem e o usuário sincronizado for necessário para alterar a senha no próximo logon no AD local, o provisionamento de nuvem não provisionará o hash de senha a ser alterado para o Azure AD. Depois que o usuário altera a senha, o hash de senha do usuário é provisionado do AD para o Azure AD.

**P: o provisionamento de nuvem oferece suporte a Write-back de MS-DS-consistencyGUID para qualquer objeto?**

Não, o provisionamento de nuvem não oferece suporte a Write-back de MS-DS-consistencyGUID para qualquer objeto (incluindo objetos de usuário). 

**P: Estou Provisionando usuários usando o provisionamento de nuvem. Excluí a configuração. Por que eu ainda vejo os antigos objetos sincronizados no Azure AD?** 

Quando você exclui a configuração, o provisionamento de nuvem não limpará os objetos sincronizados no Azure AD. Para garantir que você não tenha os objetos antigos, altere o escopo da configuração para um grupo vazio ou unidades organizacionais. Depois que o provisionamento é executado e limpa os objetos, desabilite e exclua a configuração. 

## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)
