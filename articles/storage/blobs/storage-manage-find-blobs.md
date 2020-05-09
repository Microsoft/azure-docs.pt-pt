---
title: Gerir e encontrar dados sobre o Armazenamento de Blob Azure com índice blob (pré-visualização)
description: Aprenda a usar tags de Blob Index para categorizar, gerir e consultar para descobrir objetos blob.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: f1a4d9af8a1b1095527078dd790e80ef45a5ee9a
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722898"
---
# <a name="manage-and-find-data-on-azure-blob-storage-with-blob-index-preview"></a>Gerir e encontrar dados sobre o Armazenamento de Blob Azure com índice blob (pré-visualização)

À medida que os conjuntos de dados se acrescem cada vez mais, encontrar um objeto específico num mar de dados pode ser difícil e frustrante. O Blob Index fornece capacidades de gestão e descoberta de dados utilizando atributos de etiqueta de índice de valor-chave, que lhe permitem categorizar e encontrar objetos dentro de um único recipiente ou em todos os recipientes da sua conta de armazenamento. Mais tarde, à medida que os requisitos dos dados mudam, os objetos podem ser recategorizados dinamicamente atualizando as suas tags de índice, mantendo-se no local com a sua organização de contentores atual. A utilização do Blob Index pode simplificar o desenvolvimento consolidando os seus dados blob e atribuições de índice associadas no mesmo serviço; permitindo-lhe construir aplicações eficientes e escaláveis usando características nativas. 

O Índice Blob permite-lhe:

- Categorize dinamicamente as suas bolhas utilizando tags de índice de valor-chave para a gestão de dados
- Encontre rapidamente bolhas marcadas específicas através de um único recipiente ou de uma conta de armazenamento inteira
- Especificar comportamentos condicionais para APIs blob com base na avaliação de tags de índices
- Utilize tags de índice para controlos avançados em funcionalidades da plataforma blob como [gestão de ciclo de vida](storage-lifecycle-management-concepts.md)

Considere o cenário em que tem milhões de bolhas na sua conta de armazenamento escritas e acedidas por muitas aplicações diferentes. Você quer encontrar todos os dados relacionados a partir de um único projeto, mas você não tem certeza do que está no âmbito, uma vez que os dados podem ser espalhados por vários recipientes com diferentes convenções de nomeação blob. No entanto, sabe que as suas aplicações fazem upload de todos os dados com etiquetas com base no respetivo projeto e descrição de identificação. Em vez de pesquisar milhões de bolhas e comparar nomes `Project = Contoso` e propriedades, você pode simplesmente usar como critérios de descoberta. O Índice Blob filtrará todos os recipientes em toda a sua conta `Project = Contoso`de armazenamento para encontrar e devolver rapidamente apenas o conjunto de 50 bolhas de . 

Para começar com exemplos sobre como usar o Índice [Blob, consulte o Índice de Blob utilize para gerir e encontrar dados](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Tags de Índice blob e gestão de dados

Os prefixos de nome contentor e de blob são categorização unidimensional para os seus dados armazenados. O Índice Blob permite agora a categorização de várias dimensões para todos os tipos de [dados blob (Bloco, Apêndice ou Página)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) com etiquetas de atributo aplicadas. Esta categorização multidimensional é indexada de forma nativa e disponibilizada para que você faça rapidamente consulta e encontre os seus dados.

Considere as seguintes cinco bolhas na sua conta de armazenamento:
>
> contentor1/transaction.csv  
> contentor2/campanha.docx  
> fotos/bannerphoto.png  
> arquivos/concluído/2019review.pdf  
> registos/2020/01/01/logfile.txt  
>

Estas bolhas estão atualmente separadas utilizando um prefixo de recipiente/pasta virtual/nome blob. Com o Índice Blob, pode definir `Project = Contoso` um atributo de etiqueta de índice nestas cinco bolhas para categorizá-las juntas, mantendo a sua organização prefixo atual. Isto elimina a necessidade de mover dados expondo a capacidade de filtrar e encontrar dados usando o índice multidimensional da plataforma de armazenamento.

## <a name="setting-blob-index-tags"></a>Definição de tags de índice blob

As tags do Índice Blob são atributos de valor-chave que podem ser aplicados a objetos novos ou existentes dentro da sua conta de armazenamento. Pode especificar as etiquetas de índice durante o processo de upload utilizando as operações PutBlob, PutBlockList ou CopyBlob e o cabeçalho opcional de x-ms-tags. Se já tiver bolhas na sua conta de armazenamento, pode ligar para SetBlobTags com um documento XML formatado especificando os atributos de etiqueta de índice blob no corpo do pedido. 

Considere as seguintes etiquetas de exemplos que podem ser definidas

Pode aplicar uma única etiqueta na sua bolha para descrever quando os seus dados foram concluídos.
>
> "Data processada" = '2020-01-01'
>
Pode aplicar várias tags na sua bolha para ser mais descritivo dos dados.
>
> "Projeto" = 'Contoso'  
> "Classificado" = 'Verdadeiro'  
> "Estado" = 'Não processado'  
> "Prioridade" = '01' 
>

Para modificar os atributos de etiqueta de índice existentes, tem primeiro de recuperar os atributos de etiqueta existentes, modificar os atributos de etiqueta e substituir pela operação SetBlobTags. Para remover todas as etiquetas de índice da bolha, ligue para a operação SetBlobTags sem atributos de etiqueta especificados. Como as tags de índice blob são um subrecurso ao conteúdo dos dados blob, o SetBlobTags não modifica nenhum conteúdo subjacente e não altera o último tempo modificado da bolha.

Os seguintes limites aplicam-se às etiquetas do Índice Blob:
- Cada bolha pode ter até 10 tags de índice blob
- As teclas de etiqueta devem estar entre 1 a 128 caracteres
- Os valores da etiqueta devem estar entre 0 a 256 caracteres
- As teclas e os valores são sensíveis à caixa
- As teclas de etiqueta e os valores suportam apenas os tipos de dados de cordas; quaisquer números ou caracteres especiais serão salvos como cordas
- As teclas e valores de etiqueta devem respeitar as seguintes regras de nomeação:
  - Caracteres numéricos alfa: a-z, A-Z, 0-9
  - Personagens especiais: espaço, mais, menos, período, cólon, igual, sublinhado, barra para a frente

## <a name="getting-and-listing-blob-index-tags"></a>Etiquetas do Índice de Obtenção e Listagem de Blob

As etiquetas de índice blob são armazenadas como um sub-recurso ao lado dos dados blob e podem ser recuperadas independentemente do conteúdo de dados blob subjacente. Uma vez definidas, as etiquetas de índice blob para uma única bolha podem ser recuperadas e revistas imediatamente com a operação GetBlobTags. A operação ListBlobs com `include:tags` parâmetro também devolverá todas as bolhas dentro de um recipiente, juntamente com as suas etiquetas de índice de blob aplicadas. 

Para quaisquer blobs com pelo menos 1 etiqueta de índice blob, a contagem x-ms-tag é devolvida nas operações ListBlobs, GetBlob e GetBlobProperties indicando a contagem de tags de índice blob que existe na bolha.

## <a name="finding-data-using-blob-index-tags"></a>Encontrar dados usando tags do Índice Blob

Com etiquetas de índice blob definidas nas suas bolhas, o motor de indexação expõe esses atributos chave/valor num índice multidimensional. Embora existam tags de índice na bolha e possam ser recuperadas imediatamente, pode demorar algum tempo até que o índice de blob seja atualizado com os atributos de etiqueta de índice renovados. Uma vez atualizado o índice de blob, pode agora aproveitar as capacidades de consulta e descoberta nativas oferecidas pelo armazenamento de blob.

A operação FindBlobsByTags permite-lhe obter um conjunto de bolhas de retorno filtrado cujas tags de índice correspondem a uma determinada expressão de consulta de índice de bolha. O Índice Blob suporta a filtragem em todos os recipientes da sua conta de armazenamento ou pode analisar a filtragem para apenas um único recipiente. Uma vez que todas as teclas e valores do índice blob são cordas, os operadores relacionais suportados usam uma classificação lexicográfica nos valores da etiqueta de índice.

Os seguintes critérios aplicam-se à filtragem do índice de bolha:
-   As teclas de etiqueta devem ser fechadas em cotações duplas (")
-   Os valores de etiqueta e os nomes dos contentores devem ser incluídos em cotações únicas (')
-   O carácter @ só é permitido para filtrar um @container nome de recipiente específico (isto é, = 'Nome do recipiente')
- Os filtros são aplicados com classificação lexicográfica nas cordas
-   As mesmas operações de alcance na mesma tecla são inválidas (isto é, "Rank" > '10' E "Rank" >= '15')
- Ao utilizar o REST para criar uma expressão de filtro, os caracteres devem ser codificados por URI

O quadro abaixo mostra todos os operadores válidos para FindBlobsByTags:

|  Operador  |  Descrição  | Exemplo |
|------------|---------------|---------|
|     =      |     Igual     | "Status" = 'Em curso' | 
|     >      |  Maior que |  "Data" > '2018-06-18' |
|     >=     |  Maior ou igual a | "Prioridade" >= '5' | 
|     <      |  Menor do que    | "Idade" < '32' |
|     <=     |  Menor ou igual a  | "Empresa" <= 'Contoso' |
|    AND     |  Lógico e  | "Rank" >= '010' E "Rank" < '100' |
| @container |  Âmbito de aplicação para um recipiente específico   | @container= 'videofiles' E "status" = 'done' |

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Operações de Blob condicionais com tags do Índice Blob
Nas versões REST 2019-10-10 e superiores, a maioria das APIs de [serviço blob](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) suportam agora um cabeçalho condicional, x-ms-if-tags, de modo que a operação só terá sucesso se a condição especificada do índice de blob for satisfeita. Se a condição não for `error 412: The condition specified using HTTP conditional header(s) is not met`satisfeita, terá.

O cabeçalho x-ms-if-tags pode ser combinado com os outros cabeçalhos condicionados HTTP existentes (Se-Match, If-None-Match, etc.).  Se vários cabeçalhos condicionales forem fornecidos num pedido, todos devem avaliar verdadeiramente para que a operação tenha sucesso.  Todos os cabeçalhos condicionalsão efetivamente combinados com lógicos e. 

O quadro abaixo mostra todos os operadores válidos para operações condicionais:

|  Operador  |  Descrição  | Exemplo |
|------------|---------------|---------|
|     =      |     Igual     | "Status" = 'Em curso' |
|     <>     |   Diferente   | "Status" <> 'Done'  | 
|     >      |  Maior que |  "Data" > '2018-06-18' |
|     >=     |  Maior ou igual a | "Prioridade" >= '5' | 
|     <      |  Menor do que    | "Idade" < '32' |
|     <=     |  Menor ou igual a  | "Empresa" <= 'Contoso' |
|    AND     |  Lógico e  | "Rank" >= '010' E "Rank" < '100' |
|     OU     |  Lógico ou   | "Status" = 'Done' OU "Prioridade" >= '05' |

> [!NOTE]
> Existem dois operadores adicionais, não iguais e lógicos ou, que são permitidos no cabeçalho condicional x-ms-if-tags para a operação blob, mas não existem na operação FindBlobsByTags.

## <a name="platform-integrations-with-blob-index-tags"></a>Integrações de plataforma com tags de Índice Blob

As tags do Blob Index não só ajudam a categorizá-lo, gerir e pesquisar os seus dados blob, mas também fornecer integrações com outras funcionalidades do serviço Blob, como a gestão do [ciclo de vida.](storage-lifecycle-management-concepts.md) 

### <a name="lifecycle-management"></a>Gestão do Ciclo de Vida

Utilizando o novo blobIndexMatch como filtro de regras na gestão do ciclo de vida, pode mover dados para níveis mais frios ou eliminar dados com base nas tags de índice aplicadas às suas bolhas. Isto permite-lhe ser mais granular nas suas regras e apenas mover ou eliminar dados se corresponderem aos critérios de etiquetas especificados.

Pode definir uma correspondência de índice de bolha como um filtro autónomo definido numa regra de ciclo de vida para aplicar ações em dados marcados. Ou pode combinar tanto uma correspondência de pré-fixação como uma correspondência de índice de bolha para combinar com conjuntos de dados mais específicos. Aplicar vários filtros a uma regra de ciclo de vida é uma operação lógica e de tal forma que a ação só se aplicará se todos os critérios de filtro corresponderem. 

A seguinte regra de gestão do ciclo de vida da amostra aplica-se ao bloqueio de bolhas no contentor 'videofiles' ```"Status" = 'Processed' AND "Source" == 'RAW'```e as bolhas de nível para arquivar o armazenamento apenas se os dados corresponderem aos critérios de etiqueta do índice blob de .

# <a name="portal"></a>[Portal](#tab/azure-portal)
![Blob Index corresponde à regra da gestão do ciclo de vida no portal Azure](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)
   
# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>Permissões e Autorizações

Pode autorizar o acesso ao Blob Index utilizando uma das seguintes abordagens:

- Utilizando o controlo de acesso baseado em funções (RBAC) para conceder permissões a um diretor de segurança do Azure Ative Directory (Azure AD). A Microsoft recomenda a utilização de AD Azure para uma segurança superior e facilidade de utilização. Para obter mais informações sobre a utilização de Azure AD com operações blob, consulte [Autorizar o acesso a blobs e filas utilizando o Diretório Ativo Azure](../common/storage-auth-aad.md).
- Utilizando uma assinatura de acesso partilhado (SAS) para delegar o acesso ao índice blob. Para obter mais informações sobre assinaturas de acesso partilhado, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).
- Utilizando as chaves de acesso à conta para autorizar operações com chave partilhada. Para mais informações, consulte [Autorizar com chave partilhada](/rest/api/storageservices/authorize-with-shared-key).

As etiquetas do Índice Blob são um subrecurso aos dados blob. Um utilizador com permissões ou um token SAS para ler ou escrever bolhas pode não ter acesso às etiquetas do índice blob. 

### <a name="role-based-access-control"></a>Controlo de Acesso Baseado em Funções 
Os chamadores que utilizem uma [identidade AAD](../common/storage-auth-aad.md) podem ser concedidos as seguintes permissões para operar em etiquetas de índice blob. 

|   Operações blob   |  Ação RBAC   |
|---------------------|----------------|
| Encontre Blobs por Tags  | Microsoft.Armazenamento/armazenamentoContas/blobServices/contentores/blobs/filter |
| Definir etiquetas blob         | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | 
| Obter etiquetas blob         | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read |

São necessárias permissões adicionais separadas dos dados de bolha subjacentes para operar em cima das etiquetas. O papel de Contribuinte de Dados blob de armazenamento será concedido todas estas três permissões. O Leitor de Dados de Blob de Armazenamento receberá as únicas permissões Find Blobs by Tags e Obter blob tags permissões.

### <a name="sas-permissions"></a>Permissões SAS 
Os chamadores que utilizem uma assinatura de [acesso partilhado (SAS)](../common/storage-sas-overview.md) podem ser concedidas permissões de âmbito para operar em etiquetas blob.
#### <a name="blob-sas"></a>Blob SAS
As seguintes permissões podem ser concedidas num serviço Blob SAS para permitir o acesso a tags de índice blob. As permissões Blob Read and Write por si só não são suficientes para permitir a leitura ou a escrita das suas tags indexadas.

|  Permissão  |  Símbolo URI  | Operações permitidas |
|--------------|--------------|--------------------|
|  Tags de índice  |      t         | Obter e definir etiquetas de índice blob para uma bolha |

#### <a name="container-sas"></a>SAS de contentores
As seguintes permissões podem ser concedidas no serviço de contentores SAS para permitir a filtragem em etiquetas de bolhas.  A permissão da Lista Blob não é suficiente para permitir a filtragem de bolhas pelas suas tags de índice.

|  Permissão  |  Símbolo URI  | Operações permitidas |
|--------------|--------------|--------------------|
| Tags de índice     |      f       | Encontre Blobs com tags de índice blob | 

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Escolha entre metadados e etiquetas de índice blob 
Tanto as tags e metadados do Blob Index fornecem a capacidade de armazenar propriedades arbitrárias definidas pelo utilizador ao lado de um recurso blob. Ambos podem ser recuperados e definidos diretamente, sem devolver ou alterar o conteúdo da bolha. É possível utilizar tanto metadados como tags de índice.

No entanto, apenas as tags do Índice Blob são automaticamente indexadas e tornadas consultadas pelo serviço de blob nativo. Os metadados não podem ser indexados e consultados de forma nativa, a menos que utilize um serviço separado, como [o Azure Search](../../search/search-blob-ai-integration.md). As tags do Índice Blob também têm permissões adicionais para leitura/filtragem e escrita separadas dos dados de blob subjacentes. Os metadados utilizam as mesmas permissões que a bolha e são devolvidos como cabeçalhos HTTP nas operações GetBlob ou GetBlobProperties. As tags do Índice Blob são encriptadas em repouso utilizando uma [chave gerida pela Microsoft,](../common/storage-service-encryption.md) enquanto os metadados são encriptados em repouso utilizando a mesma chave de encriptação especificada para os dados blob. 

O quadro seguinte resume as diferenças entre metadados e etiquetas do Índice blob:

|              |   Metadados   |   Tags de Índice blob  |
|--------------|--------------|--------------------|
| **Limites**         | Sem limite numérico; 8 KB total; caso insensível | 10 tags por blob max; 768 bytes por etiqueta; caso sensível |
| **Updates**      | Não é permitido no nível de arquivo; SetBlobMetadata substitui todos os metadados existentes; SetBlobMetadata altera o último tempo modificado da bolha | Permitido para todos os níveis de acesso; SetBlobTags substitui todas as etiquetas existentes; SetBlobTags não altera o último tempo modificado da bolha |
| **Armazenamento**        | Armazenado com os dados blob |  Sub-recurso para os dados blob | 
| **Consulta de & de indexação** | N/A nativamente; deve usar um serviço separado, como a Pesquisa Azure | Sim, capacidades de indexação e consulta nativas incorporadas no armazenamento de bolhas |
| **Encriptação** | Encriptado em repouso com a mesma chave de encriptação usada para dados blob |  Encriptado em repouso com uma chave de encriptação gerida pela Microsoft |
| **Preços**   | O tamanho dos metadados está incluído nos custos de armazenamento de uma bolha |    Custo fixo por etiqueta de índice | 
| **Resposta cabeçalho** | Metadados devolvidos como cabeçalhos no GetBlob e no GetBlobProperties | TagCount devolvido em GetBlob ou GetBlobProperties; Tags devolvidas apenas em GetBlobTags e ListBlobs |
| **Permissões**  |    Ler ou escrever permissões para blob dados estende-se a metadados |    São necessárias permissões adicionais para ler/filtrar ou escrever etiquetas |

## <a name="pricing"></a>Preços
Os preços do Índice Blob estão atualmente em pré-visualização pública e sujeitos a alterações para a disponibilidade geral. Os clientes são cobrados pelo número total de tags de índice blob dentro de uma conta de armazenamento, média ao longo do mês. Não há custo para o motor de indexação. Os pedidos de SetBlobTags, GetBlobTags e FindBlobsByTags são cobrados de acordo com os respetivos tipos de operação. Consulte [os preços do Block Blob para saber mais](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Apoio à conta de disponibilidade e armazenamento regional

O Índice Blob está atualmente disponível apenas com contas De Propósito Geral v2 (GPv2). No portal Azure, pode atualizar uma conta de Propósito Geral (GPv1) existente para uma conta GPv2. Para obter mais informações sobre contas de armazenamento, consulte a visão geral da conta de [armazenamento do Azure.](../common/storage-account-overview.md)

Na pré-visualização pública, o Blob Index está atualmente disponível apenas nas seguintes regiões selecionadas:
- França Central
- Sul de França

Para começar, consulte [o Índice de Blob Utilize para gerir e encontrar dados](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Consulte a secção de condições deste artigo. Para se inscrever na pré-visualização, consulte a secção de subscrição deste artigo. Tem de registar a sua subscrição antes de poder utilizar o Blob Index nas suas contas de armazenamento.

### <a name="register-your-subscription-preview"></a>Registe a sua subscrição (Pré-visualização)
Como o Índice Blob está apenas em pré-visualização pública, terá de registar a sua subscrição antes de poder utilizar a funcionalidade. Para submeter um pedido, execute os seguintes comandos PowerShell ou CLI.

#### <a name="register-by-using-powershell"></a>Registe-se utilizando o PowerShell
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Registe-se utilizando o Azure CLI
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>Condições e questões conhecidas (Pré-visualização)
Esta secção descreve questões e condições conhecidas na atual pré-visualização pública do Índice de Blob. Tal como acontece com a maioria das pré-visualizações, esta funcionalidade não deve ser utilizada para cargas de trabalho de produção até atingir a GA, uma vez que os comportamentos podem mudar.

-   Para pré-visualização, primeiro deve registar a sua subscrição antes de poder utilizar o Blob Index para a sua conta de armazenamento nas regiões de pré-visualização.
-   Apenas as contas GPv2 são suportadas atualmente na pré-visualização. As contas Blob, BlockBlobStorage e HNS ativadas por DataLake Gen2 não são atualmente suportadas com o Blob Index.
-   O upload de bolhas de página com tags de índice atualmente não persiste nas tags. Deve definir as etiquetas depois de fazer o upload de uma bolha de página.
-   Quando a filtragem é filtrada @container para um único recipiente, a única pode ser passada se todas as etiquetas de índice na expressão do filtro forem verificações de igualdade (key=value). 
-   Ao utilizar o operador de gama com a condição E, só pode especificar o mesmo nome de etiqueta de índice (Idade > '013' E Idade < '100').
-   A versão e o Índice Blob não são atualmente suportados. As etiquetas do Índice Blob são preservadas para versões, mas atualmente não são passadas para o motor do índice blob.
-   Atualmente, o failover da conta não é suportado. O índice blob pode não atualizar corretamente após a falha.
-   Atualmente, a gestão do ciclo de vida apenas suporta verificações de igualdade com blob Index Match.
-   CopyBlob não copia etiquetas de índice blob da bolha de origem para a nova bolha de destino. Pode especificar as etiquetas que deseja aplicadas à bolha de destino durante a operação de cópia. 
-   As etiquetas são persistidas na criação instantânea; no entanto, a promoção de um instantâneo não é atualmente suportada e pode resultar num conjunto de etiquetas vazias.

## <a name="faq"></a>FAQ

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>O Blob Index pode ajudar-me a filtrar e consultar o conteúdo dentro das minhas bolhas? 
Não, as etiquetas do Índice Blob podem ajudá-lo a encontrar as bolhas que procura. Se precisar de pesquisar dentro das suas bolhas, utilize a Query Acceleration ou a Azure Search.

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>As tags do Blob Index e o Gestor de Recursos Azure estão relacionadas?
Não, as etiquetas do Gestor de Recursos Azure ajudam a organizar o controlo de recursos de planos, tais como subscrições, grupos de recursos e contas de armazenamento. As tags do Índice Blob fornecem gestão de objetos e descobertas em recursos de planos de dados, tais como bolhas dentro de uma conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Veja um exemplo de como utilizar o Índice Blob. Consulte [o Índice de Blob utilize para gerir e encontrar dados](storage-blob-index-how-to.md)

