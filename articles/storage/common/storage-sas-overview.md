---
title: Conceder acesso limitado a dados com assinaturas de acesso partilhado (SAS)
titleSuffix: Azure Storage
description: Saiba utilizar assinaturas de acesso partilhado (SAS) para delegar acesso aos recursos do Armazenamento Azure, incluindo bolhas, filas, mesas e ficheiros.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 680c1b036b9b41edb1115f478fd5dc8f63ea1d02
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82688030"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Conceder acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)

Uma assinatura de acesso partilhado (SAS) fornece acesso delegado seguro aos recursos na sua conta de armazenamento sem comprometer a segurança dos seus dados. Com um SAS, tem controlo granular sobre como um cliente pode aceder aos seus dados. Pode controlar os recursos a que o cliente pode aceder, quais as permissões que tem sobre esses recursos, e quanto tempo o SAS é válido, entre outros parâmetros.

## <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de acesso partilhado

O Azure Storage suporta três tipos de assinaturas de acesso partilhado:

- **Delegação de utilizadores SAS.** Uma delegação de utilizadores SAS é assegurada com credenciais azure Ative Directory (Azure AD) e também pelas permissões especificadas para o SAS. Uma delegação de utilizadores SAS aplica-se apenas ao armazenamento blob.

    Para obter mais informações sobre a delegação de utilizadores SAS, consulte [Criar uma delegação de utilizadores SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- **Serviço SAS.** Um serviço SAS é seguro com a chave da conta de armazenamento. Um serviço Que a SAS delega acesso a um recurso em apenas um dos serviços de Armazenamento Azure: armazenamento de blob, armazenamento de fila, armazenamento de mesa ou Ficheiros Azure. 

    Para mais informações sobre o serviço SAS, consulte [Criar um serviço SAS (REST API)](/rest/api/storageservices/create-service-sas).

- **Conta SAS.** Uma conta SAS é segurada com a chave da conta de armazenamento. Uma conta SAS delega o acesso aos recursos em um ou mais dos serviços de armazenamento. Todas as operações disponíveis através de um serviço ou delegação de utilizadores SAS também estão disponíveis através de uma conta SAS. Além disso, com a conta SAS, pode delegar o acesso a operações que se aplicam ao nível do serviço, tais como **Obter/set Service Properties** e **Obter operações** de Estatísticas de Serviço. Também pode delegar o acesso às operações de leitura, escrita e eliminação em contentores de blobs, tabelas, filas e partilhas de ficheiros que não são permitidos com um serviço SAS. 

    Para mais informações sobre a conta SAS, [Crie uma conta SAS (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> A Microsoft recomenda que utilize credenciais de AD Azure sempre que possível como uma boa prática de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando o design da sua aplicação necessitar de assinaturas de acesso partilhado para acesso ao armazenamento Blob, utilize credenciais Azure AD para criar uma delegação de utilizadores SAS sempre que possível para uma segurança superior.

Uma assinatura de acesso partilhado pode assumir uma de duas formas:

- **Ad hoc SAS:** Quando se cria um SAS ad hoc, o tempo de início, o tempo de validade e as permissões para o SAS são todas especificadas no SAS URI (ou implícito, se a hora de início for omitida). Qualquer tipo de SAS pode ser um SAS ad hoc.
- **Serviço SAS com política de acesso armazenada:** Uma política de acesso armazenada é definida num recipiente de recursos, que pode ser um recipiente de bolhas, mesa, fila ou partilha de ficheiros. A política de acesso armazenada pode ser utilizada para gerir constrangimentos para uma ou mais assinaturas de acesso partilhado seleto. Quando associa um serviço SAS a uma política de acesso armazenada, o SAS herda os constrangimentos&mdash;o tempo de início, o tempo de validade e as permissões definidas&mdash;para a política de acesso armazenada.

> [!NOTE]
> Uma delegação de utilizadores SAS ou uma conta SAS deve ser um SAS ad hoc. As políticas de acesso armazenadas não são suportadas para a delegação de utilizadores SAS ou para a conta SAS.

## <a name="how-a-shared-access-signature-works"></a>Como funciona uma assinatura de acesso partilhado

Uma assinatura de acesso partilhado é um URI assinado que aponta para um ou mais recursos de armazenamento e inclui um símbolo que contém um conjunto especial de parâmetros de consulta. O símbolo indica como os recursos podem ser acedidos pelo cliente. Um dos parâmetros de consulta, a assinatura, é construído a partir dos parâmetros SAS e assinado com a chave que foi usada para criar o SAS. Esta assinatura é utilizada pelo Azure Storage para autorizar o acesso ao recurso de armazenamento.

### <a name="sas-signature"></a>Assinatura SAS

Pode assinar um SAS de duas maneiras:

- Com uma chave de *delegação* de utilizadores que foi criada usando credenciais azure Ative Directory (Azure AD). Uma delegação de utilizadores SAS é assinada com a chave da delegação de utilizadores.

    Para obter a chave da delegação do utilizador e criar o SAS, um diretor de segurança azure AD deve ser atribuído a uma função de controlo de acesso baseado em funções (RBAC) que inclua a ação **Microsoft.Storage/storageAccounts/blobServices/generateUserKey.** Para obter informações detalhadas sobre as funções RBAC com permissões para obter a chave da delegação do utilizador, consulte [Criar uma delegação de utilizadores SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- Com a chave da conta de armazenamento. Tanto um serviço SAS como uma conta SAS são assinados com a chave da conta de armazenamento. Para criar um SAS assinado com a chave da conta, um pedido deve ter acesso à chave da conta.

### <a name="sas-token"></a>Ficha SAS

O token SAS é uma cadeia que você gera do lado do cliente, por exemplo, usando uma das bibliotecas de clientes do Azure Storage. O token SAS não é rastreado pelo Armazenamento Azure de forma alguma. Pode criar um número ilimitado de fichas SAS do lado do cliente. Depois de criar um SAS, pode distribuí-lo a aplicações de clientes que requerem acesso a recursos na sua conta de armazenamento.

Quando uma aplicação de cliente fornece um SAS URI ao Armazenamento Azure como parte de um pedido, o serviço verifica os parâmetros E assinatura SAS para verificar se é válido para autorizar o pedido. Se o serviço verificar se a assinatura é válida, então o pedido é autorizado. Caso contrário, o pedido é recusado com o código de erro 403 (Proibido).

Aqui está um exemplo de um serviço SAS URI, mostrando o recurso URI e o token SAS:

![Componentes de um serviço SAS URI](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Quando usar uma assinatura de acesso partilhado

Utilize um SAS quando pretender fornecer acesso seguro aos recursos na sua conta de armazenamento a qualquer cliente que de outra forma não tenha permissões para esses recursos.

Um cenário comum em que um SAS é útil é um serviço onde os utilizadores lêem e escrevem os seus próprios dados na sua conta de armazenamento. Num cenário em que uma conta de armazenamento armazene dados de utilizador, existem dois padrões de conceção típicos:

1. Os clientes carregam e transferem dados através de um serviço de proxy de front-end, o qual realiza a autenticação. Este serviço de procuração frontal tem a vantagem de permitir a validação das regras de negócio, mas para grandes quantidades de dados ou transações de alto volume, criar um serviço que pode escalar para corresponder à procura pode ser caro ou difícil.

   ![Diagrama de cenário: Serviço de procuração frontal](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Um serviço simples autentica o cliente conforme necessário e, em seguida, gera uma SAS. Assim que a aplicação do cliente receber o SAS, podem aceder diretamente aos recursos da conta de armazenamento com as permissões definidas pelo SAS e pelo intervalo permitido pelo SAS. A SAS reduz a necessidade de encaminhamento de todos os dados através do serviço de proxy de front-end.

   ![Diagrama de cenário: Serviço de prestador sas](./media/storage-sas-overview/sas-storage-provider-service.png)

Muitos serviços do mundo real podem usar um híbrido destas duas abordagens. Por exemplo, alguns dados podem ser processados e validados através do proxy frontal, enquanto outros dados são guardados e/ou lidos diretamente usando sas.

Além disso, é necessário um SAS para autorizar o acesso ao objeto de origem numa operação de cópia em determinados cenários:

- Quando copia uma bolha para outra bolha que reside numa conta de armazenamento diferente, deve utilizar um SAS para autorizar o acesso à bolha de origem. Você pode opcionalmente usar um SAS para autorizar o acesso à bolha de destino também.
- Quando copia um ficheiro para outro ficheiro que reside numa conta de armazenamento diferente, deve utilizar um SAS para autorizar o acesso ao ficheiro fonte. Também pode utilizar opcionalmente um SAS para autorizar o acesso ao ficheiro de destino.
- Quando copia uma bolha para um ficheiro, ou um ficheiro para uma bolha, deve utilizar um SAS para autorizar o acesso ao objeto de origem, mesmo que os objetos de origem e destino residam na mesma conta de armazenamento.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS

Quando utiliza assinaturas de acesso partilhado nas suas aplicações, tem de estar ciente de dois riscos potenciais:

- Se um SAS for vazado, pode ser usado por qualquer pessoa que o obtenha, o que pode potencialmente comprometer a sua conta de armazenamento.
- Se um SAS fornecido a uma aplicação de cliente expirar e a aplicação não conseguir recuperar um novo SAS do seu serviço, então a funcionalidade da aplicação poderá ser prejudicada.

As seguintes recomendações para a utilização de assinaturas de acesso partilhado podem ajudar a mitigar estes riscos:

- **Utilize sempre https** para criar ou distribuir um SAS. Se um SAS for transmitido em HTTP e intercetado, um intruso que realize um ataque man-in-the-middle é capaz de ler o SAS e, em seguida, usá-lo como o utilizador pretendido poderia ter, potencialmente comprometendo dados sensíveis ou permitindo a corrupção de dados pelo utilizador malicioso.
- **Utilize uma delegação de utilizadores SAS sempre que possível.** Uma delegação de utilizadores SAS fornece segurança superior a um serviço SAS ou a uma conta SAS. Uma delegação de utilizadores SAS está protegida com credenciais De AD Azure, para que não precise de armazenar a sua chave de conta com o seu código.
- **Tenha um plano de revogação para um SAS.** Certifique-se de que está preparado para responder se um SAS estiver comprometido.
- **Defina uma política de acesso armazenada para um serviço SAS.** As políticas de acesso armazenadas dão-lhe a opção de revogar permissões para um serviço SAS sem ter de regenerar as chaves da conta de armazenamento. Delineie a expiração nestes muito longe no futuro (ou infinito) e certifique-se de que é regularmente atualizado para movê-lo mais longe no futuro.
- **Utilize prazos de validade de curto prazo num serviço SAS ou conta SAS.** Desta forma, mesmo que um SAS esteja comprometido, é válido apenas por um curto período de tempo. Esta prática é especialmente importante se não conseguir fazer referência a uma política de acesso armazenada. Os prazos de validade a curto prazo também limitam a quantidade de dados que podem ser escritos a uma bolha, limitando o tempo disponível para o seu upload.
- **Se necessário, os clientes renovam automaticamente o SAS.** Os clientes devem renovar o SAS muito antes da expiração, de forma a permitir tempo para repetições se o serviço que presta o SAS não estiver disponível. Se o seu SAS for destinado a ser utilizado para um pequeno número de operações imediatas e de curta duração que se espera que sejam concluídas dentro do período de validade, então isso pode ser desnecessário, uma vez que não se espera que o SAS seja renovado. No entanto, se tem um cliente que está a fazer pedidos de rotina via SAS, então a possibilidade de expiração entra em jogo. A principal consideração é equilibrar a necessidade de o SAS ser de curta duração (como anteriormente declarado) com a necessidade de garantir que o cliente está a solicitar a renovação suficientemente cedo (para evitar perturbações devido à expiração do SAS antes da renovação bem sucedida).
- **Tenha cuidado com a hora de início da SAS.** Se definir a hora de início para um SAS **até agora**, então devido ao desvio do relógio (diferenças no tempo atual de acordo com diferentes máquinas), as falhas podem ser observadas intermitentemente durante os primeiros minutos. Em geral, desuma a hora de início para pelo menos 15 minutos no passado. Ou, não o despôs, o que o tornará válido imediatamente em todos os casos. O mesmo geral mente se aplica ao tempo de validade, bem -- lembre-se que pode observar até 15 minutos de rotação do relógio em qualquer direção a qualquer pedido. Para os clientes que utilizem uma versão REST antes de 2012-02-12, a duração máxima para um SAS que não referencia uma política de acesso armazenada é de 1 hora, e quaisquer políticas que especifiquem a longo prazo do que isso falhará.
- **Tenha cuidado com o formato de data sAS.** Se definir a hora de início e/ou expiração para um SAS, para alguns utilitários (por exemplo, para o utilitário de linha de comando AzCopy) precisa que o formato de data seja "+%Y-%m%dT%H:%M%SZ", especificamente incluindo os segundos para que funcione utilizando o token SAS.  
- **Seja específico com o recurso a ser acedido.** Uma boa prática de segurança é fornecer a um utilizador os privilégios mínimos exigidos. Se um utilizador apenas necessitar de ler o acesso a uma única entidade, conceda-lhe acesso a essa entidade única e não leia/escreva/apague o acesso a todas as entidades. Isto também ajuda a diminuir os danos se um SAS estiver comprometido porque o SAS tem menos poder nas mãos de um intruso.
- **Entenda que a sua conta será cobrada para qualquer utilização, incluindo através de um SAS.** Se fornecer acesso por escrito a uma bolha, um utilizador pode optar por carregar uma bolha de 200 GB. Se também lhes deu acesso de leitura, podem optar por descarregá-lo 10 vezes, incorrendo em 2 TB em custos de saída para si. Mais uma vez, forneça permissões limitadas para ajudar a mitigar as ações potenciais de utilizadores maliciosos. Utilize SAS de curta duração para reduzir esta ameaça (mas esteja atento ao desvio do relógio no tempo final).
- **Validar os dados escritos com recurso a um SAS.** Quando uma aplicação de cliente escreve dados na sua conta de armazenamento, lembre-se que pode haver problemas com esses dados. Se a sua aplicação exigir que os dados sejam validados ou autorizados antes de estar pronto a ser utilizado, deverá efetuar esta validação depois de os dados serem escritos e antes de serem utilizados pela sua aplicação. Esta prática também protege contra dados corruptos ou maliciosos que estão a ser escritos na sua conta, quer por um utilizador que tenha adquirido corretamente o SAS, quer por um utilizador que explora um SAS vazado.
- **Saiba quando não utilizar um SAS.** Por vezes, os riscos associados a uma determinada operação contra a sua conta de armazenamento superam os benefícios da utilização de um SAS. Para tais operações, crie um serviço de nível médio que escreva na sua conta de armazenamento após a realização de validação, autenticação e auditoria da regra do negócio. Além disso, às vezes é mais simples gerir o acesso de outras formas. Por exemplo, se quiser tornar todas as bolhas num contentor publicamente legível, pode tornar o contentor público, em vez de fornecer um SAS a cada cliente para acesso.
- **Utilize registos de armazenamento Azure Monitor e Azure para monitorizar a sua aplicação.** Pode utilizar o Registo De Monitorização azure e a exploração de armazenamento para observar qualquer pico de falhas de autorização devido a uma falha no serviço de fornecedor sas ou à remoção inadvertida de uma política de acesso armazenada. Para mais informações, consulte as métricas de [Armazenamento Azure no Azure Monitor](monitor-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e na [exploração de Armazenamento Azure](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)Analytics.

## <a name="get-started-with-sas"></a>Começar com sas

Para começar com assinaturas de acesso partilhado, consulte os seguintes artigos para cada tipo SAS.

### <a name="user-delegation-sas"></a>Delegação de utilizadores SAS

- [Criar uma delegação de utilizadores SAS para um recipiente ou bolha com powerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Criar uma delegação de utilizadores SAS para um recipiente ou bolha com o Azure CLI](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Criar uma delegação de utilizadores SAS para um recipiente ou bolha com .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>SAS de serviço

- [Crie um Serviço SAS para um recipiente ou bolha com .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Contas SAS

- [Criar uma conta SAS com .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Passos seguintes

- [Acesso delegado com assinatura de acesso partilhado (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Criar uma delegação de utilizadores SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Criar um serviço SAS (REST API)](/rest/api/storageservices/create-service-sas)
- [Criar uma conta SAS (REST API)](/rest/api/storageservices/create-account-sas)
