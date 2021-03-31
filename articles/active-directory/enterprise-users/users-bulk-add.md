---
title: Criar a granel utilizadores no portal Azure Ative Directory | Microsoft Docs
description: Adicione utilizadores a granel no centro de administração Azure AD no Azure Ative Directory
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
ms.openlocfilehash: c653f3e8583ef3aadff26cb2b7a3266555d313a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547819"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>A granel cria utilizadores no Azure Ative Directory

O Azure Ative Directory (Azure AD) suporta o utilizador a granel a criar e eliminar operações e suporta o descarregamento de listas de utilizadores. Basta preencher o modelo de valores separados por vírgula (CSV) que pode descarregar a partir do portal AD AZure.

## <a name="required-permissions"></a>Permissões obrigatórias

Para criar em massa utilizadores no portal da administração, tem de ser inscrito como administrador global ou administrador do Utilizador.

## <a name="understand-the-csv-template"></a>Compreenda o modelo CSV

Faça o download e preencha o modelo de CSV de upload a granel para ajudá-lo a criar utilizadores AZure AD a granel. O modelo CSV que descarrega pode parecer este exemplo:

![Folha de cálculo para upload e chamadas explicando a finalidade e valores de cada linha e coluna](./media/users-bulk-add/create-template-example.png)

> [!WARNING]
> Se adicionar apenas uma entrada utilizando o modelo CSV, deve preservar a linha 3 e adicionar a sua nova entrada à linha 4.

### <a name="csv-template-structure"></a>Estrutura de modelo de CSV

As linhas de um modelo de CSV descarregado são as seguintes:

- **Número da versão**: A primeira linha que contém o número da versão deve ser incluída no upload CSV.
- **Posições de coluna :** O formato das posições da coluna é &lt; *o nome do item* &gt; [Nome de Propriedade] &lt; *Obrigatório ou em branco* &gt; . Por exemplo, `Name [displayName] Required`. Algumas versões mais antigas do modelo podem ter ligeiras variações.
- **Linha exemplos**: Incluímos no modelo uma linha de exemplos de valores aceitáveis para cada coluna. Deve remover os exemplos e substituí-lo pelas suas próprias entradas.

### <a name="additional-guidance"></a>Orientações adicionais

- As duas primeiras linhas do modelo de upload não devem ser removidas ou modificadas, ou o upload não pode ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos a adição de novas colunas ao modelo. Quaisquer colunas adicionais que adicionar são ignoradas e não processadas.
- Recomendamos que descarregue a versão mais recente do modelo CSV com a maior frequência possível.
- Certifique-se de que não existe um espaço branco não intencional antes/depois de qualquer campo. Para **o nome principal do utilizador,** ter tal espaço em branco causaria falha nas importações.

## <a name="to-create-users-in-bulk"></a>Para criar utilizadores a granel

1. [Inscreva-se na sua organização Azure AD](https://aad.portal.azure.com) com uma conta que é administrador do Utilizador na organização.
1. Em Azure AD, selecione **Utilizadores**  >  **A granel criar**.
1. Na página de utilizador da **Criação a Granel,** selecione **Descarregamento** para receber um ficheiro de valores separados de vírgula (CSV) válido das propriedades do utilizador e, em seguida, adicione utilizadores adicionais que pretende criar.

   ![Selecione um ficheiro CSV local no qual lista os utilizadores que pretende adicionar](./media/users-bulk-add/upload-button.png)

1. Abra o ficheiro CSV e adicione uma linha para cada utilizador que pretende criar. Os únicos valores necessários são **Nome,** **nome principal do utilizador,** **senha inicial** e **iniciar sação do Bloco (Sim/Não)**. Em seguida, guarde o ficheiro.

   [![O ficheiro CSV contém nomes e IDs dos utilizadores para criar](./media/users-bulk-add/add-csv-file.png)](./media/users-bulk-add/add-csv-file.png#lightbox)

1. Na página de utilizador da **Bulk create,** no Upload do seu ficheiro CSV, navegue para o ficheiro. Quando seleciona o ficheiro e clica em **Enviar,** inicia-se a validação do ficheiro CSV.
1. Depois de validado o conteúdo do ficheiro, verá **o Ficheiro carregado com sucesso.** Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
1. Quando o seu ficheiro passar a validação, **selecione Enviar** por terminação da operação a granel Azure que importa os novos utilizadores.
1. Quando a operação de importação estiver concluída, verá uma notificação do estado de trabalho da operação a granel.

Se houver erros, pode descarregar e ver o ficheiro de resultados na página de resultados da **operação Em Massa.** O ficheiro contém o motivo de cada erro. A submissão do ficheiro deve corresponder ao modelo fornecido e incluir os nomes exatos da coluna.

## <a name="check-status"></a>Verificar o estado

Pode ver o estado de todos os seus pedidos em massa pendentes na página de resultados da **operação Em Massa.**

   [![Verifique o estado de criação na página resultados das operações em massa](./media/users-bulk-add/bulk-center.png)](./media/users-bulk-add/bulk-center.png#lightbox)

Em seguida, pode verificar se os utilizadores que criou existem na organização Azure AD, quer no portal Azure, quer através da utilização do PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verifique os utilizadores no portal Azure

1. [Inscreva-se no centro de administração Azure AD](https://aad.portal.azure.com) com uma conta que é administrador do Utilizador na organização.
1. No painel de navegação, selecione **Azure Ative Directory**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Em **'Mostrar' (Em Exibição),** selecione **Todos os utilizadores** e verifique se os utilizadores que criou estão listados.

### <a name="verify-users-with-powershell"></a>Verificar utilizadores com PowerShell

Execute o seguinte comando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Deve ver se os utilizadores que criou estão listados.

## <a name="bulk-import-service-limits"></a>Limites de serviços de importação a granel

Cada atividade a granel para criar utilizadores pode funcionar até uma hora. Isto permite a criação em massa de pelo menos 50.000 utilizadores.

## <a name="next-steps"></a>Passos seguintes

- [Eliminar utilizadores em massa](users-bulk-delete.md)
- [Lista de utilizadores](users-bulk-download.md)
- [Restaurar utilizadores em massa](users-bulk-restore.md)
