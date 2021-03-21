---
title: Conceder acesso limitado a dados com assinaturas de acesso partilhado (SAS)
titleSuffix: Azure Storage
description: Saiba mais sobre a utilização de assinaturas de acesso partilhado (SAS) para delegar o acesso aos recursos de Armazenamento Azure, incluindo bolhas, filas, tabelas e ficheiros.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/28/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 51e73222233602491b0c8ed3835d032610c68e0d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722791"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)

Uma assinatura de acesso partilhado (SAS) proporciona acesso delegado seguro aos recursos na sua conta de armazenamento. Com um SAS, você tem controlo granular sobre como um cliente pode aceder aos seus dados. Por exemplo:
 
- Que recursos o cliente pode aceder.

- Que permissões têm a esses recursos.

- Quanto tempo o SAS é válido.

## <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de acesso partilhado

O Azure Storage suporta três tipos de assinaturas de acesso partilhado:

- Delegação de utilizadores SAS

- Serviço SAS

- Conta SAS

### <a name="user-delegation-sas"></a>Delegação de utilizadores SAS

Uma delegação de utilizador SAS é protegida com credenciais do Azure Ative Directory (Azure AD) e também pelas permissões especificadas para o SAS. Uma delegação de utilizador SAS aplica-se apenas ao armazenamento blob.

Para obter mais informações sobre a delegação de utilizadores SAS, consulte [Criar uma delegação de utilizador SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

### <a name="service-sas"></a>Serviço SAS

Um serviço SAS é seguro com a chave da conta de armazenamento. Um serviço SAS delega acesso a um recurso em apenas um dos serviços de Armazenamento Azure: armazenamento de bolhas, armazenamento de fila, armazenamento de mesa ou ficheiros Azure.

Para obter mais informações sobre o serviço SAS, consulte [Criar um serviço SAS (REST API)](/rest/api/storageservices/create-service-sas).

### <a name="account-sas"></a>Conta SAS

Uma conta SAS é segura com a chave da conta de armazenamento. Uma conta SAS delega o acesso aos recursos em um ou mais dos serviços de armazenamento. Todas as operações disponíveis através de um serviço ou delegação de utilizadores SAS também estão disponíveis através de uma conta SAS. 

Também pode delegar o acesso ao seguinte:

- Operações de nível de serviço (por exemplo, as **operações Get/set Service Properties** e **Get Service Stats).** 

- Leia, escreva e elimine operações que não são permitidas com um serviço SAS. 

Para mais informações sobre a conta SAS, [Crie uma conta SAS (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> A Microsoft recomenda que utilize credenciais AZure AD quando possível como uma melhor prática de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando o design da sua aplicação necessitar de assinaturas de acesso partilhadas para acesso ao armazenamento blob, utilize credenciais Azure AD para criar uma delegação de utilizador SAS quando possível para uma segurança superior. Para obter mais informações, consulte [Autoriza o acesso a bolhas e filas utilizando o Azure Ative Directory](storage-auth-aad.md).

Uma assinatura de acesso partilhado pode tomar um dos dois formulários seguintes:

- **SAS ad hoc.** Quando cria um SAS ad hoc, a hora de início, o tempo de validade e as permissões são especificadas no SAS URI. Qualquer tipo de SAS pode ser um SAS ad hoc.

- **Serviço SAS com política de acesso armazenado.** Uma política de acesso armazenada é definida num recipiente de recursos, que pode ser um recipiente blob, mesa, fila ou partilha de ficheiros. A política de acesso armazenada pode ser utilizada para gerir constrangimentos para uma ou mais assinaturas de acesso partilhado de serviço. Quando associa um serviço SAS a uma política de acesso armazenada, o SAS herda os constrangimentos &mdash; à hora de início, ao prazo de validade e às permissões &mdash; definidas para a política de acesso armazenada.

> [!NOTE]
> Uma delegação de utilizadores SAS ou uma conta SAS deve ser um SAS ad hoc. As políticas de acesso armazenadas não são suportadas para a delegação de utilizadores SAS ou para a conta SAS.

## <a name="how-a-shared-access-signature-works"></a>Como funciona uma assinatura de acesso partilhado

Uma assinatura de acesso partilhado é um URI assinado que aponta para um ou mais recursos de armazenamento. O URI inclui um símbolo que contém um conjunto especial de parâmetros de consulta. O token indica como os recursos podem ser acedidos pelo cliente. Um dos parâmetros de consulta, a assinatura, é construído a partir dos parâmetros SAS e assinado com a chave que foi usada para criar o SAS. Esta assinatura é utilizada pela Azure Storage para autorizar o acesso ao recurso de armazenamento.

> [!NOTE]
> Não é possível auditar a geração de fichas SAS. Qualquer utilizador que tenha privilégios para gerar um token SAS, quer utilizando a chave de conta, quer através de uma atribuição de funções Azure RBAC, pode fazê-lo sem o conhecimento do proprietário da conta de armazenamento. Tenha cuidado para restringir permissões que permitem aos utilizadores gerar fichas SAS. Para evitar que os utilizadores geram um SAS assinado com a chave de conta para cargas de trabalho de bolhas e filas, pode não permitir o acesso da Chave Partilhada à conta de armazenamento. Para obter mais informações, consulte [Prevenir a autorização com chave partilhada.](shared-key-authorization-prevent.md)

### <a name="sas-signature-and-authorization"></a>Assinatura e autorização SAS

Pode assinar um token SAS com uma chave de delegação de utilizador ou com uma chave de conta de armazenamento (Chave Partilhada). 

#### <a name="signing-a-sas-token-with-a-user-delegation-key"></a>Assinar um token SAS com uma chave de delegação de utilizador

Pode assinar um token SAS utilizando uma *chave de delegação de utilizador* criada com credenciais Azure Ative Directory (Azure AD). Uma delegação de utilizadores SAS é assinada com a chave da delegação do utilizador.

Para obter a chave, e depois criar o SAS, um diretor de segurança AZure AD deve ser atribuído um papel Azure que inclua a `Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey` ação. Para obter mais informações, consulte [Criar uma delegação de utilizador SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

#### <a name="signing-a-sas-token-with-an-account-key"></a>Assinar um token SAS com uma chave de conta

Tanto um serviço SAS como uma conta SAS são assinados com a chave da conta de armazenamento. Para criar um SAS assinado com a chave de conta, uma aplicação deve ter acesso à chave da conta.

Quando um pedido inclui um token SAS, esse pedido é autorizado com base na forma como esse token SAS é assinado. A chave de acesso ou credenciais que utiliza para criar um token SAS também são usadas pela Azure Storage para garantir o acesso a um cliente que possui o SAS.

A tabela seguinte resume como cada tipo de token SAS é autorizado.

| Tipo de SAS | Tipo de autorização |
|-|-|
| Delegação de utilizadores SAS (apenas armazenamento blob) | Azure AD |
| Serviço SAS | Chave Partilhada |
| Conta SAS | Chave Partilhada |

A Microsoft recomenda a utilização de uma delegação de utilizador SAS quando possível para uma segurança superior.

### <a name="sas-token"></a>Token de SAS

O token SAS é uma cadeia que se gera do lado do cliente, por exemplo, utilizando uma das bibliotecas de clientes do Azure Storage. O token SAS não é rastreado pelo Azure Storage de forma alguma. Pode criar um número ilimitado de fichas SAS do lado do cliente. Depois de criar um SAS, pode distribuí-lo a aplicações de clientes que requerem acesso a recursos na sua conta de armazenamento.

As aplicações do cliente fornecem o SAS URI ao Azure Storage como parte de um pedido. Em seguida, o serviço verifica os parâmetros SAS e a assinatura para verificar se é válido. Se o serviço verificar se a assinatura é válida, então o pedido é autorizado. Caso contrário, o pedido é recusado com o código de erro 403 (Proibido).

Aqui está um exemplo de um serviço SAS URI, mostrando o recurso URI e o token SAS. Uma vez que o token SAS compreende a cadeia de consulta URI, o recurso URI deve ser seguido primeiro por um ponto de interrogação e, em seguida, pelo token SAS:

![Componentes de um serviço SAS URI](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Quando usar uma assinatura de acesso partilhado

Utilize um SAS para dar acesso seguro aos recursos na sua conta de armazenamento a qualquer cliente que de outra forma não tenha permissões para esses recursos.

Um cenário comum onde um SAS é útil é um serviço onde os utilizadores lêem e escrevem os seus próprios dados na sua conta de armazenamento. Num cenário em que uma conta de armazenamento armazene dados de utilizador, existem dois padrões de conceção típicos:

1. Os clientes carregam e transferem dados através de um serviço de proxy de front-end, o qual realiza a autenticação. Este serviço de procuração frontal permite a validação de regras comerciais. Mas para grandes quantidades de dados, ou transações de grande volume, criar um serviço que possa escalar para corresponder à procura pode ser caro ou difícil.

   ![Diagrama de cenário: Serviço de procuração frontal](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

2. Um serviço simples autentica o cliente conforme necessário e, em seguida, gera uma SAS. Uma vez que a aplicação do cliente recebe o SAS, pode aceder diretamente aos recursos da conta de armazenamento. As permissões de acesso são definidas pelo SAS e pelo intervalo permitido pelo SAS. A SAS reduz a necessidade de encaminhamento de todos os dados através do serviço de proxy de front-end.

   ![Diagrama de cenário: serviço de prestadores SAS](./media/storage-sas-overview/sas-storage-provider-service.png)

Muitos serviços do mundo real podem usar um híbrido destas duas abordagens. Por exemplo, alguns dados podem ser processados e validados através do proxy frontal. Outros dados são guardados e/ou lidos diretamente através do SAS.

Além disso, um SAS é obrigado a autorizar o acesso ao objeto de origem numa operação de cópia em determinados cenários:

- Quando copia uma bolha para outra bolha que reside numa conta de armazenamento diferente. 
  
  Você pode opcionalmente usar um SAS para autorizar o acesso à bolha de destino também.

- Quando copia um ficheiro para outro ficheiro que reside numa conta de armazenamento diferente. 

  Pode utilizar opcionalmente um SAS para autorizar o acesso ao ficheiro de destino também.

- Quando copia uma bolha para um ficheiro, ou um ficheiro para uma bolha. 

  Deve utilizar um SAS mesmo que os objetos de origem e destino residam dentro da mesma conta de armazenamento.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS

Quando utiliza assinaturas de acesso partilhado nas suas aplicações, tem de estar ciente de dois riscos potenciais:

- Se um SAS for vazado, pode ser usado por qualquer pessoa que o obtenha, o que pode potencialmente comprometer a sua conta de armazenamento.

- Se um SAS fornecido a uma aplicação de cliente expirar e a aplicação não conseguir recuperar um novo SAS do seu serviço, então a funcionalidade da aplicação pode ser prejudicada.

As seguintes recomendações para a utilização de assinaturas de acesso partilhado podem ajudar a mitigar estes riscos:

- **Utilize sempre HTTPS** para criar ou distribuir um SAS. Se um SAS for passado sobre HTTP e intercetado, um atacante que realize um ataque homem-no-meio é capaz de ler o SAS. Em seguida, podem usar o SAS tal como o utilizador pretendido poderia ter. Isto pode potencialmente comprometer dados sensíveis ou permitir a corrupção de dados por parte do utilizador malicioso.

- **Utilize, sempre que possível, uma delegação de utilizador SAS.** Uma delegação de utilizador SAS fornece segurança superior a um serviço SAS ou a uma conta SAS. Uma delegação de utilizador SAS está protegida com credenciais Azure AD, para que não precise de armazenar a chave da sua conta com o seu código.

- **Tenha um plano de revogação para um SAS.** Certifique-se de que está preparado para responder se um SAS estiver comprometido.

- **Defina uma política de acesso armazenada para um serviço SAS.** As políticas de acesso armazenadas dão-lhe a opção de revogar permissões para um serviço SAS sem ter de regenerar as chaves da conta de armazenamento. Desloque a expiração sobre estes muito longe no futuro (ou infinito) e certifique-se de que é atualizado regularmente para movê-lo mais longe para o futuro.

- **Utilize tempos de validade de curto prazo num serviço SAS ad hoc ou na conta SAS.** Desta forma, mesmo que um SAS esteja comprometido, é válido apenas por um curto período de tempo. Esta prática é especialmente importante se não puder fazer referência a uma política de acesso armazenada. Os tempos de validade a curto prazo também limitam a quantidade de dados que podem ser escritos a uma bolha, limitando o tempo disponível para o upload.

- **Se necessário, os clientes renovam automaticamente o SAS.** Os clientes devem renovar o SAS muito antes do termo, de forma a dar tempo para as retrações se o serviço de prestação do SAS não estiver disponível. Isto pode ser desnecessário em alguns casos. Por exemplo, pode pretender que o SAS seja utilizado para um pequeno número de operações imediatas e de curta duração. Espera-se que estas operações sejam concluídas dentro do prazo de validade. Como resultado, não está à espera que o SAS seja renovado. No entanto, se tiver um cliente que esteja a fazer pedidos rotineiramente através do SAS, então a possibilidade de expiração entra em jogo. 

- **Tenha cuidado com a hora de início da SAS.** Se definir a hora de início de um SAS para a hora atual, podem ocorrer falhas intermitentes durante os primeiros minutos. Isto deve-se a diferentes máquinas com tempos de corrente ligeiramente diferentes (conhecidos como distorção do relógio). Em geral, desema esta hora de início para pelo menos 15 minutos no passado. Ou não o desemoquila, o que o tornará válido imediatamente em todos os casos. O mesmo geralmente aplica-se ao tempo de validade como bem-- lembre-se que pode observar até 15 minutos de distorção do relógio em qualquer direção em qualquer pedido. Para os clientes que utilizam uma versão REST antes de 2012-02-12, a duração máxima para um SAS que não faz referência a uma política de acesso armazenada é de 1 hora. Quaisquer políticas que especifiquem um prazo superior a 1 hora falharão.

- **Tenha cuidado com o formato de datação SAS.** Para alguns utilitários (como a AzCopy), é necessário que os formatos de datatime sejam '+%Y-%m-%dT%H:%M:%SZ'. Este formato inclui especificamente os segundos. 

- **Seja específico com o recurso a ser acedido.** Uma melhor prática de segurança é proporcionar ao utilizador os privilégios mínimos exigidos. Se um utilizador apenas precisar de ter acesso lido a uma única entidade, então conceda-lhes que leia o acesso a essa entidade única e não leia/escreva/apague o acesso a todas as entidades. Isto também ajuda a diminuir os danos se um SAS estiver comprometido porque o SAS tem menos poder nas mãos de um intruso.

- **Compreenda que a sua conta será cobrada para qualquer utilização, incluindo através de um SAS.** Se fornecer acesso de escrita a uma bolha, um utilizador pode optar por carregar uma bolha de 200 GB. Se também lhes deu acesso de leitura, podem optar por descarregá-lo 10 vezes, incorrendo 2 TB em custos de saída para si. Mais uma vez, forneça permissões limitadas para ajudar a mitigar as potenciais ações de utilizadores maliciosos. Utilize SAS de curta duração para reduzir esta ameaça (mas esteja atento ao desvio do relógio no tempo de fim).

- **Validar dados escritos através de um SAS.** Quando uma aplicação de cliente escreve dados na sua conta de armazenamento, tenha em mente que pode haver problemas com esses dados. Se pretende validar dados, efetue essa validação após a escrita dos dados e antes de ser utilizado pela sua aplicação. Esta prática também protege contra dados corruptos ou maliciosos que sejam escritos na sua conta, seja por um utilizador que tenha adquirido corretamente o SAS, quer por um utilizador que explora um SAS vazado.

- **Saiba quando não deve usar um SAS.** Por vezes, os riscos associados a uma determinada operação contra a sua conta de armazenamento superam os benefícios da utilização de um SAS. Para tais operações, crie um serviço de nível médio que escreva para a sua conta de armazenamento após a validação, autenticação e auditoria das regras de negócio. Além disso, às vezes é mais simples gerir o acesso de outras formas. Por exemplo, se quiser tornar publicamente legível todas as bolhas num recipiente, pode tornar o recipiente público, em vez de fornecer um SAS a cada cliente para acesso.

- **Utilize registos de armazenamento Azure Monitor e Azure para monitorizar a sua aplicação.** Podem ocorrer falhas de autorização devido a uma falha no seu serviço de prestadores SAS. Podem também ocorrer a partir de uma remoção inadvertida de uma política de acesso armazenada. Pode utilizar o Azure Monitor e o registo de análises de armazenamento para observar qualquer pico neste tipo de falhas de autorização. Para obter mais informações, consulte as [métricas de Armazenamento Azure em Azure Monitor](../blobs/monitor-blob-storage.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) e [Azure Storage Analytics .](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

> [!NOTE]
> O armazenamento não rastreia o número de assinaturas de acesso partilhada que foram geradas para uma conta de armazenamento, e nenhuma API pode fornecer este detalhe. Se precisar de saber o número de assinaturas de acesso partilhada que foram geradas para uma conta de armazenamento, deve rastrear o número manualmente.

## <a name="get-started-with-sas"></a>Começar com SAS

Para começar com assinaturas de acesso partilhado, consulte os seguintes artigos para cada tipo SAS.

### <a name="user-delegation-sas"></a>Delegação de utilizadores SAS

- [Criar uma delegação de utilizador SAS para um recipiente ou bolha com PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Criar uma delegação de utilizador SAS para um recipiente ou bolha com o Azure CLI](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Crie uma delegação de utilizador SAS para um recipiente ou bolha com .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Serviço SAS

- [Crie um serviço SAS para um recipiente ou blob com .NET](../blobs/sas-service-create.md)

### <a name="account-sas"></a>Conta SAS

- [Criar uma conta SAS com .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Passos seguintes

- [Delegado de acesso com uma assinatura de acesso partilhado (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Criar uma delegação de utilizadores SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Criar um serviço SAS (REST API)](/rest/api/storageservices/create-service-sas)
- [Criar uma conta SAS (REST API)](/rest/api/storageservices/create-account-sas)
