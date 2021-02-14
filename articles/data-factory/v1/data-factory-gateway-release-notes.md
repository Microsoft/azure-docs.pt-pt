---
title: Notas de lançamento do Gateway de Gestão de Dados
description: Data Management Gateway notas de lançamento tory
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b5bf0c6d6996a67e8b0fec3ec51e31fef8eedf26
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363921"
---
# <a name="release-notes-for-data-management-gateway"></a>Notas de lançamento do Gateway de Gestão de Dados
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [tempo de integração auto-hospedado em V2](../create-self-hosted-integration-runtime.md).

Um dos desafios para a integração moderna de dados é mover dados de e para o local para a nuvem. A Data Factory faz esta integração com o Data Management Gateway, que é um agente que pode instalar no local para permitir o movimento de dados híbridos.

Consulte os seguintes artigos para obter informações detalhadas sobre a Data Management Gateway e como utilizá-la:

*  [Data Management Gateway](data-factory-data-management-gateway.md)
*  [Mover dados entre as instalações e a nuvem usando a Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>VERSÃO ATUAL 
Não mantemos mais as notas de lançamento aqui. Receba as últimas notas de lançamento [aqui](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Versões anteriores
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Melhoramentos...
- Pode adicionar entradas DNS para permitir o autocarro de serviço em vez de permitir todos os endereços IP Azure a partir da sua firewall (se necessário). Pode encontrar a respetiva entrada de DNS no portal Azure (Data Factory -> 'Author and Deploy' -> 'Gateways' -> "serviceUrls" (em JSON)
- O conector HDFS suporta agora o certificado público auto-assinado, permitindo-lhe ignorar a validação TLS.
- Fixo: Problema com gateway offline durante a atualização (devido a distorção do relógio)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Melhoramentos...
-    Pode adicionar entradas DNS para permitir o Service Bus em vez de permitir todos os endereços IP Azure a partir da sua firewall (se necessário). Mais detalhes aqui.
-    Agora pode copiar dados de/para uma única bolha de bloco até 4.75 TB, que é o tamanho máximo suportado da bolha de bloco. (o limite anterior era de 195 GB).
-    Fixo: Fora do problema de memória enquanto abre vários pequenos ficheiros durante a atividade da cópia.
-    Fixo: Índice fora do alcance problema enquanto copia do Documento DB para SQL Server com recurso de idempoteência.
-    Fixo: O script de limpeza SQL não funciona com o SQL Server do Copy Wizard.
-    Fixo: O nome da coluna com espaço no final não funciona na atividade de cópia.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Melhoramentos...
- Fixo: Problema com credenciais em falta no reboot da máquina de gateway.
- Fixo: Emite com registo durante a restauração do gateway utilizando um ficheiro de reserva.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Melhoramentos...
- Fixo: Leitura incorreta do valor nulo decimal da Oráculo como fonte.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Novidades
- Os clientes podem fornecer feedback sobre a experiência de registo de gateways.
- Suporte um novo formato de compressão: ZIP (Deflate)

### <a name="enhancements-"></a>Melhoramentos...
- Melhoria de desempenho para Oracle Sink, fonte HDFS.
- Correção de erro para atualização automática de gateway, capacidade de processamento paralelo gateway.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Melhoramentos 
- Experiência de registo Gateway melhorada e mais robusta- Agora pode acompanhar o estado de progresso durante o processo de registo gateway, o que torna a experiência de registo mais responsiva.
- Melhoramento no Processo de Restauro gateway- Ainda pode recuperar gateway mesmo que não tenha o ficheiro de backup gateway com esta atualização. Isto exigiria que reiniciasse as credenciais de Serviço Linked no Portal.
- Correção de insetos.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Novidades

- Agora pode armazenar credenciais de origem de dados localmente. As credenciais estão encriptadas. As credenciais de origem de dados podem ser recuperadas e restauradas usando o ficheiro de backup que pode ser exportado a partir do Gateway existente, todos no local.

### <a name="enhancements-"></a>Melhoramentos...

- Experiência de registo Gateway melhorada e mais robusta.
- Suporte a deteção automática da configuração QuoteChar para formato de texto no assistente de cópia e melhore a precisão geral da deteção do formato.

## <a name="2361002"></a>2.3.6100.2

- Suporte primeiro a deteção automática de PrimeiroRowAsHeader e SkipLineCount no assistente de cópia para ficheiros de texto no sistema de ficheiros no local e HDFS.
- Aumentar a estabilidade da ligação de rede entre gateway e Service Bus
- Algumas correções de bugs


## <a name="2260721"></a>2.2.6072.1

*  Suporta a definição de procuração HTTP para o gateway utilizando o Gestor de Configuração Gateway. Se configurado, Azure Blob, Azure Table, Azure Data Lake e Document DB são acedidos através de http proxy.
*  Suporta o manuseamento do cabeçalho para TextFormat ao copiar dados de/para Azure Blob, Azure Data Lake Store, no local do Sistema de Ficheiros e no local HDFS.
*  Suporta a cópia de dados de Append Blob e Page Blob juntamente com o Bloco Blob já suportado.
*  Introduz um novo estado de gateway **Online (Limited)**, o que indica que a principal funcionalidade do gateway funciona, exceto o suporte de operação interativa para Copy Wizard.
*  Aumenta a robustez do registo de gateway utilizando a chave de registo.

## <a name="216040"></a>2.1.6040.

*  O controlador DB2 está incluído no pacote de instalação gateway agora. Não é necessário instalá-lo em separado.
*  O controlador DB2 suporta agora z/OS e DB2 para i (AS/400) juntamente com as plataformas já suportadas (Linux, Unix e Windows).
*  Suportes usando Azure Cosmos DB como fonte ou destino para lojas de dados no local
*  Suporta a cópia de dados de/para o armazenamento de bolhas frias/quentes, juntamente com a conta de armazenamento de uso geral já suportada.
*  Permite-lhe ligar-se ao SQL Server através de gateway com privilégios de login remotos.  

## <a name="2060131"></a>2.0.6013.1

*  Pode selecionar o idioma/cultura a utilizar por um gateway durante a instalação manual.

*  Quando o gateway não funcionar como esperado, pode optar por enviar registos de gateway dos últimos sete dias para a Microsoft para facilitar a resolução de problemas do problema. Se o gateway não estiver ligado ao serviço de nuvem, pode optar por guardar e arquivar registos de gateway.  

*  Melhorias na interface do utilizador para o gestor de configuração gateway:

    *  Torne o estado do gateway mais visível no separador Casa.

    *  Controlos reorganizados e simplificados.

    *  Pode copiar dados de um armazenamento utilizando a [ferramenta de cópia sem código.](data-factory-copy-data-wizard-tutorial.md) Consulte [a Cópia Encenada](data-factory-copy-activity-performance.md#staged-copy) para obter mais detalhes sobre esta funcionalidade em geral.
*  Pode utilizar o Gateway de Gestão de Dados para entrar nos dados diretamente de uma base de dados do SQL Server para a Azure Machine Learning.

*  Melhorias de desempenho

    * Melhorar o desempenho na visualização de Schema/Pré-visualização contra o SQL Server na ferramenta de cópia sem código.

## <a name="11259531"></a>1.12.5953.1

*  Correções de erros

## <a name="11159181"></a>1.11.5918.1

*  O tamanho máximo do registo de eventos de gateway foi aumentado de 1 MB para 40 MB.

*  É apresentado um diálogo de aviso no caso de ser necessário reiniciar durante a atualização automática do gateway. Pode optar por reiniciar imediatamente ou mais tarde.

*  Caso a atualização automática falhe, o instalador gateway re-actualização automática três vezes no máximo.

*  Melhorias de desempenho

    * Melhore o desempenho para carregar grandes tabelas a partir do servidor no local em cenário de cópia sem código.

*  Correções de erros

## <a name="11058921"></a>1.10.5892.1

*  Melhorias de desempenho

*  Correções de erros

## <a name="1958652"></a>1.9.5865.2

*  Capacidade de atualização automática de toque zero
*  Novo ícone de bandeja com indicadores de estado do gateway
*  Capacidade de "Atualizar agora" do cliente
*  Capacidade de definir o horário de atualização
*  Script PowerShell para toggling auto-update on/off
*  Suporte para formato JSON  
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

*  Suporte no local HDFS Fonte/Pia
*  Melhorias de desempenho
*  Correções de erros

### <a name="1656961"></a>1.6.5696.1

*  Melhorias de desempenho
*  Correções de erros

### <a name="1656761"></a>1.6.5676.1

*  Apoiar ferramentas de diagnóstico no Gestor de Configuração
*  Apoiar colunas de tabelas para fontes de dados tabulares para a Azure Data Factory
*  Apoiar a Azure Synapse Analytics para a Azure Data Factory
*  Suporte Recluso em BlobSource e FileSource para Azure Data Factory
*  Cópia de suporteBehavior – MergeFiles, PreserveHierarchy e FlattenHierarchy in BlobSink and FileSink with Binary Copy for Azure Data Factory
*  Atividade de cópia de suporte reportando progressos para Azure Data Factory
*  Validação da conectividade da fonte de dados de suporte para a fábrica de dados Azure
*  Correções de erros

### <a name="1656721"></a>1.6.5672.1

*  Nome da tabela de suporte para fonte de dados ODBC para Azure Data Factory
*  Melhorias de desempenho
*  Correções de erros

### <a name="1656581"></a>1.6.5658.1

*  Lavatório de ficheiros de suporte para Azure Data Factory
*  Apoiar a preservação da hierarquia na cópia binária para a Azure Data Factory
*  Idempotency de Cópia de Suporte para Fábrica de Dados Azure
*  Correções de erros

### <a name="1656401"></a>1.6.5640.1

*  Suporte mais 3 fontes de dados para a Azure Data Factory (ODBC, OData, HDFS)
*  Personagem de citação de suporte em csv parser para Azure Data Factory
*  Suporte à compressão (BZip2)
*  Correções de erros

### <a name="1556121"></a>1.5.5612.1

*  Suporte cinco bases de dados relacionais para a Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata e Sybase)
*  Suporte à compressão (Gzip e Deflate)
*  Melhorias de desempenho
*  Correções de erros

### <a name="1455491"></a>1.4.5549.1

*  Adicione suporte de fonte de dados da Oracle para a Azure Data Factory
*  Melhorias de desempenho
*  Correções de erros

### <a name="1454921"></a>1.4.5492.1

*  Binário unificado que suporta tanto a Microsoft Azure Data Factory como os serviços power bi do Office 365
*  Refinar o UI de configuração e o processo de registo
*  Azure Data Factory – Suporte Azure Ingress e Egress para fonte de dados do SQL Server

### <a name="1253031"></a>1.2.5303.1

*  Corrija o problema do tempo limite para suportar ligações de fonte de dados mais demoradas.

### <a name="1155268"></a>1.1.5526.8

*  Requer .NET Framework 4.5.1 como pré-requisito durante a configuração.

### <a name="1051442"></a>1.0.5144.2

*  Nenhuma alteração que afete os cenários da Azure Data Factory.
