---
title: O que é a gestão do ciclo de vida de identidade com o Azure Ative Directory? | Microsoft Docs
description: Descreve a visão geral da gestão do ciclo de vida da identidade.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65e1217041d85b66664792d9475cdfcb517559b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172440"
---
# <a name="what-is-identity-lifecycle-management"></a>O que é a gestão do ciclo de vida de identidades?

A Governação da Identidade ajuda as organizações a alcançar um equilíbrio entre produtividade - Quão rapidamente uma pessoa pode ter acesso aos recursos de que precisa, como quando se junta à minha organização? E segurança - Como deve o seu acesso mudar ao longo do tempo, como devido a alterações ao estatuto laboral dessa pessoa?

**A gestão do ciclo de vida identitária** é a base para a Governança identitária, e uma governação eficaz em escala requer a modernização da infraestrutura de gestão do ciclo de vida identitária para aplicações. A Gestão do Ciclo de Vida identitária visa automatizar e gerir todo o processo de ciclo de vida da identidade digital. 

![fornecimento de nuvens](media/what-is-provisioning/cloud-1.png)

## <a name="what-is-a-digital-identity"></a>O que é uma identidade digital?

Uma identidade digital é informação sobre uma entidade utilizada por um ou mais recursos informáticos - como sistemas operativos ou aplicações. Estas entidades podem representar pessoas, organizações, aplicações ou dispositivos.  A identidade é geralmente descrita pelos atributos que lhe estão associados, como o nome, identificadores, bem como propriedades como funções usadas para gestão de acessos.  Estes atributos ajudam os sistemas a fazer determinações tais que têm acesso ao quê e a quem é permitido usar este ou aquele sistema.  

## <a name="managing-the-lifecycle-of-digital-identities"></a>Gestão do ciclo de vida das identidades digitais

Gerir identidades digitais é uma tarefa complexa, especialmente porque se relaciona com objetos do mundo real, como uma pessoa e a sua relação com uma organização como funcionário dessa organização, com uma representação digital.    Em pequenas organizações, manter a representação digital de indivíduos que necessitam de uma identidade pode ser um processo manual - quando alguém é contratado, ou um empreiteiro chega, um especialista em TI pode criar uma conta para eles num diretório, e atribuir-lhes o acesso de que precisam.  No entanto, em organizações de tamanho médio e de grande dimensão, a automação pode permitir à organização escalar de forma mais eficaz e manter as identidades precisas.

O processo típico de criação de gestão do ciclo de vida identitário numa organização segue estes passos:

1. Determine se já existem sistemas de registo: fontes de dados que a organização trata como autoritárias.  Por exemplo, a organização pode ter um dia de trabalho do sistema de RH, e esse sistema é autoritário para fornecer a lista atual de funcionários, e algumas das suas propriedades, como o nome ou departamento do funcionário.  Ou um sistema de e-mail como o Exchange Online pode ser autoritário para o endereço de e-mail de um funcionário.

2. Ligue esses sistemas de registo a um ou mais diretórios e bases de dados utilizados por aplicações e resolva quaisquer inconsistências entre os diretórios e os sistemas de registo. Por exemplo, um diretório pode ter dados obsoletos, como uma conta para um ex-funcionário, que já não é necessária. 

3. Determinar que processos podem ser utilizados para fornecer informações autoritárias na ausência de um sistema de registo.  Por exemplo, se existem identidades digitais, mas visitantes, mas a organização não tem base de dados para os visitantes, então pode ser necessário encontrar uma forma alternativa de determinar quando uma identidade digital para um visitante já não é necessária.

4. Configure que as alterações do sistema de registo ou outros processos são replicadas em cada um dos diretórios ou bases de dados que requerem uma atualização.

## <a name="identity-lifecycle-management-for-representing-employees-and-other-individuals-with-an-organizational-relationship"></a>Gestão do ciclo de vida identitário para representar colaboradores e outros indivíduos com uma relação organizacional

Ao planear a gestão do ciclo de vida da identidade para colaboradores, ou outros indivíduos com uma relação organizacional como um empreiteiro ou estudante, muitas organizações modelam o processo de "juntar, mover e sair".  Esses avisos são:
    
   - Junte-se - quando um indivíduo entra em campo de necessidade de acesso, uma identidade é necessária por essas aplicações, por isso uma nova identidade digital pode ter de ser criada se não estiver disponível
   - Mover - quando um indivíduo se move entre fronteiras, que requer autorizações de acesso adicionais para ser adicionado ou removido à sua identidade digital
   - Sair, quando um indivíduo deixa o âmbito de acesso necessário, o acesso pode ter de ser removido e, posteriormente, a identidade deixa de ser exigida por aplicações que não sejam para fins de auditoria ou perícia

Assim, por exemplo, se um novo funcionário se juntar à sua organização, que nunca esteve afiliada à sua organização antes, esse funcionário exigirá uma nova identidade digital, representada como uma conta de utilizador no Azure AD.  A criação desta conta cairia num processo de "Joiner", que poderia ser automatizado se houvesse um sistema de registos como o Workday que pudesse indicar quando o novo empregado começa a trabalhar.  Mais tarde, se a sua organização tiver uma mudança de colaborador de, digamos, Vendas para Marketing, eles cairiam num processo de "Mover".  Isto exigiria a eliminação dos direitos de acesso que tinham na organização sales que já não necessitam, e conceder-lhes direitos na organização de Marketing que eles novos exigem.

## <a name="identity-lifecycle-management-for-guests"></a>Gestão do ciclo de vida de identidade para hóspedes

Processos semelhantes também são necessários para hóspedes e outros utilizadores.  A gestão de direitos AD AZure utiliza a Azure AD business-to-business (B2B) para fornecer os controlos de ciclo de vida necessários para colaborar com pessoas fora da sua organização que exigem acesso aos recursos da sua organização. Com o Azure AD B2B, os utilizadores externos autenticam-se no seu diretório de origem, mas têm uma representação no seu diretório. A representação no seu diretório permite ao utilizador ter acesso aos seus recursos.  A gestão de direitos permite que indivíduos fora da sua organização solicitem acesso, criando uma identidade digital para eles conforme necessário. Estas identidades digitais são automaticamente removidas quando o utilizador perde o acesso.  

## <a name="how-does-azure-ad-automate-identity-lifecycle-management"></a>Como é que a Azure AD automatiza a gestão do ciclo de vida da identidade?

A Azure AD fornece atualmente estas funcionalidades:

* Os utilizadores que representam os colaboradores podem ser automaticamente criados e atualizados em Azure AD e Ative Directory utilizando [provisões orientadas para os RH](what-is-hr-driven-provisioning.md)
* Os utilizadores já presentes no Ative Directory podem ser automaticamente criados e mantidos em Azure AD utilizando [o provisionamento inter-directório](what-is-inter-directory-provisioning.md)
* Os utilizadores podem ser automaticamente atribuídos a grupos com base nas suas propriedades, utilizando [grupos dinâmicos](../external-identities/use-dynamic-groups.md#what-are-dynamic-groups) e podem, a pedido, ser atribuídos a grupos, equipas, funções AD Azure, funções de recursos Azure e sites Online SharePoint, utilizando [a gestão de direitos](entitlement-management-scenarios.md) e [gestão de identidade privilegiada](../privileged-identity-management/pim-configure.md)
* Atualizações para os utilizadores podem ser enviadas automaticamente para mais aplicações usando [o provisionamento de apps](what-is-app-provisioning.md)

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [Reger o acesso dos utilizadores externos na gestão dos direitos da AD Azure](./entitlement-management-external-users.md)
- [O que é o fornecimento conduzido pela HR?](what-is-hr-driven-provisioning.md)
- [O que é o aprovisionamento de aplicações?](what-is-app-provisioning.md)
- [O que é o aprovisionamento entre diretórios?](what-is-inter-directory-provisioning.md)