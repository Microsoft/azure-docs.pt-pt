---
title: Gerir e encontrar dados sobre o armazenamento de blob Azure com índice blob (pré-visualização)
description: Aprenda a usar tags Blob Index para categorizar, gerir e consultar para descobrir objetos blob.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 083b130d1bb02ccc922c834c09a0d16fab004ae9
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433573"
---
# <a name="manage-and-find-data-on-azure-blob-storage-with-blob-index-preview"></a>Gerir e encontrar dados sobre o armazenamento de blob Azure com índice blob (pré-visualização)

À medida que os conjuntos de dados se formam cada vez maiores, encontrar um objeto específico num mar de dados pode ser difícil e frustrante. O Blob Index fornece capacidades de gestão e descoberta de dados utilizando atributos de etiqueta de índice de valor-chave, que permitem categorizar e encontrar objetos dentro de um único recipiente ou em todos os contentores da sua conta de armazenamento. Mais tarde, à medida que os requisitos dos dados mudam, os objetos podem ser dinamicamente recatados atualizando as suas etiquetas de índice, mantendo-se no lugar com a sua organização de contentores atual. A utilização do Blob Index pode simplificar o desenvolvimento consolidando os seus dados blob e atributos de índice associados no mesmo serviço; permitindo-lhe construir aplicações eficientes e escaláveis usando características nativas. 

O Índice Blob permite-lhe:

- Categorize dinamicamente as suas bolhas usando etiquetas de índice de valor-chave para a gestão de dados
- Encontre rapidamente bolhas marcadas específicas num único recipiente ou numa conta de armazenamento inteira
- Especificar comportamentos condicionais para APIs blob com base na avaliação das etiquetas de índice
- Utilize etiquetas de índice para controlos avançados em funcionalidades da plataforma blob, como [a gestão do ciclo de vida](storage-lifecycle-management-concepts.md)

Considere o cenário em que tem milhões de bolhas na sua conta de armazenamento escritas e acedidas por tão diferentes aplicações. Você quer encontrar todos os dados relacionados de um único projeto, mas você não tem certeza do que está no âmbito, uma vez que os dados podem ser espalhados por vários recipientes com diferentes convenções de nomeação de blob. No entanto, sabe que as suas aplicações carregam todos os dados com etiquetas com base no respetivo projeto e identificando a descrição. Em vez de pesquisar por milhões de bolhas e comparar nomes e propriedades, pode simplesmente usar `Project = Contoso` como critérios de descoberta. O Blob Index filtrará todos os recipientes em toda a sua conta de armazenamento para encontrar e devolver rapidamente apenas o conjunto de 50 bolhas de `Project = Contoso` . 

Para começar com exemplos sobre como usar o Blob Index, consulte [o Índice De Blob utilizar para gerir e encontrar dados](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Etiquetas blob index e gestão de dados

Os prefixos de nome de contentor e bolha são categorização unidimensional para os seus dados armazenados. O Blob Index permite agora uma categorização de várias dimensões para todos os seus [tipos de dados blob (Bloco, Apêndice ou Página)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) com etiquetas de atributos aplicadas. Esta categorização multidimensional é indexada e disponibilizada de forma nativa para que você possa consultar rapidamente e encontrar os seus dados.

Considere as seguintes cinco bolhas na sua conta de armazenamento:
>
> container1/transaction.csv  
> container2/campanha.docx  
> fotos/bannerphoto.png  
> archives/complete/2019review.pdf  
> logs/2020/01/01/logfile.txt  
>

Estas bolhas estão atualmente separadas utilizando um prefixo de contentor/nome de pasta virtual/blob. Com o Blob Index, pode definir um atributo de etiqueta de índice `Project = Contoso` nestas cinco bolhas para categorizá-los juntos, mantendo a sua organização atual de prefixo. Isto elimina a necessidade de mover dados expondo a capacidade de filtrar e encontrar dados usando o índice multidimensional da plataforma de armazenamento.

## <a name="setting-blob-index-tags"></a>Definição de etiquetas do Índice Blob

As tags Blob Index são atributos de valor-chave que podem ser aplicados a objetos novos ou existentes dentro da sua conta de armazenamento. Pode especificar as etiquetas de índice durante o processo de upload utilizando as operações PutBlob, PutBlockList ou CopyBlob e o cabeçalho opcional x-ms-tags. Se já tiver bolhas na sua conta de armazenamento, pode ligar para SetBlobTags com um documento XML formatado que especifica os atributos de etiqueta de índice de bolhas no corpo do pedido. 

Considere os seguintes exemplos de etiquetas que podem ser definidos

Pode aplicar uma única etiqueta na sua bolha para descrever quando os seus dados terminaram o processamento.
>
> "Processo" = '2020-01-01'
>
Pode aplicar várias tags na sua bolha para ser mais descritivo dos dados.
>
> "Projeto" = 'Contoso'  
> "Classificado" = 'Verdadeiro'  
> "Status" = 'Não processado'  
> "Prioridade" = '01' 
>

Para modificar os atributos de etiqueta de índice existentes, tem primeiro de recuperar os atributos de etiqueta existentes, modificar os atributos da etiqueta e substituir pela operação SetBlobTags. Para remover todas as etiquetas de índice da bolha, ligue para a operação SetBlobTags sem atributos de etiqueta especificados. Como as tags do índice blob são um sub-recurso para o conteúdo de dados blob, o SetBlobTags não modifica nenhum conteúdo subjacente e não altera o último tempo modificado da bolha.

Aplicam-se os seguintes limites às etiquetas blob Index:
- Cada bolha pode ter até 10 tags de índice de bolhas
- As teclas de etiqueta devem estar entre 1 a 128 caracteres
- Os valores da etiqueta devem estar entre 0 e 256 caracteres
- As chaves e valores da etiqueta são sensíveis ao caso
- Etiquetar chaves e valores apenas suportam tipos de dados de cadeia; quaisquer números, datas, horários ou caracteres especiais serão guardados como cordas
- As teclas e valores da etiqueta devem respeitar as seguintes regras de nomeação:
  - Caracteres alfa numéricos: a-z, A-Z, 0-9
  - Caracteres especiais: espaço, mais, menos, ponto, ponto, cólon, igual, sublinhado, corte para a frente

## <a name="getting-and-listing-blob-index-tags"></a>Etiquetas de Índice blob de obtenção e listagem

As tags do índice blob são armazenadas como um sub-recurso ao lado dos dados blob e podem ser obtidas independentemente do conteúdo subjacente de dados blob. Uma vez definidos, as etiquetas de índice blob para uma única bolha podem ser recuperadas e revistas imediatamente com a operação GetBlobTags. A operação ListBlobs com `include:tags` parâmetro também devolverá todas as bolhas dentro de um recipiente, juntamente com as suas etiquetas de índice de bolha aplicadas. 

Para quaisquer blobs com pelo menos 1 tag de índice de bolhas, a contagem de x-ms-tag é devolvida nas operações ListBlobs, GetBlob e GetBlobProperties indicando a contagem de tags de índice de bolhas que existem no blob.

## <a name="finding-data-using-blob-index-tags"></a>Encontrar dados usando tags blob Index

Com as etiquetas de índice blob definidas nas suas bolhas, o motor de indexação expõe esses atributos chave/valor num índice multidimensional. Embora as suas etiquetas de índice existam na bolha e possam ser recuperadas imediatamente, pode demorar algum tempo até que o índice blob seja atualizado com os atributos de etiqueta de índice atualizados. Uma vez atualizado o índice blob, pode agora tirar partido das capacidades de consulta e descoberta nativas oferecidas pelo armazenamento de bolhas.

A operação FindBlobsByTags permite-lhe obter um conjunto filtrado de bolhas de retorno cujas etiquetas de índice correspondem a uma determinada expressão de consulta de índice de bolhas. O Blob Index suporta a filtragem em todos os recipientes da sua conta de armazenamento ou pode passar a filtragem para apenas um único recipiente. Uma vez que todas as teclas e valores da etiqueta do índice blob são cordas, os operadores relacionais suportados usam uma triagem lexicográfica nos valores da etiqueta de índice.

Os seguintes critérios aplicam-se à filtragem do índice blob:
-   As teclas de etiqueta devem ser incluídas em cotações duplas (")
-   Os valores da etiqueta e os nomes dos contentores devem ser incluídos em cotações únicas (')
-   O carácter @ só é permitido para filtragem num nome específico do recipiente (isto é, @container = 'Bolo de Contentor')
- Os filtros são aplicados com classificação lexicográfica em cordas
-   As operações de gama lateral na mesma tecla são inválidas (isto é, "Rank" > '10' E "Rank" >= '15')
- Ao usar o REST para criar uma expressão de filtro, os caracteres devem ser codificados URI

A tabela abaixo mostra todos os operadores válidos para FindBlobsByTags:

|  Operador  |  Descrição  | Exemplo |
|------------|---------------|---------|
|     =      |     Igual     | "Status" = 'Em Curso' | 
|     >      |  Maior que |  "Data" > '2018-06-18' |
|     >=     |  Maior ou igual a | "Prioridade" >= '5' | 
|     <      |  Menor que    | "Idade" < '32' |
|     <=     |  Menor ou igual a  | "Empresa" <= 'Contoso' |
|    AND     |  Lógico e  | "Rank" >= '010' E "Rank" < '100' |
| @container |  Âmbito de um recipiente específico   | @container= 'videofiles' E "status" = 'feito' |

> [!NOTE]
> Esteja familiarizado com o pedido lexicográfico ao definir e consultar as etiquetas.
> - Os números são classificados antes das letras. Os números são classificados com base no primeiro dígito.
> - As letras maiúsculas são ordenadas antes das letras minúsculas.
> - Os símbolos não são normais. Alguns símbolos são classificados antes dos valores numéricos. Outros símbolos são classificados antes ou depois das letras.
>

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Operações de Blob condicionais com etiquetas blob index
Nas versões REST 2019-10-10 e mais altas, a maioria [das APIs de serviço blob](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) suportam agora um cabeçalho condicional, x-ms-if-tags, de tal forma que a operação só terá sucesso se a condição de índice blob especificado for satisfeita. Se a condição não for cumprida, `error 412: The condition specified using HTTP conditional header(s) is not met` terá.

O cabeçalho x-ms-if-tags pode ser combinado com os outros cabeçalhos condicionales HTTP existentes (Se-Match, If-None-Match, etc.).  Se forem fornecidos vários cabeçalhos condicionales num pedido, todos devem avaliar a verdade para que a operação tenha sucesso.  Todos os cabeçalhos condicional são efetivamente combinados com E lógicos. 

A tabela abaixo mostra todos os operadores válidos para operações condicionais:

|  Operador  |  Descrição  | Exemplo |
|------------|---------------|---------|
|     =      |     Igual     | "Status" = 'Em Curso' |
|     <>     |   Diferente   | "Status" <> 'Done'  | 
|     >      |  Maior que |  "Data" > '2018-06-18' |
|     >=     |  Maior ou igual a | "Prioridade" >= '5' | 
|     <      |  Menor que    | "Idade" < '32' |
|     <=     |  Menor ou igual a  | "Empresa" <= 'Contoso' |
|    AND     |  Lógico e  | "Rank" >= '010' E "Rank" < '100' |
|     OR     |  Lógico ou   | "Status" = 'Feito' ou "Prioridade" >= '05' |

> [!NOTE]
> Existem dois operadores adicionais, não iguais e lógicos ou, que são permitidos no cabeçalho x-ms-if-tags condicional para o funcionamento do blob, mas não existem na operação FindBlobsByTags.

## <a name="platform-integrations-with-blob-index-tags"></a>Integrações de plataformas com tags Blob Index

As tags do Blob Index não só o ajudam a categorizar, gerir e pesquisar os seus dados blob, mas também fornecer integrações com outras funcionalidades do serviço Blob, como [a gestão do ciclo de vida.](storage-lifecycle-management-concepts.md) 

### <a name="lifecycle-management"></a>Gestão do ciclo de vida

Utilizando o novo blobIndexMatch como filtro de regras na gestão do ciclo de vida, pode mover dados para níveis mais frios ou eliminar dados com base nas etiquetas de índice aplicadas às suas bolhas. Isto permite-lhe ser mais granular nas suas regras e apenas mover ou apagar dados se corresponderem aos critérios de etiquetas especificados.

Pode definir uma correspondência de índice de bolha como um conjunto de filtro autónomo numa regra do ciclo de vida para aplicar ações em dados marcados. Ou pode combinar tanto uma correspondência de prefixo como uma correspondência de índice de bolhas para combinar conjuntos de dados mais específicos. Aplicar filtros múltiplos a uma regra de ciclo de vida trata é uma operação lógica e tal que a ação só será aplicada se todos os critérios de filtro corresponderem. 

A seguinte regra de gestão do ciclo de vida da amostra aplica-se ao bloqueio de bolhas nos recipientes 'videofiles' e as bolhas de camadas para arquivar o armazenamento apenas se os dados corresponderem aos critérios de etiquetagem do índice blob de ```"Status" = 'Processed' AND "Source" == 'RAW'``` .

# <a name="portal"></a>[Portal](#tab/azure-portal)
![Blob Index corresponde exemplo de regra para gestão de ciclo de vida no portal Azure](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)
   
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

## <a name="permissions-and-authorization"></a>Permissões e Autorização

Pode autorizar o acesso ao Blob Index utilizando uma das seguintes abordagens:

- Utilizando o controlo de acesso baseado em funções (RBAC) para conceder permissões a um diretor de segurança Azure Ative (Azure AD). A Microsoft recomenda a utilização do Azure AD para uma segurança superior e facilidade de utilização. Para obter mais informações sobre a utilização do Azure AD com operações de [bolhas, consulte o Acesso autorizado a bolhas e filas utilizando o Azure Ative Directory](../common/storage-auth-aad.md).
- Utilizando uma assinatura de acesso partilhado (SAS) para delegar o acesso ao índice blob. Para obter mais informações sobre assinaturas de acesso partilhado, consulte [Grant acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).
- Utilizando as chaves de acesso à conta para autorizar operações com Chave Partilhada. Para mais informações, consulte [Autorizar com chave partilhada.](/rest/api/storageservices/authorize-with-shared-key)

As etiquetas blob Index são um sub-recurso para os dados do blob. Um utilizador com permissões ou um token SAS para ler ou escrever bolhas pode não ter acesso às etiquetas do índice blob. 

### <a name="role-based-access-control"></a>Controlo de Acesso Baseado em Funções 
Os chamadores que usam uma [identidade AAD](../common/storage-auth-aad.md) podem ter as seguintes permissões para operar em etiquetas de índice blob. 

|   Operações blob   |  Ação RBAC   |
|---------------------|----------------|
| Encontre Blobs por Tags  | Microsoft.Storage/storageAcounts/blobServices/containers/blobs/filter/action |
| Definir tags blob         | Microsoft.Storage/storageAcounts/blobServices/containers/blobs/tags/write | 
| Obter Tags Blob         | Microsoft.Storage/storageAcounts/blobServices/containers/blobs/tags/read |

São necessárias permissões adicionais separadas dos dados subjacentes ao blob para operar sobre as etiquetas. A função de Contribuinte de Dados blob de armazenamento será concedida a todas estas três permissões. O Leitor de Dados blob de armazenamento receberá as únicas permissões de Find Blobs por Tags e Obter Tags Blob.

### <a name="sas-permissions"></a>Permissões SAS 
Os chamadores que utilizem uma [assinatura de acesso partilhado (SAS)](../common/storage-sas-overview.md) podem ter permissões de mira para operar em tags blob.
#### <a name="blob-sas"></a>Blob SAS
As seguintes permissões podem ser concedidas num serviço Blob SAS para permitir o acesso a tags de índice blob. As permissões blob Read and Write por si só não são suficientes para permitir a leitura ou a escrita das suas etiquetas de índice.

|  Permissão  |  Símbolo URI  | Operações permitidas |
|--------------|--------------|--------------------|
|  Etiquetas de índice  |      t         | Obter e definir etiquetas de índice blob para uma bolha |

#### <a name="container-sas"></a>Contentor SAS
As seguintes permissões podem ser concedidas no serviço de contentores SAS para permitir a filtragem em etiquetas de bolhas.  A permissão blob List não é suficiente para permitir a filtragem de bolhas pelas suas etiquetas de índice.

|  Permissão  |  Símbolo URI  | Operações permitidas |
|--------------|--------------|--------------------|
| Etiquetas de índice     |      f       | Encontre Blobs com etiquetas de índice de bolhas | 

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Escolher entre metadados e etiquetas de índice de blob 
Tanto as tags Blob Index como os metadados fornecem a capacidade de armazenar propriedades de chave/valor de nível arbitrário definidas pelo utilizador ao lado de um recurso blob. Ambos podem ser recuperados e definidos diretamente, sem retornar ou alterar o conteúdo da bolha. É possível utilizar tanto metadados como tags de índice.

No entanto, apenas as tags Blob Index são automaticamente indexadas e tornadas consultadas pelo serviço de blob nativo. Os metadados não podem ser indexados e consultados de forma nativa, a menos que utilize um serviço separado como [Azure Search](../../search/search-blob-ai-integration.md). As tags Blob Index também têm permissões adicionais para leitura/filtragem e escrita que são separadas dos dados subjacentes ao blob. Os metadados utilizam as mesmas permissões que o blob e são devolvidos como cabeçalhos HTTP nas operações GetBlob ou GetBlobProperties. As tags Blob Index são encriptadas em repouso utilizando uma [chave gerida pela Microsoft,](../common/storage-service-encryption.md) enquanto os metadados são encriptados em repouso usando a mesma chave de encriptação especificada para dados blob. 

O quadro seguinte resume as diferenças entre as etiquetas metadata e blob Index:

|              |   Metadata   |   Etiquetas blob Index  |
|--------------|--------------|--------------------|
| **Limites**         | Sem limite numérico; Total de 8 KB; caso insensível | 10 etiquetas por blob max; 768 bytes por etiqueta; caso sensível |
| **Updates**      | Não é permitido no nível de arquivo; O SetBlobMetadata substitui todos os metadados existentes; SetBlobMetadata altera o último tempo modificado da bolha | Permitido para todos os níveis de acesso; O SetBlobTags substitui todas as etiquetas existentes; SetBlobTags não altera o último tempo modificado da bolha |
| **Armazenamento**        | Armazenada com os dados blob |  Sub-recurso para os dados blob | 
| **Indexação da consulta &** | N/A de forma nativa; deve usar um serviço separado, como Azure Search | Sim, capacidades de indexação e consulta nativas incorporadas no armazenamento de bolhas |
| **Encriptação** | Encriptado em repouso com a mesma chave de encriptação usada para dados blob |  Encriptado em repouso com uma chave de encriptação gerida pela Microsoft |
| **Preços**   | O tamanho dos metadados está incluído nos custos de armazenamento de uma bolha |    Custo fixo por etiqueta de índice | 
| **Resposta do cabeçalho** | Metadados devolvidos como cabeçalhos em GetBlob e GetBlobProperties | TagCount devolvido em GetBlob ou GetBlobProperties; Tags devolvidos apenas em GetBlobTags e ListBlobs |
| **Permissões**  |    Ler ou escrever permissões para blob dados estende-se a metadados |    São necessárias permissões adicionais para ler/filtrar ou escrever tags |

## <a name="pricing"></a>Preços
Os preços do Índice Blob estão atualmente em pré-visualização pública e sujeitos a alterações para disponibilidade geral. Os clientes são cobrados pelo número total de tags de índice blob dentro de uma conta de armazenamento, média ao longo do mês. Não há custo para o motor de indexação. Os pedidos de SetBlobTags, GetBlobTags e FindBlobsByTags são cobrados de acordo com os respetivos tipos de operação. Consulte [os preços do Block Blob para saber mais.](https://azure.microsoft.com/pricing/details/storage/blobs/)

## <a name="regional-availability-and-storage-account-support"></a>Apoio à conta de disponibilidade e armazenamento regional

Atualmente, o Blob Index só está disponível em contas de Finalidade Geral v2 (GPv2) com espaço hierárquico (HNS) desativado. As contas de Final Geral (GPV1) não são suportadas, mas pode atualizar qualquer conta GPv1 para uma conta GPv2. Para obter mais informações sobre contas de armazenamento, consulte [a visão geral da conta de armazenamento Azure](../common/storage-account-overview.md).

Na pré-visualização pública, o Blob Index só está disponível nas seguintes regiões selecionadas:
- Canadá Central
- Leste do Canadá
- França Central
- Sul de França

Para começar, consulte [o Use Blob Index para gerir e encontrar dados.](storage-blob-index-how-to.md)

> [!IMPORTANT]
> Consulte a secção de condições deste artigo. Para se inscrever na pré-visualização, consulte a secção de subscrição deste artigo. Tem de registar a sua subscrição antes de poder utilizar o Blob Index nas suas contas de armazenamento.

### <a name="register-your-subscription-preview"></a>Registe a sua subscrição (Pré-visualização)
Como o Índice Blob está apenas em visualização pública, terá de registar a sua subscrição antes de poder utilizar a funcionalidade. Para submeter um pedido, executar os seguintes comandos PowerShell ou CLI.

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
Esta secção descreve problemas e condições conhecidos na atual pré-visualização pública do Índice Blob. Tal como acontece com a maioria das pré-visualizações, esta funcionalidade não deve ser utilizada para cargas de trabalho de produção até chegar à AG, uma vez que os comportamentos podem mudar.

-   Para pré-visualização, tem primeiro de registar a sua subscrição antes de poder utilizar o Blob Index para a sua conta de armazenamento nas regiões de pré-visualização.
-   Apenas as contas GPv2 são atualmente suportadas na pré-visualização. Blob, BlockBlobStorage e HNS habilitadas contas DataLake Gen2 não são atualmente suportados com Índice Blob. As contas do GPv1 não serão suportadas.
-   O upload de bolhas de página com etiquetas de índice atualmente não persiste nas etiquetas. Tem de definir as etiquetas depois de carregar uma bolha de página.
-   Quando a filtragem é scopeda num único recipiente, a @container única só pode ser transmitida se todas as etiquetas de índice na expressão do filtro forem verificações de igualdade (chave=valor). 
-   Ao utilizar o operador de gama com a condição E, só é possível especificar o mesmo nome de chave de etiqueta de índice (Idade > '013' E idade < '100').
-   A versão e o Blob Index não são atualmente suportados. As etiquetas blob Index são preservadas para versões, mas atualmente não são passadas para o motor do índice blob.
-   A falha da conta não é suportada atualmente. O índice blob pode não atualizar corretamente após a falha.
-   A gestão do ciclo de vida atualmente apenas suporta verificações de igualdade com Blob Index Match.
-   CopyBlob não copia tags de índice blob da bolha de origem para a nova bolha de destino. Pode especificar as etiquetas que deseja aplicadas à bolha de destino durante a operação de cópia. 
-   As etiquetas são insistidas na criação de instantâneos; no entanto, a promoção de um instantâneo não é atualmente suportada e pode resultar num conjunto de tags vazio.

## <a name="faq"></a>FAQ

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>O Blob Index pode ajudar-me a filtrar e consultar o conteúdo dentro das minhas bolhas? 
Não, as etiquetas blob index podem ajudá-lo a encontrar as bolhas que procura. Se precisar de pesquisar dentro das suas bolhas, utilize a Aceleração de Consultas ou a Procura de Azure.

### <a name="are-there-any-special-considerations-regarding-blob-index-tag-values"></a>Existem considerações especiais em relação aos valores da etiqueta do Índice Blob?
As tags Blob Index apenas suportam tipos de dados de cadeia e consultam os resultados das devoluções com a encomenda lexicográfica. Para os números, recomenda-se zero escamar o número. Para data e horários, recomenda-se armazenar como um formato compatível ISO 8601.

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>As tags Blob Index e Azure Resource Manager estão relacionadas?
Não, as tags do Azure Resource Manager ajudam a organizar recursos de planos de controlo, tais como subscrições, grupos de recursos e contas de armazenamento. As tags Blob Index fornecem gestão de objetos e descobertas em recursos de planos de dados, tais como bolhas dentro de uma conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Veja um exemplo de como utilizar o Blob Index. Consulte [o Índice De Blob utilize para gerir e encontrar dados](storage-blob-index-how-to.md)

