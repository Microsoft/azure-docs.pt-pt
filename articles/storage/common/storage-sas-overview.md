---
title: Conceder acesso limitado a dados com assinaturas de acesso partilhado (SAS)
titleSuffix: Azure Storage
description: Saiba mais sobre a utilização de assinaturas de acesso partilhado (SAS) para delegar o acesso aos recursos de Armazenamento Azure, incluindo bolhas, filas, tabelas e ficheiros.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: b9882168cd063cb4448269cc6a4949778fe93fb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88509863"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)

Uma assinatura de acesso partilhado (SAS) proporciona acesso delegado seguro aos recursos na sua conta de armazenamento sem comprometer a segurança dos seus dados. Com um SAS, você tem controlo granular sobre como um cliente pode aceder aos seus dados. Pode controlar os recursos a que o cliente pode aceder, quais as permissões que têm sobre esses recursos, e quanto tempo o SAS é válido, entre outros parâmetros.

## <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de acesso partilhado

O Azure Storage suporta três tipos de assinaturas de acesso partilhado:

- **Delegação de utilizadores SAS.** Uma delegação de utilizador SAS é protegida com credenciais do Azure Ative Directory (Azure AD) e também pelas permissões especificadas para o SAS. Uma delegação de utilizador SAS aplica-se apenas ao armazenamento blob.

    Para obter mais informações sobre a delegação de utilizadores SAS, consulte [Criar uma delegação de utilizador SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- **Serviço SAS.** Um serviço SAS é seguro com a chave da conta de armazenamento. Um serviço SAS delega acesso a um recurso em apenas um dos serviços de Armazenamento Azure: armazenamento de bolhas, armazenamento de fila, armazenamento de mesa ou ficheiros Azure.

    Para obter mais informações sobre o serviço SAS, consulte [Criar um serviço SAS (REST API)](/rest/api/storageservices/create-service-sas).

- **Conta SAS.** Uma conta SAS é segura com a chave da conta de armazenamento. Uma conta SAS delega o acesso aos recursos em um ou mais dos serviços de armazenamento. Todas as operações disponíveis através de um serviço ou delegação de utilizadores SAS também estão disponíveis através de uma conta SAS. Além disso, com a conta SAS, pode delegar o acesso a operações que se aplicam ao nível do serviço, tais como As Propriedades de **Serviço Get/set** e operações **get Service Stats.** Também pode delegar o acesso às operações de leitura, escrita e eliminação em contentores de blobs, tabelas, filas e partilhas de ficheiros que não são permitidos com um serviço SAS. 

    Para mais informações sobre a conta SAS, [Crie uma conta SAS (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> A Microsoft recomenda que utilize credenciais AZure AD quando possível como uma melhor prática de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando o design da sua aplicação necessitar de assinaturas de acesso partilhadas para acesso ao armazenamento blob, utilize credenciais Azure AD para criar uma delegação de utilizador SAS quando possível para uma segurança superior. Para obter mais informações, consulte [Autoriza o acesso a bolhas e filas utilizando o Azure Ative Directory](storage-auth-aad.md).

Uma assinatura de acesso partilhado pode assumir uma de duas formas:

- **SAS ad hoc:** Quando se cria um SAS ad hoc, a hora de início, o tempo de validade e as permissões para o SAS estão todas especificadas no SAS URI (ou implícito, se a hora de início for omitida). Qualquer tipo de SAS pode ser um SAS ad hoc.
- **Serviço SAS com política de acesso armazenado:** Uma política de acesso armazenada é definida num recipiente de recursos, que pode ser um recipiente blob, mesa, fila ou partilha de ficheiros. A política de acesso armazenada pode ser utilizada para gerir constrangimentos para uma ou mais assinaturas de acesso partilhado de serviço. Quando associa um serviço SAS a uma política de acesso armazenada, o SAS herda os constrangimentos &mdash; à hora de início, ao prazo de validade e às permissões &mdash; definidas para a política de acesso armazenada.

> [!NOTE]
> Uma delegação de utilizadores SAS ou uma conta SAS deve ser um SAS ad hoc. As políticas de acesso armazenadas não são suportadas para a delegação de utilizadores SAS ou para a conta SAS.

## <a name="how-a-shared-access-signature-works"></a>Como funciona uma assinatura de acesso partilhado

Uma assinatura de acesso partilhado é um URI assinado que aponta para um ou mais recursos de armazenamento e inclui um símbolo que contém um conjunto especial de parâmetros de consulta. O token indica como os recursos podem ser acedidos pelo cliente. Um dos parâmetros de consulta, a assinatura, é construído a partir dos parâmetros SAS e assinado com a chave que foi usada para criar o SAS. Esta assinatura é utilizada pela Azure Storage para autorizar o acesso ao recurso de armazenamento.

### <a name="sas-signature-and-authorization"></a>Assinatura e autorização SAS

Pode assinar um token SAS de duas maneiras:

- Com uma *chave de delegação de utilizador* que foi criada usando credenciais Azure Ative Directory (Azure AD). Uma delegação de utilizadores SAS é assinada com a chave da delegação do utilizador.

    Para obter a chave da delegação do utilizador e criar o SAS, um diretor de segurança AD AZure deve ser atribuído a um papel Azure que inclua a ação **Microsoft.Storage/storageAcounts/blobServices/generateUserDelegationKey.** Para obter informações detalhadas sobre as funções Azure com permissões para obter a chave da delegação do utilizador, consulte [Criar uma delegação de utilizador SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- Com a chave da conta de armazenamento (Chave Partilhada). Tanto um serviço SAS como uma conta SAS são assinados com a chave da conta de armazenamento. Para criar um SAS assinado com a chave de conta, uma aplicação deve ter acesso à chave da conta.

Quando um pedido inclui um token SAS, esse pedido é autorizado com base na forma como esse token SAS é assinado. A chave de acesso ou credenciais que utiliza para criar um token SAS também são usadas pela Azure Storage para garantir o acesso a um cliente que possui o SAS.

A tabela que se segue resume como cada tipo de ficha SAS é autorizada quando é incluída num pedido de armazenamento Azure:

| Tipo de SAS | Tipo de autorização |
|-|-|
| Delegação de utilizadores SAS (apenas armazenamento blob) | Azure AD |
| Serviço SAS | Chave Partilhada |
| Conta SAS | Chave Partilhada |

A Microsoft recomenda a utilização de uma delegação de utilizador SAS quando possível para uma segurança superior.

### <a name="sas-token"></a>Token de SAS

O token SAS é uma cadeia que se gera do lado do cliente, por exemplo, utilizando uma das bibliotecas de clientes do Azure Storage. O token SAS não é rastreado pelo Azure Storage de forma alguma. Pode criar um número ilimitado de fichas SAS do lado do cliente. Depois de criar um SAS, pode distribuí-lo a aplicações de clientes que requerem acesso a recursos na sua conta de armazenamento.

Quando uma aplicação de cliente fornece um SAS URI ao Azure Storage como parte de um pedido, o serviço verifica os parâmetros e assinatura SAS para verificar se é válido para autorizar o pedido. Se o serviço verificar se a assinatura é válida, então o pedido é autorizado. Caso contrário, o pedido é recusado com o código de erro 403 (Proibido).

Eis um exemplo de um URI de SAS de serviço que mostra o URI de recurso e o token de SAS:

![Componentes de um serviço SAS URI](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Quando usar uma assinatura de acesso partilhado

Utilize um SAS quando pretender fornecer acesso seguro aos recursos na sua conta de armazenamento a qualquer cliente que de outra forma não tenha permissões para esses recursos.

Um cenário comum onde um SAS é útil é um serviço onde os utilizadores lêem e escrevem os seus próprios dados na sua conta de armazenamento. Num cenário em que uma conta de armazenamento armazene dados de utilizador, existem dois padrões de conceção típicos:

1. Os clientes carregam e transferem dados através de um serviço de proxy de front-end, o qual realiza a autenticação. Este serviço de procuração frontal tem a vantagem de permitir a validação de regras de negócio, mas para grandes quantidades de dados ou transações de grande volume, criar um serviço que possa escalar para corresponder à procura pode ser caro ou difícil.

   ![Diagrama de cenário: Serviço de procuração frontal](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Um serviço simples autentica o cliente conforme necessário e, em seguida, gera uma SAS. Assim que a aplicação do cliente receber o SAS, podem aceder diretamente aos recursos da conta de armazenamento com as permissões definidas pelo SAS e para o intervalo permitido pelo SAS. A SAS reduz a necessidade de encaminhamento de todos os dados através do serviço de proxy de front-end.

   ![Diagrama de cenário: serviço de prestadores SAS](./media/storage-sas-overview/sas-storage-provider-service.png)

Muitos serviços do mundo real podem usar um híbrido destas duas abordagens. Por exemplo, alguns dados podem ser processados e validados através do proxy frontal, enquanto outros dados são guardados e/ou lidos diretamente usando SAS.

Além disso, um SAS é obrigado a autorizar o acesso ao objeto de origem numa operação de cópia em determinados cenários:

- Quando copiar uma bolha para outra bolha que reside numa conta de armazenamento diferente, deve utilizar um SAS para autorizar o acesso à bolha de origem. Você pode opcionalmente usar um SAS para autorizar o acesso à bolha de destino também.
- Quando copiar um ficheiro para outro ficheiro que resida numa conta de armazenamento diferente, deve utilizar um SAS para autorizar o acesso ao ficheiro de origem. Pode utilizar opcionalmente um SAS para autorizar o acesso ao ficheiro de destino também.
- Quando copiar uma bolha para um ficheiro ou um ficheiro para uma bolha, deve utilizar um SAS para autorizar o acesso ao objeto de origem, mesmo que os objetos de origem e destino residam dentro da mesma conta de armazenamento.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS

Quando utiliza assinaturas de acesso partilhado nas suas aplicações, tem de estar ciente de dois riscos potenciais:

- Se um SAS for vazado, pode ser usado por qualquer pessoa que o obtenha, o que pode potencialmente comprometer a sua conta de armazenamento.
- Se um SAS fornecido a uma aplicação de cliente expirar e a aplicação não conseguir recuperar um novo SAS do seu serviço, então a funcionalidade da aplicação pode ser prejudicada.

As seguintes recomendações para a utilização de assinaturas de acesso partilhado podem ajudar a mitigar estes riscos:

- **Utilize sempre HTTPS** para criar ou distribuir um SAS. Se um SAS for ultrapassado em HTTP e intercetado, um intruso que realize um ataque homem-no-meio é capaz de ler o SAS e, em seguida, usá-lo como o utilizador pretendido poderia ter, potencialmente comprometendo dados sensíveis ou permitindo a corrupção de dados pelo utilizador malicioso.
- **Utilize, sempre que possível, uma delegação de utilizador SAS.** Uma delegação de utilizador SAS fornece segurança superior a um serviço SAS ou a uma conta SAS. Uma delegação de utilizador SAS está protegida com credenciais Azure AD, para que não precise de armazenar a chave da sua conta com o seu código.
- **Tenha um plano de revogação para um SAS.** Certifique-se de que está preparado para responder se um SAS estiver comprometido.
- **Defina uma política de acesso armazenada para um serviço SAS.** As políticas de acesso armazenadas dão-lhe a opção de revogar permissões para um serviço SAS sem ter de regenerar as chaves da conta de armazenamento. Desloque a expiração sobre estes muito longe no futuro (ou infinito) e certifique-se de que é atualizado regularmente para movê-lo mais longe para o futuro.
- **Utilize tempos de validade de curto prazo num serviço SAS ad hoc ou na conta SAS.** Desta forma, mesmo que um SAS esteja comprometido, é válido apenas por um curto período de tempo. Esta prática é especialmente importante se não puder fazer referência a uma política de acesso armazenada. Os tempos de validade a curto prazo também limitam a quantidade de dados que podem ser escritos a uma bolha, limitando o tempo disponível para o upload.
- **Se necessário, os clientes renovam automaticamente o SAS.** Os clientes devem renovar o SAS muito antes do termo, de forma a dar tempo para as retrações se o serviço de prestação do SAS não estiver disponível. Se o seu SAS for destinado a ser utilizado para um pequeno número de operações imediatas e de curta duração que se espera que sejam concluídas dentro do período de validade, então isso pode ser desnecessário, uma vez que não se espera que o SAS seja renovado. No entanto, se tiver cliente que está a fazer pedidos rotineiramente através do SAS, então a possibilidade de expiração entra em jogo. A principal consideração é equilibrar a necessidade de o SAS ser de curta duração (como anteriormente declarado) com a necessidade de garantir que o cliente está a solicitar a renovação suficientemente cedo (para evitar perturbações devido à expiração do SAS antes da renovação bem sucedida).
- **Tenha cuidado com a hora de início da SAS.** Se definir a hora de início de um SAS para a hora atual, poderá observar falhas que ocorrem intermitentemente durante os primeiros minutos devido a diferentes máquinas com ligeiras variações na hora atual (conhecida como distorção do relógio). Em geral, desema esta hora de início para pelo menos 15 minutos no passado. Ou não o desemoquila, o que o tornará válido imediatamente em todos os casos. O mesmo geralmente aplica-se ao tempo de validade como bem-- lembre-se que pode observar até 15 minutos de distorção do relógio em qualquer direção em qualquer pedido. Para os clientes que utilizam uma versão REST antes de 2012-02-12, a duração máxima para um SAS que não faz referência a uma política de acesso armazenado é de 1 hora, e quaisquer políticas que especifiquem um prazo mais longo do que isso falhará.
- **Tenha cuidado com o formato de datação SAS.** Se definir a hora de início e/ou a expiração de um SAS, para alguns utilitários (por exemplo para o utilitário de linha de comando AzCopy) precisa do formato de data para ser '+%%m-%m-%dT%H:%M:%SZ', especificamente incluindo os segundos para que funcione usando o token SAS.  
- **Seja específico com o recurso a ser acedido.** Uma melhor prática de segurança é proporcionar ao utilizador os privilégios mínimos exigidos. Se um utilizador apenas precisar de ter acesso lido a uma única entidade, então conceda-lhes que leia o acesso a essa entidade única e não leia/escreva/apague o acesso a todas as entidades. Isto também ajuda a diminuir os danos se um SAS estiver comprometido porque o SAS tem menos poder nas mãos de um intruso.
- **Compreenda que a sua conta será cobrada para qualquer utilização, incluindo através de um SAS.** Se fornecer acesso de escrita a uma bolha, um utilizador pode optar por carregar uma bolha de 200 GB. Se também lhes deu acesso de leitura, podem optar por descarregá-lo 10 vezes, incorrendo 2 TB em custos de saída para si. Mais uma vez, forneça permissões limitadas para ajudar a mitigar as potenciais ações de utilizadores maliciosos. Utilize SAS de curta duração para reduzir esta ameaça (mas esteja atento ao desvio do relógio no tempo de fim).
- **Validar dados escritos através de um SAS.** Quando uma aplicação de cliente escreve dados na sua conta de armazenamento, tenha em mente que pode haver problemas com esses dados. Se a sua aplicação exigir que os dados sejam validados ou autorizados antes de estar pronto a ser utilizado, deverá efetuar esta validação após a escrita dos dados e antes de ser utilizada pela sua aplicação. Esta prática também protege contra dados corruptos ou maliciosos que sejam escritos na sua conta, seja por um utilizador que tenha adquirido corretamente o SAS, quer por um utilizador que explora um SAS vazado.
- **Saiba quando não deve usar um SAS.** Por vezes, os riscos associados a uma determinada operação contra a sua conta de armazenamento superam os benefícios da utilização de um SAS. Para tais operações, crie um serviço de nível médio que escreva para a sua conta de armazenamento após a validação, autenticação e auditoria das regras de negócio. Além disso, às vezes é mais simples gerir o acesso de outras formas. Por exemplo, se quiser tornar publicamente legível todas as bolhas num recipiente, pode tornar o recipiente público, em vez de fornecer um SAS a cada cliente para acesso.
- **Utilize registos de armazenamento Azure Monitor e Azure para monitorizar a sua aplicação.** Pode utilizar o Azure Monitor e o armazenamento de analítica para observar qualquer pico de falhas de autorização devido a uma falha no seu serviço de prestadores SAS ou à remoção inadvertida de uma política de acesso armazenada. Para obter mais informações, consulte as [métricas de Armazenamento Azure em Azure Monitor](monitor-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e [Azure Storage Analytics .](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="get-started-with-sas"></a>Começar com SAS

Para começar com assinaturas de acesso partilhado, consulte os seguintes artigos para cada tipo SAS.

### <a name="user-delegation-sas"></a>Delegação de utilizadores SAS

- [Criar uma delegação de utilizador SAS para um recipiente ou bolha com PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Criar uma delegação de utilizador SAS para um recipiente ou bolha com o Azure CLI](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Crie uma delegação de utilizador SAS para um recipiente ou bolha com .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Serviço SAS

- [Crie um serviço SAS para um recipiente ou blob com .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Conta SAS

- [Criar uma conta SAS com .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Passos seguintes

- [Delegado de acesso com uma assinatura de acesso partilhado (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Criar uma delegação de utilizadores SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Criar um serviço SAS (REST API)](/rest/api/storageservices/create-service-sas)
- [Criar uma conta SAS (REST API)](/rest/api/storageservices/create-account-sas)
