---
title: Configure Azure controlo de acesso baseado em funções (Azure RBAC) para Azure API para FHIR
description: Este artigo descreve como configurar o Azure RBAC para a AZure API para o plano de dados FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.reviewer: dseven
ms.openlocfilehash: fb57b689b77ec4cc6205c8885c2a2e062c469efb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019722"
---
# <a name="configure-azure-rbac-for-fhir"></a>Configure Azure RBAC para FHIR 

Neste artigo, você aprenderá a usar o [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/index.yml) para atribuir acesso à AZure API para plano de dados FHIR. O Azure RBAC é o método preferido para atribuir o acesso a um plano de dados quando os utilizadores de planos de dados são geridos no inquilino do Azure Ative Directory associado à sua assinatura Azure. Se estiver a utilizar um inquilino externo do Azure Ative Directory, consulte a [referência de atribuição local do RBAC](configure-local-rbac.md).

## <a name="confirm-azure-rbac-mode"></a>Confirme o modo Azure RBAC

Para utilizar o Azure RBAC, o seu API Azure para FHIR deve ser configurado para usar o seu inquilino de subscrição Azure para o plano de dados e não deve haver iDs de objeto de identidade atribuídos. Pode verificar as suas definições inspecionando a lâmina de **autenticação** da sua API Azure para fHIR:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Confirme o modo Azure RBAC":::

A **Autoridade** deve ser definida para o inquilino do diretório Azure Ative associado à sua subscrição e não deve haver GUIDs na caixa etiquetada **Identificações de objetos permitidos**. Também notará que a caixa está desativada e uma etiqueta indica que o Azure RBAC deve ser usado para atribuir funções de plano de dados.

## <a name="assign-roles"></a>Atribuir funções

Para conceder aos utilizadores, principais de serviço ou grupos acesso ao plano de dados FHIR, clique no **controlo de acesso (IAM)**, clique em **atribuições de Role** e clique + **Adicionar**:

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Adicionar atribuição de função Azure":::

Na seleção **Role,** procure uma das funções incorporadas para o plano de dados FHIR:

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="Funções de dados do FHIR incorporados":::

Pode escolher entre:

* Leitor de dados FHIR: Pode ler (e pesquisar) dados do FHIR.
* FHIR Data Writer: Pode ler, escrever e eliminar suavemente os dados do FHIR.
* FHIR Data Exporter: Pode ler e exportar `$export` dados (operador).
* Colaborador de dados da FHIR: Pode efetuar todas as operações do plano de dados.

Se estas funções não forem suficientes para a sua necessidade, também pode [criar funções personalizadas](../../role-based-access-control/tutorial-custom-role-powershell.md).

Na caixa **Select,** procure um utilizador, um responsável de serviço ou um grupo a que deseje atribuir a função.

## <a name="caching-behavior"></a>Comportamento caching

A Azure API para fHIR irá cache decisões por até 5 minutos. Se conceder a um utilizador acesso ao servidor FHIR adicionando-os à lista de IDs de objetos permitidos, ou removê-los da lista, deverá esperar que leve até cinco minutos para que as alterações nas permissões se propaguem.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a atribuir papéis Azure para o plano de dados FHIR. Para saber mais sobre as configurações adicionais para a AZure API para FHIR:
 
>[!div class="nextstepaction"]
>[Definições adicionais para Azure API para FHIR](azure-api-for-fhir-additional-settings.md)