---
title: A granel restaure utilizadores apagados no portal de Diretório Ativo Azure [ Microsoft Docs
description: Restaurar utilizadores apagados a granel no centro de administração da AD Azure em Azure Ative Directory
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
ms.openlocfilehash: 11f35c7615135f5aa6c63d5d05898d139df61d0d
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203313"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>A granel restaura utilizadores eliminados no Diretório Ativo do Azure

O Azure Ative Directory (Azure AD) suporta operações de restauro de utilizadores a granel e suporta o descarregamento de listas de utilizadores, grupos e membros do grupo.

## <a name="understand-the-csv-template"></a>Compreenda o modelo CSV

Descarregue e preencha o modelo CSV para ajudá-lo a restaurar com sucesso os utilizadores de AD Azure a granel. O modelo CSV que descarrega pode parecer este exemplo:

![Folha de cálculo para upload e chamadas explicando o propósito e valores para cada linha e coluna](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas num modelo CSV descarregado são as seguintes:

- **Número**da versão : A primeira linha que contém o número da versão deve ser incluída no CSV de carregamento.
- **Títulos da coluna**: O formato das rubricas da coluna é &lt;o nome&gt; &lt; *item* [PropertyName] *exigido ou em branco*&gt;. Por exemplo, `Object ID [objectId] Required`. Algumas versões mais antigas do modelo podem ter ligeiras variações.
- **Exemplos de linha**: Incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Deve remover a linha de exemplos e substituí-la pelas suas próprias entradas.

### <a name="additional-guidance"></a>Orientações adicionais

- As duas primeiras linhas do modelo de carregamento não devem ser removidas ou modificadas, ou o upload não pode ser processado.
- As colunas necessárias estão listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Quaisquer colunas adicionais que adicione são ignoradas e não processadas.
- Recomendamos que descarregue a versão mais recente do modelo CSV sempre que possível.

## <a name="to-bulk-restore-users"></a>Para restaurar a granel os utilizadores

1. [Inscreva-se na sua organização Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de utilizador na organização Azure AD.
1. No Anúncio Azure, selecione **Utilizadores Apagados** > **Deleted**.
1. Na página **de utilizadores Eliminados,** selecione **Restaurar a Granel** para carregar um ficheiro CSV válido de propriedades dos utilizadores para restaurar.

   ![Selecione o comando de restauro a granel na página de utilizadores Eliminados](./media/users-bulk-restore/bulk-restore.png)

1. Abra o modelo CSV e adicione uma linha para cada utilizador que pretende restaurar. O único valor exigido é **objectID**. Em seguida, guarde o ficheiro.

   ![Selecione um ficheiro CSV local no qual lista os utilizadores que pretende adicionar](./media/users-bulk-restore/upload-button.png)

1. Na página **de restauro a granel,** sob **o upload do seu ficheiro CSV,** navegue para o ficheiro. Quando selecionar o ficheiro e clicar **em Submeter,** a validação do ficheiro CSV começa.
1. Quando o conteúdo do ficheiro for validado, verá o **Ficheiro carregado com sucesso**. Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, selecione **Enviar** para iniciar a operação a granel Azure que restaura os utilizadores.
1. Quando a operação de restauro terminar, verá uma notificação de que a operação a granel foi bem sucedida.

Se houver erros, pode descarregar e ver o ficheiro de resultados na página de resultados da **operação Bulk.** O ficheiro contém a razão de cada erro.

## <a name="check-status"></a>Verificar o estado

Pode ver o estado de todos os seus pedidos a granel pendentes na página de resultados da **operação Bulk.**

[![](media/users-bulk-restore/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-restore/bulk-center.png#lightbox)

Em seguida, pode verificar se os utilizadores restaurados existem na organização Azure AD, quer no portal Azure, quer através da utilização do PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Veja os utilizadores restaurados no portal Azure

1. [Inscreva-se no centro de administração da Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de Utilizador na organização.
1. No painel de navegação, selecione **Azure Ative Directory**.
1. Em **Gerir**, selecione **Utilizadores**.
1. No **Show**Programa , selecione **Todos os utilizadores** e verifique se os utilizadores restaurados estão listados.

### <a name="view-users-with-powershell"></a>Ver utilizadores com PowerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Deve ver que os utilizadores que restaurou estão listados.

## <a name="next-steps"></a>Passos seguintes

- [Utilizadores de importação a granel](users-bulk-add.md)
- [Eliminar utilizadores em massa](users-bulk-delete.md)
- [Lista de descarregamento de utilizadores](users-bulk-download.md)
