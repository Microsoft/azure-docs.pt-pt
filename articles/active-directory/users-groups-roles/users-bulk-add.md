---
title: A granel cria utentes no portal de Diretório Ativo Azure [ Microsoft Docs
description: Adicione utilizadores a granel no centro de administração da Azure AD no Diretório Ativo Azure
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03f6e3d6edde51598b1d148469aceb1ff3b3d636
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203415"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Granel cria utilizadores no Diretório Ativo do Azure

O Azure Ative Directory (Azure AD) suporta a criação e eliminação de operações por parte do utilizador em massa e suporta o download de listas de utilizadores. Basta preencher o modelo de valores separados de vírem (CSV) que pode descarregar a partir do portal Azure AD.

## <a name="required-permissions"></a>Permissões obrigatórias

Para criar utilizadores em massa no portal da administração, deve ser inscrito como administrador global ou administrador de utilizador.

## <a name="understand-the-csv-template"></a>Compreenda o modelo CSV

Descarregue e preencha o modelo CSV de carregamento a granel para ajudá-lo a criar utilizadores de AD Azure a granel. O modelo CSV que descarrega pode parecer este exemplo:

![Folha de cálculo para upload e chamadas explicando o propósito e valores para cada linha e coluna](./media/users-bulk-add/create-template-example.png)

### <a name="csv-template-structure"></a>Estrutura do modelo CSV

As linhas num modelo CSV descarregado são as seguintes:

- **Número**da versão : A primeira linha que contém o número da versão deve ser incluída no CSV de carregamento.
- **Títulos da coluna**: O formato das rubricas da coluna é &lt;o nome&gt; &lt; *item* [PropertyName] *exigido ou em branco*&gt;. Por exemplo, `Name [displayName] Required`. Algumas versões mais antigas do modelo podem ter ligeiras variações.
- **Exemplos de linha**: Incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Deve remover a linha de exemplos e substituí-la pelas suas próprias entradas.

### <a name="additional-guidance"></a>Orientações adicionais

- As duas primeiras linhas do modelo de carregamento não devem ser removidas ou modificadas, ou o upload não pode ser processado.
- As colunas necessárias estão listadas primeiro.
- Não recomendamos adicionar novas colunas ao modelo. Quaisquer colunas adicionais que adicione são ignoradas e não processadas.
- Recomendamos que descarregue a versão mais recente do modelo CSV sempre que possível.

## <a name="to-create-users-in-bulk"></a>Para criar utilizadores a granel

1. [Inscreva-se na sua organização Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de utilizador na organização.
1. No Azure AD, selecione **Users** > **Bulk criar**.
1. Na página de criação de **utilizador a Granel,** selecione **Download** para receber um ficheiro de valores separados de vírpostas (CSV) válidos e, em seguida, adicione adicionar aos utilizadores que pretende criar.

   ![Selecione um ficheiro CSV local no qual lista os utilizadores que pretende adicionar](./media/users-bulk-add/upload-button.png)

1. Abra o ficheiro CSV e adicione uma linha para cada utilizador que pretende criar. Os únicos valores exigidos são **Nome,** **Nome principal do utilizador,** **palavra-passe inicial** e sinal de bloco **(Sim/Não)**. Em seguida, guarde o ficheiro.

   [![](media/users-bulk-add/add-csv-file.png "The CSV file contains names and IDs of the users to create")](media/users-bulk-add/add-csv-file.png#lightbox)

1. Na página **de criação de granel,** sob o upload do seu ficheiro CSV, navegue para o ficheiro. Quando selecionar o ficheiro e clicar **em Submeter,** a validação do ficheiro CSV começa.
1. Após a validação do conteúdo do ficheiro, verá o **Ficheiro carregado com sucesso**. Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, selecione **Submeter** para iniciar a operação a granel Do Azure que importa os novos utilizadores.
1. Quando a operação de importação estiver concluída, verá uma notificação do estado de trabalho da operação a granel.

Se houver erros, pode descarregar e ver o ficheiro de resultados na página de resultados da **operação Bulk.** O ficheiro contém a razão de cada erro.

## <a name="check-status"></a>Verificar o estado

Pode ver o estado de todos os seus pedidos a granel pendentes na página de resultados da **operação Bulk.**

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

Em seguida, pode verificar se os utilizadores que criou existem na organização Azure AD, quer no portal Azure, quer através da utilização do PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verifique os utilizadores no portal Azure

1. [Inscreva-se no centro de administração da Azure AD](https://aad.portal.azure.com) com uma conta que é administradora de Utilizador na organização.
1. No painel de navegação, selecione **Azure Ative Directory**.
1. Em **Gerir**, selecione **Utilizadores**.
1. No **Show**Programa , selecione **Todos os utilizadores** e verifique se os utilizadores que criou estão listados.

### <a name="verify-users-with-powershell"></a>Verifique os utilizadores com powerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Deve ver que os utilizadores que criou estão listados.

## <a name="bulk-import-service-limits"></a>Limites do serviço de importação a granel

Cada atividade a granel para criar utilizadores pode funcionar até uma hora. Isto permite a criação a granel de pelo menos 50.000 utilizadores.

## <a name="next-steps"></a>Passos seguintes

- [Eliminar utilizadores em massa](users-bulk-delete.md)
- [Lista de descarregamento de utilizadores](users-bulk-download.md)
- [Restaurar utilizadores em massa](users-bulk-restore.md)
