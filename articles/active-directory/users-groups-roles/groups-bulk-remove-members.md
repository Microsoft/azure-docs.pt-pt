---
title: A granel remove os membros do grupo carregando um ficheiro CSV - Azure Ative Directory / Microsoft Docs
description: Remova os membros do grupo em operações a granel no centro de administração Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: acf3cba5046a20b99212e89b378327765d22fa23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421642"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>A granel remove membros do grupo no Azure Ative Directory

Utilizando o portal Azure Ative Directory (Azure AD), pode remover um grande número de membros de um grupo utilizando um ficheiro de valores separados por vírgula (CSV) para remover em massa membros do grupo.

## <a name="understand-the-csv-template"></a>Compreenda o modelo CSV

Faça o download e preencha o modelo de CSV de upload a granel para adicionar com sucesso membros do grupo AD AZure a granel. O seu modelo de CSV pode parecer este exemplo:

![Folha de cálculo para upload e chamadas explicando a finalidade e valores de cada linha e coluna](./media/groups-bulk-remove-members/template-example.png)

### <a name="csv-template-structure"></a>Estrutura de modelo de CSV

As linhas de um modelo de CSV descarregado são as seguintes:

- **Número da versão**: A primeira linha que contém o número da versão deve ser incluída no upload CSV.
- **Posições de coluna :** O formato das posições da coluna é &lt; *o nome do item* &gt; [Nome de Propriedade] &lt; *Obrigatório ou em branco* &gt; . Por exemplo, `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Algumas versões mais antigas do modelo podem ter ligeiras variações. Para alterações na adesão ao grupo, tem a opção de utilizar o identificador: ID de objeto de membro ou nome principal do utilizador.
- **Linha exemplos**: Incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Deve remover os exemplos e substituí-lo pelas suas próprias entradas.

### <a name="additional-guidance"></a>Orientações adicionais

- As duas primeiras linhas do modelo de upload não devem ser removidas ou modificadas, ou o upload não pode ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos a adição de novas colunas ao modelo. Quaisquer colunas adicionais que adicionar são ignoradas e não processadas.
- Recomendamos que descarregue a versão mais recente do modelo CSV com a maior frequência possível.

## <a name="to-bulk-remove-group-members"></a>Para remover em massa membros do grupo

1. Inscreva-se [no portal Azure](https://portal.azure.com) com uma conta de administrador do Utilizador na organização. Os proprietários do grupo também podem remover em massa membros de grupos que possuem.
1. Em Azure AD, selecione **Grupos**  >  **Todos os grupos**.
1. Abra o grupo do qual está a retirar membros e, em seguida, selecione **Membros**.
1. Na página dos **membros,** selecione **Remover os membros**.
1. Na página **de membros do grupo de remoção a granel,** selecione **Descarregue** para obter o modelo de ficheiro CSV com as propriedades necessárias do membro do grupo.

   ![O comando dos Membros Removidos está na página de perfil do grupo](./media/groups-bulk-remove-members/remove-panel.png)

1. Abra o ficheiro CSV e adicione uma linha para cada membro do grupo que pretende remover do grupo (os valores exigidos são ID do objeto membro ou nome principal do utilizador). Em seguida, guarde o ficheiro.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="O ficheiro CSV contém nomes e IDs dos membros do grupo para remover":::

1. Na página **de membros do grupo Em Massa,** no **upload do seu ficheiro csv,** navegue para o ficheiro. Quando selecionar o ficheiro, inicia-se a validação do ficheiro CSV.
1. Quando o conteúdo do ficheiro é validado, a página de importação a granel exibe **o Ficheiro carregado com sucesso**. Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, **selecione Enviar** por terminação da operação a granel Azure que retira os membros do grupo do grupo.
1. Quando a operação de remoção estiver concluída, verá uma notificação de que a operação a granel foi bem sucedida.

## <a name="check-removal-status"></a>Verifique o estado da remoção

Pode ver o estado de todos os seus pedidos em massa pendentes na página de resultados da **operação Em Massa.**

[![Verifique o estado na página resultados das operações em massa](media/groups-bulk-remove-members/bulk-center.png)](media/groups-bulk-remove-members/bulk-center.png#lightbox)

Para mais detalhes sobre cada item de linha dentro da operação a granel, selecione os valores sob as colunas **# Sucesso**, **# Falha**, ou **Pedidos Totais.** Se ocorrerem falhas, as razões do fracasso serão listadas.

## <a name="bulk-removal-service-limits"></a>Limites de serviço de remoção a granel

Cada atividade a granel para remover uma lista de membros do grupo pode funcionar até uma hora. Isto permite a remoção de uma lista de pelo menos 40.000 membros.

## <a name="next-steps"></a>Passos seguintes

- [Membros do grupo de importação a granel](groups-bulk-import-members.md)
- [Descarregue membros de um grupo](groups-bulk-download-members.md)
- [Faça o download de uma lista de todos os grupos](groups-bulk-download.md)
