---
title: Como garantir o acesso ao Catálogo de Dados Azure
description: Este artigo explica como garantir um catálogo de dados e os seus ativos de dados no Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b0972be2b8a6e05d3d90cde7354b4890ea95cbe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976768"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Como garantir o acesso ao catálogo de dados e aos ativos de dados

> [!IMPORTANT]
> Esta funcionalidade está disponível apenas na edição padrão do Catálogo de Dados Azure.

O Azure Data Catalog permite especificar quem pode aceder ao catálogo de dados e que operações (registar, anotar, tomar posse) podem realizar em metadados no catálogo. 

## <a name="catalog-users-and-permissions"></a>Utilizadores e permissões de catálogo

Para dar a um utilizador ou a um grupo o acesso a um catálogo de dados e definir permissões:

1. Na [página inicial do seu catálogo de dados,](https://www.azuredatacatalog.com)clique em **Definições** na barra de ferramentas.

   ![Botão de definições da página inicial do Catálogo de Dados Azure](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Na página de definições, expanda a secção Utilizadores do **Catálogo.**

   ![Utilizadores do Catálogo de Dados Azure Adicionar botão](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Clique em **Adicionar**.

4. Introduza o nome de **utilizador** ou nome do grupo de **segurança** totalmente qualificado no Diretório Ativo Azure (AAD) associado ao catálogo. Utilize a vírmula (''') como separador se estiver a adicionar mais do que um utilizador ou grupo.

   ![Utilizadores do Catálogo de Dados Azure - utilizadores ou grupos](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Pressione **ENTER** ou **TAB** fora da caixa de texto. 

6. Confirme que todas as permissões **(Annotate,** **Register,** e **Take Ownership**) são atribuídas a estes utilizadores ou grupos por padrão. Ou seja, o utilizador ou grupo pode registar ativos de [dados,]( data-catalog-how-to-register.md) [anotar ativos]( data-catalog-how-to-annotate.md)de dados, e [tomar posse de ativos de dados.]( data-catalog-how-to-manage.md) 

   ![Utilizadores do Catálogo de Dados Azure - permissões por defeito](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Para dar a um utilizador ou grupo apenas o acesso de leitura ao catálogo, limpe a opção de **anotação** para esse utilizador ou grupo. Quando o faz, o utilizador ou grupo não pode anotar os ativos de dados no catálogo, mas pode vê-los. 

8. Para negar a um utilizador ou grupo o registo de ativos de dados, ilibe a opção de **registo** desse utilizador ou grupo.

9. Para negar a um utilizador a sua propriedade de um ativo de dados, ilibe a opção **de apropriação** desse utilizador ou grupo. 

10. Para eliminar um utilizador/grupo dos utilizadores do catálogo, clique em **x** para o utilizador/grupo na parte inferior da lista. 

   ![Utilizadores do catálogo do Catálogo de Dados Azure - elimine o ícone do utilizador X](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Recomendamos que adicione grupos de segurança a utilizadores de catálogo em vez de adicionar diretamente aos utilizadores e atribuir permissões. Em seguida, adicione os utilizadores aos grupos de segurança que correspondam às suas funções e ao seu acesso necessário ao catálogo.

## <a name="special-considerations"></a>Considerações especiais

- As permissões atribuídas aos grupos de segurança são aditivas. Digamos, um utilizador está em dois grupos. Um grupo tem permissões anotadas e outro grupo não tem permissões anotadas. Em seguida, o utilizador tem permissões anotadas. 
- As permissões atribuídas explicitamente a um utilizador sobrepõem-se às permissões atribuídas aos grupos aos quais o utilizador pertence. No exemplo anterior, por exemplo, adicionou explicitamente o utilizador aos utilizadores do catálogo e não atribui permissões anótidas. O utilizador não pode anotar os ativos de dados, mesmo que o utilizador seja membro de um grupo que tenha permissões anotadas.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)
