---
title: Listas de controlo de acesso no Azure Data Lake Storage Gen2 Microsoft Docs
description: Entenda como as listas de controlo de acesso tipo POSIX funcionam na Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 2a1455c5956297a19d640146879f93b61d035139
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185908"
---
# <a name="access-control-lists-acls-in-azure-data-lake-storage-gen2"></a>Listas de controlo de acesso (ACLs) em Azure Data Lake Storage Gen2

A Azure Data Lake Storage Gen2 implementa um modelo de controlo de acesso que suporta tanto o controlo de acesso baseado em funções Azure (Azure RBAC) como as listas de controlo de acesso semelhantes a POSIX (ACLs). Este artigo descreve listas de controlo de acesso na Data Lake Storage Gen2. Para aprender como incorporar o Azure RBAC juntamente com os ACLs, e como o sistema os avalia para tomar decisões de autorização, consulte [o modelo de controlo de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

<a id="access-control-lists-on-files-and-directories"></a>

## <a name="about-acls"></a>Sobre ACLs

Pode associar um [diretor de segurança](../../role-based-access-control/overview.md#security-principal) a um nível de acesso para ficheiros e diretórios. Estas associações são capturadas numa *lista de controlo de acesso (ACL)*. Cada ficheiro e diretório na sua conta de armazenamento tem uma lista de controlo de acesso. Quando um principal de segurança tenta uma operação num ficheiro ou diretório, uma verificação da ACL determina se esse principal de segurança (utilizador, grupo, principal de serviço ou identidade gerida) tem o nível de permissão correto para realizar a operação.

> [!NOTE]
> Os ACLs aplicam-se apenas aos princípios de segurança do mesmo inquilino, e não se aplicam aos utilizadores que utilizem a Chave Partilhada ou a autenticação de assinatura de acesso partilhado (SAS). Isto porque nenhuma identidade está associada ao chamador e, portanto, a autorização baseada na autorização principal de segurança não pode ser realizada.  

## <a name="how-to-set-acls"></a>Como definir ACLs

Para definir permissões de nível de arquivo e diretório, consulte qualquer um dos seguintes artigos:

| Ambiente | Artigo |
|--------|-----------|
|Explorador de Armazenamento do Azure |[Use Azure Storage Explorer to manage directories, files, and ACLs in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access) (Utilizar o Explorador de Armazenamento do Azure para gerir diretórios, ficheiros e ACLs no Azure Data Lake Storage Gen2)|
|.NET |[Utilize .NET para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md#manage-access-control-lists-acls)|
|Java|[Use a Java para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md#manage-access-control-lists-acls)|
|Python|[Use python para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md#manage-access-control-lists-acls)|
|PowerShell|[Use o PowerShell para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md#manage-access-control-lists-acls)|
|CLI do Azure|[Use o Azure CLI para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md#manage-access-control-lists-acls)|
|API REST |[Caminho - Atualização](/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Se o diretor de segurança for um principal de *serviço,* é importante usar o ID do objeto do principal do serviço e não o ID do objeto do registo de aplicações relacionados. Para obter a identificação do objeto do principal de serviço abra o CLI Azure e, em seguida, use este comando: `az ad sp show --id <Your App ID> --query objectId` . certifique-se de substituir o `<Your App ID>` espaço reservado pelo ID da aplicação do seu registo de aplicações.

## <a name="types-of-acls"></a>Tipos de ACLs

Existem dois tipos de listas de controlo de *acesso: acedem a ACLs* de acesso e *ACLs predefinidos.*

AcLs de acesso controlam o acesso a um objeto. Os ficheiros e diretórios têm acesso a ACLs.

AcLs predefinidos são modelos de ACLs associados a um diretório que determinam os ACLs de acesso para quaisquer itens infantis que são criados sob esse diretório. Os ficheiros não têm ACLs predefinidos.

Tanto acls de acesso como ACLs predefinidos têm a mesma estrutura.

> [!NOTE]
> A alteração do ACL predefinido num progenitor não afeta o acesso ACL ou ACL predefinido de itens infantis que já existem.

## <a name="levels-of-permission"></a>Níveis de permissão

As permissões em diretórios e ficheiros num contentor são **Ler,** **Escrever** e **Executar,** e podem ser usadas em ficheiros e diretórios como mostrado na tabela seguinte:

|            |    Ficheiro     |   Diretório |
|------------|-------------|----------|
| **Leitura (R)** | Pode editar o conteúdo de um ficheiro | Requer **ler** e **executar** para listar o conteúdo do diretório |
| **Escrita (W)** | Pode escrever ou acrescentar a um ficheiro | Requer **escrever** e **executar** para criar itens infantis em um diretório |
| **Execução (X)** | Não significa nada no contexto do Data Lake Storage Gen2 | Obrigado a atravessar os itens infantis de um diretório |

> [!NOTE]
> Se estiver a conceder permissões utilizando apenas ACLs (sem Azure RBAC), em seguida, para conceder a um diretor de segurança leitura ou escrever acesso a um ficheiro, terá de dar ao diretor de segurança permissões **execute** a pasta raiz do recipiente e a cada pasta na hierarquia das pastas que levam ao ficheiro.

### <a name="short-forms-for-permissions"></a>Formatos curtos para as permissões

O **RWX** é utilizado para indicar **Leitura + Escrita + Execução**. Existe um formato numérico mais condensado, em que **Leitura=4**, **Escrita=2** e **Execução=1**, cuja respetiva soma representa as permissões. Abaixo, encontram-se alguns exemplos.

| Formato numérico | Formato curto |      O que representa     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Leitura + Escrita + Execução |
| 5            | `R-X`        | Leitura + Execução         |
| 4            | `R--`        | Ler                   |
| 0            | `---`        | Sem permissões         |

### <a name="permissions-inheritance"></a>Permissões de herança

No modelo de estilo POSIX que é usado pela Data Lake Storage Gen2, as permissões para um item são armazenadas no próprio item. Por outras palavras, as permissões de um item não podem ser herdadas dos itens-mãe se as permissões forem definidas após a criação do artigo da criança. As permissões só são herdadas se as permissões padrão tiverem sido definidas nos itens-mãe antes de os itens da criança terem sido criados.

## <a name="common-scenarios-related-to-acl-permissions"></a>Cenários comuns relacionados com permissões ACL

A tabela que se segue mostra-lhe as entradas ACL necessárias para permitir que um diretor de segurança execute as operações listadas na coluna **Operação.** 

Esta tabela mostra uma coluna que representa cada nível de uma hierarquia fictícia do diretório. Há uma coluna para o diretório de raiz do contentor , `\` um subdiretório chamado **Oregon**, um subdiretório do diretório do Oregon chamado **Portland,** e um ficheiro de texto no diretório de Portland chamado **Data.txt**. 

> [!IMPORTANT]
> Esta tabela pressupõe que está a utilizar **apenas** ACLs sem quaisquer atribuições de funções Azure. Para ver uma tabela semelhante que combina Azure RBAC juntamente com ACLs, consulte [tabela permissões: Combinando Azure RBAC e ACL](data-lake-storage-access-control-model.md#permissions-table-combining-azure-rbac-and-acl).

|    Operação             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Leia Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Apêndice a Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Apagar Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Criar Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> As permissões de escrita no ficheiro não são necessárias para eliminá-lo, desde que as duas condições anteriores sejam verdadeiras.

## <a name="users-and-identities"></a>Utilizadores e identidades

Cada ficheiro e diretório tem permissões distintas para estas identidades:

- O utilizador proprietário
- O grupo proprietário
- Utilizadores nomeados
- Grupos nomeados
- Diretores de serviço nomeados
- Identidades geridas nomeadas
- Todos os outros utilizadores

As identidades dos utilizadores e grupos são identidades do Azure Active Directory (Azure AD). Assim, salvo indicação em contrário, um *utilizador,* no contexto da Data Lake Storage Gen2, pode consultar um utilizador AD, principal de serviço, identidade gerida ou grupo de segurança.

### <a name="the-owning-user"></a>O utilizador proprietário

O utilizador que criou o item é automaticamente o utilizador proprietário do item. Um utilizador proprietário pode:

* Alterar as permissões de um ficheiro que é propriedade.
* Alterar o grupo proprietário de um ficheiro que é propriedade, desde que o utilizador proprietário também seja membro do grupo de destino.

> [!NOTE]
> O utilizador próprio *não pode* alterar o utilizador próprio de um ficheiro ou diretório. Apenas os super-utilizadores podem alterar o utilizador próprio de um ficheiro ou diretório.

### <a name="the-owning-group"></a>O grupo proprietário

Nos ACLs POSIX, cada utilizador está associado a um *grupo primário.* Por exemplo, o utilizador "Alice" pode pertencer ao grupo "finanças". Alice também pode pertencer a vários grupos, mas um grupo é sempre designado como o seu grupo principal. No POSIX, quando a Alice cria um ficheiro, o grupo proprietário do mesmo está definido como o grupo principal dela, que, neste caso, é "finanças". Caso contrário, o grupo proprietário tem um comportamento semelhante ao das permissões atribuídas para outros utilizadores/grupos.

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Atribuindo o grupo de propriedade para um novo arquivo ou diretório

* **Caso 1**: O diretório de raiz "/". Este diretório é criado quando um recipiente De Armazenamento de Data Lake Gen2 é criado. Neste caso, o grupo de propriedade é definido para o utilizador que criou o recipiente se foi feito usando OAuth. Se o contentor for criado com chave partilhada, uma Conta SAS ou uma SAS de serviço, então o proprietário e o grupo proprietário estão definidos para **$superuser**.
* **Caso 2** (Todos os outros casos): Quando um novo item é criado, o grupo de propriedade é copiado do diretório principal.

#### <a name="changing-the-owning-group"></a>Mudar o grupo de propriedade

O grupo proprietário pode ser alterado por:
* Qualquer superutilizador.
* Pelo utilizador proprietário, se o utilizador proprietário também for membro do grupo de destino.

> [!NOTE]
> O grupo de 2018 não pode alterar os ACLs de um ficheiro ou diretório.  Enquanto o grupo de propriedade é definido para o utilizador que criou a conta no caso do diretório de raiz, **caso 1** acima, uma única conta de utilizador não é válida para fornecer permissões através do grupo próprio. Pode atribuir esta permissão a um grupo de utilizadores válido, se aplicável.

## <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

O pseudocódigo que se segue representa o algoritmo de verificação de acesso para contas de armazenamento.

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
  mask = get_mask( path )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
        if ((desired_perms & entry.permissions & mask) == desired_perms)
            return True 
        
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>A máscara

Como ilustrado no Algoritmo de Verificação de Acesso, a máscara limita o acesso aos utilizadores nomeados, ao grupo de propriedade e aos grupos nomeados.  

Para um novo recipiente De armazenamento de dados Gen2, a máscara para o acesso ACL do diretório de raiz ("/") não é **750** para diretórios e **640** para ficheiros. A tabela a seguir mostra a notação simbólica destes níveis de permissão.

|Entidade|Diretórios|Ficheiros|
|--|--|--|
|Utilizador proprietário|`rwx`|`r-w`|
|Grupo proprietário|`r-x`|`r--`|
|Outro|`---`|`---`|

Os ficheiros não recebem o bit X, uma vez que é irrelevante para os ficheiros de um sistema apenas de loja. 

A máscara pode ser especificada por chamada. Isto permite que diferentes sistemas de consumo, como clusters, tenham diferentes máscaras eficazes para as suas operações de arquivo. Se uma máscara for especificada num dado pedido, substitui completamente a máscara predefinida.

### <a name="the-sticky-bit"></a>O sticky bit

A parte pegajosa é uma característica mais avançada de um recipiente POSIX. No contexto da Data Lake Storage Gen2, é improvável que a parte pegajosa seja necessária. Em resumo, se a broca pegajosa estiver ativada num diretório, um item para criança só pode ser eliminado ou renomeado pelo utilizador próprio do artigo da criança.

A parte pegajosa não é mostrada no portal Azure.

## <a name="default-permissions-on-new-files-and-directories"></a>Permissões por defeito em novos ficheiros e diretórios

Quando um novo ficheiro ou diretório é criado sob um diretório existente, o ACL predefinido no directório-mãe determina:

- ACL padrão de um diretório infantil e acesso ACL.
- ACL de acesso a um ficheiro infantil (os ficheiros não têm um ACL predefinido).

### <a name="umask"></a>umask

Ao criar um ficheiro ou diretório, umask é usada para modificar a forma como os ACLs predefinidos são definidos no item da criança. umask é um valor de 9 bits em directórios-mãe que contém um valor RWX para **o utilizador próprio,** **grupo próprio,** e **outros**.

A umask para Azure Data Lake Storage Gen2 um valor constante que está definido para 007. Este valor traduz-se em:

| componente umask     | Formato numérico | Formato curto | Significado |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Para possuir o utilizador, copie o ACL padrão do progenitor para o acesso da criança ACL | 
| umask.owning_group  |    0         |   `---`      | Para possuir o grupo, copie o ACL padrão do progenitor para o acesso da criança ACL | 
| umask.outro         |    7         |   `RWX`      | Para outro, remova todas as permissões no acesso da criança ACL |

O valor de umask usado pelo Azure Data Lake Storage Gen2 significa efetivamente que o valor para **outros** nunca é transmitido por padrão em novas crianças, a menos que um ACL predefinido seja definido no diretório principal. Nesse caso, a umask é efetivamente ignorada e as permissões definidas pela ACL predefinida são aplicadas ao item da criança. 

O pseudocódigo que se segue mostra como a umask é aplicada ao criar os ACLs para um item infantil.

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

## <a name="faq"></a>FAQ

### <a name="do-i-have-to-enable-support-for-acls"></a>É necessário ativar o suporte para as ACLs?

N.º O controlo de acesso através de ACLs está ativado para uma conta de armazenamento desde que a função Espaço Hierárquico (HNS) esteja ligada.

Se o HNS for desligado, as regras de autorização do Azure Azure RBAC ainda se aplicam.

### <a name="what-is-the-best-way-to-apply-acls"></a>Qual é a melhor maneira de aplicar ACLs?

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)] 

### <a name="how-are-azure-rbac-and-acl-permissions-evaluated"></a>Como são avaliadas as permissões Azure RBAC e ACL?

Para saber como o sistema avalia a Azure RBAC e ACLs em conjunto para tomar decisões de autorização para recursos de conta de armazenamento, consulte [como as permissões são avaliadas.](data-lake-storage-access-control-model.md#how-permissions-are-evaluated)

### <a name="what-are-the-limits-for-azure-role-assignments-and-acl-entries"></a>Quais são os limites para atribuições de funções Azure e entradas ACL?

O quadro seguinte fornece uma visão resumo dos limites a considerar ao utilizar o Azure RBAC para gerir permissões de grãos grossos (permissões aplicáveis a contas de armazenamento ou contentores) e utilização de ACLs para gerir permissões de grãos finos (permissões aplicáveis a ficheiros e diretórios). Utilize grupos de segurança para missões ACL. Ao utilizar grupos, é menos provável que exceda o número máximo de atribuições de funções por subscrição e o número máximo de entradas de ACl por ficheiro ou diretório. 

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-azure-rbac"></a>O Data Lake Storage Gen2 suporta a herança do Azure RBAC?

As tarefas do Azure herdam. As atribuições fluem de recursos de subscrição, grupo de recursos e conta de armazenamento até ao recurso do contentor.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>O Data Lake Storage Gen2 suporta a herança dos ACLs?

Os ACLs predefinidos podem ser usados para definir ACLs para novas subdiretórios infantis e ficheiros criados sob o diretório dos pais. Para atualizar os ACLs para os itens infantis existentes, terá de adicionar, atualizar ou remover os ACLs de forma recorrente para a hierarquia do diretório pretendido. Para obter mais informações, consulte [as listas de controlo de acesso (ACLs) de forma recorrente para Azure Data Lake Storage Gen2](recursive-access-control-lists.md). 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Que permissões são necessárias para eliminar novamente um diretório e o seu conteúdo?

- O chamador tem permissões de "super-utilizador",

Ou

- O diretório dos pais deve ter permissões de Escrita + Executar.
- O diretório a ser apagado, e todos os diretórios dentro dele, requer permissões de Leitura + Escrita + Execução.

> [!NOTE]
> Não precisa de escrever permissões para eliminar ficheiros em diretórios. Além disso, o diretório de raiz "/" nunca pode ser apagado.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Quem é o dono de um ficheiro ou diretório?

O criador de um ficheiro ou diretório torna-se o proprietário. No caso do diretório de raiz, esta é a identidade do utilizador que criou o contentor.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Que grupo é definido como o grupo dono de um arquivo ou diretório na criação?

O grupo de propriedade é copiado do grupo de propriedade do directório-mãe ao abrigo do qual o novo ficheiro ou diretório é criado.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Sou o dono de um ficheiro, mas não tenho as permissões RWX de que preciso. O que posso fazer?

O utilizador proprietário pode alterar as permissões do ficheiro para atribuir as permissões de RWX necessárias a ele próprio.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Por que às vezes vejo GUIDs em ACLs?

Um GUID é mostrado se a entrada representa um utilizador e esse utilizador já não existe no AD Azure. Normalmente, isto acontece quando o utilizador já não está na empresa ou se a conta dele tiver sido eliminada no Azure AD. Além disso, os principais serviços e os grupos de segurança não têm um Nome Principal do Utilizador (UPN) para os identificar e por isso são representados pelo seu atributo OID (um guia).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Como posso definir acLs corretamente para um diretor de serviço?

Quando define ACLs para os principais de serviço, é importante utilizar o ID do Objeto (OID) do *principal serviço* para o registo de aplicações que criou. É importante notar que as aplicações registadas têm um principal de serviço separado no inquilino específico da Azure AD. As aplicações registadas têm um OID que é visível no portal Azure, mas o *principal do serviço* tem outro (diferente) OID.

Para obter o OID para o principal serviço que corresponde a um registo de aplicações, você pode usar o `az ad sp show` comando. Especifique o ID da aplicação como parâmetro. Aqui está um exemplo na obtenção do OID para o principal serviço que corresponde a um registo de aplicações com ID da app = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Executar o seguinte comando no Azure CLI:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID será exibido.

Quando tiver o OID correto para o principal do serviço, vá à página de **Acesso gerido** do Explorador de Armazenamento para adicionar o OID e atribua permissões apropriadas para o OID. Confirme que selecionou **Guardar**.

### <a name="can-i-set-the-acl-of-a-container"></a>Posso definir o ACL de um recipiente?

N.º Um recipiente não tem um ACL. No entanto, pode definir o ACL do diretório de raiz do recipiente. Cada contentor tem um diretório de raiz, e tem o mesmo nome que o contentor. Por exemplo, se o recipiente for `my-container` nomeado, o diretório de raiz é nomeado `myContainer/` . 

A AZure Storage REST API contém uma operação denominada [set Container ACL,](/rest/api/storageservices/set-container-acl)mas essa operação não pode ser utilizada para definir o ACL de um recipiente ou o diretório de raiz de um recipiente. Em vez disso, esta operação é utilizada para indicar se as bolhas num recipiente [podem ser acedidas publicamente](anonymous-read-access-configure.md). 

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

- [Modelo de controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md)
