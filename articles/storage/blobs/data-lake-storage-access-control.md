---
title: Visão geral do controlo de acessos em Azure Data Lake Storage Gen2 [ Microsoft Docs
description: Entenda como funciona o controlo de acesso sintetizar em Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 192e46fd7f86b6053eaf658fa65e3c6cdfa3a4e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528613"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Access control in Azure Data Lake Storage Gen2 (Controlo de acesso no Azure Data Lake Storage Gen2)

O Azure Data Lake Storage Gen2 implementa um modelo de controlo de acesso que suporta tanto as listas de controlo de acesso baseados em funções azure (RBAC) como as listas de controlo de acesso semelhantes a POSIX (ACLs). Este artigo resume os fundamentos do modelo de controlo de acesso para Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O RBAC utiliza atribuições de funções para aplicar efetivamente conjuntos de permissões aos *diretores*de segurança . Um diretor de *segurança* é um objeto que representa um utilizador, grupo, diretor de serviço ou identidade gerida que é definido no Azure Ative Directory (AD) que está a solicitar o acesso aos recursos do Azure.

Tipicamente, esses recursos Azure estão limitados a recursos de alto nível (por exemplo: contas de armazenamento Azure). No caso do Armazenamento Azure e, consequentemente, do Azure Data Lake Storage Gen2, este mecanismo foi alargado ao recurso do contentor (sistema de ficheiros).

Para aprender a atribuir funções aos diretores de segurança no âmbito da sua conta de armazenamento, consulte o Acesso ao Grant ao Blob Azure e aos dados de [fila com o RBAC no portal Azure.](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

> [!NOTE]
> Um utilizador convidado não pode criar uma atribuição de papéis.

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>O impacto das atribuições de funções nas listas de controlo de acesso ao nível de ficheiros e de nível de diretório

Embora a utilização de atribuições de funções RBAC seja um poderoso mecanismo para controlar as permissões de acesso, é um mecanismo muito grosso em relação aos ACLs. A menor granularidade para rBAC está ao nível do contentor e esta será avaliada com uma prioridade superior à dos ACLs. Portanto, se atribuir uma função a um diretor de segurança no âmbito de um contentor, esse diretor de segurança tem o nível de autorização associado a esse papel para TODOS os diretórios e ficheiros desse contentor, independentemente das atribuições da ACL.

Quando um diretor de segurança recebe permissões de dados RBAC através de uma [função incorporada](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues), ou através de uma função personalizada, estas permissões são avaliadas primeiro após a autorização de um pedido. Se a operação solicitada for autorizada pelas atribuições RBAC do diretor de segurança, a autorização é imediatamente resolvida e não são realizados controlos adicionais da ACL. Alternativamente, se o diretor de segurança não tiver uma atribuição RBAC, ou a operação do pedido não corresponder à permissão atribuída, então são efetuados controlos ACL para determinar se o diretor de segurança está autorizado a executar a operação solicitada.

> [!NOTE]
> Se o diretor de segurança tiver sido atribuído a atribuição de funções incorporada ao Proprietário de Dados blob de armazenamento, então o diretor de segurança é considerado um *superutilizador* e tem acesso total a todas as operações em mutação, incluindo a definição do proprietário de um diretório ou ficheiro, bem como ACLs para diretórios e ficheiros para os quais não são o proprietário. O acesso ao super-utilizador é a única forma autorizada de alterar o proprietário de um recurso.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Autenticação de assinatura de chave partilhada e assinatura de acesso partilhado (SAS)

Azure Data Lake Storage Gen2 suporta os métodos de chave partilhada e SAS para autenticação. Uma característica destes métodos de autenticação é que nenhuma identidade está associada ao chamador e, portanto, a autorização baseada na permissão principal de segurança não pode ser realizada.

No caso da Chave Partilhada, o chamador ganha efetivamente acesso a 'super-utilizador', o que significa acesso total a todas as operações em todos os recursos, incluindo a definição do proprietário e a alteração de ACLs.

As fichas SAS incluem permissões permitidas como parte do símbolo. As permissões incluídas no token SAS são efetivamente aplicadas a todas as decisões de autorização, mas não são realizados controlos adicionais da ACL.

## <a name="access-control-lists-on-files-and-directories"></a>Listas de controlo de acesso em ficheiros e diretórios

Pode associar um diretor de segurança a um nível de acesso para ficheiros e diretórios. Estas associações são capturadas numa lista de controlo de *acesso (ACL)*. Cada ficheiro e diretório na sua conta de armazenamento tem uma lista de controlo de acesso.

> [!NOTE]
> Os ACLs aplicam-se apenas aos diretores de segurança do mesmo inquilino. Não se pode associar um utilizador convidado a um nível de acesso.  

Se atribuiu uma função a um diretor de segurança ao nível da conta de armazenamento, pode utilizar listas de controlo de acesso para conceder o acesso elevado ao principal de segurança a ficheiros e diretórios específicos.

Não pode usar listas de controlo de acesso para fornecer um nível de acesso inferior a um nível concedido por uma atribuição de funções. Por exemplo, se atribuir a função de Colaborador de Dados do [Depósito Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) a um diretor de segurança, então não pode utilizar listas de controlo de acesso para impedir que esse diretor de segurança escreva para um diretório.


### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Definir permissões de nível de ficheiros e diretórios utilizando listas de controlo de acesso

Para definir permissões de nível de ficheiros e diretórios, consulte qualquer um dos seguintes artigos:

|||
|--------|-----------|
|Explorador do Storage do Azure |[Use o Azure Storage Explorer para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access)|
|.NET |[Use .NET para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Use java para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
|Python|[Use python para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[Use powerShell para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md)|
|CLI do Azure|[Utilize o Azure CLI para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md)|
|API REST |[Caminho - Atualização](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Se o diretor de segurança for um diretor de *serviço,* é importante usar o ID do objeto do diretor de serviço e não o id do objeto do registo da aplicação relacionada. Para obter a identificação do objeto do diretor de serviço abra `az ad sp show --id <Your App ID> --query objectId`o Azure CLI e, em seguida, use este comando: . Certifique-se de `<Your App ID>` substituir o espaço reservado pelo ID da sua aplicação.

### <a name="types-of-access-control-lists"></a>Tipos de listas de controlo de acesso

Existem dois tipos de listas de controlo de acesso: *acesso a ACLs* e *ACLs padrão*.

Aceda ao acesso aos ACLs controle o acesso a um objeto. Os ficheiros e diretórios têm ambos acesso a ACLs.

Os ACLs padrão são modelos de ACLs associados a um diretório que determinam os ACLs de acesso para quaisquer itens infantis que sejam criados sob esse diretório. Os ficheiros não têm ACLs padrão.

Tanto os ACLs de acesso como os ACLs predefinidos têm a mesma estrutura.

> [!NOTE]
> Alterar o ACL padrão num progenitor não afeta o ACL de acesso ou o ACL padrão dos itens infantis que já existem.

### <a name="levels-of-permission"></a>Níveis de permissão

As permissões num objeto de contentor estiveram **leia,** **Escreva**e **Execute,** e podem ser utilizadas em ficheiros e diretórios, como mostrado na tabela seguinte:

|            |    Ficheiro     |   Diretório |
|------------|-------------|----------|
| **Leitura (R)** | Pode editar o conteúdo de um ficheiro | Requer **leitura** e **execução** para listar o conteúdo do diretório |
| **Escrita (W)** | Pode escrever ou acrescentar a um ficheiro | Requer **escrever** e **executar** para criar itens infantis num diretório |
| **Execução (X)** | Não significa nada no contexto do Data Lake Storage Gen2 | Necessário para atravessar os itens infantis de um diretório |

> [!NOTE]
> Se estiver a conceder permissões utilizando apenas ACLs (sem RBAC), em seguida, conceder a um diretor de segurança ler ou escrever acesso a um ficheiro, terá de dar ao diretor de segurança **Permissões** executar ao recipiente e a cada pasta na hierarquia das pastas que conduzem ao ficheiro.

#### <a name="short-forms-for-permissions"></a>Formatos curtos para as permissões

O **RWX** é utilizado para indicar **Leitura + Escrita + Execução**. Existe um formato numérico mais condensado, em que **Leitura=4**, **Escrita=2** e **Execução=1**, cuja respetiva soma representa as permissões. Abaixo, encontram-se alguns exemplos.

| Formato numérico | Formato curto |      O que representa     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Leitura + Escrita + Execução |
| 5            | `R-X`        | Leitura + Execução         |
| 4            | `R--`        | Leitura                   |
| 0            | `---`        | Sem permissões         |

#### <a name="permissions-inheritance"></a>Herança de permissões

No modelo estilo POSIX que é usado pelo Data Lake Storage Gen2, as permissões para um item são armazenadas no próprio item. Por outras palavras, as permissões para um item não podem ser herdadas dos itens-mãe se as permissões forem definidas após a criação do artigo da criança. As permissões só são herdadas se tiverem sido definidas permissões por defeito nos itens dos pais antes da criação dos artigos da criança.

### <a name="common-scenarios-related-to-permissions"></a>Cenários comuns relacionados com as permissões

A tabela seguinte enumera alguns cenários comuns para ajudá-lo a entender quais as permissões necessárias para realizar determinadas operações numa conta de armazenamento.

|    Operação             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Ler Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Apêndice a Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Eliminar Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Criar Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Não são necessárias permissões de escrita no ficheiro para o apagar, desde que as duas condições anteriores sejam verdadeiras.

### <a name="users-and-identities"></a>Utilizadores e identidades

Cada ficheiro e diretório tem permissões distintas para estas identidades:

- O utilizador proprietário
- O grupo proprietário
- Utilizadores nomeados
- Grupos nomeados
- Diretores de serviço nomeados
- Identidades geridas nomeadas
- Todos os outros utilizadores

As identidades dos utilizadores e grupos são identidades do Azure Active Directory (Azure AD). Assim, salvo indicação em contrário, um *utilizador,* no contexto do Data Lake Storage Gen2, pode consultar um utilizador, diretor de serviço, identidade gerida ou grupo de segurança.

#### <a name="the-owning-user"></a>O utilizador proprietário

O utilizador que criou o item é automaticamente o utilizador proprietário do item. Um utilizador proprietário pode:

* Alterar as permissões de um ficheiro que é propriedade.
* Alterar o grupo proprietário de um ficheiro que é propriedade, desde que o utilizador proprietário também seja membro do grupo de destino.

> [!NOTE]
> O utilizador próprio *não pode* alterar o utilizador próprio de um ficheiro ou diretório. Apenas os super-utilizadores podem alterar o utilizador próprio de um ficheiro ou diretório.

#### <a name="the-owning-group"></a>O grupo proprietário

Nos ACLs POSIX, cada utilizador está associado a um *grupo primário*. Por exemplo, o utilizador "Alice" pode pertencer ao grupo "finanças". Alice também pode pertencer a vários grupos, mas um grupo é sempre designado como o seu grupo principal. No POSIX, quando a Alice cria um ficheiro, o grupo proprietário do mesmo está definido como o grupo principal dela, que, neste caso, é "finanças". Caso contrário, o grupo proprietário tem um comportamento semelhante ao das permissões atribuídas para outros utilizadores/grupos.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Atribuir o grupo próprio para um novo ficheiro ou diretório

* **Caso 1**: O diretório raiz "/". Este diretório é criado quando é criado um recipiente Gen2 de Armazenamento de Data Lake. Neste caso, o grupo próprio está definido para o utilizador que criou o contentor se foi feito usando o OAuth. Se o recipiente for criado utilizando a Chave Partilhada, uma Conta SAS ou um SAS de serviço, então o proprietário e o grupo proprietário estão definidos para **$superuser**.
* **Caso 2** (Todos os outros casos): Quando um novo item é criado, o grupo próprio é copiado do diretório dos pais.

##### <a name="changing-the-owning-group"></a>Mudar o grupo próprio

O grupo proprietário pode ser alterado por:
* Qualquer superutilizador.
* Pelo utilizador proprietário, se o utilizador proprietário também for membro do grupo de destino.

> [!NOTE]
> O grupo próprio não pode alterar os ACLs de um ficheiro ou diretório.  Enquanto o grupo próprio está definido para o utilizador que criou a conta no caso do diretório raiz, **o Caso 1** acima, uma única conta de utilizador não é válida para fornecer permissões através do grupo próprio. Pode atribuir esta permissão a um grupo de utilizadores válido, se aplicável.

### <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

O pseudocódigo seguinte representa o algoritmo de verificação de acesso para contas de armazenamento.

```console
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

Como ilustrado no Algoritmo de Verificação de Acesso, a máscara limita o acesso aos utilizadores nomeados, ao grupo próprio e aos grupos nomeados.  

> [!NOTE]
> Para um novo recipiente data Lake Storage Gen2, a máscara para o acesso ACL do diretório raiz ("/") falha para 750 para diretórios e 640 para ficheiros. Os ficheiros não recebem a bit X, uma vez que é irrelevante para ficheiros num sistema apenas para lojas.
>
> A máscara pode ser especificada por chamada. Isto permite que diferentes sistemas de consumo, como clusters, tenham diferentes máscaras eficazes para as suas operações de ficheiros. Se uma máscara for especificada num dado pedido, substitui completamente a máscara predefinida.

#### <a name="the-sticky-bit"></a>O sticky bit

A broca pegajosa é uma característica mais avançada de um recipiente POSIX. No contexto do Data Lake Storage Gen2, é improvável que a parte pegajosa seja necessária. Em resumo, se a broca pegajosa estiver ativada num diretório, um item infantil só pode ser eliminado ou renomeado pelo utilizador do item da criança.

A parte pegajosa não é mostrada no portal Azure.

### <a name="default-permissions-on-new-files-and-directories"></a>Permissões padrão em novos ficheiros e diretórios

Quando um novo ficheiro ou diretório é criado sob um diretório existente, o ACL padrão no directório-mãe determina:

- Acl padrão de um diretório infantil e acesso ACL.
- Acesso de um ficheiro infantil ACL (os ficheiros não têm uma ACL padrão).

#### <a name="umask"></a>umask

Ao criar um ficheiro ou diretório, o umask é utilizado para modificar a forma como os ACLs padrão são definidos no item da criança. umask é um valor de 9 bits em diretórios parentais que contém um valor RWX para **o utilizador possuir,** **possuir grupo,** e **outros**.

O umask para Azure Data Lake Storage Gen2 um valor constante que está definido para 007. Este valor traduz-se em:

| componente umask     | Formato numérico | Formato curto | Significado |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Para possuir o utilizador, copie o ACL padrão do progenitor para o acesso da criança ACL | 
| umask.owning_group  |    0         |   `---`      | Para possuir grupo, copie o ACL padrão do progenitor para o acesso da criança ACL | 
| umask.outros         |    7         |   `RWX`      | Para outros, remova todas as permissões no acesso da criança ACL |

O valor umask usado pelo Azure Data Lake Storage Gen2 significa efetivamente que o valor para **outros** nunca é transmitido por padrão em novas crianças, independentemente do que a ACL padrão indica. 

O pseudocódigo seguinte mostra como o umask é aplicado ao criar os ACLs para um item infantil.

```console
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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Perguntas comuns sobre ACLs em Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>É necessário ativar o suporte para as ACLs?

Não. O controlo de acesso através de ACLs está ativado para uma conta de armazenamento desde que a função Hierárquica Namespace (HNS) seja ativada.

Se o HNS for desligado, as regras de autorização Azure RBAC continuam a ser aplicadas.

### <a name="what-is-the-best-way-to-apply-acls"></a>Qual é a melhor maneira de aplicar ACLs?

Utilize sempre os grupos de segurança Azure AD como o principal designado em ACLs. Resista à oportunidade de atribuir diretamente utilizadores individuais ou diretores de serviço. A utilização desta estrutura permitir-lhe-á adicionar e remover utilizadores ou diretores de serviço sem a necessidade de reaplicar OSAC a toda uma estrutura de diretório. ) Em vez disso, basta adicioná-los ou removê-los do grupo de segurança Azure AD apropriado. Tenha em mente que os ACLs não são herdados e, por isso, a reaplicação dos ACLs requer a atualização do ACL em todos os ficheiros e subdiretórios. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Que permissões são necessárias para apagar recursivamente um diretório e o seu conteúdo?

- O chamador tem permissões de "super-utilizador",

Ou

- O diretório dos pais deve ter permissões escritas + executar.
- O diretório a eliminar, e todos os diretórios dentro dele, requer ler + escrever + executar permissões.

> [!NOTE]
> Não precisa de permissões de escrita para apagar ficheiros em diretórios. Além disso, o diretório raiz "/" nunca pode ser eliminado.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Quem é o dono de um ficheiro ou diretório?

O criador de um ficheiro ou diretório torna-se o proprietário. No caso do diretório raiz, esta é a identidade do utilizador que criou o recipiente.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Que grupo é definido como o grupo próprio de um arquivo ou diretório na criação?

O grupo próprio é copiado do grupo próprio do directório-mãe ao abrigo do qual é criado o novo ficheiro ou diretório.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Sou o utilizador de um ficheiro, mas não tenho as permissões RWX de que preciso. O que posso fazer?

O utilizador proprietário pode alterar as permissões do ficheiro para atribuir as permissões de RWX necessárias a ele próprio.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Por que às vezes vejo GUIDs em ACLs?

Um GUID é mostrado se a entrada representa um utilizador e esse utilizador já não existe em Azure AD. Normalmente, isto acontece quando o utilizador já não está na empresa ou se a conta dele tiver sido eliminada no Azure AD. Além disso, os diretores de serviço e os grupos de segurança não têm um Nome Principal de Utilizador (UPN) para os identificar e por isso estão representados pelo seu atributo OID (um guia).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Como posso definir os ACLs corretamente para um diretor de serviço?

Quando define ACLs para diretores de serviço, é importante utilizar o ID do Objeto (OID) do *principal de serviço* para o registo da aplicação que criou. É importante notar que as aplicações registadas têm um diretor de serviço separado no inquilino da AD Azure específico. As aplicações registadas têm um OID que é visível no portal Azure, mas o diretor de *serviço* tem outro (diferente) OID.

Para obter o OID para o diretor de serviço que `az ad sp show` corresponde a um registo de aplicação, pode utilizar o comando. Especifique o ID de aplicação como parâmetro. Aqui está um exemplo sobre a obtenção do OID para o principal de serviço que corresponde a um registo de aplicação com App ID = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Executar o seguinte comando no Azure CLI:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID será exibido.

Quando tiver o OID correto para o diretor de serviço, aceda à página de **Acesso ao Controlo** do Explorador de Armazenamento para adicionar o OID e atribuir permissões adequadas para o OID. Certifique-se de que seleciona **Guardar**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 suporta a herança de ACLs?

As atribuições azure RBAC herdam. As atribuições fluem da subscrição, do grupo de recursos e dos recursos da conta de armazenamento até ao recurso do contentor.

Os ACLs não herdam. No entanto, os ACLs padrão podem ser usados para definir ACLs para subdiretórios infantis e ficheiros criados sob o diretório dos pais. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Onde posso obter mais informações sobre o modelo de controlo de acesso POSIX?

* [POSIX Access Control Lists on Linux (Listas de Controlo de Acesso POSIX no Linux)](https://www.linux.com/news/posix-acls-linux)
* [HDFS permission guide (Guia de permissões do HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [FAQ DO POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL no Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL usando listas de controlo de acesso em Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Consulte também

* [Visão geral do Armazenamento de Lagos De Dados Azure Gen2](../blobs/data-lake-storage-introduction.md)
