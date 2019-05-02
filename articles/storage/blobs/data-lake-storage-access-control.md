---
title: Descrição geral do controlo de acesso na geração 2 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Compreender como funciona o controlo de acesso na geração 2 de armazenamento do Azure Data Lake
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 8fd73b1e0fcde6bcd69c7ce76b888d1adda37de4
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939556"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Controlo de acesso na geração 2 de armazenamento do Azure Data Lake

Geração de armazenamento 2 do Azure Data Lake implementa um modelo de controle de acesso que suporte o controlo de acesso baseado em função do Azure (RBAC) e listas de controlo de acesso POSIX semelhante ao (ACLs). Este artigo resume as noções básicas do modelo de controle de acesso para a geração 2 de armazenamento do Data Lake.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

RBAC utiliza as atribuições de funções para aplicar efetivamente os conjuntos de permissões para *entidades de segurança*. R *entidade de segurança* é um objeto que representa um utilizador, o grupo, o principal de serviço ou a identidade gerida que é definida no Azure Active Directory (AD) que está a pedir acesso aos recursos do Azure.

Normalmente, esses recursos do Azure estão restritos a recursos de nível superior (por exemplo: Contas de armazenamento do Azure). No caso do armazenamento do Azure e, consequentemente, geração 2 de armazenamento do Azure Data Lake, esse mecanismo foi expandido para o recurso de sistema de ficheiros.

Para saber como atribuir funções para principais de segurança no âmbito da sua conta de armazenamento, veja [autenticar o acesso ao Azure os blobs e filas com o Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>O impacto de atribuições de funções em listas de controlo de acesso de nível de ficheiro e de diretório

Utilizar atribuições de funções do RBAC é um poderoso mecanismo para controlar as permissões de acesso, mas é um mecanismo muito grosseiros refinado em relação ao ACLs. A granularidade mais pequena para o RBAC é ao nível do sistema de ficheiros e isso será avaliado com uma prioridade mais alta que as ACLs. Por conseguinte, se atribuir uma função a uma entidade de segurança no escopo de um sistema de ficheiros, essa entidade de segurança tem o nível de autorização associado com essa função para todos os ficheiros e diretórios desse sistema de arquivos, independentemente das atribuições de ACL.

Quando uma entidade de segurança é concedida permissões de dados RBAC por meio de um [função incorporada](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues), ou por meio de uma função personalizada, estas permissões são avaliadas pela primeira vez após a autorização de um pedido. Se a operação pedida é autorizada pelas atribuições de RBAC a entidade de segurança, em seguida, a autorização é resolvido imediatamente e não adicionais são executadas verificações ACL. Em alternativa, se a entidade de segurança não tem uma atribuição de RBAC ou operação do pedido não coincide com a permissão atribuída, em seguida, ACL são executadas verificações para determinar se a entidade de segurança está autorizada a efetuar a operação pedida.

> [!NOTE]
> Se a entidade de segurança tiver sido atribuída a [dados de armazenamento de BLOBs]() atribuição de função incorporada de proprietário, em seguida, a entidade de segurança é considerado um *Superutilizador* e é concedido acesso total a modificação de todos os operações, incluindo a definir o proprietário de um diretório ou arquivo, bem como as ACLs para ficheiros para o qual não é o proprietário e diretórios. O acesso de Superutilizador é a única maneira autorizada para alterar o proprietário de um recurso.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Autenticação de chave partilhada e assinatura de acesso partilhado (SAS)

Geração de armazenamento 2 do Azure Data Lake oferece suporte a métodos de chave partilhada e SAS para a autenticação. Uma característica dos seguintes métodos de autenticação é que nenhuma identidade é associada ao chamador e, portanto, não é possível efetuar autorização com base em permissão principal de segurança.

No caso de chave partilhada, o autor da chamada efetivamente obtém 'Superutilizador' acesso, que significa que o acesso total a todas as operações em todos os recursos, incluindo a configuração de proprietário e alterar ACLs.

SAS tokens incluem permissões concedidas como parte do token. As permissões incluídas no token de SAS com eficiência são aplicadas a todas as decisões de autorização, mas sem verificações ACL adicionais são realizadas.

## <a name="access-control-lists-on-files-and-directories"></a>Listas de controlo de acesso em arquivos e diretórios

Pode associar uma entidade de segurança com um nível de acesso de arquivos e diretórios. Essas associações são registradas num *lista de controlo de acesso (ACL)*. Cada ficheiro e de diretório na sua conta de armazenamento tem uma lista de controlo de acesso.

Se atribuído uma função a uma entidade de segurança ao nível armazenamento de conta, pode utilizar listas de controlo de acesso para conceder que esse principal de segurança elevados acesso a ficheiros específicos e diretórios.

Não é possível utilizar listas de controlo de acesso para fornecer um nível de acesso que é inferior um nível concedido por uma atribuição de função. Por exemplo, se atribuir o [contribuinte de dados de Blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview) apresenta uma lista de função para uma entidade, não pode utilizar o controlo de acesso de segurança para impedir que esse principal de segurança de escrever para um diretório.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Conjunto de arquivos e diretórios permissões ao nível através de listas de controlo de acesso

Para definir as permissões ao nível de ficheiro e de diretório, veja qualquer um dos seguintes artigos:

|Se pretender utilizar esta ferramenta:    |Veja este artigo:    |
|--------|-----------|
|Explorador do Storage do Azure    |[Definir permissões de nível de ficheiros e diretórios a utilizar o Explorador de armazenamento do Azure com a geração 2 de armazenamento do Azure Data Lake](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|API REST    |[Caminho - atualização](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Se a entidade de segurança é uma *serviço* principal, é importante utilizar ID de objeto do principal de serviço e não o ID de objeto do registo de aplicações relacionadas. Para obter o ID de objeto do principal de serviço abra a CLI do Azure e, em seguida, utilize este comando: `az ad sp show --id <Your App ID> --query objectId`. Certifique-se substituir o `<Your App ID>` marcador de posição pelo ID da aplicação de registo da aplicação.

### <a name="types-of-access-control-lists"></a>Tipos de listas de controlo de acesso

Existem dois tipos de listas de controlo de acesso: *ACLs de acesso* e *ACLs predefinidas*.

Acesso de controlo de ACLs de acesso a um objeto. Ficheiros e diretórios têm ACLs de acesso.

ACLs padrão são um modelo de ACLs associados a um diretório que determinam o ACLs de acesso para todos os itens subordinados que são criadas nesse diretório. Ficheiros não têm ACLs padrão.

Ambos ACLs de acesso e ACLs predefinidas têm a mesma estrutura.

Ambos ACLs de acesso e ACLs predefinidas têm a mesma estrutura.

> [!NOTE]
> Alterar a predefinição ACL num elemento principal não afetam o ACL de acesso ou default ACL de itens subordinados que já existem.

### <a name="levels-of-permission"></a>Níveis de permissão

As permissões num objeto de sistema de ficheiros são **leitura**, **escrever**, e **Execute**, e eles podem ser usados em arquivos e diretórios conforme mostrado na seguinte tabela:

|            |    Ficheiro     |   Diretório |
|------------|-------------|----------|
| **Leitura (R)** | Pode editar o conteúdo de um ficheiro | Requer **leitura** e **Execute** para listar o conteúdo do diretório |
| **Escrita (W)** | Pode escrever ou acrescentar a um ficheiro | Requer **escrever** e **Execute** para criar itens subordinados num diretório |
| **Execução (X)** | Não tem qualquer significado no contexto de geração 2 de armazenamento do Data Lake | É necessário para atravessar os itens subordinados de um diretório |

#### <a name="short-forms-for-permissions"></a>Formatos curtos para as permissões

O **RWX** é utilizado para indicar **Leitura + Escrita + Execução**. Existe um formato numérico mais condensado, em que **Leitura=4**, **Escrita=2** e **Execução=1**, cuja respetiva soma representa as permissões. Abaixo, encontram-se alguns exemplos.

| Formato numérico | Formato curto |      O que representa     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Leitura + Escrita + Execução |
| 5            | `R-X`        | Leitura + Execução         |
| 4            | `R--`        | Leitura                   |
| 0            | `---`        | Sem permissões         |

#### <a name="permissions-inheritance"></a>Herança de permissões

No modelo de estilo POSIX utilizado pelo geração 2 de armazenamento do Data Lake, as permissões para um item são armazenadas no próprio item. Em outras palavras, as permissões para um item não podem ser herdadas dos itens principais, se as permissões estão definidas depois do item subordinado já foi criado. As permissões são herdadas apenas se as permissões predefinidas foram definidas nos itens principal antes dos itens subordinados foram criados.

### <a name="common-scenarios-related-to-permissions"></a>Cenários comuns relacionados com as permissões

A tabela seguinte lista alguns cenários comuns para ajudar a compreender que permissões são necessárias para executar determinadas operações numa conta de armazenamento.

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

### <a name="users-and-identities"></a>Utilizadores e identidades

Todos os ficheiros e diretórios têm permissões diferentes para estas identidades:

- O utilizador proprietário
- O grupo proprietário
- Utilizadores nomeados
- Grupos nomeados
- Principais de serviço com nome
- Com o nome identidades geridas
- Todos os outros utilizadores

As identidades dos utilizadores e grupos são identidades do Azure Active Directory (Azure AD). Portanto, salvo indicação em contrário, um *utilizador*, no contexto de geração 2 de armazenamento do Data Lake, pode fazer referência a um utilizador do Azure AD, do serviço identidade principal, gerida ou grupo de segurança.

#### <a name="the-owning-user"></a>O utilizador proprietário

O utilizador que criou o item é automaticamente o utilizador proprietário do item. Um utilizador proprietário pode:

* Alterar as permissões de um ficheiro que é propriedade.
* Alterar o grupo proprietário de um ficheiro que é propriedade, desde que o utilizador proprietário também seja membro do grupo de destino.

> [!NOTE]
> O utilizador proprietário *não é possível* alterar o utilizador proprietário de um ficheiro ou diretório. Apenas os superutilizadores podem alterar o utilizador proprietário de um ficheiro ou diretório.

#### <a name="the-owning-group"></a>O grupo proprietário

Nas POSIX ACLs, cada utilizador está associado um *grupo primário*. Por exemplo, o utilizador "Alice" poderá pertencer ao grupo "Finanças". A Alice poderá, também, pertencer a vários grupos, mas um dos grupos será sempre o grupo principal dela. No POSIX, quando a Alice cria um ficheiro, o grupo proprietário do mesmo está definido como o grupo principal dela, que, neste caso, é "finanças". Caso contrário, o grupo proprietário tem um comportamento semelhante ao das permissões atribuídas para outros utilizadores/grupos.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Atribuir o grupo proprietário para um novo ficheiro ou diretório

* **Caso 1**: O diretório de raiz "/". Este diretório é criado quando um sistema de ficheiros de geração 2 de armazenamento do Data Lake é criado. Neste caso, o grupo proprietário está definido para o utilizador que criou o sistema de ficheiros se foi feito com a OAuth. Se o sistema de ficheiros é criado usando a chave partilhada, uma SAS de conta ou uma SAS de serviço, em seguida, o proprietário e o grupo proprietário estão definidos como **$superuser**.
* **Caso 2** (todos os outros casos): Quando um novo item é criado, o grupo proprietário é copiado do diretório principal.

##### <a name="changing-the-owning-group"></a>Alterar o grupo proprietário

O grupo proprietário pode ser alterado por:
* Qualquer superutilizador.
* Pelo utilizador proprietário, se o utilizador proprietário também for membro do grupo de destino.

> [!NOTE]
> O grupo proprietário não é possível alterar as ACLs de um ficheiro ou diretório.  Enquanto o grupo proprietário estiver definido como o utilizador que criou a conta no caso do diretório de raiz **caso 1** acima, uma única conta de utilizador não é válida para fornecer permissões através do grupo proprietário. Pode atribuir esta permissão a um grupo de utilizadores válido, se aplicável.

### <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

O pseudocódigo seguinte representa o algoritmo de verificação de acesso para contas de armazenamento.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
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

#### <a name="the-mask"></a>A máscara

Conforme ilustrado no algoritmo de verificação de acesso, a máscara limita o acesso para utilizadores nomeados, o grupo proprietário e grupos nomeados.  

> [!NOTE]
> Um novo sistema de ficheiros de geração 2 de armazenamento do Data Lake, a máscara para aceder à ACL do diretório raiz ("/") está predefinida como 750 para diretórios e 640 para ficheiros. Ficheiros não receber o bit de X à medida que é irrelevante para arquivos num sistema de arquivo só.
>
> A máscara pode ser especificada numa base por chamada. Isso permite que diferentes sistemas de consumo, como clusters, ter diferentes máscaras eficaz para suas operações de arquivo. Se não for especificada uma máscara de numa determinada solicitação, substitui completamente a máscara de predefinição.

#### <a name="the-sticky-bit"></a>O sticky bit

O sticky bit é uma funcionalidade mais avançada de um sistema de ficheiros POSIX. No contexto de geração 2 de armazenamento do Data Lake, é improvável que o sticky bit seja necessário. Em resumo, se o sticky bit estiver habilitado num diretório, um item subordinado só pode ser eliminado ou renomeado pelo utilizador de proprietário do item subordinado.

O sticky bit não é apresentado no portal do Azure.

### <a name="default-permissions-on-new-files-and-directories"></a>Permissões padrão em novos ficheiros e diretórios

Quando um novo ficheiro ou diretório é criado num diretório existente, a predefinição ACL no diretório principal determina:

- Um diretório filho acesso ACL e ACL predefinida.
- ACL de acesso do ficheiro subordinado (ficheiros não têm uma ACL predefinida).

#### <a name="umask"></a>umask

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

Não. Controlo de acesso através das ACLs está ativado para uma conta de armazenamento, desde que o hierárquica espaço de nomes (HNS) é a funcionalidade ativadas.

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

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Como posso definir ACLs corretamente para um serviço principal?

Quando define ACLs para principais de serviço, é importante usar o ID de objeto (OID) do *principal de serviço* para o registo da aplicação que criou. É importante observar que aplicações registadas tem um principal de serviço separado no específico inquilino do Azure AD. Aplicações registadas tem um OID que está visível no portal do Azure, mas a *principal de serviço* tem outra OID (diferente).

Para obter o OID para o principal de serviço que corresponde a um registo de aplicações, pode utilizar o `az ad sp show` comando. Especifique o ID da aplicação como o parâmetro. Eis um exemplo sobre como obter o OID para o principal de serviço que corresponde a um registo de aplicação com o ID da aplicação = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Execute o seguinte comando na CLI do Azure:

`az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>`

Quando tiver o OID correto para o principal de serviço, ir para o Explorador de armazenamento **gerir acesso** página para adicionar o OID e atribuir as permissões adequadas para o OID. Certifique-se de que seleciona **guardar**.

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
