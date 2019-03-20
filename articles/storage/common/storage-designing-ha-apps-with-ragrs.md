---
title: Estruturar Aaplications elevada disponibilidade com armazenamento georredundante com acesso de leitura (RA-GRS) | Documentos da Microsoft
description: Como utilizar o armazenamento do Azure. o RA-GRS para arquitetar uma aplicação de elevada disponibilidade flexível o bastante para lidar com falhas.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 01/17/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: be1c46c5bc2c8edcfeca81c82095687c4ddfd894
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225829"
---
# <a name="designing-highly-available-applications-using-ra-grs"></a>Conceber aplicações de elevada disponibilidade com RA-GRS

Uma funcionalidade comum de infraestruturas na cloud, como o armazenamento do Azure é que eles fornecem uma plataforma de elevada disponibilidade para alojar aplicações. Os desenvolvedores de aplicativos baseados na nuvem tem de considerar cuidadosamente como tirar partido desta plataforma para fornecer aplicações de elevada disponibilidade aos respetivos utilizadores. Este artigo se concentra em como os programadores podem utilizar armazenamento Georredundante com acesso de leitura (RA-GRS) para garantir que seus aplicativos de armazenamento do Azure são de elevada disponibilidade.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Este artigo se concentra em GRS e RA-GRS. Com a GRS, três cópias dos seus dados são mantidas na região primária que selecionou ao configurar a conta de armazenamento. Três cópias adicionais são mantidas de forma assíncrona numa região secundária, especificado pelo Azure. RA-GRS oferece armazenamento georredundante com acesso de leitura para a cópia secundária.

Para obter informações sobre os quais regiões primárias são emparelhados com quais são as regiões secundárias, consulte [Business continuidade e recuperação após desastre (BCDR): Regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Existem trechos de código incluídos neste artigo e uma ligação para um exemplo completo no final que pode transferir e executar.

> [!NOTE]
> O armazenamento do Azure suporta agora o armazenamento com redundância de zona (ZRS) para a criação de aplicações de elevada disponibilidade. O ZRS oferece uma solução simple para as necessidades de redundância de muitos aplicativos. O ZRS fornece proteção contra falhas de hardware ou desastres catastróficas que afetam um único datacenter. Para obter mais informações, consulte [armazenamentoredundância de zona (ZRS): Aplicações de armazenamento do Azure de elevada disponibilidade](storage-redundancy-zrs.md).

## <a name="key-features-of-ra-grs"></a>Principais recursos do RA-GRS

Tenha em mente esses pontos-chave ao conceber a sua aplicação para o RA-GRS:

* O armazenamento do Azure mantém uma cópia só de leitura dos dados que armazenamos na sua região principal numa região secundária. Conforme observado anteriormente, o serviço de armazenamento determina a localização da região secundária.

* A cópia só de leitura estiver [eventualmente consistente](https://en.wikipedia.org/wiki/Eventual_consistency) com os dados na região primária.

* Para blobs, tabelas e filas, pode consultar a região secundária para uma *hora da última sincronização* valor que indica quando ocorreu a última replicação dos principais para a região secundária. (Isto não é suportado para ficheiros do Azure, que não têm redundância de RA-GRS neste momento.)

* Pode utilizar a biblioteca de cliente de armazenamento para interagir com os dados na região primária ou secundária. Também pode redirecionar pedidos automaticamente para a região secundária de leitura, se uma solicitação de leitura para a região primária exceder o tempo limite.

* Se a região primária ficar indisponível, pode iniciar uma ativação pós-falha de conta. Quando efetuar a ativação pós-falha para a região secundária, as entradas de DNS que aponte para a região primária são alteradas para apontar para a região secundária. Depois de concluída a ativação pós-falha, o acesso de escrita é restaurado para contas GRS e RA-GRS. Para obter mais informações, consulte [desastre recuperação e o armazenamento de conta ativação pós-falha (pré-visualização) no armazenamento do Azure](storage-disaster-recovery-guidance.md).

## <a name="application-design-considerations-when-using-ra-grs"></a>Considerações de design de aplicativo ao utilizar o RA-GRS

O objetivo deste artigo é mostrar-lhe como criar uma aplicação que continuará a funcionar (embora numa capacidade limitada), mesmo em caso de um desastre grave no Datacenter primário. É possível projetar seu aplicativo para lidar com problemas transitórios ou de execução longa ao ler a partir da região secundária quando existe um problema que interfere com a leitura da região primária. Quando a região primária estiver novamente disponível, seu aplicativo pode retornar a leitura da região primária.

### <a name="using-eventually-consistent-data"></a>Utilizar os dados eventualmente consistentes

A solução proposta parte do princípio de que é aceitável para retornar dados potencialmente obsoletos ao aplicativo de chamada. Como dados na região secundária são eventualmente consistentes, é possível que a região primária poderão ficar inacessível antes de concluir uma atualização para a região secundária a replicar.

Por exemplo, suponha que seu cliente submete uma atualização com êxito, mas a região primária falha antes da atualização é propagada para a região secundária. Quando o cliente pede ler os dados novamente, ele recebe os dados obsoletos da região secundária, em vez dos dados atualizados. Ao conceber a sua aplicação, tem de decidir se isso é aceitável e, se assim for, como será a mensagem do cliente. 

Mais tarde neste artigo, vamos mostrar como verificar a hora da última sincronização de dados secundários para verificar se o elemento secundário for atualizado.

### <a name="handling-services-separately-or-all-together"></a>Lidar com os serviços separadamente ou todas as ferramentas

Embora seja pouco provável, é possível que um serviço fiquem indisponíveis enquanto os outros serviços ainda são totalmente funcionais. Pode manipular as repetições e o modo só de leitura para cada serviço separadamente (blobs, filas, tabelas), ou pode manipular as repetições genericamente para todos os serviços de armazenamento em conjunto.

Por exemplo, se usar filas e blobs na sua aplicação, pode decidir colocar no código separado para manipular erros de repetição para cada um deles. Em seguida, se obtém uma repetição do serviço blob, mas o serviço de fila ainda está a funcionar, apenas a parte do seu aplicativo que lida com blobs será afetada. Se optar por genericamente a lidar com todas as tentativas de serviço de armazenamento e uma chamada para o serviço blob devolve um erro recuperável, em seguida, pedidos para o serviço de blob e o serviço de fila serão afetados.

Por fim, isso depende da complexidade do seu aplicativo. Pode decidir não processar as falhas pelo serviço, mas em vez disso, para redirecionar pedidos para todos os serviços de armazenamento para a região secundária de leitura e executar o aplicativo no modo só de leitura quando detetar um problema com qualquer serviço de armazenamento na região primária.

### <a name="other-considerations"></a>Outras considerações

Estas são as outras considerações, discutiremos no restante deste artigo.

*   A processar as repetições de pedidos de leitura com o padrão de disjuntor automático

*   Dados eventualmente consistente e a hora da última sincronização

*   Testes

## <a name="running-your-application-in-read-only-mode"></a>Execução da sua aplicação no modo só de leitura

Para utilizar o armazenamento RA-GRS, tem de ser capaz de lidar com ambos os pedidos falhados de leitura e de atualização pedidos falhados (com a atualização em vez disso, que significa que inserções, atualizações e eliminações). Se os dados primários falha do Datacenter, leia pedidos podem ser redirecionados para o Centro de dados secundária. No entanto, pedidos de atualização não podem ser redirecionados para o secundário porque o elemento secundário for só de leitura. Por esse motivo, terá de criar seu aplicativo para executar no modo só de leitura.

Por exemplo, pode definir um sinalizador que é verificado antes de quaisquer pedidos de atualização são submetidos ao armazenamento do Azure. Quando uma das solicitações de atualização vinda, pode ignorá-la e retornar uma resposta adequada para o cliente. Pode até mesmo desabilitar determinados recursos totalmente até que o problema é resolvido e notificar os utilizadores que essas funcionalidades estão temporariamente indisponíveis.

Se optar por tratar os erros para cada serviço separadamente, também terá de lidar com a capacidade de executar a sua aplicação no modo só de leitura pelo serviço. Por exemplo, pode ter os sinalizadores de só de leitura para cada serviço que pode ser ativado e desativado. Em seguida, pode manipular o sinalizador nos lugares apropriados no seu código.

A capacidade de executar a sua aplicação no modo só de leitura tem outra vantagem de lado – dá-lhe a capacidade para garantir que a funcionalidade limitada durante uma atualização principal da aplicação. Pode acionar a sua aplicação para ser executado no modo só de leitura e aponte para o Centro de dados secundária, garantir que ninguém está acessando os dados na região primária, quer garantir atualizações.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Atualizações de manipulação quando em execução no modo só de leitura

Existem várias formas para processar pedidos de atualização quando em execução no modo só de leitura. Não Abordaremos isso forma abrangente, mas em geral, existem alguns padrões que considerar.

1.  Pode responder ao seu utilizador e peça-lhes que não atualmente a aceitar as atualizações. Por exemplo, um sistema de gerenciamento de contatos poderia permitir aos clientes aceder a informações de contacto, mas não fazer atualizações.

2.  Pode colocar as atualizações noutra região. Neste caso, seria escrever seus pedidos de atualização pendente a uma fila numa região diferente e, em seguida, tem uma forma para processar essas solicitações, depois do Datacenter primário fica online novamente. Neste cenário, deve permitir que o cliente sabe que a atualização solicitada é colocada na fila para processamento posterior.

3.  Pode escrever as suas atualizações para uma conta de armazenamento noutra região. Em seguida, quando o Datacenter primário fica online novamente, pode ter uma forma de unir essas atualizações para os dados primários, consoante a estrutura dos dados. Por exemplo, se estiver a criar arquivos separados com um carimbo de data/hora no nome, pode copiar esses arquivos de volta para a região primária. Isto funciona para algumas cargas de trabalho como dados de registo e iOT.

## <a name="handling-retries"></a>A processar as repetições

Como sabe quais erros estão repetição? Tal é determinado através da biblioteca de clientes de armazenamento. Por exemplo, um erro 404 (recurso não encontrado) não é repetição porque não é provável que resultará na êxito repeti-lo. Por outro lado, um erro de 500 é repetição porque é um erro no servidor, e apenas pode ser um problema transitório. Para obter mais detalhes, consulte a [abrir o código-fonte para a classe ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) na biblioteca de cliente de armazenamento do .NET. (Procure o método ShouldRetry).

### <a name="read-requests"></a>Pedidos de leitura

Os pedidos de leitura podem ser redirecionados para o armazenamento secundário, se houver um problema com o armazenamento primário. Como mencionado acima na [usando, eventualmente, os dados consistentes](#using-eventually-consistent-data), tem de ser aceitável para a sua aplicação potencialmente ler dados obsoletos. Se estiver a utilizar a biblioteca de cliente de armazenamento para aceder aos dados de RA-GRS, pode especificar o comportamento de repetição de uma solicitação de leitura ao definir um valor para o **LocationMode** propriedade para um dos seguintes:

*   **PrimaryOnly** (predefinição)

*   **PrimaryThenSecondary**

*   **SecondaryOnly**

*   **SecondaryThenPrimary**

Ao definir o **LocationMode** ao **PrimaryThenSecondary**, se a solicitação de leitura inicial para a falha de ponto final primário com um erro de repetição, o cliente torna a outro pedido de leitura para o ponto final secundário. Se o erro é um tempo limite do servidor, o cliente terá de aguardar que o tempo limite expirar antes de receber um erro de repetição do serviço.

Basicamente, existem dois cenários a considerar quando decidir como responder a um erro de repetição:

*   Este é um problema isolado e os pedidos subsequentes para o ponto final primário não retornará um erro de repetição. Um exemplo de onde isto pode acontecer é quando existe um erro de rede transitórios.

    Neste cenário, não há nenhuma penalidade de desempenho significativo que **LocationMode** definida como **PrimaryThenSecondary** como isto só acontece com pouca frequência.

*   Este é um problema com, pelo menos, um dos serviços de armazenamento na região primária e todas as solicitações subseqüentes para esse serviço na região primária são provável que a devolver erros de repetição para um período de tempo. Um exemplo disso é se a região primária não está totalmente acessível.

    Neste cenário, há uma penalidade de desempenho porque todas as solicitações de leitura irão tente primeiro o ponto final primário, aguarde o tempo limite expirar, em seguida, mude para o ponto final secundário.

Nestes cenários, deve identificar que há um problema em curso com o ponto final primário e enviar todos os pedidos diretamente para o ponto final secundário de leitura, definindo a **LocationMode** propriedade **SecondaryOnly** . Neste momento, também deve alterar o aplicativo seja executado no modo só de leitura. Essa abordagem é conhecida como o [padrão de disjuntor automático](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Pedidos de atualização

O padrão de disjuntor automático também pode ser aplicado para pedidos de atualização. No entanto, pedidos de atualização não podem ser redirecionados para o armazenamento secundário, o que é só de leitura. Essas solicitações, deve deixar a **LocationMode** definida como **PrimaryOnly** (predefinição). Para tratar esses erros, pode aplicar uma métrica a essas solicitações – como 10 falhas numa linha – e quando o limiar for cumprido, mudar o aplicativo em modo só de leitura. Pode utilizar os mesmos métodos para retornar ao atualizar o modo como as descritas abaixo na próxima seção sobre o padrão de disjuntor automático.

## <a name="circuit-breaker-pattern"></a>Padrão de Disjuntor Automático

Usando o padrão de disjuntor automático em seu aplicativo pode impedir que ele repetir uma operação que é provável que falham repetidamente. Ele permite que o aplicativo continua a ser executado, em vez de reduzir o tempo, enquanto a operação será repetida exponencialmente. Também Deteta quando a falha foi corrigido, nesse momento, o aplicativo pode tentar a operação de novo.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Como implementar o padrão de disjuntor automático

Para identificar-se de que existe um problema em curso com um ponto final primário, pode monitorizar a frequência com que o cliente encontra erros de repetição. Uma vez que cada caso é diferente, terá de decidir qual o limiar que pretende utilizar para a decisão de mudar para o ponto final secundário e executar o aplicativo no modo só de leitura. Por exemplo, poderia optar por executar o comutador se existirem 10 falhas numa linha com nenhuma êxitos. Outro exemplo consiste em alterná se falhar de 90% das solicitações num período de 2 minutos.

Para o primeiro cenário, pode simplesmente manter uma contagem das falhas e se existe um êxito antes de atingir o máximo, defina a contagem de volta a zero. Para o segundo cenário, uma forma de implementá-lo é utilizar o objeto de MemoryCache (no .NET). Para cada solicitação, adicione um CacheItem para a cache, defina o valor para o sucesso (1) ou falhar (0) e defina a hora de expiração para 2 minutos na agora (ou o que quer que esteja a restrição de tempo). Quando for atingida a hora de expiração de uma entrada, a entrada é removida automaticamente. Isso lhe fornecerá uma janela de 2 minutos sem interrupção. Sempre que fizer um pedido para o serviço de armazenamento, primeiro usar uma consulta Linq entre o objeto de MemoryCache para calcular a percentagem de êxito somando os valores e dividir pela contagem. Quando a percentagem de êxito cai abaixo de algum limite (por exemplo, 10%), defina o **LocationMode** pedidos de propriedade de leitura para **SecondaryOnly** e alternar o aplicativo em modo só de leitura antes de continuar.

O limiar de erros utilizado para determinar quando deve fazer a transição pode variar de serviço a serviço na sua aplicação, pelo que deve considerar tornando-os parâmetros configuráveis. Isso também é onde decidir manipular erros de repetição de cada serviço em separado ou como uma única, como discutido anteriormente.

Outra consideração é como lidar com várias instâncias de um aplicativo e o que fazer quando detectar erros de repetição em cada instância. Por exemplo, pode ter 20 VMs em execução com a mesma aplicação carregada. Deve lidar com cada instância separadamente? Se uma instância começa a ter problemas, quer limitar a resposta para apenas que uma instância ou you want to tentamos respondem todas as instâncias da mesma forma, quando uma instância tem um problema? Lidar com as instâncias em separado é muito mais simples do que tentar coordenar a resposta em eles, mas como fazer isso depende de arquitetura de seu aplicativo.

### <a name="options-for-monitoring-the-error-frequency"></a>Opções de monitorização a frequência de erro

Tem três opções principais para a monitorização a frequência de repetições na região primária para determinar quando mudar para a região secundária e altere o aplicativo seja executado no modo só de leitura.

*   Adicionar um manipulador para o [ **repetir** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx) eventos no [ **OperationContext** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.aspx) solicita que passar para o armazenamento de objeto – este é o método apresentado neste artigo e utilizado no exemplo que acompanha este artigo. Esses eventos são disparados sempre que o cliente tenta repetir um pedido, permitindo-lhe controlar a frequência com que o cliente encontra erros não permanente de um ponto final primário.

    ```csharp 
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

*   Na [ **Evaluate** ](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx) método uma política de repetição personalizada, pode executar código personalizado sempre que uma nova tentativa ocorre. Para além de gravação quando uma nova tentativa ocorre, isso também lhe dá a oportunidade de modificar seu comportamento de repetição.

    ```csharp 
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

*   A terceira abordagem é implementar um componente de monitorização personalizado em seu aplicativo que continuamente ping para o ponto final de armazenamento primário com dummy pedidos (como ler um blob de pequenos) de leitura para determinar o respetivo estado de funcionamento. Isso poderia demorar até alguns recursos, mas não uma quantidade significativa. Quando um problema é descoberto que atinge o limite, poderia, em seguida, executar o comutador **SecondaryOnly** e o modo só de leitura.

Em algum momento, desejará mudar para utilizar o ponto final primário e permitindo que as atualizações. Se utilizar um dos dois primeiros métodos listados acima, pode simplesmente voltar a mudar para o ponto final primário e ative o modo de atualização após um período selecionado de forma arbitrária de tempo ou o número de operações foi realizado. Em seguida, pode deixá-los a percorrer a lógica de repetição novamente. Se o problema foi resolvido, irá continuar a utilizar o ponto final primário e permitir que as atualizações. Se ainda há um problema, mais uma vez será mude novamente para o ponto final secundário e o modo só de leitura não passar os critérios que definiu.

Para o cenário de terceiro, ao enviar pings para o ponto final de armazenamento primário torna-se com êxito novamente, pode acionar a opção para **PrimaryOnly** e continuar permitindo atualizações.

## <a name="handling-eventually-consistent-data"></a>Manipular dados eventualmente consistentes

O RA-GRS funciona ao replicar as transações da região primária para a secundária. Este processo de replicação garante que os dados na região secundária são *eventualmente consistente*. Isto significa que todas as transações na região primária, eventualmente, são apresentados na região secundária, mas que pode haver um atraso antes de serem apresentados e que não há nenhuma garantia chegam, as transações na região secundária na mesma ordem em que eles original de aplicação na região primária. Se suas transações chegarem na região secundária fora de ordem, *poderá* considere seus dados na região secundária para estar num estado inconsistente, até que o serviço captura a cópia de segurança.

A tabela seguinte mostra um exemplo de como o que pode acontecer quando atualizar os detalhes de um empregado para tornar ela um membro do *administradores* função. Para efeitos deste exemplo, isto requer que Atualize o **funcionário** entidade e atualizar uma **função de administrador** entidade com uma contagem do número total de administradores. Observe como as atualizações são aplicadas fora de ordem na região secundária.

| **tempo** | **Transação**                                            | **Replicação**                       | **Hora da última sincronização** | **Resultado** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transação r: <br> Inserir o funcionário <br> entidade no principal |                                   |                    | Transação A inserido para o primário,<br> não replicadas ainda. |
| T1       |                                                            | Transação A <br> replicar para<br> secundária | T1 | R de transações replicada para o secundário. <br>Hora da última sincronização atualizada.    |
| T2       | Transação b:<br>Atualizar<br> Entidade Employee<br> no principal  |                                | T1                 | Transação B escrito para o servidor primário,<br> não replicadas ainda.  |
| T3       | Transação c:<br> Atualizar <br>administrador<br>entidade de função no<br>principal |                    | T1                 | Transação C escrito para o servidor primário,<br> não replicadas ainda.  |
| *T4*     |                                                       | Transação C <br>replicar para<br> secundária | T1         | Transação C replicado para o secundário.<br>LastSyncTime não atualizada porque <br>transação B não tem sido replicada ainda.|
| *T5*     | Entidades de leitura <br>partir do secundário                           |                                  | T1                 | Obtém o valor obsoleto de funcionário <br> entidade porque ainda não a transação B <br> replicadas ainda. Obtém o novo valor<br> entidade de função de administrador porque tem de C<br> replicadas. Hora da última sincronização ainda não<br> foi atualizado porque a transação B<br> ainda não replicadas. Pode dizer o<br>entidade de função de administrador é inconsistente <br>uma vez que a entidade data/hora é após <br>a hora da última sincronização. |
| *T6*     |                                                      | Transação B<br> replicar para<br> secundária | T6                 | *T6* – tem todas as transações por meio da C <br>foram replicados, a hora da última sincronização<br> é atualizado. |

Neste exemplo, suponha que o cliente muda para leitura da região secundária em T5. Pode lido com êxito a **função de administrador** entidade neste momento, mas a entidade contém um valor para a contagem de administradores que não é consistente com o número de **funcionário** entidades que são marcado como administradores na região secundária neste momento. O cliente simplesmente foi possível apresentar este valor, com o risco que chegou a informações inconsistentes. Em alternativa, o cliente poderia tentar determinar que o **função de administrador** está num estado potencialmente inconsistente porque as atualizações ocorreram fora de ordem e, em seguida, informam o utilizador desse fato.

Reconhecer que tem dados potencialmente inconsistentes, o cliente pode utilizar o valor do *hora da última sincronização* que pode obter a qualquer momento, consultando um serviço de armazenamento. Isso informa ao tempo quando os dados na região secundária eram último consistente e quando o serviço fosse aplicada a todas as transações antes desse ponto no tempo. No exemplo mostrado acima, depois do serviço insere a **funcionário** entidade na região secundária, a hora da última sincronização estiver definida como *T1*. Ele continua a é *T1* até as atualizações de serviço a **funcionário** entidade na região secundária quando é definido como *T6*. Se o cliente obtém a hora da última sincronização quando ele lê a entidade na *T5*, ele pode compará-lo com o carimbo de hora na entidade. Se o carimbo de hora na entidade é posterior à hora da última sincronização, em seguida, a entidade está num estado inconsistente potencialmente e pode levar a que quer que esteja a ação adequada para a sua aplicação. Utilizar este campo exige que saiba quando a última atualização para o primário foi concluída.

## <a name="testing"></a>Testes

É importante testar seu aplicativo se comporta conforme o esperado quando encontrar erros de repetição. Por exemplo, precisa testar que o aplicativo alterna para o secundário e no modo só de leitura quando Deteta um problema e muda uma quando a região primária fique disponível novamente. Para tal, precisa de uma forma para simular erros de repetição e controle a frequência com que eles ocorrem.

Pode usar [Fiddler](https://www.telerik.com/fiddler) para interceptar e modificar as respostas HTTP num script. Este script pode identificar as respostas do seu ponto final primário e alterar o código de estado HTTP de forma que a biblioteca de cliente de armazenamento reconhece como um erro de repetição. Este fragmento de código mostra um exemplo simples de um script de Fiddler que intercepta as respostas a pedidos em relação a leitura a **employeedata** tabela rolesettings para retornar um status 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Pode estender esse exemplo para interceptar um maior número de pedidos e alterar apenas a **responseCode** em alguns para simular melhor um cenário do mundo real. Para obter mais informações sobre como personalizar os scripts de Fiddler, consulte [modificar uma solicitação ou resposta](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) na documentação do Fiddler.

Se tiver efetuado os limiares para mudar a sua aplicação para o modo só de leitura configurável, será mais fácil testar o comportamento com volumes de transações de não produção.

## <a name="next-steps"></a>Próximos Passos

* Para obter mais informações sobre o acesso de leitura redundância geográfica, incluindo outro exemplo de como o LastSyncTime estiver definido, veja [opções de redundância de armazenamento do Windows Azure e o armazenamento Georredundante com acesso de leitura](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Para obter um exemplo completo que mostra como fazer a transição entre os pontos de extremidade principais e secundários, consulte [amostras do Azure – usando o padrão de disjuntor automático com o armazenamento RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
