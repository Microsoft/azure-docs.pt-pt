---
title: Mover dados entre bases de dados de nuvem aumentadas horizontalmente
description: Explica como manipular fragmentos e mover dados através de um serviço auto-hospedado usando APIs de base de dados elásticas.
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
ms.openlocfilehash: 8b0db4a1e55b53165e40e176834d66b62926e24b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74421553"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Mover dados entre bases de dados de nuvem aumentadas horizontalmente

Se é um Software como desenvolvedor de serviços e, de repente, a sua aplicação sofre uma procura tremenda, precisa de acomodar o crescimento. Por isso, adicione mais bases de dados (fragmentos). Como redistribuir os dados para as novas bases de dados sem perturbar a integridade dos dados? Utilize a **ferramenta de fusão de divisão** para mover dados de bases de dados restritas para as novas bases de dados.  

A ferramenta de fusão é executado como um serviço web Azure. Um administrador ou desenvolvedor utiliza a ferramenta para mover fragmentos (dados de um fragmento) entre diferentes bases de dados (fragmentos). A ferramenta utiliza a gestão de mapas de fragmentos para manter a base de dados de metadados de serviço, e garantir mapeamentos consistentes.

![Descrição geral][1]

## <a name="download"></a>Transferência

[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Documentação

1. [Tutorial de ferramentas split-merge de base elástica](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Configuração de segurança Split-Merge](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Considerações de segurança de fusão dividida](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)
5. [Migrar bases de dados existentes para aumentar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Ferramentas de base de dados elásticas](sql-database-elastic-scale-introduction.md)
7. [Glossário de ferramentas de base de dados elástica](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Por que usar a ferramenta de fusão split

- **Flexibilidade**

  As aplicações precisam de se esticar de forma flexível para além dos limites de uma única base de dados Azure SQL DB. Utilize a ferramenta para mover dados conforme necessário para novas bases de dados, mantendo a integridade.

- **Dividido para crescer**

  Para aumentar a capacidade global de lidar com o crescimento explosivo, criar capacidade adicional, sharding os dados e distribuindo-os gradualmente por mais bases de dados até que as necessidades de capacidade sejam satisfeitas. Este é um excelente exemplo da característica **dividida.**

- **Fundir-se para encolher**

  A capacidade precisa de diminuir devido à natureza sazonal de um negócio. A ferramenta permite-lhe reduzir para unidades de menor escala quando o negócio abranda. A função 'fusão' no Serviço de fusão de escala elástica cobre este requisito.

- **Gerir hotspots movendo-se fragmentos**

  Com vários inquilinos por base de dados, a atribuição de fragmentos a fragmentos pode levar a estrangulamentos de capacidade em alguns fragmentos. Isto requer a recolocação de fragmentos ou a deslocação de fragmentos ocupados para fragmentos novos ou menos utilizados.

## <a name="concepts--key-features"></a>Conceitos & principais características

- **Serviços hospedados pelo cliente**

  A fusão é entregue como um serviço hospedado pelo cliente. Tem de implementar e acolher o serviço na subscrição do Microsoft Azure. O pacote que descarrega do NuGet contém um modelo de configuração para completar com as informações para a sua implementação específica. Consulte o [tutorial de fusão](sql-database-elastic-scale-configure-deploy-split-and-merge.md) para mais detalhes. Uma vez que o serviço é executado na sua subscrição Azure, pode controlar e configurar a maioria dos aspetos de segurança do serviço. O modelo padrão inclui as opções para configurar ssl, autenticação de cliente baseada em certificados, encriptação para credenciais armazenadas, guarda do DoS e restrições IP. Pode encontrar mais informações sobre os aspetos de segurança na [configuração de segurança](sql-database-elastic-scale-split-merge-security-configuration.md)de fusão de documentos a seguir .

  O serviço implantado por padrão funciona com um trabalhador e uma função web. Cada um usa o tamanho A1 VM em Serviços Azure Cloud. Embora não possa modificar estas definições ao implementar o pacote, poderá alterá-las após uma implementação bem sucedida no serviço de nuvem em execução (através do portal Azure). Note que a função do trabalhador não deve ser configurada por razões técnicas.

- **Integração do mapa do fragmento**

  O serviço de fusão interpartida interage com o mapa de fragmentos da aplicação. Ao utilizar o serviço de fusão split para dividir ou fundir gamas ou mover fragmentos entre fragmentos, o serviço mantém automaticamente o mapa de fragmentos atualizado. Para tal, o serviço liga-se à base de dados do gestor de mapas de fragmentos da aplicação e mantém gamas e mapeamentos como divisão/fusão/movimento solicita progresso. Isto garante que o mapa do fragmento apresenta sempre uma visão atualizada quando as operações de fusão estão a decorrer. As operações de movimento de divisão, fusão e fragmentos são implementadas movendo um lote de fragmentos do fragmento de origem para o fragmento-alvo. Durante a operação de movimento de fragmentos, os fragmentos sujeitos ao lote atual estão marcados como offline no mapa do fragmento e não estão disponíveis para ligações de encaminhamento dependentes de dados utilizando a **API OpenConnectionForKey.**

- **Conexões de fragmentos consistentes**

  Quando o movimento de dados começa para um novo lote de fragmentos, qualquer mapa de fragmentos forneceu ligações de encaminhamento dependentede dados ao fragmento que armazena o fragmento são mortos e as ligações subsequentes das APIs do mapa de fragmentos às estratos são bloqueadas enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o movimento de dados é bloqueado enquanto o em curso, a fim de evitar inconsistências. As ligações com outros fragmentos no mesmo fragmento também serão mortas, mas voltarão a ser bem sucedidas imediatamente em nova tentativa. Uma vez que o lote é movido, os fragmentos são marcados online novamente para o fragmento do alvo e os dados de origem são removidos do fragmento de origem. O serviço passa por estes passos para cada lote até que todos os fragmentos sejam movidos. Isto conduzirá a várias operações de ligação durante a operação de divisão/fusão/movimento completa.  

- **Gerir a disponibilidade de shardlet**

  Limitar a ligação ao lote atual de fragmentos, tal como discutido acima, restringe o âmbito de indisponibilidade a um lote de fragmentos de cada vez. Isto é preferido numa abordagem em que o fragmento completo permaneceria offline para todos os seus fragmentos durante uma operação de divisão ou fusão. O tamanho de um lote, definido como o número de fragmentos distintos para se mover de cada vez, é um parâmetro de configuração. Pode ser definido para cada operação dividida e de fusão dependendo das necessidades de disponibilidade e desempenho da aplicação. Note que o alcance que está a ser bloqueado no mapa do fragmento pode ser maior do que o tamanho do lote especificado. Isto porque o serviço escolhe o tamanho do alcance de tal forma que o número real de valores-chave dos dados corresponde aproximadamente ao tamanho do lote. Isto é importante lembrar em particular para chaves escassamente povoadas.

- **Armazenamento de metadados**

  O serviço de fusão de divisões utiliza uma base de dados para manter o seu estado e para manter os registos durante o processamento de pedidos. O utilizador cria esta base de dados na sua subscrição e fornece a cadeia de ligação para a mesmo no ficheiro de configuração para a implementação do serviço. Os administradores da organização do utilizador também podem ligar-se a esta base de dados para rever o progresso do pedido e investigar informações detalhadas sobre possíveis falhas.

- **Sharding-awareness**

  O serviço de fusão de divisões diferencia entre (1) mesas de referência, (2) tabelas de referência e (3) tabelas normais. A semântica de uma operação de divisão/fusão/movimento depende do tipo de tabela utilizada e é definida da seguinte forma:

  - **Mesas escaced**

    Dividir, fundir e mover operações movem fragmentos de origem para alvo. Após a conclusão comêxito do pedido global, esses fragmentos já não estão presentes na fonte. Note que as tabelas-alvo devem existir no fragmento-alvo e não devem conter dados no intervalo-alvo antes do processamento da operação.

  - **Tabelas de referência**

    Para tabelas de referência, as operações de divisão, fusão e movimento copiam os dados da fonte para o fragmento-alvo. Note-se, no entanto, que não ocorrem alterações no fragmento de alvo para uma determinada tabela se alguma linha já estiver presente nesta tabela sobre o alvo. A mesa tem de estar vazia para qualquer operação de cópia de tabela de referência para ser processada.

  - **Outras Tabelas**

    Outras tabelas podem estar presentes na fonte ou no alvo de uma operação de divisão e fusão. O serviço de fusão de divisões ignora estas tabelas para qualquer movimento de dados ou operações de cópia. Note-se, no entanto, que podem interferir com estas operações em caso de constrangimentos.

    A informação sobre as tabelas de `SchemaInfo` referência vs. sharded é fornecida pelas APIs no mapa do fragmento. O exemplo que se segue ilustra a utilização destas APIs num determinado objeto de gestor de mapas de fragmentos:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));

    // publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    As tabelas "região" e "nação" são definidas como tabelas de referência e serão copiadas com operações de divisão/fusão/movimento. Por sua vez, «cliente» e "encomendas" são definidas como mesas de caca. `C_CUSTKEY`e `O_CUSTKEY` servir como a chave mais sharding.

- **Integridade Referencial**

  O serviço de fusão divide-se analisa dependências entre tabelas e utiliza relações-chave-chave estrangeiras para encenar as operações para mover tabelas de referência e fragmentos. Em geral, as tabelas de referência são copiadas primeiro por ordem de dependência, depois os fragmentos são copiados por ordem das suas dependências dentro de cada lote. Isto é necessário para que os constrangimentos FK-PK no fragmento-alvo sejam honrados à medida que os novos dados chegam.

- **Consistência do mapa do fragmento e eventual conclusão**

  Na presença de falhas, o serviço de fusão divide-se após qualquer interrupção e pretende completar quaisquer pedidos em curso. No entanto, pode haver situações irrecuperáveis, por exemplo, quando o fragmento-alvo é perdido ou comprometido sem reparação. Nestas circunstâncias, alguns fragmentos que deveriam ser movidos podem continuar a residir na fonte. O serviço garante que os mapeamentos de shardlet só são atualizados depois de os dados necessários terem sido copiados com sucesso para o alvo. Os fragmentos só são eliminados na fonte uma vez que todos os seus dados tenham sido copiados para o alvo e os mapeamentos correspondentes tenham sido atualizados com sucesso. A operação de eliminação ocorre em segundo plano enquanto a gama já está on-line no fragmento do alvo. O serviço de fusão dividida garante sempre a correção dos mapeamentos armazenados no mapa do fragmento.

## <a name="the-split-merge-user-interface"></a>A interface de utilizador de fusão dividida

O pacote de serviços de fusão divide-se inclui um papel de trabalhador e um papel web. O papel web é usado para submeter pedidos de fusão de divisão de forma interativa. Os principais componentes da interface do utilizador são os seguintes:

- **Tipo de Operação**

  O tipo de funcionamento é um botão de rádio que controla o tipo de operação realizada pelo serviço para este pedido. Pode escolher entre os cenários de divisão, fusão e movimento. Também pode cancelar uma operação previamente submetida. Pode usar pedidos de divisão, fusão e movimentação de mapas de fragmentos de alcance. Lista de mapas de fragmentos apenas apoiar operações de movimento.

- **Mapa do fragmento**

  A próxima secção de parâmetros de pedido cobre informações sobre o mapa do fragmento e a base de dados que hospeda o seu mapa de fragmentos. Em particular, você precisa fornecer o nome do servidor de base de dados Azure SQL e base de dados hospedando o shardmap, credenciais para ligar à base de dados do mapa de fragmentos e, finalmente, o nome do mapa do fragmento. Atualmente, a operação só aceita um único conjunto de credenciais. Estas credenciais precisam de ter permissões suficientes para realizar alterações no mapa do fragmento, bem como nos dados do utilizador nos fragmentos.

- **Gama fonte (divisão e fusão)**

  Uma operação de divisão e fusão processa uma gama utilizando a sua tecla baixa e alta. Para especificar uma operação com um valor-chave não limitado, verifique a caixa de verificação "High key is max" e deixe o campo de chaves elevado vazio. Os valores-chave de alcance que especifica não precisam de corresponder com precisão a um mapeamento e aos seus limites no seu mapa de fragmentos. Se não especificar quaisquer limites de alcance em todos os serviços, o serviço irá inferir automaticamente a gama mais próxima para si. Pode utilizar o script GetMappings.ps1 PowerShell para recuperar os mapeamentos atuais num determinado mapa.

- **Comportamento de Origem Dividida (dividido)**

  Para operações divididas, defina o ponto para dividir o intervalo de origem. Faça isso fornecendo a chave de sharding onde quer que a divisão ocorra. Utilize o botão de rádio especifique se pretende que a parte inferior do intervalo (excluindo a chave dividida) se mova ou se pretende que a parte superior se mova (incluindo a chave dividida).

- **Fonte Shardlet (movimento)**

  As operações de deslocação são diferentes das operações de divisão ou fusão, uma vez que não necessitam de um intervalo para descrever a fonte. Uma fonte de movimento é simplesmente identificada pelo valor-chave que planeia mover.

- **Target Shard (split)**

  Uma vez que tenha fornecido as informações sobre a origem da sua operação dividida, tem de definir onde pretende que os dados sejam copiados, fornecendo o servidor Azure SQL Db e o nome da base de dados para o alvo.

- **Gama de destino (fusão)**

  As operações de fusão movem fragmentos para um fragmento existente. Identifica o fragmento existente fornecendo os limites de alcance da gama existente com que pretende fundir-se.

- **Tamanho do lote**

  O tamanho do lote controla o número de fragmentos que ficarão offline de cada vez durante o movimento de dados. Este é um valor inteiro onde você pode usar valores menores quando você é sensível a longos períodos de tempo de inatividade para fragmentos. Valores maiores aumentarão o tempo que um dado fragmento está offline, mas pode melhorar o desempenho.

- **Id da operação (Cancelar)**

  Se tiver uma operação em curso que já não seja necessária, pode cancelar a operação fornecendo o seu ID de operação neste campo. Pode recuperar o ID de funcionamento da tabela de estado de pedido (ver Secção 8.1) ou a partir da saída no navegador web onde submeteu o pedido.

## <a name="requirements-and-limitations"></a>Requisitos e Limitações

A atual implementação do serviço de fusão por partes está sujeita aos seguintes requisitos e limitações:

- Os fragmentos precisam de existir e ser registados no mapa do fragmento antes que uma operação de fusão dividida nestes fragmentos possa ser executada.
- O serviço não cria tabelas ou quaisquer outros objetos de base de dados automaticamente como parte das suas operações. Isto significa que o esquema para todas as tabelas e tabelas de referência deve existir no fragmento-alvo antes de qualquer operação de divisão/fusão/movimento. As mesas escaced, em particular, devem estar vazias na gama em que novos fragmentos devem ser adicionados por uma operação de divisão/fusão/movimento. Caso contrário, a operação falhará a verificação inicial da consistência no fragmento do alvo. Note também que os dados de referência só são copiados se o quadro de referência estiver vazio e não existirem garantias de coerência relativamente a outras operações de escrita simultâneas nos quadros de referência. Recomendamos isto: ao executar operações de divisão/fusão, nenhuma outra operação de escrita altera altera as tabelas de referência.
- O serviço baseia-se na identidade da linha estabelecida por um índice ou chave único que inclui a chave de sharding para melhorar o desempenho e a fiabilidade para grandes fragmentos. Isto permite que o serviço mova dados com uma granularidade ainda mais fina do que apenas o valor-chave mais fino. Isto ajuda a reduzir a quantidade máxima de espaço de log e fechaduras que são necessárias durante a operação. Considere criar um índice único ou uma chave primária, incluindo a chave sharding numa determinada tabela, se quiser usar essa tabela com pedidos de divisão/fusão/movimento. Por razões de desempenho, a tecla sharding deve ser a coluna líder na tecla ou no índice.
- Durante o processamento de pedidos, alguns dados de fragmentos podem estar presentes tanto na fonte como no fragmento-alvo. Isto é necessário para proteger contra falhas durante o movimento do fragmento. A integração da fusão com o mapa do fragmento garante que as ligações através das APIs de encaminhamento dependente de dados utilizando o método **OpenConnectionForKey** no mapa do fragmento não vêem quaisquer estados intermédios inconsistentes. No entanto, ao ligar-se à fonte ou aos fragmentos-alvo sem utilizar o método **OpenConnectionForKey,** podem ser visíveis estados intermédios inconsistentes quando se estão a decorrer pedidos de divisão/fusão/movimento. Estas ligações podem apresentar resultados parciais ou duplicados dependendo do tempo ou do fragmento subjacente à ligação. Esta limitação inclui atualmente as ligações feitas por Elastic Scale Multi-Shard-Queries.
- A base de dados de metadados para o serviço de fusão por partes não deve ser partilhada entre diferentes funções. Por exemplo, um papel do serviço de fusão split-merge que funciona na encenação precisa de apontar para uma base de dados de metadados diferente da função de produção.

## <a name="billing"></a>Faturação

O serviço de fusão divide-se funciona como um serviço na nuvem na sua subscrição microsoft Azure. Por isso, os encargos com os serviços na nuvem aplicam-se à sua instância do serviço. A menos que realize frequentemente operações de divisão/fusão/movimento, recomendamos que elimine o seu serviço de nuvem de fusão de divisões. Isso poupa custos para executar ou implementar casos de serviço na nuvem. Pode reutilizar e iniciar a sua configuração facilmente executada sempre que precisar de executar operações de divisão ou fusão.

## <a name="monitoring"></a>Monitorização

### <a name="status-tables"></a>Tabelas de estado

O Serviço de fusão divide-se fornece a tabela **RequestStatus** na base de dados da loja de metadados para monitorização de pedidos concluídos e em curso. A tabela enumera uma linha para cada pedido de fusão dividido que tenha sido submetido a esta instância do serviço de fusão de divisões. Fornece as seguintes informações para cada pedido:

- **Carimbo de data/hora**

  A hora e a data em que o pedido foi iniciado.

- **OperaçãoId**

  Um GUID que identifica exclusivamente o pedido. Este pedido também pode ser usado para cancelar a operação enquanto ainda está em curso.

- **Estado**

  O estado atual do pedido. Para os pedidos em curso, também enumera a fase atual em que o pedido se encontra.

- **Pedido de Cancelamento**

  Uma bandeira que indica se o pedido foi cancelado.

- **Progresso**

  Uma estimativa percentual de conclusão para a operação. Um valor de 50 indica que a operação está aproximadamente 50% completa.

- **Detalhes**

  Um valor XML que fornece um relatório de progresso mais detalhado. O relatório de progresso é periodicamente atualizado à medida que os conjuntos de linhas são copiados de origem para destino. Em caso de falhas ou exceções, esta coluna também inclui informações mais detalhadas sobre a falha.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

O serviço de fusão de divisão utiliza o Azure Diagnostics com base no Azure SDK 2.5 para monitorização e diagnóstico. Controla a configuração de diagnósticos como explicado aqui: [Habilitar diagnósticos em Serviços de Nuvem Azure e Máquinas Virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md). O pacote de descarregamento inclui duas configurações de diagnóstico - uma para o papel web e outra para o papel do trabalhador. Inclui as definições para registar Contadores de Desempenho, registos IIS, Registos de Eventos do Windows e registos de eventos de fusão de divisões.

## <a name="deploy-diagnostics"></a>Implementar Diagnósticos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para permitir a monitorização e diagnóstico utilizando a configuração de diagnóstico para as funções web e dos trabalhadores fornecidas pelo pacote NuGet, execute os seguintes comandos utilizando o Azure PowerShell:

```powershell
$storageName = "<azureStorageAccount>"
$key = "<azureStorageAccountKey"
$storageContext = New-AzStorageContext -StorageAccountName $storageName -StorageAccountKey $key
$configPath = "<filePath>\SplitMergeWebContent.diagnostics.xml"
$serviceName = "<cloudServiceName>"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWeb"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWorker"
```

Pode encontrar mais informações sobre como configurar e implementar definições de diagnóstico aqui: [Ativar diagnósticos em Serviços de Nuvem Azure e Máquinas Virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Recuperar diagnósticos

Pode aceder facilmente aos seus diagnósticos a partir do Visual Studio Server Explorer na parte Azure da árvore Server Explorer. Abra uma instância do Estúdio Visual e na barra de menu clique em Ver e Server Explorer. Clique no ícone Azure para se ligar à sua subscrição azure. Em seguida >, navegue `<your storage account>` até à > -> -> Tabela de mesas -> WADLogsTable. Para mais informações, consulte [Server Explorer](https://msdn.microsoft.com/library/x603htbk.aspx).

![MESA DE LOGS][2]

O WADLogsTable realçado na figura acima contém os eventos detalhados do registo de aplicações do serviço de fusão de divisões. Note que a configuração padrão do pacote descarregado está orientada para uma implementação de produção. Por conseguinte, o intervalo em que os troncos e balcões são retirados das instâncias de serviço é grande (5 minutos). Para o teste e desenvolvimento, reduza o intervalo ajustando as definições de diagnóstico da web ou a função do trabalhador às suas necessidades. Clique à direita na função no Visual Studio Server Explorer (ver acima) e, em seguida, ajuste o Período de Transferência no diálogo para as definições de configuração de Diagnóstico:

![Configuração][3]

## <a name="performance"></a>Desempenho

Em geral, é de esperar um melhor desempenho dos níveis de serviço mais elevados e mais performantes na Base de Dados Azure SQL. As dotações superiores de IO, CPU e memória para os níveis de serviço mais elevados beneficiam a cópia a granel e eliminam as operações que o serviço de fusão de divisão utiliza. Por essa razão, aumente o nível de serviço apenas para essas bases de dados por um período de tempo definido e limitado.

O serviço também realiza consultas de validação como parte das suas operações normais. Estas consultas de validação verificam a presença inesperada de dados na gama-alvo e asseguram-se de que qualquer operação de divisão/fusão/movimento começa a partir de um estado consistente. Todas estas consultas funcionam sobre as gamas-chave de sharding definidas pelo âmbito da operação e pelo tamanho do lote fornecido como parte da definição de pedido. Estas consultas têm um melhor desempenho quando um índice está presente que tem a chave sharding como a coluna líder.

Além disso, uma propriedade de singularidade com a chave sharding como a coluna líder permitirá ao serviço usar uma abordagem otimizada que limita o consumo de recursos em termos de espaço de log e memória. Esta propriedade de singularidade é necessária para mover grandes tamanhos de dados (tipicamente acima de 1GB).

## <a name="how-to-upgrade"></a>Como atualizar

1. Siga os passos em [Implementar um serviço de fusão split.](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. Altere o ficheiro de configuração do serviço de nuvem para a sua implementação de fusão de divisão para refletir os novos parâmetros de configuração. Um novo parâmetro necessário é a informação sobre o certificado usado para encriptação. Uma maneira fácil de fazer isso é comparar o novo ficheiro de modelo de configuração do download com a sua configuração existente. Certifique-se de que adiciona as definições para "DataEncryptionPrimaryCertificateThumbprint" e "DataEncryptionPrimary" tanto para a web como para a função do trabalhador.
3. Antes de lançar a atualização para o Azure, certifique-se de que todas as operações de fusão de split-merge estão concluídas. Pode fazê-lo facilmente consultando as tabelas RequestStatus e PendingWorkflows na base de dados de metadados de fusão dividida para pedidos em curso.
4. Atualize a implementação do serviço de nuvem existente para fusão dividida na subscrição do Azure com o novo pacote e o ficheiro de configuração de serviço atualizado.

Não precisa de fornecer uma nova base de dados de metadados para a fusão por divisão para atualizar. A nova versão irá atualizar automaticamente a base de dados de metadados existente para a nova versão.

## <a name="best-practices--troubleshooting"></a>Melhores práticas e resolução de problemas

- Defina um inquilino de teste e exerça as suas operações mais importantes de divisão/fusão/movimento com o inquilino de teste em vários fragmentos. Certifique-se de que todos os metadados estão definidos corretamente no seu mapa de fragmentos e que as operações não violam constrangimentos ou chaves estrangeiras.
- Mantenha o tamanho dos dados do inquilino de teste acima do tamanho máximo de dados do seu maior inquilino para garantir que não encontra problemas relacionados com o tamanho dos dados. Isto ajuda-o a avaliar um limite superior no tempo que leva para mover um único inquilino.
- Certifique-se de que o seu esquema permite supressões. O serviço de fusão de divisão requer a capacidade de remover dados do fragmento de origem uma vez que os dados foram copiados com sucesso para o alvo. Por exemplo, **eliminar os gatilhos** pode impedir que o serviço apague os dados na fonte e possa causar falhas nas operações.
- A tecla sharding deve ser a coluna líder na sua chave primária ou definição de índice única. Isto garante o melhor desempenho para as consultas de validação de divisão ou fusão, e para as operações reais de movimento de dados e eliminação que operam sempre em intervalos-chave mais sharding.
- Collo o seu serviço de fusão de divisões na região e centro de dados onde residem as suas bases de dados.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
