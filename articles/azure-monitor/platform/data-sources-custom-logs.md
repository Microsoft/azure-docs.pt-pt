---
title: Coletar logs personalizados em Azure Monitor | Microsoft Docs
description: Azure Monitor pode coletar eventos de arquivos de texto em computadores Windows e Linux.  Este artigo descreve como definir um novo log personalizado e detalhes dos registros que eles criam no Azure Monitor.
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
ms.date: 09/26/2019
ms.author: bwren
ms.openlocfilehash: 957df2d03352756c74a5450de240afde2615e50b
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177622"
---
# <a name="custom-logs-in-azure-monitor"></a>Logs personalizados no Azure Monitor

A fonte de dados de logs personalizados no Azure Monitor permite que você colete eventos de arquivos de texto em computadores Windows e Linux. Muitos aplicativos registram informações em arquivos de texto em vez de serviços de log padrão, como o log de eventos do Windows ou o syslog. Depois de coletados, você pode analisar os dados em campos individuais em suas consultas ou extrair os dados durante a coleta para campos individuais.

![Coleção de logs personalizada](media/data-sources-custom-logs/overview.png)

Os arquivos de log a serem coletados devem corresponder aos critérios a seguir.

- O log deve ter uma única entrada por linha ou usar um carimbo de data/hora correspondente a um dos formatos a seguir no início de cada entrada.

    AAAA-MM-DD HH: MM: SS<br>M/D/AAAA HH: MM: SS AM/PM<br>Mês DD, aaaa HH: MM: SS<br />yyMMdd HH: mm: SS<br />ddMMyy HH: mm: SS<br />DD d hh: mm: SS<br />DD/MMM/AAAA: HH: mm: SS zzz<br />aaaa-MM-ddTHH: mm: ssK

- O arquivo de log não deve permitir log circular ou rotação de log, onde o arquivo é substituído por novas entradas.
- O arquivo de log deve usar a codificação ASCII ou UTF-8.  Não há suporte para outros formatos, como UTF-16.

>[!NOTE]
> Se houver entradas duplicadas no arquivo de log, Azure Monitor as coletará. No entanto, os resultados da consulta serão inconsistentes, onde os resultados do filtro mostrarão mais eventos do que a contagem de resultados. Será importante validar o log para determinar se o aplicativo que o cria está causando esse comportamento e solucioná-lo, se possível, antes de criar a definição da coleção de logs personalizada.  
>

>[!NOTE]
> Um espaço de trabalho Log Analytics dá suporte aos seguintes limites:
> 
> * Apenas 500 logs personalizados podem ser criados.
> * Uma tabela só dá suporte a até 500 colunas. 
> * O número máximo de caracteres para o nome da coluna é 500. 
>

>[!IMPORTANT]
>A coleta de log Personalizada requer que o aplicativo que está gravando o arquivo de log libere o conteúdo do log para o disco periodicamente. Isso ocorre porque a coleção de logs personalizada se baseia em notificações de alteração do sistema de arquivos para o arquivo de log que está sendo acompanhado.

## <a name="defining-a-custom-log"></a>Definindo um log personalizado
Use o procedimento a seguir para definir um arquivo de log personalizado.  Role até o final deste artigo para obter uma explicação de uma amostra de como adicionar um log personalizado.

### <a name="step-1-open-the-custom-log-wizard"></a>Passo 1. Abrir o assistente de log personalizado
O assistente de log personalizado é executado no portal do Azure e permite que você defina um novo log personalizado a ser coletado.

1. No portal do Azure, selecione **log Analytics espaços de trabalho** > seu espaço de trabalho > **Configurações avançadas**.
2. Clique em **dados** > **logs personalizados**.
3. Por predefinição, todas as alterações de configuração são automaticamente enviados por push para todos os agentes. Para agentes do Linux, um arquivo de configuração é enviado para o coletor de dados Fluentd.
4. Clique em **Adicionar +** para abrir o assistente de log personalizado.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Passo 2. Carregar e analisar um log de exemplo
Você começa carregando uma amostra do log personalizado.  O assistente analisará e exibirá as entradas nesse arquivo para você validar.  Azure Monitor usará o delimitador que você especificar para identificar cada registro.

**Nova linha** é o delimitador padrão e é usado para arquivos de log que têm uma única entrada por linha.  Se a linha começar com uma data e hora em um dos formatos disponíveis, você poderá especificar um delimitador de **carimbo de hora** que dá suporte a entradas que abrangem mais de uma linha.

Se um delimitador de carimbo de data/hora for usado, a Propriedade TimeGenerated de cada registro armazenado em Azure Monitor será populada com a data/hora especificada para aquela entrada no arquivo de log.  Se um novo delimitador de linha for usado, TimeGenerated será preenchido com data e hora em que Azure Monitor coletou a entrada.

1. Clique em **procurar** e navegue até um arquivo de exemplo.  Observe que esse botão pode ser rotulado como **escolher arquivo** em alguns navegadores.
2. Clique em **Seguinte**.
3. O assistente de log personalizado carregará o arquivo e listará os registros que ele identifica.
4. Altere o delimitador que é usado para identificar um novo registro e selecione o delimitador que melhor identifica os registros no arquivo de log.
5. Clique em **Seguinte**.

### <a name="step-3-add-log-collection-paths"></a>Passo 3: Adicionar caminhos de coleção de logs
Você deve definir um ou mais caminhos no agente onde ele pode localizar o log personalizado.  Você pode fornecer um caminho e um nome específicos para o arquivo de log, ou pode especificar um caminho com um curinga para o nome. Isso dá suporte a aplicativos que criam um novo arquivo por dia ou quando um arquivo atinge um determinado tamanho. Você também pode fornecer vários caminhos para um único arquivo de log.

Por exemplo, um aplicativo pode criar um arquivo de log todos os dias com a data incluída no nome como em log20100316. txt. Um padrão para tal log pode ser *log\*.txt* que se aplicaria a qualquer arquivo de log após o esquema de nomenclatura do aplicativo.

A tabela a seguir fornece exemplos de padrões válidos para especificar arquivos de log diferentes.

| Descrição | Path |
|:--- |:--- |
| Todos os arquivos em *C:\Logs* com extensão. txt no agente do Windows |C:\Logs\\\*. txt |
| Todos os arquivos em *C:\Logs* com um nome que começa com log e uma extensão. txt no agente do Windows |C:\Logs\log\*.txt |
| Todos os arquivos em */var/log/Audit* com a extensão. txt no agente do Linux |/var/log/audit/*.txt |
| Todos os arquivos em */var/log/Audit* com um nome que começa com log e uma extensão. txt no agente do Linux |/var/log/Audit/log\*.txt |

1. Selecione Windows ou Linux para especificar o formato de caminho que você está adicionando.
2. Digite o caminho e clique no botão **+** .
3. Repita o processo para quaisquer caminhos adicionais.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Passo 4: Forneça um nome e uma descrição para o log
O nome que você especificar será usado para o tipo de log, conforme descrito acima.  Ele sempre terminará com _CL para distingui-lo como um log personalizado.

1. Digite um nome para o log.  O sufixo **\_CL** é fornecido automaticamente.
2. Adicione uma **Descrição**opcional.
3. Clique em **Avançar** para salvar a definição de log Personalizada.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Passo 5: Validar que os logs personalizados estão sendo coletados
Pode levar até uma hora para que os dados iniciais de um novo log personalizado apareçam em Azure Monitor.  Ele começará a coletar entradas dos logs encontrados no caminho especificado a partir do ponto em que você definiu o log personalizado.  Ele não manterá as entradas que você carregou durante a criação do log personalizado, mas coletará entradas já existentes nos arquivos de log que ele localizar.

Depois que Azure Monitor começar a coletar do log personalizado, seus registros estarão disponíveis com uma consulta de log.  Use o nome que você atribuiu ao log personalizado como o **tipo** em sua consulta.

> [!NOTE]
> Se a propriedade RawData estiver ausente da consulta, talvez seja necessário fechar e reabrir o navegador.

### <a name="step-6-parse-the-custom-log-entries"></a>Passo 6. Analisar as entradas de log personalizadas
Toda a entrada de log será armazenada em uma única propriedade chamada **RAWDATA**.  Provavelmente, você desejará separar as diferentes partes de informações em cada entrada em propriedades individuais para cada registro. Consulte [analisar dados de texto em Azure monitor](../log-query/parse-text.md) para obter opções de como analisar **RAWDATA** em várias propriedades.

## <a name="removing-a-custom-log"></a>Removendo um log personalizado
Use o processo a seguir no portal do Azure para remover um log personalizado que você definiu anteriormente.

1. No menu **dados** nas **Configurações avançadas** para seu espaço de trabalho, selecione **logs personalizados** para listar todos os logs personalizados.
2. Clique em **remover** ao lado do log personalizado para remover.

## <a name="data-collection"></a>Recolha de dados
Azure Monitor coletará novas entradas de cada log personalizado aproximadamente a cada 5 minutos.  O agente registrará seu local em cada arquivo de log do qual ele coleta.  Se o agente ficar offline por um período de tempo, Azure Monitor coletará entradas de onde ele parou na última vez, mesmo que essas entradas tenham sido criadas enquanto o agente estava offline.

Todo o conteúdo da entrada de log é gravado em uma única propriedade chamada **RAWDATA**.  Consulte [analisar dados de texto em Azure monitor](../log-query/parse-text.md) para que os métodos analisem cada entrada de log importada em várias propriedades.

## <a name="custom-log-record-properties"></a>Propriedades do registro de log personalizado
Os registros de log personalizados têm um tipo com o nome do log que você fornece e as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated |Data e hora em que o registro foi coletado por Azure Monitor.  Se o log usar um delimitador baseado em tempo, esse será o tempo coletado da entrada. |
| SourceSystem |Tipo de agente do qual o registro foi coletado. <br> OpsManager – agente do Windows, conexão direta ou System Center Operations Manager <br> Linux – todos os agentes do Linux |
| RawData |Texto completo da entrada coletada. Provavelmente, você desejará [analisar esses dados em propriedades individuais](../log-query/parse-text.md). |
| ManagementGroupName |Nome do grupo de gerenciamento para o System Center Operations Manage Agents.  Para outros agentes, é AOI -\<ID da área de trabalho\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Exemplo de explicação de como adicionar um log personalizado
A seção a seguir percorre um exemplo de criação de um log personalizado.  O log de exemplo que está sendo coletado tem uma única entrada em cada linha que começa com uma data e hora e os campos delimitados por vírgula para o código, o status e a mensagem.  Várias entradas de exemplo são mostradas abaixo.

    2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2019-08-27 01:38:22 302,Error,Application could not connect to database
    2019-08-27 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Carregar e analisar um log de exemplo
Fornecemos um dos arquivos de log e podemos ver os eventos que serão coletados.  Nesse caso, nova linha é um delimitador suficiente.  No entanto, se uma única entrada no log puder abranger várias linhas, um delimitador de carimbo de data/hora precisaria ser usado.

![Carregar e analisar um log de exemplo](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Adicionar caminhos de coleção de logs
Os arquivos de log estarão localizados em *C:\MyApp\Logs*.  Um novo arquivo será criado todos os dias com um nome que inclui a data no padrão *appaaaammdd. log*.  Um padrão suficiente para esse log seria *C:\MyApp\Logs @ no__t-1\*.log*.

![Caminho da coleção de logs](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Forneça um nome e uma descrição para o log
Usamos um nome de *MyApp_CL* e digitamos uma **Descrição**.

![Nome do log](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Validar que os logs personalizados estão sendo coletados
Usamos uma consulta simples de *MyApp_CL* para retornar todos os registros do log coletado.

![Consulta de log sem campos personalizados](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternativas para logs personalizados
Embora os logs personalizados sejam úteis se seus dados se ajustarem aos critérios listados, mas houver casos como os seguintes, onde você precisa de outra estratégia:

- Os dados não se ajustam à estrutura necessária, como ter o carimbo de data/hora em um formato diferente.
- O arquivo de log não atende aos requisitos como codificação de arquivo ou uma estrutura de pasta sem suporte.
- Os dados exigem pré-processamento ou filtragem antes da coleta. 

Nos casos em que os dados não podem ser coletados com logs personalizados, considere as seguintes estratégias alternativas:

- Use um script personalizado ou outro método para gravar dados em [eventos do Windows](data-sources-windows-events.md) ou [syslog](data-sources-syslog.md) que são coletados pelo Azure monitor. 
- Envie os dados diretamente para Azure Monitor usando a [API do coletor de dados http](data-collector-api.md). 

## <a name="next-steps"></a>Passos seguintes
* Consulte [analisar dados de texto em Azure monitor](../log-query/parse-text.md) para que os métodos analisem cada entrada de log importada em várias propriedades.
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções.