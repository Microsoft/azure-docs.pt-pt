---
title: Utilizar a partilhado assinaturas de acesso (SAS) no armazenamento do Azure | Documentos da Microsoft
description: Saiba como utilizar assinaturas de acesso partilhado (SAS) para delegar acesso a recursos de armazenamento do Azure, incluindo blobs, filas, tabelas e ficheiros.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/18/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: bee2a715e9e9b163af342b70c4cdd63d24ccee3b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450044"
---
# <a name="using-shared-access-signatures-sas"></a>Utilizar assinaturas de acesso partilhado (SAS)

Uma assinatura de acesso partilhado (SAS) fornece uma forma de conceder acesso limitado aos objetos na sua conta de armazenamento para outros clientes, sem expor a sua chave de conta. Neste artigo, vamos fornecer uma visão geral do modelo de SAS, rever as melhores práticas SAS e ver alguns exemplos.

Para obter exemplos de código adicional através da SAS além dos apresentados aqui, consulte [introdução ao armazenamento de Blobs do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) e outros exemplos disponíveis no [exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?service=storage) biblioteca. Pode transferir os aplicativos de exemplo e executá-los ou procurar o código no GitHub.

## <a name="what-is-a-shared-access-signature"></a>O que é uma assinatura de acesso partilhado?
Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Com uma SAS, pode conceder clientes acesso a recursos na sua conta de armazenamento sem partilhar as chaves de conta. Este é o ponto fundamental da utilização de assinaturas de acesso partilhado nas suas aplicações – uma SAS é uma forma segura de partilhar os seus recursos de armazenamento sem comprometer as chaves da conta.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

Uma SAS dá-lhe um controlo granular sobre o tipo de acesso que concede aos clientes que tenham a SAS, incluindo:

* O intervalo durante o qual a SAS é válida, incluindo a hora de início e a hora de expiração.
* As permissões concedidas a SAs. Por exemplo, uma SAS para um blob poderá conceder a leitura e permissões de escrita a esse blob, mas não eliminar as permissões.
* Um endereço IP opcional ou intervalo de endereços IP dos quais o armazenamento do Azure irá aceitar a SAS. Por exemplo, poderá especificar um intervalo de endereços IP que pertençam a sua organização.
* O protocolo através do qual o armazenamento do Azure irá aceitar a SAS. Pode utilizar este parâmetro opcional para restringir o acesso aos clientes através de HTTPS.

## <a name="when-should-you-use-a-shared-access-signature"></a>Quando deve usar uma assinatura de acesso partilhado?
Pode utilizar uma SAS para fornecer acesso a recursos na sua conta de armazenamento para qualquer cliente não possessing chaves de acesso da conta de armazenamento. Sua conta de armazenamento inclui tanto uma chave de acesso primária e secundária, que concedem acesso administrativo à sua conta e todos os recursos dentro da mesma. Expor qualquer uma destas chaves abre-se a sua conta para a possibilidade de utilização por acidente ou maliciosa. Assinaturas de acesso partilhado são uma alternativa de segura que permite aos clientes ler, escrever e eliminar dados na sua conta de armazenamento, de acordo com as permissões que se tiver concedido e sem necessidade de uma chave de conta.

Um cenário comum em que é útil uma SAS é um serviço em que os utilizadores ler e escrever seus próprios dados à sua conta de armazenamento. Num cenário em que uma conta de armazenamento armazena dados de utilizador, existem dois padrões de design típico:

1. Os clientes carregar e transferir dados através de um serviço de proxy de front-end, que efetua a autenticação. Este serviço de proxy de front-end tem a vantagem de permitir que a validação de regras de negócios, mas para grandes quantidades de dados ou transações de grande volume, criação de um serviço que possa ser dimensionada para corresponder a pedido pode ser difícil ou dispendioso.

  ![Diagrama do cenário: Serviço de proxy de front-end](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. Um serviço simples autentica o cliente conforme necessário e, em seguida, gera uma SAS. Assim que o cliente recebe a SAS, podem aceder a recursos da conta de armazenamento diretamente com as permissões definidas pela SAS e para o intervalo permitido a SAs. A SAS, reduz a necessidade para todos os dados através do serviço de front-end de proxy de encaminhamento.

  ![Diagrama do cenário: Serviço do fornecedor SAS](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

Muitos serviços do mundo real, podem utilizar uma mistura dessas duas abordagens. Por exemplo, alguns dados possam ser processados e validados através do proxy de front-end, enquanto outros dados são guardados e/ou diretamente através da SAS de leitura.

Além disso, terá de utilizar uma SAS para autorizar o acesso ao objeto de origem numa operação de cópia em determinados cenários:

* Quando copiar um blob para o blob de outro que reside numa conta de armazenamento diferentes, tem de utilizar uma SAS para autorizar o acesso para o blob de origem. Opcionalmente, pode utilizar uma SAS para autorizar o acesso para o blob de destino também.
* Quando copiar um ficheiro para outro ficheiro que reside numa conta de armazenamento diferentes, tem de utilizar uma SAS para autorizar o acesso ao ficheiro de origem. Opcionalmente, pode utilizar uma SAS para autorizar o acesso ao ficheiro de destino.
* Quando copiar um blob para um arquivo ou um ficheiro para um blob, tem de utilizar uma SAS para autorizar o acesso ao objeto de origem, mesmo que os objetos de origem e destino residam na mesma conta de armazenamento.

## <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de acesso partilhado
Pode criar dois tipos de assinaturas de acesso partilhado:

* **Serviço SAS.** O serviço SAS delega o acesso a um recurso em apenas um dos serviços de armazenamento: o serviço Blob, Fila, Tabela ou Ficheiro. Ver [construir uma SAS de serviço](https://msdn.microsoft.com/library/dn140255.aspx) e [exemplos de SAS de serviço](https://msdn.microsoft.com/library/dn140256.aspx) para obter informações aprofundadas sobre a construção do token SAS de serviço.
* **Conta SAS.** O conta SAS delegados acesso a recursos num ou mais dos serviços de armazenamento. Todas as operações disponíveis através de um serviço SAS também estão disponíveis por meio de uma conta SAS. Além disso, com a conta SAS, pode delegar o acesso às operações que se aplicam a um determinado serviço, tal como **propriedades do serviço de Get/Set** e **obter estatísticas do serviço**. Também pode delegar o acesso às operações de leitura, escrita e eliminação em contentores de blobs, tabelas, filas e partilhas de ficheiros que não são permitidos com um serviço SAS. Ver [construir uma SAS de conta](https://msdn.microsoft.com/library/mt584140.aspx) para obter informações aprofundadas sobre a construção do token SAS de conta.

## <a name="how-a-shared-access-signature-works"></a>Como funciona uma assinatura de acesso partilhado
Uma assinatura de acesso partilhado é um URI assinado que aponta para um ou mais recursos de armazenamento e inclui um token que contém um conjunto especial de parâmetros de consulta. O token indica como os recursos podem ser acessados pelo cliente. Um dos parâmetros de consulta, a assinatura é construído com os parâmetros SAS e assinado com a chave de conta. Essa assinatura é utilizada pelo armazenamento do Azure para autorizar o acesso ao recurso de armazenamento.

Eis um exemplo de um URI de SAS, que mostra o URI do recurso e o token SAS:

![Componentes de um URI de SAS](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

O token SAS é uma cadeia de caracteres é gerar no *cliente* lado (consulte a [exemplos SAS](#sas-examples) secção para obter exemplos de código). Um token SAS que gere com a biblioteca de cliente de armazenamento, por exemplo, não é registado pelo armazenamento do Azure de qualquer forma. Pode criar um número ilimitado de SAS tokens no lado do cliente.

Quando um cliente fornece um URI de SAS para o armazenamento do Azure como parte de um pedido, o serviço verifica os parâmetros SAS e a assinatura para verificar se é válido para autenticar o pedido. Se o serviço verifica se a assinatura é válida, em seguida, a solicitação é autorizada. Caso contrário, a solicitação será recusada com o código de erro 403 (proibido).

## <a name="shared-access-signature-parameters"></a>Parâmetros de assinatura de acesso partilhado
A conta SAS e tokens SAS de serviço incluem alguns parâmetros comuns e também de ter alguns parâmetros que são diferentes.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Parâmetros comuns a conta SAS e tokens SAS de serviço
* **Versão de API** um parâmetro opcional que especifica a versão do serviço de armazenamento a utilizar para executar o pedido.
* **Versão de serviço** um parâmetro obrigatório que especifica a versão do serviço de armazenamento a utilizar para autorizar a solicitação.
* **Hora de início.** Este é o tempo em que a SAS se torna válida. A hora de início para uma assinatura de acesso partilhado é opcional. Se uma hora de início for omitida, a SAS tem efeita imediato. A hora de início tem de ser expresso em UTC (Hora Universal Coordenada), com um designador de UTC especial ("Z"), por exemplo `1994-11-05T13:15:30Z`.
* **Hora de expiração.** Este é o tempo após o qual a SAS já não é válida. Melhores práticas recomendável que especifica uma hora de expiração para um SAS, ou associá-lo a uma política de acesso armazenado. A hora de expiração tem de ser expresso em UTC (Hora Universal Coordenada), com um designador de UTC especial ("Z"), por exemplo `1994-11-05T13:15:30Z` (ver mais abaixo).
* **Permissões.** As permissões especificadas no SAS indicam as operações que o cliente pode realizar com o recurso de armazenamento através da SAS. Permissões disponíveis diferem para uma conta SAS e um serviço SAS.
* **IP.** Um parâmetro opcional que especifica um endereço IP ou um intervalo de endereços IP fora do Azure (consulte a secção [estado de configuração de sessão de encaminhamento](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) de Express Route) partir do qual aceitar pedidos.
* **Protocolo.** Um parâmetro opcional que especifica o protocolo permitidos para um pedido. Os valores possíveis são HTTPS e HTTP (`https,http`), que é apenas o valor predefinido, ou HTTPS (`https`). Tenha em atenção que HTTP apenas não é um valor permitido.
* **Assinatura.** A assinatura é construída de outros parâmetros especificados como parte de token e, em seguida, são encriptados. A assinatura é utilizada para autorizar o acesso aos recursos de armazenamento especificada.

### <a name="parameters-for-a-service-sas-token"></a>Parâmetros para um token SAS de serviço
* **Recursos de armazenamento.** Recursos de armazenamento para o qual pode delegar o acesso com um serviço SAS incluem:
  * Contentores e blobs
  * Partilhas de ficheiros e ficheiros
  * Filas
  * As tabelas e intervalos de entidades da tabela.

### <a name="parameters-for-an-account-sas-token"></a>Parâmetros para um token SAS de conta
* **Serviço ou serviços.** Uma conta SAS pode delegar o acesso a um ou mais dos serviços de armazenamento. Por exemplo, pode criar uma conta SAS, que delega o acesso ao serviço de BLOBs e ficheiros. Ou pode criar uma SAS que o acesso de delegados para os quatro serviços (Blob, fila, tabela e ficheiro).
* **Tipos de recursos de armazenamento.** Uma conta SAS aplica-se para uma ou mais classes de recursos de armazenamento, em vez de um recurso específico. Pode criar uma conta SAS para delegar acesso para:
  * APIs de nível de serviço, que são chamadas contra o recurso de conta de armazenamento. Os exemplos incluem **propriedades do serviço de Get/Set**, **obter estatísticas de serviço**, e **lista contentores/filas/tabelas/partilhas**.
  * APIs de nível de contêiner, que são chamadas em relação os objetos de contentor para cada serviço: BLOBs, contentores, filas, tabelas e partilhas de ficheiros. Os exemplos incluem **criar/eliminar contentor**, **criar/eliminar fila**, **criar/eliminar tabela**, **Criar/Eliminar partilha**e o  **Lista de Blobs/ficheiros e diretórios**.
  * APIs de nível de objeto, que são chamadas em relação a blobs, fila de mensagens, entidades de tabelas e ficheiros. Por exemplo, **colocar Blob**, **entidade de consulta**, **obter mensagens**, e **criar ficheiro**.

## <a name="examples-of-sas-uris"></a>Exemplos de URIs SAS

### <a name="service-sas-uri-example"></a>Exemplo de URI de SAS de serviço

Eis um exemplo de um serviço de URI de SAS que fornece permissões de escrita para um blob e ler. A tabela divide cada parte do URI para compreender como contribui para a SAS:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| Name | Parte SAS | Descrição |
| --- | --- | --- |
| URI de blob |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |O endereço do blob. Tenha em atenção que através de HTTPS é altamente recomendado. |
| Versão de serviços de armazenamento |`sv=2015-04-05` |Para a versão 2012-02-12 dos serviços de armazenamento e mais tarde, esse parâmetro indica a versão a utilizar. |
| Hora de início |`st=2015-04-29T22%3A18%3A26Z` |Especificado no fuso horário UTC. Se pretender que a SAS para ser válida imediatamente, omita a hora de início. |
| Hora de expiração |`se=2015-04-30T02%3A23%3A26Z` |Especificado no fuso horário UTC. |
| Recurso |`sr=b` |O recurso é um blob. |
| Permissões |`sp=rw` |As permissões concedidas a SAs incluem Read (r) e escrita (w). |
| Intervalo de IPs |`sip=168.1.5.60-168.1.5.70` |O intervalo de endereços IP do qual será aceita um pedido. |
| Protocolo |`spr=https` |São permitidos apenas os pedidos através de HTTPS. |
| Assinatura |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |Utilizado para autorizar o acesso para o blob. A assinatura é um HMAC calculada com uma cadeia de caracteres para assinar e a chave a utilizar o algoritmo SHA256 e, em seguida, codificados usando a codificação Base64. |

### <a name="account-sas-uri-example"></a>Exemplo de URI de SAS de conta

Eis um exemplo de uma conta SAS que utiliza os mesmos parâmetros comuns no token. Uma vez que estes parâmetros são descritos acima, eles não são descritos aqui. Apenas os parâmetros que são específicas para a conta que SAs é descrita na tabela abaixo.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| Name | Parte SAS | Descrição |
| --- | --- | --- |
| URI do recurso |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |O Blob ponto final de serviço, com parâmetros para obter as propriedades do serviço (quando chamado com GET) ou definir as propriedades do serviço (quando chamado conjunto). |
| Serviços |`ss=bf` |A SAS se aplica aos serviços Blob e ficheiro |
| Tipos de recurso |`srt=s` |A SAS se aplica a operações de nível de serviço. |
| Permissões |`sp=rw` |As permissões de concedem acesso para ler e operações de escrita. |

Considerando que permissões são restritas para o nível de serviço, as operações acessíveis com esta SAS são **obter propriedades do serviço Blob** (ler) e **definir propriedades do serviço Blob** (escrita). No entanto, com um URI de recurso diferente, o mesmo token SAS poderia também ser utilizado para delegar o acesso ao **obter estatísticas do serviço Blob** (ler).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Controlar um SAS com uma política de acesso armazenadas
Uma assinatura de acesso partilhado pode efetuar uma das duas formas:

* **SAS ad hoc:** Quando cria uma SAS ad hoc, a hora de início, hora de expiração e as permissões para a SAS são todos especificadas no URI de SAS (explícita ou implícita, no caso em que a hora de início é omitida). Este tipo de SAS pode ser criado como uma conta SAS ou um serviço SAS.
* **SAS com a política de acesso armazenado:** Uma política de acesso armazenado é definido num contêiner de recursos – um contentor de BLOBs, tabela, fila, ou partilha – de ficheiros e pode ser utilizado para gerir restrições para um ou mais assinaturas de acesso partilhado. Quando associa um SAS com uma política de acesso armazenado, a SAS herda as restrições, a hora de início, hora de expiração e permissões – definidas para a política de acesso armazenado.

> [!NOTE]
> Atualmente, uma conta SAS tem de ser SAS ad hoc. Armazenados acesso políticas ainda não são suportadas para a conta SAS.

A diferença entre as duas formas é importante para um cenário de chave: revogação. Como um URI de SAS é um URL, qualquer pessoa que obtém a SAS pode utilizá-lo, independentemente de quem a criou originalmente. Se uma SAS é publicada publicamente, ele pode ser usado por qualquer pessoa no mundo. Uma SAS concede acesso a recursos para qualquer pessoa possessing-lo até que uma das quatro coisas acontece:

1. For atingida a hora de expiração especificada no SAS.
2. A hora de expiração especificada na política de acesso armazenado referenciada pela SAS foi atingida (se uma política de acesso armazenado é referenciada, e se ele Especifica um tempo de expiração). Isto pode ocorrer devido à decorrido o intervalo, ou modificar a política de acesso armazenadas com um tempo de expiração no passado, que é uma forma de revogar a SAS.
3. A política de acesso armazenado referenciada pela SAS é eliminada, que é outra forma de revogar a SAS. Tenha em atenção que se recriar a política de acesso armazenadas com exatamente o mesmo nome, todos os tokens SAS existentes novamente será válidos de acordo com as permissões associadas essa política de acesso armazenadas (partindo do princípio de que a hora de expiração no SAS não passou). Se pretende revogar o SAS, certifique-se de que utilize um nome diferente, caso recrie a política de acesso com um tempo de expiração no futuro.
4. A chave da conta que utilizou para criar a SAS é regenerada. Regenerar uma chave de conta fará com que todos os componentes da aplicação com essa chave não sejam autorizar até que sejam atualizados para utilizar a chave de conta válido ou a chave da conta recentemente regenerada.

> [!IMPORTANT]
> Um URI de assinatura de acesso partilhado é associado a chave da conta utilizada para criar a assinatura e o associados armazenados política de acesso (se houver). Não se for especificada nenhuma política de acesso armazenado, é a única forma de revogar uma assinatura de acesso partilhado alterar a chave de conta.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Autenticação de um aplicativo de cliente com uma SAS
Um cliente que está na posse de uma SAS pode utilizar a SAS para autorizar um pedido com uma conta de armazenamento para o qual não possuem as chaves de conta. Uma SAS podem ser incluída numa cadeia de ligação ou utilizada diretamente a partir do método ou construtor apropriado.

### <a name="using-a-sas-in-a-connection-string"></a>Com uma SAS numa cadeia de ligação
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Com uma SAS num construtor ou método
Vários construtores de biblioteca de cliente de armazenamento do Azure e a sobrecarga de métodos oferece um parâmetro SAS, para que pode autorizar um pedido para o serviço com uma SAS.

Por exemplo, aqui um URI de SAS é utilizado para criar uma referência para um blob de blocos. A SAS fornece as credenciais apenas necessárias para o pedido. A referência de blob de bloco, em seguida, é utilizada para uma operação de escrita:

```csharp
string sasUri = "https://storagesample.blob.core.windows.net/sample-container/" +
    "sampleBlob.txt?sv=2015-07-08&sr=b&sig=39Up9JzHkxhUIhFEjEH9594DJxe7w6cIRCg0V6lCGSo%3D" +
    "&se=2016-10-18T21%3A51%3A37Z&sp=rcw";

CloudBlockBlob blob = new CloudBlockBlob(new Uri(sasUri));

// Create operation: Upload a blob with the specified name to the container.
// If the blob does not exist, it will be created. If it does exist, it will be overwritten.
try
{
    MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    msWrite.Position = 0;
    using (msWrite)
    {
        await blob.UploadFromStreamAsync(msWrite);
    }

    Console.WriteLine("Create operation succeeded for SAS {0}", sasUri);
    Console.WriteLine();
}
catch (StorageException e)
{
    if (e.RequestInformation.HttpStatusCode == 403)
    {
        Console.WriteLine("Create operation failed for SAS {0}", sasUri);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    else
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}

```

## <a name="best-practices-when-using-sas"></a>Melhores práticas quando através da SAS
Quando utiliza assinaturas de acesso partilhado nas suas aplicações, terá de conhecer dois riscos potenciais:

* Se uma SAS é perdida, pode ser utilizado por qualquer pessoa que obtém, que podem comprometer a conta de armazenamento.
* Se uma SAS fornecido para uma aplicação cliente expira e o aplicativo não é possível obter uma SAS novo a partir do seu serviço, então a funcionalidade do aplicativo pode ficar prejudicada.

As seguintes recomendações para a utilização de assinaturas de acesso partilhado podem ajudar a mitigar estes riscos:

1. **Utilize sempre HTTPS** para criar ou distribuir uma SAS. Se uma SAS é transmitida através de HTTP e interceptada, um atacante que efetue um ataque man-in-the-middle é capaz de ler a SAS e, em seguida, utilizá-la tal como o utilizador em causa poderia ter, potencialmente, comprometer dados confidenciais ou permitindo a Corrupção de dados, o usuário mal-intencionado.
2. **Referência de políticas de acesso armazenadas sempre que possível.** Políticas de acesso armazenadas dão-lhe a opção para revogar permissões sem ter de voltar a gerar as chaves de conta de armazenamento. Defina a expiração sobre esses muito distante (ou infinito) e certifique-se de que é atualizado regularmente para movê-la mais no futuro.
3. **Utilize as horas de expiração de curto prazo em SAS ad hoc.** Dessa forma, mesmo que uma SAS for comprometida, é válido apenas para um curto período de tempo. Esta prática é especialmente importante se não pode fazer referência a uma política de acesso armazenado. Horas de expiração curto também limitam a quantidade de dados que podem ser gravados para um blob ao limitar o tempo disponível para carregar para o mesmo.
4. **Ter clientes automaticamente renovada a SAS se necessário.** Os clientes devem renovar a SAS bem antes da expiração, para dar tempo para repetições, se o serviço fornece a SAS não está disponível. Se sua SAS destina-se a ser utilizado para um pequeno número de operações de imediato de curta duração que devem ser concluído dentro do período de expiração, em seguida, isso pode ser desnecessário, como a SAS não deve ser renovado. No entanto, se tiver o cliente que está a fazer regularmente pedidos através de SAS, em seguida, a possibilidade de expiração entra em cena. A consideração fundamental é equilibrar a necessidade de SAS para curta (conforme anteriormente mencionado) com a necessidade de garantir que o cliente está a pedir a renovação cedo o suficiente (para evitar a interrupção devido a SAS irá expirar antes de renovação efetuada com êxito).
5. **Tenha cuidado com a hora de início do SAS.** Se definir a hora de início uma SAs **agora**, em seguida, devido a relógio skew (diferenças na hora atual, de acordo com diferentes máquinas), falhas podem ser observadas intermitentemente para os primeiros poucos minutos. Em geral, defina a hora de início para ser, pelo menos, 15 minutos no passado. Em alternativa, não defini-lo, que tornará válido imediatamente em todos os casos. O mesmo geralmente aplica-se a hora de expiração também – Lembre-se de que pode observar até 15 minutos do relógio do distorção em ambas as direções em qualquer pedido. Para clientes com uma versão REST antes de 2012-02-12, a duração máxima para um SAS que não faz referência uma política de acesso armazenado é de 1 hora e todas as políticas de especificação de longo prazo que o que irá falhar.
6. **Seja específico com o recurso a ser acessado.** Uma melhor prática de segurança é fornecer um utilizador com os mínimos privilégios necessários. Se um utilizador necessita apenas de acesso de leitura para uma única entidade, em seguida, lhes conceda acesso de leitura para essa entidade única e não de leitura/escrita/eliminar acesso a todas as entidades. Isto também ajuda a reduzir os danos se uma SAS for comprometida, uma vez que a SAS tem menos poder nas mãos de um atacante.
7. **Compreenda o que sua conta será cobrada qualquer utilização, incluindo o que é feito com SAS.** Se fornecer acesso de escrita para um blob, um utilizador pode optar por carregar um blob de 200GB. Se demos-lhes acesso de leitura também, optem para baixá-lo 10 vezes, incorrer em custos de saída em 2 TB para. Novamente, fornece permissões limitadas para ajudar a atenuar as ações possíveis de utilizadores mal intencionados. Utilize a SAS de curta duração para reduzir esta ameaça (mas ficar atentos a distorção na hora de fim).
8. **Valide dados escritos através da SAS.** Quando um aplicativo cliente escreve dados para a sua conta de armazenamento, tenha em atenção que pode haver problemas com os dados. Se seu aplicativo exigir que os dados ser validados ou autorizados antes que ele está pronto a utilizar, deve realizar esta validação depois dos dados são escritos e antes de ser utilizada pela sua aplicação. Esta prática também protege contra dados danificados ou maliciosos que está a ser escritos para a sua conta, por um usuário que adquiriu corretamente a SAS ou por um utilizador explorando uma fuga SAS.
9. **Não utilize sempre SAS.** Por vezes, os riscos associados uma determinada operação contra a sua conta de armazenamento superam os benefícios de SAS. Para essas operações, criar um serviço de camada intermediária que escreve na sua conta de armazenamento depois de efetuar as empresas a regra de validação, autenticação e auditoria. Além disso, às vezes, é mais simples gerir o acesso de outras formas. Por exemplo, se quiser fazer todos os blobs num contentor lido publicamente, pode tornar o contentor público, em vez de fornecer uma SAS para todos os clientes de acesso.
10. **Utilize a análise de armazenamento para monitorizar a sua aplicação.** Pode utilizar o registo e as métricas para observar qualquer pico em falhas de autenticação devido a uma falha no seu serviço do fornecedor SAS ou para a remoção acidental de uma política de acesso armazenado. Consulte a [blogue da equipa do Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) para obter informações adicionais.

## <a name="sas-examples"></a>Exemplos SAS
Seguem-se alguns exemplos de ambos os tipos de assinaturas de acesso partilhado, a conta SAS e SAS de serviço.

Para executar esses exemplos em C#, precisa referenciar os seguintes pacotes de NuGet no seu projeto:

* [Biblioteca de cliente de armazenamento do Azure para .NET](http://www.nuget.org/packages/WindowsAzure.Storage), versão 6.x ou posterior (para utilizar a conta SAS).
* [Gestor de Configuração do Azure](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

Para obter exemplos adicionais que mostram como criar e testar uma SAS, consulte [exemplos de código do Azure para armazenamento](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Exemplo: Criar e utilizar uma SAS de conta
O exemplo de código seguinte cria uma conta SAS que é válido para os serviços de ficheiros e BLOBs e permite que o cliente permissões de leitura, escrita e listar permissões para aceder a APIs de nível de serviço. A conta SAS restringe o protocolo HTTPS, para que o pedido tem de ser feito com HTTPS.

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use your shared key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

Para utilizar a conta SAS para aceder às APIs de nível de serviço para o serviço de BLOBs, construa um objeto de cliente de Blob com a SAS e o ponto final de armazenamento de BLOBs para a sua conta de armazenamento.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "account-name", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

### <a name="example-create-a-stored-access-policy"></a>Exemplo: Criar uma política de acesso armazenadas
O código a seguir cria uma política de acesso armazenadas num contêiner. Pode utilizar a política de acesso para especificar restrições para um serviço SAS no contentor ou os respetivos blobs.

```csharp
private static async Task CreateSharedAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new shared access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

### <a name="example-create-a-service-sas-on-a-container"></a>Exemplo: Criar um serviço SAS num contentor
O código seguinte cria um SAS num contentor. Se o nome de uma política de acesso armazenado existente for fornecido, essa política está associada com a SAS. Se nenhuma política de acesso armazenadas não for fornecida, o código cria uma SAS ad hoc no contentor.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>Exemplo: Criar um serviço SAS num blob
O código seguinte cria um SAS num blob. Se o nome de uma política de acesso armazenado existente for fornecido, essa política está associada com a SAS. Não se for fornecida nenhuma política de acesso armazenadas, em seguida, o código cria uma SAS ad hoc no blob.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

## <a name="conclusion"></a>Conclusão
Assinaturas de acesso partilhado são úteis para fornecer permissões limitadas à sua conta de armazenamento para os clientes que não devem ter a chave da conta. Como tal, são uma parte vital do modelo de segurança para qualquer aplicação com o armazenamento do Azure. Se seguir as melhores práticas indicadas aqui, pode utilizar a SAS para fornecer mais flexibilidade de acesso aos recursos na sua conta de armazenamento, sem comprometer a segurança da sua aplicação.

## <a name="next-steps"></a>Próximos Passos
* [Assinaturas de acesso, parte 2 de partilhado: Criar e utilizar um SAS com armazenamento de BLOBs](../blobs/storage-dotnet-shared-access-signature-part-2.md)
* [Gerir o acesso de leitura anónimo a contentores e blobs](../blobs/storage-manage-access-to-resources.md)
* [Delegar Acesso com uma Assinatura de Acesso Partilhado](https://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Apresentando o SAS da fila e de tabela](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
