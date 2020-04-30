---
title: Remova os membros do grupo através do upload de um ficheiro CSV - Diretório Ativo Azure [ Diretório Ativo ] Microsoft Docs
description: Remova os membros do grupo em operações a granel no centro de administração Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b3c6e471a8e44236baf9bfc2c8eb6c9d5526d72
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203467"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>A granel remove membros do grupo no Diretório Ativo do Azure

Utilizando o portal Azure Ative Directory (Azure AD), pode remover um grande número de membros de um grupo utilizando um ficheiro de valores separados de vírem (CSV) para remover a granel os membros do grupo.

## <a name="understand-the-csv-template"></a>Compreenda o modelo CSV

Descarregue e preencha o modelo CSV de carregamento a granel para adicionar com sucesso membros do grupo Azure AD a granel. O seu modelo CSV pode parecer este exemplo:

![Folha de cálculo para upload e chamadas explicando o propósito e valores para cada linha e coluna](./media/groups-bulk-remove-members/template-example.png)

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas num modelo CSV descarregado são as seguintes:

- **Número**da versão : A primeira linha que contém o número da versão deve ser incluída no CSV de carregamento.
- **Títulos da coluna**: O formato das rubricas da coluna é &lt;o nome&gt; &lt; *item* [PropertyName] *exigido ou em branco*&gt;. Por exemplo, `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Algumas versões mais antigas do modelo podem ter ligeiras variações. Para alterações na adesão ao grupo, tem a opção de identificar a utilização: id do objeto membro ou nome principal do utilizador.
- **Exemplos de linha**: Incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Deve remover a linha de exemplos e substituí-la pelas suas próprias entradas.

### <a name="additional-guidance"></a>Orientações adicionais

- As duas primeiras linhas do modelo de carregamento não devem ser removidas ou modificadas, ou o upload não pode ser processado.
- As colunas necessárias estão listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Quaisquer colunas adicionais que adicione são ignoradas e não processadas.
- Recomendamos que descarregue a versão mais recente do modelo CSV sempre que possível.

## <a name="to-bulk-remove-group-members"></a>Para remover a granel membros do grupo

1. Inscreva-se [no portal Azure](https://portal.azure.com) com uma conta de administrador de utilizador na organização. Os proprietários de grupos também podem remover em massa membros de grupos que possuem.
1. Em Azure AD, selecione **Grupos** > **Todos os grupos**.
1. Abra o grupo a partir do qual está a retirar membros e, em seguida, selecione **Membros**.
1. Na página **dos Membros,** selecione **Remover membros**.
1. Na página de remoção de membros do **grupo Bulk,** selecione **Download** para obter o modelo de ficheiro CSV com as propriedades necessárias do membro do grupo.

   ![O comando remover membros está na página de perfil do grupo](./media/groups-bulk-remove-members/remove-panel.png)

1. Abra o ficheiro CSV e adicione uma linha para cada membro do grupo que pretende remover do grupo (os valores necessários são id do objeto membro ou nome principal do utilizador). Em seguida, guarde o ficheiro.

   ![O ficheiro CSV contém nomes e IDs para os membros removerem](./media/groups-bulk-remove-members/csv-file.png)

1. Na página de remoção de membros do **grupo Bulk,** sob o **upload do ficheiro CSV,** navegue para o ficheiro. Quando selecionar o ficheiro, a validação do ficheiro CSV começa.
1. Quando o conteúdo do ficheiro é validado, a página de importação a granel apresenta **file uploaded com sucesso**. Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, selecione **Submeter** para iniciar a operação a granel Azure que remove os membros do grupo do grupo.
1. Quando a operação de remoção terminar, verá uma notificação de que a operação a granel foi bem sucedida.

## <a name="check-removal-status"></a>Verifique o estado de remoção

Pode ver o estado de todos os seus pedidos a granel pendentes na página de resultados da **operação Bulk.**

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

Para mais detalhes sobre cada item de linha dentro da operação a granel, selecione os valores sob as colunas **# Sucesso**, **# Falha,** ou **Total Solicitações.** Se ocorrerem falhas, as razões da falha serão listadas.

## <a name="bulk-removal-service-limits"></a>Limites do serviço de remoção de granéis

Cada atividade a granel para remover uma lista de membros do grupo pode funcionar até uma hora. Isto permite a remoção de uma lista de pelo menos 40.000 membros.

## <a name="next-steps"></a>Passos seguintes

- [Membros do grupo de importação a granel](groups-bulk-import-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
- [Faça o download de uma lista de todos os grupos](groups-bulk-download.md)
