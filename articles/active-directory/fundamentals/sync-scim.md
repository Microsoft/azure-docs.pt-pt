---
title: Sincronização SCIM com Diretório Ativo Azure
description: Orientação arquitetónica para alcançar a sincronização do SCIM com o Azure Ative Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b95aac504bc6ee72c353faecad25384e2dc90840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172423"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>Sincronização SCIM com Diretório Ativo Azure

O Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) é um protocolo padrão aberto para automatizar o intercâmbio de informações de identidade do utilizador entre domínios de identidade e sistemas de TI. O SCIM garante que os colaboradores adicionados ao sistema human capital management (HCM) têm automaticamente contas criadas no Azure Ative Directory (Azure AD) ou no Windows Server Ative Directory. Os atributos e perfis do utilizador são sincronizados entre os dois sistemas, atualizando os utilizadores com base no estado do utilizador ou na mudança de função.

SCIM é uma definição padronizada de dois pontos finais: a /Users's endpoint e um ponto final /Groups. Utiliza verbos rest comuns para criar, atualizar e eliminar objetos. Também usa um esquema pré-definido para atributos comuns como nome de grupo, nome de utilizador, nome de primeiro nome, apelido e e-mail. As aplicações que oferecem uma API SCIM 2.0 REST podem reduzir ou eliminar a dor de trabalhar com APIs ou produtos de gestão de utilizadores proprietários. Por exemplo, qualquer cliente em conformidade com o SCIM pode fazer um POST HTTP de um objeto JSON para o ponto final /Utilizadores para criar uma nova entrada no utilizador. Em vez de precisar de uma API ligeiramente diferente para as mesmas ações básicas, as aplicações que estão em conformidade com a norma SCIM podem instantaneamente tirar partido dos clientes, ferramentas e código pré-existentes. 

## <a name="use-when"></a>Utilize se: 

Pretende-se que forneça automaticamente informações do utilizador de um sistema HCM para O AD e Windows Server Ative Directory e, se necessário, para os sistemas de destino. 

![diagrama arquitetónico](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>Componentes do sistema 

* **Sistema HCM**: Aplicações e tecnologias que permitem o processo e práticas de Gestão de Capital Humano que suportam e automatizam processos de RH ao longo do ciclo de vida dos colaboradores. 

* **Serviço de Provisionamento Azure AD**: Utiliza o protocolo SCIM 2.0 para provisionamento automático. O serviço liga-se ao ponto final do SCIM para a aplicação, e utiliza o esquema de objetos de utilizador SCIM e as APIs REST para automatizar o fornecimento e desavisionamento de utilizadores e grupos.  

* **Azure AD**: Repositório de utilizadores utilizado para gerir o ciclo de vida das identidades e dos seus direitos. 

* **Sistema-alvo**: Aplicação ou sistema que tenha ponto final SCIM e trabalhe com o provisionamento Azure AD para permitir o fornecimento automático de utilizadores e grupos.  

## <a name="implement-scim-with-azure-ad"></a>Implementar SCIM com Azure AD 

* [Como o provisionamento funciona em Azure AD ](../app-provisioning/how-provisioning-works.md)

* [Gestão do fornecimento de conta de utilizador para aplicações empresariais no portal Azure ](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Construa um ponto final SCIM e configuure o fornecimento de utilizadores com Azure AD  ](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Conformidade do protocolo SCIM 2.0 do Serviço de Provisionamento Azure AD](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)