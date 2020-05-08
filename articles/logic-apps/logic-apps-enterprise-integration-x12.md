---
title: Enviar e receber mensagens X12 para B2B
description: Trocar mensagens X12 para cenários de integração empresarial B2B utilizando aplicações da Azure Logic com pacote de integração empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 8ec20e03544ba54b83130ae41244dcdb186252d0
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613091"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens X12 para integração empresarial B2B em Aplicações Lógicas Azure com Pacote de Integração Empresarial

Para trabalhar com mensagens X12 em Aplicações Lógicas Azure, pode utilizar o conector X12, que fornece gatilhos e ações para gerir a comunicação X12. Para obter informações sobre mensagens EDIFACT, consulte [exchange EDIFACT messages](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma assinatura Azure, [inscreva-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* A aplicação lógica de onde pretende utilizar o conector X12 e um gatilho que inicia o fluxo de trabalho da sua aplicação lógica. O conector X12 fornece apenas ações, não gatilhos. Se você é novo em aplicações lógicas, reveja [o que são As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que está associada à sua subscrição Azure e ligada à aplicação lógica onde planeia utilizar o conector X12. Tanto a sua aplicação lógica como a sua conta de integração devem existir no mesmo local ou na região de Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que já definiu na sua conta de integração utilizando o apuramento de identidade X12.

* Os [esquemas](../logic-apps/logic-apps-enterprise-integration-schemas.md) a utilizar para validação XML que já adicionou à sua conta de integração. Se estiver a trabalhar com schemas da Health Insurance Portability and Accountability Act (HIPAA), consulte [os esquemas hipaa](#hipaa-schemas).

* Antes de poder utilizar o conector X12, tem de criar um [acordo](../logic-apps/logic-apps-enterprise-integration-agreements.md) X12 entre os seus parceiros comerciais e armazenar esse acordo na sua conta de integração. Se está a trabalhar com squemas da Health Insurance Portability and Accountability `schemaReferences` Act (HIPAA), precisa adicionar uma secção ao seu acordo. Para mais informações, consulte [os esquemas hipaa](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Receber Definições

Depois de definir as propriedades do contrato, pode configurar como este acordo identifica e lida com mensagens de entrada que recebe do seu parceiro através deste acordo.

1. Em **'Adicionar',** **selecione 'Receber Definições**' .

1. Configure estas propriedades com base no seu acordo com o parceiro que troca mensagens consigo. As **Definições de Receção** estão organizadas nestas secções:

   * [Identificadores](#inbound-identifiers)
   * [Reconhecimento](#inbound-acknowledgement)
   * [Rio Schemas](#inbound-schemas)
   * [Envelopes](#inbound-envelopes)
   * [Números de Controlo](#inbound-control-numbers)
   * [Validações](#inbound-validations)
   * [Definições Internas](#inbound-internal-settings)

   Para descrições de propriedades, consulte as tabelas nesta secção.

1. Quando terminar, certifique-se de guardar as definições selecionando **OK**.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Receber Configurações - Identificadores

![Propriedades de identificador para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Propriedade | Descrição |
|----------|-------------|
| **ISA1 (Qualificação de Autorização)** | O valor de qualificação de autorização que pretende utilizar. O valor predefinido é **00 - Sem informação de autorização presente.** <p>**Nota:** Se selecionar outros valores, especifique um valor para a propriedade **ISA2.** |
| **ISA2** | O valor da Informação de Autorização a utilizar quando o imóvel **ISA1** não é **00 - Nenhuma informação**de autorização presente. Este valor de propriedade deve ter um mínimo de um caráter alfanumérico e um máximo de 10. |
| **ISA3 (Qualificação de Segurança)** | O valor de qualificação de segurança que pretende utilizar. O valor predefinido é **00 - Sem informações de segurança presentes**. <p>**Nota:** Se selecionar outros valores, especifique um valor para a propriedade **ISA4.** |
| **ISA4** | O valor da Informação de Segurança a utilizar quando a propriedade **ISA3** não é **00 - Nenhuma informação**de segurança presente . Este valor de propriedade deve ter um mínimo de um caráter alfanumérico e um máximo de 10. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Receber Definições - Reconhecimento

![Reconhecimento de mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Propriedade | Descrição |
|----------|-------------|
| **TA1 Esperado** | Devolva um reconhecimento técnico (TA1) ao remetente de intercâmbio. |
| **Fa Esperado** | Devolva um reconhecimento funcional (FA) ao remetente de intercâmbio. <p>Para a propriedade **Fa Version,** com base na versão schema, selecione os reconhecimentos 997 ou 999. <p>Para permitir a geração de loops AK2 em agradecimentos funcionais para conjuntos de transações aceites, **selecione Incluir AK2 / IK2 Loop**. |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Receber Definições - Schemas

![Esquemios para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

Para esta secção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da sua conta de [integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) para cada tipo de transação (ST01) e Aplicação Sender (GS02). O EdI Receive Pipeline desmonta a mensagem de entrada combinando os valores e esquemas que definiu nesta secção com os valores para ST01 e GS02 na mensagem de entrada e com o esquema da mensagem de entrada. Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

| Propriedade | Descrição |
|----------|-------------|
| **Versão** | A versão X12 para o esquema |
| **Tipo de transação (ST01)** | O tipo de transação |
| **Aplicação sender (GS02)** | A aplicação do remetente |
| **Esquema** | O ficheiro de esquemas que quer usar |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Receber Configurações - Envelopes

![Separadores a utilizar em conjuntos de transações para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Propriedade | Descrição |
|----------|-------------|
| **Usáldo ISA11** | O separador a utilizar num conjunto de transações: <p>- **Identificador padrão**: Utilize um período (.) para notação decimal, em vez da notação decimal do documento de entrada no Pipeline edi receive. <p>- **Separador de repetição**: Especifique o separador para ocorrências repetidas de um simples elemento de dados ou de uma estrutura de dados repetida. Por exemplo, normalmente o quilate (^) é usado como separador de repetição. Para os esquemas hipaa, só pode usar o quilate. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Receber Configurações - Números de Controlo

![Duplicado do número de controlo de manuseamento para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Propriedade | Descrição |
|----------|-------------|
| **Duplicados de númerode controlo de intercâmbio despermitidos** | Bloqueie trocas duplicadas. Verifique o número de controlo de permuta (ISA13) para ver o número de controlo de permuta recebido. Se for detetada uma correspondência, o Pipeline EDI Receive não processa o intercâmbio. <p><p>Para especificar o número de dias para efetuar o cheque, insira um valor para o **Check for duplicado ISA13 todos os (dias)** propriedade. |
| **Duplicados de número de controlo de grupo desabotoados** | Trocas de blocos que têm números duplicados de controlo de grupo. |
| **Duplicados de número de controlo de conjunto de transações desadmissidos** | Trocas de blocos que tenham duplicado números de controlo definidos de transação. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Receber Configurações - Validações

![Validações para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

A linha **Predefinido** mostra as regras de validação que são utilizadas para um tipo de mensagem EDI. Se quiser definir regras diferentes, selecione cada caixa onde quiser que a regra seja definida como **verdadeira**. Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

| Propriedade | Descrição |
|----------|-------------|
| **Tipo de mensagem** | O tipo de mensagem EDI |
| **Validação EDI** | Execute a validação EDI em tipos de dados definidos pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores de rasto. |
| **Validação Alargada** | Se o tipo de dados não for EDI, a validação está no requisito do elemento de dados e permitiu a repetição, enumerações e validação do comprimento do elemento de dados (min ou máx). |
| **Permitir zeros de ponta/rasto** | Mantenha quaisquer caracteres adicionais de liderança ou de fuga zero e espaciais. Não remova estes caracteres. |
| **Guarnição de pontos de liderança/trailing** | Remova quaisquer caracteres de liderança ou de rasto zero e espaço. |
| **Política de Separador de Trailing** | Gerar separadores de rastos. <p>- **Não É Permitido**: Proibir delimitar e separadores no cruzamento de entrada. Se o intercâmbio tiver delimitadores e separadores, o intercâmbio é declarado inválido. <p>- **Opcional**: Aceitar intercâmbios com ou sem delimitadores e separadores. <p>- **Obrigatório**: O intercâmbio de entrada deve ter delimitadores e separadores. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Receber Configurações - Configurações Internas

![Definições internas para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Propriedade | Descrição |
|----------|-------------|
| **Converter o formato decimal implícito Nn para um valor numérico base 10** | Converta um número EDI especificado com o formato "Nn" num valor numérico base-10. |
| **Crie etiquetas XML vazias se forem permitidos separadores de rasto** | O remetente de intercâmbio inclui etiquetas XML vazias para os separadores de rasto. |
| **Split Interchange como conjuntos de transações - suspender conjuntos de transações por erro** | Analisar cada conjunto de transações que se inscreva num intercâmbio num documento Separado XML aplicando o envelope apropriado ao conjunto de transações. Suspender apenas as transações em caso de falha na validação. |
| **Intercâmbio dividido como conjuntos de transações - suspender o intercâmbio por erro** | Analisar cada conjunto de transações que se inscreva num intercâmbio num documento Separado XML aplicando o envelope apropriado. Suspender todo o intercâmbio quando um ou mais conjuntos de transações na validação de falhas de troca. |
| **Preserve Interchange - suspender conjuntos de transações por erro** | Deixe o intercâmbio intacto e crie um documento XML para todo o intercâmbio em lotado. Suspender apenas os conjuntos de transações que falham na validação, mas continuam a processar todos os outros conjuntos de transações. |
| **Preserve Interchange - suspender o intercâmbio por erro** |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lotado. Suspende todo o intercâmbio quando uma ou mais transações se estabelecem na validação de falhas de troca. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Enviar Definições

Depois de definir as propriedades do contrato, pode configurar como este acordo identifica e lida com mensagens de saída que envia ao seu parceiro através deste acordo.

1. Em **adicionar,** selecione **'Enviar Definições**' .

1. Configure estas propriedades com base no seu acordo com o parceiro que troca mensagens consigo. Para descrições de propriedades, consulte as tabelas nesta secção.

   As **Definições de Envio** estão organizadas nestas secções:

   * [Identificadores](#outbound-identifiers)
   * [Reconhecimento](#outbound-acknowledgement)
   * [Rio Schemas](#outbound-schemas)
   * [Envelopes](#outbound-envelopes)
   * [Número de versão de controlo](#outbound-control-version-number)
   * [Números de Controlo](#outbound-control-numbers)
   * [Conjuntos de caracteres e separadores](#outbound-character-sets-separators)
   * [Validação](#outbound-validation)

1. Quando terminar, certifique-se de guardar as definições selecionando **OK**.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Enviar Definições - Identificadores

![Propriedades de identificador para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Propriedade | Descrição |
|----------|-------------|
| **ISA1 (Qualificação de Autorização)** | O valor de qualificação de autorização que pretende utilizar. O valor predefinido é **00 - Sem informação de autorização presente.** <p>**Nota:** Se selecionar outros valores, especifique um valor para a propriedade **ISA2.** |
| **ISA2** | O valor da Informação de Autorização a utilizar quando o imóvel **ISA1** não é **00 - Nenhuma informação**de autorização presente. Este valor de propriedade deve ter um mínimo de um caráter alfanumérico e um máximo de 10. |
| **ISA3 (Qualificação de Segurança)** | O valor de qualificação de segurança que pretende utilizar. O valor predefinido é **00 - Sem informações de segurança presentes**. <p>**Nota:** Se selecionar outros valores, especifique um valor para a propriedade **ISA4.** |
| **ISA4** | O valor da Informação de Segurança a utilizar quando a propriedade **ISA3** não é **00 - Nenhuma informação**de segurança presente . Este valor de propriedade deve ter um mínimo de um caráter alfanumérico e um máximo de 10. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Enviar Definições - Reconhecimento

![Propriedades de reconhecimento para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Propriedade | Descrição |
|----------|-------------|
| **TA1 Esperado** | Devolva um reconhecimento técnico (TA1) ao remetente de intercâmbio. <p>Esta definição especifica que o parceiro anfitrião, que está a enviar a mensagem, solicita um reconhecimento do parceiro convidado no acordo. Estes agradecimentos são esperados pelo parceiro anfitrião com base nas Definições de Receção do acordo. |
| **Fa Esperado** | Devolva um reconhecimento funcional (FA) ao remetente de intercâmbio. Para a propriedade **Fa Version,** com base na versão schema, selecione os agradecimentos 997 ou 999. <p>Esta definição especifica que o parceiro anfitrião, que está a enviar a mensagem, solicita um reconhecimento do parceiro convidado no acordo. Estes agradecimentos são esperados pelo parceiro anfitrião com base nas Definições de Receção do acordo. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Definições de envio - Schemas

![Esquemios para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

Para esta secção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da sua conta de [integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) para cada tipo de transação (ST01). Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

| Propriedade | Descrição |
|----------|-------------|
| **Versão** | A versão X12 para o esquema |
| **Tipo de transação (ST01)** | O tipo de transação para o esquema |
| **Esquema** | O ficheiro de esquemas que quer usar. Se selecionar primeiro o esquema, a versão e o tipo de transação são automaticamente definidos. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Enviar Definições - Envelopes

![Separadores numa transação definida para utilização de mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Propriedade | Descrição |
|----------|-------------|
| **Usáldo ISA11** | O separador a utilizar num conjunto de transações: <p>- **Identificador padrão**: Utilize um período (.) para notação decimal, em vez da notação decimal do documento de saída no Pipeline de envio EDI. <p>- **Separador de repetição**: Especifique o separador para ocorrências repetidas de um simples elemento de dados ou de uma estrutura de dados repetida. Por exemplo, normalmente o quilate (^) é usado como separador de repetição. Para os esquemas hipaa, só pode usar o quilate. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Enviar Definições - Número de Versão de Controlo

![Número da versão de controlo para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

Para esta secção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da sua conta de [integração](../logic-apps/logic-apps-enterprise-integration-accounts.md) para cada intercâmbio. Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

| Propriedade | Descrição |
|----------|-------------|
| **Número da versão de controlo (ISA12)** | A versão do padrão X12 |
| **Indicador de utilização (ISA15)** | O contexto de um intercâmbio, que é ou **dados de teste,** dados **de informação** ou dados de **produção** |
| **Esquema** | O esquema a utilizar para gerar os segmentos GS e ST para um intercâmbio codificado x12 que é enviado para o Pipeline EDI Send. |
| **GS1** | Opcional, selecione o código funcional. |
| **GS2** | Opcional, especifique o remetente da aplicação. |
| **GS3** | Opcional, especifique o recetor de aplicação. |
| **GS4** | Opcional, selecione **CCYYMMDD** ou **YYMMDD**. |
| **GS5** | Opcional, selecione **HHMM,** **HHMMSS,** ou **HHMMSSdd**. |
| **GS7** | Opcional, selecione um valor para a agência responsável. |
| **GS8** | Opcional, especifique a versão do documento schema. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Configurações de envio - Números de controlo

![Números de controlo para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Propriedade | Descrição |
|----------|-------------|
| **Número de controlo de intercâmbio (ISA13)** | A gama de valores para o número de controlo de intercâmbio, que pode ter um mínimo de valor 1 e um valor máximo de 9999999999 |
| **Número de controlo de grupo (GS06)** | A gama de valores para o número de controlo do grupo, que pode ter um valor mínimo de 1 e um valor máximo de 9999999999 |
| **Número de controlo definido de transação (ST02)** | A gama de valores para o número de controlo definido pela transação, que pode ter um valor mínimo de 1 e um valor máximo de 9999999999 <p>- **Prefixo**: Opcional, um valor alfanumérico <br>- **Sufixo**: Opcional, um valor alfanumérico |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Enviar Definições - Conjuntos de caracteres e separadores

![Delimitadores para tipos de mensagens em mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

A linha **Padrão** mostra o conjunto de caracteres que é usado como delimitadores para um esquema de mensagem. Se não quiser utilizar o conjunto de caracteres **Padrão,** pode introduzir um conjunto diferente de delimitadores para cada tipo de mensagem. Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

> [!TIP]
> Para fornecer valores especiais de carácter, edite o acordo como JSON e forneça o valor ASCII para o caráter especial.

| Propriedade | Descrição |
|----------|-------------|
| **Conjunto de caracteres a ser usado** | O conjunto de caracteres X12, que é **básico,** **estendido**ou **UTF8**. |
| **Esquema** | O esquema que quer usar. Depois de selecionar o esquema, selecione o conjunto de caracteres que pretende utilizar, com base nas descrições do separador abaixo. |
| **Tipo de entrada** | O tipo de entrada para o conjunto de caracteres |
| **Separador de componentes** | Um único personagem que separa elementos de dados compósitos |
| **Separador de elementos de dados** | Um único personagem que separa elementos de dados simples dentro de dados compósitos |
| **separador de caracteres de substituição** | Um personagem de substituição que substitui todos os caracteres separadores nos dados de carga útil ao gerar a mensagem X12 de saída |
| **Exterminador de Segmento** | Um único personagem que indica o fim de um segmento EDI |
| **Sufixo** | O personagem a utilizar com o identificador de segmento. Se especificar um sufixo, o elemento de dados do exterminador do segmento pode estar vazio. Se o exterminador do segmento ficar vazio, deve designar um sufixo. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Enviar Definições - Validação

![Propriedades de validação para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

A linha **Predefinido** mostra as regras de validação que são utilizadas para um tipo de mensagem EDI. Se quiser definir regras diferentes, selecione cada caixa onde quiser que a regra seja definida como **verdadeira**. Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

| Propriedade | Descrição |
|----------|-------------|
| **Tipo de mensagem** | O tipo de mensagem EDI |
| **Validação EDI** | Execute a validação EDI em tipos de dados definidos pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores de rasto. |
| **Validação Alargada** | Se o tipo de dados não for EDI, a validação está no requisito do elemento de dados e permitiu a repetição, enumerações e validação do comprimento do elemento de dados (min ou máx). |
| **Permitir zeros de ponta/rasto** | Mantenha quaisquer caracteres adicionais de liderança ou de fuga zero e espaciais. Não remova estes caracteres. |
| **Guarnição de pontos de liderança/trailing** | Remova quaisquer caracteres de liderança ou de rasto zero e espaço. |
| **Política de Separador de Trailing** | Gerar separadores de rastos. <p>- **Não É Permitido**: Proibir delimitar e separar no intercâmbio de saída. Se o intercâmbio tiver delimitadores e separadores, o intercâmbio é declarado inválido. <p>- **Opcional:** Envie intercâmbios com ou sem delimitadores e separadores. <p>- **Obrigatório**: O intercâmbio de saída deve ter delimitadores e separadores. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>Esquemas de hipaa e tipos de mensagens

Quando trabalha com schemas HIPAA e os tipos de mensagens 277 ou 837, precisa de realizar alguns passos extras. Os números da [versão documental (GS8)](#outbound-control-version-number) para estes tipos de mensagens têm mais de 9 caracteres, por exemplo, "005010X222A1". Além disso, alguns números de versão documental mapeiam para tipos de mensagens variantes. Se não fizer referência ao tipo de mensagem correto no seu esquema e no seu acordo, obtém esta mensagem de erro:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

Esta tabela lista os tipos de mensagens afetados, quaisquer variantes e os números da versão documental que mapeiam para esses tipos de mensagens:

| Tipo de mensagem ou variante |  Descrição | Número da versão do documento (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Notificação do Estado da Informação sobre Cuidados de Saúde | 005010X212 |
| 837_I | Pedido de cuidados de saúde dentário | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Reivindicação institucional de cuidados de saúde | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Profissionais de Reclamação de Cuidados de Saúde | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

Também precisa de desativar a validação EDI quando utiliza estes números de versão documental porque resultam num erro de que o comprimento do carácter é inválido.

Para especificar estes números de versão documental e tipos de mensagens, siga estes passos:

1. No seu esquema HIPAA, substitua o tipo de mensagem atual pelo tipo de mensagem variante para o número da versão do documento que pretende utilizar.

   Por exemplo, suponha que `005010X222A1` pretenda `837` utilizar o número da versão do documento com o tipo de mensagem. No seu esquema, substitua `"X12_00501_837"` cada `"X12_00501_837_P"` valor pelo valor.

   Para atualizar o seu esquema, siga estes passos:

   1. No portal Azure, vá à sua conta de integração. Encontre e baixe o seu esquema. Substitua o tipo de mensagem e mude o nome do ficheiro schema e faça o upload do seu esquema revisto para a sua conta de integração. Para mais informações, consulte [Editar schemas](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas).

   1. Nas definições de mensagem do seu acordo, selecione o esquema revisto.

1. No objeto do `schemaReferences` seu acordo, adicione outra entrada que especifica o tipo de mensagem variante que corresponde ao número da versão do documento.

   Por exemplo, suponha que `005010X222A1` pretenda `837` utilizar o número da versão do documento para o tipo de mensagem. O seu `schemaReferences` acordo tem uma secção com estas propriedades e valores:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   Nesta `schemaReferences` secção, adicione outra entrada que tenha estes valores:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   Quando terminar, a `schemaReferences` sua secção é assim:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. Nas definições de mensagem do seu contrato, desative a validação EDI limpando a caixa de **verificação de validação EDI** para cada tipo de mensagem ou para todos os tipos de mensagens se estiver a utilizar os valores **Predefinidos.**

   ![Desativar a validação para todos os tipos de mensagens ou para cada tipo de mensagem](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos adicionais sobre este conector, tais como ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/x12/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com o rótulo ISE do conector utiliza os limites de [mensagem B2B para](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)o ISE .

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores para aplicações lógicas](../connectors/apis-list.md)