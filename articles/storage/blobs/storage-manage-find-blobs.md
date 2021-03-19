---
title: Gerir e encontrar dados do Azure Blob com etiquetas de índice blob (pré-visualização)
description: Aprenda a usar tags de índice blob para categorizar, gerir e consultar objetos blob.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/05/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: be2ff9d6ed908bdc0645fc713717a08674354096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593225"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Gerir e encontrar dados do Azure Blob com etiquetas de índice blob (pré-visualização)

À medida que os conjuntos de dados se en dimensão, encontrar um objeto específico num mar de dados pode ser difícil. As tags de índice blob fornecem capacidades de gestão de dados e de descoberta utilizando atributos de etiqueta de índice de valor-chave. Pode categorizar e encontrar objetos dentro de um único recipiente ou em todos os contentores da sua conta de armazenamento. À medida que os requisitos de dados mudam, os objetos podem ser classificados dinamicamente atualizando as suas etiquetas de índice. Os objetos podem permanecer no lugar com a sua organização de contentores atual.

As etiquetas de índice blob permitem::

- Categorize dinamicamente as suas bolhas usando etiquetas de índice de valor-chave
- Rapidamente encontre bolhas marcadas específicas em toda uma conta de armazenamento
- Especificar comportamentos condicionais para APIs blob com base na avaliação das etiquetas de índice
- Use etiquetas de índice para controlos avançados em funcionalidades como [a gestão do ciclo de vida blob](storage-lifecycle-management-concepts.md)

Considere um cenário em que tenha milhões de bolhas na sua conta de armazenamento, acedidas por várias aplicações diferentes. Você quer encontrar todos os dados relacionados de um único projeto. Não tem a certeza do que está no âmbito, pois os dados podem ser espalhados por vários contentores com diferentes convenções de nomeação. No entanto, as suas aplicações carregam todos os dados com tags com base no seu projeto. Em vez de pesquisar por milhões de bolhas e comparar nomes e propriedades, pode usar `Project = Contoso` como critérios de descoberta. O índice Blob filtrará todos os recipientes em toda a sua conta de armazenamento para encontrar e devolver rapidamente apenas o conjunto de 50 bolhas de `Project = Contoso` .

> [!IMPORTANT]
> As etiquetas de índice blob estão atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Para começar com exemplos sobre como usar o índice blob, consulte [use tags de índice blob para gerir e encontrar dados](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Etiquetas de índice blob e gestão de dados

Os prefixos de contentores e nomes blob são categorizações unidimensionais. As etiquetas de índice blob permitem a categorização multidimensional para [tipos de dados blob (Bloco, Apêndice ou Página)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs). A categorização multidimensional é indexada de forma nativa pelo Azure Blob Storage para que possa encontrar rapidamente os seus dados.

Considere as seguintes cinco bolhas na sua conta de armazenamento:

- *contentor1/transaction.csv*
- *contentor/campaign.docx*
- *fotos/bannerphoto.png*
- *arquivos/concluídos/2019review.pdf*
- *registos/2020/01/01/logfile.txt*

Estas bolhas são separadas utilizando um prefixo de *recipiente/pasta virtual/nome de bolha*. Pode definir um atributo de etiqueta de índice `Project = Contoso` nestas cinco bolhas para categorizá-los juntos, mantendo a sua organização atual de prefixo. A adição de tags de índice elimina a necessidade de mover dados expondo a capacidade de filtrar e encontrar dados usando o índice.

## <a name="setting-blob-index-tags"></a>Definição de etiquetas de índice de blob

As etiquetas de índice blob são atributos de valor-chave que podem ser aplicados a objetos novos ou existentes dentro da sua conta de armazenamento. Pode especificar etiquetas de índice durante o processo de upload utilizando as operações [Put Blob,](/rest/api/storageservices/put-blob) [Put Block List](/rest/api/storageservices/put-block-list)ou Copy [Blob](/rest/api/storageservices/copy-blob) e o `x-ms-tags` cabeçalho opcional. Se já tiver bolhas na sua conta de armazenamento, ligue para [definir Tags Blob](/rest/api/storageservices/set-blob-tags) passando um documento XML formatado com as etiquetas de índice no corpo do pedido.

> [!IMPORTANT]
> A definição de etiquetas de índice blob pode ser realizada pelo Proprietário de [Dados de Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) e por qualquer pessoa com uma Assinatura de Acesso Partilhado que tenha permissão para aceder às tags do blob (a `t` permissão SAS).
>
> Além disso, os utilizadores do RBAC com a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` permissão podem realizar esta operação.

Pode aplicar uma única etiqueta na sua bolha para descrever quando os seus dados terminaram o processamento.

> "Processo" = '2020-01-01'

Pode aplicar várias tags na sua bolha para ser mais descritivo dos dados.

> "Projeto" = 'Contoso'  
> "Classificado" = 'Verdadeiro'  
> "Status" = 'Não processado'  
> "Prioridade" = '01'

Para modificar os atributos de etiqueta de índice existentes, recupere os atributos de etiqueta existentes, modifique os atributos da etiqueta e substitua-os pela operação [set Blob Tags.](/rest/api/storageservices/set-blob-tags) Para remover todas as etiquetas de índice da bolha, ligue para a `Set Blob Tags` operação sem atributos de etiqueta especificados. Como as tags de índice blob são uma subresource para o conteúdo de dados blob, `Set Blob Tags` não modifica nenhum conteúdo subjacente e não altera o último tempo modificado ou eTag do blob. Pode criar ou modificar tags de índice para todas as bolhas de base atuais e versões anteriores. No entanto, as etiquetas em instantâneos ou bolhas apagadas suaves não podem ser modificadas.

Aplicam-se os seguintes limites às etiquetas de índice blob:

- Cada bolha pode ter até 10 tags de índice de bolhas
- As teclas de etiqueta devem estar entre um e 128 caracteres
- Os valores da etiqueta devem estar entre zero e 256 caracteres
- As chaves e valores da etiqueta são sensíveis ao caso
- Etiquetar chaves e valores apenas suportam tipos de dados de cadeia. Quaisquer números, datas, horas ou caracteres especiais são guardados como cordas
- As teclas e valores da etiqueta devem respeitar as seguintes regras de nomeação:
  - Caracteres alfanuméricos:
    - **a** através de **z** (letras minúsculas)
    - **A** a **Z** (letras maiúsculas)
    - **0** a **9** (números)
  - Caracteres especiais válidos: espaço, mais, menos, período, cólon, igual, sublinhado, barra para a frente ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>Obtenção e listagem de etiquetas de índice de blob

As tags blob index são armazenadas como um subresource ao lado dos dados blob e podem ser obtidas independentemente do conteúdo subjacente de dados blob. As etiquetas de índice blob para uma única bolha podem ser recuperadas com a operação [Get Blob Tags.](/rest/api/storageservices/get-blob-tags) O funcionamento [das Blobs list](/rest/api/storageservices/list-blobs) com o `include:tags` parâmetro também devolverá todas as bolhas dentro de um recipiente, juntamente com as suas etiquetas de índice de bolhas.

> [!IMPORTANT]
> Obter e listar as etiquetas do índice blob pode ser realizada pelo Proprietário de [Dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) e por qualquer pessoa com uma Assinatura de Acesso Partilhado que tenha permissão para aceder às tags do blob (a `t` permissão SAS).
>
> Além disso, os utilizadores do RBAC com a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` permissão podem realizar esta operação.

Para quaisquer blobs com pelo menos uma etiqueta de índice blob, a `x-ms-tag-count` é devolvida nas operações List [Blobs](/rest/api/storageservices/list-blobs), [Get Blob](/rest/api/storageservices/get-blob)e [Get Blob Properties](/rest/api/storageservices/get-blob-properties) indicando a contagem de etiquetas de índice na bolha.

## <a name="finding-data-using-blob-index-tags"></a>Encontrar dados usando tags de índice de bolhas

O motor de indexação expõe os seus atributos de valor-chave num índice multidimensional. Depois de definir as suas etiquetas de índice, elas existem na bolha e podem ser recuperadas imediatamente. Pode levar algum tempo até que o índice blob atualize. Após as atualizações do índice blob, pode utilizar as capacidades de consulta e descoberta nativas oferecidas pelo Blob Storage.

A operação [Find Blobs by Tags](/rest/api/storageservices/find-blobs-by-tags) permite-lhe obter um conjunto filtrado de bolhas cujas etiquetas de índice correspondem a uma determinada expressão de consulta. `Find Blobs by Tags` suporta a filtragem em todos os recipientes dentro da sua conta de armazenamento ou pode passar a filtragem para apenas um único recipiente. Uma vez que todas as teclas e valores da etiqueta de índice são cordas, os operadores relacionais usam uma triagem lexicográfica.

> [!IMPORTANT]
> Encontrar dados utilizando tags de índice blob pode ser realizado pelo Proprietário de [Dados de Armazenamento Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) e por qualquer pessoa com uma Assinatura de Acesso Partilhado que tenha permissão para encontrar bolhas por tags (a `f` permissão SAS).
>
> Além disso, os utilizadores do RBAC com a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` permissão podem realizar esta operação.

Os seguintes critérios aplicam-se à filtragem do índice blob:

- As teclas de etiqueta devem ser incluídas em cotações duplas (")
- Os valores da etiqueta e os nomes dos contentores devem ser incluídos em cotações únicas (')
- O carácter @ só é permitido para filtragem num nome específico do recipiente (por exemplo, `@container = 'ContainerName'` )
- Os filtros são aplicados com classificação lexicográfica em cordas
- As mesmas operações de gama lateral na mesma tecla são inválidas (por exemplo, `"Rank" > '10' AND "Rank" >= '15'` )
- Ao usar o REST para criar uma expressão de filtro, os caracteres devem ser codificados URI

A tabela abaixo mostra todos os operadores válidos `Find Blobs by Tags` para:

|  Operador  |  Descrição  | Exemplo |
|------------|---------------|---------|
|     =      |     Igual     | `"Status" = 'In Progress'` |
|     >      |  Maior que | `"Date" > '2018-06-18'` |
|     >=     |  Maior ou igual a | `"Priority" >= '5'` |
|     <      |  Menor que   | `"Age" < '32'` |
|     <=     |  Menor ou igual a  | `"Company" <= 'Contoso'` |
|    AND     |  Lógico e  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | Âmbito de um recipiente específico | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> Esteja familiarizado com o pedido lexicográfico ao definir e consultar as etiquetas.
>
> - Os números são classificados antes das letras. Os números são classificados com base no primeiro dígito.
> - As letras maiúsculas são ordenadas antes das letras minúsculas.
> - Símbolos não são normais. Alguns símbolos são classificados antes dos valores numéricos. Outros símbolos são classificados antes ou depois das letras.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Operações de bolhas condicionais com etiquetas de índice de bolhas

Nas versões REST 2019-10-10 e superior, a maioria [das APIs de serviço blob](/rest/api/storageservices/operations-on-blobs) suportam agora um cabeçalho condicional, `x-ms-if-tags` de tal forma que a operação só terá sucesso se a condição de índice blob especificado for satisfeita. Se a condição não for cumprida, `error 412: The condition specified using HTTP conditional header(s) is not met` terá.

O `x-ms-if-tags` cabeçalho pode ser combinado com os outros cabeçalhos condicionales HTTP existentes (Se-Match, If-None-Match, e assim por diante). Se forem fornecidos vários cabeçalhos condicionales num pedido, todos devem avaliar a verdade para que a operação tenha sucesso. Todos os cabeçalhos condicional são efetivamente combinados com E lógicos.

A tabela abaixo mostra os operadores válidos para operações condicionais:

|  Operador  |  Descrição  | Exemplo |
|------------|---------------|---------|
|     =      |     Igual     | `"Status" = 'In Progress'` |
|     <>     |   Diferente   | `"Status" <> 'Done'` |
|     >      |  Maior que | `"Date" > '2018-06-18'` |
|     >=     |  Maior ou igual a | `"Priority" >= '5'` |
|     <      |  Menor que   | `"Age" < '32'` |
|     <=     |  Menor ou igual a  | `"Company" <= 'Contoso'` |
|    AND     |  Lógico e  | `"Rank" >= '010' AND "Rank" < '100'` |
|     OR     | Lógico ou   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Existem dois operadores adicionais, não iguais e lógicos ou, que são permitidos no cabeçalho condicional `x-ms-if-tags` para operações blob, mas não existem na `Find Blobs by Tags` operação.

## <a name="platform-integrations-with-blob-index-tags"></a>Integrações de plataformas com tags de índice blob

As etiquetas de índice blob não só o ajudam a categorizar, gerir e pesquisar os seus dados blob, mas também fornecer integração com outras funcionalidades de Blob Storage, como [a gestão do ciclo de vida.](storage-lifecycle-management-concepts.md)

### <a name="lifecycle-management"></a>Gestão do ciclo de vida

Utilizando o `blobIndexMatch` filtro como regra na gestão do ciclo de vida, pode mover dados para níveis mais frios ou eliminar dados com base nas etiquetas de índice aplicadas às suas bolhas. Pode ser mais granular nas suas regras e apenas mover ou apagar bolhas se corresponderem aos critérios de etiquetas especificados.

Pode definir uma correspondência de índice de bolha como um conjunto de filtro autónomo numa regra do ciclo de vida para aplicar ações em dados marcados. Ou pode combinar um prefixo e um índice de bolhas para combinar conjuntos de dados mais específicos. Especificar vários filtros numa regra de ciclo de vida aplica uma operação lógica E. A ação só será aplicada se *todos os* critérios de filtro corresponderem.

A seguinte regra de gestão do ciclo de vida da amostra aplica-se ao bloqueio de bolhas num recipiente chamado *videofiles*. As camadas de regras só armazenam para arquivar o armazenamento se os dados corresponderem aos critérios de etiqueta de índice blob de `"Status" == 'Processed' AND "Source" == 'RAW'` .

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Blob índice match regra exemplo para gestão de ciclo de vida no portal Azure](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

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

## <a name="permissions-and-authorization"></a>Permissões e autorização

Pode autorizar o acesso a tags de índice blob utilizando uma das seguintes abordagens:

- Utilizando o controlo de acesso baseado em funções Azure (Azure RBAC) para conceder permissões a um diretor de segurança Azure Ative (Azure AD). Utilize o Azure AD para uma segurança superior e facilidade de utilização. Para obter mais informações sobre a utilização do Azure AD com operações de [bolhas, consulte o Acesso autorizado a bolhas e filas utilizando o Azure Ative Directory](../common/storage-auth-aad.md).
- Utilizar uma assinatura de acesso partilhado (SAS) para delegar o acesso ao índice blob. Para obter mais informações sobre assinaturas de acesso partilhado, consulte [Grant acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).
- Utilizar as chaves de acesso à conta para autorizar operações com Chave Partilhada. Para mais informações, consulte [Autorizar com chave partilhada.](/rest/api/storageservices/authorize-with-shared-key)

As etiquetas de índice blob são uma subresource para os dados do blob. Um utilizador com permissões ou um token SAS para ler ou escrever bolhas pode não ter acesso às etiquetas do índice blob.

### <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Os chamadores que usam uma [identidade AD AZure](../common/storage-auth-aad.md) podem ter as seguintes permissões para operar em etiquetas de índice blob.

| Operações de etiqueta de índice de bolha                                          | Ação Azure RBAC                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [Definir tags blob](/rest/api/storageservices/set-blob-tags)           | Microsoft.Storage/storageAcounts/blobServices/containers/blobs/tags/write    |
| [Obter Tags Blob](/rest/api/storageservices/get-blob-tags)           | Microsoft.Storage/storageAcounts/blobServices/containers/blobs/tags/read     |
| [Encontre Blobs por Tags](/rest/api/storageservices/find-blobs-by-tags) | Microsoft.Storage/storageAcounts/blobServices/containers/blobs/filter/action |

São necessárias permissões adicionais, separadas dos dados subjacentes ao blob, para operações de etiqueta de índice. A função [de Proprietário de Dados Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) é concedida permissões para as três operações de etiqueta de índice de blob. O [Leitor de Dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) só tem permissões e `Find Blobs by Tags` `Get Blob Tags` operações.

### <a name="sas-permissions"></a>Permissões SAS

Os chamadores que utilizem uma [assinatura de acesso partilhado (SAS)](../common/storage-sas-overview.md) podem ter permissões de mira para operar em etiquetas de índice blob.

#### <a name="blob-sas"></a>Blob SAS

As seguintes permissões podem ser concedidas numa BOLHA SAS para permitir o acesso a tags de índice blob. As permissões de leitura e escrita blob por si só não são suficientes para permitir a leitura ou a escrita das suas etiquetas de índice.

| Permissão | Símbolo URI | Operações permitidas                |
|------------|------------|-----------------------------------|
| Etiquetas de índice |     t      | Obter e definir etiquetas de índice para uma bolha |

#### <a name="container-sas"></a>Contentor SAS

As seguintes permissões podem ser concedidas num recipiente SAS para permitir a filtragem em etiquetas de bolhas. A `Blob List` permissão não é suficiente para permitir a filtragem de bolhas pelas suas etiquetas de índice.

| Permissão | Símbolo URI | Operações permitidas         |
|------------|------------|----------------------------|
| Etiquetas de índice |     f      | Encontre bolhas com etiquetas de índice |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Escolha entre metadados e tags de índice blob

Tanto as tags de índice blob como os metadados fornecem a capacidade de armazenar propriedades arbitrárias de valor-chave definidas pelo utilizador ao lado de um recurso blob. Ambos podem ser recuperados e definidos diretamente, sem retornar ou alterar o conteúdo da bolha. É possível usar metadados e etiquetas de índice.

Apenas as etiquetas de índice são automaticamente indexadas e tornadas pescáveis pelo serviço nativo blob Storage. Os metadados não podem ser indexados ou pesquisados de forma nativa. Deve utilizar um serviço separado, como [a Azure Search](../../search/search-blob-ai-integration.md). As etiquetas do índice blob têm permissões adicionais para leitura, filtragem e escrita que são separadas dos dados subjacentes ao blob. Os metadados utilizam as mesmas permissões que a bolha e são devolvidos como cabeçalhos HTTP pelas operações [Get Blob](/rest/api/storageservices/get-blob) e [Get Blob Properties.](/rest/api/storageservices/get-blob-properties) As etiquetas de índice blob são encriptadas em repouso utilizando uma [chave gerida pela Microsoft](../common/storage-service-encryption.md). Os metadados são encriptados em repouso utilizando a mesma chave de encriptação especificada para dados blob.

O quadro a seguir resume as diferenças entre metadados e etiquetas de índice blob:

|              |   Metadados   |   Etiquetas de índice de bolha  |
|--------------|--------------|--------------------|
| **Limites**      | Sem limite numérico, 8 KB total, caso insensível | 10 tags por blob max, 768 bytes por tag, caso sensível |
| **Updates**    | Não é permitido no nível de arquivo, `Set Blob Metadata` substitui todos os metadados existentes, `Set Blob Metadata` altera o último tempo modificado da bolha | Permitido para todos os níveis de acesso, `Set Blob Tags` substitui todas as etiquetas existentes, `Set Blob Tags` não altera o último tempo modificado da bolha |
| **Armazenamento**     | Armazenada com os dados blob | Subreufonte dos dados blob |
| **Indexação da consulta &** | Deve utilizar um serviço separado, como a Azure Search | Capacidades de indexação e consulta incorporadas no Armazenamento blob |
| **Encriptação** | Encriptado em repouso com a mesma chave de encriptação usada para dados blob | Encriptado em repouso com uma chave de encriptação gerida pela Microsoft |
| **Preços** | O tamanho dos metadados está incluído nos custos de armazenamento de uma bolha | Custo fixo por etiqueta de índice |
| **Resposta do cabeçalho** | Metadados devolvidos como cabeçalhos em `Get Blob` e `Get Blob Properties` | Contagem de etiquetas devolvida por `Get Blob` `Get Blob Properties` ou, tags devolvidas apenas por `Get Blob Tags` e `List Blobs` |
| **Permissões**  | Ler ou escrever permissões para blob dados estende-se a metadados | São necessárias permissões adicionais para ler, filtrar ou escrever etiquetas de índice |
| **Atribuição de nomes** | Os nomes dos metadados devem aderir às regras de nomeação dos identificadores C# | As tags de índice blob suportam uma gama mais ampla de caracteres alfanuméricos |

## <a name="pricing"></a>Preços

Os preços do índice blob estão em pré-visualização pública e sujeitos a alterações para disponibilidade geral. É cobrado pelo número médio mensal de etiquetas de índice dentro de uma conta de armazenamento. Não há custo para o motor de indexação. Pedidos para `Set Blob Tags` `Get Blob Tags` , e são `Find Blobs by Tags` cobrados de acordo com os respetivos tipos de operação. Consulte [os preços do Block Blob para saber mais.](https://azure.microsoft.com/pricing/details/storage/blobs/)

## <a name="regional-availability-and-storage-account-support"></a>Apoio à conta de disponibilidade e armazenamento regional

As etiquetas de índice blob só estão disponíveis em contas de Finalidade Geral v2 (GPv2) com espaço hierárquico (HNS) desativado. As contas de Final Geral (GPV1) não são suportadas, mas pode atualizar qualquer conta GPv1 para uma conta GPv2.

As etiquetas de índice não são suportadas em contas de armazenamento Premium. Para obter mais informações sobre contas de armazenamento, consulte [a visão geral da conta de armazenamento Azure](../common/storage-account-overview.md).

As etiquetas do índice blob estão atualmente disponíveis em todas as regiões públicas.

Para começar, consulte [utilize etiquetas de índice blob para gerir e encontrar dados](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Tem de registar a sua subscrição antes de poder utilizar a pré-visualização do índice blob nas suas contas de armazenamento. Consulte as [Condições e questões conhecidas](#conditions-and-known-issues) deste artigo.

### <a name="register-your-subscription-preview"></a>Registe a sua subscrição (pré-visualização)

Como as tags blob estão apenas em visualização pública, terá de registar a sua subscrição antes de poder utilizar a funcionalidade. Para submeter um pedido, executar os seguintes comandos PowerShell ou CLI.

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

## <a name="conditions-and-known-issues"></a>Condições e questões conhecidas

Esta secção descreve problemas e condições conhecidos na visualização pública das etiquetas de índice blob. Esta funcionalidade não deve ser utilizada para cargas de trabalho de produção até atingir a disponibilidade geral (GA) uma vez que o comportamento pode mudar.

- Para pré-visualização, tem primeiro de registar a sua subscrição antes de poder utilizar o índice blob para a sua conta de armazenamento nas regiões de pré-visualização.
- Apenas as contas GPv2 são suportadas na pré-visualização. Blob, BlockBlobStorage e HNS habilitadas contas Da Gen2 não são suportados. As contas do GPv1 não serão suportadas.
- Carregar bolhas de página com etiquetas de índice não persiste nas etiquetas. Descreva as etiquetas depois de carregar uma bolha de página.
- Quando a filtragem é scopeda num único recipiente, a `@container` única só pode ser transmitida se todas as etiquetas de índice na expressão do filtro forem verificações de igualdade (chave=valor).
- Ao utilizar o operador de gama com a `AND` condição, só pode especificar o mesmo nome de chave de etiqueta de índice `"Age" > '013' AND "Age" < '100'` ().
- A versão e o índice blob não são suportados. As etiquetas do índice blob são preservadas para versões, mas não são passadas para o motor do índice blob.
- Não há API para determinar se as etiquetas de índice estão indexadas.
- A falta de contas não é suportada. O índice blob pode não atualizar corretamente após a falha.
- A gestão do ciclo de vida só suporta verificações de igualdade com correspondência de índice de bolha.
- `Copy Blob` não copia as etiquetas de índice blob da bolha de origem para a nova bolha de destino. Pode especificar as etiquetas que deseja aplicadas à bolha de destino durante a operação de cópia.
- `Copy Blob` (Cópia assínc) de outra conta de armazenamento com etiquetas aplicadas na bolha de destino faz com que o motor do índice blob não devolva a bolha e as suas etiquetas no conjunto de filtros. Utilização `Copy Blob` a partir de URL (Cópia Sincronizada).
- As etiquetas persistem na criação de instantâneos. No entanto, a promoção de um instantâneo não é suportada e pode resultar num conjunto de etiquetas vazio.

## <a name="faq"></a>FAQ

**O índice blob pode ajudar-me a filtrar e consultar o conteúdo dentro das minhas bolhas?**

Não, se precisar de pesquisar dentro dos seus dados blob, utilize a aceleração da consulta ou a pesquisa do Azure.

**Existem requisitos sobre os valores da etiqueta de índice?**

As tags de índice blob apenas suportam tipos de dados de cadeia e consultam os resultados das devoluções com a encomenda lexicográfica. Para os números, zero almofada o número. Para datas e horários, guarde como um formato compatível COM ISO 8601.

**As etiquetas de índice blob e as etiquetas do Gestor de Recursos Azure estão relacionadas?**

Não, as etiquetas do Gestor de Recursos ajudam a organizar recursos de controlo do avião, tais como subscrições, grupos de recursos e contas de armazenamento. As etiquetas de índice fornecem gestão e descoberta de blob no plano de dados.

## <a name="next-steps"></a>Passos seguintes

Para obter um exemplo de como utilizar o índice blob, consulte [use índice blob para gerir e encontrar dados](storage-blob-index-how-to.md).

Saiba mais sobre [a gestão do ciclo de vida](storage-lifecycle-management-concepts.md) e descreva uma regra com a correspondência do índice blob.
