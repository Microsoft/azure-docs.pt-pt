---
title: Remover em massa os membros do grupo carregando um arquivo CSV-Azure Active Directory | Microsoft Docs
description: Adicione usuários em massa no centro de administração do Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d384ea4749e2d0bc7edf8df7ac0508566f2f76b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517099"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Remover membros do grupo em massa (visualização) no Azure Active Directory

Usando o portal do Azure Active Directory (AD do Azure), você pode remover um grande número de membros de um grupo usando um arquivo CSV (valores separados por vírgula) para remover em massa membros do grupo.

## <a name="to-bulk-remove-group-members"></a>Para remover em massa membros do grupo

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador de usuário na organização. Os proprietários do grupo também podem remover em massa os membros dos grupos que eles possuem.
1. No Azure AD, selecione **grupos**  > **todos os grupos**.
1. Abra o grupo do qual você está removendo membros e, em seguida, selecione **Membros**.
1. Na página **Membros** , selecione **remover membros**.
1. Na página **remover membros do grupo em massa (versão prévia)** , selecione **baixar** para obter o modelo de arquivo CSV com as propriedades necessárias do membro do grupo.

   ![O comando remover membros está na página de perfil do grupo](./media/groups-bulk-remove-members/remove-panel.png)

1. Abra o arquivo CSV e adicione uma linha para cada membro do grupo que você deseja remover do grupo (os valores necessários são ID de objeto de membro ou nome UPN). Em seguida, guarde o ficheiro.

   ![O arquivo CSV contém nomes e IDs para os membros removerem](./media/groups-bulk-remove-members/csv-file.png)

1. Na página **remover membros do grupo em massa (versão prévia)** , em **carregar o arquivo CSV**, navegue até o arquivo. Quando você seleciona o arquivo, a validação do arquivo. csv é iniciada.
1. Quando o conteúdo do arquivo é validado, a página importação em massa exibe o **arquivo carregado com êxito**. Se houver erros, você deverá corrigi-los antes de poder enviar o trabalho.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que remove os membros do grupo do grupo.
1. Quando a operação de remoção for concluída, você verá uma notificação de que a operação em massa foi bem-sucedida.

## <a name="check-removal-status"></a>Verificar status da remoção

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação em massa (versão prévia)** .

   ![A página resultados de operações em massa mostra o status de solicitação em massa](./media/groups-bulk-remove-members/bulk-center.png)

Para obter detalhes sobre cada item de linha na operação em massa, selecione os valores nas colunas **# êxito**, **# falha**ou **total de solicitações** . Se ocorrerem falhas, os motivos da falha serão listados.

## <a name="bulk-removal-service-limits"></a>Limites de serviço de remoção em massa

Cada atividade em massa para remover uma lista de membros do grupo de pode ser executada por até uma hora. Isso permite a remoção de uma lista de pelo menos 40.000 membros.

## <a name="next-steps"></a>Passos seguintes

- [Membros do grupo de importação em massa](groups-bulk-import-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
- [Baixar uma lista de todos os grupos](groups-bulk-download.md)
