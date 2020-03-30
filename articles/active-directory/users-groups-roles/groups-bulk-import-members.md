---
title: Carregamento de importação a granel para adicionar membros a um grupo - Azure Ative Diretório [ Azure Ative Diretório ] Microsoft Docs
description: Adicione membros do grupo a granel no centro de administração do Azure Ative Directory.
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
ms.openlocfilehash: 8ff4234d9fd28e655e868108e37b09c5afe2767c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517122"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Membros do grupo de importação a granel (pré-visualização) no Diretório Ativo do Azure

Utilizando o portal Azure Ative Directory (Azure AD), pode adicionar um grande número de membros a um grupo utilizando um ficheiro de valores separados de vírem (CSV) aos membros do grupo de importação a granel.

## <a name="to-bulk-import-group-members"></a>Aos membros do grupo de importação a granel

1. Inscreva-se [no portal Azure](https://portal.azure.com) com uma conta de administrador de utilizador na organização. Os proprietários de grupos também podem importar a granel membros de grupos que possuem.
1. Em Azure AD, selecione **Grupos** > **Todos os grupos**.
1. Abra o grupo ao qual adiciona membros e, em seguida, selecione **Membros**.
1. Na página **dos Membros,** selecione **Os membros da Importação**.
1. Na página dos membros do grupo de **importação a granel (Pré-visualização),** selecione **Download** para obter o modelo de ficheiro CSV com propriedades de membros do grupo necessários.

    ![O comando dos Membros importados está na página de perfil do grupo](./media/groups-bulk-import-members/import-panel.png)

1. Abra o ficheiro CSV e adicione uma linha para cada membro do grupo que pretende importar para o grupo (os valores exigidos são o ID do **objeto membro** ou o nome principal do **utilizador).** Em seguida, guarde o ficheiro.

   ![O ficheiro CSV contém nomes e IDs para os membros importarem](./media/groups-bulk-import-members/csv-file.png)

1. Na página dos membros do **grupo de importação a granel (Pré-visualização),** sob o upload do seu ficheiro **CSV,** navegue para o ficheiro. Quando selecionar o ficheiro, a validação do ficheiro CSV começa.
1. Quando o conteúdo do ficheiro é validado, a página de importação a granel apresenta **file uploaded com sucesso**. Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, selecione **Submeter** para iniciar a operação a granel Azure que importa os membros do grupo para o grupo.
1. Quando a operação de importação estiver concluída, verá uma notificação de que a operação a granel foi bem sucedida.

## <a name="check-import-status"></a>Verificar o estado da importação

Pode ver o estado de todos os seus pedidos a granel pendentes na página de resultados da **operação Bulk (pré-visualização).**

   ![A página de resultados das operações a granel mostra o seu estado de pedido em massa](./media/groups-bulk-import-members/bulk-center.png)

Para mais detalhes sobre cada item de linha dentro da operação a granel, selecione os valores sob as colunas **# Sucesso**, **# Falha,** ou **Total Solicitações.** Se ocorrerem falhas, as razões da falha serão listadas.

## <a name="bulk-import-service-limits"></a>Limites do serviço de importação a granel

Cada atividade a granel para importar uma lista de membros do grupo pode funcionar até uma hora. Isto permite a importação de uma lista de pelo menos 40.000 membros.

## <a name="next-steps"></a>Passos seguintes

- [Remoção a granel membros do grupo](groups-bulk-remove-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
- [Faça o download de uma lista de todos os grupos](groups-bulk-download.md)
