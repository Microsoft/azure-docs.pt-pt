---
title: Descrição geral do controlo de acesso no Data Lake Storage Gen1 | Documentos da Microsoft
description: Compreender como funciona o controlo de acesso na geração 1 de armazenamento do Azure Data Lake
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 7c8553aed809290ea52fcb2e98fea48a30c109f6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539603"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Controlo de acesso na geração 1 de armazenamento do Azure Data Lake

Geração de armazenamento 1 do Azure Data Lake implementa um modelo de controle de acesso que deriva do HDFS, que por sua vez deriva de modelo de controlo de acesso POSIX. Este artigo resume as noções básicas do modelo de controle de acesso para a geração 1 de armazenamento do Data Lake. 

## <a name="access-control-lists-on-files-and-folders"></a>Listas de controlo de acesso em ficheiros e pastas

Existem dois tipos de listas de controlo de acesso (ACLs) – **ACLs de Acesso** e **ACLs Predefinidas**.

* **ACLs de acesso**: Controlam o acesso a um objeto. Os ficheiros e as pastas têm ACLs de Acesso.

* **ACLs predefinidas**: Um "modelo" de ACLs associado uma pasta que determinam as ACLs de acesso para todos os itens subordinados que são criados nessa pasta. Os ficheiros não possuem ACLs Predefinidas.


Tanto as ACLs de Acesso como as ACLs Predefinidas têm a mesma estrutura.



> [!NOTE]
> Alterar a ACL Predefinida num componente principal não afeta a ACL de Acesso ou a ACL Predefinida de itens subordinados que já existem.
>
>

## <a name="permissions"></a>Permissões

As permissões num objeto do sistema de ficheiros são **Leitura**, **Escrita** e **Execução** e podem ser utilizadas em ficheiros e pastas conforme mostrado na tabela seguinte:

|            |    Ficheiro     |   Pasta |
|------------|-------------|----------|
| **Leitura (R)** | Pode editar o conteúdo de um ficheiro | Requer **Leitura** e **Execução** para listar os conteúdos da pasta|
| **Escrita (W)** | Pode escrever ou acrescentar a um ficheiro | Requer **Escrita** e **Execução** para criar itens subordinados numa pasta |
| **Execução (X)** | Não tem qualquer significado no contexto de geração 1 de armazenamento do Data Lake | É necessário para atravessar os itens subordinados de uma pasta |

### <a name="short-forms-for-permissions"></a>Formatos curtos para as permissões

O **RWX** é utilizado para indicar **Leitura + Escrita + Execução**. Existe um formato numérico mais condensado, em que **Leitura=4**, **Escrita=2** e **Execução=1**, cuja respetiva soma representa as permissões. Abaixo, encontram-se alguns exemplos.

| Formato numérico | Formato curto |      O que representa     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Leitura + Escrita + Execução |
| 5            | `R-X`        | Leitura + Execução         |
| 4            | `R--`        | Leitura                   |
| 0            | `---`        | Sem permissões         |


### <a name="permissions-do-not-inherit"></a>As permissões não são herdadas

No modelo de estilo POSIX utilizado pelo Data Lake Storage Gen1, as permissões para um item são armazenadas no próprio item. Por outras palavras, as permissões para um item não podem ser herdadas dos itens principais.

## <a name="common-scenarios-related-to-permissions"></a>Cenários comuns relacionados com as permissões

Seguem-se alguns cenários comuns para ajudar a compreender que permissões são necessárias para executar determinadas operações numa conta de geração 1 de armazenamento do Data Lake.

| Operação | Object              |    /      | Seattle /   | Portland /   | Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| Leitura      | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Acrescentar a | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Eliminar    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Criar    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista      | /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista      | /Seattle/Portland /  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> As permissões de Escrita no ficheiro não são necessárias para o eliminar, desde que as duas condições anteriores sejam verdadeiras.
>
>


## <a name="users-and-identities"></a>Utilizadores e identidades

Todos os ficheiros e pastas têm permissões diferentes para estas identidades:

* O utilizador proprietário
* O grupo proprietário
* Utilizadores nomeados
* Grupos nomeados
* Todos os outros utilizadores

As identidades dos utilizadores e grupos são identidades do Azure Active Directory (Azure AD). Portanto, salvo indicação em contrário, um "utilizador", no contexto de geração 1 de armazenamento do Data Lake, pode significar um utilizador ou um grupo de segurança do Azure AD.

### <a name="the-super-user"></a>O superutilizador

Um Superutilizador tem mais direitos entre todos os utilizadores na conta de geração 1 de armazenamento do Data Lake. O superutilizador:

* Tem permissões de RWX para **todos** os ficheiros e pastas.
* Pode alterar as permissões em qualquer ficheiro ou pasta.
* Pode alterar o utilizador proprietário ou grupo proprietário de qualquer ficheiro ou pasta.

Todos os utilizadores que fazem parte do **proprietários** função para uma conta de geração 1 de armazenamento do Data Lake são automaticamente um Superutilizador.

### <a name="the-owning-user"></a>O utilizador proprietário

O utilizador que criou o item é automaticamente o utilizador proprietário do item. Um utilizador proprietário pode:

* Alterar as permissões de um ficheiro que é propriedade.
* Alterar o grupo proprietário de um ficheiro que é propriedade, desde que o utilizador proprietário também seja membro do grupo de destino.

> [!NOTE]
> O utilizador proprietário *não pode* alterar o utilizador proprietário de um ficheiro ou pasta. Apenas os superutilizadores podem alterar o utilizador proprietário de um ficheiro ou pasta.
>
>

### <a name="the-owning-group"></a>O grupo proprietário

**em segundo plano**

Nas ACLs POSIX, cada utilizador está associado um "grupo principal". Por exemplo, o utilizador "alice" poderá pertencer ao grupo "finanças". A Alice poderá, também, pertencer a vários grupos, mas um dos grupos será sempre o grupo principal dela. No POSIX, quando a Alice cria um ficheiro, o grupo proprietário do mesmo está definido como o grupo principal dela, que, neste caso, é "finanças". Caso contrário, o grupo proprietário tem um comportamento semelhante ao das permissões atribuídas para outros utilizadores/grupos.

Como não há nenhum "grupo principal" associado a utilizadores na geração 1 de armazenamento do Data Lake, o grupo proprietário é atribuído como abaixo.

**Atribuir o grupo proprietário para um novo ficheiro ou pasta**

* **Caso 1**: A pasta raiz "/". Esta pasta é criada quando é criada uma conta de geração 1 de armazenamento do Data Lake. Neste caso, o grupo proprietário está definido para um GUID de todos os de zero.  Este valor não permite que qualquer acesso.  É um marcador de posição até a hora num que grupo é atribuído.
* **Caso 2** (todos os outros casos): Quando um novo item é criado, o grupo proprietário é copiado da pasta principal.

**Alterar o grupo proprietário**

O grupo proprietário pode ser alterado por:
* Qualquer superutilizador.
* Pelo utilizador proprietário, se o utilizador proprietário também for membro do grupo de destino.

> [!NOTE]
> O grupo proprietário *não pode* alterar as ACLs de um ficheiro ou pasta.
>
> Para as contas criadas antes de Setembro de 2018, o grupo proprietário foi definido para o utilizador que criou a conta no caso da pasta de raiz para **caso 1**, acima.  Uma única conta de utilizador não é válida para fornecer permissões através do grupo proprietário, portanto, não existem permissões são concedidas por esta predefinição. Pode atribuir esta permissão a um grupo de utilizador válido.


## <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

O pseudocódigo seguinte representa o algoritmo de verificação de acesso para contas de geração 1 de armazenamento do Data Lake.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & e.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

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

Conforme ilustrado no algoritmo de verificação de acesso, a máscara limita o acesso para **utilizadores nomeados**, o **grupo proprietário**, e **grupos nomeados**.  

> [!NOTE]
> Para uma nova conta de geração 1 de armazenamento do Data Lake, a máscara da ACL de acesso da pasta raiz ("/") está predefinida como RWX.
>
>

### <a name="the-sticky-bit"></a>O sticky bit

O sticky bit é uma funcionalidade mais avançada de um sistema de ficheiros POSIX. No contexto de geração 1 de armazenamento do Data Lake, é improvável que o sticky bit seja necessário. Em resumo, se o sticky bit estiver habilitado numa pasta, um item subordinado só pode ser eliminado ou renomeado pelo utilizador de proprietário do item subordinado.

O sticky bit não é apresentado no portal do Azure.

## <a name="default-permissions-on-new-files-and-folders"></a>Permissões padrão em novos ficheiros e pastas

Quando um novo ficheiro ou pasta são criados numa pasta existente, a ACL Predefinida na pasta principal determina:

- A ACL Predefinida e a ACL de Acesso da pasta subordinada.
- A ACL de Acesso do ficheiro subordinado (os ficheiros não têm ACLs Predefinidas)

### <a name="umask"></a>umask

Ao criar um ficheiro ou pasta, a umask é utilizada para modificar a forma como as ACLs padrão são definidas no item subordinado. umask é um pouco de 9 um valor de 9 bits na pastas principais, que contém um valor RWX para **utilizador proprietário**, **grupo proprietário**, e **outros**.

A umask para o Azure Data Lake Storage Gen1 uma constante de valor ou seja definido como 007. Este valor se traduz em

| componente de umask     | Formato numérico | Formato curto | Significado |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Para o utilizador proprietário, copie a ACL predefinida da principal para a ACL de acesso do menor | 
| umask.owning_group  |    0         |   `---`      | Para o grupo proprietário, copie a ACL predefinida da principal para a ACL de acesso do menor | 
| umask.other         |    7         |   `RWX`      | Para outros, remover todas as permissões na ACL de acesso do menor |

O valor de umask utilizado pelo Gen1 de armazenamento do Azure Data Lake com eficiência significa que o valor para outros nunca é transmitido por predefinição em novos filhos - independentemente do que indica a ACL predefinida. 

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Perguntas comuns sobre as ACLs no Data Lake Storage Gen1

### <a name="do-i-have-to-enable-support-for-acls"></a>É necessário ativar o suporte para as ACLs?

Não. Controlo de acesso através das ACLs está sempre ativado para uma conta de geração 1 de armazenamento do Data Lake.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Que permissões são necessárias para eliminar recursivamente uma pasta e o respetivo conteúdo?

* A pasta principal tem de ter as permissões de **Escrita + Execução**.
* A pasta a eliminar, e cada pasta dentro da mesma, requer as permissões de **Leitura + Escrita + Execução**.

> [!NOTE]
> Não precisa das permissões de Escrita para eliminar ficheiros em pastas. Além disso, a pasta raiz "/" **nunca** pode ser eliminada.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Quem é o proprietário de um ficheiro ou pasta?

O criador de um ficheiro ou de uma pasta torna-se o proprietário.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Que grupo é definido como proprietário de um ficheiro ou pasta durante a criação?

O grupo proprietário é copiado do grupo proprietário da pasta principal sob a qual o novo ficheiro ou pasta é criado.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Sou o utilizador proprietário de um ficheiro, mas não tenho as permissões de RWX necessárias. O que posso fazer?

O utilizador proprietário pode alterar as permissões do ficheiro para atribuir as permissões de RWX necessárias a ele próprio.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Quando vejo as ACLs no portal do Azure, vejo os nomes de utilizador, mas pelas APIs vejo GUIDs. Por que motivo é que isto acontece?

As entradas nas ACLs são armazenadas como GUIDs que correspondem aos utilizadores no Azure AD. As APIs devolvem os GUIDs conforme estão. O portal do Azure tenta tornar as ACLs mais fáceis de utilizar, ao traduzir os GUIDs para nomes amigáveis sempre que possível.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Porque é que, por vezes, vejo GUIDs nas ACLs quando utilizo o portal do Azure?

Quando um utilizador deixa de existir no Azure AD, é apresentado um GUID. Normalmente, isto acontece quando o utilizador já não está na empresa ou se a conta dele tiver sido eliminada no Azure AD.

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Geração 1 de armazenamento do Data Lake suporta a herança de ACLs?

Não, mas as ACLs Predefinidas podem ser utilizadas para definir ACLs para ficheiros e pastas subordinados criados recentemente na pasta principal.  

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

* [Descrição geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
