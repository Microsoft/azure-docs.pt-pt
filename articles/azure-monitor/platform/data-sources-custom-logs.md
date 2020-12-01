---
title: Colete registos personalizados com o agente Log Analytics no Azure Monitor
description: O Azure Monitor pode recolher eventos a partir de ficheiros de texto em computadores Windows e Linux.  Este artigo descreve como definir um novo registo personalizado e detalhes dos registos que criam no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: b2b27da096ed18170ca8c9d70f31dc955fb74950
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352835"
---
# <a name="collect-custom-logs-with-log-analytics-agent-in-azure-monitor"></a>Colete registos personalizados com o agente Log Analytics no Azure Monitor

A fonte de dados de Registos Personalizados para o agente Log Analytics no Azure Monitor permite-lhe recolher eventos a partir de ficheiros de texto em ambos os computadores Windows e Linux. Muitas aplicações registam informações em ficheiros de texto em vez de serviços de registo padrão, como o registo do Windows Event ou o Syslog. Uma vez recolhidos, pode analisar os dados em campos individuais nas suas consultas ou extrair os dados durante a recolha para campos individuais.

> [!IMPORTANT]
> Este artigo cobre a recolha de registos personalizados com o [agente Log Analytics,](log-analytics-agent.md) que é um dos agentes utilizados pelo Azure Monitor. Outros agentes recolhem dados diferentes e são configurados de forma diferente. Consulte [a visão geral dos agentes do Azure Monitor](agents-overview.md) para obter uma lista dos agentes disponíveis e os dados que podem recolher.

![Coleção de registos personalizados](media/data-sources-custom-logs/overview.png)

Os ficheiros de registo a recolher devem corresponder aos seguintes critérios.

- O registo deve ter uma única entrada por linha ou utilizar um timetamp correspondente a um dos seguintes formatos no início de cada entrada.

    YYYY-MM-DD HH:MM:SS<br>M/D/YYYY HH:MM:SS AM/PM<br>Mon DD, YYYY HH:MM:SS<br />ymMdd HH:mm:ss<br />ddMMyy HH:mm:ss<br />MMM d hh:mm:ss<br />dd/MMM/yyyy:HH:mm:ss zzz<br />yyyy-MM-ddTHH:mm:ssK

- O ficheiro de registo não deve permitir a rotação circular de registo ou registo, onde o ficheiro é substituído com novas entradas.
- O ficheiro de registo deve utilizar a codificação ASCII ou UTF-8.  Outros formatos como UTF-16 não são suportados.
- Para o Linux, a conversão do fuso horário não é suportada para carimbos de tempo nos registos.

>[!NOTE]
> Se houver entradas duplicadas no ficheiro de registo, o Azure Monitor irá recolhê-las. No entanto, os resultados da consulta serão inconsistentes quando os resultados do filtro mostrarem mais eventos do que a contagem de resultados. Será importante que valide o registo para determinar se a aplicação que a cria está a causar este comportamento e endereçá-la se possível antes de criar a definição de recolha de registos personalizado.  
>

>[!NOTE]
> Um espaço de trabalho Log Analytics suporta os seguintes limites:
> 
> * Apenas 500 registos personalizados podem ser criados.
> * Uma tabela só suporta até 500 colunas. 
> * O número máximo de caracteres para o nome da coluna é de 500. 
>

>[!IMPORTANT]
>A recolha de registos personalizados requer que a aplicação que escreve o ficheiro de registo descarregue periodicamente o conteúdo do registo no disco. Isto porque a recolha de registos personalizados baseia-se em notificações de alteração do sistema de ficheiros para o ficheiro de registo que está a ser rastreado.

## <a name="defining-a-custom-log"></a>Definindo um log personalizado
Utilize o seguinte procedimento para definir um ficheiro de registo personalizado.  Percorra até ao final deste artigo para obter uma passagem de uma amostra de adição de um tronco personalizado.

### <a name="step-1-open-the-custom-log-wizard"></a>Passo 1. Abra o Assistente de Registo Personalizado
O Assistente de Registo Personalizado funciona no portal Azure e permite definir um novo registo personalizado para recolher.

1. No portal Azure, selecione **Log Analytics >** espaço de trabalho > **Definições Avançadas**.
2. Clique **Data** em  >  **registos personalizados de dados.**
3. Por predefinição, todas as alterações de configuração são automaticamente empurradas para todos os agentes. Para os agentes Linux, é enviado um ficheiro de configuração para o coletor de dados Fluentd.
4. Clique **em Add+** para abrir o Assistente de Registo Personalizado.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Passo 2. Faça upload e analise um registo de amostra
Começa-se por enviar uma amostra do registo personalizado.  O assistente analisará e apresentará as entradas neste ficheiro para validar.  O Azure Monitor utilizará o declimiter que especifica para identificar cada registo.

**New Line** é o delimiter predefinido e é usado para ficheiros de registo que têm uma única entrada por linha.  Se a linha começar com uma data e hora num dos formatos disponíveis, então pode especificar um delimiter **Timestamp** que suporta entradas que se estendem por mais de uma linha.

Se for utilizado um delimiter de tempo, então a propriedade TimeGenerated de cada registo armazenado no Azure Monitor será povoada com a data/hora especificada para essa entrada no ficheiro de registo.  Se for utilizado um novo delimiter de linha, então o TimeGenerated é preenchido com a data e a hora em que o Azure Monitor recolheu a entrada.

1. Clique **em procurar** e navegue num ficheiro de amostra.  Note que este botão pode ser rotulado **Escolha Arquivo** em alguns navegadores.
2. Clique em **Seguinte**.
3. O Assistente de Registo Personalizado irá carregar o ficheiro e listar os registos que identifica.
4. Altere o declimiter que é usado para identificar um novo registo e selecione o declimiter que melhor identifica os registos no seu ficheiro de registo.
5. Clique em **Seguinte**.

### <a name="step-3-add-log-collection-paths"></a>Passo 3. Adicionar caminhos de recolha de registos
Tem de definir um ou mais caminhos no agente onde pode localizar o registo personalizado.  Pode fornecer um caminho e nome específicos para o ficheiro de registo, ou pode especificar um caminho com um wildcard para o nome. Isto suporta aplicações que criam um novo ficheiro todos os dias ou quando um ficheiro atinge um determinado tamanho. Também pode fornecer vários caminhos para um único ficheiro de registo.

Por exemplo, uma aplicação pode criar um ficheiro de registo todos os dias com a data incluída no nome como em log20100316.txt. Um padrão para tal log pode ser *registar \* .txt* que se aplicaria a qualquer ficheiro de registo após o esquema de nomeação da aplicação.

A tabela seguinte fornece exemplos de padrões válidos para especificar diferentes ficheiros de registo.

| Descrição | Caminho |
|:--- |:--- |
| Todos os ficheiros em *C:\Logs* com extensão .txt no agente windows |C:\Registos \\ \* .txt |
| Todos os ficheiros em *C:\Logs* com um nome a começar com log e uma extensão de .txt no agente do Windows |C:\Logs\log \* .txt |
| Todos os ficheiros em */var/log/auditoria* com .txt extensão do agente Linux |/var/log/audit/*.txt |
| Todos os ficheiros em */var/log/auditoria* com um nome começando com log e uma extensão de .txt no agente Linux |/var/log/audit/log \* .txt |

1. Selecione Windows ou Linux para especificar qual o formato de caminho que está a adicionar.
2. Digite o caminho e clique no **+** botão.
3. Repita o processo para quaisquer caminhos adicionais.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Passo 4: Fornecer um nome e descrição para o log
O nome que especificar será utilizado para o tipo de registo, conforme descrito acima.  Terminará sempre com _CL para distingui-lo como um registo personalizado.

1. Digite um nome para o diário de bordo.  O sufixo **\_ CL** é fornecido automaticamente.
2. Adicione uma **descrição** opcional .
3. Clique **em Seguinte** para guardar a definição de registo personalizado.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Passo 5. Validar que os registos personalizados estão a ser recolhidos
Pode levar até uma hora para que os dados iniciais de um novo registo personalizado apareçam no Azure Monitor.  Começará a recolher entradas dos registos encontrados no caminho especificado a partir do ponto em que definiu o registo personalizado.  Não irá reter as entradas que carregou durante a criação de registos personalizados, mas irá recolher as entradas já existentes nos ficheiros de registo que localiza.

Assim que o Azure Monitor começar a recolher a partir do registo personalizado, os seus registos estarão disponíveis com uma consulta de registo.  Use o nome que deu ao registo personalizado como **tipo** na sua consulta.

> [!NOTE]
> Se faltar a propriedade RawData da consulta, poderá ter de fechar e reabrir o seu navegador.

### <a name="step-6-parse-the-custom-log-entries"></a>Passo 6. Analise as entradas de registo personalizado
Toda a entrada de registo será armazenada numa única propriedade chamada **RawData**.  Provavelmente irá querer separar as diferentes peças de informação em cada entrada em propriedades individuais para cada registo. Consulte os [dados de texto da Parse no Azure Monitor](../log-query/parse-text.md) para obter opções sobre a análise **do RawData** em várias propriedades.

## <a name="removing-a-custom-log"></a>Remoção de um tronco personalizado
Utilize o seguinte processo no portal Azure para remover um registo personalizado que definiu anteriormente.

1. A partir do menu **Dados** nas **Definições Avançadas** para o seu espaço de trabalho, selecione **Registos Personalizados** para listar todos os seus registos personalizados.
2. Clique em **Remover** ao lado do registo personalizado para remover.

## <a name="data-collection"></a>Recolha de dados
O Azure Monitor recolherá novas entradas de cada registo personalizado aproximadamente a cada 5 minutos.  O agente gravará o seu lugar em cada ficheiro de registo que recolhe.  Se o agente ficar offline por um período de tempo, então o Azure Monitor recolherá as entradas de onde ficou pela última vez, mesmo que essas entradas tenham sido criadas enquanto o agente estava offline.

Todo o conteúdo da entrada de registo é escrito numa única propriedade chamada **RawData**.  Consulte [os dados de texto parse no Azure Monitor](../log-query/parse-text.md) para obter métodos para analisar cada entrada de registo importado em várias propriedades.

## <a name="custom-log-record-properties"></a>Propriedades de registo de registo de registo personalizado
Os registos de registos personalizados têm um tipo com o nome de registo que fornece e as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated |Data e hora em que o registo foi recolhido pelo Azure Monitor.  Se o registo utilizar um delimiter baseado no tempo, então este é o tempo recolhido da entrada. |
| SourceSystem |Tipo de agente de onde o registo foi recolhido. <br> OpsManager - Agente windows, ou conectar diretamente ou Gestor de Operações do Centro de Sistema <br> Linux - Todos os agentes Linux |
| RawData |Texto completo da entrada recolhida. Provavelmente irá querer [analisar estes dados em propriedades individuais.](../log-query/parse-text.md) |
| ManagementGroupName |Nome do grupo de gestão dos agentes de gestão de operações do System Center.  Para outros agentes, este é o AOI...\<workspace ID\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Experimente a passagem de um log personalizado
A secção seguinte percorre um exemplo de criação de um registo personalizado.  O registo de amostras que está a ser recolhido tem uma única entrada em cada linha a começar com uma data e hora e, em seguida, campos delimitados por vírgula para código, estado e mensagem.  Várias entradas de amostra são mostradas abaixo.

```output
2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2019-08-27 01:38:22 302,Error,Application could not connect to database
2019-08-27 01:31:34 303,Error,Application lost connection to database
```

### <a name="upload-and-parse-a-sample-log"></a>Faça upload e analise um registo de amostra
Fornecemos um dos ficheiros de registo e podemos ver os eventos que irá recolher.  Neste caso, a New Line é um delimiter suficiente.  Se uma única entrada no registo pode abranger várias linhas, então um delimiter de tempo teria de ser usado.

![Faça upload e analise um registo de amostra](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Adicionar caminhos de recolha de registos
Os ficheiros de registo serão localizados em *C:\MyApp\Logs*.  Um novo ficheiro será criado todos os dias com um nome que inclui a data na *app padrãoYYYYMMDD.log*.  Um padrão suficiente para este registo seria *C:\MyApp\Logs \\ \* .log*.

![Caminho de recolha de registos](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Fornecer um nome e descrição para o log
Utilizamos um nome de *MyApp_CL* e escrevemos numa **Descrição.**

![Nome do registo](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Validar que os registos personalizados estão a ser recolhidos
Usamos uma simples consulta de *MyApp_CL* para devolver todos os registos do registo recolhido.

![Consulta de registo sem campos personalizados](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternativas aos registos personalizados
Embora os registos personalizados sejam úteis se os seus dados se enquadrarem nos critérios acima indicados, existem casos como o seguinte em que precisa de outra estratégia:

- Os dados não se enquadram na estrutura necessária, como ter a hora de pontuação num formato diferente.
- O ficheiro de registo não adere a requisitos como codificação de ficheiros ou estrutura de pasta não suportada.
- Os dados requerem pré-processamento ou filtragem antes da recolha. 

Nos casos em que os seus dados não possam ser recolhidos com registos personalizados, considere as seguintes estratégias alternativas:

- Utilize um script personalizado ou outro método para escrever dados para [Eventos do Windows](data-sources-windows-events.md) ou [Syslog](data-sources-syslog.md) que são recolhidos pelo Azure Monitor. 
- Envie os dados diretamente para o Azure Monitor utilizando [a API do Colecionador de Dados HTTP](data-collector-api.md). 

## <a name="next-steps"></a>Passos seguintes
* Consulte [os dados de texto parse no Azure Monitor](../log-query/parse-text.md) para obter métodos para analisar cada entrada de registo importado em várias propriedades.
* Saiba mais [sobre consultas de registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções.
