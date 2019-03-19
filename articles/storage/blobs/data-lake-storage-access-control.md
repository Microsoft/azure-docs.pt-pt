---
title: Descrição geral do controlo de acesso na geração 2 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Compreender como funciona o controlo de acesso na geração 2 de armazenamento do Azure Data Lake
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: 906b1dde3d145268df4fb1ff5c243c7daa8396ec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992432"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Controlo de acesso na geração 2 de armazenamento do Azure Data Lake

Geração de armazenamento 2 do Azure Data Lake implementa um modelo de controle de acesso que suporte o controlo de acesso com base do Azure funções (RBAC) e listas de controlo de acesso POSIX semelhante ao (ACLs). Este artigo resume as noções básicas do modelo de controle de acesso para a geração 2 de armazenamento do Data Lake. 

## <a name="azure-role-based-access-control-rbac"></a>Controlo de acesso baseado em função do Azure (RBAC)

Do Azure com base em função de controlo de acesso (RBAC) utiliza as atribuições de funções para aplicar efetivamente os conjuntos de permissões a utilizadores, grupos e principais de serviço para recursos do Azure. Normalmente, esses recursos do Azure estão restritos a recursos de nível superior (*por exemplo,*, contas de armazenamento do Azure). No caso do armazenamento do Azure e, consequentemente, geração 2 de armazenamento do Azure Data Lake, esse mecanismo foi expandido para o recurso de sistema de ficheiros.

Utilizar atribuições de funções do RBAC é um poderoso mecanismo para permissões de controlo de utilizador, mas é um mecanismo muito grosseiros refinado em relação ao ACLs. A granularidade mais pequena para o RBAC é ao nível do sistema de ficheiros e isso será avaliado com uma prioridade mais alta que as ACLs. Por conseguinte, se atribuir as permissões RBAC num sistema de ficheiros, esse utilizador ou principal de serviço terá essa autorização para todos os ficheiros e diretórios desse sistema de arquivos, independentemente das atribuições de ACL.

O armazenamento do Azure fornece três funções incorporadas do RBAC para o armazenamento de BLOBs: 

- [Proprietário de dados de Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview)
- [Contribuinte de dados de Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview)
- [Leitor de dados de Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview)

Quando um utilizador ou principal de serviço é concedido as permissões RBAC de dados por meio de uma destas funções incorporadas ou por meio de uma função personalizada, estas permissões são avaliadas pela primeira vez após a autorização de um pedido. Se a operação pedida é autorizada pelas atribuições de RBAC do chamador, em seguida, a autorização é resolvido imediatamente e não adicionais são executadas verificações ACL. Em alternativa, se o chamador não tem uma atribuição de RBAC ou operação do pedido não coincide com a permissão atribuída, em seguida, ACL são executadas verificações para determinar se o chamador está autorizado a efetuar a operação pedida.

Uma nota especial deve ser feita da função incorporada do proprietário de dados de Blob de armazenamento. Se o chamador tem esta atribuição de RBAC, então o utilizador é considerado um *Superutilizador* e a é concedido acesso total a todos os modificação de operações, incluindo a definir o proprietário de um diretório ou arquivo, bem como as ACLs para ficheiros e diretórios para o qual Não é o proprietário. O acesso de Superutilizador é a única maneira autorizada para alterar o proprietário de um recurso.

## <a name="shared-key-and-shared-access-signature-authentication"></a>Autenticação da assinatura de acesso partilhado de chave e partilhado

Geração de armazenamento 2 do Azure Data Lake oferece suporte a métodos de chave partilhada e assinatura de acesso partilhado para autenticação. Uma característica dos seguintes métodos de autenticação é que nenhuma identidade é associada ao chamador e, portanto, não é possível efetuar autorização com base em permissão do utilizador.

No caso de chave partilhada, o autor da chamada efetivamente obtém 'Superutilizador' acesso, que significa que o acesso total a todas as operações em todos os recursos, incluindo a configuração de proprietário e alterar ACLs.

SAS tokens incluem permissões concedidas como parte do token. As permissões incluídas no token de SAS com eficiência são aplicadas a todas as decisões de autorização, mas sem verificações ACL adicionais são realizadas.

## <a name="access-control-lists-on-files-and-directories"></a>Listas de controlo de acesso em arquivos e diretórios

Existem dois tipos de acesso (ACLs) de listas de controlo: aceder a ACL e ACLs padrão.

* **ACLs de acesso**: Acesso de controlo de ACLs de acesso a um objeto. Ficheiros e diretórios têm ACLs de acesso.

* **ACLs predefinidas**: Um modelo de ACLs associado com um diretório que determinam o ACLs de acesso para todos os itens subordinados que são criadas nesse diretório. Ficheiros não têm ACLs padrão.

Ambos ACLs de acesso e ACLs predefinidas têm a mesma estrutura.

> [!NOTE]
> Alterar a predefinição ACL num elemento principal não afetam o ACL de acesso ou default ACL de itens subordinados que já existem.

## <a name="permissions"></a>Permissões

As permissões num objeto de sistema de ficheiros são **leitura**, **escrever**, e **Execute**, e eles podem ser usados em arquivos e diretórios conforme mostrado na seguinte tabela:

|            |    Ficheiro     |   Diretório |
|------------|-------------|----------|
| **Leitura (R)** | Pode editar o conteúdo de um ficheiro | Requer **leitura** e **Execute** para listar o conteúdo do diretório |
| **Escrita (W)** | Pode escrever ou acrescentar a um ficheiro | Requer **escrever** e **Execute** para criar itens subordinados num diretório |
| **Execução (X)** | Não tem qualquer significado no contexto de geração 2 de armazenamento do Data Lake | É necessário para atravessar os itens subordinados de um diretório |

### <a name="short-forms-for-permissions"></a>Formatos curtos para as permissões

O **RWX** é utilizado para indicar **Leitura + Escrita + Execução**. Existe um formato numérico mais condensado, em que **Leitura=4**, **Escrita=2** e **Execução=1**, cuja respetiva soma representa as permissões. Abaixo, encontram-se alguns exemplos.

| Formato numérico | Formato curto |      O que representa     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Leitura + Escrita + Execução |
| 5            | `R-X`        | Leitura + Execução         |
| 4            | `R--`        | Leitura                   |
| 0            | `---`        | Sem permissões         |

### <a name="permissions-inheritance"></a>Herança de permissões

No modelo de estilo POSIX utilizado pelo geração 2 de armazenamento do Data Lake, as permissões para um item são armazenadas no próprio item. Em outras palavras, as permissões para um item não podem ser herdadas dos itens principais, se as permissões estão definidas depois do item subordinado já foi criado. As permissões são herdadas apenas se as permissões predefinidas foram definidas nos itens principal antes dos itens subordinados foram criados.

## <a name="common-scenarios-related-to-permissions"></a>Cenários comuns relacionados com as permissões

A tabela seguinte lista alguns cenários comuns para ajudar a compreender que permissões são necessárias para executar determinadas operações numa conta de geração 2 de armazenamento do Data Lake.

|    Operação             |    /    | Oregon / | Portland / | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Ler Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Acrescentar a Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Eliminar Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Criar Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Oregon/Portland /  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> Escreva não são necessárias permissões no ficheiro para eliminá-lo, desde que as duas condições anteriores sejam verdadeiras.
>
>

## <a name="users-and-identities"></a>Utilizadores e identidades

Todos os ficheiros e diretórios têm permissões diferentes para estas identidades:

- O utilizador proprietário
- O grupo proprietário
- Utilizadores nomeados
- Grupos nomeados
- Principais de serviço com nome
- Todos os outros utilizadores

As identidades dos utilizadores e grupos são identidades do Azure Active Directory (Azure AD). Portanto, salvo indicação em contrário, um *utilizador*, no contexto de geração 2 de armazenamento do Data Lake, pode fazer referência a um utilizador, grupo de segurança ou principal de serviço.

### <a name="the-owning-user"></a>O utilizador proprietário

O utilizador que criou o item é automaticamente o utilizador proprietário do item. Um utilizador proprietário pode:

* Alterar as permissões de um ficheiro que é propriedade.
* Alterar o grupo proprietário de um ficheiro que é propriedade, desde que o utilizador proprietário também seja membro do grupo de destino.

> [!NOTE]
> O utilizador proprietário *não é possível* alterar o utilizador proprietário de um ficheiro ou diretório. Apenas os superutilizadores podem alterar o utilizador proprietário de um ficheiro ou diretório.

### <a name="the-owning-group"></a>O grupo proprietário

Nas POSIX ACLs, cada utilizador está associado um *grupo primário*. Por exemplo, o utilizador "alice" poderá pertencer ao grupo "finanças". A Alice poderá, também, pertencer a vários grupos, mas um dos grupos será sempre o grupo principal dela. No POSIX, quando a Alice cria um ficheiro, o grupo proprietário do mesmo está definido como o grupo principal dela, que, neste caso, é "finanças". Caso contrário, o grupo proprietário tem um comportamento semelhante ao das permissões atribuídas para outros utilizadores/grupos.

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Atribuir o grupo proprietário para um novo ficheiro ou diretório

* **Caso 1**: O diretório de raiz "/". Este diretório é criado quando um sistema de ficheiros de geração 2 de armazenamento do Data Lake é criado. Neste caso, o grupo proprietário está definido para o utilizador que criou o sistema de ficheiros se foi feito com a OAuth. Se o sistema de ficheiros é criado usando a chave partilhada, uma SAS de conta ou uma SAS de serviço, em seguida, o proprietário e o grupo proprietário estão definidos como **$superuser**.
* **Caso 2** (todos os outros casos): Quando um novo item é criado, o grupo proprietário é copiado do diretório principal.

#### <a name="changing-the-owning-group"></a>Alterar o grupo proprietário

O grupo proprietário pode ser alterado por:
* Qualquer superutilizador.
* Pelo utilizador proprietário, se o utilizador proprietário também for membro do grupo de destino.

> [!NOTE]
> O grupo proprietário não é possível alterar as ACLs de um ficheiro ou diretório.  Enquanto o grupo proprietário estiver definido como o utilizador que criou a conta no caso do diretório de raiz **caso 1** acima, uma única conta de utilizador não é válida para fornecer permissões através do grupo proprietário. Pode atribuir esta permissão a um grupo de utilizadores válido, se aplicável.

## <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

O pseudocódigo seguinte representa o algoritmo de verificação de acesso para contas de geração 2 de armazenamento do Data Lake.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask IS NOT used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>A máscara

Conforme ilustrado no algoritmo de verificação de acesso, a máscara limita o acesso para utilizadores nomeados, o grupo proprietário e grupos nomeados.  

> [!NOTE]
> Um novo sistema de ficheiros de geração 2 de armazenamento do Data Lake, a máscara para aceder à ACL do diretório raiz ("/") está predefinida como 750 para diretórios e 640 para ficheiros. Ficheiros não receber o bit de X à medida que é irrelevante para arquivos num sistema de arquivo só.
>
> A máscara pode ser especificada numa base por chamada. Isso permite que diferentes sistemas de consumo, como clusters, ter diferentes máscaras eficaz para suas operações de arquivo. Se não for especificada uma máscara de numa determinada solicitação, substitui completamente a máscara de predefinição.

### <a name="the-sticky-bit"></a>O sticky bit

O sticky bit é uma funcionalidade mais avançada de um sistema de ficheiros POSIX. No contexto de geração 2 de armazenamento do Data Lake, é improvável que o sticky bit seja necessário. Em resumo, se o sticky bit estiver habilitado num diretório, um item subordinado só pode ser eliminado ou renomeado pelo utilizador de proprietário do item subordinado.

O sticky bit não é apresentado no portal do Azure.

## <a name="default-permissions-on-new-files-and-directories"></a>Permissões padrão em novos ficheiros e diretórios

Quando um novo ficheiro ou diretório é criado num diretório existente, a predefinição ACL no diretório principal determina:

- Um diretório filho acesso ACL e ACL predefinida.
- ACL de acesso do ficheiro subordinado (ficheiros não têm uma ACL predefinida).

### <a name="umask"></a>umask

Ao criar um ficheiro ou diretório, a umask é utilizada para modificar a forma como as ACLs padrão são definidas no item subordinado. umask é um valor de 9 bits nos diretórios de principal que contém um valor RWX **utilizador proprietário**, **grupo proprietário**, e **outros**.

A umask para o Azure Data Lake Storage Gen2 uma constante de valor ou seja definido como 007. Este valor se traduz em:

| componente de umask     | Formato numérico | Formato curto | Significado |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Para o utilizador proprietário, copie a ACL predefinida da principal para o acesso do menor ACL | 
| umask.owning_group  |    0         |   `---`      | Para o grupo proprietário, copie a ACL predefinida da principal para o acesso do menor ACL | 
| umask.other         |    7         |   `RWX`      | Para outros, remover todas as permissões no acesso do menor ACL |

O valor de umask utilizado pela geração 2 de armazenamento do Azure Data Lake com eficiência significa que o valor para **outros** nunca é transmitida por predefinição em novos crianças, independentemente do que o padrão que indica a ACL. 

O pseudocódigo a seguir mostra como é que a umask é aplicada ao criar as ACLs para um item subordinado.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Perguntas comuns sobre as ACLs no Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>É necessário ativar o suporte para as ACLs?

Não. Controlo de acesso através das ACLs está ativado para uma conta de geração 2 de armazenamento do Data Lake, desde que o hierárquica espaço de nomes (HNS) é a funcionalidade ativadas.

Se HNS estiver desativado, as regras de autorização RBAC do Azure ainda se aplicam.

### <a name="what-is-the-best-way-to-apply-acls"></a>O que é a melhor forma de aplicar ACLs?

Utilize sempre os grupos de segurança do Azure AD como o principal atribuído nas ACLs. Resista a oportunidade de atribuir diretamente a utilizadores individuais ou principais de serviço. Usar essa estrutura, poderá adicionar e remover utilizadores ou principais de serviço sem a necessidade de voltar a aplicar ACLs para uma estrutura de diretório inteiro. ) Em vez disso, simplesmente precisa adicionar ou remova-o adequado do grupo de segurança do Azure AD. Tenha em atenção que as ACLs não são herdadas e então reaplicando ACLs requer a atualização a ACL em todos os ficheiros e o subdiretório. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Que permissões são necessárias para eliminar recursivamente uma directory e o respetivo conteúdo?

- O chamador tem permissões de 'Superutilizador',

Ou

- O diretório principal deve ter escrita + execução permissões.
- O diretório da eliminar e cada diretório dentro da mesma, requer leitura + escrita + permissões de execução.

> [!NOTE]
> Não precisa de permissões de escrita para eliminar os ficheiros em diretórios. Além disso, o diretório de raiz "/" nunca pode ser eliminado.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Quem é o proprietário de um ficheiro ou diretório?

O criador de um ficheiro ou diretório se torna o proprietário. No caso do diretório de raiz, esta é a identidade do utilizador que criou o sistema de ficheiros.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Que grupo está definido como o grupo proprietário de um ficheiro ou diretório durante a criação?

O grupo proprietário é copiado do grupo proprietário do diretório principal sob a qual é criado o novo ficheiro ou diretório.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Sou o utilizador proprietário de um ficheiro, mas não tenho as permissões de RWX necessárias. O que posso fazer?

O utilizador proprietário pode alterar as permissões do ficheiro para atribuir as permissões de RWX necessárias a ele próprio.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Por que é, às vezes, vejo GUIDs nas ACLs?

Se a entrada representa um utilizador e que o utilizador não existe no Azure AD já, é apresentado um GUID. Normalmente, isto acontece quando o utilizador já não está na empresa ou se a conta dele tiver sido eliminada no Azure AD. Além disso, os principais de serviço e grupos de segurança não têm um utilizador nome Principal (UPN) para identificá-los e por isso, são representados pelo respetivo atributo OID (um guid). 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Geração 2 de armazenamento do Data Lake suporta a herança de ACLs?

Herdar as atribuições de RBAC do Azure. Fluxo de atribuições de subscrição, grupo de recursos e recursos da conta de armazenamento para baixo para o recurso de sistema de ficheiros.

Não herdam as ACLs. No entanto, as ACLs padrão pode ser utilizado para definir ACLs para subdiretórios de subordinados e arquivos criados sob o diretório principal. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Onde posso obter mais informações sobre o modelo de controlo de acesso POSIX?

* [POSIX Access Control Lists on Linux (Listas de Controlo de Acesso POSIX no Linux)](https://www.linux.com/news/posix-acls-linux)
* [HDFS permission guide (Guia de permissões do HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [FAQ DO POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL no Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL: using access control lists on Linux (ACL: utilizar listas de controlo de acesso no Linux)](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Consulte também

* [Descrição geral do Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
