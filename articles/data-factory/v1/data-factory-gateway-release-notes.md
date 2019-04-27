---
title: Notas de versão do Data Management Gateway | Documentos da Microsoft
description: Notas de versão do Data Management Gateway partir
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 865bfdae199bca7ebee888be527db239d34511d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60486469"
---
# <a name="release-notes-for-data-management-gateway"></a>Notas de lançamento do Gateway de Gestão de Dados
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [Self-hosted integration runtime no V2](../create-self-hosted-integration-runtime.md).

Um dos desafios para a integração de dados moderno é mover dados para e do local para a cloud. Fábrica de dados torna esta integração com o Data Management Gateway, que é um agente que pode instalar no local para permitir o movimento de dados híbrida.

Veja os artigos seguintes para obter informações detalhadas sobre o Gateway de gestão de dados e como usá-lo:

*  [Data Management Gateway](data-factory-data-management-gateway.md)
*  [Mover dados entre locais e na cloud com o Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>VERSÃO ATUAL 
Mantemos não mais aqui as notas de versão. Obter notas de versão mais recente [aqui](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Versões anteriores
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Aprimoramentos de-
- Pode adicionar entradas DNS para o barramento de serviço de lista branca em vez de listas de permissões de todos os endereços IP do Azure da sua firewall (se necessário). Encontrará a respetiva entrada DNS no portal do Azure (Data Factory -> "Criar e implementar" -> "Gateways" -> "serviceurls válida" (em JSON)
- Conector HDFS suporta agora o certificado público autoassinado, permitindo-lhe ignorar a validação de SSL.
- Corrigido: Problema com o gateway offline durante a atualização (devido a distorção)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Aprimoramentos de-
-   Pode adicionar entradas DNS à lista de permissões do Service Bus, em vez de listas de permissões de todos os endereços IP do Azure da sua firewall (se necessário). Obter mais detalhes aqui.
-   Pode agora até 4.75 TB, o que é o tamanho máximo suportado de blob de blocos de BLOBs de cópia de dados de/para um único bloco. (limite anterior era 195 GB).
-   Corrigido: Fora do problema de memória ao deszipar vários arquivos pequenos durante a atividade de cópia.
-   Corrigido: Índice fora do problema de intervalo ao copiar do Document DB, a um servidor de SQL no local com a funcionalidade de idempotência.
-   Corrigido: Script de limpeza SQL não funciona com o servidor de SQL no local do Assistente para copiar.
-   Corrigido: Nome da coluna com espaço no final não funciona na atividade de cópia.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Aprimoramentos de-
- Corrigido: Problema com a falta de credenciais no reinício da máquina de gateway.
- Corrigido: Problema com o registo durante o gateway de restauro através de um ficheiro de cópia de segurança.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Aprimoramentos de-
- Corrigido: Leitura incorreta de valor nulo Decimal do Oracle como origem.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Novidades
- Os clientes podem fornecer comentários sobre a registar a experiência de gateway.
- Suporte um novo formato de compressão: ZIP (Deflate)

### <a name="enhancements-"></a>Aprimoramentos de-
- Melhoria de desempenho para Oracle Sink, origem HDFS.
- Correção de erros para automática do gateway de atualização, capacidade de processamento paralelo de gateway.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Aprimoramentos
- Melhorada e mais robusto Gateway registo experiência-agora pode controlar o estado do progresso durante o processo de registo do Gateway, o que faz com que o registo experiência mais responsivos.
- Melhoria no Gateway restaurar processo - ainda pode recuperar o gateway, mesmo se não tiver o ficheiro de cópia de segurança de gateway com esta atualização. Isso exigiria a repor as credenciais do serviço ligado no Portal.
- Correção de erros.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Novidades

- Agora pode armazenar as credenciais da origem de dados localmente. As credenciais são encriptadas. As credenciais da origem de dados podem ser recuperadas e restaurados com o ficheiro de cópia de segurança que pode ser exportado a partir do Gateway existente, todos os locais.

### <a name="enhancements-"></a>Aprimoramentos de-

- Experiência de registo de Gateway maior e mais robusta.
- Suporta a deteção automática de configuração de QuoteChar para formato de texto no Assistente para copiar e melhorar a precisão de deteção de formato geral.

## <a name="2361002"></a>2.3.6100.2

- Suporte firstRowAsHeader e SkipLineCount a deteção automática no Assistente de cópia de ficheiros de texto no sistema de ficheiros no local e no HDFS.
- Melhorar a estabilidade da ligação de rede entre o gateway e o Service Bus
- Algumas correções de erros


## <a name="2260721"></a>2.2.6072.1

*  Suporta a definição do proxy HTTP para o gateway com o Gestor de configuração do Gateway. Se configurado, os BLOBs do Azure, tabelas do Azure, Azure Data Lake e Document DB são acedidos através do proxy HTTP.
*  Cabeçalho de suporta manipulação para TextFormat quando se copiam dados de/para Blob do Azure, Azure Data Lake Store, sistema de ficheiros no local e no local HDFS.
*  Suporta a cópia de dados do Blob de acréscimo e BLOBs de páginas, juntamente com o Blob de blocos já suportadas.
*  Apresenta um novo estado do gateway **Online (limitado)**, que indica que a funcionalidade principal do gateway funcione, exceto o suporte de operação interativo para o Assistente para copiar.
*  Melhora a robustez do Registro de gateway utilizando a chave de registo.

## <a name="216040"></a>2.1.6040.

*  Driver de DB2 agora está incluído no pacote de instalação de gateway. Não é necessário instalá-lo em separado.
*  Driver de DB2 suporta agora z/OS e DB2 para eu (AS as/400), juntamente com as plataformas suportadas já (Linux, Unix e Windows).
*  Suporta a utilização do Azure Cosmos DB como uma origem ou de destino para arquivos de dados no local
*  Suporta a cópia de dados de/para frequente/esporádico BLOBs de armazenamento juntamente com a conta de armazenamento para fins gerais já suportadas.
*  Permite-lhe ligar ao servidor de SQL no local através do gateway com privilégios de início de sessão remoto.  

## <a name="2060131"></a>2.0.6013.1

*  Pode selecionar o idioma/cultura a ser utilizado por um gateway durante a instalação manual.

*  Quando o gateway não funciona conforme esperado, pode optar por enviar registos do gateway dos últimos sete dias para a Microsoft para facilitar a resolução de problemas do problema. Se o gateway não está ligado ao serviço cloud, pode optar por guardar e arquivar registos do gateway.  

*  Aprimoramentos da interface de utilizador para o Gestor de configuração do gateway:

    *  Verifique o estado do gateway mais visível no separador base.

    *  Controles reorganizados e simplificadas.

    *  Pode copiar dados a partir de um armazenamento com o [ferramenta de cópia sem código](data-factory-copy-data-wizard-tutorial.md). Ver [cópia faseada](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre esta funcionalidade em geral.
*  Pode utilizar o Gateway de gestão de dados para dados de entrada diretamente a partir de uma base de dados do SQL Server no local no Azure Machine Learning.

*  Melhorias de desempenho

    * Melhore o desempenho sobre a visualização de esquema/pré-visualização no SQL Server na ferramenta de cópia sem código.

## <a name="11259531"></a>1.12.5953.1

*  Correções de erros

## <a name="11159181"></a>1.11.5918.1

*  Tamanho máximo do registo de eventos de gateway foi aumentado de 1 MB para 40 MB.

*  Uma caixa de diálogo de aviso é apresentada no caso de um reinício é necessário durante a atualização automática do gateway. Pode optar por reiniciar à direita, em seguida, ou posterior.

*  No caso de falha de atualização automática, o instalador do gateway repete a atualização automática três vezes no máximo.

*  Melhorias de desempenho

    * Melhore o desempenho para carregar tabelas grandes a partir do servidor no local, num cenário de cópia sem código.

*  Correções de erros

## <a name="11058921"></a>1.10.5892.1

*  Melhorias de desempenho

*  Correções de erros

## <a name="1958652"></a>1.9.5865.2

*  Recurso de atualização de automática zero touch
*  Novo ícone de Bandeja com indicadores de estado do gateway
*  Capacidade de "Atualizar agora" do cliente
*  Capacidade de definir a hora de agendamento de atualização
*  Script do PowerShell para ativar/desativar a atualização automática
*  Suporte para o formato JSON  
*  Melhorias de desempenho
*  Correções de erros

## <a name="1858221"></a>1.8.5822.1

*  Melhorar a experiência de resolução de problemas
*  Melhorias de desempenho
*  Correções de erros

### <a name="1757951"></a>1.7.5795.1

*  Melhorias de desempenho
*  Correções de erros

### <a name="1757641"></a>1.7.5764.1

*  Melhorias de desempenho
*  Correções de erros

### <a name="1657351"></a>1.6.5735.1

*  Suporte no local HDFS origem/Sink
*  Melhorias de desempenho
*  Correções de erros

### <a name="1656961"></a>1.6.5696.1

*  Melhorias de desempenho
*  Correções de erros

### <a name="1656761"></a>1.6.5676.1

*  Ferramentas de diagnóstico de suporte sobre o Configuration Manager
*  Colunas da tabela de suporte para origens de dados em tabela do Azure Data Factory
*  Suporte do SQL DW a fábrica de dados do Azure
*  Suporte Reclusive no BlobSource e FileSource fábrica de dados do Azure
*  Suporte CopyBehavior – MergeFiles PreserveHierarchy e FlattenHierarchy em BlobSink e FileSink com cópia binária para a fábrica de dados do Azure
*  Suporte a atividade de cópia, relatórios de andamento do Azure Data Factory
*  Validação da conectividade de origem de dados de suporte do Azure Data Factory
*  Correções de erros

### <a name="1656721"></a>1.6.5672.1

*  Nome da tabela de suporte para a origem de dados ODBC do Azure Data Factory
*  Melhorias de desempenho
*  Correções de erros

### <a name="1656581"></a>1.6.5658.1

*  Ficheiro de suporte de Sink para a fábrica de dados do Azure
*  Suporte a preservação de hierarquia na cópia binária do Azure Data Factory
*  Suporte Idempotência de atividade de cópia para a fábrica de dados do Azure
*  Correções de erros

### <a name="1656401"></a>1.6.5640.1

*  3 mais origens de dados de suporte do Azure Data Factory (ODBC, OData, HDFS)
*  Suporte caráter de plica no analisador csv para o Azure Data Factory
*  Suporte de compactação (BZip2)
*  Correções de erros

### <a name="1556121"></a>1.5.5612.1

*  Suporte a cinco bases de dados relacionais para o Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata e Sybase)
*  Suporte de compactação (Gzip e Deflate)
*  Melhorias de desempenho
*  Correções de erros

### <a name="1455491"></a>1.4.5549.1

*  Adicionar suporte de origem de dados Oracle para o Azure Data Factory
*  Melhorias de desempenho
*  Correções de erros

### <a name="1454921"></a>1.4.5492.1

*  Binário unificado que ofereça suporte a serviços do Microsoft Azure Data Factory e Power BI do Office 365
*  Refine o processo de IU de configuração e registo
*  O Azure Data Factory – suportam a entrada do Azure e de saída para a origem de dados do SQL Server

### <a name="1253031"></a>1.2.5303.1

*  Corrigi o problema de tempo limite para suportar mais ligações de origem de dados demorado.

### <a name="1155268"></a>1.1.5526.8

*  Requer o .NET Framework 4.5.1 como pré-requisito durante a configuração.

### <a name="1051442"></a>1.0.5144.2

*  Não existem alterações que afetam os cenários do Azure Data Factory.
