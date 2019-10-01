---
title: Limites e configuração – aplicativos lógicos do Azure
description: Limites de serviço e valores de configuração para aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/19/2019
ms.openlocfilehash: 02c27faa4ac45165747d5eb450e75f666ba7d013
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703476"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Limites e informações de configuração para aplicativos lógicos do Azure

Este artigo descreve os limites e os detalhes de configuração para criar e executar fluxos de trabalho automatizados com aplicativos lógicos do Azure. Para Microsoft Flow, consulte [limites e configuração no Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limites de definição

Aqui estão os limites para uma única definição de aplicativo lógico:

| Name | Limite | Notas |
| ---- | ----- | ----- |
| Ações por fluxo de trabalho | 500 | Para estender esse limite, você pode adicionar fluxos de trabalho aninhados conforme necessário. |
| Profundidade de aninhamento permitida para ações | 8 | Para estender esse limite, você pode adicionar fluxos de trabalho aninhados conforme necessário. |
| Fluxos de trabalho por região por assinatura | 1,000 | |
| Gatilhos por fluxo de trabalho | 10 | Ao trabalhar no modo de exibição de código, não no designer |
| Limite de casos do switch Scope | 25 | |
| Variáveis por fluxo de trabalho | 250 | |
| Caracteres por expressão | 8,192 | |
| Tamanho máximo para`trackedProperties` | 16.000 caracteres |
| Nome para `action` ou`trigger` | 80 caracteres | |
| Comprimento de`description` | 256 caracteres | |
| Maior`parameters` | 50 | |
| Maior`outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Duração da execução e limites de retenção

Estes são os limites de execução de um único aplicativo lógico:

| Name | Limite de vários locatários | Limite do ambiente do serviço de integração | Notas |
|------|--------------------|---------------------------------------|-------|
| Duração da execução | 90 dias | 365 dias | Para alterar o limite padrão, consulte [alterar duração da execução](#change-duration). |
| Retenção de armazenamento | 90 dias a partir da hora de início da execução | 365 dias | Para alterar o limite padrão, consulte [alterar retenção de armazenamento](#change-retention). |
| Intervalo de recorrência mínimo | 1 segundo | 1 segundo ||
| Intervalo de recorrência máximo | 500 dias | 500 dias ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Duração da execução de alteração e retenção de armazenamento

Para alterar o limite padrão de duração da execução e retenção de armazenamento, siga estas etapas. Se você precisar ir além do limite máximo, [entre em contato com a equipe dos aplicativos lógicos](mailto://logicappsemail@microsoft.com) para obter ajuda com seus requisitos.

1. No portal do Azure, no menu do seu aplicativo lógico, escolha **configurações de fluxo de trabalho**.

2. Em **Opções de tempo de execução**, na lista **retenção do histórico de execuções em dias** , escolha **personalizada**.

3. Insira ou arraste o controle deslizante para o número de dias desejados.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Limites de simultaneidade, looping e debatching

Estes são os limites de execução de um único aplicativo lógico:

| Name | Limite | Notas |
| ---- | ----- | ----- |
| Disparar simultaneidade | * Ilimitado quando o controle de simultaneidade está desativado <p><p>* 25 é o limite padrão quando o controle de simultaneidade é ativado, o que não pode ser desfeito depois que você ativa o controle. Você pode alterar o padrão para um valor entre 1 e 50, inclusive. | Esse limite descreve o número mais alto de instâncias de aplicativo lógico que podem ser executadas ao mesmo tempo ou em paralelo. <p><p>Para alterar o limite padrão para um valor entre 1 e 50 inclusivamente, consulte [alterar o limite](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) de simultaneidade de gatilho ou [instâncias de gatilho sequencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Máximo de execuções de espera | Quando o controle de simultaneidade é ativado, o número mínimo de execuções de espera é 10 mais o número de execuções simultâneas (simultaneidade de gatilho). Você pode alterar o número máximo de até 100 inclusive. | Esse limite descreve o número mais alto de instâncias de aplicativo lógico que podem esperar para serem executadas quando seu aplicativo lógico já estiver executando o máximo de instâncias simultâneas. <p><p>Para alterar o limite padrão, consulte [alterar o limite](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)de execuções em espera. |
| Itens da matriz foreach | 100,000 | Esse limite descreve o número mais alto de itens de matriz que um loop "for each" pode processar. <p><p>Para filtrar matrizes maiores, você pode usar a [ação de consulta](../connectors/connectors-native-query.md). |
| Simultaneidade foreach | 20 é o limite padrão quando o controle de simultaneidade é desativado. Você pode alterar o padrão para um valor entre 1 e 50, inclusive. | Esse limite é o número mais alto de iterações de loop "for each" que podem ser executadas ao mesmo tempo ou em paralelo. <p><p>Para alterar o limite padrão para um valor entre 1 e 50 inclusive, consulte [alterar "para cada" limite](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) de simultaneidade ou [executar "para cada" loops sequencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Itens de divisão | 100,000 | Para gatilhos que retornam uma matriz, você pode especificar uma expressão que usa uma propriedade ' Splitness ' que [divide ou delote itens de matriz em várias instâncias de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) para processamento, em vez de usar um loop "foreach". Essa expressão faz referência à matriz a ser usada para criar e executar uma instância de fluxo de trabalho para cada item de matriz. |
| Até iterações | 5,000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limites de taxa de transferência

Aqui estão os limites para uma única definição de aplicativo lógico:

### <a name="multi-tenant-logic-apps-service"></a>Serviço de aplicativos lógicos multilocatários

| Name | Limite | Notas |
| ---- | ----- | ----- |
| Ação: Execuções por 5 minutos | 100.000 é o limite padrão, mas 300.000 é o limite máximo. | Para alterar o limite padrão, consulte [executar seu aplicativo lógico no modo de "alta taxa de transferência"](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), que está em versão prévia. Ou, você pode distribuir a carga de trabalho entre mais de um aplicativo lógico, conforme necessário. |
| Ação: Chamadas de saída simultâneas | ~2,500 | Você pode reduzir o número de solicitações simultâneas ou reduzir a duração conforme necessário. |
| Ponto de extremidade de tempo de execução: Chamadas de entrada simultâneas | ~1,000 | Você pode reduzir o número de solicitações simultâneas ou reduzir a duração conforme necessário. |
| Ponto de extremidade de tempo de execução: Ler chamadas por 5 minutos  | 60,000 | Você pode distribuir a carga de trabalho entre mais de um aplicativo, conforme necessário. |
| Ponto de extremidade de tempo de execução: Invocar chamadas por 5 minutos | 45,000 | Você pode distribuir a carga de trabalho entre mais de um aplicativo, conforme necessário. |
| Taxa de transferência de conteúdo por 5 minutos | 600 MB | Você pode distribuir a carga de trabalho entre mais de um aplicativo, conforme necessário. |
||||

### <a name="integration-service-environment-ise"></a>Ambiente do serviço de integração (ISE)

Estes são os limites de taxa de transferência para o SKU Premium:

| Name | Limite | Notas |
|------|-------|-------|
| Limite de execução de unidade base | Sistema-limitado quando a capacidade da infraestrutura atinge 80% | Fornece ~ 4.000 execuções de ação por minuto, que é ~ 160 milhões execuções de ação por mês | |
| Limite de execução de unidade de escala | Sistema-limitado quando a capacidade da infraestrutura atinge 80% | Cada unidade de escala pode fornecer aproximadamente 2.000 execuções de ação adicionais por minuto, que é ~ 80 milhões mais execuções de ação por mês | |
| Unidades de escala máximas que você pode adicionar | 10 | |
||||

Para ultrapassar esses limites no processamento normal ou executar testes de carga que possam ultrapassar esses limites, [entre em contato com a equipe de aplicativos lógicos](mailto://logicappsemail@microsoft.com) para obter ajuda com seus requisitos.

> [!NOTE]
> A [SKU do desenvolvedor](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) não tem limites publicados, pois essa SKU não tem nenhum SLA (contrato de nível de serviço) ou recursos para escalar verticalmente. Use esta SKU somente para testes, desenvolvimento e teste, não para produção ou teste de desempenho.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Limites de gateway

O aplicativo lógico do Azure dá suporte a operações de gravação, incluindo inserções e atualizações, por meio do gateway. No entanto, essas operações têm [limites de seu tamanho de carga](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="request-limits"></a>

## <a name="http-limits"></a>Limites de HTTP

Aqui estão os limites para uma única solicitação HTTP ou chamada de conector síncrono:

#### <a name="timeout"></a>Tempo limite

Algumas operações de conector fazem chamadas assíncronas ou escutam solicitações de webhook, portanto, o tempo limite para essas operações pode ser maior do que esses limites. Para obter mais informações, consulte os detalhes técnicos para o conector específico e também [gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name | Limite de vários locatários | Limite do ambiente do serviço de integração | Notas |
|------|--------------------|---------------------------------------|-------|
| Solicitação de saída | 120 segundos | 240 segundos | Para operações em execução mais longas, use um [padrão](../logic-apps/logic-apps-create-api-app.md#async-pattern) de sondagem assíncrona ou um [loop Until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). |
| Resposta síncrona | 120 segundos | 240 segundos | Para a solicitação original obter a resposta, todas as etapas na resposta devem ser concluídas dentro do limite, a menos que você chame outro aplicativo lógico como um fluxo de trabalho aninhado. Para obter mais informações, consulte [chamar, disparar ou aninhar aplicativos lógicos](../logic-apps/logic-apps-http-endpoint.md). |
|||||

#### <a name="message-size"></a>Tamanho da mensagem

| Name | Limite de vários locatários | Limite do ambiente do serviço de integração | Notas |
|------|--------------------|---------------------------------------|-------|
| Tamanho da mensagem | 100 MB | 200 MB | Para contornar esse limite, consulte [tratar mensagens grandes com agrupamento](../logic-apps/logic-apps-handle-large-messages.md). No entanto, alguns conectores e APIs podem não dar suporte a agrupamento ou até mesmo ao limite padrão. |
| Tamanho da mensagem com agrupamento | 1 GB | 5 GB | Esse limite se aplica a ações que oferecem suporte nativo ao agrupamento ou permitem que você habilite o agrupamento em sua configuração de tempo de execução. <p>Para o ambiente do serviço de integração, o mecanismo dos aplicativos lógicos dá suporte a esse limite, mas os conectores têm seus próprios limites de agrupamento até o limite do mecanismo, por exemplo, consulte [conector do armazenamento de BLOBs do Azure](/connectors/azureblob/). Para obter mais informações sobre agrupamento, consulte [tratar mensagens grandes com agrupamento](../logic-apps/logic-apps-handle-large-messages.md). |
| Limite de avaliação de expressão | 131.072 caracteres | 131.072 caracteres | As `@concat()`expressões `@base64()`, ,`@string()` não podem ser maiores que esse limite. |
|||||

#### <a name="retry-policy"></a>Política de repetição

| Name | Limite | Notas |
| ---- | ----- | ----- |
| Tentativas de repetição | 90 | O padrão é 4. Para alterar o padrão, use o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Atraso máximo de repetição | 1 dia | Para alterar o padrão, use o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Atraso mínimo de repetição | 5 segundos | Para alterar o padrão, use o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limites de conector personalizado

Aqui estão os limites para conectores personalizados que você pode criar com base em APIs da Web.

| Name | Limite de vários locatários | Limite do ambiente do serviço de integração | Notas |
|------|--------------------|---------------------------------------|-------|
| Número de conectores personalizados | 1000 por subscrição do Azure | 1000 por subscrição do Azure ||
| Número de solicitações por minuto para um conector personalizado | 500 solicitações por minuto por conexão | 2\.000 solicitações por minuto por *conector personalizado* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Identidades geridas

| Name | Limite |
| ---- | ----- |
| Número de aplicativos lógicos com identidades gerenciadas atribuídas pelo sistema por assinatura do Azure | 100 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limites da conta de integração

Cada assinatura do Azure tem estes limites de conta de integração:

* Uma conta de integração de [camada gratuita](../logic-apps/logic-apps-pricing.md#integration-accounts) por região do Azure

* 1\.000 total de contas de integração, incluindo contas de integração em qualquer [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) em [SKUs do desenvolvedor e Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Cada ISE, seja [desenvolvedor ou Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), está limitado a 5 contas de integração totais:

  | SKU DO ISE | Limites da conta de integração |
  |---------|----------------------------|
  | **Premium** | total de 5 contas [padrão](../logic-apps/logic-apps-pricing.md#integration-accounts) , incluindo uma conta padrão gratuitamente. Nenhuma conta gratuita ou básica é permitida. |
  | **Programador** | 5 total- [livre](../logic-apps/logic-apps-pricing.md#integration-accounts) (limitado a 1 conta) e [padrão](../logic-apps/logic-apps-pricing.md#integration-accounts) combinado, ou todas as contas padrão. Nenhuma conta básica é permitida. Use a [SKU do desenvolvedor](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) para experimentação, desenvolvimento e teste, mas não para teste de produção ou de desempenho. |
  |||

Custos adicionais se aplicam a contas de integração que você adiciona além das contas de integração incluídas com um ISE. Para saber como o preço e a cobrança funcionam para o ISEs, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limites de artefato por conta de integração

Aqui estão os limites do número de artefatos para cada camada de conta de integração. Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). Para saber como o preço e a cobrança funcionam para contas de integração, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Use a camada gratuita somente para cenários exploratórios, não cenários de produção. Essa camada restringe a taxa de transferência e o uso e não tem nenhum SLA (contrato de nível de serviço).

| Artefacto | Livre | Básica | Standard |
|----------|------|-------|----------|
| Contratos comerciais de EDI | 10 | 1 | 1,000 |
| Parceiros comerciais de EDI | 25 | 2 | 1,000 |
| Maps | 25 | 500 | 1,000 |
| Esquemas | 25 | 500 | 1,000 |
| Assemblagens | 10 | 25 | 1,000 |
| Certificados | 25 | 2 | 1,000 |
| Configurações do lote | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limites de capacidade do artefato

| Artefacto | Limite | Notas |
| -------- | ----- | ----- |
| Assemblagem | 8 MB | Para carregar arquivos com mais de 2 MB, use uma [conta de armazenamento do Azure e um contêiner de blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| MAP (arquivo XSLT) | 8 MB | Para carregar arquivos com mais de 2 MB, use a [API REST de aplicativos lógicos do Azure-Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). |
| Esquema | 8 MB | Para carregar arquivos com mais de 2 MB, use uma [conta de armazenamento do Azure e um contêiner de blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

| Ponto de extremidade tempo de execução | Limite | Notas |
|------------------|-------|-------|
| Ler chamadas por 5 minutos | 60,000 | Você pode distribuir a carga de trabalho entre mais de uma conta, conforme necessário. |
| Invocar chamadas por 5 minutos | 45,000 | Você pode distribuir a carga de trabalho entre mais de uma conta, conforme necessário. |
| Acompanhamento de chamadas por 5 minutos | 45,000 | Você pode distribuir a carga de trabalho entre mais de uma conta, conforme necessário. |
| Bloqueando chamadas simultâneas | ~1,000 | Você pode reduzir o número de solicitações simultâneas ou reduzir a duração conforme necessário. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Tamanho da mensagem do protocolo B2B (AS2, X12, EDIFACT)

Estes são os limites de tamanho de mensagem que se aplicam aos protocolos B2B:

| Name | Limite de vários locatários | Limite do ambiente do serviço de integração | Notas |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2-100 MB<br>v1-50 MB | v2-200 MB <br>v1-50 MB | Aplica-se a decodificar e codificar |
| X12 | 50 MB | 50 MB | Aplica-se a decodificar e codificar |
| EDIFACT | 50 MB | 50 MB | Aplica-se a decodificar e codificar |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Desabilitando ou excluindo aplicativos lógicos

Quando você desabilita um aplicativo lógico, nenhuma execução nova é instanciada. Todas as execuções em andamento e pendentes continuam até que sejam concluídas, o que pode levar tempo para ser concluído.

Quando elimina uma aplicação lógica, não são instanciadas novas execuções. Todas as execuções em curso e pendentes são canceladas. Se tiver milhares de execuções, o cancelamento pode demorar muito tempo a concluir.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Configuração da firewall: Endereços IP

Os endereços IP que o aplicativo lógico do Azure usa para chamadas de entrada e saída dependem da região em que seu aplicativo lógico existe. *Todos os* aplicativos lógicos que estão na mesma região usam os mesmos intervalos de endereços IP.

> [!NOTE]
> Algumas chamadas Microsoft Flow, como solicitações **http** e **http + openapi** , vão diretamente por meio do serviço aplicativos lógicos do Azure e vêm dos endereços IP listados aqui. Para obter mais informações sobre os endereços IP usados pelo Microsoft Flow, consulte [limites e configuração no Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config#ip-address-configuration).

* Para dar suporte às chamadas que seus aplicativos lógicos fazem diretamente com [http](../connectors/connectors-native-http.md), [http + Swagger](../connectors/connectors-native-http-swagger.md)e outras solicitações HTTP, configure seu firewall com *todos* os endereços IP de [entrada](#inbound) *e* [saída](#outbound) que são usados pelos aplicativos lógicos serviço, com base nas regiões em que os seus aplicativos lógicos existem. Esses endereços aparecem sob os cabeçalhos de **entrada** e **saída** nesta seção e são classificados por região.

* Para dar suporte às chamadas feitas pelos conectores gerenciados pela [Microsoft](../connectors/apis-list.md) , configure seu firewall com *todos* os endereços IP de [saída](#outbound) usados por esses conectores, com base nas regiões em que seus aplicativos lógicos existem. Esses endereços aparecem sob o título de **saída** nesta seção e são classificados por região. 

* Para aplicativos lógicos executados em um ambiente do serviço de integração (ISE), certifique-se de [abrir essas portas](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#ports).

* Os aplicativos lógicos não podem acessar diretamente as contas de armazenamento do Azure que têm [regras de firewall](../storage/common/storage-network-security.md) e existem na mesma região. No entanto, os aplicativos lógicos podem acessar contas de armazenamento do Azure que existem em uma região diferente porque um endereço IP público é usado para se comunicar entre regiões. Apenas certifique-se de permitir os [endereços IP de saída para conectores gerenciados em sua região](../logic-apps/logic-apps-limits-and-config.md#outbound). Ou, você pode usar opções mais avançadas aqui:

  * Crie um [ambiente de serviço de integração](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que pode se conectar a recursos em uma rede virtual do Azure.

  * Se você usar uma camada dedicada para o gerenciamento de API, poderá antecipar a API de armazenamento usando o gerenciamento de API e permitindo os endereços IP da última por meio do firewall. Basicamente, adicione a rede virtual do Azure que é usada pelo gerenciamento de API à configuração de firewall da conta de armazenamento. Em seguida, você pode usar a ação de gerenciamento de API ou a ação HTTP para chamar as APIs de armazenamento do Azure. No entanto, se você escolher essa opção, precisará manipular o processo de autenticação por conta própria. Para obter mais informações, consulte [arquitetura de integração corporativa simples](https://aka.ms/aisarch).

* Para conectores personalizados, [Azure governamental](../azure-government/documentation-government-overview.md)e [Azure China 21VIANET](https://docs.microsoft.com/azure/china/), endereços IP fixos ou reservados não estão disponíveis.

> [!IMPORTANT]
> Se você tiver configurações de firewall que você configurou antes de 1º de setembro de 2018, verifique se elas correspondem aos endereços IP atuais nessas listas para as regiões em que seus aplicativos lógicos existem.

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Endereços IP de entrada-somente serviço de aplicativos lógicos

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
| East US | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| EUA Leste 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| França Central | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Sul de França | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Leste do Japão | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Oeste do Japão | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| Coreia do Sul Central | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Coreia do Sul | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| EUA Centro-Norte | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Europa do Norte | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| EUA Centro-Sul | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Sul da Índia | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Sudeste Asiático | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| EUA Centro-Oeste | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Europa Ocidental | 13.95.155.53, 52.174.54.218, 52.174.49.6, 51.144.176.185 |
| Oeste da Índia | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| EUA Oeste | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| EUA Oeste 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Reino Unido Sul | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Reino Unido Oeste | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Endereços IP de saída-& conectores gerenciados do serviço de aplicativos lógicos

| Região | IP dos aplicativos lógicos | IP de conectores gerenciados |
|--------|---------------|-----------------------|
| Leste da Austrália | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10 |
| Sudeste da Austrália | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.77.50.240 - 13.77.50.255, 13.70.136.174 |
| Sul do Brasil | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 191.233.203.192 - 191.233.203.207, 104.41.59.51 | 
| Canadá Central | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.237.24.126 |
| Leste do Canadá | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.242.35.152 |
| Índia Central | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 104.211.81.192 - 104.211.81.207, 52.172.211.12 |
| EUA Central | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 52.173.245.164 |
| Ásia Oriental | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 52.175.23.169 |
| East US | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 |
| EUA Leste 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154 |
| França Central | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28,40.89.190.104 | 40.89.135.2 |
| Sul de França | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.133.184 |
| Leste do Japão | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.78.108.0 - 13.78.108.15, 13.71.153.19 |
| Oeste do Japão | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 104.215.61.248 |
| Coreia do Sul Central | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.36.214 |
| Coreia do Sul | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.163.10 |
| EUA Centro-Norte | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161 |
| Europa do Norte | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68 |
| EUA Centro-Sul | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 104.214.19.48 - 104.214.19.63, 13.65.86.57 |
| Sul da Índia | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 40.78.194.240 - 40.78.194.255, 13.71.125.22 |
| Sudeste Asiático | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 52.187.68.19 |
| EUA Centro-Oeste | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.102.22 |
| Europa Ocidental | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 |
| Oeste da Índia | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.189.218 |
| EUA Oeste | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 | 40.112.243.160 - 40.112.243.175, 104.42.122.49 |
| EUA Oeste 2 | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128 - 13.66.140.143, 52.183.78.157 |
| Reino Unido Sul | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.148.0 - 51.140.148.15, 51.140.80.51 |
| Reino Unido Oeste | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 |
||||

## <a name="next-steps"></a>Passos seguintes  

* Saiba como [criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Saiba mais sobre [exemplos e cenários comuns](../logic-apps/logic-apps-examples-and-scenarios.md)
