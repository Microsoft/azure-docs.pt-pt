---
title: Automatizar tarefas com vários serviços Azure
description: Tutorial - Criar fluxos de trabalho automatizados para processar e-mails com Apps Azure Logic, Azure Storage e Azure Functions
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 02/27/2020
ms.openlocfilehash: 7e58dcf8206ae9feab4d8a09517bf9efda244dd5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451570"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>Tutorial: Automatizar tarefas para processar e-mails utilizando apps Azure Logic, Funções Azure e Armazenamento Azure

O Azure Logic Apps ajuda-o a automatizar fluxos de trabalho e a integrar dados nos serviços do Azure, nos serviços Microsoft, noutras aplicações de software como serviço (Saas) e em sistemas no local. Este tutorial mostra como pode criar uma [aplicação lógica](../logic-apps/logic-apps-overview.md) que processa e-mails recebidos e anexos. Esta aplicação lógica analisa o conteúdo do e-mail, guarda o conteúdo para o armazenamento do Azure e envia notificações para rever esse conteúdo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar o [Armazenamento do Azure](../storage/common/storage-introduction.md) e o Explorador de Armazenamento para verificar e-mails e anexos guardados.
> * Criar uma [função do Azure](../azure-functions/functions-overview.md) que remove o HTML dos e-mails. Este tutorial inclui o código que pode utilizar para esta função.
> * Criar uma aplicação lógica em branco.
> * Adicionar um acionador que monitoriza os e-mails para verificar se há anexos.
> * Adicionar uma condição que verifica se os e-mails têm anexos.
> * Adicionar uma ação que chama a função do Azure quando um e-mail tem anexos.
> * Adicionar uma ação que cria blobs de armazenamento para e-mails e anexos.
> * Adicionar uma ação que envia notificações de e-mail.

Quando terminar, a aplicação lógica é semelhante a este fluxo de trabalho a alto nível:

![Aplicação lógica concluída de alto nível](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma conta de e-mail de um fornecedor de e-mail que o Logic Apps suporte, como o Outlook do Office 365, o Outlook.com ou o Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](/connectors/).

  Esta aplicação lógica utiliza uma conta de trabalho ou escola. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

  > [!IMPORTANT]
  > Se quiser utilizar o conector do Gmail, apenas as contas de negócios da G-Suite podem utilizar este conector sem restrições em aplicações lógicas. Se tiver uma conta de consumo do Gmail, pode utilizar este conector apenas com serviços específicos aprovados pela Google, ou pode [criar uma aplicação para clientes da Google para utilizar para autenticação com o seu conector Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) Para obter mais informações, consulte [as políticas de segurança de dados e privacidade para conectores google em Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Transferir e instalar o [Explorador de Armazenamento do Microsoft Azure](https://storageexplorer.com/) gratuito. Esta ferramenta ajuda-o a verificar se o seu contentor de armazenamento está configurado corretamente.

## <a name="set-up-storage-to-save-attachments"></a>Configurar o armazenamento para guardar anexos

Pode guardar os e-mails e anexos recebidos como blobs num [contentor de armazenamento do Azure](../storage/common/storage-introduction.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Antes de criar um recipiente de armazenamento, [crie uma conta de armazenamento](../storage/common/storage-account-create.md) com estas definições no **separador Básicos** no portal Azure:

   | Definição | Valor | Descrição |
   |---------|-------|-------------|
   | **Subscrição** | <*Nome de subscrição Azure*> | O nome para a subscrição do Azure |  
   | **Grupo de recursos** | <*Grupo de recursos Azure*> | O nome do [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) utilizado para organizar e gerir os recursos relacionados. Este exemplo utiliza "LA-Tutorial-RG". <p>**Nota:** os grupos de recursos existem dentro de regiões específicas. Embora os itens neste tutorial possam não estar disponíveis em todas as regiões, tente utilizar a mesma região sempre que possível. |
   | **Nome da conta de armazenamento** | <*Nome da conta de armazenamento Azure*> | O nome da sua conta de armazenamento, que deve ter 3-24 caracteres e pode conter apenas letras e números minúsculos. Este exemplo utiliza "attachmentstorageacct". |
   | **Localização** | <*Região de Azure*> | A região onde armazenar informações sobre a sua conta de armazenamento. Este exemplo usa "West US". |
   | **Desempenho** | Standard | Esta definição especifica os tipos de dados suportados e os suportes de dados para armazenar dados. Veja [Tipos de contas de armazenamento](../storage/common/storage-introduction.md#types-of-storage-accounts). |
   | **Tipo de conta** | Fins gerais | O [tipo de conta de armazenamento](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **Replicação** | Armazenamento localmente redundante (LRS) | Esta definição especifica de que forma os seus dados são copiados, armazenados, geridos e sincronizados. Ver [armazenamento localmente redundante (LRS): Redundância de dados de baixo custo para o armazenamento Azure](../storage/common/storage-redundancy.md). |
   | **Camada de acesso (predefinida)** | Mantenha a regulação atual. |
   ||||

   No separador **Avançado,** selecione esta definição:

   | Definição | Valor | Descrição |
   |---------|-------|-------------|
   | **Transferência segura necessária** | Desativado | Esta definição especifica a segurança necessária para pedidos provenientes de ligações. Veja [Require secure transfer](../storage/common/storage-require-secure-transfer.md) (Transferência segura necessária). |
   ||||

   Para criar a sua conta de armazenamento, também pode utilizar o [Azure PowerShell](../storage/common/storage-account-create.md?tabs=powershell) ou a [CLI do Azure](../storage/common/storage-account-create.md?tabs=azure-cli).

1. Quando terminar, selecione **Review + create**.

1. Depois de o Azure implementar a sua conta de armazenamento, encontre a sua conta de armazenamento e obtenha a chave de acesso da conta de armazenamento:

   1. No menu da conta de armazenamento, em **Definições**, selecione **Chaves de acesso**.

   1. Copie o nome da sua conta de armazenamento e **a chave1**, e guarde esses valores num lugar seguro.

      ![Copie e cole o nome e a chave da conta de armazenamento](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   Para obter a chave de acesso à sua conta de armazenamento, também pode utilizar o [Azure PowerShell](/powershell/module/az.storage/get-azstorageaccountkey) ou a [CLI do Azure](/cli/azure/storage/account/keys?view=azure-cli-latest.md#az-storage-account-keys-list).

1. Crie um contentor de armazenamento de blobs para os anexos de e-mails.

   1. No menu da conta de armazenamento, selecione **Descrição Geral**. No painel de visão geral, selecione **Recipientes**.

      ![Adicionar contentor do armazenamento de blobs](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. Depois de a página **Contentores** abrir, na barra de ferramentas, selecione **Contentor**.

   1. Sob **o novo recipiente,** insira `attachments` como o nome do seu recipiente. Ao **nível de acesso público**, selecione Container **(acesso anónimo para recipientes e bolhas)**  >  **OK**.

      Quando terminar, pode encontrar o contentor de armazenamento na sua conta de armazenamento aqui no portal do Azure:

      ![Contentor de armazenamento terminado](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   Para criar um recipiente de armazenamento, também pode utilizar [a Azure PowerShell](/powershell/module/az.storage/new-azstoragecontainer) ou [a Azure CLI](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create).

Depois, ligue o Explorador de Armazenamento à conta de armazenamento.

## <a name="set-up-storage-explorer"></a>Configurar o Explorador de Armazenamento

Agora, ligue o Explorador de Armazenamento à conta de armazenamento, de modo a verificar que a sua aplicação lógica consegue guardar corretamente os anexos como blobs no contentor.

1. Lançar Microsoft Azure Storage Explorer.

   O Explorador de Armazenamento pede-lhe uma ligação à sua conta de armazenamento.

1. No painel **de armazenamento Connect to Azure,** selecione Utilize o nome da conta de armazenamento e a **chave**  >  **Seguinte**.

   ![Explorador de Armazenamento - Ligar a conta de armazenamento](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > Se não aparecer nenhuma solicitação na barra de ferramentas do Explorador de Armazenamento, **selecione Adicionar uma conta**.

1. No **nome Display,** forneça um nome amigável para a sua ligação. Em **Nome da conta**, indique o nome da sua conta de armazenamento. Na **tecla Conta**, forneça a chave de acesso que guardou anteriormente e selecione **Seguinte**.

1. Confirme as informações de ligação e, em seguida, **selecione Connect**.

   O Storage Explorer cria a ligação e mostra a sua conta de armazenamento na janela explorer sob as contas de armazenamento **&**  >  **Locais anexas**.

1. Para encontrar o seu recipiente de armazenamento de **bolhas,** em Contas de Armazenamento, expanda a sua conta de armazenamento, que é **o anexo** aqui, e expanda **os Recipientes Blob** onde encontra o recipiente **de anexos,** por exemplo:

   ![Explorador de Armazenamento - encontrar o contentor de armazenamento](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Depois, crie uma [função do Azure](../azure-functions/functions-overview.md) que remove o HTML dos e-mails recebidos.

## <a name="create-function-to-clean-html"></a>Criar uma função para limpar o HTML

Agora, utilize o fragmento de código fornecido nestes passos para criar uma função do Azure que remove o HTML de cada e-mail recebido. Desta forma, o conteúdo dos e-mails fica mais limpo e é processado mais facilmente. Depois, pode chamar esta função a partir da sua aplicação lógica.

1. Antes de poder criar uma função, [crie uma aplicação de funções](../azure-functions/functions-create-function-app-portal.md) com estas definições:

   | Definição | Valor | Descrição |
   | ------- | ----- | ----------- |
   | **Nome da aplicação** | <*função-app-nome*> | O nome da sua aplicação de função, que deve ser globalmente único em todo o Azure. Este exemplo já usa "CleanTextFunctionApp", por isso forneça um nome diferente, como "MyCleanTextFunctionApp-<*o seu nome*>" |
   | **Subscrição** | <*seu-Azure-nome de subscrição*> | A mesma subscrição do Azure que utilizou anteriormente |
   | **Grupo de Recursos** | LA-Tutorial-RG | O mesmo grupo de recursos do Azure que utilizou anteriormente |
   | **SO** | <*seu sistema operativo*> | Selecione o sistema operativo que suporta a sua linguagem de programação de funções favorita. Para este exemplo, selecione **Windows**. |
   | **Plano de Alojamento** | Plano de Consumo | Esta definição determina como alocar e dimensionar os recursos, como o poder de computação, para executar a aplicação de funções. Veja [Hosting plans comparison](../azure-functions/functions-scale.md) (Comparação dos planos de alojamento). |
   | **Localização** | E.U.A. Oeste | A mesma região que utilizou anteriormente |
   | **Pilha de Runtime** | Linguagem preferencial | Selecione um tempo de execução que suporte a sua linguagem de programação de funções favorita. Selecione **.NET** para as funções C# e F#. |
   | **Armazenamento** | cleantextfunctionstorageacct | Crie uma conta de armazenamento para a sua aplicação de funções. Utilize apenas letras minúsculas e números. <p>**Nota:** Esta conta de armazenamento contém as suas aplicações de função e difere da sua conta de armazenamento previamente criada para anexos de e-mail. |
   | **Application Insights** | Desativar | Liga a monitorização de aplicações com [Insights de Aplicação,](../azure-monitor/app/app-insights-overview.md)mas para este tutorial, selecione **Disable**  >  **Apply**. |
   ||||

   Se a sua aplicação de função não abrir automaticamente após a implementação, na caixa de pesquisa do [portal Azure,](https://portal.azure.com) encontre e selecione **a App de Função**. Na **Aplicação de Função,** selecione a sua aplicação de função.

   ![Selecionar aplicação de funções](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   Caso contrário, o Azure abre automaticamente a sua aplicação de funções, conforme mostrado aqui:

   ![Aplicação de funções criada](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Para criar uma aplicação de função, também pode utilizar os modelos [Azure CLI](../azure-functions/create-first-function-cli-csharp.md), ou [PowerShell e Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

1. Na lista **de Aplicações de Função,** expanda a sua aplicação de função, se não já expandida. No âmbito da sua aplicação de funções, selecione **Funções**. Na barra de ferramentas das funções, selecione **Nova função**.

   ![Criar função nova](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. Em **Escolha um modelo abaixo ou vá para o arranque rápido,** selecione o modelo de gatilho **HTTP.**

   ![Selecione o modelo de gatilho HTTP](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   O Azure cria uma função utilizando um modelo específico do idioma para uma função desencadeada http.

1. No painel **Nova Função**, em **Nome**, introduza `RemoveHTMLFunction`. Mantenha o **nível de Autorização** definido para **Função** e selecione **Criar**.

   ![Dê um nome à sua função](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

1. Quando o editor estiver aberto, substitua o código do modelo por este código de exemplo, que remove o HTML e devolve ps resultados ao chamador:

   ```csharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log) {

      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

1. Quando tiver terminado, selecione **Guardar**. Para testar a sua função, na extremidade direita do editor, sob o ícone de seta ( **<** ) selecione **Test**.

   ![Abrir o painel "Test" ("Teste")](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. No painel **de teste,** no **corpo do Pedido,** introduza esta linha e selecione **Executar**.

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![Testar a função](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   A janela **Saída** mostra o resultado da função:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Depois de confirmar que a função está a funcionar, crie a aplicação lógica. Embora este tutorial mostre como criar uma função que remove o HTML dos e-mails, o Logic Apps também fornece um conector **HTML para Texto**.

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. Na caixa de pesquisa de nível superior Azure, insira `logic apps` e selecione **Aplicações Lógicas**.

   ![Localizar e selecionar "Aplicações lógicas"](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. No painel **de aplicações** lógicas, selecione **Adicionar**.

   ![Adicionar novo aplicativo de lógica](./media/tutorial-process-email-attachments-workflow/add-new-logic-app.png)

1. No painel **da Aplicação Lógica,** forneça detalhes sobre a sua aplicação lógica, como mostrado aqui. Depois de terminar, selecione **Review + create**.

   ![Indicar as informações da aplicação lógica](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Definição | Valor | Descrição |
   | ------- | ----- | ----------- |
   | **Subscrição** | <*seu-Azure-nome de subscrição*> | A mesma subscrição do Azure que utilizou anteriormente |
   | **Grupo de recursos** | LA-Tutorial-RG | O mesmo grupo de recursos do Azure que utilizou anteriormente |
   | **Nome da Aplicação Lógica** | LA-ProcessAttachment | O nome para a aplicação lógica |
   | **Selecione a localização** | E.U.A. Oeste | A mesma região que utilizou anteriormente |
   | **Log Analytics** | Desligado | Para este tutorial, selecione a definição **Off.** |
   ||||

1. Depois de o Azure implementar a sua aplicação, na barra de ferramentas Azure, selecione o ícone de notificações e selecione **Ir para o recurso**.

   ![A partir da lista de notificações do Azure, selecione "Ir para o recurso"](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. Depois de o Logic Apps Designer abrir e mostrar uma página com um vídeo de introdução e modelos para padrões de aplicações lógicas comuns. Em **Modelos**, selecione **Aplicação Lógica em Branco**.

   ![Selecione modelo de aplicativo de lógica em branco](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Em seguida, adicione um [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts) que escuta os e-mails recebidos que têm anexos. Todas as aplicações lógicas têm de ser iniciadas com um acionador, que é acionado quando um evento específico acontece ou quando dados novos satisfazem uma condição específica. Para obter mais informações, veja [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Monitorizar os e-mails recebidos

1. No designer na caixa de pesquisa, introduza `when new email arrives` como filtro. Selecione o acionador para o seu fornecedor de e-mail: **Quando é recebido um e-mail novo - <*fornecedor de e-mail*>**

   Por exemplo:

   ![Selecione este acionador para o fornecedor de e-mail: "When a new email arrives"](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Relativamente a contas escolares ou profissionais do Azure, selecione Office 365 Outlook (Outlook do Office 365).

   * Quanto a contas Microsoft pessoais, selecione Outlook.com.

1. Se lhe forem pedidas credenciais, inicie sessão na sua conta de e-mail para que o Logic Apps se possa ligar à mesma.

1. Agora, indique os critérios que o acionador utiliza para filtrar os e-mails novos.

   1. Especifique as definições descritas abaixo para verificar e-mails.

      ![Especifique a pasta, o intervalo e a frequência para a verificação dos e-mails](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Definição | Valor | Descrição |
      | ------- | ----- | ----------- |
      | **Pasta** | A Receber | A pasta de e-mail a verificar |
      | **Tem Anexo** | Sim | Obter apenas os e-mails com anexos. <p>**Nota:** o acionador não remove e-mails da sua conta; verifica apenas as mensagens novas e só processa os e-mails que correspondam ao filtro do assunto. |
      | **Incluir Anexos** | Sim | Obter os anexos como entrada para o seu fluxo de trabalho em vez de verificar apenas os anexos. |
      | **Intervalo** | 1 | O número de intervalos de espera entre verificações |
      | **Frequência** | Minuto | A unidade de tempo para cada intervalo entre verificações |
      ||||

   1. Na nova lista **de parâmetros** Adicionar, selecione **Filtro de Assunto**.

   1. Depois da caixa **de filtro de assunto** aparecer na ação, especifique o assunto listado aqui:

      | Definição | Valor | Descrição |
      | ------- | ----- | ----------- |
      | **Filtro de Assunto** | `Business Analyst 2 #423501` | O texto a localizar no assunto do e-mail |
      ||||

1. Para ocultar os detalhes do acionador por agora, clique no interior da barra de título do mesmo.

   ![Fechar forma para ocultar os detalhes](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

   A sua aplicação lógica está agora ativa, mas não faz mais nada que não verificar os e-mails. Em seguida, adicione uma condição que especifique critérios para continuar o fluxo de trabalho.

## <a name="check-for-attachments"></a>Verificar a existência de anexos

Agora, adicione uma condição que seleciona apenas os e-mails que têm anexos.

1. Sob o gatilho, selecione **Novo passo**.

   !["Novo passo"](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. Em **Escolha uma ação,** na caixa de pesquisa, insira `condition` . Selecione esta ação: **Condição**

   ![Selecione "Condição"](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. Mude o nome da condição com uma descrição melhor. Na barra de título da condição, selecione as elipses **(...**) botão > **Rename**.

      ![Mudar o nome da condição](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. Mude o nome da condição com a descrição `If email has attachments and key subject phrase`

1. Criar uma condição que verifica se os e-mails têm anexos.

   1. Na primeira linha por baixo de **E**, clique no interior da caixa à esquerda. Na lista de conteúdo dinâmico que aparece, selecione a propriedade **Tem anexo**.

      ![Screenshot que mostra a propriedade "E" para a condição e a seleção de propriedade "Tem Anexo".](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. Na caixa do meio, mantenha o operador **é igual a**.

   1. Na caixa certa, insira-se **como** o valor a comparar com o valor da propriedade **Has Attachment** a partir do gatilho.

      ![Criar condição](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      Se ambos os valores forem iguais, o e-mail tem, pelo menos, um anexo, a condição é transmitida e o fluxo de trabalho continua.

   Na definição da aplicação lógica subjacente, que pode ver na janela do editor de código, esta condição terá um aspeto semelhante a este exemplo:

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

### <a name="test-your-condition"></a>Testar a condição

Agora, teste para verificar se a condição funciona corretamente:

1. Se a sua aplicação lógica ainda não estiver em execução, selecione **Executar** na barra de ferramentas do designer.

   Este passo inicia manualmente a aplicação lógica sem ter de esperar que o intervalo que especificou passe. No entanto, nada acontece enquanto o e-mail de teste não chegar à sua caixa de entrada.

1. Envie um e-mail para si próprio que cumpra estes critérios:

   * O seu email tem o texto especificado no **filtro Subject** do gatilho: `Business Analyst 2 #423501`

   * O e-mail tem um anexo. Por agora, crie apenas um ficheiro de texto vazia e anexe-o ao e-mail.

   Quando o e-mail chegar, a aplicação lógica verifica a existência de anexos e o texto do assunto especificado. Se a condição for aprovada, o acionador é acionado e faz com que o motor do Logic Apps crie uma instância da aplicação lógica e inicie o fluxo de trabalho.

1. Para verificar se o gatilho disparou e a aplicação lógica correu com sucesso, no menu de aplicações lógicas, selecione **Overview**.

   ![Verificar o acionador e o histórico de execuções](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Se a sua aplicação lógica não for acionada ou executada, apesar de o acionador ter sido bem-sucedido, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

Em seguida, defina as ações a realizar para o ramo **Se verdadeiro**. Para guardar o e-mail juntamente com eventuais anexos, remova todo o HTML do corpo do e-mail e crie blobs no contentor de armazenamento para a mensagem e os anexos.

> [!NOTE]
> A aplicação lógica não tem de fazer nada no ramo **Se falso** se os e-mails não tiverem anexos.
> Como exercício de bónus após concluir este tutorial, pode adicionar qualquer ação adequada que queira realizar no ramo **Se falso**.

## <a name="call-removehtmlfunction"></a>Chamar RemoveHTMLFunction

Este passo adiciona a função do Azure criada anteriormente à sua aplicação lógica e transmite o conteúdo do corpo de e-mail do acionador de e-mail à sua função.

1. No menu da aplicação lógica, selecione **Estruturador da Aplicação Lógica**. No ramo **Se verdadeiro,** selecione **Adicione uma ação**.

   ![Dentro de "Se verdadeiro", adicionar ação](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. Na caixa de pesquisa, encontre "Funções Azure", e selecione esta ação: **Escolha uma função Azure - Funções Azure**

   ![Selecionar a ação “Escolher uma função do Azure”](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. Selecione a sua aplicação de função previamente criada, que está `CleanTextFunctionApp` neste exemplo:

   ![Selecionar a aplicação de funções do Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

1. Agora, selecione a função **RemoveHTMLFunction**

   ![Selecionar a função do Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

1. Mude o nome da forma da função com a descrição `Call RemoveHTMLFunction to clean email body`

1. Agora, especifique a entrada para a função processar.

   1. Em **Corpo do Pedido**, introduza este texto com um espaço à esquerda:

      `{ "emailBody":`

      Enquanto trabalha nesta entrada nos passos seguintes, obterá um erro relativo a JSON inválido até que a sua entrada esteja corretamente formatada como JSON. Quando testou esta função anteriormente, a entrada especificada para a mesma utilizou JavaScript Object Notation (JSON). Por isso, o corpo do pedido também tem de utilizar o mesmo formato.

      Além disso, quando o cursor está dentro da caixa **Corpo do pedido**, é apresentada uma lista de conteúdo dinâmico para que possa selecionar os valores das propriedades disponíveis de ações anteriores.

   1. Na lista de conteúdo dinâmico, em **Quando é recebido um novo e-mail**, selecione a propriedade **Corpo**. Depois desta propriedade, não se esqueça de adicionar a chaveta de fecho: `}`

      ![Especificar o corpo do pedido que vai ser transmitido à função](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   Quando estiver pronto, a entrada para a sua função terá o aspeto deste exemplo:

   ![Concluiu o corpo do pedido a transmitir à sua função](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

1. Guarde a sua aplicação lógica.

Em seguida, adicione uma ação que cria um blob no contentor de armazenamento para que possa guardar o corpo do e-mail.

## <a name="create-blob-for-email-body"></a>Criar blob para o corpo do e-mail

1. No bloco **Se verdadeiro** e sob a sua função Azure, selecione Adicione **uma ação**.

1. Na caixa de pesquisa, insira `create blob` como filtro e selecione esta ação: **Crie blob**

   ![Adicionar ação para criar blob para o corpo do e-mail](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. Crie uma ligação com estas definições, conforme mostrado e descrito aqui. Quando concluir, selecione **Criar**.

   ![Criar ligação à conta de armazenamento](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Definição | Valor | Descrição |
   | ------- | ----- | ----------- |
   | **Nome de conexão** | AttachmentStorageConnection | Um nome descritivo para a ligação |
   | **Conta de Armazenamento** | attachmentstorageacct | O nome da conta de armazenamento que criou anteriormente para guardar anexos |
   ||||

1. Mude o nome da ação **Create blob** pela descrição `Create blob for email body`

1. Na ação **Criar blob**, indique estas informações e selecione estes campos para criar o blob, conforme mostrado e descrito:

   ![Indicar as informações do blob para o corpo do e-mail](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Definição | Valor | Descrição |
   | ------- | ----- | ----------- |
   | **Folder path** | /attachments | O caminho e o nome do contentor que criou anteriormente. Neste exemplo, clique no ícone de pasta e, em seguida, selecione o contentor "/attachments". |
   | **Blob name** | Campo **From** | Neste exemplo, utilize o nome do remetente como o nome do blob. Clique no interior desta caixa para que a lista de conteúdo dinâmico seja apresentada e, em seguida, selecione o campo **De** na ação **Quando é recebido um novo e-mail**. |
   | **Blob content** | Campo **Content** | Neste exemplo, utilize o corpo do e-mail sem HTML como o conteúdo do blob. Clique no interior desta caixa para que a lista de conteúdo dinâmico seja apresentada e, em seguida, selecione **Corpo** na ação **Call RemoveHTMLFunction to clean email body**. |
   ||||

   Quando estiver pronto, a ação terá o aspeto deste exemplo:

   ![Screenshot que mostra um exemplo de uma ação "Create blob" acabada.](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. Guarde a sua aplicação lógica.

### <a name="check-attachment-handling"></a>Verificar o processamento de anexos

Agora, teste se a sua aplicação lógica processa os e-mails da forma que especificou:

1. Se a sua aplicação lógica ainda não estiver em execução, selecione **Executar** na barra de ferramentas do designer.

1. Envie um e-mail para si próprio que cumpra estes critérios:

   * O seu email tem o texto especificado no **filtro Subject** do gatilho: `Business Analyst 2 #423501`

   * O e-mail tem, pelo menos, um anexo. Por enquanto, basta criar um ficheiro de texto vazio e anexar esse ficheiro ao seu e-mail.

   * O seu e-mail tem alguns conteúdos de teste no corpo, por exemplo: `Testing my logic app`

   Se a sua aplicação lógica não for acionada ou executada, apesar de o acionador ter sido bem-sucedido, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

1. Confirme que a aplicação lógica guardou o e-mail no contentor de armazenamento correto.

   1. No Explorador de Armazenamento, expandir os anexos de anexos de contas de armazenamento **locais &**  >  **Storage Accounts**  >  **anexados (Chave)**  >  **anexos de contentores blob**  >  **attachments**.

   1. Verifique o contentor **attachments** relativamente ao e-mail.

      Neste momento, apenas o e-mail aparece no contentor, porque a aplicação lógica ainda não processa anexos.

      ![Verificar o e-mail guardado no Explorador de Armazenamento](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. Quando tiver terminado, elimine o e-mail no Explorador de Armazenamento.

1. Opcionalmente, para testar o ramo **Se falso**, que não faz nada nesta fase, pode enviar um e-mail que não cumpra os critérios.

Em seguida, adicione um ciclo para processar todos os anexos de e-mail.

## <a name="process-attachments"></a>Processar anexos

Para processar cada anexo no e-mail, adicione um ciclo **Para cada** ao fluxo de trabalho da sua aplicação lógica.

1. Sob a **bolha Create para formato de corpo de e-mail,** selecione Adicione uma **ação**.

   ![Adicionar ciclo "for each"](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. Em **Escolha uma ação,** na caixa de pesquisa, insira `for each` como filtro e selecione esta ação: **Para cada**

   ![Selecione "Para cada um"](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. Mude o nome do ciclo com a descrição `For each email attachment`

1. Agora, especifique os dados que o ciclo vai processar. Clique no interior da caixa **Selecionar uma saída dos passos anteriores** para que a lista de conteúdo dinâmico abra e, em seguida, selecione **Anexos**.

   ![Selecionar "Attachments"](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   O campo **Anexos** transmite uma matriz que contém todos os anexos incluídos num e-mail. O ciclo **For each** repete as ações em cada item que é transmitido com a matriz.

1. Guarde a sua aplicação lógica.

Em seguida, adicione a ação que guarda cada anexo como um blob no seu contentor de armazenamento **attachments**.

## <a name="create-blob-for-each-attachment"></a>Criar blob para cada anexo

1. No ciclo de **anexo para cada e-mail,** selecione **Adicione uma ação** para que possa especificar a tarefa a executar em cada anexo encontrado.

   ![Adicionar ação ao ciclo](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. Na caixa de pesquisa, insira `create blob` como filtro e, em seguida, selecione esta ação: **Criar bolha**

   ![Adicionar ação para criar blob](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

1. Mude o nome da ação **Create blob 2** pela descrição `Create blob for each email attachment`

1. Na ação **Criar blob para cada anexo de e-mail**, indique estas informações e selecione as propriedades para cada blob que pretende criar, conforme mostrado e descrito:

   ![Indicar as informações dos blobs](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Definição | Valor | Descrição |
   | ------- | ----- | ----------- |
   | **Folder path** | /attachments | O caminho e o nome do contentor que criou anteriormente. Neste exemplo, clique no ícone de pasta e, em seguida, selecione o contentor "/attachments". |
   | **Blob name** | Campo **Name** | Neste exemplo, utilize o nome do anexo como o nome do blob. Clique no interior desta caixa para que a lista de conteúdo dinâmico seja apresentada e, em seguida, selecione o campo **Nome** na ação **Quando é recebido um novo e-mail**. |
   | **Blob content** | Campo **Content** | Neste exemplo, utilize o campo **Conteúdo** como o conteúdo do blob. Clique no interior desta caixa para que a lista de conteúdo dinâmico seja apresentada e, em seguida, selecione **Conteúdo** na ação **Quando é recebido um novo e-mail**. |
   ||||

   Quando estiver pronto, a ação terá o aspeto deste exemplo:

   ![Concluiu a ação “Criar blob”](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

1. Guarde a sua aplicação lógica.

### <a name="check-attachment-handling"></a>Verificar o processamento de anexos

Em seguida, teste se a sua aplicação lógica processa os anexos da forma que especificou:

1. Se a sua aplicação lógica ainda não estiver em execução, selecione **Executar** na barra de ferramentas do designer.

1. Envie um e-mail para si próprio que cumpra estes critérios:

   * O seu email tem o texto especificado na propriedade do **filtro subjecto** do gatilho: `Business Analyst 2 #423501`

   * O seu e-mail tem, pelo menos, dois anexos. Por agora, crie apenas dois ficheiros de texto vazios e anexe-os ao e-mail.

   Se a sua aplicação lógica não for acionada ou executada, apesar de o acionador ter sido bem-sucedido, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

1. Confirme que a aplicação lógica guardou o e-mail e os anexos no contentor de armazenamento correto.

   1. No Explorador de Armazenamento, expandir os anexos de anexos de contas de armazenamento **locais &**  >  **Storage Accounts**  >  **anexados (Chave)**  >  **anexos de contentores blob**  >  **attachments**.

   1. Verifique o contentor **attachments** relativamente ao e-mail e aos anexos.

      ![Verificar o e-mail e os anexos guardados](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. Quando tiver terminado, elimine o e-mail e os anexos no Explorador de Armazenamento.

Em seguida, adicione uma ação para que a sua aplicação lógica envia um e-mail para rever os anexos.

## <a name="send-email-notifications"></a>Enviar notificações por e-mail

1. No ramo **Se verdadeiro,** sob o laço **de anexo para cada e-mail,** selecione **Adicione uma ação**.

   ![Adicionar ação no ciclo "for each"](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. Na caixa de pesquisa, insira `send email` como filtro e, em seguida, selecione a ação "enviar e-mail" para o seu fornecedor de e-mail.

   Para filtrar a lista de ações para um serviço específico, pode selecionar primeiro o conector.

   ![Selecionar a ação "send email" relativa ao seu fornecedor de e-mail](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Relativamente a contas escolares ou profissionais do Azure, selecione Office 365 Outlook (Outlook do Office 365).

   * Quanto a contas Microsoft pessoais, selecione Outlook.com.

1. Se lhe forem pedidas credenciais, inicie sessão na sua conta de e-mail para que o Logic Apps crie uma ligação para a sua conta de e-mail.

1. Mude o nome da ação **Send an email** pela descrição `Send email for review`

1. Indique as informações desta ação e selecione os campos que pretende incluir no e-mail, conforme mostrado e descrito. Para adicionar linhas em branco a uma caixa de edição, prima Shift + Enter.

   ![Enviar notificação por e-mail](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Se não conseguir encontrar um campo esperado na lista de conteúdos dinâmicos, selecione **Ver mais** ao lado de Quando chegar um **novo e-mail**.

   | Definição | Valor | Notas |
   | ------- | ----- | ----- |
   | **Para** | <*destinatário-endereço de e-mail*> | Para fins de teste, pode utilizar o seu próprio endereço de e-mail. |
   | **Assunto**  | ```ASAP - Review applicant for position:``` **Assunto** | O assunto do e-mail que pretende incluir. Clique no interior desta caixa, introduza o texto de exemplo e, na lista de conteúdo dinâmico, selecione o campo **Assunto** em **Quando é recebido um novo e-mail**. |
   | **Corpo** | ```Please review new applicant:``` <p>```Applicant name:```**De** <p>```Application file location:``` **Caminho** <p>```Application email content:``` **Corpo** | O conteúdo do corpo do e-mail. Clique no interior desta caixa, introduza o texto de exemplo e, na lista de conteúdo dinâmico, selecione estes campos: <p>- **From**, em **When a new email arrives** </br>- **Path**, em **Create blob for email body** </br>- **Body**, em **Call RemoveHTMLFunction to clean email body** |
   ||||

   > [!NOTE]
   > Se selecionar um campo que contém uma matriz, como o campo **Conteúdo**, que é uma matriz que contém anexos, o estruturador adiciona automaticamente um ciclo “Para cada” em torno da ação que referencia esse campo.
   > Desta forma, a sua aplicação lógica pode realizar essa ação em cada item da matriz.
   > Para remover o laço, retire o campo para a matriz, mova a ação de referência para fora do laço, selecione as elipses **(...**) na barra de título do loop e selecione **Delete**.

1. Guarde a sua aplicação lógica.

Teste a sua aplicação lógica, que é agora semelhante a este exemplo:

![Aplicação lógica concluída](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Executar a aplicação lógica

1. Envie um e-mail para si próprio que cumpra estes critérios:

   * O seu email tem o texto especificado na propriedade do **filtro subjecto** do gatilho: `Business Analyst 2 #423501`

   * O seu email tem um ou mais anexos. Pode reutilizar um ficheiro de texto vazio do teste anterior. Para um cenário realista, anexe um ficheiro de currículo.

   * O corpo do e-mail tem este texto, que pode copiar e colar:

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

1. Execute a aplicação lógica. Se tiver êxito, a aplicação lógica envia-lhe um e-mail semelhante ao deste exemplo:

   ![Notificação de e-mail enviada pela aplicação lógica](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Se não receber nenhuma mensagem de e-mail, verifique a pasta de lixo do e-mail. O filtro de lixo de e-mail poderá redirecionar estes tipos de mensagem de e-mail. Caso contrário, se não tiver a certeza de que a aplicação lógica foi executada corretamente, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

Parabéns! Criou e executou uma aplicação lógica que automatiza tarefas em diferentes serviços do Azure e que chama um código personalizado.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar deste exemplo, elimine o grupo de recursos que contém a aplicação lógica e os recursos relacionados.

1. Na caixa de pesquisa Azure de nível superior, insira `resources groups` e selecione **grupos de Recursos**.

   ![Localizar e selecionar "Grupos de recursos"](./media/tutorial-process-email-attachments-workflow/find-azure-resource-groups.png)

1. Na lista **de grupos de recursos,** selecione o grupo de recursos para este tutorial. 

   ![Encontre o grupo de recursos para tutorial](./media/tutorial-process-email-attachments-workflow/find-select-tutorial-resource-group.png)

1. No **painel de visão** geral, selecione **Eliminar o grupo de recursos**.

   ![Eliminar grupo de recursos da aplicação lógica](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. Quando o painel de confirmação aparecer, insira o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma aplicação lógica que processa e armazena anexos de e-mail através da integração de serviços do Azure, como o Armazenamento do Azure e as Funções do Azure. Agora, saiba mais sobre outros conectores que pode utilizar para criar aplicações lógicas.

> [!div class="nextstepaction"]
> [Saiba mais sobre os conectores do Logic Apps](../connectors/apis-list.md)