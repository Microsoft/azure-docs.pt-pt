---
title: Recolher registos personalizados no Azure Monitor | Documentos da Microsoft
description: O Azure Monitor pode recolher eventos de arquivos de texto em computadores Windows e Linux.  Este artigo descreve como definir um registo personalizado novo e detalhes dos registos que criaram no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: bwren
ms.openlocfilehash: c80736dcd8be0c7ff3aae850aaaf9659f47daf36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996522"
---
# <a name="custom-logs-in-azure-monitor"></a>Registos personalizados no Azure Monitor
A origem de dados de registos personalizado no Azure Monitor permite-lhe recolher eventos do ficheiros de texto em computadores Windows e Linux. Muitos aplicativos registram informações em arquivos de texto em vez de serviços de registo padrão, como o registo de eventos do Windows ou Syslog. Depois de recolhidos, pode analisar os dados em campos individuais em suas consultas ou extrair os dados durante a coleção de campos individuais.

![Recolha de registos personalizado](media/data-sources-custom-logs/overview.png)

Os ficheiros de registo a recolher devem coincidir com os seguintes critérios.

- O registo tem de ter uma única entrada por linha ou utilizar um carimbo que corresponda a um dos seguintes formatos no início de cada entrada.

    HH: MM: DE AAAA-MM-DD<br>M/AAAA HH: MM: SS AM/PM<br>Mês DD, hh: mm: de aaaa<br />yyMMdd hh: mm:<br />ddMMyy hh: mm:<br />MMM d hh: mm:<br />dd/MMM/yyyy:HH:mm:ss zzz<br />aaaa-MM-ddTHH:mm:ssK

- O ficheiro de registo tem não permitem registo circular ou rotação do registo, onde o ficheiro será substituído com novas entradas.
- O ficheiro de registo deve usar a codificação ASCII ou UTF-8.  Não são suportados outros formatos, como o UTF-16.

>[!NOTE]
>Se existirem entradas duplicadas no ficheiro de registo, o Azure Monitor recolhê-los.  No entanto, os resultados da consulta serão inconsistente onde os filtrar os resultados mostram mais eventos que a contagem de resultado.  É importante que validar o registo para determinar se o aplicativo que cria a mesma está a causar esse comportamento e solucioná-lo se for possível antes de criar a definição de coleção de registo personalizado.  
>
  
>[!NOTE]
> Se a sua aplicação cria um novo ficheiro de registo por dia ou quando atingir um determinado tamanho, o agente do Log Analytics para Linux não Deteta-las após ser reiniciado. Isso é porque o agente apenas enumera e começa a monitorizar a existência de padrões com os registos especificados após a inicialização e, por isso precisa planejar em torno dele automatizando o reinício do agente.  Esta limitação não existe com o agente do Log Analytics para Windows.  
>

>[!NOTE]
> Uma área de trabalho do Log Analytics suporta os seguintes limites:
> 
> * Podem ser criados apenas 500 registos personalizados.
> * Uma tabela só suporta até 500 colunas. 
> * O número máximo de carateres para o nome da coluna é 500. 
>

## <a name="defining-a-custom-log"></a>Definir um registo personalizado
Utilize o procedimento seguinte para definir um arquivo de log personalizado.  Desloque-se no final deste artigo para obter instruções de um exemplo da adição de um registo personalizado.

### <a name="step-1-open-the-custom-log-wizard"></a>Passo 1. Abrir o Assistente de registo personalizado
O Assistente de registo personalizado é executado no portal do Azure e permite-lhe definir um registo personalizado novo para recolher.

1. No portal do Azure, selecione **áreas de trabalho do Log Analytics** > sua área de trabalho > **definições avançadas**.
2. Clique em **dados** > **os registos personalizados**.
3. Por predefinição, todas as alterações de configuração são automaticamente enviados por push para todos os agentes.  Para agentes do Linux, um ficheiro de configuração é enviado para o recoletor de dados Fluentd.  Se deseja modificar esse arquivo manualmente em cada agente do Linux, em seguida, desmarque a caixa *aplicar configuração abaixo aos meus computadores Linux*.
4. Clique em **adicionar +** para abrir o Assistente de registo personalizado.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Passo 2. Carregar e analisar um registo de exemplo
Começa carregando um exemplo do log personalizado.  O assistente irá analisar e apresentar as entradas existentes neste ficheiro para que possa validar.  O Azure Monitor irá utilizar o delimitador que especificar para identificar cada registo.

**Nova linha** é o delimitador padrão e é utilizado para ficheiros de registo que têm uma única entrada por linha.  Se a linha começa com uma data e hora dos formatos disponíveis, em seguida, pode especificar uma **Timestamp** delimitador que suporta as entradas que abrangem mais de uma linha.

Se for utilizado um delimitador de timestamp, em seguida, a propriedade TimeGenerated de cada registo armazenado no Azure Monitor será preenchida com a data/hora especificada para essa entrada no ficheiro de registo.  Se for utilizado um delimitador de linha nova, TimeGenerated é preenchida com a data e hora em que o Azure Monitor recolhidos a entrada.


1. Clique em **procurar** e navegue para um ficheiro de exemplo.  Tenha em atenção que isto pode botão pode ser rotulada como **Escolher ficheiro** em alguns navegadores.
2. Clique em **Seguinte**.
3. O Assistente de registo personalizado irá carregar o ficheiro e listar os registos que ele identifica.
4. Altere o delimitador que é utilizado para identificar um novo registo e selecionar o delimitador que melhor identifique os registos no seu ficheiro de registo.
5. Clique em **Seguinte**.

### <a name="step-3-add-log-collection-paths"></a>Passo 3. Adicionar caminhos da coleção de registos
Deve definir um ou mais caminhos no agente onde ele pode localizar o registo personalizado.  Pode fornecer um caminho específico e um nome para o ficheiro de registo ou pode especificar um caminho com um caráter universal para o nome. Isto suporta as aplicações que criar um novo ficheiro, por dia ou quando um ficheiro atinge um certo tamanho. Também pode fornecer vários caminhos para um único ficheiro de registo.

Por exemplo, um aplicativo pode criar um ficheiro de registo por dia com a data incluída no nome do que no log20100316.txt. Um padrão para um início de sessão pode ser *log\*. txt* que seria aplicada a qualquer ficheiro de registo após a aplicação de atribuição de nomes da esquema.

>[!NOTE]
> Se a sua aplicação cria um novo ficheiro de registo por dia ou quando atingir um determinado tamanho, o agente do Log Analytics para Linux não Deteta-las após ser reiniciado. Isso é porque o agente apenas enumera e começa a monitorizar a existência de padrões com os registos especificados após a inicialização e, por isso precisa planejar em torno dele automatizando o reinício do agente.  Esta limitação não existe com o agente do Log Analytics para Windows.  
>

A tabela seguinte fornece exemplos de padrões válidos para especificar os ficheiros de registo diferente.

| Descrição | Caminho |
|:--- |:--- |
| Todos os ficheiros numa *c:\Logs.* com extensão. txt no agente do Windows |C:\Logs\\\*. txt |
| Todos os ficheiros numa *c:\Logs.* com um nome a partir do registo e uma extensão. txt no agente do Windows |C:\Logs\log\*.txt |
| Todos os ficheiros numa */var/log/audit* com extensão. txt no agente do Linux |/var/log/audit/*.txt |
| Todos os ficheiros numa */var/log/audit* com um nome a partir do registo e uma extensão. txt no agente do Linux |/var/log/audit/log\*. txt |

1. Selecione Windows ou Linux para especificar qual formato de caminho que está a adicionar.
2. Escreva o caminho e clique nas **+** botão.
3. Repita o processo para quaisquer caminhos adicionais.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Passo 4. Forneça um nome e descrição para o registo
O nome que especificar será utilizado para o tipo de registo, conforme descrito acima.  Sempre terminará com _CL para distingui-la como um registo personalizado.

1. Escreva um nome para o registo.  O  **\_CL** sufixo é fornecido automaticamente.
2. Adicionar opcional **Descrição**.
3. Clique em **seguinte** para guardar a definição de registo personalizado.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Passo 5. Validar que estão a ser recolhidos os registos personalizados
Ele poderá demorar até uma hora para os dados iniciais de um registo personalizado novo para que sejam apresentadas no Monitor do Azure.  Irá iniciar a recolha de entradas dos registos encontrados no caminho especificado do ponto de que definiu o registo personalizado.  Ele não irá reter as entradas que carregou durante a criação de registo personalizado, mas ele irá recolher entradas já existentes nos ficheiros de registo que ele localiza.

Assim que o Azure Monitor inicia a recolha de log personalizado, seus registos de estará disponíveis com uma consulta de registo.  Utilize o nome que deu o registo personalizado, como o **tipo** na sua consulta.

> [!NOTE]
> Se a propriedade de RawData está em falta da consulta, terá de fechar e reabrir o browser.


### <a name="step-6-parse-the-custom-log-entries"></a>Passo 6. Analisar as entradas de registo personalizado
A entrada de registo inteiro será armazenada numa única propriedade chamada **RawData**.  Provavelmente desejará separar as diferentes partes de informações em cada entrada em propriedades individuais para cada registo. Consulte a [analisar dados de texto no Azure Monitor](../log-query/parse-text.md) para obter as opções na análise **RawData** em várias propriedades.

## <a name="removing-a-custom-log"></a>Remover um registo personalizado
Utilize o seguinte processo no portal do Azure para remover um registo personalizado que definiu anteriormente.

1. Do **dados** menu no **definições avançadas** sua área de trabalho, selecione **registos personalizados** para listar todos os seus registos personalizados.
2. Clique em **remover** ao lado de log personalizado a remover.


## <a name="data-collection"></a>Recolha de dados
O Azure Monitor irá recolher novas entradas de cada registo personalizado aproximadamente a cada 5 minutos.  O agente registrará seu lugar em cada arquivo de log recolhidos dos.  Se o agente ficar offline durante um período de tempo, em seguida, do Azure Monitor recolherá entradas onde pela última vez parou, mesmo que essas entradas foram criadas, enquanto o agente estava offline.

Todo o conteúdo da entrada de log é gravado numa única propriedade chamada **RawData**.  Ver [analisar dados de texto no Azure Monitor](../log-query/parse-text.md) para métodos para analisar cada importados entrada de log em várias propriedades.

## <a name="custom-log-record-properties"></a>Propriedades de registo de registo personalizado
Registros de log personalizado tem um tipo com o nome de registo que fornece e as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated |Data e hora em que o registo foi recolhido pelo Azure Monitor.  Se o registo de utilizar um delimitador baseados no tempo, em seguida, esta é a vez recolhida a partir da entrada. |
| SourceSystem |Tipo de registo foi recolhido a partir do agente. <br> Ligar OpsManager – agente de Windows, direta ou System Center Operations Manager <br> Linux – todos os agentes do Linux |
| RawData |Texto completo da entrada recolhido. Provavelmente vai querer [analisar estes dados em propriedades individuais](../log-query/parse-text.md). |
| ManagementGroupName |Nome do grupo de gestão para agentes do System Center Operations Manager.  Para outros agentes, é AOI -\<ID da área de trabalho\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Instruções de exemplo da adição de um registo personalizado
A secção seguinte descreve um exemplo de como criar um registo personalizado.  O registo de exemplo que está a ser recolhido tem uma única entrada em cada linha, começando com uma data e campos de tempo e, em seguida, delimitada por vírgulas para código, o estado e mensagem.  Várias entradas de exemplo são mostradas abaixo.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Carregar e analisar um registo de exemplo
Podemos fornecer um dos ficheiros de registo e pode ver os eventos que vamos coletar.  Nesse caso, nova linha é um delimitador suficiente.  Se uma única entrada no registo de pode distribuir várias linhas no entanto, seria necessário um delimitador de timestamp a ser utilizado.

![Carregar e analisar um registo de exemplo](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Adicionar caminhos da coleção de registos
Estarão localizados os ficheiros de registo em *C:\MyApp\Logs*.  Será criado um novo ficheiro por dia com um nome que inclui a data no padrão *appYYYYMMDD.log*.  Um padrão suficiente para este registo seria *C:\MyApp\Logs\\\*. log*.

![Caminho da coleção de registo](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Forneça um nome e descrição para o registo
Vamos utilizar um nome de *MyApp_CL* e escreva um **Descrição**.

![Nome do registo](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Validar que estão a ser recolhidos os registos personalizados
Usamos uma consulta de *tipo = MyApp_CL* para retornar todos os registos do log recolhido.

![Consulta de registo sem nenhum campo personalizado](media/data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Analisar as entradas de registo personalizado
Podemos usar campos personalizados para definir o *EventTime*, *código*, *estado*, e *mensagem* campos e podemos ver a diferença nos registos que são devolvidos pela consulta.

![Consulta de registo com campos personalizados](media/data-sources-custom-logs/query-02.png)

## <a name="alternatives-to-custom-logs"></a>Alternativas para registos personalizados
Embora registos personalizados são úteis se os critérios indicados sobre se adequa a seus dados, mas há casos, como o seguinte em que precisa outra estratégia:

- Os dados não se ajusta a estrutura necessária, como tendo o carimbo de hora num formato diferente.
- O ficheiro de registo não cumprir os requisitos, como a codificação do ficheiro ou uma estrutura de pastas não suportado.
- Os dados requer o pré-processamento ou filtragem antes de coleção. 

Nos casos em que os seus dados não podem ser recolhidos com registos personalizados, considere as estratégias alternativas a seguir:

- Utilize um script personalizado ou outro método para escrever dados para [eventos do Windows](data-sources-windows-events.md) ou [Syslog](data-sources-syslog.md) que é recolhido pelo Monitor do Azure. 
- Enviar os dados diretamente para utilizar o Azure Monitor [HTTP Data Collector API](data-collector-api.md). Um exemplo de utilização de runbooks na automatização do Azure é fornecido na [recolher registos de dados no Azure Monitor com um runbook da automatização do Azure](runbook-datacollect.md).

## <a name="next-steps"></a>Passos Seguintes
* Ver [analisar dados de texto no Azure Monitor](../log-query/parse-text.md) para métodos para analisar cada importados entrada de log em várias propriedades.
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.