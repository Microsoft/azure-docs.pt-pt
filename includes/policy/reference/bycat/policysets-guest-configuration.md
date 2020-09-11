---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 70503b8914310d205582e93e92ed7c51dc7c54d2
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022033"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[Máquinas de auditoria com definições de segurança de senha insegura](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Esta iniciativa implementa os requisitos de política e audita máquinas com definições de segurança de senha inseguras. Para mais informações sobre as políticas de Configuração de Hóspedes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[Implementar pré-requisitos para permitir políticas de configuração de hóspedes em máquinas virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Esta iniciativa adiciona uma identidade gerida atribuída ao sistema e implementa a extensão de Configuração de Hóspedes apropriada para a plataforma para máquinas virtuais que são elegíveis para serem monitorizadas pelas políticas de Configuração de Hóspedes. Este é um pré-requisito para todas as políticas de Configuração de Hóspedes e deve ser atribuído ao âmbito de atribuição de políticas antes de usar qualquer política de Configuração de Convidados. Para mais informações sobre a Configuração do Hóspede, visite [https://aka.ms/gcpol](https://aka.ms/gcpol) . |4 |1.0.0-pré-visualização |
|[As máquinas windows devem satisfazer os requisitos para a linha de base de segurança Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Esta iniciativa audita máquinas Windows com configurações que não cumprem a linha de base de segurança Azure. Para mais detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0-pré-visualização |
