---
title: Como proteger o acesso ao catálogo de dados do Azure
description: Este artigo explica como proteger um catálogo de dados e seus ativos de dados no catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b0972be2b8a6e05d3d90cde7354b4890ea95cbe4
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976768"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Como proteger o acesso ao catálogo de dados e aos ativos de dados

> [!IMPORTANT]
> Esse recurso está disponível apenas na edição Standard do catálogo de dados do Azure.

O catálogo de dados do Azure permite que você especifique quem pode acessar o catálogo de dados e quais operações (registrar, anotar, apropriar-se) podem ser executadas nos metadados no catálogo. 

## <a name="catalog-users-and-permissions"></a>Usuários e permissões do catálogo

Para conceder a um usuário ou grupo o acesso a um catálogo de dados e definir permissões:

1. Na [Home Page do catálogo de dados](https://www.azuredatacatalog.com), clique em **configurações** na barra de ferramentas.

   ![Botão Configurações de home page do catálogo de dados do Azure](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Na página configurações, expanda a seção **usuários do catálogo** .

   ![Botão Adicionar usuários do catálogo de dados do Azure](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Clique em **Adicionar**.

4. Insira o nome de **usuário** totalmente qualificado ou o nome do **grupo de segurança** no Azure Active Directory (AAD) associado ao catálogo. Use vírgula (', ') como um separador se você estiver adicionando mais de um usuário ou grupo.

   ![Usuários do catálogo de dados do Azure-usuários ou grupos](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Pressione **Enter** ou **Tab para** fora da caixa de texto. 

6. Confirme se todas as permissões(anotar, registrar e **apropriar** **-** se) estão atribuídas a esses usuários ou grupos por padrão. Ou seja, o usuário ou grupo pode [registrar ativos de dados]( data-catalog-how-to-register.md), [anotar ativos de dados]( data-catalog-how-to-annotate.md)e apropriar- [se de ativos de dados]( data-catalog-how-to-manage.md). 

   ![Usuários do catálogo de dados do Azure-permissões padrão](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Para conceder a um usuário ou grupo apenas o acesso de leitura para o catálogo, desmarque a opção anotar para esse usuário ou grupo. Quando você faz isso, o usuário ou grupo não pode anotar os ativos de dados no catálogo, mas eles podem exibi-los. 

8. Para negar que um usuário ou grupo Registre ativos de dados, desmarque a opção **registrar** para esse usuário ou grupo.

9. Para impedir que um usuário assuma a propriedade de um ativo de dados, desmarque a opção apropriar-se desse usuário ou grupo. 

10. Para excluir um usuário/grupo de usuários do catálogo, clique em **x** para o usuário/grupo na parte inferior da lista. 

   ![Usuários do catálogo do catálogo de dados do Azure – ícone Excluir usuário X](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Recomendamos que você adicione grupos de segurança para catalogar usuários em vez de adicionar usuários diretamente e atribuir permissões. Em seguida, adicione usuários aos grupos de segurança que correspondem às suas funções e seu acesso necessário ao catálogo.

## <a name="special-considerations"></a>Considerações especiais

- As permissões atribuídas a grupos de segurança são aditivas. Digamos que um usuário esteja em dois grupos. Um grupo tem permissões para anotar e outro grupo não tem permissões de anotações. Em seguida, o usuário tem permissões para anotar. 
- As permissões atribuídas explicitamente a um usuário substituem as permissões atribuídas a grupos aos quais o usuário pertence. No exemplo anterior, digamos que você adicionou explicitamente o usuário aos usuários do catálogo e não atribui permissões de anotações. O usuário não pode anotar ativos de dados mesmo que o usuário seja membro de um grupo que tenha permissões para anotar.

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)
