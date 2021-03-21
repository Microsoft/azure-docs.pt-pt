---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 654a7f18d7b06756a6073b4203ea7b6042d1c747
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604946"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[Máquinas de auditoria com definições de segurança de senha insegura](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Esta iniciativa implementa os requisitos de política e audita máquinas com definições de segurança de senha inseguras. Para mais informações sobre as políticas de Configuração de Hóspedes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[\[\]Pré-visualização : Implementar pré-requisitos para permitir políticas de configuração de hóspedes em máquinas virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Esta iniciativa adiciona uma identidade gerida atribuída ao sistema e implementa a extensão de Configuração de Hóspedes apropriada para a plataforma para máquinas virtuais que são elegíveis para serem monitorizadas pelas políticas de Configuração de Hóspedes. Este é um pré-requisito para todas as políticas de Configuração de Hóspedes e deve ser atribuído ao âmbito de atribuição de políticas antes de usar qualquer política de Configuração de Convidados. Para mais informações sobre a Configuração do Hóspede, visite [https://aka.ms/gcpol](https://aka.ms/gcpol) . |4 |1.0.0-pré-visualização |
|[\[Pré-visualização \] : As máquinas windows devem satisfazer os requisitos para a linha de base de segurança Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Esta iniciativa audita máquinas Windows com configurações que não cumprem a linha de base de segurança Azure. Para mais detalhes, visite [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0-pré-visualização |
