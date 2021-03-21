---
title: Como garantir o acesso ao Catálogo de Dados da Azure
description: Este artigo explica como proteger um catálogo de dados e os seus ativos de dados no Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 537254d7fb153e182be8ece13675a357a9a3e24e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674823"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Como garantir o acesso ao catálogo de dados e aos ativos de dados

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

> [!IMPORTANT]
> Esta funcionalidade está disponível apenas na edição padrão do Azure Data Catalog.

O Azure Data Catalog permite especificar quem pode aceder ao catálogo de dados e quais as operações (registar, anotar, tomar posse) que podem realizar em metadados no catálogo. 

## <a name="catalog-users-and-permissions"></a>Utilizadores e permissões de catálogo

Para dar a um utilizador ou a um grupo o acesso a um catálogo de dados e definir permissões:

1. Na [página inicial do seu catálogo de dados,](https://www.azuredatacatalog.com)clique em **Definições** na barra de ferramentas.

   ![Botão de definições de página inicial do Catálogo de Dados Azure](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Na página de definições, expanda a secção Utilizadores de **Catálogo.**

   ![Utilizadores do Catálogo de Dados Azure Adicionar botão](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Clique em **Adicionar**.

4. Introduza o nome ou nome de **utilizador** totalmente qualificado do grupo de **segurança** no Azure Ative Directory (AAD) associado ao catálogo. Utilize a vírgula (',') como separador se estiver a adicionar mais do que um utilizador ou grupo.

   ![Utilizadores do Catálogo de Dados Azure - utilizadores ou grupos](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Pressione **ENTER** ou **TAB** fora da caixa de texto. 

6. Confirme que todas as permissões **(Annotate**, **Register**, e **Take Ownership**) são atribuídas a estes utilizadores ou grupos por padrão. Ou seja, o utilizador ou grupo pode [registar ativos de dados]( data-catalog-how-to-register.md), anotar ativos de [dados,]( data-catalog-how-to-annotate.md)e [apropriar-se de ativos de dados]( data-catalog-how-to-manage.md). 

   ![Utilizadores do Catálogo de Dados Azure - permissões padrão](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Para dar a um utilizador ou grupo apenas o acesso de leitura ao catálogo, limpe a opção **anotada** para esse utilizador ou grupo. Quando o fizer, o utilizador ou grupo não pode anotar os ativos de dados no catálogo, mas eles podem vê-los. 

8. Para negar a um utilizador ou grupo o registo de ativos de dados, limpe a opção de **registo** para esse utilizador ou grupo.

9. Para negar a um utilizador a apropriação de um ativo de dados, limpe a opção de **apropriação** desse utilizador ou grupo. 

10. Para eliminar um utilizador/grupo dos utilizadores do catálogo, clique em **x** para o utilizador/grupo na parte inferior da lista. 

   ![Utilizadores do catálogo do catálogo Azure Data Catalog - apague o ícone do utilizador X](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Recomendamos que adicione grupos de segurança aos utilizadores catalogar em vez de adicionar diretamente os utilizadores e atribuir permissões. Em seguida, adicione os utilizadores aos grupos de segurança que correspondam às suas funções e ao seu acesso necessário ao catálogo.

## <a name="special-considerations"></a>Considerações especiais

- As permissões atribuídas aos grupos de segurança são adtivas. Digamos, um utilizador está em dois grupos. Um grupo tem permissões anotadas e outro grupo não tem permissões anotadas. Em seguida, o utilizador tem permissões anotadas. 
- As permissões atribuídas explicitamente a um utilizador substituem as permissões atribuídas a grupos a que o utilizador pertence. No exemplo anterior, por exemplo, adicionou explicitamente o utilizador ao catálogo de utilizadores e não atribui permissões anotadas. O utilizador não pode anotar os ativos de dados, mesmo que o utilizador seja membro de um grupo que tem permissões anotadas.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)
