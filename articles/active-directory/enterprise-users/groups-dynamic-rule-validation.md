---
title: Validar regras para a adesão dinâmica ao grupo (pré-visualização) - Azure AD / Microsoft Docs
description: Como testar membros contra uma regra de adesão para um grupo dinâmico no Azure Ative Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ccd17dc4112bd286257a6ed95ecc55dd13af428
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650755"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Validar uma regra de adesão dinâmica do grupo (pré-visualização) no Azure Ative Directory

O Azure Ative Directory (Azure AD) dispõe agora dos meios para validar regras dinâmicas de grupo (na pré-visualização pública). No separador **Regras Validadas,** pode validar a sua regra dinâmica contra membros do grupo de amostras para confirmar que a regra está a funcionar como esperado. Ao criar ou atualizar regras dinâmicas do grupo, os administradores querem saber se um utilizador ou um dispositivo será membro do grupo. Isto ajuda a avaliar se o utilizador ou dispositivo cumpre os critérios de regra e ajuda na resolução de problemas quando a adesão não é esperada.

## <a name="step-by-step-walk-through"></a>Passo a passo

Para começar, vá ao **Azure Ative Directory**  >  **Groups**. Selecione um grupo dinâmico existente ou crie um novo grupo dinâmico e clique nas regras de adesão Dynamic. Em seguida, pode ver o **separador Regras validadas.**

![Encontre o separador regras Validate e comece com uma regra existente](./media/groups-dynamic-rule-validation/validate-tab.png)

No **separador Regras De Validação,** pode selecionar utilizadores para validar os seus membros. 20 utilizadores ou dispositivos podem ser selecionados de uma só vez.

![Adicione os utilizadores para validar a regra existente contra](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Depois de escolher os utilizadores ou dispositivos do picker, e **Select**, a validação iniciar-se-á automaticamente e aparecerá os resultados da validação.

![Ver os resultados da validação da regra](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Os resultados dizem se um utilizador é ou não membro do grupo. Se a regra não for válida ou houver um problema de rede, o resultado será apresentado como **Desconhecido**. Em caso de **Desconhecido,** a mensagem de erro detalhada descreverá o problema e as ações necessárias.

![Ver os detalhes dos resultados da validação da regra](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Pode modificar a regra e a validação dos membros será desencadeada. Para ver por que o utilizador não é membro do grupo, clique em "Ver detalhes" e os detalhes de verificação mostrarão o resultado de cada expressão que compõe a regra. Clique **OK** para sair.

## <a name="next-steps"></a>Próximos passos

- [Regras dinâmicas de adesão para grupos](groups-dynamic-membership.md)
