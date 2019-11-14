---
title: Visão geral do controle de acesso no Azure Data Lake Storage Gen2 | Microsoft Docs
description: Entenda como o controle de acesso funciona no Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: a35cf935d990dbb61f440d2592d59d21f33a2ae8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037231"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Controle de acesso no Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementa um modelo de controle de acesso que dá suporte ao RBAC (controle de acesso baseado em função) do Azure e às ACLs (listas de controle de acesso) semelhantes a POSIX. Este artigo resume os conceitos básicos do modelo de controle de acesso para Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O RBAC usa atribuições de função para aplicar efetivamente conjuntos de permissões a *entidades de segurança*. Uma *entidade de segurança* é um objeto que representa um usuário, grupo, entidade de serviço ou identidade gerenciada que é definida no Azure Active Directory (AD) que está solicitando acesso aos recursos do Azure.

Normalmente, esses recursos do Azure são restritos a recursos de nível superior (por exemplo: contas de armazenamento do Azure). No caso do armazenamento do Azure e, consequentemente, Azure Data Lake Storage Gen2, esse mecanismo foi estendido para o recurso de contêiner (sistema de arquivos).

Para saber como atribuir funções a entidades de segurança no escopo da sua conta de armazenamento, consulte [conceder acesso ao blob do Azure e dados de fila com RBAC no portal do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>O impacto das atribuições de função em listas de controle de acesso de nível de arquivo e diretório

Embora o uso de atribuições de função RBAC seja um mecanismo poderoso para controlar as permissões de acesso, trata-se de um mecanismo muito mais esparso em relação às ACLs. A menor granularidade do RBAC está no nível de contêiner e isso será avaliado em uma prioridade mais alta do que as ACLs. Portanto, se você atribuir uma função a uma entidade de segurança no escopo de um contêiner, essa entidade de segurança terá o nível de autorização associado a essa função para todos os diretórios e arquivos nesse contêiner, independentemente das atribuições de ACL.

Quando uma entidade de segurança recebe permissões de dados RBAC por meio de uma [função interna](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)ou por meio de uma função personalizada, essas permissões são avaliadas primeiro após a autorização de uma solicitação. Se a operação solicitada for autorizada pelas atribuições de RBAC da entidade de segurança, a autorização será imediatamente resolvida e nenhuma verificação de ACL adicional será executada. Como alternativa, se a entidade de segurança não tiver uma atribuição de RBAC ou se a operação da solicitação não corresponder à permissão atribuída, as verificações de ACL serão executadas para determinar se a entidade de segurança está autorizada a executar a operação solicitada.

> [!NOTE]
> Se a entidade de segurança tiver sido atribuída a atribuição de função interna do proprietário de dados do blob de armazenamento, a entidade de segurança será considerada um *superusuário* e terá acesso completo a todas as operações de mutação, incluindo a definição do proprietário de um diretório ou arquivo, bem como ACLs para diretórios e arquivos para os quais eles não são proprietários. O acesso de superusuário é a única maneira autorizada de alterar o proprietário de um recurso.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Autenticação de chave compartilhada e SAS (assinatura de acesso compartilhado)

O Azure Data Lake Storage Gen2 dá suporte a métodos de chave compartilhada e SAS para autenticação. Uma característica desses métodos de autenticação é que nenhuma identidade está associada ao chamador e, portanto, a autorização baseada em permissão de entidade de segurança não pode ser executada.

No caso da chave compartilhada, o chamador efetivamente obtém o acesso de "superusuário", o que significa acesso completo a todas as operações em todos os recursos, incluindo a configuração de proprietário e a alteração de ACLs.

Os tokens SAS incluem permissões permitidas como parte do token. As permissões incluídas no token SAS são efetivamente aplicadas a todas as decisões de autorização, mas nenhuma verificação de ACL adicional é executada.

## <a name="access-control-lists-on-files-and-directories"></a>Listas de controle de acesso em arquivos e diretórios

Você pode associar uma entidade de segurança a um nível de acesso para arquivos e diretórios. Essas associações são capturadas em uma *ACL (lista de controle de acesso)* . Cada arquivo e diretório na sua conta de armazenamento tem uma lista de controle de acesso.

Se você atribuiu uma função a uma entidade de segurança no nível da conta de armazenamento, poderá usar listas de controle de acesso para conceder a essa entidade de segurança acesso elevado a arquivos e diretórios específicos.

Você não pode usar listas de controle de acesso para fornecer um nível de acesso inferior a um nível concedido por uma atribuição de função. Por exemplo, se você atribuir a função de [colaborador de dados de blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) a uma entidade de segurança, não poderá usar listas de controle de acesso para impedir que a entidade de segurança grave em um diretório.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Definir permissões de nível de arquivo e diretório usando listas de controle de acesso

Para definir permissões de nível de arquivo e diretório, consulte qualquer um dos seguintes artigos:

|Se você quiser usar essa ferramenta:    |Consulte este artigo:    |
|--------|-----------|
|Explorador do Storage do Azure    |[Definir permissões de nível de arquivo e diretório usando Gerenciador de Armazenamento do Azure com Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|API REST    |[Caminho-atualizar](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Se a entidade de segurança for uma entidade de *serviço* , é importante usar a ID de objeto da entidade de serviço e não a ID de objeto do registro do aplicativo relacionado. Para obter a ID de objeto da entidade de serviço, abra o CLI do Azure e, em seguida, use este comando: `az ad sp show --id <Your App ID> --query objectId`. Certifique-se de substituir o espaço reservado `<Your App ID>` pela ID do aplicativo do registro do aplicativo.

### <a name="types-of-access-control-lists"></a>Tipos de listas de controle de acesso

Há dois tipos de listas de controle de acesso: *ACLs de acesso* e *ACLs padrão*.

As ACLs de acesso controlam o acesso a um objeto. Os arquivos e diretórios têm ACLs de acesso.

As ACLs padrão são modelos de ACLs associadas a um diretório que determinam as ACLs de acesso para qualquer item filho que são criados nesse diretório. Os arquivos não têm ACLs padrão.

As ACLs de acesso e as ACLs padrão têm a mesma estrutura.

> [!NOTE]
> A alteração da ACL padrão em um pai não afeta a ACL de acesso ou a ACL padrão de itens filho que já existem.

### <a name="levels-of-permission"></a>Níveis de permissão

As permissões em um objeto de contêiner são de **leitura**, **gravação**e **execução**, e podem ser usadas em arquivos e diretórios, conforme mostrado na tabela a seguir:

|            |    Ficheiro     |   Diretório |
|------------|-------------|----------|
| **Leitura (R)** | Pode editar o conteúdo de um ficheiro | Requer **leitura** e **execução** para listar o conteúdo do diretório |
| **Escrita (W)** | Pode escrever ou acrescentar a um ficheiro | Requer **gravação** e **execução** para criar itens filho em um diretório |
| **Execução (X)** | Não significa nada no contexto de Data Lake Storage Gen2 | Necessário para atravessar os itens filho de um diretório |

> [!NOTE]
> Se você estiver concedendo permissões usando somente ACLs (sem RBAC), para conceder a uma entidade de segurança acesso de leitura ou de gravação a um arquivo, você precisará conceder permissões de **execução** da entidade de segurança para o contêiner e para cada pasta na hierarquia de pastas que levam ao arquivo.

#### <a name="short-forms-for-permissions"></a>Formatos curtos para as permissões

O **RWX** é utilizado para indicar **Leitura + Escrita + Execução**. Existe um formato numérico mais condensado, em que **Leitura=4**, **Escrita=2** e **Execução=1**, cuja respetiva soma representa as permissões. Abaixo, encontram-se alguns exemplos.

| Formato numérico | Formato curto |      O que representa     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Leitura + Escrita + Execução |
| 5            | `R-X`        | Leitura + Execução         |
| 4            | `R--`        | Leitura                   |
| 0            | `---`        | Sem permissões         |

#### <a name="permissions-inheritance"></a>Herança de permissões

No modelo de estilo POSIX que é usado pelo Data Lake Storage Gen2, as permissões para um item são armazenadas no próprio item. Em outras palavras, as permissões para um item não poderão ser herdadas dos itens pai se as permissões forem definidas depois que o item filho já tiver sido criado. As permissões serão herdadas somente se as permissões padrão tiverem sido definidas nos itens pai antes de os itens filho terem sido criados.

### <a name="common-scenarios-related-to-permissions"></a>Cenários comuns relacionados com as permissões

A tabela a seguir lista alguns cenários comuns para ajudá-lo a entender quais permissões são necessárias para executar determinadas operações em uma conta de armazenamento.

|    Operação             |    /    | Oregon | Portland / | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Ler Data. txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Anexar a data. txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Excluir Data. txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Criar data. txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Listar/Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Listar/Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> As permissões de gravação no arquivo não são necessárias para excluí-lo, desde que as duas condições anteriores sejam verdadeiras.

### <a name="users-and-identities"></a>Utilizadores e identidades

Cada arquivo e diretório têm permissões distintas para essas identidades:

- O utilizador proprietário
- O grupo proprietário
- Utilizadores nomeados
- Grupos nomeados
- Entidades de serviço nomeadas
- Identidades gerenciadas nomeadas
- Todos os outros utilizadores

As identidades dos utilizadores e grupos são identidades do Azure Active Directory (Azure AD). Então, salvo indicação em contrário, um *usuário*, no contexto de data Lake Storage Gen2, pode se referir a um usuário do Azure AD, entidade de serviço, identidade gerenciada ou grupo de segurança.

#### <a name="the-owning-user"></a>O utilizador proprietário

O utilizador que criou o item é automaticamente o utilizador proprietário do item. Um utilizador proprietário pode:

* Alterar as permissões de um ficheiro que é propriedade.
* Alterar o grupo proprietário de um ficheiro que é propriedade, desde que o utilizador proprietário também seja membro do grupo de destino.

> [!NOTE]
> O usuário proprietário *não pode* alterar o usuário proprietário de um arquivo ou diretório. Somente superusuários podem alterar o usuário proprietário de um arquivo ou diretório.

#### <a name="the-owning-group"></a>O grupo proprietário

Nas ACLs de POSIX, cada usuário é associado a um *grupo primário*. Por exemplo, o usuário "Alice" pode pertencer ao grupo "Finance". Alice também pode pertencer a vários grupos, mas um grupo é sempre designado como seu grupo primário. No POSIX, quando a Alice cria um ficheiro, o grupo proprietário do mesmo está definido como o grupo principal dela, que, neste caso, é "finanças". Caso contrário, o grupo proprietário tem um comportamento semelhante ao das permissões atribuídas para outros utilizadores/grupos.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Atribuindo o grupo proprietário para um novo arquivo ou diretório

* **Caso 1**: o diretório raiz "/". Esse diretório é criado quando um contêiner de Data Lake Storage Gen2 é criado. Nesse caso, o grupo proprietário é definido para o usuário que criou o contêiner se ele foi feito usando o OAuth. Se o contêiner for criado usando chave compartilhada, uma SAS de conta ou uma SAS de serviço, o proprietário e o grupo proprietário serão definidos como **$superuser**.
* **Caso 2** (todos os outros casos): quando um novo item é criado, o grupo proprietário é copiado do diretório pai.

##### <a name="changing-the-owning-group"></a>Alterando o grupo proprietário

O grupo proprietário pode ser alterado por:
* Qualquer superutilizador.
* Pelo utilizador proprietário, se o utilizador proprietário também for membro do grupo de destino.

> [!NOTE]
> O grupo proprietário não pode alterar as ACLs de um arquivo ou diretório.  Embora o grupo proprietário esteja definido como o usuário que criou a conta no caso do diretório raiz, **caso 1** acima, uma única conta de usuário não é válida para fornecer permissões por meio do grupo proprietário. Pode atribuir esta permissão a um grupo de utilizadores válido, se aplicável.

### <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

O pseudocódigo a seguir representa o algoritmo de verificação de acesso para contas de armazenamento.

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

Conforme ilustrado no algoritmo de verificação de acesso, a máscara limita o acesso para usuários nomeados, o grupo proprietário e os grupos nomeados.  

> [!NOTE]
> Para um novo contêiner Data Lake Storage Gen2, a máscara para a ACL de acesso do diretório raiz ("/") usa como padrão 750 para diretórios e 640 para arquivos. Os arquivos não recebem o X bit, pois são irrelevantes para os arquivos em um sistema somente de armazenamento.
>
> A máscara pode ser especificada em uma base por chamada. Isso permite que diferentes sistemas de consumo, como clusters, tenham diferentes máscaras efetivas para suas operações de arquivo. Se uma máscara for especificada em uma determinada solicitação, ela substituirá completamente a máscara padrão.

#### <a name="the-sticky-bit"></a>O sticky bit

O bit adesivo é um recurso mais avançado de um contêiner POSIX. No contexto de Data Lake Storage Gen2, é improvável que o bit de aderência seja necessário. Em resumo, se o bit adesivo estiver habilitado em um diretório, um item filho só poderá ser excluído ou renomeado pelo usuário proprietário do item filho.

O bit adesivo não é mostrado na portal do Azure.

### <a name="default-permissions-on-new-files-and-directories"></a>Permissões padrão em novos arquivos e diretórios

Quando um novo arquivo ou diretório é criado em um diretório existente, a ACL padrão no diretório pai determina:

- ACL padrão de um diretório filho e ACL de acesso.
- A ACL de acesso de um arquivo filho (arquivos não têm uma ACL padrão).

#### <a name="umask"></a>umask

Ao criar um arquivo ou diretório, umask é usado para modificar como as ACLs padrão são definidas no item filho. umask é um valor de 9 bits em diretórios pai que contém um valor de RWX para **usuário proprietário**, **grupo proprietário**e **outros**.

O umask para Azure Data Lake Storage Gen2 um valor constante que é definido como 007. Esse valor é convertido em:

| componente de umask     | Formato numérico | Formato curto | Significado |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Para o usuário proprietário, copie a ACL padrão do pai para a ACL de acesso do filho | 
| umask.owning_group  |    0         |   `---`      | Para o grupo proprietário, copie a ACL padrão do pai para a ACL de acesso do filho | 
| umask.other         |    7         |   `RWX`      | Para outros, remova todas as permissões na ACL de acesso do filho |

O valor umask usado por Azure Data Lake Storage Gen2 efetivamente significa que o valor de **outro** nunca é transmitido por padrão em novos filhos, independentemente do que a ACL padrão indica. 

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Perguntas comuns sobre ACLs no Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>É necessário ativar o suporte para as ACLs?

Não. O controle de acesso por meio de ACLs é habilitado para uma conta de armazenamento, desde que o recurso de namespace hierárquico (HNS) esteja ativado.

Se o HNS for desativado, as regras de autorização do RBAC do Azure ainda se aplicarão.

### <a name="what-is-the-best-way-to-apply-acls"></a>Qual é a melhor maneira de aplicar ACLs?

Sempre use grupos de segurança do Azure AD como a entidade atribuída em ACLs. Resista à oportunidade de atribuir diretamente usuários individuais ou entidades de serviço. Usar essa estrutura permitirá que você adicione e remova usuários ou entidades de serviço sem a necessidade de reaplicar ACLs a uma estrutura de diretório inteira. ) Em vez disso, basta adicionar ou removê-los do grupo de segurança apropriado do Azure AD. Tenha em mente que as ACLs não são herdadas e, portanto, reaplicar ACLs requer a atualização da ACL em todos os arquivos e subdiretórios. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Quais permissões são necessárias para excluir recursivamente um diretório e seu conteúdo?

- O chamador tem permissões de ' superusuário ',

Ou

- O diretório pai deve ter permissões Write + execute.
- O diretório a ser excluído e todos os diretórios dentro dele requer permissões de leitura + gravação + execução.

> [!NOTE]
> Você não precisa de permissões de gravação para excluir arquivos em diretórios. Além disso, o diretório raiz "/" nunca pode ser excluído.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Quem é o proprietário de um arquivo ou diretório?

O criador de um arquivo ou diretório se torna o proprietário. No caso do diretório raiz, essa é a identidade do usuário que criou o contêiner.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Qual grupo está definido como o grupo proprietário de um arquivo ou diretório na criação?

O grupo proprietário é copiado do grupo proprietário do diretório pai no qual o novo arquivo ou diretório é criado.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Sou o utilizador proprietário de um ficheiro, mas não tenho as permissões de RWX necessárias. O que posso fazer?

O utilizador proprietário pode alterar as permissões do ficheiro para atribuir as permissões de RWX necessárias a ele próprio.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Por que, às vezes, vejo GUIDs em ACLs?

Um GUID será mostrado se a entrada representar um usuário e esse usuário não existir mais no Azure AD. Normalmente, isto acontece quando o utilizador já não está na empresa ou se a conta dele tiver sido eliminada no Azure AD. Além disso, as entidades de serviço e os grupos de segurança não têm um UPN (nome principal do usuário) para identificá-los e, portanto, são representados por seu atributo OID (um GUID).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Como fazer definir ACLs corretamente para uma entidade de serviço?

Quando você define ACLs para entidades de serviço, é importante usar a OID (ID de objeto) da entidade de *serviço* para o registro do aplicativo que você criou. É importante observar que os aplicativos registrados têm uma entidade de serviço separada no locatário específico do Azure AD. Os aplicativos registrados têm um OID que é visível no portal do Azure, mas a *entidade de serviço* tem outro OID (diferente).

Para obter o OID para a entidade de serviço que corresponde a um registro de aplicativo, você pode usar o comando `az ad sp show`. Especifique a ID do aplicativo como o parâmetro. Veja um exemplo de como obter o OID para a entidade de serviço que corresponde a um registro de aplicativo com ID do aplicativo = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Execute o seguinte comando no CLI do Azure:

```
$ az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>
```

Quando você tiver o OID correto para a entidade de serviço, vá para a página Gerenciador de Armazenamento **gerenciar acesso** para adicionar o OID e atribuir as permissões apropriadas para o OID. Certifique-se de selecionar **salvar**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 dá suporte à herança de ACLs?

As atribuições do RBAC do Azure herdam. As atribuições fluem de assinatura, grupo de recursos e recursos da conta de armazenamento até o recurso de contêiner.

As ACLs não herdam. No entanto, as ACLs padrão podem ser usadas para definir ACLs para subdiretórios filho e arquivos criados no diretório pai. 

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

* [Visão geral do Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
