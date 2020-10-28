---
title: Mover dados entre bases de dados de nuvem aumentadas horizontalmente
description: Explica como manipular fragmentos e mover dados através de um serviço auto-hospedado usando APIs de base de dados elástica.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5a646ffe1d306d7ea13da002715d5bd9b907107b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793471"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Mover dados entre bases de dados de nuvem aumentadas horizontalmente
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Se é um desenvolvedor de Software como desenvolvedor de Serviços, e de repente a sua aplicação sofre uma procura tremenda, precisa de acomodar o crescimento. Assim, adicione mais bases de dados (fragmentos). Como redistribuir os dados para as novas bases de dados sem perturbar a integridade dos dados? Utilize a **ferramenta de fusão dividida** para mover dados de bases de dados restritas para as novas bases de dados.  

A ferramenta de fusão dividida funciona como um serviço web Azure. Um administrador ou desenvolvedor utiliza a ferramenta para mover fragmentos (dados de um fragmento) entre diferentes bases de dados (fragmentos). A ferramenta utiliza a gestão do mapa de fragmentos para manter a base de dados de metadados de serviço e garantir mapeamentos consistentes.

![Descrição geral][1]

## <a name="download"></a>Download

[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Documentação

1. [Tutorial de ferramenta de fusão de base de dados elástica](elastic-scale-configure-deploy-split-and-merge.md)
2. [Configuração de segurança de fusão dividida](elastic-scale-split-merge-security-configuration.md)
3. [Considerações de segurança de fusão dividida](elastic-scale-split-merge-security-configuration.md)
4. [Gestão de mapas de partições horizontais](elastic-scale-shard-map-management.md)
5. [Migrar bases de dados existentes para aumentar horizontalmente](elastic-convert-to-use-elastic-tools.md)
6. [Ferramentas de base de dados elásticas](elastic-scale-introduction.md)
7. [Glossário das ferramentas das bases de dados elásticas](elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Por que usar a ferramenta de fusão dividida

- **Flexibilidade**

  As aplicações devem estender-se de forma flexível para além dos limites de uma única base de dados na Base de Dados Azure SQL. Utilize a ferramenta para mover os dados conforme necessário para novas bases de dados, mantendo a integridade.

- **Dividir para crescer**

  Para aumentar a capacidade global para lidar com o crescimento explosivo, criar capacidade adicional através do fragmento dos dados e distribuindo-os através de mais bases de dados incrementalmente até que as necessidades de capacidade sejam satisfeitas. Este é um excelente exemplo da característica **dividida.**

- **Fundir para encolher**

  A capacidade precisa de ser diminuída devido à natureza sazonal de um negócio. A ferramenta permite reduzir para menos unidades de escala quando o negócio abranda. A função 'fusão' no Serviço de Split-Merge de Escala Elástica cobre este requisito.

- **Gerir hotspots movendo shardlets**

  Com vários inquilinos por base de dados, a alocação de shardlets a fragmentos pode levar a estrangulamentos de capacidade em alguns fragmentos. Isto requer a reafectação de shardlets ou a deslocação de shardlets ocupados para fragmentos novos ou menos utilizados.

## <a name="concepts--key-features"></a>Conceitos & principais características

- **Serviços hospedados pelo cliente**

  A fusão dividida é entregue como um serviço hospedado pelo cliente. Tem de implementar e hospedar o serviço na sua subscrição Microsoft Azure. O pacote que descarrega do NuGet contém um modelo de configuração para completar com as informações para a sua implementação específica. Consulte o [tutorial de fusão dividida](elastic-scale-configure-deploy-split-and-merge.md) para mais detalhes. Uma vez que o serviço funciona na sua subscrição Azure, pode controlar e configurar a maioria dos aspetos de segurança do serviço. O modelo predefinido inclui as opções de configuração de TLS, autenticação do cliente baseada em certificados, encriptação para credenciais armazenadas, guarda do DoS e restrições de IP. Pode encontrar mais informações sobre os aspetos de segurança na seguinte [configuração de segurança de fusão de documentos.](elastic-scale-split-merge-security-configuration.md)

  O serviço implementado por defeito funciona com um trabalhador e uma função web. Cada um usa o tamanho A1 VM nos Serviços de Nuvem Azure. Embora não possa modificar estas definições ao implementar o pacote, pode alterá-las após uma implementação bem sucedida no serviço de nuvem em execução (através do portal Azure). Note que a função de trabalhador não deve ser configurada por mais do que um único exemplo por razões técnicas.

- **Integração de mapa de fragmentos**

  O serviço de fusão dividida interage com o mapa de fragmentos da aplicação. Ao utilizar o serviço de fusão dividida para dividir ou fundir gamas ou para mover fragmentos entre fragmentos, o serviço mantém automaticamente o mapa de fragmentos atualizado. Para tal, o serviço conecta-se à base de dados do gestor de mapas de fragmentos da aplicação e mantém gamas e mapeamentos à medida que os pedidos de divisão/fusão/movimento progridem. Isto garante que o mapa de fragmentos apresenta sempre uma visão atualizada quando as operações de fusão de divisão estão em curso. As operações de movimento de divisão, fusão e shardlet são implementadas movendo um lote de shardlets do fragmento de origem para o fragmento do alvo. Durante a operação de movimento de fragmentos, as shardlets sujeitas ao lote atual estão marcadas como offline no mapa de fragmentos e não estão disponíveis para ligações de encaminhamento dependentes de dados utilizando a **API OpenConnectionForKey.**

- **Conexões de fragmentos consistentes**

  Quando o movimento de dados começa para um novo lote de shardlets, qualquer mapa de fragmentos fornecido ligações de encaminhamento dependentes de dados para o fragmento que armazena o fragmento do shardlet são mortos e as ligações subsequentes das APIs do mapa de fragmentos para as shardlets são bloqueadas enquanto o movimento de dados está em andamento, a fim de evitar inconsistências. As ligações com outras shardlets no mesmo fragmento também serão mortas, mas serão novamente bem sucedidas imediatamente após a volta. Uma vez que o lote é movido, os fragmentos são marcados on-line novamente para o fragmento do alvo e os dados de origem são removidos do fragmento de origem. O serviço passa por estes passos para cada lote até que todas as shardlets tenham sido movidas. Isto levará a várias operações de separação durante a operação completa de divisão/fusão/movimento.  

- **Gerir a disponibilidade de shardlet**

  Limitar a eliminação da ligação ao atual lote de shardlets, tal como discutido acima, restringe o âmbito de indisponibilidade a um lote de shardlets de cada vez. Isto é preferido numa abordagem em que o fragmento completo permaneceria offline para todas as suas shardlets durante uma operação de divisão ou fusão. O tamanho de um lote, definido como o número de shardlets distintos para mover-se de cada vez, é um parâmetro de configuração. Pode ser definido para cada operação de divisão e fusão dependendo das necessidades de disponibilidade e desempenho da aplicação. Note que o alcance que está a ser bloqueado no mapa de fragmentos pode ser maior do que o tamanho do lote especificado. Isto porque o serviço escolhe o tamanho da gama de tal forma que o número real de valores-chave de fragmentos nos dados corresponde aproximadamente ao tamanho do lote. Isto é importante lembrar em particular para as chaves de cacos pouco povoadas.

- **Armazenamento de metadados**

  O serviço de fusão dividida utiliza uma base de dados para manter o seu estado e para manter registos durante o processamento do pedido. O utilizador cria esta base de dados na sua subscrição e fornece a cadeia de ligação para o mesmo no ficheiro de configuração para a implementação do serviço. Os administradores da organização do utilizador também podem ligar-se a esta base de dados para rever o pedido de progresso e investigar informações detalhadas sobre possíveis falhas.

- **Consciência de sharding**

  O serviço de fusão dividida diferencia entre (1) tabelas de fragmentos, (2) tabelas de referência e (3) tabelas normais. A semântica de uma operação de divisão/fusão/movimento depende do tipo da tabela utilizada e é definida da seguinte forma:

  - **Mesas de cacos**

    Divida, sepaze e mova as operações movem fragmentos de fonte para fragmentos de alvo. Após a conclusão bem sucedida do pedido global, estas lanças já não estão presentes na fonte. Note que as tabelas-alvo devem existir no fragmento-alvo e não devem conter dados no intervalo de destino antes do processamento da operação.

  - **Tabelas de referência**

    Para tabelas de referência, as operações de divisão, fusão e movimentação copiam os dados da fonte para o fragmento do alvo. Note-se, no entanto, que não ocorrem alterações no fragmento-alvo de uma dada tabela se já houver uma linha presente nesta tabela sobre o alvo. A tabela tem de estar vazia para qualquer operação de cópia de mesa de referência para ser processada.

  - **Outras mesas**

    Outras tabelas podem estar presentes na fonte ou no alvo de uma operação de divisão e fusão. O serviço de fusão dividida ignora estas tabelas para qualquer movimento de dados ou operações de cópia. Note-se, no entanto, que podem interferir com estas operações em caso de constrangimentos.

    As informações sobre tabelas de referência vs. fragmentos são fornecidas pelas `SchemaInfo` APIs no mapa de fragmentos. O exemplo a seguir ilustra a utilização destas APIs num determinado objeto de gestor de mapas de fragmentos:

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

    Os quadros "região" e "nação" são definidos como quadros de referência e serão copiados com operações de divisão/fusão/movimento. Por sua vez, as 'encomendas' e "clientes" são definidas como mesas de sharded. `C_CUSTKEY` e `O_CUSTKEY` servir como a chave de caco.

- **Integridade referencial**

  O serviço de fusão dividida analisa as dependências entre tabelas e utiliza relações-chave primárias estrangeiras para encenar as operações para mover tabelas de referência e fragmentos. Em geral, as tabelas de referência são copiadas primeiro por ordem de dependência, depois as shardlets são copiadas por ordem das suas dependências dentro de cada lote. Isto é necessário para que as restrições FK-PK no fragmento do alvo sejam honradas à medida que os novos dados chegam.

- **Consistência do mapa de fragmentos e eventual conclusão**

  Na presença de falhas, o serviço de fusão dividida retoma as operações após qualquer paragem e visa completar quaisquer pedidos em curso. No entanto, pode haver situações irrecuperáveis, por exemplo, quando o fragmento do alvo é perdido ou comprometido irremediavelmente. Nestas circunstâncias, algumas shardlets que deveriam ser movidas podem continuar a residir no fragmento de origem. O serviço garante que os mapeamentos de shardlet só são atualizados depois de os dados necessários terem sido copiados com sucesso para o alvo. Os fragmentos só são eliminados na fonte uma vez que todos os seus dados tenham sido copiados para o alvo e os mapeamentos correspondentes tenham sido atualizados com sucesso. A operação de eliminação acontece em segundo plano enquanto o intervalo já está online no fragmento do alvo. O serviço de fusão dividida garante sempre a correção dos mapeamentos armazenados no mapa de fragmentos.

## <a name="the-split-merge-user-interface"></a>A interface de utilizador de fusão dividida

O pacote de serviço de fusão dividida inclui um papel de trabalhador e uma função web. A função web é usada para apresentar pedidos de fusão dividida de forma interativa. Os principais componentes da interface do utilizador são os seguintes:

- **Tipo de operação**

  O tipo de funcionamento é um botão de rádio que controla o tipo de funcionamento realizado pelo serviço para este pedido. Pode escolher entre os cenários de divisão, fusão e mudança. Também pode cancelar uma operação previamente submetida. Pode utilizar pedidos de divisão, fusão e movimentação de mapas de fragmentos de alcance. Listar mapas de fragmentos apenas suportam operações de movimento.

- **Mapa de fragmentos**

  A próxima secção de parâmetros de pedido abrange informações sobre o mapa de fragmentos e a base de dados que hospeda o seu mapa de fragmentos. Em particular, você precisa fornecer o nome do servidor e da base de dados que hospeda o mapa de fragmentos, credenciais para ligar à base de dados do mapa de fragmentos e, finalmente, o nome do mapa de fragmentos. Atualmente, a operação apenas aceita um único conjunto de credenciais. Estas credenciais precisam de ter permissões suficientes para efetuar alterações no mapa de fragmentos, bem como para os dados do utilizador nos fragmentos.

- **Gama de origem (divisão e fusão)**

  Uma operação de divisão e fusão processa uma gama utilizando a sua tecla baixa e alta. Para especificar uma operação com um valor de chave elevado não limitado, verifique a caixa de verificação "High é max" e deixe o campo de teclas elevado vazio. Os valores-chave de alcance que especifica não precisam de corresponder com precisão a um mapeamento e os seus limites no seu mapa de fragmentos. Se não especificar quaisquer limites de alcance em todos os limites, o serviço inferirá automaticamente o alcance mais próximo para si. Pode utilizar o GetMappings.ps1 script PowerShell para recuperar os mapeamentos atuais num determinado mapa de fragmentos.

- **Comportamento de fonte dividida (divisão)**

  Para operações divididas, defina o ponto para dividir o intervalo de origem. Fá-lo fornecendo a chave de cace onde quer que a divisão ocorra. Utilize o botão de rádio especificar se deseja que a parte inferior do intervalo (excluindo a tecla dividida) se mova ou se pretende que a parte superior se mova (incluindo a tecla dividida).

- **Fragmento de origem (movimento)**

  As operações de movimentação são diferentes das operações de divisão ou fusão, uma vez que não necessitam de um intervalo para descrever a fonte. Uma fonte de movimento é simplesmente identificada pelo valor chave de fragmentos que planeia mover.

- **Fragmento de alvo (dividido)**

  Depois de ter fornecido as informações sobre a origem da sua operação dividida, tem de definir para onde pretende que os dados sejam copiados, fornecendo o nome do servidor e da base de dados para o alvo.

- **Intervalo de destino (fusão)**

  As operações de fusão movem fragmentos para um fragmento existente. Identifica o fragmento existente fornecendo os limites de alcance da gama existente com a qual pretende fundir-se.

- **Tamanho do lote**

  O tamanho do lote controla o número de shardlets que ficarão offline de cada vez durante o movimento dos dados. Este é um valor inteiro onde você pode usar valores menores quando você é sensível a longos períodos de tempo de inatividade para shardlets. Valores maiores aumentarão o tempo que um determinado fragmento está offline, mas pode melhorar o desempenho.

- **ID da Operação (cancelar)**

  Se tiver uma operação em curso que já não seja necessária, pode cancelar a operação fornecendo o seu ID de funcionamento neste campo. Pode recuperar o ID de operação a partir da tabela de status do pedido (ver Secção 8.1) ou da saída no navegador web onde submeteu o pedido.

## <a name="requirements-and-limitations"></a>Requisitos e limitações

A atual implementação do serviço de fusão por partes está sujeita aos seguintes requisitos e limitações:

- Os fragmentos precisam de existir e ser registados no mapa de fragmentos antes de uma operação de fusão dividida nestes fragmentos poder ser realizada.
- O serviço não cria tabelas ou quaisquer outros objetos de base de dados automaticamente como parte das suas operações. Isto significa que o esquema para todas as tabelas e tabelas de referência em pedaços deve existir no fragmento do alvo antes de qualquer operação de divisão/fusão/movimento. As tabelas de cacos, em particular, devem estar vazias no intervalo onde devem ser adicionadas novas shardlets através de uma operação de divisão/fusão/movimento. Caso contrário, a operação falhará na verificação de consistência inicial no fragmento do alvo. Note também que os dados de referência só são copiados se a tabela de referência estiver vazia e se não houver garantias de consistência relativamente a outras operações de escrita simultâneas nos quadros de referência. Recomendamos isto: ao executar operações de divisão/fusão, nenhuma outra operação de escrita faz alterações nas tabelas de referência.
- O serviço baseia-se na identidade de linha estabelecida por um índice ou chave único que inclui a chave de fragmentos para melhorar o desempenho e a fiabilidade para grandes shardlets. Isto permite que o serviço mova dados com uma granularidade ainda mais fina do que apenas o valor chave de fragmentos. Isto ajuda a reduzir a quantidade máxima de espaço de log e fechaduras que são necessárias durante a operação. Considere criar um índice único ou uma chave primária, incluindo a chave de fragmentos numa dada tabela, se quiser utilizar essa tabela com pedidos de divisão/fusão/movimento. Por razões de desempenho, a chave de fragmentos deve ser a coluna líder na chave ou no índice.
- Durante o tratamento do pedido, alguns dados de fragmentos podem estar presentes tanto na fonte como no fragmento do alvo. Isto é necessário para proteger contra falhas durante o movimento do shardlet. A integração da fusão dividida com o mapa de fragmentos garante que as ligações através das APIs de encaminhamento dependentes de dados utilizando o método **OpenConnectionForKey** no mapa de fragmentos não vêem quaisquer estados intermédios inconsistentes. No entanto, ao ligar-se à fonte ou aos fragmentos-alvo sem utilizar o método **OpenConnectionForKey,** estados intermédios inconsistentes podem ser visíveis quando os pedidos de divisão/fusão/movimento estão em curso. Estas ligações podem apresentar resultados parciais ou duplicados dependendo do tempo ou do fragmento subjacente à ligação. Esta limitação inclui atualmente as ligações efetuadas por Escala Elástica Multi-Shard-Consultas.
- A base de dados de metadados para o serviço de fusão dividida não deve ser partilhada entre diferentes funções. Por exemplo, um papel do serviço de fusão dividida em execução tem de apontar para uma base de dados de metadados diferente da função de produção.

## <a name="billing"></a>Faturação

O serviço de fusão dividida funciona como um serviço de cloud na sua subscrição Microsoft Azure. Assim, os custos para serviços na nuvem aplicam-se à sua instância do serviço. A menos que realize frequentemente operações de divisão/fusão/movimento, recomendamos que elimine o seu serviço de nuvem de fusão dividida. Isso poupa custos para a execução ou implementação de instâncias de serviço em nuvem. Pode voltar a implantar e iniciar a sua configuração facilmente runível sempre que precisar de realizar operações de divisão ou fusão.

## <a name="monitoring"></a>Monitorização

### <a name="status-tables"></a>Tabelas de estado

O Serviço de Fusão Dividida fornece a tabela **RequestStatus** na base de dados da loja de metadados para monitorização de pedidos preenchidos e em curso. A tabela lista uma linha para cada pedido de fusão por partes que tenha sido submetida a este caso do serviço de fusão por partes. Fornece as seguintes informações para cada pedido:

- **Timestamp**

  A hora e a data em que o pedido foi iniciado.

- **OperationId**

  Um GUID que identifica exclusivamente o pedido. Este pedido também pode ser usado para cancelar a operação enquanto ainda está em curso.

- **Estado**

  O estado atual do pedido. Para os pedidos em curso, enumera também a fase atual em que o pedido se encontra.

- **CancelRequest**

  Uma bandeira que indica se o pedido foi cancelado.

- **Progresso**

  Uma estimativa percentual de conclusão para a operação. Um valor de 50 indica que a operação está aproximadamente 50% completa.

- **Detalhes**

  Um valor XML que fornece um relatório de progresso mais detalhado. O relatório de progresso é periodicamente atualizado à medida que os conjuntos de linhas são copiados de fonte para alvo. Em caso de falhas ou exceções, esta coluna também inclui informações mais detalhadas sobre a falha.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

O serviço de fusão dividida utiliza Azure Diagnostics com base no Azure SDK 2.5 para monitorização e diagnóstico. Você controla a configuração de diagnóstico como explicado aqui: [Habilitando diagnósticos em Azure Cloud Services e Máquinas Virtuais](../../cloud-services/cloud-services-dotnet-diagnostics.md). O pacote de descarregamento inclui duas configurações de diagnóstico - uma para o papel web e outra para o papel de trabalhador. Inclui as definições para registar contadores de desempenho, registos IIS, Registos de Eventos do Windows e registos de eventos de fusão dividida.

## <a name="deploy-diagnostics"></a>Implementar Diagnósticos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para permitir a monitorização e diagnóstico utilizando a configuração de diagnóstico para as funções web e de trabalhador fornecidas pelo pacote NuGet, execute os seguintes comandos utilizando a Azure PowerShell:

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

Pode encontrar mais informações sobre como configurar e implementar definições de diagnóstico aqui: [Habilitar diagnósticos em Serviços de Nuvem Azure e Máquinas Virtuais](../../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Recuperar diagnósticos

Pode aceder facilmente aos seus diagnósticos a partir do Visual Studio Server Explorer na parte Azure da árvore Server Explorer. Abra uma instância do Estúdio Visual e na barra de menu clique em Ver e Server Explorer. Clique no ícone Azure para ligar à sua subscrição Azure. Em seguida, navegue para Azure -> Storage -> `<your storage account>` -> Tabelas -> WADLogsTable. Para obter mais informações, consulte [o Server Explorer](/previous-versions/x603htbk(v=vs.140)).

![WADLogsTable][2]

O WADLogsTable realçado na figura acima contém os eventos detalhados do registo de aplicações do serviço de fusão dividida. Note que a configuração padrão do pacote descarregado está orientada para uma implementação de produção. Portanto, o intervalo em que os troncos e balcões são retirados das instâncias de serviço é grande (5 minutos). Para o teste e desenvolvimento, reduza o intervalo ajustando as definições de diagnóstico da web ou do papel do trabalhador às suas necessidades. Clique com o botão direito na função no Explorador do Servidor do Estúdio Visual (ver acima) e, em seguida, ajuste o Período de Transferência no diálogo para as definições de configuração de Diagnóstico:

![Configuração][3]

## <a name="performance"></a>Desempenho

Em geral, espera-se um melhor desempenho dos níveis de serviço mais elevados e mais performantes. As alocações mais elevadas de IO, CPU e memória para os níveis de serviço mais elevados beneficiam a cópia a granel e eliminam as operações que o serviço de fusão dividida utiliza. Por essa razão, aumente o nível de serviço apenas para essas bases de dados por um período de tempo definido e limitado.

O serviço também realiza consultas de validação como parte das suas operações normais. Estas consultas de validação verificam a presença inesperada de dados na gama alvo e asseguram que qualquer operação de divisão/fusão/movimento começa a partir de um estado consistente. Todas estas consultas funcionam sobre as gamas de chaves de sharding definidas pelo âmbito da operação e pelo tamanho do lote fornecido como parte da definição de pedido. Estas consultas têm um melhor desempenho quando um índice está presente que tem a chave de fragmentos como a coluna principal.

Além disso, uma propriedade de singularidade com a chave de fragmentos como a coluna líder permitirá ao serviço usar uma abordagem otimizada que limita o consumo de recursos em termos de log espaço e memória. Esta propriedade de singularidade é necessária para mover grandes tamanhos de dados (tipicamente acima de 1GB).

## <a name="how-to-upgrade"></a>Como atualizar

1. Siga os passos no [Implementar um serviço de fusão dividida.](elastic-scale-configure-deploy-split-and-merge.md)
2. Altere o ficheiro de configuração do serviço de nuvem para a sua implementação de fusão dividida para refletir os novos parâmetros de configuração. Um novo parâmetro requerido é a informação sobre o certificado utilizado para encriptação. Uma maneira fácil de o fazer é comparar o novo ficheiro de modelo de configuração do download com a configuração existente. Certifique-se de que adiciona as definições de "DataEncryptionPrimaryCertificateThumbprint" e "DataEncryptionPrimary" tanto para a web como para o papel do trabalhador.
3. Antes de implementar a atualização para o Azure, certifique-se de que todas as operações de fusão em execução estão concluídas. Pode fazê-lo facilmente consultando as tabelas RequestStatus e PendingWorkflows na base de dados de metadados de fusão dividida para pedidos em curso.
4. Atualize a implementação do serviço de nuvem existente para a fusão dividida na sua subscrição Azure com o novo pacote e o seu ficheiro de configuração de serviço atualizado.

Não é necessário providenciar uma nova base de dados de metadados para a fusão por partes para atualizar. A nova versão irá atualizar automaticamente a base de dados de metadados existente para a nova versão.

## <a name="best-practices--troubleshooting"></a>Melhores práticas e resolução de problemas

- Defina um inquilino de teste e exerça as suas operações mais importantes de divisão/fusão/movimento com o inquilino de teste em vários fragmentos. Certifique-se de que todos os metadados estão corretamente definidos no seu mapa de fragmentos e que as operações não violam constrangimentos ou chaves estrangeiras.
- Mantenha o tamanho dos dados do inquilino de teste acima do tamanho máximo de dados do seu maior inquilino para garantir que não está a encontrar problemas relacionados com o tamanho dos dados. Isto ajuda-o a avaliar um limite superior sobre o tempo que leva para mover um único inquilino.
- Certifique-se de que o seu esquema permite supressões. O serviço de fusão dividida requer a capacidade de remover dados do fragmento de origem uma vez que os dados tenham sido copiados com sucesso para o alvo. Por exemplo, **eliminar os gatilhos** pode impedir que o serviço elimine os dados da fonte e pode causar a falha das operações.
- A chave de fragmentos deve ser a coluna principal na sua chave primária ou definição de índice único. Isso garante o melhor desempenho para as consultas de validação de divisão ou fusão, e para as operações reais de movimento de dados e eliminação que operam sempre em intervalos de chaves de fragmentos.
- Transpareça o seu serviço de fusão dividida na região e no centro de dados onde residem as suas bases de dados.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/elastic-scale-overview-split-and-merge/diagnostics-config.png