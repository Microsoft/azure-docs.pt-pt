---
title: Notas de lançamento do Gateway de Gestão de Dados
description: Notas de lançamento tory Gateway de Gestão de Dados
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
ms.openlocfilehash: 838e523f74a21c44958ddb6dc88e4dab3526d81a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80065004"
---
# <a name="release-notes-for-data-management-gateway"></a>Notas de lançamento do Gateway de Gestão de Dados
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o tempo de execução da [integração auto-hospedado em V2](../create-self-hosted-integration-runtime.md).

Um dos desafios para a integração moderna de dados é mover dados de e para o local para a nuvem. Data Factory faz esta integração com data Management Gateway, que é um agente que você pode instalar no local para permitir o movimento de dados híbridos.

Consulte os seguintes artigos para obter informações detalhadas sobre gateway de gestão de dados e como utilizá-lo:

*  [Data Management Gateway](data-factory-data-management-gateway.md)
*  [Mova dados entre as instalações e a nuvem usando a Fábrica de Dados Azure](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>VERSÃO ATUAL 
Não mais mantemos as notas de lançamento aqui. Obtenha as últimas notas de lançamento [aqui](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Versões anteriores
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Melhorias...
- Pode adicionar entradas dNS ao autocarro de serviço whitelist em vez de listar todos os endereços IP Do Azure da sua firewall (se necessário). Pode encontrar a respetiva entrada de DNS no portal Azure (Data Factory -> 'Author and Deploy' -> 'Gateways' -> "serviceUrls" (em JSON)
- O conector HDFS suporta agora um certificado público auto-assinado, permitindo-lhe ignorar a validação do TLS.
- Fixo: Problema com gateway offline durante a atualização (devido a distorção do relógio)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Melhorias...
-   Pode adicionar entradas dNS ao Whitelist Service Bus em vez de listar todos os endereços IP Do Azure da sua firewall (se necessário). Mais detalhes aqui.
-   Agora pode copiar dados para/de uma única bolha de bloco até 4,75 TB, que é o tamanho máximo suportado da bolha do bloco. (limite anterior era de 195 GB).
-   Fixo: Fora do problema de memória ao desapertar vários pequenos ficheiros durante a atividade da cópia.
-   Fixo: Index fora de alcance problema ao copiar do Documento DB para um Servidor SQL no local com recurso de idempotency.
-   Fixo: O script de limpeza SQL não funciona com o Servidor SQL no local do Copy Wizard.
-   Fixo: O nome da coluna com espaço no final não funciona na atividade de cópia.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Melhorias...
- Fixo: Emita com credenciais em falta no reboot da máquina de gateway.
- Fixo: Problema com o registo durante a restauração do gateway utilizando um ficheiro de reserva.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Melhorias...
- Fixo: Leitura incorreta do valor nulo decimal da Oracle como fonte.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Novidades
- Os clientes podem fornecer feedback sobre a experiência de registo de gateways.
- Suporte um novo formato de compressão: ZIP (Deflate)

### <a name="enhancements-"></a>Melhorias...
- Melhoria de desempenho para Oracle Sink, fonte HDFS.
- Correção de bugs para atualização automática gateway, capacidade de processamento paralelo gateway.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Melhoramentos 
- Experiência de registo gateway melhorada e mais robusta- Agora pode acompanhar o estado de progresso durante o processo de registo gateway, o que torna a experiência de registo mais responsiva.
- Melhoria no Processo de Restauro gateway- Ainda pode recuperar o gateway mesmo que não tenha o ficheiro de backup gateway com esta atualização. Isto exigiria que redefinisse as credenciais do Linked Service no Portal.
- Correção de insetos.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Novidades

- Agora pode armazenar credenciais de origem de dados localmente. As credenciais estão encriptadas. As credenciais de origem de dados podem ser recuperadas e restauradas utilizando o ficheiro de backup que pode ser exportado a partir do Gateway existente, todos no local.

### <a name="enhancements-"></a>Melhorias...

- Experiência de registo gateway melhorada e mais robusta.
- Apoie a deteção automática da configuração QuoteChar para o formato de Texto no assistente de cópia e melhore a precisão geral de deteção do formato.

## <a name="2361002"></a>2.3.6100.2

- Suporte primeiroRowAsHeader e SkipLineCount deteção automática no assistente de cópia para ficheiros de texto no sistema de ficheiros no local e HDFS.
- Melhorar a estabilidade da ligação de rede entre gateway e Service Bus
- Algumas correções de insetos


## <a name="2260721"></a>2.2.6072.1

*  Suporta a definição de proxy HTTP para o gateway utilizando o Gateway Configuration Manager. Se configurado, Azure Blob, Azure Table, Azure Data Lake e Document DB são acedidos através de http proxy.
*  Suporta o manuseamento do cabeçalho para textFormat ao copiar dados de/para Azure Blob, Azure Data Lake Store, no local do Sistema de Ficheiros e no local HDFS.
*  Suporta a cópia de dados do Append Blob e do Page Blob juntamente com o já suportado Block Blob.
*  Introduz um novo estado de gateway **Online (Limited),** que indica que a principal funcionalidade do gateway funciona, exceto o suporte de operação interativo para o Copy Wizard.
*  Aumenta a robustez do registo de gateway utilizando a chave de registo.

## <a name="216040"></a>2.1.6040.

*  O controlador DB2 está incluído no pacote de instalação do gateway agora. Não é necessário instalá-lo em separado.
*  O controlador DB2 suporta agora z/OS e DB2 para i (AS/400) juntamente com as plataformas já suportadas (Linux, Unix e Windows).
*  Suporta a utilização do Azure Cosmos DB como fonte ou destino para as lojas de dados no local
*  Suporta a cópia dos dados de/para o armazenamento de bolhas frias/quentes, juntamente com a conta de armazenamento de fins gerais já suportada.
*  Permite-lhe ligar-se ao SQL Server no local através de gateway com privilégios de login remotos.  

## <a name="2060131"></a>2.0.6013.1

*  Pode selecionar a língua/cultura para ser utilizada por um portal durante a instalação manual.

*  Quando o gateway não funcionar como esperado, pode optar por enviar registos de gateway dos últimos sete dias à Microsoft para facilitar a resolução de problemas do problema. Se o gateway não estiver ligado ao serviço de nuvem, pode optar por guardar e arquivar registos de gateway.  

*  Melhorias na interface do utilizador para o gestor de configuração do gateway:

    *  Torne o estado do gateway mais visível no separador Home.

    *  Controlos reorganizados e simplificados.

    *  Pode copiar dados de um armazenamento utilizando a [ferramenta de cópia sem código](data-factory-copy-data-wizard-tutorial.md). Consulte [a Cópia Encenada](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre esta funcionalidade em geral.
*  Pode utilizar o Portal de Gestão de Dados para ingressar dados diretamente a partir de uma base de dados do SQL Server no local para o Azure Machine Learning.

*  Melhorias no desempenho

    * Melhore o desempenho na visualização de Schema/Preview contra o SQL Server na ferramenta de cópia sem código.

## <a name="11259531"></a>1.12.5953.1

*  Correções de erros

## <a name="11159181"></a>1.11.5918.1

*  O tamanho máximo do registo do evento gateway foi aumentado de 1 MB para 40 MB.

*  É apresentado um diálogo de advertência no caso de ser necessário reiniciar durante a atualização automática do gateway. Pode optar por reiniciar logo ou mais tarde.

*  Caso a atualização automática falhe, o instalador de gateway volta a atualizar automaticamente três vezes no máximo.

*  Melhorias no desempenho

    * Melhore o desempenho para carregar grandes tabelas do servidor no local em cenário de cópia sem código.

*  Correções de erros

## <a name="11058921"></a>1.10.5892.1

*  Melhorias no desempenho

*  Correções de erros

## <a name="1958652"></a>1.9.5865.2

*  Capacidade de atualização automática de zero toque
*  Novo ícone da bandeja com indicadores de estado de gateway
*  Capacidade de "Atualizar agora" do cliente
*  Capacidade de definir o tempo de programação da atualização
*  Script PowerShell para atualização automática de toggling on/off
*  Suporte para o formato JSON  
*  Melhorias no desempenho
*  Correções de erros

## <a name="1858221"></a>1.8.5822.1

*  Melhorar a experiência de resolução de problemas
*  Melhorias no desempenho
*  Correções de erros

### <a name="1757951"></a>1.7.5795.1

*  Melhorias no desempenho
*  Correções de erros

### <a name="1757641"></a>1.7.5764.1

*  Melhorias no desempenho
*  Correções de erros

### <a name="1657351"></a>1.6.5735.1

*  Suporte no local HDFS Fonte/Pia
*  Melhorias no desempenho
*  Correções de erros

### <a name="1656961"></a>1.6.5696.1

*  Melhorias no desempenho
*  Correções de erros

### <a name="1656761"></a>1.6.5676.1

*  Apoie ferramentas de diagnóstico no Gestor de Configuração
*  Colunas de mesa de suporte para fontes de dados tabular para a Fábrica de Dados Azure
*  Suporte SQL DW para Fábrica de Dados Azure
*  Suporte recluso em BlobSource e FileSource para Fábrica de Dados Azure
*  Suporte CopyBehavior – MergeFiles, PreserveHierarchy e FlattenHierarchy em BlobSink e FileSink com Cópia Binária para Fábrica de Dados Azure
*  Atividade de cópia de suporte reportando progresso para Azure Data Factory
*  Suporte de suporte Fonte de Conectividade de Dados Validação para Fábrica de Dados Azure
*  Correções de erros

### <a name="1656721"></a>1.6.5672.1

*  Nome da tabela de suporte para fonte de dados da ODBC para a Fábrica de Dados Azure
*  Melhorias no desempenho
*  Correções de erros

### <a name="1656581"></a>1.6.5658.1

*  Sumidouro de arquivo de suporte para fábrica de dados azure
*  Apoiar a preservação da hierarquia em cópia binária para a Azure Data Factory
*  Atividade de cópia de suporte Idempotency para Azure Data Factory
*  Correções de erros

### <a name="1656401"></a>1.6.5640.1

*  Suporte mais 3 fontes de dados para a Azure Data Factory (ODBC, OData, HDFS)
*  Suporte citação personagem em parser csv para Azure Data Factory
*  Suporte de compressão (BZip2)
*  Correções de erros

### <a name="1556121"></a>1.5.5612.1

*  Suporte cinco bases de dados relacionais para a Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata e Sybase)
*  Suporte de compressão (Gzip e Deflate)
*  Melhorias no desempenho
*  Correções de erros

### <a name="1455491"></a>1.4.5549.1

*  Adicione suporte de fonte de dados da Oracle para a Fábrica de Dados Azure
*  Melhorias no desempenho
*  Correções de erros

### <a name="1454921"></a>1.4.5492.1

*  Binário unificado que suporta tanto a Microsoft Azure Data Factory como os serviços do Office 365 Power BI
*  Refinar a UI de Configuração e o processo de registo
*  Azure Data Factory – Suporte azure Ingress e Egress para fonte de dados do SQL Server

### <a name="1253031"></a>1.2.5303.1

*  Corrija o problema do tempo para suportar ligações de fonte de dados mais morosas.

### <a name="1155268"></a>1.1.5526.8

*  Requer a .NET Quadro 4.5.1 como pré-requisito durante a configuração.

### <a name="1051442"></a>1.0.5144.2

*  Não há alterações que afetem os cenários da Azure Data Factory.
