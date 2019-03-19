---
title: Mover dados entre bases de dados de cloud aumentadas horizontalmente | Documentos da Microsoft
description: Explica como manipular as partições horizontais e mover dados através de um serviço de hospedagem interna usando APIs de bases de dados elásticas.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 2127c05d7e52b0103d91ecfac4fb5977a4815f31
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901938"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Mover dados entre bases de dados de nuvem aumentadas horizontalmente

Se é um Software como um desenvolvedor de serviço e, de repente, seu aplicativo passa pelo pedido enorme, precisa acomodar o crescimento. Então, adicionar mais bases de dados (partições horizontais). Como redistribuir os dados para as novas bases de dados sem interromper a integridade dos dados? Utilize o **ferramenta de dividir / unir** para mover dados de bases de dados restritas para as bases de dados nova.  

A ferramenta de divisão / intercalação é executado como um serviço web do Azure. Um administrador ou programador utiliza a ferramenta para mover os shardlets (dados de uma partição horizontal) entre diferentes bancos de dados (partições horizontais). A ferramenta utiliza a gestão de mapas de partições horizontais para manter a base de dados de metadados de serviço e certifique-se de mapeamentos consistentes.

![Descrição geral][1]

## <a name="download"></a>Transferência

[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Documentação

1. [Tutorial de ferramenta de dividir / unir da base de dados elásticas](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Configuração de segurança de dividir / unir](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Considerações de segurança de dividir / unir](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)
5. [Migrar bases de dados existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Ferramentas de bases de dados elásticas](sql-database-elastic-scale-introduction.md)
7. [Glossário de ferramentas de base de dados elástico](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Por que usar a ferramenta de dividir / unir

- **Flexibilidade**

  Os aplicativos precisam para a extensão de forma flexível para além dos limites de um único banco de dados de BD SQL do Azure. Utilize a ferramenta de mover os dados conforme necessário para novas bases de dados, mantendo a integridade.

- **Dividir a crescer**

  Para aumentar a capacidade geral para processar o crescimento explosivo, criar capacidade adicional, os dados de fragmentação e distribuindo-lo através de forma incremental mais bases de dados até que as necessidades de capacidade são cumpridas. Este é um excelente exemplo do **dividir** funcionalidade.

- **Intercalação para reduzir**

  Capacidade tem de reduzir devido à natureza sazonal de uma empresa. A ferramenta permite-lhe reduzir verticalmente para unidades de escala menos quando fica lenta à medida de negócios. A funcionalidade de 'merge' no serviço de divisão / intercalação de escala elástica aborda este requisito.

- **Gerir hotspots movendo shardlets**

  Com vários inquilinos por base de dados, a alocação de shardlets a partições horizontais pode causar afunilamentos de capacidade em algumas partições horizontais. Isto requer a Realocação dos shardlets ou para mover os shardlets ocupados a partições horizontais de novas ou menos utilizadas.

## <a name="concepts--key-features"></a>Conceitos e funcionalidades-chave

- **Serviços alojados no cliente**

  Dividir / unir é entregue como um serviço hospedado pelo cliente. Tem de implementar e hospedar o serviço na sua subscrição do Microsoft Azure. O pacote de que transferir a partir do NuGet contém um modelo de configuração a ser concluída com as informações para a sua implementação específica. Consulte a [tutorial de dividir / unir](sql-database-elastic-scale-configure-deploy-split-and-merge.md) para obter detalhes. Uma vez que o serviço é executado na sua subscrição do Azure, pode controlar e configurar a maioria dos aspectos de segurança do serviço. O modelo padrão inclui as opções para configurar o SSL, a autenticação de cliente baseada em certificados, a encriptação de credenciais armazenadas, DoS protege e restrições de IP. Pode encontrar mais informações sobre os aspectos de segurança no documento seguinte [configuração de segurança de dividir / unir](sql-database-elastic-scale-split-merge-security-configuration.md).

  A predefinição implementado execuções de serviço com uma função de trabalho e uma web role. Cada utiliza o tamanho da A1 VM em serviços Cloud do Azure. Embora não pode modificar estas definições ao implementar o pacote, pode alterá-los após uma implementação com êxito no serviço cloud em execução, (através do portal do Azure). Tenha em atenção que a função de trabalho não deve ser configurada durante mais de uma única instância, por motivos técnicos.

- **Integração de mapa de partições horizontais**

  O serviço de divisão / intercalação interage com o mapa de fragmentação da aplicação. Ao utilizar o serviço de divisão / intercalação para dividir ou unir intervalos ou para mover os shardlets entre partições horizontais, o serviço automaticamente mantém o mapa de partições horizontais atualizado. Para fazer isso, o serviço liga-se para a base de dados da Gestor de mapas das partições horizontais da aplicação e mantém intervalos e mapeamentos de como o progresso de pedidos de divisão/intercalação/mover. Isto garante que o mapa de partições horizontais sempre apresenta uma exibição atualizada quando as operações de dividir / unir estão sendo executadas. Operações de movimento de intercalação e shardlet dividida, são implementadas passando um lote de shardlets a partição horizontal de origem para a partição horizontal de destino. Durante a operação de movimento shardlet os shardlets sujeitos ao lote atual são marcados como offline no mapa de partições horizontais e não estão disponíveis para ligações de encaminhamento dependente de dados com o **OpenConnectionForKey** API.

- **Ligações de shardlet consistente**

  Quando é iniciado o movimento de dados para um novo lote de shardlets, qualquer mapa de partições horizontais fornecidas ligações de encaminhamento dependente de dados para a partição horizontal, armazenando o shardlet são terminadas e as ligações subsequentes do mapa de partições horizontais que APIs para os shardlets são bloqueadas enquanto é o movimento de dados em curso para evitar inconsistências. Ligações para outros shardlets na mesma partição horizontal irá também obter terminadas, mas será concluída com êxito novamente imediatamente aquando da repetição. Assim que o batch é movido, os shardlets são marcados online novamente para a partição horizontal de destino e a origem de dados é removida da partição horizontal de origem. O serviço passa por estes passos para todos os lotes até que todos os shardlets foram movidos. Isso resultará em várias operações de eliminação de ligação durante o curso de concluir a operação de divisão/intercalação/mover.  

- **Gerir a disponibilidade de shardlet**

  Limitar a ligação eliminar para o lote atual de shardlets conforme abordamos anteriormente restringe o âmbito de indisponibilidade para um lote de shardlets cada vez. Isso é preferencial através de uma abordagem em que a partição horizontal completa seria permaneça offline por todos os seus shardlets no decorrer de uma operação de intercalação ou divisão. O tamanho de um lote, definido como o número de shardlets distintos para mover cada vez, é um parâmetro de configuração. Ela pode ser definida para cada operação de divisão e intercalação consoante as necessidades de disponibilidade e desempenho do aplicativo. Tenha em atenção que o intervalo que está a ser bloqueado no mapa de partições horizontais pode ser maior do que o tamanho de lote especificado. Isto acontece porque o serviço escolhe o tamanho do intervalo, de modo a que o número real de valores de chave de fragmentação dos dados corresponde aproximadamente o tamanho do lote. Isso é importante lembrar-se em particular para as chaves de fragmentação de forma escassa preenchida.

- **Armazenamento de metadados**

  O serviço de divisão / intercalação utiliza uma base de dados para manter o estado e para manter os registos durante o processamento do pedido. O utilizador cria esta base de dados na sua subscrição e fornece a cadeia de ligação para o mesmo no ficheiro de configuração para a implementação do serviço. Os administradores da organização do utilizador também podem ligar a esta base de dados para rever o progresso do pedido e para obter informações detalhadas sobre potenciais falhas de investigar.

- **Deteção de fragmentação**

  O serviço de divisão / intercalação faz distinção entre tabelas (1) em partição horizontal, tabelas de referência (2) e tabelas (3) normais. A semântica de uma operação de divisão/intercalação/mover depende do tipo da tabela utilizada e é definida da seguinte forma:

  - **Tabelas em partição horizontal**

    Operações de movimentação, intercalação e divisão mover shardlets de origem para partições horizontais de destino. Após a conclusão bem-sucedida da solicitação geral, essas shardlets já não estão presentes na origem. Tenha em atenção que as tabelas de destino têm de existir na partição horizontal destino e não pode conter os dados de intervalo de destino antes do processamento da operação.

  - **Tabelas de referência**

    Para tabelas de referência, a divisão, operações de unir e mover copiar os dados da origem para a partição horizontal de destino. No entanto, observe que nenhuma alteração ocorrerá na partição horizontal de destino para uma determinada tabela se qualquer linha já se encontra presente nesta tabela no destino. A tabela tem de estar vazia para qualquer operação de cópia de tabela de referência processadas.

  - **Outras tabelas**

    Outras tabelas podem estar presentes na origem ou destino de uma operação de divisão e intercalação. O serviço de divisão / intercalação disregards essas tabelas para qualquer movimento de dados ou a operações de cópia. Observe, porém, que pode interferir com essas operações em caso de restrições.

    As informações de referência versus tabelas em partição horizontal são fornecidas pelo `SchemaInfo` APIs no mapa de partições horizontais. O exemplo a seguir ilustra o uso dessas APIs num objeto de Gestor de mapa de partições horizontais determinado:

    ```c#
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // Reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // Sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));
    // Publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    A região"tabelas" e 'nação' são definidos como tabelas de referência e serão copiados com operações de divisão/intercalação/mover. "cliente" e "orders" por sua vez são definidos como tabelas em partição horizontal. `C_CUSTKEY` e `O_CUSTKEY` servir como a chave de fragmentação.

- **Integridade referencial**

  O serviço de divisão / intercalação analisa as dependências entre tabelas e usa relações de chaves externas chave primária para testar as operações para mover as tabelas de referência e shardlets. Em geral, tabelas de referência são copiadas em primeiro lugar na ordem de dependência, em seguida, shardlets são copiados em ordem de suas dependências em cada lote. Isso é necessário para que as restrições de FK PK a partição horizontal de destino são honradas à medida que chegam novos dados.

- **Consistência de mapa de partições horizontais e sua Eventual conclusão**

  Na ocorrência de falhas, o serviço de divisão / intercalação retoma de operações após qualquer interrupção e tem como objetivo concluir qualquer nos pedidos de progresso. No entanto, poderão existir situações irrecuperáveis, por exemplo, quando a partição horizontal de destino forem perdida ou comprometida mais conserto. Sob essas circunstâncias, alguns shardlets que foram deve ser movida podem continuar a residir na partição horizontal de origem. O serviço garante que os mapeamentos de shardlet são actualizados apenas depois dos dados necessários foi copiados com êxito para o destino. Shardlets só são eliminados na origem depois de todos os seus dados foram copiados para o destino e os mapeamentos correspondentes foram atualizados com êxito. A operação de eliminação ocorre em segundo plano enquanto o intervalo já está online a ser a partição horizontal de destino. O serviço de divisão / intercalação sempre garante a correção dos mapeamentos armazenados no mapa de partições horizontais.

## <a name="the-split-merge-user-interface"></a>A interface do usuário de dividir / unir

O pacote de serviço de divisão / intercalação inclui uma função de trabalho e uma função da web. A função da web é utilizada para submeter pedidos de divisão / intercalação de forma interativa. Os principais componentes da interface do usuário são os seguintes:

- **Tipo de operação**

  O tipo de operação é um botão de opção que controla o tipo de operação executada pelo serviço para este pedido. Pode escolher entre a divisão, unir e mover os cenários. Também pode cancelar uma operação submetida anteriormente. Pode utilizar o split, intercalar e mover pedidos para os mapas de partições horizontais de intervalo. Apenas operações de movimentação do suporte de mapas de partições horizontais de lista.

- **Mapa de partições horizontais**

  A próxima seção dos parâmetros do pedido inclui informações sobre o mapa de partições horizontais e a base de dados que aloja o seu mapa de partições horizontais. Em particular, tem de fornecer o nome do servidor de base de dados do Azure SQL e a base de dados que aloja o shardmap, as credenciais para ligar para a base de dados do mapa de partições horizontais e, finalmente, o nome do mapa de partições horizontais. Atualmente, a operação só aceita um único conjunto de credenciais. Estas credenciais têm de ter permissões suficientes para efetuar as alterações para o mapa de partições horizontais também os dados de utilizador em partições horizontais.

- **Intervalo de origem (dividir e intercalar)**

  Uma operação de divisão e intercalação processa um intervalo utilizando a respetiva chave de baixa e alta. Para especificar uma operação com um valor de chave elevado imensos, marque a caixa de verificação "chave superior é máx." e deixar o campo de chave elevado vazio. Os valores de chave do intervalo que especificou não é necessário corresponder exatamente um mapeamento e seus limites no seu mapa de partições horizontais. Se não especificar qualquer limites de intervalo de todo o serviço irá inferir o intervalo mais próximo para automaticamente. Pode utilizar o script do GetMappings.ps1 PowerShell para recuperar os mapeamentos atuais num mapa de partições horizontais determinado.

- **Comportamento de origem de divisão (divisão)**

  Para operações de divisão, defina o ponto de dividir o intervalo de origem. Pode fazê-lo ao fornecer a chave de fragmentação onde pretende que a divisão ocorrer. Utilize o botão de opção de especificar se pretende que a parte inferior do intervalo (excluindo a chave de divisão) para mover ou se pretende que a parte superior para mover (incluindo a chave de divisão).

- **Origem Shardlet (mover)**

  Operações de movimentação são diferentes das operações de intercalação ou divisão como não necessitam de um intervalo para descrever a origem. Uma origem para movimentação é simplesmente identificada pelo valor de chave de fragmentação que pretende mover.

- **Partição de destino (divisão)**

  Após fornecer as informações na origem da sua operação de divisão, terá de definir onde pretende que os dados para ser copiado para ao fornecer o servidor de BD SQL do Azure e o nome de base de dados para o destino.

- **Intervalo de destino (intercalação)**

  Intercale shardlets de movimentação de operações para uma partição horizontal existente. Identifique a partição horizontal existente ao fornecer os limites de intervalo do intervalo existente que pretende intercalar com.

- **Tamanho do lote**

  O tamanho do lote controla o número de shardlets que ficarão offline durante o movimento de dados. Isso é um valor inteiro em que pode usar valores menores quando é sensíveis a longos períodos de tempo de inatividade para shardlets. Valores maiores aumentará o tempo que é um shardlet específico mas offline podem melhorar o desempenho.

- **ID da operação (Cancelar)**

  Se tiver uma operação em curso que já não é necessário, pode cancelar a operação ao fornecer o respetivo ID de operação neste campo. Pode obter o ID da operação da tabela de estado de pedido (consulte a secção 8.1) ou a partir da saída no navegador da web em que submeteu o pedido.

## <a name="requirements-and-limitations"></a>Requisitos e limitações

A implementação atual do serviço de divisão / intercalação está sujeita aos seguintes requisitos e limitações:

- As partições horizontais tem de existir e estar registados no mapa de partições horizontais, antes de uma operação de divisão / intercalação nestas partições horizontais pode ser executada.
- O serviço não cria tabelas ou de outros objetos de base de dados automaticamente como parte de suas operações. Isso significa que o esquema para tabelas tudo em partição horizontal e tabelas de referência tem de existir na partição horizontal destino antes de qualquer operação de divisão/intercalação/mover. Tabelas em partição horizontal em particular são necessários para estar em branco no intervalo de onde o novo shardlets estão a ser adicionado por uma operação de divisão/intercalação/mover. Caso contrário, a operação irá falhar a verificação de consistência inicial sobre a partição horizontal de destino. Observe também que dados são copiados de apenas se a referência de tabela está vazia e que não existem não existem garantias de consistência em relação a outras simultâneas operações de escrita nas tabelas de referência de referência. Fazemos esta recomendação: ao executar operações de dividir/unir, sem outras operações de escrita fazer alterações às tabelas de referência.
- O serviço depende da identidade de linha estabelecida por um índice exclusivo ou uma chave que inclui a chave de fragmentação para melhorar o desempenho e fiabilidade para shardlets grandes. Isso permite que o serviço mover dados com uma granularidade ainda mais do que apenas o valor da chave de fragmentação. Isto ajuda a reduzir a quantidade máxima de espaço de registo e bloqueios que são necessários durante a operação. Considere criar um índice exclusivo ou uma chave primária, incluindo a chave de fragmentação numa determinada tabela se quiser usar essa tabela com pedidos de divisão/intercalação/mover. Por motivos de desempenho, a chave de fragmentação deve ser a coluna à esquerda na chave ou o índice.
- Durante o processamento da solicitação, alguns dados shardlet podem ser presentes na origem e a partição horizontal de destino. Isso é necessário para proteger contra falhas durante o movimento de shardlet. A integração de divisão / intercalação com o mapa de partições horizontais garante que as ligações através do uso encaminhamento APIs do dependente de dados do **OpenConnectionForKey** método no mapa de partições horizontais não vir qualquer Estados intermediários inconsistentes. No entanto, ao ligar-se para a origem ou as partições horizontais de destino, sem utilizar o **OpenConnectionForKey** método, Estados intermediários inconsistentes poderão ser visíveis quando a pedidos de divisão/intercalação/mover estão sendo executadas. Estas ligações poderão Mostrar resultados parciais ou duplicados, consoante o período de tempo ou a partição horizontal subjacente a ligação. Esta limitação inclui atualmente as ligações efetuadas pelo dimensionamento elástico várias-Shard-consultas.
- A base de dados de metadados para o serviço de divisão / intercalação não deve ser partilhado entre funções diferentes. Por exemplo, uma função do serviço de divisão / intercalação em execução para efeitos de teste tem de apontar para uma base de dados de metadados diferentes do que a função de produção.

## <a name="billing"></a>Faturação

O serviço de divisão / intercalação é executado como um serviço cloud na sua subscrição do Microsoft Azure. Por conseguinte, os custos para serviços em nuvem se aplicam à sua instância do serviço. A menos que com frequência, executar operações de divisão/intercalação/mover, recomendamos que elimine o seu serviço cloud de dividir / unir. Que guarda os custos para executar ou implementado as instâncias de serviço cloud. Pode voltar a implementar e iniciar a configuração do seu executável prontamente sempre que precisar realizar operações de intercalação ou divisão.

## <a name="monitoring"></a>Monitorização

### <a name="status-tables"></a>Tabelas de estado

O serviço de divisão / intercalação fornece a **RequestStatus** tabela na base de dados do arquivo de metadados para a monitorização de pedidos concluídos e em curso. A tabela apresenta uma linha para cada pedido de dividir / unir que foi submetido a esta instância do serviço de divisão / intercalação. Ele fornece as seguintes informações para cada solicitação:

- **Timestamp**

  A hora e a data quando o pedido foi iniciado.

- **OperationId**

  Um GUID que identifica exclusivamente o pedido. Este pedido também pode ser usado para cancelar a operação enquanto está ainda em curso.

- **Estado**

  O estado atual do pedido. Para pedidos em curso, também apresenta a fase atual no qual é o pedido.

- **CancelRequest**

  Um sinalizador que indica se o pedido foi cancelado.

- **Progresso**

  Uma estimativa de percentagem de conclusão para a operação. Um valor de 50 indica que a operação é aproximadamente 50% concluída.

- **Detalhes**

  Um valor XML que fornece um relatório de progresso mais detalhado. O relatório de progresso é atualizado periodicamente conforme conjuntos de linhas são copiados da origem para destino. Em caso de falhas ou exceções, esta coluna também inclui informações mais detalhadas sobre a falha.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

O serviço de divisão / intercalação utiliza o diagnóstico do Azure com base no Azure SDK 2.5 para monitorização e diagnóstico. Controlar a configuração de diagnósticos conforme explicado aqui: [Ativar diagnósticos nos serviços Cloud do Azure e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md). O pacote de download inclui duas configurações de diagnóstico - um para a função da web e outro para a função de trabalho. Ele inclui as definições para registar contadores de desempenho, registos IIS, Logs de eventos do Windows e registos de eventos do aplicativo de dividir / unir.

## <a name="deploy-diagnostics"></a>Implementar o diagnóstico

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para ativar a monitorização e diagnóstico utilizando a configuração de diagnóstico para as funções web e de trabalho fornecidas pelo pacote NuGet, execute os seguintes comandos com o Azure PowerShell:

```powershell
    $storage_name = "<YourAzureStorageAccount>"
    $key = "<YourAzureStorageAccountKey"
    $storageContext = New-AzStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb"
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker"
```

Pode encontrar mais informações sobre como configurar e implementar as definições de diagnóstico aqui: [Ativar diagnósticos nos serviços Cloud do Azure e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Obter diagnóstico

Pode facilmente acessar o diagnóstico do Visual Studio no Explorador de servidores na parte da árvore Explorador de servidores do Azure. Abra uma instância do Visual Studio e, na barra de menus clique em View e o Explorador de servidores. Clique no ícone do Azure para ligar à sua subscrição do Azure. Em seguida, navegue para o Azure -> armazenamento -> `<your storage account>` -> WADLogsTable -> de tabelas. Para obter mais informações, consulte [Explorador de servidores](https://msdn.microsoft.com/library/x603htbk.aspx).

![WADLogsTable][2]

WADLogsTable realçado na figura acima contém os eventos detalhados do registo de aplicações do serviço de divisão / intercalação. Tenha em atenção que a configuração predefinida do pacote transferido é destinada uma implementação de produção. Por conseguinte, o intervalo a que os registos e contadores são extraídos das instâncias de serviço é grandes (5 minutos). Para testes e desenvolvimento, reduza o intervalo ao ajustar as definições de diagnóstico de web ou a função de trabalho às suas necessidades. Com o botão direito na função no Visual Studio Server Explorer (consulte acima) e, em seguida, ajuste o período de transferência de mensagens em fila na caixa de diálogo de configuração do diagnóstico:

![Configuração][3]

## <a name="performance"></a>Desempenho

Em geral, um melhor desempenho é de esperar de maior, mais alto desempenho escalões de serviço na base de dados do Azure SQL. Alocações de e/s, CPU e memória superior para os escalões de serviço superior beneficiam a cópia em massa e eliminar operações de que o serviço de divisão / intercalação utiliza. Por esse motivo, aumente o escalão de serviço apenas para essas bases de dados durante um período definido, limitado de tempo.

O serviço também executa consultas de validação como parte das suas operações normais. Estas consultas de validação verificar presença inesperada de dados. o intervalo de destino e certifique-se de que qualquer operação de divisão/intercalação/mover começa a partir de um estado consistente. Estas consultas em todos os trabalham em intervalos de chave de fragmentação definidos pelo escopo da operação e o tamanho do lote fornecido como parte da definição do pedido. Estas consultas têm um melhor desempenho quando um índice é que tem a chave de fragmentação, como a coluna à esquerda.

Além disso, uma propriedade de exclusividade com a chave de fragmentação, como a coluna à esquerda permitirá que o serviço para utilizar uma abordagem otimizada, que limita o consumo de recursos em termos de memória e espaço de registo. Esta propriedade de exclusividade é necessário para mover grandes tamanhos de dados (normalmente, acima de 1GB).

## <a name="how-to-upgrade"></a>Como atualizar

1. Siga os passos em [implementar um serviço de divisão / intercalação](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Altere o seu ficheiro de configuração do serviço de nuvem para a sua implementação de dividir / unir refletir os novos parâmetros de configuração. Um novo parâmetro necessário é as informações sobre o certificado utilizado para encriptação. Uma forma fácil de fazer isso é comparar o novo ficheiro de modelo de configuração do download em relação a sua configuração existente. Certifique-se de que adicionar as definições para "DataEncryptionPrimaryCertificateThumbprint" e "DataEncryptionPrimary" para a web e a função de trabalho.
3. Antes de implementar a atualização para o Azure, certifique-se de que tem concluído a todas as operações de divisão / intercalação em execução. Pode facilmente fazer isso, consultando os RequestStatus e PendingWorkflows tabelas na base de dados de metadados de dividir / unir para pedidos em curso.
4. Atualize a sua implementação do serviço cloud existente para dividir / unir na sua subscrição do Azure com o novo pacote e o ficheiro de configuração de serviço atualizado.

Não é necessário aprovisionar uma nova base de dados de metadados para dividir / unir a atualização. A nova versão irão atualizar automaticamente a sua base de dados de metadados existentes para a nova versão.

## <a name="best-practices--troubleshooting"></a>Melhores práticas e resolução de problemas

- Definir um inquilino de teste e executar as operações de divisão/intercalação/mover mais importantes com o inquilino de teste através de vários shards. Certifique-se de que a todos os metadados está definido corretamente no seu mapa de partições horizontais e que as operações violam as restrições ou as chaves estrangeiras.
- Manter o inquilino de teste problemas relacionados com o tamanho de dados acima o tamanho máximo de dados do seu inquilino do maior para garantir que não tiver com tamanho de dados. Isto ajuda-o a avaliar um limite superior no tempo que demora a mover um único inquilino.
- Certifique-se de que o seu esquema permite eliminações. O serviço de divisão / intercalação requer a capacidade de remover os dados de partição horizontal origem depois dos dados foram copiados com êxito para o destino. Por exemplo, **eliminar acionadores** pode impedir que o serviço de eliminar os dados na origem e pode fazer com que operações falhem.
- A chave de fragmentação deve ser a coluna à esquerda na sua chave primária ou a definição de índice exclusivo. Garante que o melhor desempenho para as consultas de validação de intercalação ou divisão e para as operações de movimento e a eliminação de dados reais que sempre operam em intervalos de chave de fragmentação.
- Colocar o seu serviço de divisão / intercalação no Centro de dados e a região onde residem as bases de dados.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
