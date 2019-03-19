---
title: Limites e configuração - Azure Logic Apps | Documentos da Microsoft
description: Valores de configuração para o Azure Logic Apps e limites do serviço
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 11/16/2018
ms.openlocfilehash: 4a2b06f18e709f9a8479770d58f43928893281be
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117358"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Limites e informações de configuração para o Azure Logic Apps

Este artigo descreve os limites e os detalhes de configuração para criar e executar fluxos de trabalho automatizados com o Azure Logic Apps. Para o Microsoft Flow, veja [limites e configuração no Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limites de definição

Aqui estão os limites para uma definição de aplicação lógica única:

| Name | Limite | Notas | 
| ---- | ----- | ----- | 
| Ações por fluxo de trabalho | 500 | Para expandir este limite, pode adicionar fluxos de trabalho aninhados conforme necessário. |
| Profundidade de aninhamento para ações de permissão | 8 | Para expandir este limite, pode adicionar fluxos de trabalho aninhados conforme necessário. | 
| Fluxos de trabalho por região por subscrição | 1,000 | | 
| Acionadores por fluxo de trabalho | 10 | Ao trabalhar na vista de código, não o designer | 
| Limite de casos de âmbito do comutador | 25 | | 
| Variáveis por fluxo de trabalho | 250 | | 
| Carateres por expressão | 8,192 | | 
| Tamanho máximo de `trackedProperties` | 16.000 carateres | 
| Dê um nome para `action` ou `trigger` | 80 carateres | | 
| Comprimento de `description` | 256 carateres | | 
| Máximo `parameters` | 50 | | 
| Máximo `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Limites de retenção e a duração da execução

Aqui estão os limites para uma execução da aplicação lógica única:

| Name | Limite | Notas | 
|------|-------|-------| 
| Duração de execução | 90 dias | Para alterar este limite, consulte [a duração da execução de alteração](#change-duration). | 
| Retenção de armazenamento | hora de início de 90 dias a partir da execução | Para alterar este limite para um valor entre 7 dias e 90 dias, consulte [alterar a retenção de armazenamento](#change-retention). | 
| Intervalo de periodicidade mínimo | 1 segundo | | 
| Intervalo de periodicidade máximo | dias de 500 | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Alterar a retenção de armazenamento e a duração da execução

Para alterar o limite predefinido para entre 7 dias e 90 dias, siga estes passos. Se tiver de ir acima do limite máximo, [contacte a equipa de Logic Apps](mailto://logicappsemail@microsoft.com) para obter ajuda com os seus requisitos.

1. No portal do Azure, no menu da sua aplicação lógica, escolha **definições de fluxo de trabalho**. 

2. Sob **opções de tempo de execução**, da **executar retenção do histórico em dias** lista, escolha **personalizado**. 

3. Introduza ou arraste o controlo de deslize para o número de dias em que quer.

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>A desativar ou eliminar aplicações lógicas

Quando desativa uma aplicação lógica, não existem novas execuções são instanciadas. Todos em curso e execuções pendentes continuará até terminarem, que pode levar tempo a concluir.

Quando elimina uma aplicação lógica, não são instanciadas novas execuções. Todas as execuções em curso e pendentes são canceladas. Se tiver milhares de execuções, o cancelamento pode demorar muito tempo a concluir.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Simultaneidade, looping e divisões de limites

Aqui estão os limites para uma execução da aplicação lógica única:

| Name | Limite | Notas | 
| ---- | ----- | ----- | 
| Simultaneidade de Acionador | * Ilimitado quando o controle de simultaneidade está desativado <p><p>* 25 é o limite padrão quando o controle de simultaneidade estiver ativado, que não pode ser anulado depois de ativar o controlo. Pode alterar o padrão para um valor entre 1 e 50, inclusivamente. | Este limite descreve o maior número de instâncias de aplicações lógicas que podem ser executadas ao mesmo tempo, ou em paralelo. <p><p>Para alterar o limite predefinido para um valor entre 1 e 50, inclusivamente, consulte [limite de simultaneidade do acionador de alteração](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) ou [acionar instâncias sequencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). | 
| Máximo de espera de execuções | Quando o controle de simultaneidade é ativado, o número mínimo de execuções de espera é 10 mais o número de execuções simultâneas (simultaneidade de Acionador). Pode alterar o número máximo de até 100, inclusivamente. | Este limite descreve o maior número de instâncias de aplicações lógicas, que pode aguardar para ser executada quando a aplicação lógica já está a executar o número máximo de instâncias em simultâneo. <p><p>Para alterar o limite predefinido, consulte [limitam execuções de espera da alteração](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). | 
| Itens de matriz de foreach | 100 000 | Este limite descreve o maior número de itens de matriz, que pode processar um loop "for each". <p><p>Para filtrar matrizes maiores, pode utilizar o [ação de consulta](../connectors/connectors-native-query.md). | 
| Simultaneidade de foreach | 20 é o limite padrão quando o controle de simultaneidade é desligado. Pode alterar o padrão para um valor entre 1 e 50, inclusivamente. | Este limite é o maior número de "para cada um" loop iterações que podem ser executadas ao mesmo tempo, ou em paralelo. <p><p>Para alterar o limite predefinido para um valor entre 1 e 50, inclusivamente, consulte [alterar simultaneidade "for each" limite](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) ou [executar "for each" faz um loop sequencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). | 
| Itens SplitOn | 100 000 | Para acionadores que retornam uma matriz, pode especificar uma expressão que utiliza uma propriedade "SplitOn" que [divide ou debatches itens da matriz em várias instâncias de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) para processamento, em vez de usar um loop "Foreach". Esta expressão referencia a matriz a utilizar para criar e executar uma instância de fluxo de trabalho para cada item da matriz. |
| Iterações until | 5.000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limites de débito

Aqui estão os limites para uma execução da aplicação lógica única:

| Name | Limite | Notas | 
| ---- | ----- | ----- | 
| Ação: Execuções por 5 minutos | 300,000 | O limite predefinido é 100 000. Para alterar o limite predefinido, consulte [executar a aplicação lógica no modo de "débitos"](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), que está em pré-visualização. Em alternativa, pode distribuir a carga de trabalho em mais do que uma aplicação de lógica, conforme necessário. | 
| Ação: Chamadas simultâneas | ~2,500 | Pode reduzir o número de pedidos simultâneos ou reduza a duração, se necessário. | 
| Ponto final de tempo de execução: Chamadas de entrada em simultâneo | ~1,000 | Pode reduzir o número de pedidos simultâneos ou reduza a duração, se necessário. | 
| Ponto final de tempo de execução: Chamadas de leitura por 5 minutos  | 60,000 | É possível distribuir a carga de trabalho em mais do que uma aplicação conforme necessário. | 
| Ponto final de tempo de execução: Invocar chamadas por 5 minutos | 45,000 | É possível distribuir a carga de trabalho em mais do que uma aplicação conforme necessário. | 
| Taxa de transferência de conteúdo por 5 minutos | 600 MB | É possível distribuir a carga de trabalho em mais do que uma aplicação conforme necessário. | 
|||| 

Para passar destes limites no processamento normal ou executar o teste de carga que pode passar destes limites, [contacte a equipa de Logic Apps](mailto://logicappsemail@microsoft.com) para obter ajuda com os seus requisitos.

<a name="sftp"></a>

## <a name="ftp-sftp-and-sftp-ssh-limits"></a>Limites FTP, SFTP e SFTP-SSH

### <a name="file-size"></a>Tamanho dos ficheiros

| Name | Limite | Notas |
|------|-------|-------|
| FTP | 50 MB | Para exceder este limite para ações apenas, veja [processar mensagens grandes com a segmentação](../logic-apps/logic-apps-handle-large-messages.md). <p>**Nota**: Segmentação não se aplica a acionadores. Além disso, algumas APIs e conectores podem não suportar a segmentação ou até mesmo o limite predefinido. | 
| SFTP | 50 MB | Para exceder este limite para ações apenas, utilize o [conector do SFTP-SSH](../connectors/connectors-sftp-ssh.md) ou consulte [processar mensagens grandes com a segmentação](../logic-apps/logic-apps-handle-large-messages.md). <p>**Nota**: Segmentação não se aplica a acionadores. Além disso, algumas APIs e conectores podem não suportar a segmentação ou até mesmo o limite predefinido. | 
| SFTP-SSH | 1 GB | Este conector manipula até 1 GB, mas que transfere dados em partes de 50 MB, não de 1 GB partes. Para exceder este limite para ações apenas, veja [processar mensagens grandes com a segmentação](../logic-apps/logic-apps-handle-large-messages.md). <p>**Nota**: Segmentação não se aplica a acionadores. | 
|||| 

<a name="request-limits"></a>

## <a name="http-limits"></a>Limites HTTP

Aqui estão os limites para uma única solicitação HTTP ou uma chamada síncrona de conector:

#### <a name="timeout"></a>Tempo Limite (excedido)

Algumas operações de conector fazem chamadas assíncronas ou escutam os pedidos de webhook, portanto, o tempo limite para essas operações pode ser maior do que estes limites. Para obter mais informações, consulte os detalhes técnicos para o conector específico e também [acionadores de fluxo de trabalho e as ações](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name | Limite | Notas | 
| ---- | ----- | ----- | 
| Pedido de saída | 120 Segundos | Para mais operações em execução, utilize um [padrão de consulta assíncrona](../logic-apps/logic-apps-create-api-app.md#async-pattern) ou uma [até que o loop](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Resposta síncrona | 120 Segundos | Para a solicitação original obter a resposta, todos os passos na resposta deverá ser concluída dentro do limite, a menos que chamar outra aplicação de lógica como um fluxo de trabalho aninhado. Para obter mais informações, consulte [chamar, acionar, ou aninhar aplicações lógicas](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Tamanho da mensagem

| Name | Limite | Notas | 
| ---- | ----- | ----- | 
| Tamanho da mensagem | 100 MB | Para contornar este limite, consulte [processar mensagens grandes com a segmentação](../logic-apps/logic-apps-handle-large-messages.md). No entanto, algumas APIs e conectores podem não suportar a segmentação ou até mesmo o limite predefinido. | 
| Tamanho de mensagem com a segmentação | 1 GB | Este limite aplica-se a ações que nativamente suportam segmentação ou permitem-lhe ativar a divisão na respetiva configuração de tempo de execução. Para obter mais informações, consulte [processar mensagens grandes com a segmentação](../logic-apps/logic-apps-handle-large-messages.md). | 
| Limite de avaliação da expressão | 131,072 carateres | O `@concat()`, `@base64()`, `@string()` expressões não podem ser superior a este limite. | 
|||| 

#### <a name="retry-policy"></a>Política de repetição

| Name | Limite | Notas | 
| ---- | ----- | ----- | 
| Tentativas de repetição | 90 | A predefinição é 4. Para alterar a predefinição, utilize o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Intervalo máx. de repetição | 1 dia | Para alterar a predefinição, utilize o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Intervalo mínimo de repetição | 5 segundos | Para alterar a predefinição, utilize o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limites de conector personalizado

Aqui estão os limites dos conectores personalizados que pode criar a partir de web APIs.

| Name | Limite | 
| ---- | ----- | 
| Número de conectores personalizados | 1000 por subscrição do Azure | 
| Número de pedidos por minuto para cada ligação criada por um conector personalizado | 500 pedidos por ligação |
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Identidades geridas

| Name | Limite | 
| ---- | ----- | 
| Número de aplicações lógicas com atribuído o sistema gerido identidades por subscrição do Azure | 10 | 
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limites de conta de integração

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limites de artefacto por conta de integração

Aqui estão os limites no número de artefactos para cada conta de integração. Para obter mais informações, consulte [preços de aplicações lógicas](https://azure.microsoft.com/pricing/details/logic-apps/). 

*Escalão gratuito*

Utilize o escalão gratuito apenas para cenários de exploratórios, não os cenários de produção. Este escalão restringe o débito e a utilização e não tem nenhum contrato de nível de serviço (SLA).

| Artefacto | Limite | Notas | 
|----------|-------|-------| 
| Assemblagens | 10 | | 
| Configurações de batch | 5 | 
| Certificados | 25 | | 
| Contratos comerciais de EDI | 10 | | 
| Parceiros comerciais EDI | 25 | | 
| Maps | 25 | | 
| Esquemas | 25 | 
|||| 

*Escalão básico*

| Artefacto | Limite | Notas | 
|----------|-------|-------| 
| Assemblagens | 25 | | 
| Configurações de batch | 1 | | 
| Certificados | 2 | | 
| Contratos comerciais de EDI | 1 | | 
| Parceiros comerciais EDI | 2 | | 
| Maps | 500 | | 
| Esquemas | 500 | 
|||| 

*Escalão Standard*

| Artefacto | Limite | Notas | 
|----------|-------|-------| 
| Assemblagens | 50 | | 
| Configurações de batch | 5 |  
| Certificados | 50 | | 
| Contratos comerciais de EDI | 500 | | 
| Parceiros comerciais EDI | 500 | | 
| Maps | 500 | | 
| Esquemas | 500 | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limites de capacidade de artefacto

| Artefacto | Limite | Notas | 
| -------- | ----- | ----- | 
| Assemblagem | 8 MB | Para carregar ficheiros maiores do que 2 MB, utilize um [contentor de BLOBs e de conta de armazenamento do Azure](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Mapa (ficheiro XSLT) | 8 MB | Para carregar ficheiros maiores do que 2 MB, utilize o [mapeia a API de REST do Azure Logic Apps -](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). | 
| Esquema | 8 MB | Para carregar ficheiros maiores do que 2 MB, utilize um [contentor de BLOBs e de conta de armazenamento do Azure](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
||||

| Ponto final de tempo de execução | Limite | Notas |
|------------------|-------|-------|
| Chamadas de leitura por 5 minutos | 60,000 | É possível distribuir a carga de trabalho em mais de uma conta conforme necessário. | 
| Invocar chamadas por 5 minutos | 45,000 | É possível distribuir a carga de trabalho em mais de uma conta conforme necessário. | 
| Chamadas de controlo por 5 minutos | 45,000 | É possível distribuir a carga de trabalho em mais de uma conta conforme necessário. | 
| Bloquear chamadas simultâneas | ~1,000 | Pode reduzir o número de pedidos simultâneos ou reduza a duração, se necessário. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Tamanho da mensagem de protocolo de B2B (AS2, X12, EDIFACT)

Aqui estão os limites que se aplicam aos protocolos B2B:

| Name | Limite | Notas | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Aplica-se a descodificar e codificar | 
| X12 | 50 MB | Aplica-se a descodificar e codificar | 
| EDIFACT | 50 MB | Aplica-se a descodificar e codificar | 
|||| 

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Configuração da firewall: Endereços IP

Todas as aplicações lógicas na mesma região, utilize os mesmos intervalos de endereços IP. Para suportar as chamadas que as logic apps tornam diretamente com [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)e outros pedidos HTTP, configurar as firewalls com *todos os* o [ entrada](#inbound) *e* [saída](#outbound) endereços IP utilizados pelo serviço de aplicações lógicas, baseado nas regiões onde existem as logic apps. Estes endereços são apresentados no **Inbound** e **saída** cabeçalhos nesta secção e são ordenados por região.

Para suportar as chamadas que [conectores geridos pela Microsoft](../connectors/apis-list.md) make, configurar a firewall com *todos os* o [saída](#outbound) endereços IP utilizados por estes conectores, com base nas regiões de onde existem as logic apps. Estes endereços são apresentados no **saída** nesta seção do cabeçalho e são ordenados por região. 

Para [do Azure Government](../azure-government/documentation-government-overview.md) e [Azure China 21Vianet](/azure/china/china-welcome), endereços IP reservados para os conectores não estão atualmente disponíveis.

> [!IMPORTANT]
> 
> Se tiver configurações existentes, atualize-los **logo que possível antes de 1 de Setembro de 2018** para que incluem e correspondem aos endereços IP estas listas para as regiões onde existem as logic apps. 

O Logic Apps não suporta a ligação direta para contas de armazenamento do Azure através de firewalls. Para aceder a estas contas de armazenamento, utilize uma das opções aqui: 

* Criar uma [ambiente do serviço de integração](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que pode ligar aos recursos numa rede virtual do Azure. 

* Se já utilizar a gestão de API, pode utilizar este serviço para este cenário. Para mais informações, veja [arquitetura de integração empresarial simples](https://aka.ms/aisarch).

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Endereços IP de entrada - apenas o serviço de aplicações lógicas

| Região | IP |
|--------|----|
| Leste da Austrália | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Sudeste da Austrália | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Sul do Brasil | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Canadá Central | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Leste do Canadá | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Índia Central | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| EUA Central | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Ásia Oriental | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| EUA Leste | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| EUA Leste 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Leste do Japão | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Oeste do Japão | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| EUA Centro-Norte | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Europa do Norte | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| EUA Centro-Sul | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Sul da Índia | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Sudeste Asiático | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| EUA Centro-Oeste | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Europa Ocidental | 13.95.155.53, 52.174.49.6, 52.174.49.6, 52.174.54.218 |
| Oeste da Índia | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| EUA Oeste | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| EUA Oeste 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Reino Unido Sul | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Reino Unido Oeste | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Endereços IP de saída - serviço de aplicações lógicas e conectores geridos

| Região | IP de aplicações lógicas | IP de conectores geridos | 
|--------|---------------|-----------------------|
| Leste da Austrália | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 | 
| Sudeste da Austrália | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34 | 
| Sul do Brasil | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207 | 
| Canadá Central | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126 | 
| Leste do Canadá | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152 | 
| Índia Central | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164 | 
| EUA Central | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164 | 
| Ásia Oriental | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169 | 
| EUA Leste | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 | 
| EUA Leste 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 | 
| Leste do Japão | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96 | 
| Oeste do Japão | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248 | 
| EUA Centro-Norte | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 | 
| Europa do Norte | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 | 
| EUA Centro-Sul | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 | 
| Sul da Índia | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225 | 
| Sudeste Asiático | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19 | 
| EUA Centro-Oeste | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22 | 
| Europa Ocidental | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 | 
| Oeste da Índia | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218 | 
| EUA Oeste | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 |40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49 | 
| EUA Oeste 2 | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128 - 13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 | 
| Reino Unido Sul | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15 | 
| Reino Unido Oeste | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 | 
|||| 

## <a name="next-steps"></a>Passos Seguintes  

* Saiba como [criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Saiba mais sobre [exemplos e cenários comuns](../logic-apps/logic-apps-examples-and-scenarios.md)
