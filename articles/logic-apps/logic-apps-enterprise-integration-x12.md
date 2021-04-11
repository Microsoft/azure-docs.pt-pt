---
title: Enviar e receber mensagens X12 para B2B
description: Trocar mensagens X12 para cenários de integração empresarial B2B utilizando apps Azure Logic com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 87a2bcc386ec5688fadb68aabdd2e5239e205516
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077477"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens X12 para integração empresarial B2B em Azure Logic Apps com Pacote de Integração Empresarial

Para trabalhar com mensagens X12 em Azure Logic Apps, pode utilizar o conector X12, que fornece gatilhos e ações para gerir a comunicação X12. Para obter informações sobre mensagens EDIFACT, consulte [as mensagens EDIFACT do Exchange](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, [inscreva-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* A aplicação lógica de onde pretende utilizar o conector X12 e um gatilho que inicia o fluxo de trabalho da sua aplicação lógica. O conector X12 fornece apenas ações, não gatilhos. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que está associada à sua subscrição Azure e ligada à aplicação lógica onde planeia utilizar o conector X12. Tanto a sua aplicação lógica como a sua conta de integração devem existir no mesmo local ou na região de Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que já definiu na sua conta de integração usando o qualificador de identidade X12.

* Os [esquemas](../logic-apps/logic-apps-enterprise-integration-schemas.md) a utilizar para validação XML que já adicionou à sua conta de integração. Se estiver a trabalhar com esquemas de Portabilidade e Prestação de Contas (HIPAA) do Seguro de Saúde, consulte [os esquemas da HIPAA](#hipaa-schemas).

* Antes de poder utilizar o conector X12, tem de criar um [acordo](../logic-apps/logic-apps-enterprise-integration-agreements.md) X12 entre os seus parceiros comerciais e armazenar esse acordo na sua conta de integração. Se estiver a trabalhar com esquemas de Portabilidade e Prestação de Contas (HIPAA) do Seguro de Saúde, tem de adicionar uma `schemaReferences` secção ao seu acordo. Para obter mais informações, consulte [os esquemas da HIPAA](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Receber Definições

Depois de definir as propriedades do contrato, pode configurar como este acordo identifica e lida com mensagens de entrada que recebe do seu parceiro através deste acordo.

1. Em **Adicionar**, selecione **'Receber' Definições**.

1. Configure estas propriedades com base no seu acordo com o parceiro que troca mensagens consigo. As **Definições de Receção** são organizadas nestas secções:

   * [Identificadores](#inbound-identifiers)
   * [Reconhecimento](#inbound-acknowledgement)
   * [Esquemas](#inbound-schemas)
   * [Envelopes](#inbound-envelopes)
   * [Números de controlo](#inbound-control-numbers)
   * [Validações](#inbound-validations)
   * [Definições internas](#inbound-internal-settings)

   Para obter descrições de propriedade, consulte as tabelas desta secção.

1. Quando terminar, certifique-se de que guarda as suas definições selecionando **OK**.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Receber Definições - Identificadores

![Propriedades do identificador para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Propriedade | Descrição |
|----------|-------------|
| **ISA1 (Qualificação de Autorização)** | O valor de qualificação de autorização que pretende utilizar. O valor predefinido é **00 - Sem Informação de Autorização Presente**. <p>**Nota:** Se selecionar outros valores, especifique um valor para a propriedade **ISA2.** |
| **ISA2** | O valor das Informações de Autorização a utilizar quando o imóvel **ISA1** não é **00 - Não há Informações de Autorização Presentes**. Este valor de propriedade deve ter um mínimo de um caráter alfanumérico e um máximo de 10. |
| **ISA3 (Qualificação de Segurança)** | O valor da Qualificação de Segurança que pretende utilizar. O valor predefinido é **00 - Nenhuma Informação de Segurança Presente**. <p>**Nota:** Se selecionar outros valores, especifique um valor para a propriedade **ISA4.** |
| **ISA4** | O valor de Informação de Segurança a utilizar quando a propriedade **ISA3** não é **00 - Nenhuma Informação de Segurança Presente**. Este valor de propriedade deve ter um mínimo de um caráter alfanumérico e um máximo de 10. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Receber Definições - Reconhecimento

![Reconhecimento para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Propriedade | Descrição |
|----------|-------------|
| **TA1 Esperado** | Devolver um reconhecimento técnico (TA1) ao remetente de intercâmbio. |
| **FA Esperado** | Devolva um reconhecimento funcional (FA) ao remetente de intercâmbio. <p>Para a propriedade **fa version,** com base na versão schema, selecione os agradecimentos 997 ou 999. <p>Para ativar a geração de loops AK2 em reconhecimentos funcionais para conjuntos de transações aceites, **selecione Incluir AK2 / IK2 Loop**. |

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Receber Definições - Schemas

![Esquemas para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

Para esta secção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da sua conta de [integração](./logic-apps-enterprise-integration-create-integration-account.md) para cada tipo de transação (ST01) e Aplicação de Remetente (GS02). O Pipeline EDI Receive desmonta a mensagem de entrada correspondendo os valores e esquemas que definiu nesta secção com os valores de ST01 e GS02 na mensagem de entrada e com o esquema da mensagem de entrada. Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

| Propriedade | Descrição |
|----------|-------------|
| **Versão** | A versão X12 para o esquema |
| **Tipo de Transação (ST01)** | O tipo de transação |
| **Aplicação remetente (GS02)** | O pedido de remetente |
| **Esquema** | O ficheiro de esquema que pretende usar |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Receber Definições - Envelopes

![Separadores para usar em conjuntos de transações para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Propriedade | Descrição |
|----------|-------------|
| **Utilização ISA11** | O separador a utilizar num conjunto de transações: <p>- **Identificador Padrão**: Utilize um período (.) para notação decimal, em vez da notação decimal do documento de entrada no Pipeline de Receção do EDI. <p>- **Separador de repetição**: Especifique o separador para ocorrências repetidas de um elemento de dados simples ou de uma estrutura de dados repetida. Por exemplo, normalmente o quilate (^) é usado como separador de repetição. Para os esquemas HIPAA, só pode utilizar o quilate. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Receber Definições - Números de controlo

![O número de controlo do manuseamento duplica para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Propriedade | Descrição |
|----------|-------------|
| **Desativar duplicações do número de controlo de intercâmbio** | Bloquear trocas duplicadas. Verifique o número de controlo de intercâmbio (ISA13) para ver o número de controlo de intercâmbio recebido. Se for detetada uma correspondência, o Pipeline EDI Receive não processa o intercâmbio. <p><p>Para especificar o número de dias para efetuar a verificação, insira um valor para a **verificação de duplicação de bens ISA13 a cada (dias)** propriedade. |
| **Duplicar o número de controlo do grupo** | Bloqueie os intercâmbios que têm números de controlo de grupo duplicados. |
| **Desativar duplicações do número de controlo do conjunto de transações** | Bloqueie os intercâmbios que têm números de controlo de transação duplicados. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Receber Definições - Validações

![Validações para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

A linha **Predefinição** mostra as regras de validação que são utilizadas para um tipo de mensagem EDI. Se quiser definir regras diferentes, selecione cada caixa onde deseja que a regra seja definida como **verdadeira**. Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

| Propriedade | Descrição |
|----------|-------------|
| **Tipo de mensagem** | O tipo de mensagem EDI |
| **Validação do EDI** | Realizar validação de EDI em tipos de dados definidos pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores de fuga. |
| **Validação alargada** | Se o tipo de dados não for EDI, a validação está no requisito do elemento de dados e permitida repetição, enumerações e validação do comprimento do elemento de dados (min ou máx). |
| **Permitir Zeros de Liderança/Trailing** | Mantenha qualquer protagonista adicional ou trailing zero e caracteres espaciais. Não remova estes caracteres. |
| **Trim Liderando/Trailing Zeroes** | Remova qualquer caracteres de ponta ou de pista zero e espaço. |
| **Política de separador de rastos** | Gerar separadores de rasto. <p>- **Não Permitido**: Proibir os delimiters e separadores de fuga no intercâmbio de entrada. Se o intercâmbio tiver delimiters e separadores, o intercâmbio é declarado não válido. <p>- **Opcional**: Aceitar intercâmbios com ou sem delimiters e separadores. <p>- **Obrigatório**: O intercâmbio de entrada deve ter delimiters e separadores. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Receber Definições - Definições Internas

![Definições internas para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Propriedade | Descrição |
|----------|-------------|
| **Converter o formato decimal implícito Nn para um valor numérico base 10** | Converter um número EDI especificado com o formato "Nn" num valor numérico base-10. |
| **Crie tags XML vazias se forem permitidos separadores de fuga** | O remetente de intercâmbio inclua etiquetas XML vazias para separadores de fuga. |
| **Split Interchange como conjuntos de transações - suspender conjuntos de transações por erro** | Parse cada conjunto de transações que está num intercâmbio num documento XML separado aplicando o envelope apropriado ao conjunto de transações. Suspender apenas as transações em que a validação falha. |
| **Split Interchange como conjuntos de transações - suspender o intercâmbio por erro** | Parse cada conjunto de transações que está em um intercâmbio em um documento XML separado aplicando o envelope apropriado. Suspender todo o intercâmbio quando uma ou mais transações se estabelecerem na validação de falhas de permuta. |
| **Preserve Interchange - suspender conjuntos de transações por erro** | Deixe o intercâmbio intacto e crie um documento XML para todo o intercâmbio em lote. Suspender apenas os conjuntos de transações que falham na validação, mas continuar a processar todos os outros conjuntos de transações. |
| **Preserve Interchange - suspender o intercâmbio por erro** |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lote. Suspende todo o intercâmbio quando uma ou mais transações se ajustam na validação de falha de permuta. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Enviar Definições

Depois de definir as propriedades do contrato, pode configurar como este acordo identifica e lida com mensagens de saída que envia ao seu parceiro através deste acordo.

1. Em **Adicionar**, selecione **'Enviar Definições'.**

1. Configure estas propriedades com base no seu acordo com o parceiro que troca mensagens consigo. Para obter descrições de propriedade, consulte as tabelas desta secção.

   As **Definições de Envio** são organizadas nestas secções:

   * [Identificadores](#outbound-identifiers)
   * [Reconhecimento](#outbound-acknowledgement)
   * [Esquemas](#outbound-schemas)
   * [Envelopes](#outbound-envelopes)
   * [Número da versão de controlo](#outbound-control-version-number)
   * [Números de controlo](#outbound-control-numbers)
   * [Conjuntos de caracteres e separadores](#outbound-character-sets-separators)
   * [Validação](#outbound-validation)

1. Quando terminar, certifique-se de que guarda as suas definições selecionando **OK**.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Enviar Definições - Identificadores

![Propriedades do identificador para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Propriedade | Descrição |
|----------|-------------|
| **ISA1 (Qualificação de Autorização)** | O valor de qualificação de autorização que pretende utilizar. O valor predefinido é **00 - Sem Informação de Autorização Presente**. <p>**Nota:** Se selecionar outros valores, especifique um valor para a propriedade **ISA2.** |
| **ISA2** | O valor das Informações de Autorização a utilizar quando o imóvel **ISA1** não é **00 - Não há Informações de Autorização Presentes**. Este valor de propriedade deve ter um mínimo de um caráter alfanumérico e um máximo de 10. |
| **ISA3 (Qualificação de Segurança)** | O valor da Qualificação de Segurança que pretende utilizar. O valor predefinido é **00 - Nenhuma Informação de Segurança Presente**. <p>**Nota:** Se selecionar outros valores, especifique um valor para a propriedade **ISA4.** |
| **ISA4** | O valor de Informação de Segurança a utilizar quando a propriedade **ISA3** não é **00 - Nenhuma Informação de Segurança Presente**. Este valor de propriedade deve ter um mínimo de um caráter alfanumérico e um máximo de 10. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Configurações de envio - Reconhecimento

![Propriedades de reconhecimento para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Propriedade | Descrição |
|----------|-------------|
| **TA1 Esperado** | Devolver um reconhecimento técnico (TA1) ao remetente de intercâmbio. <p>Esta definição especifica que o parceiro anfitrião, que está a enviar a mensagem, solicita um reconhecimento do parceiro convidado no acordo. Estes agradecimentos são esperados pelo parceiro anfitrião com base nas Definições de Receção do acordo. |
| **FA Esperado** | Devolva um reconhecimento funcional (FA) ao remetente de intercâmbio. Para a propriedade **fa version,** com base na versão schema, selecione os agradecimentos 997 ou 999. <p>Esta definição especifica que o parceiro anfitrião, que está a enviar a mensagem, solicita um aviso do parceiro convidado no acordo. Estes agradecimentos são esperados pelo parceiro anfitrião com base nas Definições de Receção do acordo. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Enviar Definições - Schemas

![Esquemas para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

Para esta secção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da sua conta de [integração](./logic-apps-enterprise-integration-create-integration-account.md) para cada tipo de transação (ST01). Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

| Propriedade | Descrição |
|----------|-------------|
| **Versão** | A versão X12 para o esquema |
| **Tipo de Transação (ST01)** | O tipo de transação para o esquema |
| **Esquema** | O ficheiro de esquema que pretende usar. Se selecionar primeiro o esquema, a versão e o tipo de transação são automaticamente definidos. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Enviar Definições - Envelopes

![Separadores numa transação definida para usar para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Propriedade | Descrição |
|----------|-------------|
| **Utilização ISA11** | O separador a utilizar num conjunto de transações: <p>- **Identificador Padrão**: Utilize um período (.) para notação decimal, em vez da notação decimal do documento de saída no Pipeline de Envio de EDI. <p>- **Separador de repetição**: Especifique o separador para ocorrências repetidas de um elemento de dados simples ou de uma estrutura de dados repetida. Por exemplo, normalmente o quilate (^) é usado como separador de repetição. Para os esquemas HIPAA, só pode utilizar o quilate. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Configurações de envio - Número da versão de controlo

![Número da versão de controlo para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

Para esta secção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da sua conta de [integração](./logic-apps-enterprise-integration-create-integration-account.md) para cada intercâmbio. Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

| Propriedade | Descrição |
|----------|-------------|
| **Número da versão de controlo (ISA12)** | A versão do padrão X12 |
| **Indicador de utilização (ISA15)** | O contexto de um intercâmbio, que é ou dados **de teste,** dados **de informação** ou dados **de produção** |
| **Esquema** | O esquema a utilizar para gerar os segmentos GS e ST para um intercâmbio codificado X12 que é enviado para o Pipeline de Envio de EDI. |
| **GS1** | Opcional, selecione o código funcional. |
| **GS2** | Opcional, especifique o remetente de aplicações. |
| **GS3** | Opcional, especifique o recetor da aplicação. |
| **GS4** | Opcional, selecione **CCYYMMDD** ou **YYMMDD**. |
| **GS5** | Opcional, selecione **HHMM,** **HHMMSS,** ou **HHMMSSdd.** |
| **GS7** | Opcional, selecione um valor para a agência responsável. |
| **GS8** | Opcional, especifique a versão do documento de esquema. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Enviar Definições - Números de controlo

![Números de controlo para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Propriedade | Descrição |
|----------|-------------|
| **Número de controlo de intercâmbio (ISA13)** | A gama de valores para o número de controlo de intercâmbio, que pode ter um mínimo de valor 1 e um valor máximo de 9999999999 |
| **Número de controlo do grupo (GS06)** | A gama de valores para o número de controlo do grupo, que pode ter um valor mínimo de 1 e um valor máximo de 9999999999 |
| **Número de controlo do conjunto de transações (ST02)** | A gama de valores para o número de controlo definido por transação, que pode ter um valor mínimo de 1 e um valor máximo de 99999999999 <p>- **Prefixo**: Opcional, um valor alfanumérico <br>- **Sufixo**: Opcional, um valor alfanumérico |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Enviar Definições - Conjuntos de caracteres e separadores

![Delimiters para tipos de mensagens em mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

A linha **Padrão** mostra o conjunto de caracteres que é usado como delimiters para um esquema de mensagem. Se não quiser utilizar o conjunto de caracteres **Predefinidos,** pode introduzir um conjunto diferente de delimiters para cada tipo de mensagem. Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

> [!TIP]
> Para fornecer valores especiais de caracteres, edite o acordo como JSON e forneça o valor ASCII para o caráter especial.

| Propriedade | Descrição |
|----------|-------------|
| **Conjunto de caracteres a ser usado** | O conjunto de caracteres X12, que é **básico,** **estendido,** ou **UTF8**. |
| **Esquema** | O esquema que quer usar. Depois de selecionar o esquema, selecione o conjunto de caracteres que pretende utilizar, com base nas descrições do separador abaixo. |
| **Tipo de entrada** | O tipo de entrada para o conjunto de caracteres |
| **Separador de componentes** | Um único personagem que separa elementos de dados compósitos |
| **Separador de elementos de dados** | Um único personagem que separa elementos de dados simples dentro de dados compósitos |
| **separador de caracteres de substituição** | Um personagem de substituição que substitui todos os caracteres separadores nos dados de carga útil ao gerar a mensagem X12 de saída |
| **Exterminador de segmento** | Um único personagem que indica o fim de um segmento de EDI |
| **Sufixo** | O personagem para usar com o identificador de segmento. Se especificar um sufixo, o elemento de dados do exterminador do segmento pode estar vazio. Se o exterminador do segmento ficar vazio, deve designar um sufixo. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Enviar Definições - Validação

![Propriedades de validação para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

A linha **Predefinição** mostra as regras de validação que são utilizadas para um tipo de mensagem EDI. Se quiser definir regras diferentes, selecione cada caixa onde deseja que a regra seja definida como **verdadeira**. Depois de completar cada linha, aparece automaticamente uma nova linha vazia.

| Propriedade | Descrição |
|----------|-------------|
| **Tipo de mensagem** | O tipo de mensagem EDI |
| **Validação do EDI** | Realizar validação de EDI em tipos de dados definidos pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores de fuga. |
| **Validação alargada** | Se o tipo de dados não for EDI, a validação está no requisito do elemento de dados e permitida repetição, enumerações e validação do comprimento do elemento de dados (min ou máx). |
| **Permitir Zeros de Liderança/Trailing** | Mantenha qualquer protagonista adicional ou trailing zero e caracteres espaciais. Não remova estes caracteres. |
| **Trim Liderando/Trailing Zeroes** | Remova qualquer caracteres de ponta ou de pista zero e espaço. |
| **Política de separador de rastos** | Gerar separadores de rasto. <p>- **Não Permitido**: Proibir os delimiters e separadores de fuga no intercâmbio de saída. Se o intercâmbio tiver delimiters e separadores, o intercâmbio é declarado não válido. <p>- **Opcional**: Enviar trocas com ou sem delimiters e separadores. <p>- **Obrigatório**: O intercâmbio de saída deve ter delimiters e separadores. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>Esquemas e tipos de mensagens HIPAA

Quando trabalhar com esquemas HIPAA e os tipos de mensagens 277 ou 837, tem de realizar alguns passos extras. Os [números da versão documental (GS8)](#outbound-control-version-number) para estes tipos de mensagens têm mais de 9 caracteres, por exemplo, "005010X222A1". Além disso, alguns números de versão documental mapeiam para tipos de mensagens variantes. Se não referenciar o tipo de mensagem correto no seu esquema e no seu acordo, obtém esta mensagem de erro:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

Esta tabela lista os tipos de mensagens afetadas, quaisquer variantes e os números de versão documental que mapeiam para esses tipos de mensagens:

| Tipo de mensagem ou variante |  Description | Número da versão documental (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Notificação do estado da informação dos cuidados de saúde | 005010X212 |
| 837_I | Reivindicação de Cuidados de Saúde Institucional | 004010X096A1 <br>005010X223A1 <br>005010X22 |
| 837_D | Reclamação dentária de cuidados de saúde | 004010X097A1 <br>005010X224A1 <br>005010X22 |
| 837_P | Profissional de Saúde | 004010X098A1 <br>005010X22 <br>005010X22A1 |
|||

Também é necessário desativar a validação do EDI quando utilizar estes números de versão documental, porque resultam num erro de que o comprimento do personagem é inválido.

Para especificar estes números de versão documental e tipos de mensagens, siga estes passos:

1. No seu esquema HIPAA, substitua o tipo de mensagem atual pelo tipo de mensagem variante para o número de versão do documento que pretende utilizar.

   Por exemplo, suponha que pretende utilizar o número da versão documental `005010X222A1` com o tipo de `837` mensagem. No seu esquema, substitua cada `"X12_00501_837"` valor pelo `"X12_00501_837_P"` valor.

   Para atualizar o seu esquema, siga estes passos:

   1. No portal Azure, vá à sua conta de integração. Encontre e baixe o seu esquema. Substitua o tipo de mensagem e rebatize o ficheiro de esquema e faça o upload do seu esquema revisto para a sua conta de integração. Para obter mais informações, consulte [editar esquemas.](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas)

   1. Nas definições de mensagens do seu acordo, selecione o esquema revisto.

1. No objeto do seu `schemaReferences` contrato, adicione outra entrada que especifique o tipo de mensagem variante que corresponde ao número da versão do documento.

   Por exemplo, suponha que pretende utilizar o número da versão `005010X222A1` documental para o `837` tipo de mensagem. O seu contrato tem uma `schemaReferences` secção com estas propriedades e valores:

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

   Quando terminar, a sua `schemaReferences` secção fica assim:

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

1. Nas definições de mensagem do seu contrato, desative a validação do EDI, limpando a caixa de **verificação de validação EDI** para cada tipo de mensagem ou para todos os tipos de mensagens se estiver a utilizar os valores **Predefinidos.**

   ![Desativar a validação para todos os tipos de mensagens ou cada tipo de mensagem](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/x12/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)esta versão com rótulo ISE deste conector utiliza os limites de [mensagem B2B para ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores para Apps Lógicas](../connectors/apis-list.md)
