---
title: Mover dados entre bases de dados de nuvem aumentadas horizontalmente
description: Explica como manipular fragmentos e mover dados por meio de um serviço hospedado internamente usando APIs de banco de dados elástico.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 00f579017ce4dd79e913565ee27698398b5feb38
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823591"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Mover dados entre bases de dados de nuvem aumentadas horizontalmente

Se você for um desenvolvedor de software como serviço e, de repente, seu aplicativo sofrerá uma enorme demanda, você precisará acomodar o crescimento. Portanto, você adiciona mais bancos de dados (fragmentos). Como redistribuir os dados para os novos bancos sem interromper a integridade dos dados? Use a **ferramenta de divisão/mesclagem** para mover dados de bancos de dado restritos para os novos bancos.  

A ferramenta de divisão/mesclagem é executada como um serviço Web do Azure. Um administrador ou desenvolvedor usa a ferramenta para mover shardlets (dados de um fragmento) entre bancos de dados diferentes (fragmentos). A ferramenta usa o gerenciamento de mapa de fragmentos para manter o banco de dados de metadados de serviço e garantir mapeamentos consistentes.

![Descrição geral][1]

## <a name="download"></a>Transferência

[Microsoft. Azure. SQLDatabase. ElasticScale. Service. SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Documentação

1. [Tutorial da ferramenta de divisão/mesclagem do banco de dados elástico](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Configuração de segurança de divisão/mesclagem](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Considerações de segurança de divisão/mesclagem](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)
5. [Migrar bases de dados existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Ferramentas de banco de dados elástico](sql-database-elastic-scale-introduction.md)
7. [Glossário de ferramentas de banco de dados elástico](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Por que usar a ferramenta de divisão/mesclagem

- **Maior**

  Os aplicativos precisam ampliar de maneira flexível além dos limites de um único banco de dados do Azure SQL DB. Use a ferramenta para mover os dados conforme necessário para os novos bancos e manter a integridade.

- **Dividir para aumentar**

  Para aumentar a capacidade geral para lidar com o crescimento explosivo, crie capacidade adicional fragmentando os dados e distribuindo-os em incrementalmente mais bancos até que as necessidades de capacidade sejam atendidas. Este é um exemplo primo do recurso de **divisão** .

- **Mesclar para redução**

  A capacidade precisa ser reduzida devido à natureza sazonal de um negócio. A ferramenta permite reduzir verticalmente até menos unidades de escala quando a empresa for lenta. O recurso de ' mesclagem ' no serviço de divisão/mesclagem de escala elástica aborda esse requisito.

- **Gerenciar pontos de HotSpot movendo shardlets**

  Com vários locatários por banco de dados, a alocação de shardlets para fragmentos pode levar a gargalos de capacidade em alguns fragmentos. Isso requer a realocação de shardlets ou a movimentação de shardlets ocupados para fragmentos novos ou menos utilizados.

## <a name="concepts--key-features"></a>Conceitos & principais recursos

- **Serviços hospedados pelo cliente**

  A mesclagem de divisão é entregue como um serviço hospedado pelo cliente. Você deve implantar e hospedar o serviço em sua assinatura do Microsoft Azure. O pacote baixado do NuGet contém um modelo de configuração para ser concluído com as informações de sua implantação específica. Consulte o [tutorial de divisão/mesclagem](sql-database-elastic-scale-configure-deploy-split-and-merge.md) para obter detalhes. Como o serviço é executado em sua assinatura do Azure, você pode controlar e configurar a maioria dos aspectos de segurança do serviço. O modelo padrão inclui as opções para configurar SSL, autenticação de cliente baseada em certificado, criptografia para credenciais armazenadas, proteção de DoS e restrições de IP. Você pode encontrar mais informações sobre os aspectos de segurança no seguinte documento [configuração de segurança de divisão e mesclagem](sql-database-elastic-scale-split-merge-security-configuration.md).

  O serviço implantado padrão é executado com um operador e uma função Web. Cada uma usa o tamanho de VM a1 nos serviços de nuvem do Azure. Embora não seja possível modificar essas configurações ao implantar o pacote, você pode alterá-las após uma implantação bem-sucedida no serviço de nuvem em execução, (por meio do portal do Azure). Observe que a função de trabalho não deve ser configurada para mais de uma única instância por motivos técnicos.

- **Integração do mapa de fragmentos**

  O serviço de divisão/mesclagem interage com o mapa de fragmentos do aplicativo. Ao usar o serviço de divisão/mesclagem para dividir ou mesclar intervalos ou para mover shardlets entre fragmentos, o serviço mantém automaticamente o mapa do fragmento atualizado. Para fazer isso, o serviço se conecta ao banco de dados do Gerenciador do mapa de fragmentos do aplicativo e mantém intervalos e mapeamentos como progresso de solicitações de divisão/mesclagem/movimentação. Isso garante que o mapa de fragmentos sempre apresente uma exibição atualizada quando as operações de divisão/mesclagem estiverem em andamento. Operações de movimentação de divisão, mesclagem e shardlet são implementadas movendo um lote de shardlets do fragmento de origem para o fragmento de destino. Durante a operação de movimentação de shardlet, o shardlets sujeito ao lote atual é marcado como offline no mapa de fragmentos e não está disponível para conexões de roteamento dependentes de dados usando a API **OpenConnectionForKey** .

- **Conexões shardlet consistentes**

  Quando a movimentação de dados é iniciada para um novo lote de shardlets, qualquer mapa de fragmentos forneceu conexões de roteamento dependentes de dados para o fragmento que armazena os shardlet são eliminados e as conexões subsequentes das APIs do mapa de fragmentos para o shardlets são bloqueadas enquanto a movimentação de dados é em andamento, a fim de evitar inconsistências. Conexões com outros shardlets no mesmo fragmento também serão encerradas, mas terão sucesso novamente imediatamente na nova tentativa. Depois que o lote for movido, os shardlets serão marcados online novamente para o fragmento de destino e os dados de origem serão removidos do fragmento de origem. O serviço passa por essas etapas para cada lote até que todos os shardlets tenham sido movidos. Isso levará a várias operações de eliminação de conexão durante o curso da operação de divisão/mesclagem/movimentação completa.  

- **Gerenciando a disponibilidade do shardlet**

  Limitar a eliminação de conexão para o lote atual de shardlets conforme discutido acima restringe o escopo de indisponibilidade a um lote de shardlets de cada vez. Isso é preferível a uma abordagem em que o fragmento completo permanecerá offline para todos os seus shardletss durante a operação de divisão ou mesclagem. O tamanho de um lote, definido como o número de shardlets distintos a serem movidos por vez, é um parâmetro de configuração. Ele pode ser definido para cada operação de divisão e mesclagem dependendo das necessidades de desempenho e disponibilidade do aplicativo. Observe que o intervalo que está sendo bloqueado no mapa de fragmentos pode ser maior do que o tamanho do lote especificado. Isso ocorre porque o serviço escolhe o tamanho do intervalo de modo que o número real de valores de chave de fragmentação nos dados aproximadamente corresponda ao tamanho do lote. Isso é importante para se lembrar em particular para chaves de fragmentação preenchidas de mais grosseira.

- **Armazenamento de metadados**

  O serviço de divisão/mesclagem usa um banco de dados para manter seu status e para manter os logs durante o processamento da solicitação. O usuário cria esse banco de dados em sua assinatura e fornece a cadeia de conexão para ele no arquivo de configuração para a implantação do serviço. Os administradores da organização do usuário também podem se conectar a esse banco de dados para examinar o andamento da solicitação e investigar informações detalhadas sobre falhas em potencial.

- **Reconhecimento de fragmentação**

  O serviço de divisão/mesclagem diferencia entre (1) tabelas fragmentadas, (2) tabelas de referência e (3) tabelas normais. A semântica de uma operação de divisão/mesclagem/movimentação depende do tipo da tabela usada e é definida da seguinte maneira:

  - **Tabelas fragmentadas**

    Operações de divisão, mesclagem e movimentação movem shardlets da origem para o fragmento de destino. Após a conclusão bem-sucedida da solicitação geral, essas shardlets não estarão mais presentes na origem. Observe que as tabelas de destino precisam existir no fragmento de destino e não devem conter dados no intervalo de destino antes do processamento da operação.

  - **Tabelas de referência**

    Para tabelas de referência, as operações de divisão, mesclagem e movimentação copiam os dados da origem para o fragmento de destino. No entanto, observe que nenhuma alteração ocorrerá no fragmento de destino para uma determinada tabela se alguma linha já estiver presente nesta tabela no destino. A tabela deve estar vazia para qualquer operação de cópia de tabela de referência para ser processada.

  - **Outras tabelas**

    Outras tabelas podem estar presentes na origem ou no destino de uma operação de divisão e mesclagem. O serviço de divisão/mesclagem ignora essas tabelas para qualquer movimentação de dados ou operações de cópia. Observe, no entanto, que eles podem interferir nessas operações em caso de restrições.

    As informações sobre as tabelas de referência vs. fragmentadas são fornecidas pelas APIs de `SchemaInfo` no mapa de fragmentos. O exemplo a seguir ilustra o uso dessas APIs em um determinado objeto do Gerenciador de mapa de fragmentos:

    ```csharp
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

    As tabelas ' Region ' e ' Nation ' são definidas como tabelas de referência e serão copiadas com operações de divisão/mesclagem/movimentação. ' Customer ' e ' Orders ', por sua vez, são definidos como tabelas fragmentadas. `C_CUSTKEY` e `O_CUSTKEY` servem como a chave de fragmentação.

- **Integridade referencial**

  O serviço de divisão/mesclagem analisa as dependências entre tabelas e usa relações de chave primária-chave estrangeira para preparar as operações para mover as tabelas de referência e shardlets. Em geral, as tabelas de referência são copiadas primeiro em ordem de dependência, então shardlets são copiadas em ordem de suas dependências dentro de cada lote. Isso é necessário para que as restrições de FK-CP no fragmento de destino sejam respeitadas à medida que os novos dados chegam.

- **Consistência do mapa de fragmentos e conclusão eventual**

  Na presença de falhas, o serviço de divisão/mesclagem retoma as operações após qualquer interrupção e pretende concluir qualquer solicitação em andamento. No entanto, pode haver situações irrecuperáveis, por exemplo, quando o fragmento de destino é perdido ou comprometido além do reparo. Nessas circunstâncias, algumas shardlets que deveriam ser movidas podem continuar a residir no fragmento de origem. O serviço garante que os mapeamentos de shardlet sejam atualizados somente depois que os dados necessários tiverem sido copiados com êxito para o destino. Shardlets são excluídos somente na origem depois que todos os seus dados tiverem sido copiados para o destino e os mapeamentos correspondentes tiverem sido atualizados com êxito. A operação de exclusão ocorre em segundo plano enquanto o intervalo já está online no fragmento de destino. O serviço de divisão/mesclagem sempre garante a exatidão dos mapeamentos armazenados no mapa de fragmentos.

## <a name="the-split-merge-user-interface"></a>A interface do usuário de divisão/mesclagem

O pacote de serviço de divisão/mesclagem inclui uma função de trabalho e uma função Web. A função Web é usada para enviar solicitações de divisão/mesclagem de maneira interativa. Os principais componentes da interface do usuário são os seguintes:

- **Tipo de operação**

  O tipo de operação é um botão de opção que controla o tipo de operação executado pelo serviço para essa solicitação. Você pode escolher entre os cenários de divisão, mesclagem e movimentação. Você também pode cancelar uma operação enviada anteriormente. Você pode usar as solicitações de divisão, mesclagem e movimentação para mapas de fragmentos de intervalo. Os mapas de fragmento de lista dão suporte apenas a operações de movimentação.

- **Mapa de fragmentos**

  A próxima seção de parâmetros de solicitação aborda informações sobre o mapa de fragmentos e o banco de dados que hospeda seu mapa de fragmentos. Em particular, você precisa fornecer o nome do servidor de banco de dados SQL do Azure e o banco de dados que hospeda o shardmap, as credenciais para se conectar ao banco de dados do mapa de fragmentos e, por fim, o nome do mapa de fragmentos. Atualmente, a operação aceita apenas um único conjunto de credenciais. Essas credenciais precisam ter permissões suficientes para executar alterações no mapa do fragmento, bem como nos dados do usuário nos fragmentos.

- **Intervalo de origem (divisão e mesclagem)**

  Uma operação de divisão e mesclagem processa um intervalo usando sua chave baixa e alta. Para especificar uma operação com um valor de alta chave não associado, marque a caixa de seleção "chave alta é máxima" e deixe o campo de chave alta vazio. Os valores de chave de intervalo que você especificar não precisam corresponder precisamente a um mapeamento e seus limites em seu mapa de fragmentos. Se você não especificar nenhum limite de intervalo em todo o serviço, inferirá o intervalo mais próximo para você automaticamente. Você pode usar o script do PowerShell getmappings. ps1 para recuperar os mapeamentos atuais em um determinado mapa de fragmentos.

- **Comportamento de origem de divisão (divisão)**

  Para operações de divisão, defina o ponto para dividir o intervalo de origem. Você faz isso fornecendo a chave de fragmentação onde deseja que a divisão ocorra. Use o botão de opção para especificar se deseja que a parte inferior do intervalo (excluindo a chave de divisão) seja movida ou se você deseja que a parte superior seja movida (incluindo a chave de divisão).

- **Shardlet de origem (mover)**

  As operações de movimentação são diferentes das operações de divisão ou mesclagem, pois não exigem um intervalo para descrever a origem. Uma fonte para movimentação é simplesmente identificada pelo valor de chave de fragmentação que você planeja mover.

- **Fragmento de destino (divisão)**

  Depois de fornecer as informações sobre a origem de sua operação de divisão, você precisa definir onde deseja que os dados sejam copiados fornecendo o nome do banco de dado e o servidor do BD SQL do Azure para o destino.

- **Intervalo de destino (mesclagem)**

  Operações de mesclagem movem shardlets para um fragmento existente. Identifique o fragmento existente fornecendo os limites de intervalo do intervalo existente com o qual você deseja mesclar.

- **Tamanho do lote**

  O tamanho do lote controla o número de shardlets que ficarão offline por vez durante a movimentação de dados. Esse é um valor inteiro em que você pode usar valores menores quando é sensível a longos períodos de inatividade para shardlets. Valores maiores aumentarão o tempo que um determinado shardlet está offline, mas pode melhorar o desempenho.

- **ID da operação (cancelar)**

  Se você tiver uma operação em andamento que não é mais necessária, você poderá cancelar a operação fornecendo sua ID de operação nesse campo. Você pode recuperar a ID da operação da tabela status da solicitação (consulte a seção 8,1) ou da saída no navegador da Web em que você enviou a solicitação.

## <a name="requirements-and-limitations"></a>Requisitos e limitações

A implementação atual do serviço de divisão/mesclagem está sujeita aos seguintes requisitos e limitações:

- Os fragmentos precisam existir e ser registrados no mapa de fragmentos antes que uma operação de divisão/mesclagem nesses fragmentos possa ser executada.
- O serviço não cria tabelas ou outros objetos de banco de dados automaticamente como parte de suas operações. Isso significa que o esquema para todas as tabelas fragmentadas e tabelas de referência precisa existir no fragmento de destino antes de qualquer operação de divisão/mesclagem/movimentação. Tabelas fragmentadas em particular são necessárias para estar vazias no intervalo em que novos shardlets devem ser adicionados por uma operação de divisão/mesclagem/movimentação. Caso contrário, a operação falhará na verificação de consistência inicial no fragmento de destino. Observe também que os dados de referência só serão copiados se a tabela de referência estiver vazia e se não houver garantias de consistência em relação a outras operações de gravação simultâneas nas tabelas de referência. Recomendamos isso: ao executar operações de divisão/mesclagem, nenhuma outra operação de gravação faz alterações nas tabelas de referência.
- O serviço depende da identidade da linha estabelecida por um índice ou chave exclusiva que inclui a chave de fragmentação para melhorar o desempenho e a confiabilidade para shardlets grandes. Isso permite que o serviço Mova dados a uma granularidade ainda melhor do que apenas o valor da chave de fragmentação. Isso ajuda a reduzir a quantidade máxima de espaço de log e os bloqueios necessários durante a operação. Considere criar um índice exclusivo ou uma chave primária, incluindo a chave de fragmentação em uma determinada tabela, se você quiser usar essa tabela com solicitações de divisão/mesclagem/movimentação. Por motivos de desempenho, a chave de fragmentação deve ser a coluna à esquerda na chave ou no índice.
- Durante o processo de processamento de solicitações, alguns dados de shardlet podem estar presentes tanto na origem quanto no fragmento de destino. Isso é necessário para proteger contra falhas durante o movimento do shardlet. A integração de divisão/mesclagem com o mapa de fragmentos garante que as conexões por meio das APIs de roteamento dependentes de dados usando o método **OpenConnectionForKey** no mapa de fragmentos não vejam nenhum estado intermediário inconsistente. No entanto, ao conectar-se aos fragmentos de origem ou de destino sem usar o método **OpenConnectionForKey** , Estados intermediários inconsistentes poderão ficar visíveis quando as solicitações de divisão/mesclagem/movimentação estiverem em andamento. Essas conexões podem mostrar resultados parciais ou duplicados, dependendo do tempo ou do fragmento subjacente à conexão. Essa limitação atualmente inclui as conexões feitas por consultas de vários fragmentos de escala elástica.
- O banco de dados de metadados para o serviço de divisão/mesclagem não deve ser compartilhado entre funções diferentes. Por exemplo, uma função do serviço de divisão/mesclagem em execução no preparo precisa apontar para um banco de dados de metadados diferente da função de produção.

## <a name="billing"></a>Faturação

O serviço de divisão/mesclagem é executado como um serviço de nuvem em sua assinatura do Microsoft Azure. Portanto, os encargos dos serviços de nuvem se aplicam à sua instância do serviço. A menos que você execute com frequência operações de divisão/mesclagem/movimentação, recomendamos que você exclua seu serviço de nuvem de divisão/mesclagem. Isso economiza custos para execução ou implantação de instâncias de serviço de nuvem. Você pode reimplantar e iniciar sua configuração prontamente executável sempre que precisar executar operações de divisão ou mesclagem.

## <a name="monitoring"></a>Monitorização

### <a name="status-tables"></a>Tabelas de status

O serviço de divisão/mesclagem fornece a tabela **RequestStatus** no banco de dados de repositório de metadados para o monitoramento de solicitações concluídas e em andamento. A tabela lista uma linha para cada solicitação de divisão/mesclagem que foi enviada para essa instância do serviço de divisão/mesclagem. Ele fornece as seguintes informações para cada solicitação:

- **Estampa**

  A data e a hora em que a solicitação foi iniciada.

- **OperationId**

  Um GUID que identifica exclusivamente a solicitação. Essa solicitação também pode ser usada para cancelar a operação enquanto ela ainda está em andamento.

- **Estado**

  O estado atual da solicitação. Para solicitações em andamento, ele também lista a fase atual em que a solicitação é.

- **CancelRequest**

  Um sinalizador que indica se a solicitação foi cancelada.

- **Andamento**

  Uma estimativa percentual da conclusão da operação. Um valor de 50 indica que a operação é aproximadamente 50% concluída.

- **Detalhes**

  Um valor XML que fornece um relatório de progresso mais detalhado. O relatório de progresso é atualizado periodicamente conforme os conjuntos de linhas são copiados da origem para o destino. No caso de falhas ou exceções, essa coluna também inclui informações mais detalhadas sobre a falha.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

O serviço de divisão/mesclagem usa Diagnóstico do Azure com base no SDK do Azure 2,5 para monitoramento e diagnóstico. Você controla a configuração de diagnóstico conforme explicado aqui: [habilitando o diagnóstico nos serviços de nuvem do Azure e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md). O pacote de download inclui duas configurações de diagnóstico: uma para a função Web e outra para a função de trabalho. Ele inclui as definições para registrar em log contadores de desempenho, logs do IIS, logs de eventos do Windows e logs de eventos do aplicativo de divisão/mesclagem.

## <a name="deploy-diagnostics"></a>Implantar diagnóstico

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Para habilitar o monitoramento e o diagnóstico usando a configuração de diagnóstico para as funções Web e de trabalho fornecidas pelo pacote NuGet, execute os seguintes comandos usando Azure PowerShell:

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

Você pode encontrar mais informações sobre como configurar e implantar configurações de diagnóstico aqui: [habilitando o diagnóstico nos serviços de nuvem do Azure e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Recuperar diagnósticos

Você pode acessar facilmente o seu diagnóstico do Gerenciador de Servidores do Visual Studio na parte do Azure da árvore de Gerenciador de Servidores. Abra uma instância do Visual Studio e, na barra de menus, clique em Exibir e Gerenciador de Servidores. Clique no ícone do Azure para se conectar à sua assinatura do Azure. Em seguida, navegue até Azure-> Storage-> tabelas `<your storage account>`->-> WADLogsTable. Para obter mais informações, consulte [Gerenciador de servidores](https://msdn.microsoft.com/library/x603htbk.aspx).

![WADLogsTable][2]

O WADLogsTable realçado na figura acima contém os eventos detalhados do log de aplicativo do serviço de divisão/mesclagem. Observe que a configuração padrão do pacote baixado é destinada a uma implantação de produção. Portanto, o intervalo no qual os logs e contadores são extraídos das instâncias de serviço é grande (5 minutos). Para teste e desenvolvimento, reduza o intervalo ajustando as configurações de diagnóstico da Web ou da função de trabalho às suas necessidades. Clique com o botão direito do mouse na função no Gerenciador de Servidores do Visual Studio (veja acima) e, em seguida, ajuste o período de transferência na caixa de diálogo para as definições de configuração de diagnóstico:

![Configuração][3]

## <a name="performance"></a>Desempenho

Em geral, é esperado um melhor desempenho das camadas de serviço mais altas e de alto desempenho no banco de dados SQL do Azure. As alocações de e/s mais altas, CPU e memória para as camadas de serviço superiores beneficiam as operações de cópia em massa e de exclusão que o serviço de divisão/mesclagem usa. Por esse motivo, aumente a camada de serviço apenas para esses bancos de dados por um período de tempo limitado e definido.

O serviço também executa consultas de validação como parte de suas operações normais. Essas consultas de validação verificam a presença inesperada de dados no intervalo de destino e garantem que qualquer operação de divisão/mesclagem/movimentação inicie a partir de um estado consistente. Essas consultas funcionam em intervalos de chaves de fragmentação definidos pelo escopo da operação e pelo tamanho do lote fornecido como parte da definição da solicitação. Essas consultas têm melhor desempenho quando um índice está presente e tem a chave de fragmentação como a coluna principal.

Além disso, uma propriedade de exclusividade com a chave de fragmentação como a coluna principal permitirá que o serviço use uma abordagem otimizada que limita o consumo de recursos em termos de espaço de log e memória. Essa propriedade de exclusividade é necessária para mover grandes tamanhos de dados (normalmente acima de 1GB).

## <a name="how-to-upgrade"></a>Como atualizar

1. Siga as etapas em [implantar um serviço de divisão/mesclagem](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Altere o arquivo de configuração do serviço de nuvem para sua implantação de divisão/mesclagem para refletir os novos parâmetros de configuração. Um novo parâmetro necessário são as informações sobre o certificado usado para criptografia. Uma maneira fácil de fazer isso é comparar o novo arquivo de modelo de configuração do download com a configuração existente. Certifique-se de adicionar as configurações para "DataEncryptionPrimaryCertificateThumbprint" e "DataEncryptionPrimary" para a função Web e de trabalho.
3. Antes de implantar a atualização no Azure, certifique-se de que todas as operações de divisão e mesclagem atualmente em execução foram concluídas. Você pode fazer isso facilmente consultando as tabelas RequestStatus e PendingWorkflows no banco de dados de metadados de divisão/mesclagem para solicitações em andamento.
4. Atualize sua implantação de serviço de nuvem existente para divisão/mesclagem em sua assinatura do Azure com o novo pacote e o arquivo de configuração de serviço atualizado.

Você não precisa provisionar um novo banco de dados de metadados para a atualização de divisão/mesclagem. A nova versão atualizará automaticamente o banco de dados de metadados existente para a nova versão.

## <a name="best-practices--troubleshooting"></a>Práticas recomendadas & solução de problemas

- Defina um locatário de teste e exerça suas operações de divisão/mesclagem/movimentação mais importantes com o locatário de teste em vários fragmentos. Verifique se todos os metadados estão definidos corretamente no mapa de fragmentos e se as operações não violam as restrições ou chaves estrangeiras.
- Mantenha o tamanho dos dados do locatário de teste acima do tamanho máximo dos dados do seu maior locatário para garantir que você não está encontrando problemas relacionados ao tamanho dos dados. Isso ajuda a avaliar um limite superior no tempo necessário para mover um único locatário.
- Verifique se o esquema permite exclusões. O serviço de divisão/mesclagem requer a capacidade de remover dados do fragmento de origem depois que os dados são copiados com êxito para o destino. Por exemplo, os **gatilhos Delete** podem impedir que o serviço exclua os dados na origem e possa causar falha nas operações.
- A chave de fragmentação deve ser a coluna principal em sua definição de índice exclusivo ou de chave primária. Isso garante o melhor desempenho para as consultas de validação de divisão ou mesclagem e para as operações reais de movimentação e exclusão de dados que sempre operam em intervalos de chaves de fragmentação.
- Colocar o serviço de divisão/mesclagem na região e data center onde residem seus bancos de dados.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
