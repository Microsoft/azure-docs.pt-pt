---
title: Conceder acesso limitado a dados com SAS (assinaturas de acesso compartilhado)
titleSuffix: Azure Storage
description: Saiba mais sobre como usar SAS (assinaturas de acesso compartilhado) para delegar acesso aos recursos de armazenamento do Azure, incluindo BLOBs, filas, tabelas e arquivos.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e4a5f83e3f4d26c2321ed1b4c48a385d07e6489d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895145"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)

Uma SAS (assinatura de acesso compartilhado) fornece acesso delegado seguro aos recursos em sua conta de armazenamento sem comprometer a segurança de seus dados. Com uma SAS, você tem controle granular sobre como um cliente pode acessar seus dados. Você pode controlar quais recursos o cliente pode acessar, quais permissões eles têm sobre esses recursos e por quanto tempo a SAS é válida, entre outros parâmetros.

## <a name="types-of-shared-access-signatures"></a>Tipos de assinaturas de acesso partilhado

O armazenamento do Azure dá suporte a três tipos de assinaturas de acesso compartilhado:

- **SAS de delegação de usuário (visualização).** Uma SAS de delegação de usuário é protegida com as credenciais do Azure Active Directory (AD do Azure) e também pelas permissões especificadas para a SAS. Uma SAS de delegação de usuário se aplica somente ao armazenamento de BLOB.

    Para obter mais informações sobre a SAS de delegação de usuário, consulte [criar uma delegação de usuário de SAS (API REST)](/rest/api/storageservices/create-user-delegation-sas).

- **SAS de serviço.** Uma SAS de serviço é protegida com a chave da conta de armazenamento. Uma SAS de serviço Delega acesso a um recurso em apenas um dos serviços de armazenamento do Azure: armazenamento de BLOBs, armazenamento de filas, armazenamento de tabelas ou arquivos do Azure. 

    Para obter mais informações sobre a SAS do serviço, consulte [criar um serviço SAS (API REST)](/rest/api/storageservices/create-service-sas).

- **SAS da conta.** Uma SAS de conta é protegida com a chave da conta de armazenamento. Uma conta SAS delega o acesso aos recursos em um ou mais dos serviços de armazenamento. Todas as operações disponíveis por meio de uma SAS de delegação de serviço ou de usuário também estão disponíveis por meio de uma SAS de conta. Além disso, com a SAS da conta, você pode delegar acesso a operações que se aplicam ao nível do serviço, como **obter/definir propriedades de serviço** e obter as operações de **Estatísticas de serviço** . Também pode delegar o acesso às operações de leitura, escrita e eliminação em contentores de blobs, tabelas, filas e partilhas de ficheiros que não são permitidos com um serviço SAS. 

    Para obter mais informações sobre a SAS da conta, [crie uma SAS da conta (API REST)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> A Microsoft recomenda que você use as credenciais do Azure AD quando possível como uma prática recomendada de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando o design do aplicativo exigir assinaturas de acesso compartilhado para acesso ao armazenamento de BLOB, use as credenciais do Azure AD para criar uma SAS de delegação de usuário quando possível para segurança superior.

Uma assinatura de acesso compartilhado pode ter uma das duas formas:

- **SAS ad hoc:** Quando você cria uma SAS ad hoc, a hora de início, a hora de expiração e as permissões para a SAS são todas especificadas no URI de SAS (ou implícita, se a hora de início for omitida). Qualquer tipo de SAS pode ser uma SAS ad hoc.
- **SAS de serviço com política de acesso armazenada:** Uma política de acesso armazenada é definida em um contêiner de recursos, que pode ser um contêiner de BLOBs, uma tabela, uma fila ou um compartilhamento de arquivos. A política de acesso armazenada pode ser usada para gerenciar restrições para uma ou mais assinaturas de acesso compartilhado do serviço. Quando você associa uma SAS de serviço a uma política de acesso armazenada, a SAS herda as restrições&mdash;hora de início, hora de expiração e permissões&mdash;definidas para a política de acesso armazenada.

> [!NOTE]
> Uma SAS de delegação de usuário ou uma SAS de conta deve ser uma SAS ad hoc. As políticas de acesso armazenadas não têm suporte para a SAS de delegação de usuário ou a SAS da conta.

## <a name="how-a-shared-access-signature-works"></a>Como funciona uma assinatura de acesso compartilhado

Uma assinatura de acesso compartilhado é um URI assinado que aponta para um ou mais recursos de armazenamento e inclui um token que contém um conjunto especial de parâmetros de consulta. O token indica como os recursos podem ser acessados pelo cliente. Um dos parâmetros de consulta, a assinatura, é construído a partir dos parâmetros de SAS e assinado com a chave que foi usada para criar a SAS. Essa assinatura é usada pelo armazenamento do Azure para autorizar o acesso ao recurso de armazenamento.

### <a name="sas-signature"></a>Assinatura SAS

Você pode assinar uma SAS de uma das duas maneiras:

- Com uma *chave de delegação de usuário* que foi criada usando as credenciais do Azure Active Directory (AD do Azure). Uma SAS de delegação de usuário é assinada com a chave de delegação de usuário.

    Para obter a chave de delegação de usuário e criar a SAS, uma entidade de segurança do Azure AD deve ser atribuída a uma função de RBAC (controle de acesso baseado em função) que inclui a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** . Para obter informações detalhadas sobre as funções RBAC com permissões para obter a chave de delegação de usuário, consulte [criar uma delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas).

- Com a chave da conta de armazenamento. Uma SAS de serviço e uma SAS de conta são assinadas com a chave da conta de armazenamento. Para criar uma SAS que é assinada com a chave de conta, um aplicativo deve ter acesso à chave de conta.

### <a name="sas-token"></a>Token de SAS

O token SAS é uma cadeia de caracteres que você gera no lado do cliente, por exemplo, usando uma das bibliotecas de cliente do armazenamento do Azure. O token SAS não é acompanhado pelo armazenamento do Azure de nenhuma forma. Você pode criar um número ilimitado de tokens SAS no lado do cliente. Depois de criar uma SAS, você pode distribuí-la aos aplicativos cliente que exigem acesso aos recursos em sua conta de armazenamento.

Quando um aplicativo cliente fornece um URI de SAS para o armazenamento do Azure como parte de uma solicitação, o serviço verifica os parâmetros SAS e a assinatura para verificar se ele é válido para autorizar a solicitação. Se o serviço verificar que a assinatura é válida, a solicitação será autorizada. Caso contrário, a solicitação será recusada com o código de erro 403 (proibido).

Aqui está um exemplo de um URI de SAS de serviço, mostrando o URI de recurso e o token SAS:

![Componentes de um URI de SAS de serviço](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Quando usar uma assinatura de acesso compartilhado

Use uma SAS quando desejar fornecer acesso seguro aos recursos em sua conta de armazenamento para qualquer cliente que não tenha permissões para esses recursos.

Um cenário comum em que uma SAS é útil é um serviço no qual os usuários lêem e gravam seus próprios dados em sua conta de armazenamento. Num cenário em que uma conta de armazenamento armazene dados de utilizador, existem dois padrões de conceção típicos:

1. Os clientes carregam e transferem dados através de um serviço de proxy de front-end, o qual realiza a autenticação. Esse serviço de proxy de front-end tem a vantagem de permitir a validação de regras de negócio, mas, para grandes quantidades de dados ou transações de alto volume, criar um serviço que pode ser dimensionado para corresponder a demanda pode ser caro ou difícil.

   ![Diagrama de cenário: serviço de proxy de front-end](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Um serviço simples autentica o cliente conforme necessário e, em seguida, gera uma SAS. Depois que o aplicativo cliente recebe a SAS, ele pode acessar os recursos da conta de armazenamento diretamente com as permissões definidas pela SAS e para o intervalo permitido pela SAS. A SAS reduz a necessidade de encaminhamento de todos os dados através do serviço de proxy de front-end.

   ![Diagrama de cenário: serviço de provedor SAS](./media/storage-sas-overview/sas-storage-provider-service.png)

Muitos serviços do mundo real podem usar um híbrido dessas duas abordagens. Por exemplo, alguns dados podem ser processados e validados por meio do proxy de front-end, enquanto outros dados são salvos e/ou lidos diretamente usando SAS.

Além disso, uma SAS é necessária para autorizar o acesso ao objeto de origem em uma operação de cópia em determinados cenários:

- Ao copiar um blob para outro blob que reside em uma conta de armazenamento diferente, você deve usar uma SAS para autorizar o acesso ao blob de origem. Opcionalmente, você pode usar uma SAS para autorizar o acesso ao blob de destino também.
- Quando você copia um arquivo para outro arquivo que reside em uma conta de armazenamento diferente, você deve usar uma SAS para autorizar o acesso ao arquivo de origem. Opcionalmente, você pode usar uma SAS para autorizar o acesso ao arquivo de destino também.
- Ao copiar um blob para um arquivo, ou um arquivo para um blob, você deve usar uma SAS para autorizar o acesso ao objeto de origem, mesmo que os objetos de origem e destino residam na mesma conta de armazenamento.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS

Ao usar assinaturas de acesso compartilhado em seus aplicativos, você precisa estar ciente de dois riscos potenciais:

- Se uma SAS for vazada, ela poderá ser usada por qualquer pessoa que a obtenha, o que pode comprometer sua conta de armazenamento.
- Se uma SAS fornecida a um aplicativo cliente expirar e o aplicativo não puder recuperar uma nova SAS do seu serviço, a funcionalidade do aplicativo poderá ser prejudicada.

As recomendações a seguir para usar assinaturas de acesso compartilhado podem ajudar a mitigar esses riscos:

- **Sempre use HTTPS** para criar ou distribuir uma SAS. Se uma SAS for passada por HTTP e interceptada, um invasor executando um ataque man-in-the-Middle será capaz de ler a SAS e, em seguida, usá-la da mesma forma que o usuário pretendido pode ter, potencialmente comprometendo dados confidenciais ou permitindo corrupção de dados pelo usuário mal-intencionado.
- **Use uma SAS de delegação de usuário quando possível.** Uma SAS de delegação de usuário fornece segurança superior a uma SAS de serviço ou a uma SAS de conta. Uma SAS de delegação de usuário é protegida com as credenciais do Azure AD, para que você não precise armazenar sua chave de conta com seu código.
- **Ter um plano de revogação em vigor para uma SAS.** Verifique se você está preparado para responder se uma SAS está comprometida.
- **Defina uma política de acesso armazenada para uma SAS de serviço.** As políticas de acesso armazenadas oferecem a opção de revogar permissões para uma SAS de serviço sem a necessidade de regenerar as chaves da conta de armazenamento. Defina a expiração nelas no futuro (ou infinita) e verifique se ela é atualizada regularmente para movê-la para o futuro.
- **Use tempos de expiração de curto prazo em uma SAS do serviço SAS ad hoc ou SAS da conta.** Dessa forma, mesmo se uma SAS for comprometida, ela será válida somente por um curto período de tempo. Essa prática será especialmente importante se você não puder fazer referência a uma política de acesso armazenada. Os tempos de expiração de curto prazo também limitam a quantidade de dados que podem ser gravados em um blob, limitando o tempo disponível para carregá-lo.
- **Fazer com que os clientes renovem automaticamente a SAS, se necessário.** Os clientes devem renovar a SAS bem antes da expiração, a fim de permitir tempo para novas tentativas se o serviço que fornece a SAS não estiver disponível. Se a sua SAS for destinada a ser usada para um pequeno número de operações imediatas e de curta duração que devem ser concluídas dentro do período de validade, isso pode ser desnecessário, pois a SAS não deve ser renovada. No entanto, se você tiver um cliente que está rotineiramente fazendo solicitações via SAS, a possibilidade de expiração entrará em cena. A principal consideração é equilibrar a necessidade de que a SAS seja de curta duração (como mencionado anteriormente) com a necessidade de garantir que o cliente esteja solicitando a renovação antecipadamente o suficiente (para evitar a interrupção devido à expiração da SAS antes da renovação bem-sucedida).
- **Tenha cuidado com a hora de início da SAS.** Se você definir a hora de início de uma SAS como **agora**, devido à distorção do relógio (diferenças na hora atual de acordo com computadores diferentes), as falhas poderão ser observadas intermitentemente pelos primeiros minutos. Em geral, defina a hora de início como no mínimo 15 minutos no passado. Ou então, não o defina, o que o tornará válido imediatamente em todos os casos. O mesmo geralmente se aplica ao tempo de expiração também – lembre-se de que você pode observar até 15 minutos de distorção de relógio em qualquer direção em qualquer solicitação. Para clientes que usam uma versão REST anterior à 2012-02-12, a duração máxima de uma SAS que não faz referência a uma política de acesso armazenada é de 1 hora e todas as políticas que especificam um prazo maior do que isso falharão.
- **Seja específico com o recurso a ser acessado.** Uma prática recomendada de segurança é fornecer a um usuário os privilégios mínimos necessários. Se um usuário precisar apenas de acesso de leitura a uma única entidade, conceda a eles acesso de leitura a essa entidade única e não o acesso de leitura/gravação/exclusão a todas as entidades. Isso também ajuda a diminuir o dano se uma SAS for comprometida porque a SAS tem menos poder nas mãos de um invasor.
- **Entenda que sua conta será cobrada por qualquer uso, incluindo por meio de uma SAS.** Se você fornecer acesso de gravação a um blob, um usuário poderá optar por carregar um blob de 200 GB. Se você tiver dado a eles acesso de leitura também, eles poderão optar por baixá-lo 10 vezes, incorrendo em 2 TB de custos de egresso para você. Novamente, forneça permissões limitadas para ajudar a mitigar as ações potenciais de usuários mal-intencionados. Use SAS de curta duração para reduzir essa ameaça (mas lembre-se da distorção do relógio na hora de término).
- **Validar os dados gravados usando uma SAS.** Quando um aplicativo cliente grava dados em sua conta de armazenamento, tenha em mente que pode haver problemas com esses dados. Se seu aplicativo exigir que os dados sejam validados ou autorizados antes que estejam prontos para uso, você deverá executar essa validação depois que os dados forem gravados e antes de serem usados pelo seu aplicativo. Essa prática também protege contra dados corrompidos ou mal-intencionados que estão sendo gravados em sua conta, seja por um usuário que adquiriu corretamente a SAS ou por um usuário que explora uma SAS vazada.
- **Saiba quando não usar uma SAS.** Às vezes, os riscos associados a uma determinada operação em relação à sua conta de armazenamento superam os benefícios do uso de uma SAS. Para essas operações, crie um serviço de camada intermediária que grave em sua conta de armazenamento depois de executar a validação, a autenticação e a auditoria da regra de negócio. Além disso, às vezes é mais simples gerenciar o acesso de outras maneiras. Por exemplo, se você quiser tornar todos os BLOBs em um contêiner publicamente legíveis, você poderá tornar o contêiner público, em vez de fornecer uma SAS a cada cliente para acesso.
- **Use Azure Monitor e logs de armazenamento do Azure para monitorar seu aplicativo.** Você pode usar o Azure Monitor e o log da análise de armazenamento para observar qualquer pico nas falhas de autorização devido a uma interrupção no serviço do provedor SAS ou à remoção inadvertida de uma política de acesso armazenada. Para obter mais informações, consulte [métricas de armazenamento do Azure em log de Azure monitor](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e [análise de armazenamento do Azure](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Introdução à SAS

Para começar a usar as assinaturas de acesso compartilhado, consulte os artigos a seguir para cada tipo de SAS.

### <a name="user-delegation-sas"></a>SAS de delegação de usuário

- [Criar uma SAS de delegação de usuário para um contêiner ou BLOB com o PowerShell (versão prévia)](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Criar uma SAS de delegação de usuário para um contêiner ou BLOB com a CLI do Azure (versão prévia)](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Criar uma SAS de delegação de usuário para um contêiner ou BLOB com .NET (versão prévia)](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>SAS do serviço

- [Criar uma SAS de serviço para um contêiner ou BLOB com .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>SAS da conta

- [Criar uma SAS de conta com .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Passos seguintes

- [Delegar acesso com uma assinatura de acesso compartilhado (API REST)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Criar uma SAS de delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Criar uma SAS de serviço (API REST)](/rest/api/storageservices/create-service-sas)
- [Criar uma SAS de conta (API REST)](/rest/api/storageservices/create-account-sas)
