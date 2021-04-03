---
title: Upload a granel para adicionar ou criar membros de um grupo - Azure Ative Directory | Microsoft Docs
description: Adicione membros do grupo a granel no centro de administração Azure Ative.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22d39a2ee66f2c63612ad2cb3cf9ae61f1660de3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547751"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>A granel adiciona membros do grupo no Azure Ative Directory

Utilizando o portal Azure Ative Directory (Azure AD), pode adicionar um grande número de membros a um grupo utilizando um ficheiro de valores separados por vírgula (CSV) para membros do grupo de importação a granel.

## <a name="understand-the-csv-template"></a>Compreenda o modelo CSV

Faça o download e preencha o modelo de CSV de upload a granel para adicionar com sucesso membros do grupo AD AZure a granel. O seu modelo de CSV pode parecer este exemplo:

![Folha de cálculo para upload e chamadas explicando a finalidade e valores de cada linha e coluna](./media/groups-bulk-import-members/template-with-callouts.png)

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
- Adicione pelo menos duas UPNs de utilizadores ou IDs de objeto para carregar com sucesso o ficheiro.

## <a name="to-bulk-import-group-members"></a>Para importar a granel membros do grupo

1. Inscreva-se [no portal Azure](https://portal.azure.com) com uma conta de administrador do Utilizador na organização. Os proprietários do grupo também podem importar a granel membros de grupos que possuam.
1. Em Azure AD, selecione **Grupos**  >  **Todos os grupos**.
1. Abra o grupo ao qual está a adicionar membros e, em seguida, selecione **Membros**.
1. Na página dos **membros,** selecione **Membros de Importação**.
1. Na página de membros do **grupo de importação a granel,** selecione **Descarregue** para obter o modelo de ficheiro CSV com as propriedades necessárias do grupo membro.

    ![O comando dos Membros Importados está na página de perfil do grupo](./media/groups-bulk-import-members/import-panel.png)

1. Abra o ficheiro CSV e adicione uma linha para cada membro do grupo que pretende importar para o grupo (os valores exigidos são **iD de objeto de membro** ou nome principal do **utilizador).** Em seguida, guarde o ficheiro.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="O ficheiro CSV contém nomes e IDs dos membros para importar":::

1. Na página de membros do **grupo de importação a granel,** no upload do seu ficheiro **csv,** navegue para o ficheiro. Quando selecionar o ficheiro, inicia-se a validação do ficheiro CSV.
1. Quando o conteúdo do ficheiro é validado, a página de importação a granel exibe **o Ficheiro carregado com sucesso**. Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, **selecione Submeter-se** a iniciar a operação a granel Azure que importa os membros do grupo para o grupo.
1. Quando a operação de importação terminar, verá uma notificação de que a operação a granel foi bem sucedida.

## <a name="check-import-status"></a>Verificar o estado das importações

Pode ver o estado de todos os seus pedidos em massa pendentes na página de resultados da **operação Em Massa.**

[![Verifique o estado na página resultados das operações a granel.](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

Para mais detalhes sobre cada item de linha dentro da operação a granel, selecione os valores sob as colunas **# Sucesso**, **# Falha**, ou **Pedidos Totais.** Se ocorrerem falhas, as razões do fracasso serão listadas.

## <a name="bulk-import-service-limits"></a>Limites de serviços de importação a granel

Cada atividade a granel para importar uma lista de membros do grupo pode decorrer até uma hora. Isto permite a importação de uma lista de no máximo 40.000 membros.

## <a name="next-steps"></a>Passos seguintes

- [A granel remove membros do grupo](groups-bulk-remove-members.md)
- [Descarregue membros de um grupo](groups-bulk-download-members.md)
- [Faça o download de uma lista de todos os grupos](groups-bulk-download.md)
