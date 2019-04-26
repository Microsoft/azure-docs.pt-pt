---
title: Design de identidade híbrida - requisitos da sincronização de diretórios do Azure | Documentos da Microsoft
description: Identificar os requisitos são necessários para a sincronização de todos os utilizadores entre = no local e na cloud para empresas.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381166"
---
# <a name="determine-directory-synchronization-requirements"></a>Determinar requisitos de sincronização de diretórios
A sincronização é fornecer aos utilizadores uma identidade na cloud com base na respetiva identidade no local. Independentemente de eles usarão sincronizada conta para autenticação ou autenticação federada, os utilizadores ainda tem de ter uma identidade na cloud.  Esta identidade tem de ser gerida e atualizada periodicamente.  As atualizações podem assumir várias formas, de alterações de título para alterações de palavra-passe.  

Comece a avaliar as organizações requisitos de utilizador e de solução de identidade no local. Essa avaliação é importante definir os requisitos técnicos de como as identidades dos utilizadores serão criadas e mantidas na cloud.  Para a maioria das organizações, Active Directory está no local e será ser o diretório no local que os utilizadores serão por sincronizadas a partir do, no entanto em alguns casos isso não será o caso.  

Certifique-se responder às seguintes perguntas:

* Tem uma floresta do AD, múltiplos ou nenhum?
  
  * Quantos diretórios do Azure AD irá ser a sincronizar com o?
    
    1. Está a utilizar filtragem?
    2. Tem vários servidores do Azure AD Connect planeados?
* Têm atualmente uma sincronização ferramenta no local?
  
  * Se Sim, é que os utilizadores se os utilizadores têm uma virtual/integração de diretórios de identidades?
* Tem qualquer outro diretório no local que pretende sincronizar (por exemplo, diretório LDAP, base de dados de RH, etc)?
  * Vai fazer qualquer GALSync?
  * O que é o estado atual do UPNs na sua organização? 
  * Tem um diretório diferente que os usuários são autenticados contra?
  * A sua empresa utiliza o Microsoft Exchange?
    * Elas pretendam de ter uma implementação híbrida do exchange?

Agora que tem uma ideia sobre os requisitos de sincronização, terá de determinar qual ferramenta é a correta para atender a esses requisitos.  A Microsoft fornece várias ferramentas para realizar a sincronização e integração de diretórios.  Consulte a [tabela de comparação de ferramentas de integração de diretórios de identidade híbrida](plan-hybrid-identity-design-considerations-tools-comparison.md) para obter mais informações. 

Agora que tem seus requisitos de sincronização e a ferramenta que irá fazer isso para a sua empresa, terá de avaliar as aplicações que utilizam estes serviços de diretório. Essa avaliação é importante definir os requisitos técnicos para integrar estas aplicações para a cloud. Certifique-se responder às seguintes perguntas:

* Esses aplicativos ser movidos para a cloud e o diretório?
* Existem atributos especiais que precisam ser sincronizados para a cloud, para que esses aplicativos podem usá-los com êxito?
* Estas aplicações tem de ser reescritos para tirar partido da autenticação na nuvem?
* Estas aplicações continuarão a em direto no local, enquanto os utilizadores aceder aos mesmos usando a identidade de cloud?

Também terá de determinar a sincronização de diretórios de requisitos e restrições de segurança. Essa avaliação é importante para obter uma lista dos requisitos que serão necessários para criar e manter as identidades dos utilizadores na cloud. Certifique-se responder às seguintes perguntas:

* Onde o servidor de sincronização será localizado?
* Irá ser associado a um domínio?
* O servidor irá ser localizado numa rede restrita por trás de um firewall, como uma rede de Perímetro?
  * Será capaz de abrir as portas de firewall necessárias para suportar a sincronização?
* Tem um plano de recuperação após desastre para o servidor de sincronização?
* Tem uma conta com as permissões corretas para todas as florestas que pretende sincronizar com?
  * Se a sua empresa não sabe a resposta para essa pergunta, consulte a secção "Permissões para a sincronização de palavra-passe" no artigo [instalar o serviço do Azure Active Directory Sync](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) e determinar se já tiver uma conta com essas permissões ou se precisar de criar uma.
* Se tiver o sincronização estiverem floresta é o servidor de sincronização capaz de fazer em cada floresta?

> [!NOTE]
> Lembre-se de que anota cada resposta e compreender as razões implícitas para a resposta. [Determinar os requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md) abordará as opções disponíveis. Ao responder a essas questões, vai selecionar qual opção melhor se adequa aos seu negócio precisa.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
[Determinar os requisitos de autenticação multifator](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Consulte também
[Descrição geral das considerações de design](plan-hybrid-identity-design-considerations-overview.md)

