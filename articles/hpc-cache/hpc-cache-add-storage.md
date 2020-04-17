---
title: Adicione armazenamento a uma Cache Azure HPC
description: Como definir alvos de armazenamento para que o seu Cache Azure HPC possa utilizar o seu sistema NFS no local ou recipientes Azure Blob para armazenamento de ficheiros a longo prazo
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: cecafd9209b095270e9a06ca59ffef162326efc2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538006"
---
# <a name="add-storage-targets"></a>Adicionar destinos de armazenamento

*Os alvos* de armazenamento são armazenamento de back-end para ficheiros que são acedidos através de uma Cache Azure HPC. Pode adicionar armazenamento NFS (como um sistema de hardware no local) ou armazenar dados em Azure Blob.

Pode definir até dez alvos de armazenamento diferentes para uma cache. A cache apresenta todos os alvos de armazenamento num espaço de nome agregado.

Lembre-se que as exportações de armazenamento devem ser acessíveis a partir da rede virtual do seu cache. Para armazenamento de hardware no local, poderá ser necessário configurar um servidor DNS que possa resolver nomes de anfitriões para acesso ao armazenamento NFS. Leia mais no [acesso ao DNS](hpc-cache-prereqs.md#dns-access).

Adicione alvos de armazenamento depois de criar a sua cache. O procedimento é ligeiramente diferente dependendo se você está adicionando armazenamento Azure Blob ou uma exportação NFS. Os detalhes para cada um estão abaixo.

## <a name="open-the-storage-targets-page"></a>Abra a página de alvos de armazenamento

A partir do portal Azure, abra a sua instância de cache e clique em **alvos de armazenamento** na barra lateral esquerda. A página de alvos de armazenamento lista todos os alvos existentes e dá um link para adicionar um novo.

![screenshot da ligação dos alvos de armazenamento na barra lateral, sob a rubrica Configuração, que está entre as definições das rubricas de categorias E monitorização](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Adicione um novo alvo de armazenamento Azure Blob

Um novo alvo de armazenamento Blob precisa de um recipiente Blob vazio ou um recipiente que é povoado com dados no formato de sistema de ficheiros de nuvem Azure HPC Cache. Leia mais sobre pré-carregamento de um recipiente Blob em [Mover dados para o armazenamento de Blob Azure](hpc-cache-ingest.md).

Pode criar um novo recipiente a partir desta página pouco antes de adicioná-lo.

![screenshot da página alvo de armazenamento adicionar, povoada com informações para um novo alvo de armazenamento Azure Blob](media/hpc-cache-add-blob.png)

Para definir um recipiente Azure Blob, introduza esta informação.

* **Nome alvo** de armazenamento - Defina um nome que identifique este alvo de armazenamento na Cache Azure HPC.
* **Tipo de alvo** - Escolha **Blob**.
* **Conta de armazenamento** - Selecione a conta que pretende utilizar.

  Terá de autorizar a instância de cache a aceder à conta de armazenamento conforme descrito em [Adicionar as funções](#add-the-access-control-roles-to-your-account)de acesso .

  Para obter informações sobre o tipo de conta de armazenamento que pode utilizar, leia os requisitos de [armazenamento blob](hpc-cache-prereqs.md#blob-storage-requirements).

* **Recipiente de armazenamento** - Selecione o recipiente Blob para este alvo ou clique **em Criar novo**.

  ![screenshot do diálogo para especificar nome e nível de acesso (privado) para novo recipiente](media/add-blob-new-container.png)

* **Caminho de espaço** de nome virtual - Delineie o caminho de ficheiro virado para o cliente para este alvo de armazenamento. Leia [configurar espaço](hpc-cache-namespace.md) de nome agregado para saber mais sobre a funcionalidade de espaço de nome virtual.

Quando terminar, clique em **OK** para adicionar o alvo de armazenamento.

> [!NOTE]
> Se a firewall da sua conta de armazenamento estiver definida para restringir o acesso apenas a "redes selecionadas", utilize a supor a suver temporária documentada em torno das definições de firewall da conta de [armazenamento Blob](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Adicione as funções de controlo de acesso à sua conta

A Azure HPC Cache utiliza [o controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) para autorizar o serviço de cache a aceder à sua conta de armazenamento para alvos de armazenamento Da Blob Azure.

O proprietário da conta de armazenamento deve adicionar explicitamente as funções [De Contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) de Conta de Armazenamento e Contribuinte de Dados [blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) de armazenamento para o utilizador "HPC Cache Resource Provider".

Pode fazê-lo com antecedência ou clicando num link na página onde adiciona um alvo de armazenamento Blob. Tenha em mente que pode demorar até cinco minutos para as definições de funções se propagarem através do ambiente Azure, pelo que deve esperar alguns minutos depois de adicionar as funções antes de criar um alvo de armazenamento.

Passos para adicionar as funções RBAC:

1. Abra a página de controlo de **acesso (IAM)** para a conta de armazenamento. (O link na **página-alvo** de armazenamento adicionar abre automaticamente esta página para a conta selecionada.)

1. Clique **+** no topo da página e escolha Adicionar uma atribuição de **funções**.

1. Selecione a função "Contribuinte de Conta de Armazenamento" da lista.

1. No **acesso atribuído ao** campo, deixe o valor predefinido selecionado ("Utilizador, grupo ou diretor de serviço Azure AD").  

1. No campo **Select,** procure por "hpc".  Esta cadeia deve coincidir com um diretor de serviço, denominado "HPC Cache Resource Provider". Clique no diretor para selecioná-lo.

   > [!NOTE]
   > Se uma pesquisa por "hpc" não funcionar, tente utilizar a corda "storagecache" em vez disso. Os utilizadores que participaram em pré-visualizações (antes da GA) podem ter de usar o nome mais antigo para o diretor de serviço.

1. Clique no botão **Guardar** na parte inferior.

1. Repita este processo para atribuir a função "Contribuinte de Dados blob de armazenamento".  

![screenshot de adicionar role assignment GUI](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Adicione um novo alvo de armazenamento NFS

Um alvo de armazenamento NFS tem mais campos do que o alvo de armazenamento blob. Estes campos especificam como chegar à exportação de armazenamento e como cache eficientemente os seus dados. Além disso, um alvo de armazenamento NFS permite criar múltiplos caminhos de espaço de nome se o hospedeiro NFS tiver mais do que uma exportação disponível.

![Screenshot de adicionar página-alvo de armazenamento com alvo NFS definido](media/add-nfs-target.png)

> [!NOTE]
> Antes de criar um alvo de armazenamento NFS, certifique-se de que o seu sistema de armazenamento está acessível a partir do Cache Azure HPC e satisfaz os requisitos de permissão. A criação do alvo de armazenamento falhará se a cache não conseguir aceder ao sistema de armazenamento. Leia os requisitos de [armazenamento nFS](hpc-cache-prereqs.md#nfs-storage-requirements) e a [configuração da NAS de Troubleshoot e os problemas de alvo de armazenamento NFS](troubleshoot-nas.md) para mais detalhes.

Forneça estas informações para um alvo de armazenamento apoiado pela NFS:

* **Nome alvo** de armazenamento - Defina um nome que identifique este alvo de armazenamento na Cache Azure HPC.

* **Tipo de destino** - Escolha **NFS**.

* **Nome de anfitrião** - Introduza o endereço IP ou nome de domínio totalmente qualificado para o seu sistema de armazenamento NFS. (Utilize um nome de domínio apenas se a sua cache tiver acesso a um servidor DNS que possa resolver o nome.)

* Modelo de **utilização** - Escolha um dos perfis de cache de dados com base no seu fluxo de trabalho, descrito no [Choose a use model](#choose-a-usage-model), abaixo.

### <a name="nfs-namespace-paths"></a>Caminhos espaciais de nome NFS

Um alvo de armazenamento NFS pode ter múltiplos caminhos virtuais, desde que cada caminho represente uma exportação ou subdireção diferente no mesmo sistema de armazenamento.

Crie todos os caminhos a partir de um alvo de armazenamento.

Pode [adicionar e editar caminhos de espaço](hpc-cache-edit-storage.md) de nome num alvo de armazenamento a qualquer momento.

Preencha estes valores para cada caminho espaço-nome:

* **Caminho de espaço** de nome virtual - Delineie o caminho de ficheiro virado para o cliente para este alvo de armazenamento. Leia [configurar espaço](hpc-cache-namespace.md) de nome agregado para saber mais sobre a funcionalidade de espaço de nome virtual.

* Rota de **exportação nFS** - Insira o caminho para a exportação de NFS.

* Caminho do **subdiretório** - Se quiser montar um subdiretório específico da exportação, insira-o aqui. Se não, deixe este campo em branco.

Quando terminar, clique em **OK** para adicionar o alvo de armazenamento.

### <a name="choose-a-usage-model"></a>Escolha um modelo de utilização
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Quando cria um alvo de armazenamento que aponta para um sistema de armazenamento NFS, tem de escolher o modelo de utilização para esse alvo. Este modelo determina como os seus dados estão em cache.

Existem três opções:

* **Ler escritas pesadas e pouco frequentes** - Use esta opção se quiser acelerar o acesso a ficheiros que sejam estáticos ou raramente alterados.

  Esta opção caches ficheiros que os clientes lêem, mas passa escritos através do armazenamento de back-end imediatamente. Os ficheiros armazenados na cache nunca são comparados com os ficheiros do volume de armazenamento NFS.

  Não utilize esta opção se houver o risco de um ficheiro poder ser modificado diretamente no sistema de armazenamento sem antes o escrever à cache. Se isso acontecer, a versão em cache do ficheiro nunca será atualizada com alterações a partir da parte de trás, e o conjunto de dados pode tornar-se inconsistente.

* **Mais de 15% escreve** - Esta opção acelera tanto a leitura como a escrita. Ao utilizar esta opção, todos os clientes devem aceder a ficheiros através do Cache Azure HPC em vez de montarem o armazenamento de back-end diretamente. Os ficheiros em cache terão alterações recentes que não são armazenadas na parte de trás.

  Neste modelo de utilização, os ficheiros na cache não são verificados com os ficheiros no armazenamento traseiro. A versão em cache do ficheiro é assumida como mais atual. Um ficheiro modificado na cache é escrito no sistema de armazenamento traseiro depois de ter estado na cache durante uma hora sem alterações adicionais.

* **Os clientes escrevem para o alvo nFS, contornando a cache** - Escolha esta opção se algum cliente no seu fluxo de trabalho escrever dados diretamente para o sistema de armazenamento sem primeiro escrever para a cache. Os ficheiros que os clientes solicitam estão em cache, mas quaisquer alterações nos ficheiros do cliente são imediatamente transmitidas para o sistema de armazenamento de back-end.

  Com este modelo de utilização, os ficheiros na cache são frequentemente verificados com as versões traseiras para atualizações. Esta verificação permite que os ficheiros sejam alterados fora da cache, mantendo a consistência dos dados.

Esta tabela resume as diferenças do modelo de utilização:

| Modelo de utilização | Modo caching | Verificação de back-end | Atraso máximo de drecção |
| ---- | ---- | ---- | ---- |
| Ler escritos pesados e pouco frequentes | Leitura | Nunca | Nenhuma |
| Maior de 15% escreve | Leitura/escrita | Nunca | Uma hora |
| Os clientes contornam a cache | Leitura | 30 segundos | Nenhuma |

## <a name="next-steps"></a>Passos seguintes

Depois de criar metas de armazenamento, considere uma destas tarefas:

* [Monte a Cache Azure HPC](hpc-cache-mount.md)
* [Mova dados para o armazenamento de Blob Azure](hpc-cache-ingest.md)

Se precisar de atualizar quaisquer definições, pode [editar um alvo](hpc-cache-edit-storage.md)de armazenamento .
