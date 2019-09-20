---
title: Upload de importação em massa para adicionar membros a um grupo-Azure Active Directory | Microsoft Docs
description: Adicione membros do grupo em massa no centro de administração do Azure Active Directory.
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
ms.openlocfilehash: 4412bc9ce8d78b5810b25b60724575af66774127
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146268"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Importar membros do grupo (visualização) em massa no Azure Active Directory

Usando o portal do Azure Active Directory (AD do Azure), você pode adicionar um grande número de membros a um grupo usando um arquivo CSV (valores separados por vírgula) para importar membros do grupo em massa.

> [!NOTE]
> As operações em massa do Azure AD são um recurso de visualização pública do Azure AD e estão disponíveis com qualquer plano de licença pago do Azure AD. Para obter mais informações sobre os termos de uso de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="to-bulk-import-group-members"></a>Para importar membros do grupo em massa

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de administrador de usuário na organização. Os proprietários do grupo também podem importar em massa membros de grupos que possuem.
1. No Azure AD, selecione **grupos** > **todos os grupos**.
1. Abra o grupo ao qual você está adicionando Membros e, em seguida, selecione **Membros**.
1. Na página **Membros** , selecione **importar Membros**.
1. Na página **membros do grupo de importação em massa (versão prévia)** , selecione **baixar** para obter o modelo de arquivo CSV com as propriedades necessárias do membro do grupo.

    ![O comando importar Membros está na página de perfil do grupo](./media/groups-bulk-import-members/import-panel.png)

1. Abra o arquivo CSV e adicione uma linha para cada membro do grupo que você deseja importar para o grupo (os valores necessários são **ID de objeto de membro** ou **nome UPN**). Em seguida, guarde o ficheiro.

   ![O arquivo CSV contém nomes e IDs para os membros a serem importados](./media/groups-bulk-import-members/csv-file.png)

1. Na página **membros do grupo de importação em massa (versão prévia)** , em **carregar o arquivo CSV**, navegue até o arquivo. Quando você seleciona o arquivo, a validação do arquivo CSV é iniciada.
1. Quando o conteúdo do arquivo é validado, a página importação em massa exibe o **arquivo carregado com êxito**. Se houver erros, você deverá corrigi-los antes de poder enviar o trabalho.
1. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que importa os membros do grupo para o grupo.
1. Quando a operação de importação for concluída, você verá uma notificação de que a operação em massa foi bem-sucedida.

## <a name="check-import-status"></a>Verificar status da importação

Você pode ver o status de todas as suas solicitações em massa pendentes na página **resultados da operação em massa (versão prévia)** .

   ![A página resultados de operações em massa mostra o status de solicitação em massa](./media/groups-bulk-import-members/bulk-center.png)

Para obter detalhes sobre cada item de linha na operação em massa, selecione os valores nas colunas **# êxito**, **# falha**ou **total de solicitações** . Se ocorrerem falhas, os motivos da falha serão listados.

## <a name="bulk-import-service-limits"></a>Limites do serviço de importação em massa

Cada atividade em massa para importar uma lista de membros do grupo pode ser executada por até uma hora. Isso permite a importação de uma lista de pelo menos 40.000 membros.

## <a name="next-steps"></a>Passos seguintes

- [Remover membros do grupo em massa](groups-bulk-remove-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
- [Baixar uma lista de todos os grupos](groups-bulk-download.md)
