---
title: Visão geral do controlo de acessos na Data Lake Storage Gen1 Microsoft Docs
description: Conheça os fundamentos do modelo de controlo de acessos da Azure Data Lake Storage Gen1, que deriva do HDFS.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: d889c82142cda60b920f7b29bd91755cbc34f525
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701454"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Controlo de acesso no Azure Data Lake Storage Gen1

A Azure Data Lake Storage Gen1 implementa um modelo de controlo de acesso que deriva do HDFS, que por sua vez deriva do modelo de controlo de acesso POSIX. Este artigo resume os fundamentos do modelo de controlo de acesso para data lake storage gen1. 

## <a name="access-control-lists-on-files-and-folders"></a>Listas de controlo de acesso em ficheiros e pastas

Existem dois tipos de listas de controlo de acesso (ACLs) – **ACLs de Acesso** e **ACLs Predefinidas**.

* **ACLs de Acesso**: controlam o acesso a um objeto. Os ficheiros e as pastas têm ACLs de Acesso.

* **ACLs Predefinidas**: um "modelo" de ACLs associado a uma pasta que determinam as ACLs de Acesso para todos os itens subordinados que são criados nessa pasta. Os ficheiros não possuem ACLs Predefinidas.


Tanto as ACLs de Acesso como as ACLs Predefinidas têm a mesma estrutura.



> [!NOTE]
> Alterar a ACL Predefinida num componente principal não afeta a ACL de Acesso ou a ACL Predefinida de itens subordinados que já existem.
>
>

## <a name="permissions"></a>Permissões

As permissões num objeto do sistema de ficheiros são **Leitura**, **Escrita** e **Execução** e podem ser utilizadas em ficheiros e pastas conforme mostrado na tabela seguinte:

|            |    Ficheiro     |   Pasta |
|------------|-------------|----------|
| **Leitura (R)** | Pode editar o conteúdo de um ficheiro | Requer **ler** e **executar** para listar o conteúdo da pasta|
| **Escrita (W)** | Pode escrever ou acrescentar a um ficheiro | Requer **Escrita** e **Execução** para criar itens subordinados numa pasta |
| **Execução (X)** | Não significa nada no contexto do Data Lake Storage Gen1 | É necessário para atravessar os itens subordinados de uma pasta |

### <a name="short-forms-for-permissions"></a>Formatos curtos para as permissões

O **RWX** é utilizado para indicar **Leitura + Escrita + Execução**. Existe um formato numérico mais condensado, em que **Leitura=4**, **Escrita=2** e **Execução=1**, cuja respetiva soma representa as permissões. Abaixo, encontram-se alguns exemplos.

| Formato numérico | Formato curto |      O que representa     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Leitura + Escrita + Execução |
| 5            | `R-X`        | Leitura + Execução         |
| 4            | `R--`        | Ler                   |
| 0            | `---`        | Sem permissões         |


### <a name="permissions-do-not-inherit"></a>As permissões não são herdadas

No modelo de estilo POSIX que é usado pela Data Lake Storage Gen1, as permissões para um item são armazenadas no próprio item. Por outras palavras, as permissões para um item não podem ser herdadas dos itens principais.

## <a name="common-scenarios-related-to-permissions"></a>Cenários comuns relacionados com as permissões

Seguem-se alguns cenários comuns para o ajudar a compreender quais as permissões necessárias para realizar determinadas operações numa conta da Data Lake Storage Gen1.

| Operação | Objeto              |    /      | Seattle/   | Portland/   | Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| Ler      | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Apêndice para | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Eliminar    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Criar    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista      | /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista      | /Seattle/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


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

As identidades dos utilizadores e grupos são identidades do Azure Active Directory (Azure AD). Assim, salvo indicação em contrário, um "utilizador", no contexto da Data Lake Storage Gen1, pode significar um utilizador AD Azure ou um grupo de segurança Azure AD.

### <a name="the-super-user"></a>O superutilizador

Um super utilizador tem mais direitos de todos os utilizadores na conta Da Gen1 de Armazenamento de Data Lake. O superutilizador:

* Tem permissões de RWX para **todos** os ficheiros e pastas.
* Pode alterar as permissões em qualquer ficheiro ou pasta.
* Pode alterar o utilizador proprietário ou grupo proprietário de qualquer ficheiro ou pasta.

Todos os utilizadores que fazem parte da função **proprietários** para uma conta Gen1 de armazenamento de dados são automaticamente um super utilizador.

### <a name="the-owning-user"></a>O utilizador proprietário

O utilizador que criou o item é automaticamente o utilizador proprietário do item. Um utilizador proprietário pode:

* Alterar as permissões de um ficheiro que é propriedade.
* Alterar o grupo proprietário de um ficheiro que é propriedade, desde que o utilizador proprietário também seja membro do grupo de destino.

> [!NOTE]
> O utilizador proprietário *não pode* alterar o utilizador proprietário de um ficheiro ou pasta. Apenas os superutilizadores podem alterar o utilizador proprietário de um ficheiro ou pasta.
>
>

### <a name="the-owning-group"></a>O grupo proprietário

**Fundo**

Nas ACLs POSIX, cada utilizador está associado um "grupo principal". Por exemplo, o utilizador "alice" poderá pertencer ao grupo "finanças". A Alice poderá, também, pertencer a vários grupos, mas um dos grupos será sempre o grupo principal dela. No POSIX, quando a Alice cria um ficheiro, o grupo proprietário do mesmo está definido como o grupo principal dela, que, neste caso, é "finanças". Caso contrário, o grupo proprietário tem um comportamento semelhante ao das permissões atribuídas para outros utilizadores/grupos.

Como não existe um "grupo primário" associado aos utilizadores na Data Lake Storage Gen1, o grupo de propriedade é atribuído como abaixo.

**Atribuindo o grupo de propriedade para um novo arquivo ou pasta**

* **Caso 1**: a pasta raiz "/". Esta pasta é criada quando uma conta Gen1 de armazenamento de datas é criada. Neste caso, o grupo de propriedade está definido para um GUID totalmente zero.  Este valor não permite qualquer acesso.  É um espaço reservado até que um grupo seja atribuído.
* **Caso 2** (todos os outros casos): quando é criado um item novo, o grupo proprietário é copiado da pasta principal.

**Mudar o grupo de propriedade**

O grupo proprietário pode ser alterado por:
* Qualquer superutilizador.
* Pelo utilizador proprietário, se o utilizador proprietário também for membro do grupo de destino.

> [!NOTE]
> O grupo proprietário *não pode* alterar as ACLs de um ficheiro ou pasta.
>
> Para contas criadas em ou antes de setembro de 2018, o grupo de propriedade foi definido para o utilizador que criou a conta no caso da pasta raiz para o **Caso 1**, acima.  Uma única conta de utilizador não é válida para fornecer permissões através do grupo próprio, pelo que não são concedidas permissões por esta definição predefinição. Pode atribuir esta permissão a um grupo de utilizador válido.


## <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

O pseudocódigo que se segue representa o algoritmo de verificação de acesso para contas Gen1 de armazenamento de dados.

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
      return ( (desired_perms & entry.permissions) == desired_perms )

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

Conforme ilustrado no Algoritmo de Verificação de Acesso, a máscara limita o acesso aos **utilizadores nomeados**, o **grupo de propriedade**, e **grupos nomeados**.  

> [!NOTE]
> Para uma nova conta Gen1 de armazenamento de dados, a máscara para o Access ACL da pasta raiz ("/") é padrão para RWX.
>
>

### <a name="the-sticky-bit"></a>O sticky bit

O sticky bit é uma funcionalidade mais avançada de um sistema de ficheiros POSIX. No contexto da Data Lake Storage Gen1, é improvável que a parte pegajosa seja necessária. Em resumo, se a broca pegajosa estiver ativada numa pasta, um item para criança só pode ser eliminado ou renomeado pelo utilizador próprio do artigo da criança.

O sticky bit não é apresentado no portal do Azure.

## <a name="default-permissions-on-new-files-and-folders"></a>Permissões predefinidos em novos ficheiros e pastas

Quando um novo ficheiro ou pasta são criados numa pasta existente, a ACL Predefinida na pasta principal determina:

- A ACL Predefinida e a ACL de Acesso da pasta subordinada.
- A ACL de Acesso do ficheiro subordinado (os ficheiros não têm ACLs Predefinidas)

### <a name="umask"></a>umask

Ao criar um ficheiro ou pasta, umak é usada para modificar a forma como os ACLs predefinidos são definidos no item da criança. umask é um valor de 9 bits em pastas-mãe que contém um valor RWX para **o utilizador próprio,** **grupo próprio,** e **outros**.

A umask para Azure Data Lake Storage Gen1 é um valor constante definido para 007. Este valor traduz-se para

| componente umask     | Formato numérico | Formato curto | Significado |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Para possuir o utilizador, copie o ACL padrão do progenitor para o Acesso da criança ACL | 
| umask.owning_group  |    0         |   `---`      | Para possuir o grupo, copie o ACL padrão do progenitor para o Acesso da criança ACL | 
| umask.outro         |    7         |   `RWX`      | Para outro, remova todas as permissões no Access ACL da criança |

O valor de umask usado pelo Azure Data Lake Storage Gen1 significa efetivamente que o valor para os outros nunca é transmitido por padrão em novas crianças - independentemente do que a ACL padrão indica. 

O pseudocódigo que se segue mostra como a umask é aplicada ao criar os ACLs para um item infantil.

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Perguntas comuns sobre ACLs em Data Lake Storage Gen1

### <a name="do-i-have-to-enable-support-for-acls"></a>É necessário ativar o suporte para as ACLs?

Não. O controlo de acesso via ACLs está sempre ligado para uma conta Gen1 de armazenamento de data lake.

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

Quando um utilizador deixa de existir no Azure AD, é apresentado um GUID. Normalmente, isto acontece quando o utilizador já não está na empresa ou se a conta dele tiver sido eliminada no Azure AD. Além disso, certifique-se de que está a utilizar o ID certo para configurar ACLs (detalhes em questão abaixo).

### <a name="when-using-service-principal-what-id-should-i-use-to-set-acls"></a>Ao utilizar o principal de serviço, que ID devo usar para definir ACLs?

No Portal Azure, aceda às **aplicações Azure Ative Directory -> Enterprise** e selecione a sua aplicação. O **separador Visão** Geral deve apresentar um ID de objeto e é isso que deve ser utilizado ao adicionar ACLs para acesso a dados (e não Id de aplicação).

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>O Data Lake Storage Gen1 suporta a herança dos ACLs?

Não, mas as ACLs Predefinidas podem ser utilizadas para definir ACLs para ficheiros e pastas subordinados criados recentemente na pasta principal.  

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Onde posso obter mais informações sobre o modelo de controlo de acesso POSIX?

* [POSIX Access Control Lists on Linux (Listas de Controlo de Acesso POSIX no Linux)](https://www.linux.com/news/posix-acls-linux)
* [HDFS permission guide (Guia de permissões do HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [FAQ DO POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL no Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL usando listas de controlo de acesso em Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Ver também

* [Visão geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
