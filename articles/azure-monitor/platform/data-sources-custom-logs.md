---
title: Recolher registos personalizados no Monitor Azure [ Monitor' Microsoft Docs
description: O Azure Monitor pode recolher eventos a partir de ficheiros de texto em computadores Windows e Linux.  Este artigo descreve como definir um novo registo personalizado e detalhes dos registos que criam no Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/26/2019
ms.openlocfilehash: 1e889aaef7cd01cd743e8063a8a1dd5138ba9d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670598"
---
# <a name="custom-logs-in-azure-monitor"></a>Registos personalizados no Azure Monitor

A fonte de dados custom Logs no Monitor Azure permite-lhe recolher eventos a partir de ficheiros de texto em computadores Windows e Linux. Muitas aplicações registam informações para ficheiros de texto em vez de serviços de registo padrão, como registo de eventos do Windows ou Syslog. Uma vez recolhidos, pode analisar os dados em campos individuais nas suas consultas ou extrair os dados durante a recolha para campos individuais.

![Coleção de registos personalizado](media/data-sources-custom-logs/overview.png)

Os ficheiros de registo a recolher devem corresponder aos seguintes critérios.

- O registo deve ter uma única entrada por linha ou utilizar uma marca de tempo correspondente a um dos seguintes formatos no início de cada entrada.

    Yyy-MM-DD HH:MM:SS<br>M/D/YYYY HH:MM:SS AM/PM<br>Mon DD, YYYY HH:MM:SS<br />yyMMdd HH:mm:ss<br />ddMMyy HH:mm:ss<br />MMM d hh:mm:ss<br />dd/MMM/yyyy:HH:mm:ss zzz<br />yyy-MM-ddTHH:mm:ssK

- O ficheiro de registo não deve permitir a exploração circular ou a rotação de registo, onde o ficheiro é substituído com novas entradas.
- O ficheiro de registo deve utilizar codificação ASCII ou UTF-8.  Outros formatos como o UTF-16 não são suportados.

>[!NOTE]
> Se houver entradas duplicadas no ficheiro de registo, o Monitor Azure irá recolhê-las. No entanto, os resultados da consulta serão inconsistentes onde os resultados do filtro mostram mais eventos do que a contagem de resultados. Será importante que valide o registo para determinar se a aplicação que o cria está a causar este comportamento e abordá-lo se possível antes de criar a definição de recolha de registos personalizada.  
>

>[!NOTE]
> Um espaço de trabalho log Analytics suporta os seguintes limites:
> 
> * Apenas 500 registos personalizados podem ser criados.
> * Uma mesa só suporta até 500 colunas. 
> * O número máximo de caracteres para o nome da coluna é de 500. 
>

>[!IMPORTANT]
>A recolha de registos personalizada requer que a aplicação que escreve o ficheiro de registo descarregue periodicamente o conteúdo do registo para o disco. Isto porque a recolha de registos personalizado baseia-se em notificações de alteração do sistema de ficheiros para o ficheiro de registo que está a ser rastreado.

## <a name="defining-a-custom-log"></a>Definindo um registo personalizado
Utilize o seguinte procedimento para definir um ficheiro de registo personalizado.  Percorra até ao final deste artigo para uma amostra de adicionar um registo personalizado.

### <a name="step-1-open-the-custom-log-wizard"></a>Passo 1. Abra o Assistente de Registo Personalizado
O Assistente de Registo Personalizado funciona no portal Azure e permite-lhe definir um novo log personalizado para recolher.

1. No portal Azure, selecione espaços de **trabalho Log Analytics** > o seu espaço de trabalho > **Definições Avançadas**.
2. Clique em**registos personalizados**de **dados** > .
3. Por predefinição, todas as alterações de configuração são automaticamente empurradas para todos os agentes. Para os agentes Linux, um ficheiro de configuração é enviado para o coletor de dados Fluentd.
4. Clique em **Adicionar+** para abrir o Assistente de Registo Personalizado.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Passo 2. Carregar e analisar um registo de amostra
Comece por carregar uma amostra do registo personalizado.  O assistente analisará e exibirá as entradas neste ficheiro para que valide.  O Monitor Azure utilizará o delimitador que especifica para identificar cada registo.

**New Line** é o delimitador predefinido e é utilizado para ficheiros de registo que têm uma única entrada por linha.  Se a linha começar com uma data e hora num dos formatos disponíveis, então pode especificar um delimitador **timestamp** que suporta entradas que se estendem por mais de uma linha.

Se for utilizado um delimitador de marca temporal, a propriedade TimeGenerated de cada registo armazenado no Monitor Azure será povoada com a data/hora especificada para essa entrada no ficheiro de registo.  Se for utilizado um novo delimitador de linha, o TimeGenerated é povoado com data e hora que o Monitor Azure recolheu a entrada.

1. Clique em **Navegar** e navegue num ficheiro de amostra.  Note que este botão pode ser rotulado **Escolha File** em alguns navegadores.
2. Clique em **Seguinte**.
3. O Assistente de Registo Personalizado irá carregar o ficheiro e listar os registos que identifica.
4. Altere o delimitador que é utilizado para identificar um novo registo e selecione o delimitador que melhor identifica os registos no seu ficheiro de registo.
5. Clique em **Seguinte**.

### <a name="step-3-add-log-collection-paths"></a>Passo 3. Adicione caminhos de recolha de registos
Deve definir um ou mais caminhos no agente onde pode localizar o registo personalizado.  Pode fornecer um caminho e nome específicos para o ficheiro de registo, ou pode especificar um caminho com um wildcard para o nome. Isto suporta aplicações que criam um novo ficheiro todos os dias ou quando um ficheiro atinge um determinado tamanho. Também pode fornecer vários caminhos para um único ficheiro de registo.

Por exemplo, uma aplicação pode criar um ficheiro de registo todos os dias com a data incluída no nome como em log20100316.txt. Um padrão para tal registo pode ser *log\*.txt* que se aplicaria a qualquer ficheiro de registo na sequência do esquema de nomeação do pedido.

A tabela que se segue fornece exemplos de padrões válidos para especificar diferentes ficheiros de registo.

| Descrição | Caminho |
|:--- |:--- |
| Todos os ficheiros em *C:\Logs* com extensão .txt no agente Windows |C:\Logs\\\*.txt |
| Todos os ficheiros em *C:\Logs* com um nome começando com log e uma extensão .txt no agente Windows |C:\Logs\log\*.txt |
| Todos os ficheiros em */var/log/audit* com extensão .txt no agente Linux |/var/log/audit/*.txt |
| Todos os ficheiros em */var/log/audit* com um nome começando com registo e uma extensão .txt no agente Linux |/var/log/audit/log\*.txt |

1. Selecione Windows ou Linux para especificar qual o formato de caminho que está a adicionar.
2. Digite o caminho **+** e clique no botão.
3. Repita o processo para quaisquer caminhos adicionais.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Passo 4. Forneça um nome e descrição para o registo
O nome que especifica será utilizado para o tipo de registo descrito acima.  Terminará sempre com _CL para distingui-lo como um registo personalizado.

1. Digite um nome para o registo.  O sufixo ** \_CL** é fornecido automaticamente.
2. Adicione uma **descrição**opcional.
3. Clique em **Seguir** para guardar a definição de registo personalizada.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Passo 5. Valide que os registos personalizados estão a ser recolhidos
Pode levar até uma hora para que os dados iniciais de um novo registo personalizado apareçam no Monitor Azure.  Começará a recolher entradas dos registos encontrados no caminho que especificou a partir do ponto em que definiu o registo personalizado.  Não irá reter as entradas que carregou durante a criação de registo personalizado, mas irá recolher as entradas já existentes nos ficheiros de registo que localiza.

Assim que o Monitor Azure começar a recolher a partir do registo personalizado, os seus registos estarão disponíveis com uma consulta de registo.  Use o nome que deu ao registo personalizado como o **Tipo** na sua consulta.

> [!NOTE]
> Se a propriedade RawData estiver ausente da consulta, poderá ter de fechar e reabrir o seu navegador.

### <a name="step-6-parse-the-custom-log-entries"></a>Passo 6. Analisar as entradas de registo personalizadas
Toda a entrada de registo será armazenada numa única propriedade chamada **RawData**.  Provavelmente, irá querer separar as diferentes peças de informação em cada entrada em propriedades individuais para cada registo. Consulte os dados de [texto da Parse no Monitor Azure](../log-query/parse-text.md) para obter opções sobre a análise do **RawData** em várias propriedades.

## <a name="removing-a-custom-log"></a>Remover um registo personalizado
Utilize o seguinte processo no portal Azure para remover um registo personalizado que previamente definido.

1. A partir do menu **Dados** nas **Definições Avançadas** para o seu espaço de trabalho, selecione **Registos Personalizados** para listar todos os seus registos personalizados.
2. Clique em **Remover** ao lado do registo personalizado para remover.

## <a name="data-collection"></a>Recolha de dados
O Monitor Azure recolherá novas entradas de cada registo personalizado aproximadamente a cada 5 minutos.  O agente registará o seu lugar em cada ficheiro de registo que recolhe.  Se o agente ficar offline por um período de tempo, então o Monitor Azure recolherá entradas de onde ficou parada, mesmo que essas entradas tenham sido criadas enquanto o agente estava offline.

Todo o conteúdo da entrada de registo está escrito numa única propriedade chamada **RawData**.  Consulte os dados de [texto da Parse no Monitor Azure](../log-query/parse-text.md) para obter métodos para analisar cada entrada de registo importado em várias propriedades.

## <a name="custom-log-record-properties"></a>Propriedades de registo de registo personalizado
Os registos de registo personalizados têm um tipo com o nome de registo que fornece e as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated |Data e hora em que o disco foi recolhido pelo Monitor Azure.  Se o registo utilizar um delimitador baseado no tempo, então este é o tempo recolhido a partir da entrada. |
| SourceSystem |Tipo de agente de que o registo foi recolhido. <br> OpsManager – Agente Windows, quer diretamente ligue ou Gestor de Operações do Centro de Sistema <br> Linux - Todos os agentes linux |
| RawData |Texto completo da entrada recolhida. Provavelmente, irá querer [analisar estes dados em propriedades individuais.](../log-query/parse-text.md) |
| ManagementGroupName |Nome do grupo de gestão para agentes de gestão de operações do Centro de Sistema.  Para outros agentes, este\<é O II- espaço de trabalho\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Experimente a amostra de adicionar um registo personalizado
A secção seguinte percorre um exemplo de criação de um registo personalizado.  O registo de amostras que está a ser recolhido tem uma única entrada em cada linha a partir de uma data e hora e, em seguida, campos de delimitados de vírem para código, estado e mensagem.  Várias entradas de amostra são mostradas abaixo.

    2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2019-08-27 01:38:22 302,Error,Application could not connect to database
    2019-08-27 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Carregar e analisar um registo de amostra
Fornecemos um dos ficheiros de registo e podemos ver os eventos que irá recolher.  Neste caso, a New Line é um delimitador suficiente.  No entanto, se uma única entrada no registo pudesse abranger várias linhas, então um delimitador de marca tempora teria de ser utilizado.

![Carregar e analisar um registo de amostra](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Adicione caminhos de recolha de registos
Os ficheiros de registo serão localizados em *C:\MyApp\Logs*.  Um novo ficheiro será criado todos os dias com um nome que inclui a data no padrão *appYYYYMMDD.log*.  Um padrão suficiente para este registo seria *C:\MyApp\Logs\\\*.log*.

![Caminho de recolha de log](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Forneça um nome e descrição para o registo
Usamos um nome de *MyApp_CL* e digitamos uma **Descrição**.

![Nome do registo](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Valide que os registos personalizados estão a ser recolhidos
Usamos uma simples consulta de *MyApp_CL* para devolver todos os registos do registo recolhido.

![Consulta de log sem campos personalizados](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternativas a registos personalizados
Embora os registos personalizados sejam úteis se os seus dados se adequarem aos critérios listados, mas existem casos como o seguinte em que precisa de outra estratégia:

- Os dados não se enquadram na estrutura necessária, como ter a marca de tempo num formato diferente.
- O ficheiro de registo não adere a requisitos como codificação de ficheiros ou estrutura de pasta não suportada.
- Os dados requerem pré-processamento ou filtragem antes da recolha. 

Nos casos em que os seus dados não podem ser recolhidos com registos personalizados, considere as seguintes estratégias alternativas:

- Utilize um script personalizado ou outro método para escrever dados para [Windows Events](data-sources-windows-events.md) ou [Syslog](data-sources-syslog.md) que são recolhidos pelo Azure Monitor. 
- Envie os dados diretamente para o Monitor Azure utilizando a [Http Data Collector API](data-collector-api.md). 

## <a name="next-steps"></a>Passos seguintes
* Consulte os dados de [texto da Parse no Monitor Azure](../log-query/parse-text.md) para obter métodos para analisar cada entrada de registo importado em várias propriedades.
* Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados.
