---
title: Eliminar a granel os utilizadores no portal Azure Ative Directory | Microsoft Docs
description: Eliminar utilizadores a granel no centro de administração Azure em Azure Ative Directory
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
ms.openlocfilehash: b57d675003c1ebeb29927b86338f95cf7dd68090
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96574468"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Excluir a granel os utilizadores no Azure Ative Directory

Utilizando o portal Azure Ative Directory (Azure AD), pode remover um grande número de membros para um grupo utilizando um ficheiro de valores separados por vírgula (CSV) para eliminar em massa os utilizadores.

## <a name="understand-the-csv-template"></a>Compreenda o modelo CSV

Faça o download e preencha o modelo CSV para ajudá-lo a eliminar com sucesso os utilizadores AZure AD a granel. O modelo CSV que descarrega pode parecer este exemplo:

![Folha de cálculo para upload e chamadas explicando a finalidade e valores de cada linha e coluna](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>Estrutura de modelo de CSV

As linhas de um modelo de CSV descarregado são as seguintes:

- **Número da versão**: A primeira linha que contém o número da versão deve ser incluída no upload CSV.
- **Posições de coluna :** O formato das posições da coluna é &lt; *o nome do item* &gt; [Nome de Propriedade] &lt; *Obrigatório ou em branco* &gt; . Por exemplo, `User name [userPrincipalName] Required`. Algumas versões mais antigas do modelo podem ter ligeiras variações.
- **Linha exemplos**: Incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Deve remover os exemplos e substituí-lo pelas suas próprias entradas.

### <a name="additional-guidance"></a>Orientações adicionais

- As duas primeiras linhas do modelo de upload não devem ser removidas ou modificadas, ou o upload não pode ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos a adição de novas colunas ao modelo. Quaisquer colunas adicionais que adicionar são ignoradas e não processadas.
- Recomendamos que descarregue a versão mais recente do modelo CSV com a maior frequência possível.

## <a name="to-bulk-delete-users"></a>Eliminar em massa os utilizadores

1. [Inscreva-se na sua organização Azure AD](https://aad.portal.azure.com) com uma conta que é administrador do Utilizador na organização.
1. Em Azure AD, selecione **Utilizadores**  >  **Delete a granel**.
1. Na página de **utilizador de exclusão a granel,** selecione **Descarregue** para receber um ficheiro CSV válido das propriedades do utilizador.

   ![Selecione um ficheiro CSV local no qual lista os utilizadores que pretende eliminar](./media/users-bulk-delete/bulk-delete.png)

1. Abra o ficheiro CSV e adicione uma linha para cada utilizador que pretende eliminar. O único valor exigido é **o nome principal do utilizador.** Em seguida, guarde o ficheiro.

   ![O ficheiro CSV contém nomes e IDs dos utilizadores para eliminar](./media/users-bulk-delete/delete-csv-file.png)

1. Na página de **utilizador de exclusão a granel,** no **upload do seu ficheiro CSV,** navegue para o ficheiro. Quando seleciona o ficheiro e clica em submeter, inicia-se a validação do ficheiro CSV.
1. Quando o conteúdo do ficheiro for validado, verá **o Ficheiro carregado com sucesso.** Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, selecione Enviar por **terminação** da operação a granel Azure que elimina os utilizadores.
1. Quando a operação de eliminação estiver concluída, verá uma notificação de que a operação a granel foi bem sucedida.

Se houver erros, pode descarregar e ver o ficheiro de resultados na página de resultados da **operação Em Massa.** O ficheiro contém o motivo de cada erro.

## <a name="check-status"></a>Verificar o estado

Pode ver o estado de todos os seus pedidos em massa pendentes na página de resultados da **operação Em Massa.**

   [![Verifique o estado de eliminação na página Resultados das Operações em Massa.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

Em seguida, pode verificar se os utilizadores que eliminou existem na organização Azure AD, quer no portal Azure, quer utilizando o PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verifique os utilizadores eliminados no portal Azure

1. Inscreva-se no portal Azure com uma conta que é administrador do Utilizador na organização.
1. No painel de navegação, selecione **Azure Ative Directory**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Em **'Mostrar' (Em Exibição),** selecione **apenas todos os utilizadores** e verifique se os utilizadores que eliminou já não estão listados.

### <a name="verify-deleted-users-with-powershell"></a>Verificar utilizadores eliminados com PowerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Verifique se os utilizadores que eliminou já não estão listados.

## <a name="next-steps"></a>Passos seguintes

- [Utilizadores adicionados a granel](users-bulk-add.md)
- [Lista de utilizadores](users-bulk-download.md)
- [Restaurar utilizadores em massa](users-bulk-restore.md)
