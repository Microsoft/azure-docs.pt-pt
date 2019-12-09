---
title: Notas de lançamento do Gateway de Gestão de Dados
description: Notas de versão do Tory gateway Gerenciamento de Dados
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 9d0e31a89494477e048c7a2f9f7b8165e08d1a2f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924263"
---
# <a name="release-notes-for-data-management-gateway"></a>Notas de lançamento do Gateway de Gestão de Dados
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [tempo de execução de integração auto-hospedado em v2](../create-self-hosted-integration-runtime.md).

Um dos desafios para a integração de dados moderna é mover dados de e para o local para a nuvem. Data Factory torna essa integração com o gateway de Gerenciamento de Dados, que é um agente que você pode instalar localmente para habilitar a movimentação de dados híbridos.

Consulte os artigos a seguir para obter informações detalhadas sobre Gerenciamento de Dados gateway e como usá-lo:

*  [Data Management Gateway](data-factory-data-management-gateway.md)
*  [Mover dados entre o local e a nuvem usando Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>VERSÃO ATUAL 
Não mantemos mais as notas de versão aqui. Obtenha as notas de versão mais recentes [aqui](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Versões anteriores
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Aprimoramentos
- Pode adicionar entradas DNS para o barramento de serviço de lista de permissões em vez de listas de permissões de todos os endereços IP do Azure da sua firewall (se necessário). Você pode encontrar a respectiva entrada DNS em portal do Azure (Data Factory-> ' criar e implantar '-> ' gateways '-> "perurls" (em JSON)
- O conector HDFS agora dá suporte ao certificado público autoassinado, permitindo que você ignore a validação SSL.
- Corrigido: problema com o gateway offline durante a atualização (devido à distorção do relógio)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Aprimoramentos
-   Você pode adicionar entradas DNS ao barramento de serviço da lista de permissões em vez de colocar em lista de permissões todos os endereços IP do Azure do firewall (se necessário). Mais detalhes aqui.
-   Agora você pode copiar dados de/para um único blob de blocos até 4,75 TB, que é o tamanho máximo com suporte do blob de blocos. (o limite anterior era 195 GB).
-   Corrigido: problema de memória insuficiente ao descompactar vários arquivos pequenos durante a atividade de cópia.
-   Corrigido: problema de índice fora do intervalo ao copiar do BD de documento para um SQL Server local com o recurso Idempotência.
-   Corrigido: o script de limpeza do SQL não funciona com SQL Server locais do assistente de cópia.
-   Corrigido: o nome da coluna com espaço no final não funciona na atividade de cópia.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Aprimoramentos
- Corrigido: problema com credenciais ausentes na reinicialização do computador do gateway.
- Corrigido: problema com o registro durante a restauração do gateway usando um arquivo de backup.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Aprimoramentos
- Corrigido: leitura incorreta do valor nulo decimal do Oracle como fonte.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Novidades
- Os clientes podem fornecer comentários sobre a experiência de registro do gateway.
- Suporte a um novo formato de compactação: ZIP (desinflar)

### <a name="enhancements-"></a>Aprimoramentos
- Melhoria de desempenho para o coletor Oracle, origem do HDFS.
- Correção de bug para atualização automática do gateway, capacidade de processamento paralelo do gateway.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Aprimoramentos
- Experiência de registro de gateway aprimorada e mais robusta-agora você pode acompanhar o status de progresso durante o processo de registro do gateway, o que torna a experiência de registro mais responsiva.
- Melhoria no processo de restauração do gateway-você ainda pode recuperar o gateway mesmo se não tiver o arquivo de backup do gateway com essa atualização. Isso exigiria que você redefina as credenciais do serviço vinculado no Portal.
- Correção de bug.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Novidades

- Agora você pode armazenar as credenciais de fonte de dados localmente. As credenciais são criptografadas. As credenciais da fonte de dados podem ser recuperadas e restauradas usando o arquivo de backup que pode ser exportado do gateway existente, tudo no local.

### <a name="enhancements-"></a>Aprimoramentos

- Experiência de registro de gateway aprimorada e mais robusta.
- Dê suporte à detecção automática da configuração do QuoteChar para o formato de texto no assistente de cópia e melhore a precisão da detecção de formato geral.

## <a name="2361002"></a>2.3.6100.2

- Suporte para detecção automática de firstRowAsHeader e SkipLineCount no assistente de cópia para arquivos de texto no sistema de arquivos local e no HDFS.
- Aprimore a estabilidade da conexão de rede entre o gateway e o barramento de serviço
- Algumas correções de bugs


## <a name="2260721"></a>2.2.6072.1

*  Dá suporte à configuração do proxy HTTP para o gateway usando o Configuration Manager de gateway. Se configurado, BLOB do Azure, tabela do Azure, Azure Data Lake e o banco de documentos do documento são acessados por proxy HTTP.
*  Dá suporte ao tratamento de cabeçalho para TextFormat ao copiar dados de/para blob do Azure, Azure Data Lake Store, sistema de arquivos local e HDFS local.
*  Dá suporte à cópia de dados do blob de acréscimo e do blob de página junto com o blob de blocos já suportado.
*  Apresenta um novo status de gateway **online (limitado)** , que indica que a funcionalidade principal do gateway funciona, exceto o suporte de operação interativa para o assistente de cópia.
*  Aprimora a robustez do registro de gateway usando a chave de registro.

## <a name="216040"></a>2.1.6040.

*  O driver do DB2 está incluído no pacote de instalação do gateway agora. Não é necessário instalá-lo em separado.
*  O driver DB2 agora dá suporte a z/OS e DB2 para i (AS/400) junto com as plataformas já suportadas (Linux, UNIX e Windows).
*  Dá suporte ao uso de Azure Cosmos DB como origem ou destino para armazenamentos de dados locais
*  O oferece suporte à cópia de dados de/para o armazenamento de BLOBs frio/quente junto com a conta de armazenamento de uso geral já suportada.
*  Permite que você se conecte a SQL Server locais por meio de gateway com privilégios de logon remoto.  

## <a name="2060131"></a>2.0.6013.1

*  Você pode selecionar o idioma/cultura a ser usado por um gateway durante a instalação manual.

*  Quando o gateway não funciona conforme o esperado, você pode optar por enviar logs de gateway dos últimos sete dias para a Microsoft para facilitar a solução do problema. Se o gateway não estiver conectado ao serviço de nuvem, você poderá optar por salvar e arquivar os logs de gateway.  

*  Aprimoramentos da interface do usuário para o Gerenciador de configurações de gateway:

    *  Torne o status do gateway mais visível na guia página inicial.

    *  Controles reorganizados e simplificados.

    *  Você pode copiar dados de um armazenamento usando a [ferramenta de cópia sem código](data-factory-copy-data-wizard-tutorial.md). Confira [cópia em etapas](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre esse recurso em geral.
*  Você pode usar Gerenciamento de Dados gateway para entrada de dados diretamente de um banco de SQL Server local no Azure Machine Learning.

*  Melhorias no desempenho

    * Melhore o desempenho na exibição de esquema/visualização em relação a SQL Server na ferramenta de cópia sem código.

## <a name="11259531"></a>1.12.5953.1

*  Correções de erros

## <a name="11159181"></a>1.11.5918.1

*  O tamanho máximo do log de eventos do gateway aumentou de 1 MB para 40 MB.

*  Uma caixa de diálogo de aviso é exibida no caso de uma reinicialização ser necessária durante a atualização automática do gateway. Você pode optar por reiniciar logo em seguida ou mais tarde.

*  Caso a atualização automática falhe, o instalador do gateway tentará atualizar automaticamente três vezes no máximo.

*  Melhorias no desempenho

    * Melhore o desempenho para carregar grandes tabelas do servidor local no cenário de cópia sem código.

*  Correções de erros

## <a name="11058921"></a>1.10.5892.1

*  Melhorias no desempenho

*  Correções de erros

## <a name="1958652"></a>1.9.5865.2

*  Recurso de atualização automática de toque zero
*  Novo ícone de bandeja com indicadores de status do gateway
*  Capacidade de "atualizar agora" do cliente
*  Capacidade de definir a hora da agenda de atualização
*  Script do PowerShell para ativar/desativar a atualização automática
*  Suporte para o formato JSON  
*  Melhorias no desempenho
*  Correções de erros

## <a name="1858221"></a>1.8.5822.1

*  Melhorar a experiência de solução de problemas
*  Melhorias no desempenho
*  Correções de erros

### <a name="1757951"></a>1.7.5795.1

*  Melhorias no desempenho
*  Correções de erros

### <a name="1757641"></a>1.7.5764.1

*  Melhorias no desempenho
*  Correções de erros

### <a name="1657351"></a>1.6.5735.1

*  Suporte à fonte/coletor do HDFS local
*  Melhorias no desempenho
*  Correções de erros

### <a name="1656961"></a>1.6.5696.1

*  Melhorias no desempenho
*  Correções de erros

### <a name="1656761"></a>1.6.5676.1

*  Suporte para ferramentas de diagnóstico no Configuration Manager
*  Suporte a colunas da tabela para fontes de dados tabulares para Azure Data Factory
*  Suporte ao SQL DW para Azure Data Factory
*  Suporte a Reclusivo em Blobname e filename para Azure Data Factory
*  Suporte a CopyBehavior – MergeFiles, PreserveHierarchy e FlattenHierarchy em BlobSink e filesink com cópia binária para Azure Data Factory
*  Suporte ao progresso de relatórios de atividades de cópia para Azure Data Factory
*  Suporte à validação de conectividade da fonte de dados para Azure Data Factory
*  Correções de erros

### <a name="1656721"></a>1.6.5672.1

*  Nome da tabela de suporte para fonte de dados ODBC para Azure Data Factory
*  Melhorias no desempenho
*  Correções de erros

### <a name="1656581"></a>1.6.5658.1

*  Suporte ao coletor de arquivos para Azure Data Factory
*  Suporte para preservar a hierarquia na cópia binária para Azure Data Factory
*  Suporte para Idempotência de atividade de cópia para Azure Data Factory
*  Correções de erros

### <a name="1656401"></a>1.6.5640.1

*  Suporte a mais 3 fontes de dados para Azure Data Factory (ODBC, OData, HDFS)
*  Suporte ao caractere de aspas no analisador de CSV para Azure Data Factory
*  Suporte à compactação (BZip2)
*  Correções de erros

### <a name="1556121"></a>1.5.5612.1

*  Suporte a cinco bancos de dados relacionais para Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata e Sybase)
*  Suporte à compactação (gzip e deflate)
*  Melhorias no desempenho
*  Correções de erros

### <a name="1455491"></a>1.4.5549.1

*  Adicionar suporte à fonte de dados Oracle para Azure Data Factory
*  Melhorias no desempenho
*  Correções de erros

### <a name="1454921"></a>1.4.5492.1

*  Binário unificado que dá suporte a Microsoft Azure Data Factory e serviços de Power BI do Office 365
*  Refinar a interface do usuário de configuração e o processo de registro
*  Azure Data Factory – suporte de entrada e saída do Azure para SQL Server fonte de dados

### <a name="1253031"></a>1.2.5303.1

*  Corrija o problema de tempo limite para dar suporte a mais conexões de fonte de dados demoradas.

### <a name="1155268"></a>1.1.5526.8

*  Requer o .NET Framework 4.5.1 como um pré-requisito durante a instalação.

### <a name="1051442"></a>1.0.5144.2

*  Nenhuma alteração que afete Azure Data Factory cenários.
