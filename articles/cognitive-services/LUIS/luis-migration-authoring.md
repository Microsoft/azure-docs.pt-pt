---
title: Migrar para uma chave de autoria de recursos Azure
titleSuffix: Azure Cognitive Services
description: Este artigo descreve como migrar a autenticação linguística (LUIS) autoria de uma conta de e-mail para um recurso Azure.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18, contperf-fy21q2
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 3ff48ff5a3f46d8ec0fbf81b4cd20d20c217344b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98787642"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrar para uma chave de autoria de recursos Azure

> [!IMPORTANT]
>  A partir de 3 de dezembro, os utilizadores luis existentes devem concluir o processo de migração para continuar a autorizar aplicações LUIS.

A autenticação de autoria de Compreensão linguística (LUIS) mudou de uma conta de e-mail para um recurso Azure. Use este artigo para aprender a migrar a sua conta, se ainda não emigrou.  


## <a name="what-is-migration"></a>O que é migração?

Migração é o processo de alteração da autenticação de autoria de uma conta de e-mail para um recurso Azure. A sua conta estará ligada a uma subscrição do Azure e a um recurso de autoria do Azure após a sua migração.

A migração tem de ser feita a partir do [portal LUIS.](https://www.luis.ai) Se criar as teclas de autoria utilizando o CLI LUIS, por exemplo, terá de concluir o processo de migração no portal LUIS. Ainda pode ter coautores nas suas aplicações após a migração, mas estas serão adicionadas ao nível de recursos Azure em vez do nível de aplicação. Migrar a sua conta não pode ser revertido.

> [!Note]
> * Se precisar de criar um recurso de tempo de previsão, há [um processo separado](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) para criá-lo.
> * Consulte a secção [de notas de migração](#migration-notes) abaixo para obter informações sobre como as suas aplicações e colaboradores serão afetados. 
> * A autoria da sua aplicação LUIS é gratuita, conforme indicado no nível F0. Saiba [mais sobre os níveis de preços.](luis-limits.md#key-limits)

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

* Uma subscrição válida da Azure. Peça ao seu administrador inquilino para adicioná-lo na subscrição, ou [inscreva-se para um gratuito](https://azure.microsoft.com/free/cognitive-services).
* Um recurso de autoria LUIS Azure do portal LUIS ou do [portal Azure.](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) 
    * A criação de um recurso de autoria a partir do portal LUIS faz parte do processo de migração descrito na secção seguinte.
* Se for colaborador em aplicações, as aplicações não migrarão automaticamente. Será solicitado a exportar estas aplicações enquanto atravessa o fluxo migratório. Também pode utilizar a [API de exportação.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) Pode importar a app de volta para o LUIS após a migração. O processo de importação cria uma nova app com um novo ID de aplicação, para o qual você é o proprietário.        
* Se for o proprietário da aplicação, não precisará exportar as suas apps porque migrarão automaticamente. É fornecido um modelo de e-mail com uma lista de todos os colaboradores para cada aplicação, para que possam ser notificados do processo de migração.

## <a name="migration-steps"></a>Passos de migração

1. Ao entrar no [portal LUIS,](https://www.luis.ai)abre-se uma janela de migração do Azure com os passos para a migração. Se o descartar, não poderá prosseguir com a autoria das suas aplicações LUIS, e a única ação exibida será continuar com a migração.

    > [!div class="mx-imgBorder"]
    > ![Introdução da Janela de Migração](./media/migrate-authoring-key/notify-azure-migration.png)

2. Se tiver colaboradores em alguma das suas aplicações, verá uma lista de nomes de aplicações por si, juntamente com a região de autoria e e-mails colaboradores em cada aplicação. Recomendamos o envio de um e-mail aos seus colaboradores notificando-os sobre a migração clicando no botão de símbolo **de envio** à esquerda do nome da aplicação.
Aparecerá `*` um símbolo ao lado do nome da aplicação se um colaborador tiver um recurso de previsão atribuído à sua aplicação. Após a migração, estas aplicações ainda terão estes recursos de previsão atribuídos a eles, mesmo que os colaboradores não tenham acesso às suas aplicações. No entanto, esta atribuição será quebrada se o proprietário do recurso de previsão [regenerar as chaves](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) do portal Azure.  

   > [!div class="mx-imgBorder"]
   > ![Notificar colaboradores](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   Para cada colaborador e app, a aplicação de e-mail padrão abre com um e-mail levemente formatado. Pode editar o e-mail antes de enviá-lo. O modelo de e-mail inclui o ID de aplicação exato e o nome da aplicação.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > Depois de migrar a sua conta para o Azure, as suas aplicações deixarão de estar disponíveis para colaboradores.

3. Se você é um colaborador em qualquer aplicação, uma lista de nomes de aplicações compartilhados com você é mostrado juntamente com a região de autoria e e-mails do proprietário em cada aplicação. Recomenda-se exportar uma cópia das aplicações clicando no botão de exportação à esquerda do nome da aplicação. Pode importar estas aplicações de volta depois de migrar, porque não serão migradas automaticamente consigo.
Aparecerá `*` um símbolo ao lado do nome da aplicação se tiver um recurso de previsão atribuído a uma aplicação. Após a migração, o seu recurso de previsão ainda será atribuído a estas aplicações, mesmo que já não tenha acesso a estas aplicações. Se quiser quebrar a atribuição entre o seu recurso de previsão e a aplicação, terá de ir ao portal Azure e [regenerar as teclas](./luis-how-to-azure-subscription.md#regenerate-an-azure-key).

   > [!div class="mx-imgBorder"]
   > ![Exporte as suas aplicações.](./media/migrate-authoring-key/migration-export-apps.png)


4. Na janela para as regiões migratórias, ser-lhe-á pedido que emigure as suas aplicações para um recurso Azure na mesma região em que foram da autoria. A LUIS tem três regiões e portais de [autoria.](./luis-reference-regions.md#luis-authoring-regions) A janela mostrará as regiões onde as suas aplicações foram da autoria. As regiões de migração exibidas podem ser diferentes dependendo do portal regional que usa, e aplicações que você escreveu. 

   > [!div class="mx-imgBorder"]
   > ![Migração multi-região.](./media/migrate-authoring-key/migration-regional-flow.png)

5. Para cada região, opte por criar um novo recurso de autoria LUIS, ou migrar para um existente utilizando os botões.

   > [!div class="mx-imgBorder"]
   > ![escolher criar ou existir recurso de autoria](./media/migrate-authoring-key/migration-multiregional-resource.png)

   Forneça as seguintes informações:

   * **Nome do Inquilino**: O inquilino a que a sua assinatura Azure está associada. Por defeito, isto é definido para o inquilino que você está usando atualmente. Pode trocar os inquilinos fechando esta janela e selecionando o avatar no canto superior direito do ecrã, contendo as suas iniciais. Clique em **Migrar para Azure** para reabrir a janela.
   * **Nome de assinatura Azure**: A subscrição que será associada ao recurso. Se tiver mais do que uma subscrição que pertença ao seu inquilino, selecione a que deseja da lista de espera.
   * **Nome de recurso de autoria**: nome personalizado que você escolhe. É usado como parte da URL para as suas consultas de autoria e previsão. Se estiver a criar um novo recurso de autoria, note que o nome do recurso só pode incluir caracteres alfanuméricos, `-` e não pode começar ou terminar com `-` . Se outros símbolos estiverem incluídos no nome, a criação de recursos e a migração falharão.
   * **Nome do grupo de recursos Azure**: Nome de grupo de recursos personalizados que escolhe da lista de drop-down. Os grupos de recursos permitem-lhe agrupar recursos Azure para acesso e gestão. Se atualmente não tiver um grupo de recursos na sua subscrição, não poderá criar um no portal LUIS. Vá ao [portal Azure](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) para criar um e depois vá ao LUIS para continuar o processo de inscrição.

6. Depois de migrar com sucesso em todas as regiões, clique em acabamento. Terá agora acesso às suas aplicações. Pode continuar a autorizar e manter todas as suas aplicações em todas as regiões dentro do portal.

## <a name="migration-notes"></a>Notas de migração

* Antes da migração, os coautores são conhecidos como _colaboradores_ a nível da aplicação LUIS. Após a migração, o papel Azure do _contribuinte_ é usado para a mesma funcionalidade ao nível dos recursos Azure.
* Se já se inscreveu em mais de um [portal regional LUIS,](./luis-reference-regions.md#luis-authoring-regions)será solicitado que migrar em várias regiões de uma só vez.
* As aplicações migrarão automaticamente consigo se for o proprietário da aplicação. As candidaturas não migrarão consigo se for colaborador na aplicação. No entanto, os colaboradores serão solicitados a exportar as aplicações de que necessitam.
* Os proprietários de aplicações não podem escolher um subconjunto de apps para migrar e não há forma de um proprietário saber se os colaboradores migraram.
* A migração não move automaticamente ou adiciona colaboradores ao recurso de autoria Azure. O dono da aplicação é quem precisa de completar este passo após a migração. Este passo requer [permissões ao recurso de autoria Azure](./luis-how-to-collaborate.md).
* Após a atribuição dos contribuintes ao recurso Azure, terão de migrar antes de poderem aceder às aplicações. Caso contrário, não terão acesso ao autor das aplicações.


## <a name="using-apps-after-migration"></a>Usando aplicativos após migração

Após o processo de migração, todas as suas aplicações LUIS para as quais você é o proprietário serão agora atribuídas a um único recurso de autoria LUIS.
A lista **My Apps** mostra as aplicações migradas para o novo recurso de autoria. Antes de aceder às suas apps, **selecione Escolha um recurso de autoria diferente** para selecionar o recurso de subscrição e autoria para visualizar as aplicações que podem ser da autoria.

> [!div class="mx-imgBorder"]
> ![selecionar recurso de subscrição e autoria](./media/migrate-authoring-key/select-sub-and-resource.png)


Se pretende editar as suas aplicações programáticamente, precisará dos valores-chave de autoria. Estes valores são apresentados clicando em **Gerir** a parte superior do ecrã no portal LUIS e, em seguida, selecionar **Recursos Azure**. Também estão disponíveis no portal Azure na página **Chave e pontos finais** do recurso. Também pode criar mais recursos de autoria e atribuí-los a partir da mesma página.

## <a name="adding-contributors-to-authoring-resources"></a>Adicionar contribuintes à autoria de recursos

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Saiba [como adicionar colaboradores](luis-how-to-collaborate.md) no seu recurso de autoria. Os contribuintes terão acesso a todas as aplicações ao abrigo desse recurso.

Pode adicionar colaboradores ao recurso de autoria do portal Azure, na página **Access Control (IAM)** para esse recurso. Para obter mais informações, consulte [Adicionar colaboradores à sua aplicação.](luis-how-to-collaborate.md)

> [!Note]
> Se o proprietário da app LUIS migrar e adicionar o colaborador como colaborador no recurso Azure, o colaborador ainda não terá acesso à app a menos que também migrar.

## <a name="troubleshooting-the-migration-process"></a>Resolução de problemas do processo de migração

Se não conseguir encontrar a sua subscrição Azure na lista de drop-down:
* Certifique-se de que tem uma subscrição válida do Azure que está autorizada a criar recursos de Serviços Cognitivos. Vá ao [portal Azure](https://ms.portal.azure.com) e verifique o estado da subscrição. Se não tiver uma, [crie uma conta Azure gratuita.](https://azure.microsoft.com/free/cognitive-services/)
* Certifique-se de que está no inquilino adequado associado à sua subscrição válida. Pode trocar os inquilinos selecionando o avatar no canto superior direito do ecrã, contendo as suas iniciais.

  > [!div class="mx-imgBorder"]
  > ![Página para mudar diretórios](./media/migrate-authoring-key/switch-directories.png)

Se tiver um recurso de autoria existente, mas não o encontrar quando selecionar a opção Utilizar o **recurso de autoria existente:**
* O seu recurso foi provavelmente criado numa região diferente daquela em que está a tentar migrar.
* Criar um novo recurso a partir do portal LUIS.

Se selecionar a opção **Criar Novo Recurso de Autoria** e a migração falhar com a mensagem de erro "Falha na recuperação das informações Azure do utilizador, volte a tentar mais tarde":
* A sua subscrição pode ter 10 ou mais recursos de autoria por região, por subscrição. Se for esse o caso, não poderá criar um novo recurso de autoria.
* Migrar selecionando a opção **Utilização do Recurso de Autoria Existente** e selecionando um dos recursos existentes sob a sua subscrição.

## <a name="create-new-support-request"></a>Criar novo pedido de apoio

Se tiver algum problema com a migração que não seja abordado na secção de resolução de problemas, por favor [crie um tópico de apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e forneça as informações abaixo com os seguintes campos:

   * **Tipo de Emissão**: Técnico
   * **Subscrição**: Escolha uma subscrição da lista de abandono
   * **Serviço**: Pesquisar e selecionar "Serviços Cognitivos"
   * **Recurso**: Escolha um recurso LUIS se houver um existente. Caso contrário, selecione a pergunta geral.

## <a name="next-steps"></a>Passos seguintes

* Rever [conceitos sobre a autoria e as chaves de tempo de execução](luis-how-to-azure-subscription.md)
* Reveja como [atribuir chaves](luis-how-to-azure-subscription.md) e [adicionar contribuintes](luis-how-to-collaborate.md)