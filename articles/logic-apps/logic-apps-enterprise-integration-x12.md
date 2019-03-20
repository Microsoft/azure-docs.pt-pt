---
title: Nas mensagens X12 para a integração empresarial de B2B - Azure Logic Apps | Documentos da Microsoft
description: Trocar mensagens X12 no formato EDI para enterprise integração B2B no Azure Logic Apps Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.date: 01/31/2017
ms.openlocfilehash: 6665ea8bc4016c9d64005f9c742115cf785ed5ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842146"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens X12 para enterprise integração B2B no Azure Logic Apps Enterprise Integration Pack

Antes de pode trocar X12 mensagens para o Azure Logic Apps, tem de criar um X12 contrato e armazenar esse contrato na sua conta de integração. Eis os passos para saber como criar um X12 contrato.

> [!NOTE]
> Esta funcionalidade de bastidores X12 de página para o Azure Logic Apps. Para obter mais informações, consulte [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Antes de começar

Eis os itens que precisa:

* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já definida e associada à sua subscrição do Azure
* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) que são definidos na sua conta de integração e configurado com o X12 identificador em **identidades de negócio**    
* Necessária [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) que pode carregar a sua conta de integração

Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [adicionar parceiros](logic-apps-enterprise-integration-partners.md)e ter um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) que pretende utilizar, pode criar um X12 contrato através dos seguintes passos.

## <a name="create-an-x12-agreement"></a>Criar um X12 contrato

1. Inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure"). 

2. No menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, introduza "integração" e, em seguida, selecione **contas de integração**.  

   ![Localize a conta de integração](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Se **todos os serviços** não aparecer, poderá ter de expandir o menu pela primeira vez. Na parte superior do menu fechado, selecione **Show menu**.

3. Sob **contas de integração**, selecione a conta de integração em que pretende adicionar o contrato.

   ![Selecione onde pretende criar o contrato de conta de integração](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Selecione **descrição geral**, em seguida, selecione a **contratos** mosaico. 
   Se não tiver um mosaico de contratos, adicione primeiro o mosaico. 

   ![Escolha o que mosaico "Contratos"](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. Sob **contratos**, escolha **Add**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)     

6. Sob **Add**, introduza um **nome** para seu contrato. 
   Para o tipo de contrato, selecione **X12**. 
   Selecione o **parceiro do anfitrião**, **identidade do anfitrião**, **parceiro convidado**, e **identidade do convidado** para seu contrato. 
   Para obter mais detalhes de propriedade, consulte a tabela neste passo.

    ![Fornecer detalhes do contrato](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Propriedade | Descrição |
    | --- | --- |
    | Name |Nome do contrato |
    | Tipo de Contrato | Deve ser X12 |
    | Parceiro do Anfitrião |Tem de um contrato de parceiro de um anfitrião e convidado. O parceiro do anfitrião representa a organização que configura o contrato. |
    | Identidade do Anfitrião |Um identificador para o parceiro do anfitrião |
    | Parceiro Convidado |Tem de um contrato de parceiro de um anfitrião e convidado. O parceiro convidado representa a organização que está negociando com o parceiro do anfitrião. |
    | Identidade do Convidado |Um identificador para o parceiro convidado |
    | Definições de Receção |Estas propriedades aplicam-se a todas as mensagens recebidas por um contrato. |
    | Definições de Envio |Estas propriedades aplicam-se a todas as mensagens enviadas por um contrato. |  

   > [!NOTE]
   > Resolução do contrato depende o qualificador do remetente e o identificador e o qualificador do destinatário e o identificador definido na mensagem de entrada e parceiro correspondentes de X12. Se alterar estes valores para o seu parceiro, atualize também o contrato.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurar a forma como seus identificadores de contrato receberam mensagens

Agora que definiu as propriedades de contrato, é possível configurar como o presente contrato identifica e processa mensagens de entrada recebidas do seu parceiro por meio do presente contrato.

1.  Sob **Add**, selecione **receber definições**.
Configure estas propriedades com base no seu contrato com o parceiro que troca mensagens com. Para descrições das propriedades, consulte as tabelas nesta secção.

    **Receber definições** está organizada nestas secções: Identificadores, reconhecimento, esquemas, Envelopes, números de controlo, validações e as definições internas.

2. Depois de terminar, certifique-se de guardar as definições ao escolher **OK**.

Agora o seu contrato está pronto para lidar com mensagens de entrada que está em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

![Definir as propriedades do identificador](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Propriedade | Descrição |
| --- | --- |
| ISA1 (Qualificador de Autorização) |Selecione o valor de qualificador de autorização na lista pendente. |
| ISA2 |Opcional. Introduza o valor de informações de autorização. Se o valor introduzido para ISA1 for diferente de 00, introduza um mínimo de um caráter alfanumérico e um máximo de 10. |
| ISA3 (Qualificador de Segurança) |Selecione o valor de qualificador de segurança na lista pendente. |
| ISA4 |Opcional. Introduza o valor de informações de segurança. Se o valor introduzido para ISA3 for diferente de 00, introduza um mínimo de um caráter alfanumérico e um máximo de 10. |

### <a name="acknowledgment"></a>Confirmação

![Definir as propriedades de confirmação](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Propriedade | Descrição |
| --- | --- |
| TA1 esperado |Devolve uma confirmação técnica para o remetente de intercâmbio |
| FA esperado |Devolve uma confirmação funcional para o remetente de intercâmbio. Em seguida, selecione se pretende que as confirmações 997 ou 999, com base na versão de esquema |
| Incluir ciclo AK2/IK2 Loop |Permite a geração de ciclo AK2 loops em confirmações funcionais para conjuntos de transações aceites |

### <a name="schemas"></a>Esquemas

Selecione um esquema para cada tipo de transação (ST1) e a aplicação do remetente (GS2). O pipeline de recebimento desmonta a mensagem de entrada pela correspondência entre os valores para ST1 e GS2 na mensagem de entrada com valores que definir aqui e o esquema de mensagem de entrada com o esquema que definir aqui.

![Selecione o esquema](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Propriedade | Descrição |
| --- | --- |
| Versão |Selecione o X12 versão |
| Tipo de Transação (ST01) |Selecione o tipo de transação |
| Aplicação do Remetente (GS02) |Selecione a aplicação de remetente |
| Esquema |Selecione o ficheiro de esquema que pretende utilizar. Esquemas são adicionados à sua conta de integração. |

> [!NOTE]
> Configurar o necessários [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) que é carregado para o seu [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes

![Especifique o separador num conjunto de transações: escolher identificador padrão ou separador de repetição](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Propriedade | Descrição |
| --- | --- |
| Utilização de ISA11 |Especifica o separador a utilizar um conjunto de transações para: <p>Selecione **identificador padrão** para utilizar um ponto (.) para notação decimal, em vez da notação decimal do documento de entrada no EDI receber pipeline. <p>Selecione **separador de repetição** para especificar o separador de ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetidas. Por exemplo, normalmente, o acento circunflexo (^) é utilizado como separador de repetição. Para esquemas HIPAA, pode usar apenas o acento circunflexo. |

### <a name="control-numbers"></a>Números de Controlo

![Selecione a forma de lidar com os duplicados de números de controlo](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Propriedade | Descrição |
| --- | --- |
| Não permitir duplicados de intercâmbio de número de controlo |Bloquear intercâmbios duplicados. Verifica o número de controlo de intercâmbio (ISA13) para o número de controlo de intercâmbio recebido. Se for detetada uma correspondência, o pipeline de recebimento não processa o intercâmbio. Pode especificar o número de dias para efetuar a verificação, atribuindo um valor para *verificar a existência de duplicados ISA13 (dias)*. |
| Não permitir duplicados de números de controlo de Grupo |Bloco de intercâmbios com números de controlo do grupo duplicado. |
| Não permitir duplicados de números de controlo do conjunto de Transação |Bloco de intercâmbios com números de controlo do conjunto de transações duplicado. |

### <a name="validations"></a>Validações

![Definir propriedades de validação para as mensagens recebidas](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Quando concluir cada linha de validação, o outro é adicionado automaticamente. Se não especificar quaisquer regras, em seguida, validação usa a linha de "Predefinição".

| Propriedade | Descrição |
| --- | --- |
| Tipo de Mensagem |Selecione o tipo de mensagem EDI. |
| Validação de EDI |Execute a validação de EDI em tipos de dados, conforme definido pelo esquema EDI propriedades, restrições de comprimento, elementos de dados vazia e separadores à direita. |
| Validação Expandida |Se o tipo de dados não estiver EDI, a validação é o requisito de elemento de dados e permitido repetição, enumerações e dados de validação de comprimento de elemento (mín/máx.). |
| Permitir Zeros à Esquerda/Direita |Manter qualquer adicionais do esquerda ou à direita zero e carateres de espaço. Não remova estes carateres. |
| Cortar Zeros à Esquerda/Direita |Remova zero à esquerda nem à direita e carateres de espaço. |
| Política de Separador Decimal |Gere separadores à direita. <p>Selecione **Nepovoluje** para proibir delimitadores à direita e de separadores no intercâmbio recebido. Se o intercâmbio tem delimitadores à direita e os separadores, o intercâmbio é declarado não válido. <p>Selecione **opcional** para aceitar intercâmbios com ou sem delimitadores à direita e os separadores. <p>Selecione **obrigatório** quando o intercâmbio tem de ter delimitadores à direita e os separadores. |

### <a name="internal-settings"></a>Definições Internas

![Selecione as definições internas](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Propriedade | Descrição |
| --- | --- |
| Converter formato decimal implícito "Nn" para um valor numérico 10 base |Converte um número EDI especificado com o formato "Nn" num valor numérico de base 10 |
| Criar etiquetas XML vazias se forem permitidos separadores decimais |Selecione esta caixa de verificação para que o remetente de intercâmbio incluem etiquetas XML vazias para separadores à direita. |
| Dividir Intercâmbio como conjuntos de transação - suspender conjuntos de transação com erro|Analisa cada transação definida num intercâmbio num documento XML separado, aplicando o envelope apropriado para o conjunto de transações. Suspende somente as transações em que a validação falhar. |
| Dividir Intercâmbio como conjuntos de transação - suspender intercâmbio com erro|Analisa cada transação definida num intercâmbio num documento XML separado, aplicando o envelope apropriado. Suspende o intercâmbio inteiro quando um ou mais conjuntos de transação no intercâmbio a falha de validação. | 
| Preservar intercâmbio - suspender conjuntos transação com erro |Mantém o intercâmbio do utilizador intactos, cria um documento XML para o intercâmbio em lote inteiro. Suspende somente os conjuntos de transação que a falha de validação, enquanto continua a processar todos os outros conjuntos de transação. |
| Preservar Intercâmbio - suspender intercâmbio com erro |Mantém o intercâmbio do utilizador intactos, cria um documento XML para o intercâmbio em lote inteiro. Suspenda o intercâmbio de todo um ou mais conjuntos de transação no intercâmbio a falha de validação. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurar a forma como o seu contrato envia mensagens

É possível configurar como o presente contrato identifica e processa mensagens de saída que envia para o seu parceiro por meio do presente contrato.

1.  Sob **Add**, selecione **enviar definições**.
Configure estas propriedades com base no seu contrato com o seu parceiro que troca mensagens com. Para descrições das propriedades, consulte as tabelas nesta secção.

    **Definições de envio** está organizada nestas secções: Identificadores, reconhecimento, os esquemas, Envelopes, conjuntos de carateres e separadores, números de controlo e validação.

2. Depois de terminar, certifique-se de guardar as definições ao escolher **OK**.

Agora o seu contrato está pronto para lidar com mensagens de saída que estão em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

![Definir as propriedades do identificador](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Propriedade | Descrição |
| --- | --- |
| Qualificador de autorização (ISA1) |Selecione o valor de qualificador de autorização na lista pendente. |
| ISA2 |Introduza o valor de informações de autorização. Se este valor for diferente de 00, em seguida, introduza um mínimo de um caráter alfanumérico e um máximo de 10. |
| Qualificador de segurança (ISA3) |Selecione o valor de qualificador de segurança na lista pendente. |
| ISA4 |Introduza o valor de informações de segurança. Se este valor for diferente de 00, para a caixa de texto do valor (ISA4), em seguida, introduza um mínimo de um valor de alfanumérico e um máximo de 10. |

### <a name="acknowledgment"></a>Confirmação

![Definir as propriedades de confirmação](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | Descrição |
| --- | --- |
| TA1 esperado |Devolva uma confirmação técnica (TA1) para o remetente de intercâmbio. Esta definição especifica que o parceiro do anfitrião que está a enviar a mensagem solicita uma confirmação do parceiro convidado no contrato. Espera-se pelo parceiro de anfitrião com base nas definições do contrato de receber essas confirmações. |
| FA esperado |Devolva uma confirmação funcional (FA) para o remetente de intercâmbio. Selecione se pretende que as confirmações 997 ou 999, com base nas versões de esquema que está a trabalhar com. Espera-se pelo parceiro de anfitrião com base nas definições do contrato de receber essas confirmações. |
| Versão FA |Selecione a versão FA |

### <a name="schemas"></a>Esquemas

![Selecione o esquema para utilizar](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | Descrição |
| --- | --- |
| Versão |Selecione o X12 versão |
| Tipo de Transação (ST01) |Selecione o tipo de transação |
| SCHEMA |Selecione o esquema a utilizar. Esquemas estão localizados na sua conta de integração. Se selecionar esquema pela primeira vez, este configura automaticamente a versão e de transação de tipo  |

> [!NOTE]
> Configurar o necessários [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) que é carregado para o seu [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes

![Especifique o separador num conjunto de transações: escolher identificador padrão ou separador de repetição](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Propriedade | Descrição |
| --- | --- |
| Utilização de ISA11 |Especifica o separador a utilizar um conjunto de transações para: <p>Selecione **identificador padrão** para utilizar um ponto (.) para notação decimal, em vez da notação decimal do documento de entrada no EDI receber pipeline. <p>Selecione **separador de repetição** para especificar o separador de ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetidas. Por exemplo, normalmente, o acento circunflexo (^) é utilizado como separador de repetição. Para esquemas HIPAA, pode usar apenas o acento circunflexo. |

### <a name="control-numbers"></a>Números de Controlo

![Especificar as propriedades de número de controlo](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Propriedade | Descrição |
| --- | --- |
| Número da Versão de Controlo (ISA12) |Selecione a versão de X12 padrão |
| Indicador de utilização (ISA15) |Selecione o contexto de um intercâmbio.  Os valores são informações, dados de produção, ou dados de teste |
| Esquema |Gera os segmentos GS e ST para um intercâmbio de X12 codificada que envia para o Pipeline de envio |
| GS1 |Opcional, selecione um valor para o código funcional na lista pendente |
| GS2 |Opcional, remetente da aplicação |
| GS3 |Opcional, recetor de aplicação |
| GS4 |Opcional, selecione SSAAMMDD ou YYMMDD |
| GS5 |Opcional, selecione HHMM, T'HHMMSS ou HHMMSSdd |
| GS7 |Opcional, selecione um valor para a agência responsável da lista pendente |
| GS8 |Opcional, versão do documento |
| Número de controlo (ISA13) |Necessário, introduza um intervalo de valores para o número de controlo de intercâmbio. Introduza um valor numérico com um mínimo de 1 e um máximo de 999999999 |
| Número de controlo de grupo (GS06) |Necessário, introduza um intervalo de números para o número de controlo de grupo. Introduza um valor numérico com um mínimo de 1 e um máximo de 999999999 |
| Número de controlo do conjunto de transação (ST02) |Necessário, introduza um intervalo de números para o número de controlo de conjunto de transações. Introduza um intervalo de valores numéricos com um mínimo de 1 e um máximo de 999999999 |
| Prefixo |Opcional, designado para o intervalo de números de controlo de conjunto de transações usados na confirmação. Introduza um valor numérico para os dois campos intermediária e um valor de alfanumérico (se desejado) para os campos de prefixo e sufixo. Os campos intermediária são necessários e contêm os valores mínimos e máximo para o número de controlo |
| Sufixo |Opcional, designado para o intervalo de números de controlo de conjunto de transações usados numa confirmação. Introduza um valor numérico para os dois campos meio e um valor de alfanumérico (se desejado) para os campos de prefixo e sufixo. Os campos intermediária são necessários e contêm os valores mínimos e máximo para o número de controlo |

### <a name="character-sets-and-separators"></a>Conjuntos de Carateres e Separadores

Além de definir o caráter, pode introduzir um conjunto de delimitadores diferente para cada tipo de mensagem. Se um conjunto de carateres não está especificado para um esquema de mensagem em questão, o conjunto de carateres predefinido é utilizado.

![Especifique os delimitadores para tipos de mensagem](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Propriedade | Descrição |
| --- | --- |
| Conjunto de caracteres a ser utilizado |Para validar as propriedades, o conjunto de caracteres select X12. As opções são Basic, UTF8 e expandidas. |
| Esquema |Selecione um esquema da lista pendente. Depois de concluir cada linha, é automaticamente adicionada uma nova linha. Para o esquema selecionado, selecione o conjunto de separadores que pretende utilizar, com base nas descrições do separador abaixo. |
| Tipo de Entrada |Selecione um tipo de entrada na lista pendente. |
| Separador de Componente |Para separar os elementos de dados compostos, introduza um único caractere. |
| Separador do Elemento de Dados |Para separar os elementos de dados simples dentro de elementos de dados compostos, introduza um único caractere. |
| Caráter de substituição |Introduza um caráter de substituição utilizado para substituir todos os caracteres separadores nos dados payload ao gerar a saída X12 mensagem. |
| Terminador de Segmento |Para indicar o final de um segmento EDI, introduza um único caractere. |
| Sufixo |Selecione os carateres que é utilizado com o identificador de segmento. Se designar um sufixo, em seguida, o elemento de dados de terminador de segmento pode estar vazio. Se o terminador de segmento é deixado em branco, em seguida, tem de designar um sufixo. |

> [!TIP]
> Para fornecer valores de caráter especial, editar o contrato como JSON e forneça o valor de ASCII para o caráter especial.

### <a name="validation"></a>Validação

![Definir as propriedades de validação para o envio de mensagens](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Quando concluir cada linha de validação, o outro é adicionado automaticamente. Se não especificar quaisquer regras, em seguida, validação usa a linha de "Predefinição".

| Propriedade | Descrição |
| --- | --- |
| Tipo de Mensagem |Selecione o tipo de mensagem EDI. |
| Validação de EDI |Execute a validação de EDI em tipos de dados, conforme definido pelo esquema EDI propriedades, restrições de comprimento, elementos de dados vazia e separadores à direita. |
| Validação Expandida |Se o tipo de dados não estiver EDI, a validação é o requisito de elemento de dados e permitido repetição, enumerações e dados de validação de comprimento de elemento (mín/máx.). |
| Permitir Zeros à Esquerda/Direita |Manter qualquer adicionais do esquerda ou à direita zero e carateres de espaço. Não remova estes carateres. |
| Cortar Zeros à Esquerda/Direita |Remova esquerda ou à direita zero carateres. |
| Política de Separador Decimal |Gere separadores à direita. <p>Selecione **Nepovoluje** para proibir delimitadores à direita e de separadores no intercâmbio enviado. Se o intercâmbio tem delimitadores à direita e os separadores, o intercâmbio é declarado não válido. <p>Selecione **opcional** para enviar intercâmbios com ou sem delimitadores à direita e os separadores. <p>Selecione **obrigatório** se tem de ter o intercâmbio enviado delimitadores à direita e os separadores. |

## <a name="find-your-created-agreement"></a>Encontrar o contrato criado

1.  Depois de concluir a configuração de todas as suas propriedades de contrato, no **Add** página, selecione **OK** para concluir a criação de seu contrato e retornar à sua conta de integração.

    Agora seu contrato recentemente adicionado é apresentado no seu **contratos** lista.

2.  Também pode ver os contratos de na sua descrição de geral de conta de integração. No seu menu de conta de integração, escolha **descrição geral**, em seguida, selecione a **contratos** mosaico.

    ![Escolha o que mosaico "Contratos"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Ver o swagger
Consulte a [detalhes de swagger](/connectors/x12/). 

## <a name="learn-more"></a>Saiba mais
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  

