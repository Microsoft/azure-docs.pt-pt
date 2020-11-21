---
title: Migrar para uma chave de autoria de recursos Azure
titleSuffix: Azure Cognitive Services
description: Este artigo descreve como migrar a autenticação linguística (LUIS) autoria de uma conta de e-mail para um recurso Azure.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 455c7d66748740ae6e2cc11c6a44bbf30c1cbced
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018842"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrar para uma chave de autoria de recursos Azure

A autenticação de autoria de Compreensão linguística (LUIS) passou de uma conta de e-mail para um recurso Azure. Embora não seja atualmente necessário, a mudança para um recurso Azure será aplicada no futuro.


## <a name="what-is-migration"></a>O que é migração?

Migração é o processo de alteração da autenticação de autoria de uma conta de e-mail para um recurso Azure. A sua conta estará ligada a uma subscrição do Azure e a um recurso de autoria do Azure após a sua migração. *Todos os utilizadores de LUIS (proprietários ou colaboradores) acabarão por precisar de migrar.*

A migração tem de ser feita a partir do portal LUIS. Se criar as teclas de autoria utilizando o CLI LUIS, por exemplo, terá de concluir o processo de migração no portal LUIS. Ainda pode ter coautores nas suas aplicações após a migração, mas estas serão adicionadas ao nível de recursos Azure em vez do nível de aplicação.

> [!Note]
> Antes da migração, os coautores são conhecidos como _colaboradores_ a nível da aplicação LUIS. Após a migração, o papel Azure do _contribuinte_ é usado para a mesma funcionalidade ao nível dos recursos Azure.

## <a name="note-before-you-migrate"></a>Nota antes de migrar

* Deve migrar a sua experiência de autor até **2 de novembro de 2020.** 
* A migração é um processo unidirecciona. Não pode voltar depois de migrar.
* As aplicações migrarão automaticamente consigo se for o proprietário da aplicação.
* O proprietário não pode escolher um subconjunto de apps para migrar, e o processo não é reversível.
* As aplicações desaparecerão do lado do colaborador após a migração do proprietário.
* Os proprietários são solicitados a enviar e-mails aos colaboradores para informá-los da migração.
* As candidaturas não migrarão consigo se for colaborador na aplicação.
* Não há como um proprietário saber que os colaboradores migraram.
* A migração não recolhe automaticamente colaboradores e move-os ou adiciona-os ao recurso de autoria Azure. O dono da aplicação é quem precisa de completar este passo após a migração. Este passo requer [permissões ao recurso de autoria Azure](./luis-how-to-collaborate.md).
* Depois de os colaboradores serem atribuídos ao recurso Azure, precisam de migrar para aceder às aplicações. Caso contrário, não terão acesso aos autores das aplicações.
* Um utilizador migrado não pode ser adicionado como colaborador da aplicação.
* Se possuir chaves de previsão que são atribuídas a aplicações pertencentes a outro utilizador, isso bloqueará a migração tanto para o proprietário como para os colaboradores. Veja as recomendações mais tarde neste artigo.

> [!Note]
> Se precisar de criar um recurso de tempo de previsão, há [um processo separado](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) para criá-lo.

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

* Tem de estar associado a uma subscrição válida do Azure. Peça ao seu administrador inquilino para adicioná-lo na subscrição, ou [inscreva-se para um gratuito](https://azure.microsoft.com/free/cognitive-services).
* É necessário criar um recurso de autoria LUIS Azure a partir do portal LUIS ou do portal Azure. A criação de um recurso de autoria a partir do portal LUIS faz parte do fluxo migratório que é discutido na secção seguinte.
* Se for colaborador em aplicações, as aplicações não migrarão automaticamente. Recomendamos que apoie estas aplicações exportando-as ou utilizando a [API de exportação.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) Pode importar a app de volta para o LUIS após a migração. O processo de importação cria uma nova app com um novo ID de aplicação, para o qual você é o proprietário.
* Se for o proprietário da aplicação, não precisará exportar as suas apps porque migrarão automaticamente. Recomendamos que guarde a lista de colaboradores de cada aplicação. Um modelo de e-mail que tem esta lista é fornecido opcionalmente como parte do processo de migração.


|Portal|Objetivo|
|--|--|
|[Azure](https://azure.microsoft.com/free/cognitive-services)| Criar recursos de previsão e autoria.<br> Atribuir contribuintes em recursos.|
|[LUIS](https://www.luis.ai)| Migrar para novos recursos de autoria.<br> Criar novos recursos de autoria no fluxo migratório.<br> Atribuir ou atribuir recursos de previsão e autoria de recursos para apps a partir da página **de** recursos Manage  >  **Azure.** <br> Mover aplicações de um recurso de autoria para outro.  |

> [!Note]
> A autoria da sua aplicação LUIS é gratuita, conforme indicado no nível F0. Saiba [mais sobre os níveis de preços.](luis-limits.md#key-limits)


## <a name="migration-steps"></a>Passos de migração

1. No portal LUIS em que está a trabalhar, pode iniciar o processo de migração a partir do ícone **Azure** na barra de ferramentas superior.

   > [!div class="mx-imgBorder"]
   > ![Ícone de migração](./media/migrate-authoring-key/migration-button.png)

2. A janela pop-up de migração permite-lhe continuar a migração ou migrar mais tarde. **Selecione Migrar agora**.

   > [!div class="mx-imgBorder"]
   > ![Primeira janela pop-up no processo de migração, onde seleciona Migrar agora](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Se alguma das suas aplicações tiver colaboradores, é-lhe pedido que envie um e-mail que os informe sobre a migração. Este passo é opcional.

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

4. Se for colaborador em qualquer aplicação, é solicitado a exportar uma cópia das aplicações selecionando esta opção durante o fluxo de migração. Este passo é opcional.

   Se selecionar a opção, aparece a seguinte página. Selecione os botões de descarregamento à esquerda para exportar as aplicações que deseja. Pode importar estas aplicações de volta depois de migrar, porque não serão migradas automaticamente consigo.

   > [!div class="mx-imgBorder"]
   > ![Ad leve a exportar as suas aplicações.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Pode optar por criar um novo recurso de autoria LUIS ou migrar para um recurso de autoria existente se já tiver criado um a partir de Azure. Escolha a opção que deseja selecionando um dos seguintes botões.

   > [!div class="mx-imgBorder"]
   > ![Botões para criar um novo recurso de autoria e usar um recurso de autoria existente](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Criar novo recurso de autoria da LUIS para migrar

Se pretender criar um novo recurso de autoria, selecione **Criar Novo Recurso de Autoria** e forneça as seguintes informações na janela seguinte. Em seguida, selecione **Done** (Concluído).

> [!div class="mx-imgBorder"]
> ![Janela para criar um recurso de autoria](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Nome do Inquilino**: O inquilino a que a sua assinatura Azure está associada. Isto é definido por defeito para o inquilino que você está usando atualmente. Pode trocar de inquilino selecionando o avatar mais à direita, que contém as suas iniciais.
* **Nome do recurso**: nome personalizado que escolhe. É usado como parte da URL para as suas consultas de autoria e previsão.
* **Nome da assinatura**: A subscrição que será associada ao recurso. Se tiver mais do que uma subscrição que pertença ao seu inquilino, selecione a que deseja da lista de espera.
* **Nome do grupo de recursos Azure**: Nome de grupo de recursos personalizados que escolhe da lista de drop-down. Os grupos de recursos permitem-lhe agrupar recursos Azure para acesso e gestão.

Note que pode ter 10 recursos de autoria gratuita por região, por subscrição. Se a sua subscrição tiver mais de 10 recursos de autoria na mesma região, não poderá criar um novo.

Quando o recurso de autoria é criado, a mensagem de sucesso é mostrada. Selecione **Perto** para fechar a janela pop-up.

  > [!div class="mx-imgBorder"]
  > ![Mensagem que indica que o seu recurso de autoria foi criado com sucesso](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>Utilize o recurso de autoria existente para migrar

Se a sua subscrição já estiver associada a um recurso luis autoria Azure, ou se criou um recurso a partir do portal Azure e pretende migrar para ele em vez de criar um novo, selecione **Use Existing Authoring Resource**. Forneça as seguintes informações na janela seguinte e, em seguida, selecione **Fazer**.

> [!div class="mx-imgBorder"]
>![Janela para alterar um recurso de autoria existente](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Nome do Inquilino**: O inquilino a que a sua assinatura Azure está associada. Isto é definido por defeito para o inquilino que você está usando atualmente.
* **Nome da assinatura**: A subscrição que será associada ao recurso. Se tiver mais do que uma subscrição que pertença ao seu inquilino, selecione a que deseja da lista de espera.
* **Denominação de recurso**: O recurso de autoria para o que pretende migrar.

> [!Note]
> Se não consegue ver o seu recurso de autoria na lista de drop-down, certifique-se de que o criou na localização adequada de acordo com o portal LUIS onde está inscrito. Certifique-se também de que o que criou é um recurso de autoria e não um recurso de previsão.


Valide o nome do seu recurso de autoria e selecione o botão **Migrar.**

> [!div class="mx-imgBorder"]
> ![Janela para escolher um recurso de autoria, com o botão Migrar agora disponível](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

Uma mensagem de sucesso aparece. Selecione **Perto** para fechar a janela pop-up.

> [!div class="mx-imgBorder"]
> ![Mensagem que diz que o seu recurso de autoria foi migrado com sucesso](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Usando aplicativos após migração

Após o processo de migração, todas as suas aplicações LUIS para as quais você é o proprietário serão agora atribuídas a um único recurso de autoria LUIS.

A lista **My Apps** mostra as aplicações migradas para o novo recurso de autoria. Antes de aceder às suas apps, selecione o recurso de subscrição e autoria de LUIS para ver as aplicações que pode ser autora.

 > [!div class="mx-imgBorder"]
 > ![Caixas para recurso de subscrição e autoria](./media/create-app-in-portal-select-subscription-luis-resource.png)

Não precisa de saber a chave do recurso de autoria para continuar a editar as suas apps no portal LUIS.

Se pretende editar as suas aplicações programáticamente, precisará dos valores-chave de autoria. Estes valores são apresentados na página **Manage**  >  **Azure Resources** no portal LUIS. Também estão disponíveis no portal Azure na página **Keys** do recurso. Também pode criar mais recursos de autoria e atribuí-los a partir da mesma página.

 > [!div class="mx-imgBorder"]
 > ![Página para gerir recursos de autoria](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>Adicionar contribuintes à autoria de recursos

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Saiba [como adicionar colaboradores](luis-how-to-collaborate.md) no seu recurso de autoria. Os contribuintes terão acesso a todas as aplicações ao abrigo desse recurso.

Pode adicionar colaboradores ao recurso de autoria do portal Azure, na página **Access Control (IAM)** para esse recurso. Para obter mais informações, consulte [Adicionar colaboradores à sua aplicação.](luis-how-to-collaborate.md)

> [!Note]
> Se o proprietário da app LUIS migrar e adicionar o colaborador como colaborador no recurso Azure, o colaborador ainda não terá acesso à app a menos que também migrar.

## <a name="luis-portal-migration-reminders"></a>Lembretes de migração do portal LUIS

O [portal LUIS](https://www.luis.ai) prevê o processo de migração.

Pedir-lhe-ão que migrar se ambas as condições forem verdadeiras:
* Tem aplicações no sistema de autenticação de email para autoria.
* És o dono da aplicação.

Semanalmente, é solicitado a migrar as suas aplicações. Pode fechar esta janela sem migrar. Se quiser migrar antes do próximo período programado, pode iniciar o processo de migração a partir do ícone **Azure** na barra de ferramentas superior do portal LUIS.

## <a name="prediction-resources-blocking-migration"></a>Recursos de previsão que bloqueiam a migração
A sua migração afeta negativamente o tempo de execução de qualquer aplicação. Quando migra, qualquer colaborador é removido das suas apps e é removido como colaborador de outras apps. Este processo significa que as chaves que um colaborador atribui também são removidas, o que pode quebrar a sua aplicação se estiver em produção. Esta é a razão pela qual bloqueamos a migração até que remova manualmente colaboradores ou chaves que lhes sejam atribuídas.

A migração é bloqueada se qualquer uma destas condições for verdadeira:

* Atribuiu recursos de previsão/tempo de execução em aplicações que não possui.
* Tem outros utilizadores que atribuem recursos de previsão/tempo de execução a aplicações que possui.

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>Passos recomendados se você é o proprietário da app
Se é proprietário de algumas aplicações e tem colaboradores que atribuem uma chave de previsão/tempo de execução a estas aplicações, surge um erro quando migra. O erro lista os IDs da aplicação que têm chaves de previsão atribuídas a eles por outros utilizadores.

É recomendável que:
* Notifique os colaboradores sobre a migração.
* Remova todos os colaboradores das aplicações apresentadas no erro.
* Submeta-se ao processo de migração, que deverá ter sucesso se remover manualmente os colaboradores.
* Atribua colaboradores como contribuintes para o seu novo recurso de autoria. Os colaboradores migrarão e reatribuirão os recursos de previsão para as aplicações. Note que isto irá causar uma rutura na aplicação temporariamente até que os recursos de previsão sejam transferidos.

Há outra solução potencial aqui. Antes da migração do proprietário, os colaboradores podem adicionar os proprietários de aplicações como contribuintes nas suas subscrições Azure a partir do portal Azure. Este passo permitirá ao proprietário o acesso ao recurso de previsão de tempo de execução. Se o proprietário migrar usando a nova subscrição a que foram adicionados (que será encontrado sob um novo inquilino), este passo não irá apenas desbloquear o processo de migração tanto para colaboradores como para o proprietário de aplicações. Também irá permitir uma migração suave de apps, com a chave de previsão ainda atribuída a que não quebram as apps.


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>Passos recomendados se for colaborador numa aplicação
Se estiver a colaborar em aplicações e tiver atribuído uma chave de previsão/tempo de execução a estas aplicações, surge um erro quando migra. O erro lista os IDs de aplicação e os caminhos-chave que estão a bloquear a migração.

É recomendável que:
* Pedidos de exportação como apoio. Este é um passo opcional no processo de migração.
* Desatribua os recursos de previsão da página de recursos **do Manage**  >  **Azure.**
* Submeter-se ao processo de migração.
* Importar pedidos de volta após a migração.
* Reatribuir chaves de previsão para as suas aplicações a partir da página **de** recursos Manage  >  **Azure.**

> [!Note]
> Quando importar as suas aplicações depois de migrar, terão diferentes IDs de aplicações. Também serão diferentes dos que estão a ser atingidos na produção. Será agora o proprietário destas aplicações.

## <a name="troubleshooting-the-migration-process"></a>Resolução de problemas do processo de migração

Quando tenta migrar, mas não consegue encontrar a sua subscrição do Azure na lista de drop-down:
* Certifique-se de que tem uma subscrição válida do Azure que está autorizada a criar recursos de Serviços Cognitivos. Vá ao [portal Azure](https://ms.portal.azure.com) e verifique o estado da subscrição. Se não tiver uma, [crie uma conta Azure gratuita.](https://azure.microsoft.com/free/cognitive-services/)
* Certifique-se de que está no inquilino adequado associado à sua subscrição válida. Pode trocar os inquilinos do avatar para a esquerda das suas iniciais nesta barra de ferramentas: ![ Toolbar onde pode trocar de inquilinos](./media/migrate-authoring-key/switch-user-tenant-2.png)

Se tiver um recurso de autoria existente, mas não o encontrar quando selecionar a opção Utilizar o **recurso de autoria existente:**
* O seu recurso foi provavelmente criado num local diferente do portal onde está inscrito. Consulte as [regiões e portais de autoria DO LUIS.](./luis-reference-regions.md#luis-authoring-regions)
* Criar um novo recurso a partir do portal LUIS.

Se selecionar a opção **Criar Novo Recurso de Autoria** e a migração falhar com a mensagem de erro "Falha na recuperação das informações Azure do utilizador, volte a tentar mais tarde":
* A sua subscrição pode ter 10 ou mais recursos de autoria por região, por subscrição. Se for esse o caso, não poderá criar um novo recurso de autoria.
* Migrar selecionando a opção **Utilização do Recurso de Autoria Existente** e selecionando um dos recursos existentes sob a sua subscrição.

Se vir o seguinte erro, verifique os [passos recomendados se é o proprietário da aplicação.](#recommended-steps-if-youre-the-owner-of-the-app)
![Erro que mostra que a migração falhou para os proprietários](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Se vir o seguinte erro, verifique os [passos recomendados se é colaborador numa aplicação.](#recommended-steps-if-youre-a-collaborator-on-an-app)
![Erro que mostra que a migração falhou para os colaboradores](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Próximos passos

* Reveja [conceitos sobre a autoria e as teclas de tempo de execução.](luis-how-to-azure-subscription.md)
* Reveja como [atribuir chaves](luis-how-to-azure-subscription.md) e [adicionar contribuintes](luis-how-to-collaborate.md).
