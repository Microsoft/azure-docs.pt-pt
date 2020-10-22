---
title: A granel restaura os utilizadores eliminados no portal Azure Ative Directory Microsoft Docs
description: Restaurar os utilizadores eliminados a granel no centro de administração Azure AD em Azure Ative Directory
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
ms.openlocfilehash: f4031356c3a2ff51f6f3da8b53dd0dcc9fd5a426
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376436"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Restaurar a granel utilizadores eliminados no Azure Ative Directory

O Azure Ative Directory (Azure AD) suporta operações de restauro de utilizadores a granel e suporta o descarregamento de listas de utilizadores, grupos e membros do grupo.

## <a name="understand-the-csv-template"></a>Compreenda o modelo CSV

Faça o download e preencha o modelo CSV para ajudá-lo a restaurar com sucesso os utilizadores AZure AD a granel. O modelo CSV que descarrega pode parecer este exemplo:

![Folha de cálculo para upload e chamadas explicando a finalidade e valores de cada linha e coluna](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>Estrutura de modelo de CSV

As linhas de um modelo de CSV descarregado são as seguintes:

- **Número da versão**: A primeira linha que contém o número da versão deve ser incluída no upload CSV.
- **Posições de coluna :** O formato das posições da coluna é &lt; *o nome do item* &gt; [Nome de Propriedade] &lt; *Obrigatório ou em branco* &gt; . Por exemplo, `Object ID [objectId] Required`. Algumas versões mais antigas do modelo podem ter ligeiras variações.
- **Linha exemplos**: Incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Deve remover os exemplos e substituí-lo pelas suas próprias entradas.

### <a name="additional-guidance"></a>Orientações adicionais

- As duas primeiras linhas do modelo de upload não devem ser removidas ou modificadas, ou o upload não pode ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos a adição de novas colunas ao modelo. Quaisquer colunas adicionais que adicionar são ignoradas e não processadas.
- Recomendamos que descarregue a versão mais recente do modelo CSV com a maior frequência possível.

## <a name="to-bulk-restore-users"></a>Para restaurar a granel os utilizadores

1. [Inscreva-se na sua organização Azure AD](https://aad.portal.azure.com) com uma conta que é administradora do Utilizador na organização Azure AD.
1. Em Azure AD, selecione **utilizadores**  >  **eliminados**.
1. Na página de **utilizadores eliminados,** selecione **Aparacção** a granel para carregar um ficheiro CSV válido das propriedades dos utilizadores para restaurar.

    ![Selecione o comando de restauro a granel na página de utilizadores eliminados](./media/users-bulk-restore/bulk-restore.png)

1. Abra o modelo CSV e adicione uma linha para cada utilizador que pretende restaurar. O único valor exigido é **o ObjectID.** Em seguida, guarde o ficheiro.

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="Selecione um ficheiro CSV local no qual lista os utilizadores que pretende adicionar":::

1. Na página de restauro a **granel,** no **upload do seu ficheiro csv,** navegue para o ficheiro. Quando seleciona o ficheiro e clica em **Enviar,** inicia-se a validação do ficheiro CSV.
1. Quando o conteúdo do ficheiro for validado, verá **o Ficheiro carregado com sucesso.** Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, **selecione Enviar** por terminação da operação a granel Azure que restaura os utilizadores.
1. Quando a operação de restauro terminar, verá uma notificação de que a operação a granel foi bem sucedida.

Se houver erros, pode descarregar e ver o ficheiro de resultados na página de resultados da **operação Em Massa.** O ficheiro contém a razão de cada erro.

## <a name="check-status"></a>Verificar o estado

Pode ver o estado de todos os seus pedidos em massa pendentes na página de resultados da **operação Em Massa.**

[![Verifique o estado na página resultados das operações a granel.](./media/users-bulk-restore/bulk-center.png)](./media/users-bulk-restore/bulk-center.png#lightbox)

Em seguida, pode verificar se os utilizadores que restaurou existem na organização Azure AD, quer no portal Azure, quer utilizando o PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Ver utilizadores restaurados no portal Azure

1. [Inscreva-se no centro de administração Azure AD](https://aad.portal.azure.com) com uma conta que é administrador do Utilizador na organização.
1. No painel de navegação, selecione **Azure Ative Directory**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Em **'Mostrar' (Em Exibição),** selecione **Todos os utilizadores** e verifique se os utilizadores que restaurou estão listados.

### <a name="view-users-with-powershell"></a>Ver utilizadores com PowerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Deve ver se os utilizadores que restaurou estão listados.

## <a name="next-steps"></a>Passos seguintes

- [Utilizadores de importação a granel](users-bulk-add.md)
- [Eliminar utilizadores em massa](users-bulk-delete.md)
- [Lista de utilizadores](users-bulk-download.md)
