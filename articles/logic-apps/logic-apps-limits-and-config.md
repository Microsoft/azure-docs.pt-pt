---
title: Limites e configuração
description: Limites de serviço, tais como duração, produção e capacidade, mais valores de configuração, como endereços IP para permitir, para Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: 8e5b43383e0b49c0fe6fffdd9ffee6667fb540f8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054759"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Limites e informações de configuração para o Azure Logic Apps

Este artigo descreve os limites e detalhes de configuração para criar e executar fluxos de trabalho automatizados com Apps Azure Logic. Para automatizar a energia, consulte [limites e configuração em Automatismo de potência](/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="logic-app-definition-limits"></a>Limites de definição de aplicativos lógicos

Aqui estão os limites para uma definição de aplicação lógica única:

| Name | Limite | Notas |
| ---- | ----- | ----- |
| Ações por fluxo de trabalho | 500 | Para estender este limite, pode adicionar fluxos de trabalho aninhados conforme necessário. |
| Permitiu profundidade de nidificação para ações | 8 | Para estender este limite, pode adicionar fluxos de trabalho aninhados conforme necessário. |
| Fluxos de trabalho por região por subscrição | 1,000 | |
| Gatilhos por fluxo de trabalho | 10 | Ao trabalhar na visão de código, não o designer |
| Limite de casos de âmbito de comutação | 25 | |
| Variáveis por fluxo de trabalho | 250 | |
| Nome para `action` ou `trigger` | 80 caracteres | |
| Caracteres por expressão | 8,192 | |
| Comprimento de `description` | 256 caracteres | |
| Número máximo de `parameters` | 50 | |
| Número máximo de `outputs` | 10 | |
| Tamanho máximo para `trackedProperties` | 16.000 caracteres |
| Ação inline Code - Número máximo de caracteres de código | 1.024 caracteres | Para estender este limite a 100.000 caracteres, crie as suas aplicações lógicas com o tipo de recurso **Logic App (Preview),** quer [utilizando o portal Azure,](create-stateful-stateless-workflows-azure-portal.md) quer [utilizando o Código do Estúdio Visual e a extensão **Azure Logic Apps (Preview).**](create-stateful-stateless-workflows-visual-studio-code.md) |
| Ação inline Code - Duração máxima para o código de execução | 5 segundos | Para estender este limite a 15 segundos, crie as suas aplicações lógicas com o tipo de recurso **Logic App (Preview),** [quer utilizando o portal Azure,](create-stateful-stateless-workflows-azure-portal.md) quer [utilizando o Código do Estúdio Visual e a extensão **Azure Logic Apps (Preview).**](create-stateful-stateless-workflows-visual-studio-code.md) |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Prazos de duração e conservação

Aqui estão os limites para uma única aplicação lógica executada:

| Name | Limite de vários inquilinos | Limite de ambiente de serviço de integração | Notas |
|------|--------------------|---------------------------------------|-------|
| Duração da execução | 90 dias | 366 dias | A duração da execução é calculada utilizando a hora de início de uma execução e o limite especificado na definição do fluxo de trabalho, [**Executar a retenção**](#change-duration) do histórico em dias nessa hora de início. <p><p>Para alterar o limite por defeito, consulte [alterar a duração do funcional e a retenção do histórico no armazenamento](#change-duration). |
| Executar retenção de história no armazenamento | 90 dias | 366 dias | Se a duração de uma corrida exceder o limite de retenção do histórico de execução atual, o percurso é removido do histórico de execuções no armazenamento. Quer a execução esteja concluída ou fora do tempo, a retenção do histórico é sempre calculada utilizando o tempo de início da execução e o limite atual especificado na definição do fluxo de trabalho, [**Executar a retenção do histórico em dias**](#change-retention). Independentemente do limite anterior, o limite atual é sempre utilizado para o cálculo da retenção. <p><p>Para alterar o limite de predefinição e para obter mais informações, consulte [alterar a duração e executar a retenção do histórico no armazenamento](#change-retention). Para aumentar o limite máximo, [contacte a equipa da Logic Apps](mailto://logicappsemail@microsoft.com) para obter ajuda com os seus requisitos. |
| Intervalo mínimo de recorrência | 1 segundo | 1 segundo ||
| Intervalo máximo de recorrência | 500 dias | 500 dias ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>Alterar a duração do run e a retenção de histórico no armazenamento

A mesma definição controla o número máximo de dias que um fluxo de trabalho pode executar e para manter o histórico de execução no armazenamento. Para alterar o limite padrão ou atual destas propriedades, siga estes passos.

* Para aplicações lógicas em Azure multi-inquilino, o limite de 90 dias de incumprimento é o mesmo que o limite máximo. Só se pode diminuir este valor.

* Para aplicações lógicas num ambiente de serviço de integração, pode diminuir ou aumentar o limite de 90 dias.

Por exemplo, suponha que reduza o limite de retenção de 90 dias para 30 dias. Uma corrida de 60 dias é removida da história das corridas. Se aumentar o período de retenção de 30 dias para 60 dias, uma corrida de 20 dias fica na história das corridas por mais 40 dias.

> [!IMPORTANT]
> Se a duração de uma corrida exceder o limite de retenção do histórico de execução atual, o percurso é removido do histórico de execuções no armazenamento. Para evitar perder o histórico de execuções, certifique-se de que o limite de retenção é *sempre* mais do que a duração mais longa possível da corrida.

1. Na caixa de pesquisa do [portal Azure,](https://portal.azure.com) encontre e selecione **aplicações Lógicas.**

1. Encontre e selecione a sua aplicação lógica. Abra a sua aplicação lógica no Logic App Designer.

1. No menu da aplicação lógica, selecione **as definições do Fluxo de Trabalho**.

1. Nas **opções runtime**, a partir da retenção de histórico executar na lista **de dias,** selecione **Custom**.

1. Arraste o slider para alterar o número de dias que deseja.

1. Quando terminar, na barra de **ferramentas de definição de fluxo de trabalho,** selecione **Guardar**.

Se gerar um modelo de Gestor de Recursos Azure para a sua aplicação lógica, esta definição aparece como uma propriedade na definição de recursos do seu fluxo de trabalho, que é descrita na referência do [modelo de fluxos de trabalho microsoft.logic](/azure/templates/microsoft.logic/workflows):

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Limites de concurrency, looping e debatching

Aqui estão os limites para uma única aplicação lógica executada:

### <a name="loops"></a>Ciclos

| Name | Limite | Notas |
| ---- | ----- | ----- |
| Itens de matriz de foreach | 100.000 | Este limite descreve o número máximo de itens de matriz que um laço "para cada" pode processar. <p><p>Para filtrar matrizes maiores, pode utilizar a [ação de consulta](logic-apps-perform-data-operations.md#filter-array-action). |
| Conúnva de Foreach | Com concurrency fora: 20 <p><p>Com concordância em: <p><p>- Predefinição: 20 <br>- Min: 1 <br>- Máx: 50 | Este limite é o número máximo de iterações em loop "para cada um" que podem ser executadas ao mesmo tempo, ou paralelamente. <p><p>Para alterar este limite, consulte [alterar o limite de conuncy "para cada um"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) ou executar [sequencialmente os ciclos "para cada um".](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each) |
| Iterações Until | - Predefinição: 60 <br>- Min: 1 <br>- Máx: 5.000 | O número máximo de ciclos que um loop "Até" pode ter durante uma aplicação lógica executada. <p><p>Para alterar este limite, na forma do loop "Até", selecione **Limites de alteração** e especifique o valor da propriedade **Count.** |
| Até o intervalo | - Predefinição: PT1H (1 hora) | O maior número de tempo que o laço "Até" pode ser executado antes de sair e é especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor de tempo limite é avaliado para cada ciclo de ciclo. Se qualquer ação no circuito demorar mais do que o limite de tempo, o ciclo atual não para. No entanto, o próximo ciclo não começa porque a condição limite não está cumprida. <p><p>Para alterar este limite, na forma do loop "Até", selecione **Limites de alteração** e especifique o valor da propriedade **Timeout.** |
||||

### <a name="concurrency-and-debatching"></a>Concurrency e debatching

| Name | Limite | Notas |
| ---- | ----- | ----- |
| Concuência do gatilho | Com concurrency off: Unlimited <p><p>Com concuência, que não pode desfazer depois de permitir: <p><p>- Predefinição: 25 <br>- Min: 1 <br>- Máx: 50 | Este limite é o número máximo de instâncias de aplicações lógicas que podem ser executadas ao mesmo tempo, ou em paralelo. <p><p>**Nota:** Quando a concuência é ligada, o limite SplitOn é reduzido a 100 itens para [debatching](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Para alterar este limite, consulte [alterar o limite de concordância do gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) ou desencadear [casos sequencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Corridas de espera máximas | Com a concordância fora: <p><p>- Min: 1 <br>- Máx: 50 <p><p>Com concordância em: <p><p>- Min: 10 mais o número de execuções simultâneas (concuência do gatilho) <br>- Máx: 100 | Este limite é o número máximo de instâncias de aplicações lógicas que podem esperar para correr quando a sua aplicação lógica já está a executar as instâncias máximas simultâneas. <p><p>Para alterar este limite, consulte [o limite de execuções de espera change](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Itens SplitOn | Com concordância fora: 100.000 <p><p>Com concordância em: 100 | Para os gatilhos que devolvem uma matriz, pode especificar uma expressão que utiliza uma propriedade 'SplitOn' que [divide ou debate itens de matriz em múltiplas instâncias](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) de fluxo de trabalho para processamento, em vez de usar um loop "Foreach". Esta expressão faz referência à matriz a utilizar para criar e executar uma instância de fluxo de trabalho para cada item de matriz. <p><p>**Nota:** Quando a concordância é ligada, o limite SplitOn é reduzido a 100 itens. |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limites de débito

Aqui estão os limites para uma definição de aplicação lógica única:

### <a name="multi-tenant-logic-apps-service"></a>Serviço de Apps Lógicas Multi-inquilinos

| Name | Limite | Notas |
| ---- | ----- | ----- |
| Ação: Execuções por 5 minutos | 100.000 é o limite de incumprimento, mas 300.000 é o limite máximo. | Para elevar o limite padrão ao máximo para a sua aplicação lógica, consulte [Executar em modo de produção elevado](#run-high-throughput-mode), que está em pré-visualização. Ou, pode [distribuir a carga de trabalho por mais do que uma aplicação lógica,](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) se necessário. |
| Ação: Chamadas de saída simultâneas | ~2500 | Pode reduzir o número de pedidos simultâneos ou reduzir a duração, se necessário. |
| Ponto final de tempo de execução: Chamadas de entrada simultâneas | ~1.000 | Pode reduzir o número de pedidos simultâneos ou reduzir a duração, se necessário. |
| Ponto final de tempo de execução: Ler chamadas por 5 minutos  | 60 000 | Este limite aplica-se a chamadas que obtêm as entradas e saídas brutas do histórico de execução de uma aplicação lógica. Pode distribuir a carga de trabalho por mais de uma aplicação, se necessário. |
| Ponto final de tempo de execução: Invocar chamadas por 5 minutos | 45 000 | Pode distribuir carga de trabalho por mais de uma aplicação, se necessário. |
| Produção de conteúdo por 5 minutos | 600 MB | Pode distribuir carga de trabalho por mais de uma aplicação, se necessário. |
||||

<a name="run-high-throughput-mode"></a>

#### <a name="run-in-high-throughput-mode"></a>Correr em modo de produção alta

Para uma definição de aplicação lógica única, o número de ações que executam a cada 5 minutos tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Para elevar o limite padrão ao máximo para a sua aplicação lógica, pode ativar o modo de produção elevado, que está em pré-visualização. Ou, pode [distribuir a carga de trabalho por mais do que uma aplicação lógica,](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) se necessário.

1. No portal Azure, no menu de aplicações lógicas, em **Definições,** selecione **Definições de Fluxo de Trabalho**.

1. In **Runtime ops**  >  **Alta produção**, altere a definição para **On**.

   ![Screenshot que mostra menu de aplicativos lógicos no portal Azure com "Definições de Workflow" e "High Throughput" definido para "On".](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

Para ativar esta definição num modelo ARM para a implementação da sua aplicação lógica, no objeto para a `properties` definição de recursos da sua aplicação lógica, adicione o `runtimeConfiguration` objeto com a propriedade definida para `operationOptions` `OptimizedForHighThroughput` :

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Para obter mais informações sobre a definição de recursos da sua aplicação lógica, consulte [a visão geral: Automatize a implementação para apps Azure Logic utilizando modelos de Gestor de Recursos Azure](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition).

### <a name="integration-service-environment-ise"></a>Ambiente de serviço de integração (ISE)

Aqui estão os limites de produção para o [Premium ISE SKU:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

| Name | Limite | Notas |
|------|-------|-------|
| Limite de execução da unidade de base | Sistema acelerado quando a capacidade de infraestrutura atinge os 80% | Fornece ~4.000 execuções de ação por minuto, que é ~160 milhões de execuções de ação por mês | |
| Limite de execução da unidade de escala | Sistema acelerado quando a capacidade de infraestrutura atinge os 80% | Cada unidade de escala pode fornecer ~2.000 execuções de ação adicionais por minuto, que é ~80 milhões mais execuções de ação por mês | |
| Unidades de escala máxima que pode adicionar | 10 | |
||||

Para ultrapassar estes limites no processamento normal, ou executar testes de carga que possam ir além destes limites, [contacte a equipa da Logic Apps](mailto://logicappsemail@microsoft.com) para obter ajuda com os seus requisitos.

> [!NOTE]
> O [Developer ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) não tem limites publicados, não tem capacidades para aumentar e não tem acordo de nível de serviço (SLA). Utilize este SKU apenas para experimentar, desenvolvimento e testes, não testes de produção ou desempenho.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Limites do gateway

A Azure Logic Apps suporta operações de escrita, incluindo inserções e atualizações, através do gateway. No entanto, estas operações têm [limites no seu tamanho de carga](/data-integration/gateway/service-gateway-onprem#considerations)útil.

<a name="http-limits"></a>

## <a name="http-limits"></a>Limites HTTP

Aqui estão os limites para uma única chamada de entrada ou saída:

<a name="http-timeout-limits"></a>

#### <a name="timeout-duration"></a>Duração do tempo limite

Algumas operações de conector fazem chamadas assíncronos ou ouvem pedidos de webhook, pelo que o tempo limite para estas operações pode ser maior do que estes limites. Para mais informações, consulte os detalhes técnicos do conector específico e também [os gatilhos e ações do Fluxo de Trabalho.](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

| Name | Aplicativos lógicos (multi-inquilino) | Aplicativos lógicos (pré-visualização) | Ambiente de serviço de integração | Notas |
|------|---------------------------|----------------------|---------------------------------|-------|
| Pedido de saída | 120 Segundos <br>(2 minutos) | 230 segundos <br>(3,9 minutos) | 240 segundos <br>(4 minutos) | Exemplos de pedidos de saída incluem chamadas feitas pelo gatilho HTTP ou ação. Para obter mais informações sobre a versão de pré-visualização, consulte [a pré-visualização de Apps Azure Logic](logic-apps-overview-preview.md). <p><p>**Dica**: Para operações mais longas, utilize um [padrão de sondagem assíncronos](../logic-apps/logic-apps-create-api-app.md#async-pattern) ou um [loop até ao loop](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). Para trabalhar em torno de limites de tempo quando você chama outra aplicação lógica que tem um [ponto final chamado,](logic-apps-http-endpoint.md)você pode usar a ação incorporada Azure Logic Apps, que você pode encontrar no conector picker em **incorporado**. |
| Pedido de entrada | 120 Segundos <br>(2 minutos) | 230 segundos <br>(3,9 minutos) | 240 segundos <br>(4 minutos) | Exemplos de pedidos de entrada incluem chamadas recebidas pelo gatilho do pedido, detonador HTTP Webhook e ação HTTP Webhook. Para obter mais informações sobre a versão de pré-visualização, consulte [a pré-visualização de Apps Azure Logic](logic-apps-overview-preview.md). <p><p>**Nota:** Para que o chamador original obtenha a resposta, todos os passos na resposta devem terminar dentro do limite, a menos que chame outra aplicação lógica como um fluxo de trabalho aninhado. Para obter mais informações, consulte [aplicações lógicas Call, Trigger ou Nest.](../logic-apps/logic-apps-http-endpoint.md) |
||||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Tamanho da mensagem

| Name | Limite de vários inquilinos | Limite de ambiente de serviço de integração | Notas |
|------|--------------------|---------------------------------------|-------|
| Tamanho da mensagem | 100 MB | 200 MB | Para contornar este limite, consulte [Handle big messages with chunking](../logic-apps/logic-apps-handle-large-messages.md). No entanto, alguns conectores e APIs podem não suportar a chunking ou mesmo o limite padrão. <p><p>- Conectores como AS2, X12 e EDIFACT têm os seus [próprios limites de mensagem B2B](#b2b-protocol-limits). <br>- Os conectores ISE utilizam o limite ISE, não os seus limites de conector não ISE. |
| Tamanho da mensagem com chunking | 1 GB | 5 GB | Este limite aplica-se a ações que suportam de forma nativa o chunking ou permitem que possassar a sua configuração de tempo de execução. <p><p>Se estiver a utilizar um ISE, o motor Logic Apps suporta este limite, mas os conectores têm os seus próprios limites de rutura até ao limite do motor, por exemplo, consulte a [referência API do conector Azure Blob Storage](/connectors/azureblob/). Para obter mais informações sobre o chunking, consulte [Handle big messages with chunking](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Limites de caracteres

| Name | Notas |
|------|-------|
| Limite de avaliação da expressão | 131 072 carateres | As `@concat()` `@base64()` `@string()` expressões não podem ser mais longas do que este limite. |
| Pedido limite de caracteres URL | 16.384 caracteres |
|||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Política de Repetição

| Name | Limite | Notas |
| ---- | ----- | ----- |
| Tentativas de repetição | 90 | A predefinição é 4. Para alterar o padrão, utilize o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Intervalo máx. de repetição | 1 dia | Para alterar o padrão, utilize o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Intervalo mín. de repetição | 5 segundos | Para alterar o padrão, utilize o [parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Limites de autenticação

Aqui estão os limites para uma aplicação lógica que começa com um gatilho request e permite [a autenticação aberta do Diretório Ativo Azure](../active-directory/develop/index.yml) (Azure AD OAuth) para autorizar chamadas de entrada para o gatilho do Pedido:

| Name | Limite | Notas |
| ---- | ----- | ----- |
| Políticas de autorização Azure AD | 5 | |
| Reclamações por política de autorização | 10 | |
| Valor da reclamação - Número máximo de caracteres | 150 |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limites dos conectores personalizados

Aqui estão os limites para conectores personalizados que pode criar a partir de APIs web.

| Name | Limite de vários inquilinos | Limite de ambiente de serviço de integração | Notas |
|------|--------------------|---------------------------------------|-------|
| Número de conectores personalizados | 1000 por subscrição do Azure | 1000 por subscrição do Azure ||
| Número de pedidos por minuto para um conector personalizado | 500 pedidos por minuto por ligação | 2.000 pedidos por minuto por *conector personalizado* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Identidades geridas

| Name | Limite |
|------|-------|
| Identidades geridas por app lógica | Ou a identidade atribuída ao sistema ou 1 identidade atribuída ao utilizador |
| Número de aplicações lógicas que têm uma identidade gerida numa subscrição do Azure por região | 1,000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limites das contas de integração

Cada subscrição do Azure tem estes limites de conta de integração:

* Uma conta de integração [de nível livre](../logic-apps/logic-apps-pricing.md#integration-accounts) por região de Azure. Este nível só está disponível para regiões públicas em Azure, por exemplo, eua ocidental ou sudeste asiático, mas não para [o Azure China 21Vianet](/azure/china/overview-operations) ou [para o Governo Azure.](../azure-government/documentation-government-welcome.md)

* 1.000 contas totais de integração, incluindo contas de integração em quaisquer [ambientes de serviços de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) tanto em termos de Programadores como [de SKUs Premium.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

* Cada ISE, seja [Developer ou Premium,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)pode utilizar uma única conta de integração sem custos adicionais, embora o tipo de conta incluída varie por ISE SKU. Pode criar mais contas de integração para o seu ISE até ao limite total por um [custo adicional:](logic-apps-pricing.md#fixed-pricing)

  | ISE SKU | Limites das contas de integração |
  |---------|----------------------------|
  | **Premium** | 20 contas totais, incluindo uma conta Standard sem custos adicionais. Com este SKU, só pode ter contas [Standard.](../logic-apps/logic-apps-pricing.md#integration-accounts) Não são permitidas contas Gratuitas ou Básicas. |
  | **Programador** | 20 contas totais, incluindo uma conta [Gratuita](../logic-apps/logic-apps-pricing.md#integration-accounts) (limitada a 1). Com este SKU, pode ter qualquer combinação: <p>- Uma conta gratuita e até 19 contas [Standard.](../logic-apps/logic-apps-pricing.md#integration-accounts) <br>- Sem conta gratuita e até 20 contas Standard. <p>Não são permitidas contas Básicas ou gratuitas. <p><p>**Importante**: Utilize o [Desenvolvedor SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) para experimentar, desenvolvimento e testes, mas não para testes de produção ou desempenho. |
  |||

Para aprender como os preços e a faturação funcionam para as ISEs, consulte o [modelo de preços de Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para taxas de preços, consulte [os preços das Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limites de artefactos por conta de integração

Aqui estão os limites do número de artefactos para cada nível de conta de integração. Para taxas de preços, consulte [os preços das Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/) Para aprender como os preços e faturação funcionam para contas de integração, consulte o [modelo de preços de Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#integration-accounts)

> [!NOTE]
> Utilize o nível Free apenas para cenários exploratórios, não para cenários de produção. Este nível restringe a produção e a utilização, e não tem nenhum acordo de nível de serviço (SLA).

| Artefacto | Gratuito | Básico | Standard |
|----------|------|-------|----------|
| Acordos de negociação do EDI | 10 | 1 | 1,000 |
| Parceiros comerciais EDI | 25 | 2 | 1,000 |
| Mapas | 25 | 500 | 1,000 |
| Esquemas | 25 | 500 | 1,000 |
| Assemblagens | 10 | 25 | 1,000 |
| Certificados | 25 | 2 | 1,000 |
| Configurações de lote | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limites de capacidade do artefacto

| Artefacto | Limite | Notas |
| -------- | ----- | ----- |
| Assemblagem | 8 MB | Para carregar ficheiros maiores do que 2 MB, utilize uma [conta de armazenamento Azure e um recipiente blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Mapa (ficheiro XSLT) | 8 MB | Para carregar ficheiros maiores do que 2 MB, utilize as [Aplicações AZure Logic REST API - Mapas](/rest/api/logic/maps/createorupdate). <p><p>**Nota:** A quantidade de dados ou registos que um mapa pode processar com sucesso baseia-se no tamanho da mensagem e nos limites de tempo de ação nas Apps Azure Logic. Por exemplo, se utilizar uma ação HTTP, com base no [tamanho da mensagem HTTP e nos limites de tempo,](#http-limits)um mapa pode processar dados até ao limite do tamanho da mensagem HTTP se a operação estiver concluída dentro do prazo limite HTTP. |
| Esquema | 8 MB | Para carregar ficheiros maiores do que 2 MB, utilize uma [conta de armazenamento Azure e um recipiente blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Limites de débito

| Ponto final de tempo de execução | Gratuito | Básico | Standard | Notas |
|------------------|------|-------|----------|-------|
| Ler chamadas por 5 minutos | 3.000 | 30,000 | 60 000 | Este limite aplica-se a chamadas que obtêm as entradas e saídas brutas do histórico de execução de uma aplicação lógica. Pode distribuir a carga de trabalho por mais de uma conta, se necessário. |
| Invocar chamadas por 5 minutos | 3.000 | 30,000 | 45 000 | Pode distribuir a carga de trabalho por mais de uma conta, se necessário. |
| Chamadas de rastreio por 5 minutos | 3.000 | 30,000 | 45 000 | Pode distribuir a carga de trabalho por mais de uma conta, se necessário. |
| Bloquear chamadas simultâneas | ~1.000 | ~1.000 | ~1.000 | O mesmo para todos os SKUs. Pode reduzir o número de pedidos simultâneos ou reduzir a duração, se necessário. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Protocolo B2B (AS2, X12, EDIFACT) tamanho da mensagem

Aqui estão os limites de tamanho da mensagem que se aplicam aos protocolos B2B:

| Name | Limite de vários inquilinos | Limite de ambiente de serviço de integração | Notas |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 25 MB | v2 - 200 MB <br>v1 - 25 MB | Aplica-se para descodificar e codificar |
| X12 | 50 MB | 50 MB | Aplica-se para descodificar e codificar |
| EDIFACT | 50 MB | 50 MB | Aplica-se para descodificar e codificar |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Desativar ou eliminar aplicações lógicas

Quando desativa uma aplicação lógica, não há novas execuções instantâneas. Todas as corridas em curso e pendentes continuam até terminarem, o que pode levar algum tempo a ser concluído.

Quando elimina uma aplicação lógica, não são instanciadas novas execuções. Todas as execuções em curso e pendentes são canceladas. Se tiver milhares de execuções, o cancelamento pode demorar muito tempo a concluir.

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Configuração de firewall: endereços IP e tags de serviço

Quando a sua aplicação lógica precisa de comunicar através de uma firewall que limite o tráfego a endereços IP específicos, essa firewall precisa de permitir o acesso *tanto para os* endereços IP de [entrada](#inbound) como [para saída](#outbound) utilizados pelo serviço De aplicações lógicas ou tempo de execução na região de Azure onde existe a sua aplicação lógica. *Todas as* aplicações lógicas da mesma região utilizam os mesmos intervalos de endereços IP.

Por exemplo, para apoiar chamadas que aplicações lógicas na região oeste dos EUA enviam ou recebem através de gatilhos e ações incorporadas, como o [trigger http ou ação](../connectors/connectors-native-http.md), a sua firewall precisa de permitir o acesso a *todos os* endereços IP de entrada de serviço de Aplicações Lógicas *e* endereços IP de saída que existem na região oeste dos EUA.

Se a sua aplicação lógica também utilizar [conectores geridos](../connectors/apis-list.md#managed-api-connectors)– como o conector Office 365 Outlook ou o conector SQL, ou utilizar [conectores personalizados](/connectors/custom-connectors/)– a firewall também precisa de permitir o acesso de *todos os* [endereços IP de saída geridos](#outbound) do conector na região Azure da sua aplicação lógica. Além disso, se utilizar conectores personalizados que acedam aos recursos no local através do [recurso de gateway de dados no local em Azure,](logic-apps-gateway-connection.md)é necessário configurar a instalação gateway para permitir o acesso aos *[respetivos conectores](#outbound)geridos endereços IP de saída.*

Para obter mais informações sobre a configuração das definições de comunicação no gateway, consulte estes tópicos:

* [Ajustar as definições de comunicação do gateway de dados no local](/data-integration/gateway/service-gateway-communication)
* [Configurar as definições de proxy do gateway de dados no local](/data-integration/gateway/service-gateway-proxy)

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>Considerações de configuração IP de firewall

Antes de configurar a sua firewall com endereços IP, reveja estas considerações:

* Se estiver a utilizar [o Power Automamate](/power-automate/getting-started), algumas ações, como **HTTP** e HTTP **+ OpenAPI,** aceda diretamente ao serviço Azure Logic Apps e venha dos endereços IP listados aqui. Para obter mais informações sobre os endereços IP utilizados pela Power Automamate, consulte [Limites e configuração para Automatizar energia](/flow/limits-and-config#ip-address-configuration).

* Para [o Azure China 21Vianet,](/azure/china/)os endereços IP fixos ou reservados não estão disponíveis para [conectores personalizados](../logic-apps/custom-connector-overview.md) e para [conectores geridos](../connectors/apis-list.md#managed-api-connectors), tais como Azure Storage, SQL Server, Office 365 Outlook, e assim por diante.

* Se as suas aplicações lógicas funcionarem num [ambiente de serviço de integração (ISE),](connect-virtual-network-vnet-isolated-environment-overview.md)certifique-se de que também abre estas [portas](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Para ajudá-lo a simplificar quaisquer regras de segurança que pretenda criar, pode utilizar opcionalmente [tags](../virtual-network/service-tags-overview.md) de serviço em vez de especificar prefixos de endereço IP para cada região. Estas etiquetas funcionam em todas as regiões onde o serviço De aplicações lógicas está disponível:

  * **LogicAppsManagement**: Representa os prefixos de endereço IP de entrada para o serviço De aplicações lógicas.

  * **LogicApps**: Representa os prefixos de endereço IP de saída para o serviço De aplicações lógicas.

  * **AzureConnectors**: Representa os prefixos de endereço IP para conectores geridos que fazem chamadas webhook de entrada para o serviço De aplicações lógicas e chamadas de saída para os seus respetivos serviços, tais como Azure Storage ou Azure Event Hubs.

* Se as suas aplicações lógicas tiverem problemas em aceder a contas de armazenamento Azure que utilizam [firewalls e regras de firewall,](../storage/common/storage-network-security.md)tem [várias outras opções para permitir o acesso.](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

  Por exemplo, as aplicações lógicas não podem aceder diretamente a contas de armazenamento que usam regras de firewall e existem na mesma região. No entanto, se permitir os [endereços IP de saída para conectores geridos na sua região,](../logic-apps/logic-apps-limits-and-config.md#outbound)as suas aplicações lógicas podem aceder a contas de armazenamento que se encontrem numa região diferente, exceto quando utilizar os conectores Azure Table Storage ou Azure Queue Storage. Para aceder ao armazenamento de mesa ou ao armazenamento de filas, pode utilizar o gatilho HTTP e as ações. Para outras opções, consulte [as contas de armazenamento do Access por trás de firewalls](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Endereços IP de entrada

Esta secção lista os endereços IP de entrada apenas para o serviço Azure Logic Apps. Se tiver o Governo Azure, consulte o [Governo Azure - Endereços IP de entrada](#azure-government-inbound).

> [!TIP]
> Para ajudar a reduzir a complexidade ao criar regras de segurança, pode utilizar opcionalmente a etiqueta de [serviço](../virtual-network/service-tags-overview.md), **LogicAppsManagement**, em vez de especificar prefixos de endereço IP de aplicações lógicas de entrada para cada região. Opcionalmente, também pode utilizar a etiqueta de serviço **AzureConnectors** para conectores geridos que fazem chamadas webhook de entrada para o serviço De aplicações lógicas, em vez de especificar prefixos de endereço IP geridos por inbound para cada região. Estas etiquetas funcionam em todas as regiões onde o serviço De Aplicações Lógicas está disponível.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Multi-inquilinoS Azure - Endereços IP de entrada

| Região multi-arrendatário | IP |
|---------------------|----|
| Leste da Austrália | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Austrália Sudeste | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Sul do Brasil | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Canadá Central | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Leste do Canadá | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Índia Central | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| E.U.A. Central | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Ásia Leste | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| E.U.A. Leste | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| E.U.A. Leste 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| França Central | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Sul de França | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Alemanha Norte | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Alemanha Centro-Oeste | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0 |
| Leste do Japão | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Oeste do Japão | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Coreia do Sul Central | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Sul da Coreia do Sul | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| E.U.A. Centro-Norte | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europa do Norte | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Leste da Noruega | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| Norte da África do Sul | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Oeste da África do Sul | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| E.U.A. Centro-Sul | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Sul da Índia | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Sudeste Asiático | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Suíça Norte | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Suíça Oeste | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| Centro dos Emirados Árabes Unidos | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Uae Norte | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| Sul do Reino Unido | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Oeste do Reino Unido | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| E.U.A. Centro-Oeste | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa Ocidental | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Oeste da Índia | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| E.U.A. Oeste | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| E.U.A. Oeste 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Governo Azure - Endereços IP de entrada

| Região do Governo de Azure | IP |
|-------------------------|----|
| US Gov - Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov - Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Gov - Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD Centro | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Endereços IP de saída

Esta secção lista os endereços IP de saída para o serviço Azure Logic Apps e conectores geridos. Se tiver o Governo Azure, consulte [o Governo Azure - Endereços IP de saída](#azure-government-outbound).

> [!TIP]
> Para ajudar a reduzir a complexidade ao criar regras de segurança, pode utilizar opcionalmente a etiqueta de [serviço](../virtual-network/service-tags-overview.md), **LogicApps,** em vez de especificar prefixos ip de endereços ip de aplicações lógicas de saída para cada região. Opcionalmente, também pode utilizar a etiqueta de serviço **AzureConnectors** para conectores geridos que fazem chamadas de saída para os seus respetivos serviços, como O Azure Storage ou Azure Event Hubs, em vez de especificar prefixos de endereço IP geridos de saída para cada região. Estas etiquetas funcionam em todas as regiões onde o serviço De Aplicações Lógicas está disponível.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Multi-inquilinoS Azure - Endereços IP de saída

| Região multi-arrendatário | Aplicativos lógicos IP | Conectores geridos IP |
|---------------------|---------------|-----------------------|
| Leste da Austrália | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 52.237.214.72, 13.72.243.10, 13.70.72.192 - 13.70.72.207, 13.70.78.224 - 13.70.78.255 |
| Austrália Sudeste | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 52.255.48.202, 13.70.136.174, 13.77.50.240 - 13.77.50.255, 13.77.55.160 - 13.77.55.191 |
| Sul do Brasil | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 191.232.191.157, 104.41.59.51, 191.233.203.192 - 191.233.203.207, 191.233.207.160 - 191.233.207.191 |
| Canadá Central | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 52.237.32.212, 52.237.24.126, 13.71.170.208 - 13.71.170.223, 13.71.175.160 - 13.71.175.191 |
| Leste do Canadá | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 52.242.30.112, 52.242.35.152, 40.69.106.240 - 40.69.106.255, 40.69.111.0 - 40.69.111.31 |
| Índia Central | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.212.129, 52.172.211.12, 20.43.123.0 - 20.43.123.31, 104.211.81.192 - 104.211.81.207 |
| E.U.A. Central | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 52.173.241.27, 52.173.245.164, 13.89.171.80 - 13.89.171.95, 13.89.178.64 - 13.89.178.95 |
| Ásia Leste | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.110.131, 52.175.23.169, 13.75.36.64 - 13.75.36.79, 104.214.164.0 - 104.214.164.31 |
| E.U.A. Leste | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.249.139, 40.71.249.205, 40.114.40.132, 40.71.11.80 - 40.71.11.95, 40.71.15.160 - 40.71.15.191 |
| E.U.A. Leste 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 52.225.129.144, 52.232.188.154, 104.209.247.23, 40.70.146.208 - 40.70.146.223, 40.70.151.96 - 40.70.151.127 |
| França Central | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.89.186.239, 40.89.135.2, 40.79.130.208 - 40.79.130.223, 40.79.148.96 - 40.79.148.127 |
| Sul de França | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.142.154, 52.136.133.184, 40.79.178.240 - 40.79.178.255, 40.79.180.224 - 40.79.180.255 |
| Alemanha Norte | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 | 51.116.60.192, 51.116.211.212, 51.116.59.16 - 51.116.59.31, 51.116.60.192 - 51.116.60.223 |
| Alemanha Centro-Oeste | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51 | 51.116.158.97, 51.116.236.78, 51.116.155.80 - 51.116.155.95, 51.116.158.96 - 51.116.158.127 |
| Leste do Japão | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.73.21.230, 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.79.189.64 - 40.79.189.95 |
| Oeste do Japão | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 104.215.27.24, 104.215.61.248, 40.74.100.224 - 40.74.100.239, 40.80.180.64 - 40.80.180.95 |
| Coreia do Sul Central | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.1.104, 52.141.36.214, 20.44.29.64 - 20.44.29.95, 52.231.18.208 - 52.231.18.223 |
| Sul da Coreia do Sul | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.201.173, 52.231.163.10, 52.231.147.0 - 52.231.147.15, 52.231.148.224 - 52.231.148.255 |
| E.U.A. Centro-Norte | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.126.4, 52.162.242.161, 52.162.107.160 - 52.162.107.175, 52.162.111.192 - 52.162.111.223 |
| Europa do Norte | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 52.169.28.181, 52.178.150.68, 94.245.91.93, 13.69.227.208 - 13.69.227.223, 13.69.231.192 - 13.69.231.223 |
| Leste da Noruega | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 | 51.120.100.192, 51.120.92.27, 51.120.98.224 - 51.120.98.239, 51.120.100.192 - 51.120.100.223 |
| Norte da África do Sul | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31 |
| Oeste da África do Sul | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15 |
| E.U.A. Centro-Sul | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63 |
| Sul da Índia | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255 |
| Sudeste Asiático | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63 |
| Suíça Norte | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255 |
| Suíça Oeste | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 | 51.107.156.224, 51.107.231.190, 51.107.155.16 - 51.107.155.31, 51.107.156.224 - 51.107.156.255 |
| Centro dos Emirados Árabes Unidos | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| Uae Norte | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 | 65.52.250.208, 40.123.224.120, 40.120.64.64 - 40.120.64.95, 65.52.250.208 - 65.52.250.223 |
| Sul do Reino Unido | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15 |
| Oeste do Reino Unido | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255 |
| E.U.A. Centro-Oeste | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223 |
| Europa Ocidental | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223 |
| Oeste da Índia | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239 |
| E.U.A. Oeste | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175 |
| E.U.A. Oeste 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Governo Azure - Endereços IP de saída

| Region | Aplicativos lógicos IP | Conectores geridos IP |
|--------|---------------|-----------------------|
| US DoD Centro | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223 |
| US Gov - Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255 |
| US Gov - Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159 |
| US Gov - Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* Conheça os [exemplos e cenários comuns](../logic-apps/logic-apps-examples-and-scenarios.md)
