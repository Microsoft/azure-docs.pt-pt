---
title: Migrar para o recurso Azure para a autoria 2
titleSuffix: Azure Cognitive Services
description: Migrar para uma chave de recursos de autoria Azure 2.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/17/2020
ms.author: diberry
ms.openlocfilehash: 5f0778436db7bd8c3a09e3ba346d8a9a6c4af454
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84983717"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrar para uma chave de autoria de recursos Azure

A autenticação de autoria de Compreensão linguística (LUIS) passou de uma conta de e-mail para um recurso Azure. Embora não seja necessário atualmente, a mudança para um recurso Azure será aplicada no futuro.


## <a name="what-is-migration"></a>O que é migração?

Migração é o processo de alteração da autenticação de autoria de uma conta de e-mail para um recurso Azure. A sua conta estará ligada a uma subscrição do Azure e a um recurso de autoria do Azure após a sua migração. **Todos os utilizadores de LUIS (proprietários ou colaboradores) acabarão por precisar de migrar.** A migração tem de ser feita a partir do portal LUIS. Se criar as teclas de autoria, como com o CLI LUIS, ainda terá de concluir o processo de migração no portal LUIS. Ainda pode ter coautores nas suas aplicações após a migração, mas estas serão adicionadas ao nível de recursos Azure em vez do nível de aplicação.

> [!Note]
> Antes da migração, os coautores são conhecidos como _colaboradores_ ao nível da aplicação LUIS. Após a migração, o papel Azure do _contribuinte_ é usado para a mesma funcionalidade, mas ao nível dos recursos Azure.

## <a name="note-before-you-migrate"></a>Nota antes de migrar

* A migração é um processo **unidirecciona.** Não pode voltar depois de migrar.
* As aplicações **migrarão automaticamente** consigo se for o **proprietário** da aplicação.
* O proprietário não pode escolher um subconjunto de apps para migrar e o processo não é reversível.
* As aplicações **desaparecerão dos lados do Colaborador** após a migração do **proprietário.**
* Os proprietários são solicitados a enviar e-mails aos Colaboradores para informá-los da migração.
* As candidaturas **não migrarão** consigo se for **colaborador** na aplicação.
* Não há como um proprietário saber que os seus colaboradores migraram.
* **A migração não** recolhe automaticamente colaboradores e move-os ou adiciona-os ao recurso de autoria Azure. O dono da aplicação é quem precisa de completar este passo após a migração. Este passo requer [permissões ao recurso de autoria Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Depois de atribuídos ao recurso Azure, **os colaboradores terão de migrar para aceder às aplicações.** Caso contrário, não terão acesso aos autores das aplicações.
* Um utilizador migrado não pode ser adicionado como colaborador da aplicação.
* Se **possuir chaves de previsão que são atribuídas a aplicações pertencentes a outro utilizador,** isso bloqueará a **migração** tanto para o proprietário como para os colaboradores. Veja abaixo as recomendações.

> [!Note]
> Se precisar de criar um recurso de tempo de previsão, existe [um processo separado](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) para criá-lo.

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

* Tem de estar associado a uma Subscrição Azure válida. Peça ao seu administrador inquilino para adicioná-lo na subscrição ou pode inscrever-se para um gratuito [aqui.](https://azure.microsoft.com/free/)
* É necessário criar um recurso de autoria LUIS Azure a partir do portal LUIS ou do portal Azure. A criação de um recurso de autoria a partir do portal LUIS faz parte do fluxo migratório que é discutido na secção seguinte.
* Se for **colaborador em aplicações,** as aplicações não migrarão automaticamente. **Recomenda-se que apoie estes pedidos** exportando-os ou utilizando a [API de exportação.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) Pode importar a app de volta para o LUIS após a migração. O processo de importação cria uma nova app com um novo ID de aplicação, para o qual você é o proprietário.
* Se for **o proprietário da aplicação,** não necessitará de exportar as suas apps, uma vez que irão migrar automaticamente. **Recomenda-se guardar a lista de colaboradores de cada aplicação.** Um modelo de e-mail que tem esta lista é fornecido opcionalmente como parte do processo de migração.


|Portal|Objetivo|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Criar recursos de previsão e autoria.<br>* Atribuir contribuintes em recursos.|
|[LUIS](https://www.luis.ai)|* Migrar para novos recursos de autoria.<br>* Crie novos recursos de autoria no fluxo migratório.<br>* Atribuir ou atribuir recursos de previsão e autoria a apps da página de **recursos Manage -> Azure.** <br>* Mover as aplicações de um recurso de autoria para outro.  |

> [!Note]
> **A autoria da sua aplicação LUIS é gratuita,** indicada pelo `F0` tier. Saiba [mais sobre os níveis de preços.](luis-limits.md#key-limits)


## <a name="migration-steps"></a>Passos de migração

1. No portal LUIS em que está a trabalhar, pode iniciar o processo de migração a partir do ícone **Azure** na barra de ferramentas superior.

   > [!div class="mx-imgBorder"]
   > ![Ícone de migração](./media/migrate-authoring-key/migration-button.png)

2. A janela pop-up de migração permite-lhe continuar a migração ou migrar mais tarde. **Selecione Migrar agora**.

   > [!div class="mx-imgBorder"]
   > ![Primeira janela pop-up no processo de migração, selecione Migrar agora.](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Opcionalmente, se alguma das suas aplicações tiver colaboradores, é solicitado que **envie um e-mail** informando-os sobre a migração. Este é um passo opcional.

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

4. Opcionalmente, se for colaborador em qualquer aplicação, é solicitado que **exporte uma cópia das aplicações** selecionando esta opção durante o fluxo de migração. Assim que selecionar a opção, encontrará a página abaixo onde clica no botão de descarregamento à esquerda para exportar as aplicações que deseja. Pode importar estas aplicações de volta depois de migrar, uma vez que não serão migradas automaticamente consigo. Este é um passo opcional.

   > [!div class="mx-imgBorder"]
   > ![Ad pronta para exportar o seu pedido.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Pode optar por criar um novo recurso de autoria LUIS ou migrar para um recurso de autoria existente se já tiver criado um a partir de Azure. Escolha a opção que deseja selecionando o botão adequado a partir de baixo.

   > [!div class="mx-imgBorder"]
   > ![Criar recurso de autoria](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Criar novo recurso de autoria da LUIS para migrar

Se pretender criar um novo recurso de autoria, selecione **criar um novo recurso de Autoria** e fornecer as seguintes informações na janela seguinte.

> [!div class="mx-imgBorder"]
> ![Criar recurso de autoria](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Nome do recurso** - um nome personalizado que você escolhe, usado como parte do URL para as suas consultas de autoria e previsão de ponto final.
* **Nome da assinatura** - a subscrição que será associada ao recurso. Se tiver mais do que uma subscrição que pertença ao seu inquilino, selecione a que deseja da lista de espera.
* **Grupo de recursos** - um nome de grupo de recursos personalizado que você escolhe da lista de drop down. Os grupos de recursos permitem-lhe agrupar recursos Azure para acesso e gestão.
* **Inquilino** - o inquilino a que a sua assinatura Azure está associada. Isto é definido por defeito para o inquilino que você está escolhendo atualmente. Pode trocar de inquilino selecionando o avatar mais à direita que contém as suas iniciais.

Depois de introduzir as informações acima, selecione **'Fazer' ( 'Fazer' (' 'Fazer')**

Note que pode ter 10 recursos de autoria gratuita por região, por subscrição. Se a sua subscrição tiver mais de 10 recursos de autoria na mesma região, não poderá criar um novo.

* Quando o recurso de autoria é criado, a mensagem de sucesso é mostrada. Selecione **Perto** para fechar a janela pop-up.

  > [!div class="mx-imgBorder"]
  > ![O seu recurso de autoria foi criado com sucesso.](./media/migrate-authoring-key/migration-success-2.png)

### <a name="use-existing-authoring-resource-to-migrate"></a>Utilize o recurso de autoria existente para migrar

Se a sua subscrição já estiver associada a um recurso luis autoria Azure ou se criou a partir do portal Azure e pretende migrar para ele em vez de criar um novo recurso, selecione **Use Existing Resourceing resource** e forneça as seguintes informações na janela seguinte.

> [!div class="mx-imgBorder"]
>![Criar recurso de autoria](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Inquilino** - o inquilino a que a sua assinatura Azure está associada. Isto é definido por defeito para o inquilino que você está escolhendo atualmente.
* **Nome da assinatura** - a subscrição que será associada ao recurso. Se tiver mais do que uma subscrição que pertença ao seu inquilino, selecione a que deseja da lista de entrega.
* **Nome do recurso** - Selecione o recurso de autoria para o quais pretende migrar.

> [!Note]
> Se não conseguir ver o seu recurso de autoria na lista de dropdown, certifique-se de que o criou na **localização adequada,** de acordo com o portal LUIS em que está inscrito. Certifique-se também de que o que criou é, de facto, um **recurso de autoria** e não um **recurso de previsão.**


* Valide o nome do seu recurso de autoria e clique no botão **Migrar agora.**

 > [!div class="mx-imgBorder"]
 > ![Criar recurso de autoria](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

* A mensagem de sucesso é mostrada. Selecione **Perto** para fechar a janela pop-up.

 > [!div class="mx-imgBorder"]
 > ![O seu recurso de autoria foi criado com sucesso.](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Usando aplicativos após migração

* Após o processo de migração, todas as suas aplicações LUIS de que é proprietário, serão agora atribuídas a um único recurso de autoria LUIS.
* A lista **de aplicações My** mostra as aplicações migradas para o novo recurso de autoria.
* Antes de aceder às suas apps, selecione o recurso de subscrição e autoria DE LUIS para ver as aplicações que pode autorar.

 > [!div class="mx-imgBorder"]
 > ![Selecione o recurso de subscrição e autoria DE LUIS para ver as aplicações que pode autor.](./media/create-app-in-portal-select-subscription-luis-resource.png)

* Não precisa de saber a chave do recurso de autoria para continuar a editar as suas apps no portal LUIS.
* Se pretende editar as suas aplicações programáticamente, necessitará dos valores-chave de autoria. Estes valores são apresentados na página **de recursos Do Azure gestão->** no portal LUIS e estão também disponíveis no portal Azure na página **Chaves** do recurso. Também pode criar mais recursos de autoria e atribuí-los a partir da mesma página.

 > [!div class="mx-imgBorder"]
 > ![Gerir recurso de autoria.](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="add-contributors-to-authoring-resources"></a>Adicionar contribuintes à autoria de recursos

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Saiba [como adicionar colaboradores](luis-how-to-collaborate.md) no seu recurso de autoria. Os contribuintes terão acesso a todas as aplicações ao abrigo desse recurso.

Pode adicionar colaboradores ao recurso de autoria a partir do _portal Azure_, na página **Access Control (IAM)** para esse recurso. Para mais informações, consulte [o acesso ao contribuinte.](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)

> [!Note]
> Se o proprietário da app LUIS migrar e adicionar o colaborador como colaborador no recurso Azure, o colaborador ainda não terá acesso à app a menos que também migrar.

## <a name="luis-portal-migration-reminders"></a>Lembretes de migração do portal LUIS

O [portal LUIS](https://www.luis.ai) prevê o processo de migração.

Ser-lhe-á pedido que migrar se:
* Tem aplicações no sistema de autenticação de email para autoria.
* E tu és o dono da aplicação.

Semanalmente, é solicitado que emigue as suas apps. Pode cancelar esta janela sem migrar. Se quiser migrar antes do próximo período programado, pode iniciar o processo de migração a partir do ícone **Azure** na barra de ferramentas superior do portal LUIS.

Pode atrasar o processo de migração cancelando-o pela janela. É-lhe pedido periodicamente que migrar até migrar ou o prazo de migração ser ultrapassado. Pode iniciar o processo de migração a partir do ícone de bloqueio da barra de navegação superior.

## <a name="prediction-resources-blocking-migration"></a>Recursos de previsão que bloqueiam a migração
A sua migração tem um impacto negativo no tempo de execução das aplicações. Ao migrar, quaisquer colaboradores são removidos das suas apps e é removido como colaborador de outras apps. Este processo significa que as chaves que um Colaborador atribui também são removidas, o que pode quebrar a sua aplicação se estiver em produção. Esta é a razão pela qual bloqueamos a migração até remover colaboradores ou chaves que lhes sejam atribuídas manualmente.

### <a name="when-does-prediction-resources-block-migration"></a>Quando é que os recursos de previsão bloqueiam a migração?
* A migração fica bloqueada se tiver atribuído recursos de previsão/tempo de execução em apps que não possui.
* A migração fica bloqueada se tiver outros utilizadores a atribuir recursos de previsão/tempo de execução às aplicações que possui.

### <a name="recommended-steps-to-do-if-you-are-the-owner-of-the-app"></a>Passos recomendados para fazer se você é o proprietário da app
Se é proprietário de algumas aplicações e tem colaboradores atribuídos chave de previsão/tempo de execução a estas aplicações, é mostrado um erro quando migra que lista os IDs de aplicação que têm chaves de previsão que lhes são atribuídas por outros utilizadores.

Recomenda-se:
* Notifique os colaboradores sobre a migração.
* Remova todos os colaboradores das aplicações apresentadas no erro.
* Submeta-se ao processo de migração que deverá ter sucesso se remover manualmente os colaboradores.
* Atribua colaboradores como contribuintes para o seu novo recurso de autoria.
* Os colaboradores devem migrar e reatribuir os recursos de previsão de volta às aplicações.
Note que isto irá causar uma rutura na aplicação temporariamente até que os recursos de previsão sejam re-atribuídos.

Outra solução aqui é, antes da migração do proprietário, os colaboradores podem adicionar os proprietários de aplicações como contribuintes nas suas subscrições Azure do portal Azure. Isto permitirá ao proprietário o acesso ao recurso de previsão de tempo de execução. Se o proprietário migrar usando a nova subscrição a que foram adicionadas (que será encontrada sob um novo inquilino), isso não só irá desbloquear o processo de migração tanto para colaboradores como para o proprietário de apps, como permitirá uma migração suave de apps com a chave de previsão ainda atribuída a eles não quebrar as apps.


### <a name="recommended-steps-to-do-if-you-are-a-collaborator-on-an-app"></a>Passos recomendados para fazer se você é um colaborador em uma aplicação
Se estiver a colaborar em aplicações e tiver atribuído a chave de previsão/tempo de execução a estas aplicações, é mostrado um erro quando migra que lista os IDs da aplicação e os caminhos-chave que estão a bloquear a migração.

Recomenda-se:
* Pedidos de exportação como apoio. Isto é dado como um passo opcional no processo de migração.
* Desatribuir os recursos de previsão da página de **recursos do Manage-> Azure.**
* Submeter-se ao processo de migração.
* Importar pedidos de volta após a migração.
* Reatribuir chaves de previsão para as suas aplicações Gerir -> página **de recursos Azure.**

> [!Note]
> Quando importa de volta as suas aplicações depois de migrar, elas terão iDs de aplicações diferentes e serão diferentes das que estão a ser atingidas na produção. Será agora o proprietário destas aplicações.

## <a name="troubleshooting-migration-process"></a>Processo de migração de resolução de problemas

Quando tenta migrar, mas não encontra a subscrição do Azure na lista de abandonos:
* Certifique-se de que tem uma subscrição válida da Azure que está autorizada a criar recursos de Serviços Cognitivos. Vá ao [portal Azure](https://ms.portal.azure.com) e verifique o estado da subscrição. Se não tiver um, [crie um teste gratuito.](https://azure.microsoft.com/free/)
* Certifique-se de que está no inquilino adequado associado à sua subscrição válida. Pode trocar os inquilinos do avatar mais à esquerda na barra de ferramentas abaixo: ![ Troque os inquilinos.](./media/migrate-authoring-key/switch-user-tenant-2.png)

 Se já tiver um recurso de autoria existente, mas não o encontrar ao selecionar a opção "Use o recurso de autoria existente":
* O seu recurso foi provavelmente criado num local diferente do portal em que tem acesso. Por favor, verifique [as Regiões de Autoria DO LUIS e os portais](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)
* Criar um novo recurso a partir do portal LUIS

Se selecionar a opção "Criar novo recurso de autoria" e a migração falhar com a mensagem de erro "Falha na recuperação das informações do Azure do utilizador, volte a tentar mais tarde"
* A sua subscrição pode ter 10 ou mais recursos de autoria por região por subscrição. Se for esse o caso, não poderá criar um novo recurso de autoria.
* Migrar selecionando a opção "Use o recurso de autoria existente" e selecione um dos recursos existentes que tem sob a sua subscrição.

Se vir o erro abaixo, verifique os passos [recomendados para fazer se for o proprietário da secção de aplicações] ![ A migração falha para os proprietários](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Se vir o erro abaixo, verifique os passos [recomendados para fazer se for colaborador numa secção de aplicações] ![ A migração falha para os colaboradores](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Passos seguintes

* Rever [conceitos](luis-concept-keys.md) sobre a autoria e as chaves de tempo de execução
* [Reveja como atribuir chaves](luis-how-to-azure-subscription.md) e adicionar [contribuintes](luis-how-to-collaborate.md)
