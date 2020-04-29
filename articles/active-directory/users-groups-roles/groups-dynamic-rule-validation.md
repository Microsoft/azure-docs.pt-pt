---
title: Valide regras para a adesão ao grupo dinâmico (pré-visualização) - Azure AD / Microsoft Docs
description: Como testar os membros contra uma regra de adesão para um grupo dinâmico no Diretório Ativo Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e081f62949f4cf3f7ae375b2c60ee5b032d974
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81115524"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Validar uma regra dinâmica de membro do grupo (pré-visualização) no Diretório Ativo do Azure

O Azure Ative Directory (Azure AD) fornece agora os meios para validar as regras dinâmicas do grupo (em pré-visualização pública). No separador de **regras Validar,** pode validar a sua regra dinâmica contra membros do grupo de amostras para confirmar que a regra está a funcionar como esperado. Ao criar ou atualizar regras dinâmicas do grupo, os administradores querem saber se um utilizador ou um dispositivo será membro do grupo. Isto ajuda a avaliar se o utilizador ou o dispositivo cumprem os critérios de regra e ajudam na resolução de problemas quando não é esperado a adesão.

## <a name="step-by-step-walk-through"></a>Passo a passo passo

Para começar, vá a **Azure Ative Directory** > **Groups**. Selecione um grupo dinâmico existente ou crie um novo grupo dinâmico e clique nas regras de adesão dinâmicas. Em seguida, pode ver o separador **Regras de Validação.**

![Encontre o separador de regras de Validação e comece com uma regra existente](./media/groups-dynamic-rule-validation/validate-tab.png)

No separador **de regras Valida,** pode selecionar os utilizadores para validar as suas subscrições. 20 utilizadores ou dispositivos podem ser selecionados de uma só vez.

![Adicione utilizadores para validar a regra existente contra](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Depois de escolher os utilizadores ou dispositivos do picker, e **Select**, a validação começará automaticamente e os resultados de validação aparecerão automaticamente.

![Ver os resultados da validação da regra](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Os resultados dizem se um utilizador é ou não membro do grupo. Se a regra não for válida ou houver um problema de rede, o resultado mostrar-se-á como **Desconhecido**. No caso de **Unknown,** a mensagem de erro detalhada descreverá o problema e as ações necessárias.

![Ver os detalhes dos resultados da validação da regra](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Pode modificar a regra e a validação dos membros será desencadeada. Para ver por que razão o utilizador não é membro do grupo, clique em "Ver detalhes" e os detalhes de verificação mostrarão o resultado de cada expressão que compõe a regra. Clique **em OK** para sair.

## <a name="next-steps"></a>Passos seguintes

- [Regras dinâmicas de adesão para grupos](groups-dynamic-membership.md)
