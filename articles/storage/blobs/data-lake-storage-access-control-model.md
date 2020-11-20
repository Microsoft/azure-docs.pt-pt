---
title: Modelo de controlo de acesso para Azure Data Lake Storage Gen2 Microsoft Docs
description: Saiba como configurar o acesso ao contentor, diretório e ao nível de ficheiros em contas que tenham um espaço hierárquico de nomes.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/10/2020
ms.author: normesta
ms.openlocfilehash: 3ddcbe57112251a428e11d6c164cdb1224553f98
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959208"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Modelo de controlo de acesso em Azure Data Lake Storage Gen2

Data Lake Storage Gen2 suporta os seguintes mecanismos de autorização:

- Autorização de Chave Partilhada
- Autorização de assinatura de acesso partilhado (SAS)
- Controlo de acesso baseado em funções (Azure RBAC)
- Listas de controlo de acesso (ACL)

[A autorização da Shared Key e da SAS](#shared-key-and-shared-access-signature-sas-authorization) concede acesso a um utilizador (ou aplicação) sem que os exija ter uma identidade no Azure Ative Directory (Azure AD). Com estas duas formas de autenticação, os RBAC e acLs Azure não têm qualquer efeito.

A Azure RBAC e ACL exigem que o utilizador (ou aplicação) tenha uma identidade em Azure AD.  O Azure RBAC permite-lhe conceder acesso "grosso a dados da conta de armazenamento", tais como ler ou escrever acesso a **todos os** dados numa conta de armazenamento, enquanto os ACLs permitem conceder acesso "fino", como escrever acesso a um diretório ou ficheiro específico.  

Este artigo centra-se no Azure RBAC e ACLs, e como o sistema os avalia em conjunto para tomar decisões de autorização para recursos de conta de armazenamento.

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>Controlo de acesso baseado em funções (Azure RBAC)

A Azure RBAC utiliza atribuições de funções para aplicar conjuntos de permissões aos [princípios de segurança.](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) Um principal de segurança é um objeto que representa um utilizador, grupo, diretor de serviço ou identidade gerida que é definido no Azure Ative Directy (AD). Um conjunto de permissões pode dar a um diretor de segurança um nível de acesso "grosso",, como ler ou escrever acesso a **todos os** dados numa conta de armazenamento ou a **todos os** dados num recipiente. 

As seguintes funções permitem a um diretor de segurança aceder aos dados numa conta de armazenamento. 

|Função|Descrição|
|--|--|
| [Proprietário dos Dados do Armazenamento de Blobs](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) | Acesso total a recipientes e dados de armazenamento Blob. Este acesso permite ao titular de segurança definir um item ao proprietário e modificar os ACLs de todos os itens. |
| [Contribuinte de Dados do Armazenamento de Blobs](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) | Leia, escreva e elimine o acesso a recipientes de armazenamento Blob e bolhas. Este acesso não permite ao diretor de segurança definir a propriedade de um item, mas pode modificar a ACL de itens que são propriedade do responsável pela segurança. |
| [Leitor de Dados do Armazenamento de Blobs](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) | Leia e enuncie os recipientes de armazenamento Blob e as bolhas. |

Funções como [Proprietário,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) [Contribuinte,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) [Leitor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)e [Contribuinte de Conta de Armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) permitem a um responsável de segurança gerir uma conta de armazenamento, mas não fornecem acesso aos dados dentro dessa conta. No entanto, estas funções (excluindo o **Leitor)** podem obter acesso às chaves de armazenamento, que podem ser usadas em várias ferramentas do cliente para aceder aos dados.

## <a name="access-control-lists-acls"></a>Listas de controlo de acesso (ACL)

Os ACLs dão-lhe a capacidade de aplicar o nível de acesso "mais fino" a diretórios e ficheiros. *Um ACL* é uma construção de permissão que contém uma série de *entradas ACL*. Cada entrada da ACL associa o principal de segurança a um nível de acesso.  Para saber mais, consulte [as listas de controlo de acesso (ACLs) em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="how-permissions-are-evaluated"></a>Como as permissões são avaliadas

Durante a autorização principal de segurança, as permissões são avaliadas na seguinte ordem.

:um: &nbsp; &nbsp; As atribuições de funções Azure são avaliadas em primeiro lugar e têm prioridade sobre quaisquer atribuições da ACL.

:2: &nbsp; &nbsp; Se a operação for totalmente autorizada com base na atribuição de funções Azure, então os ACLs não são avaliados de todo.

:três: &nbsp; &nbsp; Se a operação não estiver totalmente autorizada, então os ACLs são avaliados.

> [!div class="mx-imgBorder"]
> ![fluxo de permissão de armazenamento de lago de dados](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

Devido à forma como as permissões de acesso são avaliadas pelo sistema, **não é possível** utilizar um ACL para **restringir** o acesso que já foi concedido por uma atribuição de funções. Isto porque o sistema avalia primeiro as atribuições de funções do Azure, e se a atribuição conceder permissão de acesso suficiente, os ACLs são ignorados. 

O diagrama a seguir mostra o fluxo de permissão para três operações comuns: listar conteúdos de diretórios, ler um ficheiro e escrever um ficheiro.

> [!div class="mx-imgBorder"]
> ![exemplo de fluxo de permissão de armazenamento de lago de dados](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>Tabela de permissões: Combinação de Azure RBAC e ACL

A tabela que se segue mostra-lhe como combinar funções Azure e entradas ACL para que um diretor de segurança possa executar as operações listadas na coluna **Operação.** Esta tabela mostra uma coluna que representa cada nível de uma hierarquia fictícia do diretório. Há uma coluna para o diretório de raiz do contentor , `/` um subdiretório chamado **Oregon**, um subdiretório do diretório do Oregon chamado **Portland,** e um ficheiro de texto no diretório de Portland chamado **Data.txt**. Aparecendo nessas colunas são representações de [forma curta](data-lake-storage-access-control.md#short-forms-for-permissions) da entrada ACL necessárias para conceder permissões. **N/A** (_Não aplicável_) aparece na coluna se não for necessária uma entrada ACL para realizar a operação.

|    Operação             | Papel RBAC atribuído               |    /        | Oregon/     | Portland/ | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Leia Data.txt            |   Proprietário dos Dados do Armazenamento de Blobs        | N/D      | N/D      | N/D       | N/D    |  
|                          |   Contribuinte de Dados do Armazenamento de Blobs  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Armazenamento de Blobs       | N/D      | N/D      | N/D       | N/D    |
|                          |   Nenhum                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Apêndice a Data.txt       |   Proprietário dos Dados do Armazenamento de Blobs        | N/D      | N/D      | N/D       | N/D    |
|                          |   Contribuinte de Dados do Armazenamento de Blobs  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Armazenamento de Blobs       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   Nenhum                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Apagar Data.txt          |   Proprietário dos Dados do Armazenamento de Blobs        | N/D      | N/D      | N/D       | N/D    |
|                          |   Contribuinte de Dados do Armazenamento de Blobs  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Armazenamento de Blobs       | `--X`    | `--X`    | `-WX`     | N/D    |
|                          |   Nenhum                           | `--X`    | `--X`    | `-WX`     | N/D    |
| Criar Data.txt          |   Proprietário dos Dados do Armazenamento de Blobs        | N/D      | N/D      | N/D       | N/D    |
|                          |   Contribuinte de Dados do Armazenamento de Blobs  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Armazenamento de Blobs       | `--X`    | `--X`    | `-WX`     | N/D    |
|                          |   Nenhum                           | `--X`    | `--X`    | `-WX`     | N/D    |
| Lista /                   |   Proprietário dos Dados do Armazenamento de Blobs        | N/D      | N/D      | N/D       | N/D    |
|                          |   Contribuinte de Dados do Armazenamento de Blobs  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Armazenamento de Blobs       | N/D      | N/D      | N/D       | N/D    |
|                          |   Nenhum                           | `R-X`    | N/D      | N/D       | N/D    |
| Lista /Oregon/            |   Proprietário dos Dados do Armazenamento de Blobs        | N/D      | N/D      | N/D       | N/D    |
|                          |   Contribuinte de Dados do Armazenamento de Blobs  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Armazenamento de Blobs       | N/D      | N/D      | N/D       | N/D    |
|                          |   Nenhum                           | `--X`    | `R-X`    | N/D       | N/D    |
| Lista /Oregon/Portland/   |   Proprietário dos Dados do Armazenamento de Blobs        | N/D      | N/D      | N/D       | N/D    |
|                          |   Contribuinte de Dados do Armazenamento de Blobs  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Armazenamento de Blobs       | N/D      | N/D      | N/D       | N/D    |
|                          |   Nenhum                           | `--X`    | `--X`    | `R-X`     | N/D    |


> [!NOTE] 
> Para visualizar o conteúdo de um contentor no Azure Storage Explorer, os princípios de segurança devem [inscrever-se no Storage Explorer utilizando a Azure AD](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#add-a-resource-via-azure-ad), e (no mínimo) ler o acesso (R--) à pasta raiz ( `\` ) de um recipiente. Este nível de permissão dá-lhes a capacidade de listar o conteúdo da pasta raiz. Se não quiser que o conteúdo da pasta raiz seja visível, pode atribuir-lhes o papel [de Leitor.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) Com esse papel, poderão listar os contentores na conta, mas não o conteúdo do contentor. Em seguida, pode conceder acesso a diretórios e ficheiros específicos utilizando ACLs.   

## <a name="security-groups"></a>Grupos de segurança

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Limites nas atribuições de funções Azure e entradas da ACL

Ao utilizar grupos, é menos provável que exceda o número máximo de atribuições de funções por subscrição e o número máximo de entradas ACL por ficheiro ou diretório. A tabela a seguir descreve estes limites.

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>Autorização de Assinatura de Chave Partilhada e Assinatura de Acesso Partilhado (SAS)

A Azure Data Lake Storage Gen2 também suporta métodos [de chave partilhada](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) e [SAS](https://docs.microsoft.com/azure/storage/common/storage-sas-overview?toc=/azure/storage/blobs/toc.json) para a autenticação. Uma característica destes métodos de autenticação é que nenhuma identidade está associada ao chamador e, portanto, a autorização baseada na autorização principal de segurança não pode ser realizada.

No caso da Chave Partilhada, o chamador obtém efetivamente acesso a 'super-utilizador', o que significa acesso total a todas as operações em todos os recursos, incluindo dados, definição do proprietário e alteração de ACLs.

Os tokens SAS incluem permissões permitidas como parte do token. As permissões incluídas no token SAS são efetivamente aplicadas a todas as decisões de autorização, mas não são realizados controlos adicionais da ACL.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as listas de controlo de acesso, consulte  [as listas de controlo de acesso (ACLs) em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

