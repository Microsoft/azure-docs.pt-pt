---
title: Exporte a sua configuração de provisionamento e recue para um bom estado conhecido para a recuperação de desastres.[ Microsoft Docs
description: Aprenda a exportar a sua configuração de provisionamento e recue para um bom estado conhecido para a recuperação de desastres.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80051315"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Exporte a sua configuração de provisionamento e recue para um bom estado conhecido

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Exportar e importar a sua configuração de provisionamento do portal Azure

### <a name="how-can-i-export-my-provisioning-configuration"></a>Como posso exportar a minha configuração de provisionamento?
Para exportar a sua configuração:
1. No [portal Azure,](https://portal.azure.com/)no painel de navegação à esquerda, selecione **Azure Ative Directory**.
2. No painel **azure Ative Directory,** selecione **aplicações Da Enterprise** e escolha a sua aplicação.
3. No painel de navegação à esquerda, selecione **o fornecimento**. A partir da página de configuração de provisionamento, clique em **mapeamentos de atributos,** em **seguida, mostrar opções avançadas**, e finalmente **rever o seu esquema**. Isto vai levá-lo ao editor de esquemas. 
5. Clique em download na barra de comando na parte superior da página para descarregar o seu esquema.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Recuperação de desastres - recue para um bom estado conhecido
Exportar e salvar a sua configuração permite-lhe reverter para uma versão anterior da sua configuração. Recomendamos exportar a sua configuração de provisionamento e guardá-la para posterior utilização sempre que fizer uma alteração nos mapeamentos de atributos ou filtros de deteção. Tudo o que precisa de fazer é abrir o ficheiro JSON que descarregou nos degraus acima, copiar todo o conteúdo do ficheiro JSON, substituir todo o conteúdo da carga útil JSON no editor de esquemas e, em seguida, guardar. Se houver um ciclo de provisionamento ativo, estará concluído e o próximo ciclo utilizará o esquema atualizado. O próximo ciclo será também um ciclo inicial, que reavalia todos os utilizadores e grupos com base na nova configuração. Considere o seguinte ao voltar para uma configuração anterior:
* Os utilizadores serão novamente avaliados para determinar se devem estar no âmbito. Se os filtros de deteção tiverem mudado, um utilizador já não está no âmbito, serão desativados. Embora este seja o comportamento desejado na maioria dos casos, há momentos em que pode querer evitar isso e pode usar a funcionalidade [de supressão de supressões de âmbito.](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions) 
* Alterar a configuração de fornecimento reinicia o serviço e aciona um [ciclo inicial](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental).


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Exportar e importar a sua configuração de provisionamento utilizando a Microsoft Graph API
Pode utilizar o Microsoft Graph API e o Microsoft Graph Explorer para exportar os mapeamentos de atributos e esquemas para um ficheiro JSON e importá-lo de volta para AD Azure. Também pode utilizar os passos capturados aqui para criar uma cópia de segurança da sua configuração de provisionamento. 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passo 1: Recuperar o seu ID principal do serviço de aplicações de provisionamento (ID do objeto)

1. Lance o [portal Azure](https://portal.azure.com)e navegue para a secção Propriedades da sua aplicação de provisionamento. Por exemplo, se quiser exportar o seu Dia de Trabalho para o mapeamento de *aplicações de fornecimento* de utilizadores ad para a secção Propriedades dessa aplicação. 
1. Na secção Propriedades da sua aplicação de provisionamento, copie o valor GUID associado ao campo ID do *Objeto.* Este valor também é chamado de **ServicePrincipalId** da sua App e será utilizado nas operações do Microsoft Graph Explorer.

   ![Id principal do serviço de aplicações do dia de trabalho](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passo 2: Assine no Microsoft Graph Explorer

1. Lançar [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão "Iniciar sessão com a Microsoft" e iniciar sessão utilizando credenciais de Administração Global Azure AD Ou App Admin.

    ![Microsoft Graph Sign-in](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Após o sucesso do início de sessão, verá os detalhes da conta do utilizador no painel da mão esquerda.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Passo 3: Recuperar o ID de trabalho de provisionamento da App de Provisionamento

No Microsoft Graph Explorer, faça a seguinte consulta GET substituindo [servicePrincipalId] pelo **ServiçoPrincipalId** extraído do [Passo 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Receberá uma resposta como mostrado abaixo. Copie o "atributo id" presente na resposta. Este valor é o **ProvisioningJobId** e será utilizado para recuperar os metadados de esquemas subjacentes.

   [![Provisioning Job ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Passo 4: Descarregue o Provisioning Schema

No Microsoft Graph Explorer, faça a seguinte consulta GET, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e o ProvisioningJobId recuperados nos passos anteriores.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copie o objeto JSON a partir da resposta e guarde-o para um ficheiro para criar uma cópia de segurança do esquema.

### <a name="step-5-import-the-provisioning-schema"></a>Passo 5: Importar o Schema de Provisionamento

> [!CAUTION]
> Execute este passo apenas se precisar de modificar o esquema para configuração que não pode ser alterada usando o portal Azure ou se precisar restaurar a configuração a partir de um ficheiro previamente apoiado com esquema válido e de trabalho.

No Microsoft Graph Explorer, configure a seguinte consulta PUT, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e o ProvisioningJobId recuperados nos passos anteriores.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

No separador "Request Body", copie o conteúdo do ficheiro esquema JSON.

   [![Corpo do Pedido](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

No separador "Cabeçalhos de Pedido", adicione o atributo cabeçalho do Tipo conteúdo com valor "aplicação/json"

   [![Cabeçalhos de Pedido](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Clique no botão "Run Query" para importar o novo esquema.
