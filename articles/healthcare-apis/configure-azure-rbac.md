---
title: Configure Azure Role Based Access Control (RBAC) para AZure API para FHIR
description: Este artigo descreve como configurar o Azure RBAC para a AZure API para o plano de dados FHIR
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: mihansen
ms.openlocfilehash: 3fbbfd931efb0e570bc96639bc6b7943fddc0237
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417680"
---
# <a name="configure-azure-rbac-for-fhir"></a>Configure Azure RBAC para FHIR 

Neste artigo, você aprenderá a usar [o Azure Role Based Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/) para atribuir acesso à AZure API para o plano de dados FHIR. O Azure RBAC é o método preferido para atribuir o acesso a um plano de dados quando os utilizadores de planos de dados são geridos no inquilino do Azure Ative Directory associado à sua assinatura Azure. Se estiver a utilizar um inquilino externo do Azure Ative Directory, consulte a [referência de atribuição local do RBAC](configure-local-rbac.md).

## <a name="confirm-azure-rbac-mode"></a>Confirme o modo Azure RBAC

Para utilizar o Azure RBAC, o seu API Azure para FHIR deve ser configurado para usar o seu inquilino de subscrição Azure para o plano de dados e não deve haver iDs de objeto de identidade atribuídos. Pode verificar as suas definições inspecionando a lâmina de **autenticação** da sua API Azure para fHIR:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Confirme o modo Azure RBAC":::

A **Autoridade** deve ser definida para o inquilino do diretório Azure Ative associado à sua subscrição e não deve haver GUIDs na caixa etiquetada **Identificações de objetos permitidos**. Também notará que a caixa está desativada e uma etiqueta indica que o Azure RBAC deve ser usado para atribuir funções de plano de dados.

## <a name="assign-roles"></a>Atribuir funções

Para conceder aos utilizadores, principais de serviço ou grupos acesso ao plano de dados FHIR, clique no **controlo de acesso (IAM)**, clique em **atribuições de Role** e clique + **Adicionar**:

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Adicionar atribuição de função Azure RBAC":::

Na seleção **Role,** procure uma das funções incorporadas para o plano de dados FHIR:

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="Funções de dados do FHIR incorporados":::

Pode escolher entre:

* Leitor de dados FHIR: Pode ler (e pesquisar) dados do FHIR.
* FHIR Data Writer: Pode ler, escrever e eliminar suavemente os dados do FHIR.
* FHIR Data Exporter: Pode ler e exportar `$export` dados (operador).
* Colaborador de dados da FHIR: Pode efetuar todas as operações do plano de dados.

Se estas funções não forem suficientes para a sua necessidade, também pode [criar funções personalizadas](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell).

Na caixa **Select,** procure um utilizador, um responsável de serviço ou um grupo a que deseje atribuir a função.

## <a name="caching-behavior"></a>Comportamento caching

A Azure API para fHIR irá cache decisões por até 5 minutos. Se conceder a um utilizador acesso ao servidor FHIR adicionando-os à lista de IDs de objetos permitidos, ou removê-los da lista, deverá esperar que leve até cinco minutos para que as alterações nas permissões se propaguem.

## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a atribuir papéis de Azure RBAC para o plano de dados FHIR. Em seguida, saiba mais sobre as definições adicionais para a AZure API para FHIR:
 
>[!div class="nextstepaction"]
>[Definições adicionais Azure API para FHIR](azure-api-for-fhir-additional-settings.md)
