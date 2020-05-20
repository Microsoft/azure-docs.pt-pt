---
title: Upload a granel para adicionar ou criar membros de um grupo - Azure Ative Diretório [ Azure Ative Diretório ] Microsoft Docs
description: Adicione membros do grupo a granel no centro de administração do Azure Ative Directory.
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
ms.openlocfilehash: 5b83b0687db72c78a0c7788933acf07f5cb9e0ed
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83679335"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Granel adicionar membros do grupo no Diretório Ativo Azure

Utilizando o portal Azure Ative Directory (Azure AD), pode adicionar um grande número de membros a um grupo utilizando um ficheiro de valores separados de vírem (CSV) aos membros do grupo de importação a granel.

## <a name="understand-the-csv-template"></a>Compreenda o modelo CSV

Descarregue e preencha o modelo CSV de carregamento a granel para adicionar com sucesso membros do grupo Azure AD a granel. O seu modelo CSV pode parecer este exemplo:

![Folha de cálculo para upload e chamadas explicando o propósito e valores para cada linha e coluna](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas num modelo CSV descarregado são as seguintes:

- **Número**da versão : A primeira linha que contém o número da versão deve ser incluída no CSV de carregamento.
- **Títulos da coluna**: O formato das rubricas da coluna é &lt; *o nome item* &gt; [PropertyName] &lt; *exigido ou em branco* &gt; . Por exemplo, `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Algumas versões mais antigas do modelo podem ter ligeiras variações. Para alterações na adesão ao grupo, tem a opção de identificar a utilização: id do objeto membro ou nome principal do utilizador.
- **Exemplos de linha**: Incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Deve remover a linha de exemplos e substituí-la pelas suas próprias entradas.

### <a name="additional-guidance"></a>Orientações adicionais

- As duas primeiras linhas do modelo de carregamento não devem ser removidas ou modificadas, ou o upload não pode ser processado.
- As colunas necessárias estão listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Quaisquer colunas adicionais que adicione são ignoradas e não processadas.
- Recomendamos que descarregue a versão mais recente do modelo CSV sempre que possível.

## <a name="to-bulk-import-group-members"></a>Aos membros do grupo de importação a granel

1. Inscreva-se [no portal Azure](https://portal.azure.com) com uma conta de administrador de utilizador na organização. Os proprietários de grupos também podem importar a granel membros de grupos que possuem.
1. Em Azure AD, selecione **Groups**  >  **Grupos Todos os grupos**.
1. Abra o grupo ao qual adiciona membros e, em seguida, selecione **Membros**.
1. Na página **dos Membros,** selecione **Os membros da Importação**.
1. Na página dos membros do **grupo de importação a granel,** selecione **Download** para obter o modelo de ficheiro CSV com propriedades de membros do grupo necessários.

    ![O comando dos Membros importados está na página de perfil do grupo](./media/groups-bulk-import-members/import-panel.png)

1. Abra o ficheiro CSV e adicione uma linha para cada membro do grupo que pretende importar para o grupo (os valores exigidos são o ID do **objeto membro** ou o nome principal do **utilizador).** Em seguida, guarde o ficheiro.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="O ficheiro CSV contém nomes e IDs dos membros para importar":::

1. Na página dos membros do **grupo de importação a granel,** sob o upload do seu ficheiro **csv,** navegue para o ficheiro. Quando selecionar o ficheiro, a validação do ficheiro CSV começa.
1. Quando o conteúdo do ficheiro é validado, a página de importação a granel apresenta **file uploaded com sucesso**. Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, selecione **Submeter** para iniciar a operação a granel Azure que importa os membros do grupo para o grupo.
1. Quando a operação de importação estiver concluída, verá uma notificação de que a operação a granel foi bem sucedida.

## <a name="check-import-status"></a>Verificar o estado da importação

Pode ver o estado de todos os seus pedidos a granel pendentes na página de resultados da **operação Bulk.**

[![](media/groups-bulk-import-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-import-members/bulk-center.png#lightbox)

Para mais detalhes sobre cada item de linha dentro da operação a granel, selecione os valores sob as colunas **# Sucesso**, **# Falha,** ou **Total Solicitações.** Se ocorrerem falhas, as razões da falha serão listadas.

## <a name="bulk-import-service-limits"></a>Limites do serviço de importação a granel

Cada atividade a granel para importar uma lista de membros do grupo pode funcionar até uma hora. Isto permite a importação de uma lista de pelo menos 40.000 membros.

## <a name="next-steps"></a>Passos seguintes

- [Remoção a granel membros do grupo](groups-bulk-remove-members.md)
- [Baixar membros de um grupo](groups-bulk-download-members.md)
- [Faça o download de uma lista de todos os grupos](groups-bulk-download.md)
