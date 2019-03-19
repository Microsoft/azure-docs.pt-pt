---
title: Mensagens EDIFACT para a integração empresarial de B2B - Azure Logic Apps | Documentos da Microsoft
description: Trocar mensagens EDIFACT no formato EDI para enterprise integração B2B no Azure Logic Apps Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.date: 07/26/2016
ms.openlocfilehash: bbcdad7c5496cd08994a613b07e1bc7c611e4572
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57876855"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens EDIFACT para enterprise integração B2B no Azure Logic Apps Enterprise Integration Pack

Antes de pode trocar mensagens EDIFACT para o Azure Logic Apps, tem de criar um contrato EDIFACT e armazenar esse contrato na sua conta de integração. Eis os passos para saber como criar um contrato EDIFACT.

> [!NOTE]
> Esta página abrange as funcionalidades EDIFACT para o Azure Logic Apps. Para obter mais informações, consulte [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Antes de começar

Eis os itens que precisa:

* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já definida e associada à sua subscrição do Azure  
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidas na sua conta de integração

> [!NOTE]
> Quando cria um contrato, o conteúdo nas mensagens que receber ou enviar de e para o parceiro tem de corresponder ao tipo de contrato.

Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [adicionar parceiros](logic-apps-enterprise-integration-partners.md), pode criar um contrato EDIFACT seguindo estes passos.

## <a name="create-an-edifact-agreement"></a>Crie um contrato EDIFACT 

1. Inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure"). 

2. No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, introduza "integração" e, em seguida, selecione **contas de integração**.

   ![Localize a conta de integração](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Se **todos os serviços** não aparecer, poderá ter de expandir o menu pela primeira vez. Na parte superior do menu fechado, selecione **Mostrar etiquetas de texto**.

3. Sob **contas de integração**, selecione a conta de integração em que pretende criar o contrato.

   ![Selecione onde pretende criar o contrato de conta de integração](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Escolher **contratos**. Se não tiver um mosaico de contratos, adicione primeiro o mosaico.   

   ![Escolha o que mosaico "Contratos"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na página de contratos, escolha **adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Sob **Add**, introduza um **nome** para seu contrato. Para **tipo de contrato**, selecione **EDIFACT**. Selecione o **parceiro do anfitrião**, **identidade do anfitrião**, **parceiro convidado**, e **identidade do convidado** para seu contrato.

   ![Fornecer detalhes do contrato](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Propriedade | Descrição |
   | --- | --- |
   | Name |Nome do contrato |
   | Tipo de Contrato | Deve ser EDIFACT |
   | Parceiro do Anfitrião |Tem de um contrato de parceiro de um anfitrião e convidado. O parceiro do anfitrião representa a organização que configura o contrato. |
   | Identidade do Anfitrião |Um identificador para o parceiro do anfitrião |
   | Parceiro Convidado |Tem de um contrato de parceiro de um anfitrião e convidado. O parceiro convidado representa a organização que está negociando com o parceiro do anfitrião. |
   | Identidade do Convidado |Um identificador para o parceiro convidado |
   | Definições de Receção |Estas propriedades aplicam-se a todas as mensagens recebidas por um contrato. |
   | Definições de Envio |Estas propriedades aplicam-se a todas as mensagens enviadas por um contrato. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurar a forma como seus identificadores de contrato receberam mensagens

Agora que definiu as propriedades de contrato, é possível configurar como o presente contrato identifica e processa mensagens de entrada recebidas do seu parceiro por meio do presente contrato.

1. Sob **Add**, selecione **receber definições**.
Configure estas propriedades com base no seu contrato com o parceiro que troca mensagens com. Para descrições das propriedades, consulte as tabelas nesta secção.

   **Receber definições** está organizada nestas secções: Identificadores, reconhecimento, esquemas, números de controlo, validação e as definições internas.

   ![Configurar "Receber definições"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Depois de terminar, certifique-se de guardar as definições ao escolher **OK**.

Agora o seu contrato está pronto para lidar com mensagens de entrada que está em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

| Propriedade | Descrição |
| --- | --- |
| UNB6.1 (Palavra-passe de Referência do Destinatário) |Introduza um valor de alfanumérico que variam entre 1 e 14 caracteres. |
| UNB6.2 (Qualificador de Referência do Destinatário) |Introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de dois caracteres. |

### <a name="acknowledgments"></a>Agradecimentos

| Propriedade | Descrição |
| --- | --- |
| Receção de Mensagem (CONTRL) |Selecione esta caixa de verificação a devolver um técnico de reconhecimento (CONTRL) para o remetente de intercâmbio. A confirmação é enviada para o remetente de intercâmbio com base nas definições de enviar para o contrato. |
| Reconhecimento (CONTRL) |Selecione esta caixa de verificação a devolver um reconhecimento (CONTRL) funcional para o remetente de intercâmbio a confirmação é enviada para o remetente de intercâmbio com base nas definições de enviar para o contrato. |

### <a name="schemas"></a>Esquemas

| Propriedade | Descrição |
| --- | --- |
| UNH2.1 (TIPO) |Selecione um tipo de conjunto de transações. |
| UNH2.2 (VERSÃO) |Introduza o número de versão de mensagem. (Mínimo, um caráter; máximo, três carateres). |
| UNH2.3 (VERSÃO) |Introduza o número de versão de mensagem. (Mínimo, um caráter; máximo, três carateres). |
| UNH2.5 (CÓDIGO DE ATRIBUÍDO ASSOCIADO) |Introduza o código atribuído. (No máximo, seis carateres. Tem de ser alfanumérico). |
| UNG2.1 (ID DE REMETENTE DA APLICAÇÃO) |Introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de 35 carateres. |
| UNG2.2 (QUALIFICADOR DO CÓDIGO DO REMETENTE DE APLICAÇÃO) |Introduza um valor de alfanumérico, com um máximo de quatro carateres. |
| SCHEMA |Selecione o esquema previamente carregado que pretende utilizar da sua conta de integração associada. |

### <a name="control-numbers"></a>Números de Controlo
| Propriedade | Descrição |
| --- | --- |
| Não permitir duplicados de intercâmbio de número de controlo |Para bloquear intercâmbios duplicados, selecione esta propriedade. Se selecionado, a ação de descodificação de EDIFACT verifica que o número de controlo de intercâmbio (UNB5) para o intercâmbio recebido não corresponde ao número de controlo do intercâmbio anteriormente processados. Se for detetada uma correspondência, o intercâmbio não foi processado. |
| Verificar UNB5 duplicados a cada (dias) |Se optar por não permitir números de controlo de intercâmbio duplicado, pode especificar o número de dias para efetuar a verificação fornecendo o valor adequado para esta definição. |
| Não permitir duplicados de números de controlo de Grupo |Para bloquear intercâmbios com números de controlo do grupo duplicado (UNG5), selecione esta propriedade. |
| Não permitir duplicados de números de controlo do conjunto de Transação |Para bloquear intercâmbios com números de controlo do conjunto de transações duplicado (UNH1), selecione esta propriedade. |
| Número de Controlo de Reconhecimento do EDIFACT |Para designar os números de referência de conjunto de transações para uso numa confirmação, introduza um valor para o prefixo, um intervalo de números de referência e um sufixo. |

### <a name="validations"></a>Validações

Quando concluir cada linha de validação, o outro é adicionado automaticamente. Se não especificar quaisquer regras, em seguida, validação usa a linha de "Predefinição".

| Propriedade | Descrição |
| --- | --- |
| Tipo de Mensagem |Selecione o tipo de mensagem EDI. |
| Validação de EDI |Execute a validação de EDI em tipos de dados, conforme definido pelo esquema EDI propriedades, restrições de comprimento, elementos de dados vazia e separadores à direita. |
| Validação Expandida |Se o tipo de dados não estiver EDI, a validação é o requisito de elemento de dados e permitido repetição, enumerações e dados de validação de comprimento de elemento (mín/máx.). |
| Permitir Zeros à Esquerda/Direita |Manter qualquer adicionais do esquerda ou à direita zero e carateres de espaço. Não remova estes carateres. |
| Cortar Zeros à Esquerda/Direita |Remova zero à esquerda nem à direita e carateres de espaço. |
| Política de Separador Decimal |Gere separadores à direita. <p>Selecione **Nepovoluje** para proibir delimitadores à direita e de separadores no intercâmbio recebido. Se o intercâmbio tem delimitadores à direita e os separadores, o intercâmbio é declarado não válido. <p>Selecione **opcional** para aceitar intercâmbios com ou sem delimitadores à direita e os separadores. <p>Selecione **obrigatório** quando o intercâmbio recebido tem de ter delimitadores à direita e os separadores. |

### <a name="internal-settings"></a>Definições Internas

| Propriedade | Descrição |
| --- | --- |
| Criar etiquetas XML vazias se forem permitidos separadores decimais |Selecione esta caixa de verificação para que o remetente de intercâmbio incluem etiquetas XML vazias para separadores à direita. |
| Dividir Intercâmbio como conjuntos de transação - suspender conjuntos de transação com erro|Analisa cada transação definida num intercâmbio num documento XML separado, aplicando o envelope apropriado para o conjunto de transações. Suspenda apenas os conjuntos de transação que a falha de validação. |
| Dividir Intercâmbio como conjuntos de transação - suspender intercâmbio com erro|Analisa cada transação definida num intercâmbio num documento XML separado, aplicando o envelope apropriado. Suspenda o intercâmbio inteiro quando um ou mais conjuntos de transação no intercâmbio a falha de validação. | 
| Preservar intercâmbio - suspender conjuntos transação com erro |Mantém o intercâmbio do utilizador intactos, cria um documento XML para o intercâmbio em lote inteiro. Suspenda apenas os conjuntos de transação que a falha de validação, enquanto continua a processar todos os outros conjuntos de transação. |
| Preservar Intercâmbio - suspender intercâmbio com erro |Mantém o intercâmbio do utilizador intactos, cria um documento XML para o intercâmbio em lote inteiro. Suspenda o intercâmbio inteiro quando um ou mais conjuntos de transação no intercâmbio a falha de validação. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurar a forma como o seu contrato envia mensagens

É possível configurar como o presente contrato identifica e processa mensagens de saída que envia para os seus parceiros através do presente contrato.

1.  Sob **Add**, selecione **enviar definições**.
Configure estas propriedades com base no seu contrato com o seu parceiro que troca mensagens com. Para descrições das propriedades, consulte as tabelas nesta secção.

    **Definições de envio** está organizada nestas secções: Identificadores, reconhecimento, os esquemas, Envelopes, conjuntos de carateres e separadores, números de controlo e validações.

    ![Configurar a "Definições de envio"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Depois de terminar, certifique-se de guardar as definições ao escolher **OK**.

Agora o seu contrato está pronto para lidar com mensagens de saída que estão em conformidade com as definições selecionadas.

### <a name="identifiers"></a>Identificadores

| Propriedade | Descrição |
| --- | --- |
| UNB1.2 (versão de sintaxe) |Selecione um valor entre **1** e **4**. |
| UNB2.3 (Endereço de Encaminhamento Inverso do Remetente) |Introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de 14 caracteres. |
| UNB3.3 (Endereço de Encaminhamento Inverso do Destinatário) |Introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de 14 caracteres. |
| UNB6.1 (Palavra-passe de Referência do Destinatário) |Introduza um valor de alfanumérico com um mínimo de um e um máximo de 14 caracteres. |
| UNB6.2 (Qualificador de Referência do Destinatário) |Introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de dois caracteres. |
| UNB7 (ID de Referência da Aplicação) |Introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de 14 caracteres |

### <a name="acknowledgment"></a>Confirmação
| Propriedade | Descrição |
| --- | --- |
| Receção de Mensagem (CONTRL) |Selecione esta caixa de verificação se o parceiro alojado espera receber um técnico de reconhecimento (CONTRL). Esta definição especifica que o parceiro alojado, o que está a enviar a mensagem, solicita uma confirmação do parceiro convidado. |
| Reconhecimento (CONTRL) |Selecione esta caixa de verificação se o parceiro alojado espera receber uma confirmação (CONTRL) funcional. Esta definição especifica que o parceiro alojado, o que está a enviar a mensagem, solicita uma confirmação do parceiro convidado. |
| Gerar ciclo SG1/SG4 para conjuntos de transações aceites |Se tiver escolhido solicitar uma confirmação funcional, selecione esta caixa de verificação para forçar a geração de SG1/SG4 loops em confirmações CONTRL funcionais para conjuntos de transações aceites. |

### <a name="schemas"></a>Esquemas
| Propriedade | Descrição |
| --- | --- |
| UNH2.1 (TIPO) |Selecione um tipo de conjunto de transações. |
| UNH2.2 (VERSÃO) |Introduza o número de versão de mensagem. |
| UNH2.3 (VERSÃO) |Introduza o número de versão de mensagem. |
| SCHEMA |Selecione o esquema a utilizar. Esquemas estão localizados na sua conta de integração. Para acessar seus esquemas, primeiro ligar a sua conta de integração à sua aplicação lógica. |

### <a name="envelopes"></a>Envelopes
| Propriedade | Descrição |
| --- | --- |
| UNB8 (Código de Prioridade de Processamento) |Introduza um valor alfabético que não é mais do que um caráter. |
| UNB10 (Contrato de Comunicação) |Introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de 40 carateres. |
| UNB11 (Indicador de Teste) |Selecione esta caixa de verificação para indicar que o intercâmbio gerado é dados de teste |
| Aplicar Segmento UNA (Conselho de Cadeia de Serviço) |Selecione esta caixa de verificação para gerar um segmento UNA para intercâmbio de envio. |
| Aplicar Segmentos UNG (Cabeçalho do Grupo de Função) |Selecione esta caixa de verificação para criar o agrupamento de segmentos no cabeçalho do grupo funcional nas mensagens enviadas para o parceiro convidado. Os seguintes valores são utilizados para criar os segmentos UNG: <p>Para **UNG1**, introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de seis carateres. <p>Para **UNG2.1**, introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de 35 carateres. <p>Para **UNG2.2**, introduza um valor de alfanumérico, com um máximo de quatro carateres. <p>Para **UNG3.1**, introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de 35 carateres. <p>Para **UNG3.2**, introduza um valor de alfanumérico, com um máximo de quatro carateres. <p>Para **UNG6**, introduza um valor de alfanumérico com um mínimo de um e um máximo de três caracteres. <p>Para **UNG7.1**, introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de três carateres. <p>Para **UNG7.2**, introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de três carateres. <p>Para **UNG7.3**, introduza um valor de alfanumérico com um mínimo de 1 caráter e um máximo de 6 carateres. <p>Para **UNG8**, introduza um valor de alfanumérico com um mínimo de um caráter e um máximo de 14 caracteres. |

### <a name="character-sets-and-separators"></a>Conjuntos de Carateres e Separadores

Além de definir o caráter, pode introduzir um conjunto de delimitadores a ser utilizado para cada tipo de mensagem diferente. Se um conjunto de carateres não for especificado para um esquema de mensagem em questão, o conjunto de carateres predefinido é utilizado.

| Propriedade | Descrição |
| --- | --- |
| UNB1.1 (Identificador de Sistema) |Selecione o conjunto a ser aplicado sobre o intercâmbio de saída de caracteres EDIFACT. |
| Esquema |Selecione um esquema da lista pendente. Depois de concluir cada linha, é automaticamente adicionada uma nova linha. Para o esquema selecionado, selecione o conjunto de separadores que pretende utilizar, com base nas descrições do separador abaixo. |
| Tipo de Entrada |Selecione um tipo de entrada na lista pendente. |
| Separador de Componente |Para separar os elementos de dados compostos, introduza um único caractere. |
| Separador do Elemento de Dados |Para separar os elementos de dados simples dentro de elementos de dados compostos, introduza um único caractere. |
| Terminador de Segmento |Para indicar o final de um segmento EDI, introduza um único caractere. |
| Sufixo |Selecione os carateres que é utilizado com o identificador de segmento. Se designar um sufixo, em seguida, o elemento de dados de terminador de segmento pode estar vazio. Se o terminador de segmento é deixado em branco, em seguida, tem de designar um sufixo. |

### <a name="control-numbers"></a>Números de Controlo
| Propriedade | Descrição |
| --- | --- |
| UNB5 (Número de Controlo de Nó) |Introduza um prefixo, um intervalo de valores para o número de controlo de intercâmbio e um sufixo. Estes valores são utilizados para gerar um intercâmbio de saída. O prefixo e sufixo são opcionais, enquanto o número de controlo é necessário. O número de controlo é incrementado para cada nova mensagem; o prefixo e sufixo permanecem os mesmos. |
| UNG5 (Número de Controlo de Grupo) |Introduza um prefixo, um intervalo de valores para o número de controlo de intercâmbio e um sufixo. Estes valores são utilizados para gerar o número de controlo de grupo. O prefixo e sufixo são opcionais, enquanto o número de controlo é necessário. O número de controlo é incrementado para cada nova mensagem até que seja atingido o valor máximo; o prefixo e sufixo permanecem os mesmos. |
| UNH1 (Número de Referência do Cabeçalho de Mensagem) |Introduza um prefixo, um intervalo de valores para o número de controlo de intercâmbio e um sufixo. Estes valores são utilizados para gerar o número de referência do cabeçalho de mensagem. O prefixo e sufixo são opcionais, enquanto o número de referência é necessário. O número de referência é incrementado para cada nova mensagem; o prefixo e sufixo permanecem os mesmos. |

### <a name="validations"></a>Validações

Quando concluir cada linha de validação, o outro é adicionado automaticamente. Se não especificar quaisquer regras, em seguida, validação usa a linha de "Predefinição".

| Propriedade | Descrição |
| --- | --- |
| Tipo de Mensagem |Selecione o tipo de mensagem EDI. |
| Validação de EDI |Execute a validação de EDI em tipos de dados, conforme definido pelas propriedades EDI do esquema, separadores decimais, elementos de dados vazia e restrições de comprimento. |
| Validação Expandida |Se o tipo de dados não estiver EDI, a validação é o requisito de elemento de dados e permitido repetição, enumerações e dados de validação de comprimento de elemento (mín/máx.). |
| Permitir Zeros à Esquerda/Direita |Manter qualquer adicionais do esquerda ou à direita zero e carateres de espaço. Não remova estes carateres. |
| Cortar Zeros à Esquerda/Direita |Remova esquerda ou à direita zero carateres. |
| Política de Separador Decimal |Gere separadores à direita. <p>Selecione **Nepovoluje** para proibir delimitadores à direita e de separadores no intercâmbio enviado. Se o intercâmbio tem delimitadores à direita e os separadores, o intercâmbio é declarado não válido. <p>Selecione **opcional** para enviar intercâmbios com ou sem delimitadores à direita e os separadores. <p>Selecione **obrigatório** se tem de ter o intercâmbio enviado delimitadores à direita e os separadores. |

## <a name="find-your-created-agreement"></a>Encontrar o contrato criado

1.  Depois de concluir a configuração de todas as suas propriedades de contrato, no **Add** página, selecione **OK** para concluir a criação de seu contrato e retornar à sua conta de integração.

    Agora seu contrato recentemente adicionado é apresentado no seu **contratos** lista.

2.  Também pode ver os contratos de na sua descrição de geral de conta de integração. No seu menu de conta de integração, escolha **descrição geral**, em seguida, selecione a **contratos** mosaico. 

    ![Escolha o que mosaico "Contratos"](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>Ver o ficheiro Swagger
Para ver os detalhes de Swagger para o conector do EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Saiba mais
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  

