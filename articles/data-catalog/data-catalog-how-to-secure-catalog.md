---
title: Como proteger o acesso ao catálogo de dados do Azure
description: Este artigo explica como proteger seus ativos de dados e o catálogo de dados.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: de67ca0c209f9a61b00c5451a27a1198d0de3724
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532916"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Como proteger o acesso ao catálogo de dados e recursos de dados
> [!IMPORTANT]
> Esta funcionalidade está disponível apenas na edição standard do catálogo de dados do Azure.

O catálogo de dados do Azure permite-lhe especificar quem pode aceder ao catálogo de dados e as operações (registar, anotar, assumir a propriedade) que podem realizar nos metadados no catálogo. 

## <a name="catalog-users-and-permissions"></a>Os utilizadores do catálogo e permissões
Para dar um utilizador ou um grupo o acesso a um catálogo de dados e definir permissões:

1. Sobre o [home page do seu catálogo de dados](https://www.azuredatacatalog.com), clique em **definições** na barra de ferramentas.

    ![catálogo de dados - definições](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. Na página Definições, expanda o **utilizadores do catálogo** secção.
    ![Catálogo utilizadores - adicionar](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Clique em **Adicionar**.
4. Introduza completamente qualificado **nome de utilizador** ou o nome da **grupo de segurança** no Azure Active Directory (AAD) associados com o catálogo. Utilize a vírgula (",") como um separador se estiver a adicionar mais do que um utilizador ou grupo.
    ![Utilizadores - os utilizadores ou grupos de catálogo](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. Prima **ENTER** ou **SEPARADOR** fora da caixa de texto. 
6.  Confirme que todas as permissões (**anotar**, **registar**, e **obter propriedade**) são atribuídos a esses utilizadores ou grupos, por predefinição. Ou seja, o utilizador ou grupo pode [registar recursos de dados]( data-catalog-how-to-register.md), [anotar recursos de dados]( data-catalog-how-to-annotate.md), e [assumir a propriedade de recursos de dados]( data-catalog-how-to-manage.md). 
    ![Utilizadores do catálogo - permissões predefinidas](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Para dar um utilizador ou grupo apenas o acesso de leitura para o catálogo, desmarque a **anotar** opção para esse utilizador ou grupo. Ao fazê-lo, o utilizador ou grupo não é possível anotar recursos de dados no catálogo, mas eles podem visualizá-los. 
8.  Para negar um utilizador ou grupo de recursos de dados a registar, desmarque a **registar** opção para esse utilizador ou grupo.
9.  Para negar um utilizador a partir de obter a propriedade de um recurso de dados, desmarque a **assumir a propriedade** opção para esse utilizador ou grupo. 
10. Para eliminar um utilizador/grupo de utilizadores do catálogo, clique em **x** para o utilizador/grupo na parte inferior da lista. 
    ![Os utilizadores do catálogo - eliminar utilizador](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > Recomendamos que adicione os grupos de segurança diretamente do catálogo utilizadores, em vez de adicionar utilizadores e atribuir permissões. Em seguida, adicione utilizadores aos grupos de segurança que correspondem a suas funções e o respetivo acesso necessário para o catálogo.

## <a name="special-considerations"></a>Considerações especiais

- As permissões atribuídas a grupos de segurança são cumulativas. Digamos, um utilizador estiver em dois grupos. Um grupo tem de anotar as permissões e outro grupo não têm a anotar as permissões. Em seguida, o utilizador tem anotar as permissões. 
- As permissões atribuídas explicitamente a um utilizador substituem as permissões atribuídas a grupos a que o utilizador pertence. No exemplo anterior, digamos, explicitamente adicionado ao utilizador para os utilizadores do catálogo e fazer não atribuir anotar as permissões. O utilizador não é possível anotar recursos de dados, mesmo que o utilizador é um membro de um grupo que tenha permissões de anotar.

## <a name="next-steps"></a>Passos Seguintes
- [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)

