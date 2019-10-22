---
title: Mensagens X12 para integração B2B – aplicativos lógicos do Azure
description: Trocar mensagens X12 no formato EDI para integração corporativa B2B em aplicativos lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: 8bc5c458240925af1fdd74ebc9b2d4c3db71fb05
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679987"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens X12 para integração de empresas B2B em aplicativos lógicos do Azure com o Enterprise Integration Pack

Antes de poder trocar mensagens X12 para aplicativos lógicos do Azure, você deve criar um contrato X12 e armazenar esse contrato em sua conta de integração. Aqui estão as etapas para criar um contrato X12.

> [!NOTE]
> Esta página aborda os recursos do X12 para aplicativos lógicos do Azure. Para obter mais informações, consulte [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Antes de começar

Aqui estão os itens de que você precisa:

* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já está definida e associada à sua assinatura do Azure
* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) que são definidos em sua conta de integração e configurados com o identificador X12 em **identidades de negócios**    
* Um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessário que você pode carregar para sua conta de integração

Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [Adicionar parceiros](logic-apps-enterprise-integration-partners.md)e ter um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) que você deseja usar, você pode criar um contrato do X12 seguindo estas etapas.

## <a name="create-an-x12-agreement"></a>Criar um contrato do X12

1. Inicie sessão no [portal do Azure](https://portal.azure.com "Portal do Azure"). 

2. No menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, digite "integração" e, em seguida, selecione **contas de integração**.  

   ![Localizar sua conta de integração](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Se **todos os serviços** não aparecerem, talvez você tenha que expandir o menu primeiro. Na parte superior do menu recolhido, selecione **Mostrar menu**.

3. Em **contas de integração**, selecione a conta de integração à qual você deseja adicionar o contrato.

   ![Selecione a conta de integração onde criar o contrato](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Selecione **visão geral**e, em seguida, selecione o bloco **contratos** . 
   Se você não tiver um bloco de contratos, adicione o bloco primeiro. 

   ![Escolha o bloco "contratos"](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. Em **contratos**, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. Em **Adicionar**, insira um **nome** para seu contrato. 
   Para o tipo de contrato, selecione **X12**. 
   Selecione o **parceiro host**, a **identidade do host**, o **parceiro convidado**e a **identidade de convidado** para seu contrato. 
   Para obter mais detalhes de propriedade, consulte a tabela nesta etapa.

    ![Fornecer detalhes do contrato](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Propriedade | Descrição |
    | --- | --- |
    | Nome |Nome do contrato |
    | Tipo de contrato | Deve ser X12 |
    | Parceiro de host |Um contrato precisa de um parceiro de host e convidado. O parceiro host representa a organização que configura o contrato. |
    | Identidade do host |Um identificador para o parceiro de host |
    | Parceiro convidado |Um contrato precisa de um parceiro de host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro de host. |
    | Identidade de convidado |Um identificador para o parceiro convidado |
    | Configurações de recebimento |Essas propriedades se aplicam a todas as mensagens recebidas por um contrato. |
    | Configurações de envio |Essas propriedades se aplicam a todas as mensagens enviadas por um contrato. |  

   > [!NOTE]
   > A resolução do contrato X12 depende da correspondência entre o identificador e o qualificador do remetente e o identificador e o qualificador do receptor definidos no parceiro e na mensagem de entrada. Se esses valores mudarem para seu parceiro, atualize o contrato também.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurar como seu contrato lida com mensagens recebidas

Agora que você definiu as propriedades do contrato, é possível configurar como este contrato identifica e manipula as mensagens recebidas do seu parceiro por meio deste contrato.

1.  Em **Adicionar**, selecione **configurações de recebimento**.
Configure essas propriedades com base em seu contrato com o parceiro que troca mensagens com você. Para obter descrições de propriedades, consulte as tabelas nesta seção.

    **As configurações de recebimento** são organizadas nestas seções: identificadores, confirmação, esquemas, envelopes, números de controle, validações e configurações internas.

2. Depois de terminar, lembre-se de salvar suas configurações escolhendo **OK**.

Agora seu contrato está pronto para lidar com mensagens de entrada que estão em conformidade com as configurações selecionadas.

### <a name="identifiers"></a>Identificadores

![Definir propriedades do identificador](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Propriedade | Descrição |
| --- | --- |
| ISA1 (qualificador de autorização) |Selecione o valor do qualificador de autorização na lista suspensa. |
| ISA2 |Opcional. Insira o valor das informações de autorização. Se o valor inserido para ISA1 for diferente de 00, insira no mínimo um caractere alfanumérico e no máximo 10. |
| ISA3 (qualificador de segurança) |Selecione o valor do qualificador de segurança na lista suspensa. |
| ISA4 |Opcional. Insira o valor informações de segurança. Se o valor inserido para ISA3 for diferente de 00, insira no mínimo um caractere alfanumérico e no máximo 10. |

### <a name="acknowledgment"></a>Confirmação

![Definir propriedades de confirmação](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Propriedade | Descrição |
| --- | --- |
| TA1 esperado |Retorna uma confirmação técnica para o remetente do intercâmbio |
| FA esperado |Retorna uma confirmação funcional para o emissor de intercâmbio. Em seguida, selecione se você deseja as confirmações 997 ou 999, com base na versão do esquema |
| Incluir loop AK2/IK2 |Habilita a geração de loops AK2 em confirmações funcionais para conjuntos de transação aceitos |

### <a name="schemas"></a>Esquemas

Selecione um esquema para cada tipo de transação (ST1) e aplicativo de remetente (GS2). O pipeline de recebimento desmonta a mensagem de entrada combinando os valores de ST1 e GS2 na mensagem de entrada com os valores definidos aqui e o esquema da mensagem de entrada com o esquema definido aqui.

![Selecionar esquema](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Propriedade | Descrição |
| --- | --- |
| Versão |Selecione a versão X12 |
| Tipo de transação (ST01) |Selecione o tipo de transação |
| Aplicativo do remetente (GS02) |Selecionar o aplicativo do remetente |
| Esquema |Selecione o arquivo de esquema que você deseja usar. Os esquemas são adicionados à sua conta de integração. |

> [!NOTE]
> Configure o [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessário que é carregado para sua [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes

![Especificar o separador em um conjunto de transações: escolher identificador padrão ou separador de repetição](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Propriedade | Descrição |
| --- | --- |
| Uso de ISA11 |Especifica o separador a ser usado em um conjunto de transações: <p>Selecione o **identificador padrão** para usar um ponto (.) para notação decimal, em vez da notação decimal do documento de entrada no pipeline de recebimento EDI. <p>Selecione **separador de repetição** para especificar o separador para ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetida. Por exemplo, geralmente o quilate (^) é usado como o separador de repetição. Para esquemas da HIPAA, você só pode usar o quilate. |

### <a name="control-numbers"></a>Números de controle

![Selecione como lidar com duplicatas de número de controle](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Propriedade | Descrição |
| --- | --- |
| Não permitir duplicatas de números de controle de intercâmbio |Bloquear intercâmbios duplicados. Verifica o número de controle de intercâmbio (ISA13) para o número de controle de intercâmbio recebido. Se uma correspondência for detectada, o pipeline de recebimento não processará o intercâmbio. Você pode especificar o número de dias para executar a verificação, fornecendo um valor para *verificar ISA13 duplicados a cada (dias)* . |
| Não permitir duplicatas de número de controle de grupo |Bloquear intercâmbios com números de controle de grupo duplicados. |
| Não permitir duplicatas de números de controle de conjunto de transações |Bloquear intercâmbios com números de controle de conjunto de transação duplicados. |

### <a name="validations"></a>Validações

![Definir propriedades de validação para mensagens recebidas](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Quando você conclui cada linha de validação, outra é adicionada automaticamente. Se você não especificar nenhuma regra, a validação usará a linha "padrão".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação de EDI |Execute a validação de EDI em tipos de dados, conforme definido pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| Validação estendida |Se o tipo de dados não for EDI, a validação estará no requisito de elemento de dados e na validação de comprimento de elemento de dados permitida, enumerações e repetição (mín/máx). |
| Permitir zeros à esquerda/à direita |Manter qualquer zero à esquerda ou à direita e caracteres de espaço adicionais. Não remova esses caracteres. |
| Cortar zeros à esquerda/à direita |Remova zeros à esquerda ou à direita e caracteres de espaço. |
| Política de separador à direita |Gerar separadores à direita. <p>Selecione **não permitido** para proibir delimitadores e separadores à direita no intercâmbio recebido. Se o intercâmbio tiver delimitadores e separadores à direita, o intercâmbio será declarado como inválido. <p>Selecione **opcional** para aceitar intercâmbios com ou sem delimitadores e separadores à direita. <p>Selecione **obrigatório** quando o intercâmbio deve ter delimitadores e separadores à direita. |

### <a name="internal-settings"></a>Configurações internas

![Selecionar configurações internas](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Propriedade | Descrição |
| --- | --- |
| Converter o formato decimal implícito "nn" em um valor numérico de base 10 |Converte um número de EDI especificado com o formato "nn" em um valor numérico de base 10 |
| Criar marcas XML vazias se os separadores à direita forem permitidos |Marque essa caixa de seleção para que o emissor de intercâmbio inclua marcas XML vazias para separadores à direita. |
| Dividir intercâmbio como conjuntos de transações – suspender conjuntos de transação com erro|Analisa cada conjunto de transações em um intercâmbio em um documento XML separado aplicando o envelope apropriado ao conjunto de transações. Suspende somente as transações em que a validação falha. |
| Dividir intercâmbio como conjuntos de transações – suspender intercâmbio com erro|Analisa cada conjunto de transações em um intercâmbio em um documento XML separado aplicando o envelope apropriado. Suspende o intercâmbio inteiro quando um ou mais conjuntos de transações no intercâmbio falham na validação. | 
| Preservar intercâmbio-suspender conjuntos de transação com erro |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lote. Suspende somente os conjuntos de transações que falham na validação, enquanto continuam a processar todos os outros conjuntos de transações. |
| Preservar intercâmbio-suspender intercâmbio em caso de erro |Deixa o intercâmbio intacto, cria um documento XML para todo o intercâmbio em lote. Suspende o intercâmbio inteiro quando um ou mais conjuntos de transações no intercâmbio falham na validação. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurar como seu contrato envia mensagens

Você pode configurar como este contrato identifica e trata as mensagens de saída que você envia para o seu parceiro por meio deste contrato.

1.  Em **Adicionar**, selecione **configurações de envio**.
Configure essas propriedades com base em seu contrato com seu parceiro que troca mensagens com você. Para obter descrições de propriedades, consulte as tabelas nesta seção.

    **As configurações de envio** são organizadas nestas seções: identificadores, confirmação, esquemas, envelopes, conjuntos de caracteres e separadores, números de controle e validação.

2. Depois de terminar, lembre-se de salvar suas configurações escolhendo **OK**.

Agora seu contrato está pronto para lidar com mensagens de saída que estão em conformidade com as configurações selecionadas.

### <a name="identifiers"></a>Identificadores

![Definir propriedades do identificador](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Propriedade | Descrição |
| --- | --- |
| Qualificador de autorização (ISA1) |Selecione o valor do qualificador de autorização na lista suspensa. |
| ISA2 |Insira o valor das informações de autorização. Se esse valor for diferente de 00, digite no mínimo um caractere alfanumérico e no máximo 10. |
| Qualificador de segurança (ISA3) |Selecione o valor do qualificador de segurança na lista suspensa. |
| ISA4 |Insira o valor informações de segurança. Se esse valor for diferente de 00, na caixa de texto valor (ISA4), insira no mínimo um valor alfanumérico e no máximo 10. |

### <a name="acknowledgment"></a>Confirmação

![Definir propriedades de confirmação](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | Descrição |
| --- | --- |
| TA1 esperado |Retorne uma confirmação técnica (TA1) ao remetente do intercâmbio. Essa configuração especifica que o parceiro host que está enviando a mensagem solicita uma confirmação do parceiro convidado no contrato. Essas confirmações são esperadas pelo parceiro host com base nas configurações de recebimento do contrato. |
| FA esperado |Retorne uma confirmação funcional (FA) ao remetente do intercâmbio. Selecione se você deseja as confirmações 997 ou 999, com base nas versões de esquema com as quais está trabalhando. Essas confirmações são esperadas pelo parceiro host com base nas configurações de recebimento do contrato. |
| Versão do FA |Selecione a versão FA |

### <a name="schemas"></a>Esquemas

![Selecione o esquema a ser usado](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | Descrição |
| --- | --- |
| Versão |Selecione a versão X12 |
| Tipo de transação (ST01) |Selecione o tipo de transação |
| ESQUEMA |Selecione o esquema a ser usado. Os esquemas estão localizados em sua conta de integração. Se você selecionar o esquema primeiro, ele configurará automaticamente a versão e o tipo de transação  |

> [!NOTE]
> Configure o [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessário que é carregado para sua [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes

![Especificar o separador em um conjunto de transações: escolher identificador padrão ou separador de repetição](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Propriedade | Descrição |
| --- | --- |
| Uso de ISA11 |Especifica o separador a ser usado em um conjunto de transações: <p>Selecione o **identificador padrão** para usar um ponto (.) para notação decimal, em vez da notação decimal do documento de entrada no pipeline de recebimento EDI. <p>Selecione **separador de repetição** para especificar o separador para ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetida. Por exemplo, geralmente o quilate (^) é usado como o separador de repetição. Para esquemas da HIPAA, você só pode usar o quilate. |

### <a name="control-numbers"></a>Números de controle

![Especificar propriedades de número de controle](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Propriedade | Descrição |
| --- | --- |
| Número de versão de controle (ISA12) |Selecione a versão do padrão X12 |
| Indicador de uso (ISA15) |Selecione o contexto de um intercâmbio.  Os valores são informações, dados de produção ou dados de teste |
| Esquema |Gera os segmentos GS e ST para um intercâmbio X12 que ele envia para o pipeline de envio |
| GS1 |Opcional, selecione um valor para o código funcional na lista suspensa |
| GS2 |Opcional, remetente do aplicativo |
| GS3 |Opcional, receptor de aplicativo |
| GS4 |Opcional, selecione CCYYMMDD ou YYMMDD |
| GS5 |Opcional, selecione HHMM, HHMMSS ou HHMMSSdd |
| GS7 |Opcional, selecione um valor para a agência responsável na lista suspensa |
| GS8 |Opcional, versão do documento |
| Número de controle de intercâmbio (ISA13) |Obrigatório, insira um intervalo de valores para o número de controle de intercâmbio. Insira um valor numérico com um mínimo de 1 e um máximo de 999999999 |
| Número de controle de grupo (GS06) |Obrigatório, insira um intervalo de números para o número de controle de grupo. Insira um valor numérico com um mínimo de 1 e um máximo de 999999999 |
| Número de controle do conjunto de transação (ST02) |Obrigatório, insira um intervalo de números para o número de controle do conjunto de transações. Insira um intervalo de valores numéricos com um mínimo de 1 e um máximo de 999999999 |
| Prefixo |Opcional, designado para o intervalo de números de controle de conjunto de transação usado na confirmação. Insira um valor numérico para os dois campos intermediários e um valor alfanumérico (se desejado) para os campos de prefixo e sufixo. Os campos intermediários são obrigatórios e contêm os valores mínimo e máximo para o número de controle |
| Sufixo |Opcional, designado para o intervalo de números de controle de conjunto de transações usado em uma confirmação. Insira um valor numérico para os dois campos intermediários e um valor alfanumérico (se desejado) para os campos de prefixo e sufixo. Os campos intermediários são obrigatórios e contêm os valores mínimo e máximo para o número de controle |

### <a name="character-sets-and-separators"></a>Conjuntos de caracteres e separadores

Além do conjunto de caracteres, você pode inserir um conjunto diferente de delimitadores para cada tipo de mensagem. Se um conjunto de caracteres não for especificado para um determinado esquema de mensagem, o conjunto de caracteres padrão será usado.

![Especificar delimitadores para tipos de mensagem](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Propriedade | Descrição |
| --- | --- |
| Conjunto de caracteres a ser usado |Para validar as propriedades, selecione o conjunto de caracteres X12. As opções são Basic, Extended e UTF8. |
| Esquema |Selecione um esquema na lista suspensa. Depois de concluir cada linha, uma nova linha é adicionada automaticamente. Para o esquema selecionado, selecione o conjunto de separadores que você deseja usar, com base nas descrições de separador abaixo. |
| Tipo de entrada |Selecione um tipo de entrada na lista suspensa. |
| Separador de componentes |Para separar os elementos de dados compostos, insira um único caractere. |
| Separador de elemento de dados |Para separar elementos de dados simples em elementos de dados compostos, insira um único caractere. |
| Caractere de substituição |Insira um caractere de substituição usado para substituir todos os caracteres separadores nos dados de carga ao gerar a mensagem X12 de saída. |
| Terminador de segmento |Para indicar o fim de um segmento EDI, insira um único caractere. |
| Sufixo |Selecione o caractere que é usado com o identificador de segmento. Se você designar um sufixo, o elemento de dados de terminador de segmento poderá ficar vazio. Se o terminador de segmento for deixado vazio, você deverá designar um sufixo. |

> [!TIP]
> Para fornecer valores de caracteres especiais, edite o contrato como JSON e forneça o valor ASCII para o caractere especial.

### <a name="validation"></a>Validação

![Definir propriedades de validação para enviar mensagens](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Quando você conclui cada linha de validação, outra é adicionada automaticamente. Se você não especificar nenhuma regra, a validação usará a linha "padrão".

| Propriedade | Descrição |
| --- | --- |
| Tipo de mensagem |Selecione o tipo de mensagem EDI. |
| Validação de EDI |Execute a validação de EDI em tipos de dados, conforme definido pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| Validação estendida |Se o tipo de dados não for EDI, a validação estará no requisito de elemento de dados e na validação de comprimento de elemento de dados permitida, enumerações e repetição (mín/máx). |
| Permitir zeros à esquerda/à direita |Manter qualquer zero à esquerda ou à direita e caracteres de espaço adicionais. Não remova esses caracteres. |
| Cortar zeros à esquerda/à direita |Remova os caracteres zero à esquerda ou à direita. |
| Política de separador à direita |Gerar separadores à direita. <p>Selecione **não permitido** para proibir delimitadores e separadores à direita no intercâmbio enviado. Se o intercâmbio tiver delimitadores e separadores à direita, o intercâmbio será declarado como inválido. <p>Selecione **opcional** para enviar intercâmbios com ou sem delimitadores e separadores à direita. <p>Selecione **obrigatório** se o intercâmbio enviado precisar ter delimitadores e separadores à direita. |

## <a name="find-your-created-agreement"></a>Encontre seu contrato criado

1.  Depois de concluir a configuração de todas as propriedades do contrato, na página **Adicionar** , escolha **OK** para concluir a criação do seu contrato e retornar à sua conta de integração.

    Seu contrato recém-adicionado agora aparece na sua lista de **contratos** .

2.  Você também pode exibir seus contratos em sua visão geral da conta de integração. No menu conta de integração, escolha **visão geral**e, em seguida, selecione o bloco **contratos** .

    ![Escolha o bloco "contratos"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Exibir o Swagger
Consulte os [detalhes do Swagger](/connectors/x12/). 

## <a name="learn-more"></a>Saber mais
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  

