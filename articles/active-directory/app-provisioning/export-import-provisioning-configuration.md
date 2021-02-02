---
title: Configuração de provisionamento de exportação e retrocesso para um bom estado conhecido para recuperação de desastres
description: Aprenda a exportar a sua configuração de provisionamento e volte para um bom estado conhecido para a recuperação de desastres.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/19/2020
ms.author: kenwith
ms.openlocfilehash: c6af42c78bda66c4b397cbb99b26af7d6a5c7f07
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256378"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Como fazer: Configuração de provisionamento de exportação e retrocesso para um bom estado conhecido

Neste artigo, vai aprender a:

- Exporte e importe a sua configuração de provisionamento a partir do portal Azure
- Exporte e importe a sua configuração de provisionamento utilizando a API do Microsoft Graph

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Exporte e importe a sua configuração de provisionamento a partir do portal Azure

### <a name="export-your-provisioning-configuration"></a>Exporte a sua configuração de provisionamento

Para exportar a sua configuração:

1. No [portal Azure,](https://portal.azure.com/)no painel de navegação à esquerda, selecione **Azure Ative Directory**.
1. No painel **Azure Ative Directory,** selecione **aplicações da Enterprise** e escolha a sua aplicação.
1. No painel de navegação à esquerda, selecione **provisionamento**. A partir da página de configuração de provisionamento, clique nos **mapeamentos de atributos,** em seguida, **mostre opções avançadas** e, finalmente, **reveja o seu esquema**. Isto vai levá-lo ao editor de esquemas.
1. Clique em baixar na barra de comando no topo da página para baixar o seu esquema.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Recuperação de desastres - recue para um bom estado conhecido

Exportar e guardar a sua configuração permite-lhe reverter para uma versão anterior da sua configuração. Recomendamos exportar a sua configuração de provisionamento e guardá-la para posterior utilização sempre que fizer uma alteração nos mapeamentos ou filtros de deteção de atributos. Tudo o que precisa de fazer é abrir o ficheiro JSON que descarregou nos degraus acima, copiar todo o conteúdo do ficheiro JSON, substituir todo o conteúdo da carga JSON no editor de esquemas e, em seguida, guardar. Se houver um ciclo de provisionamento ativo, ele completa-se e o ciclo seguinte utilizará o esquema atualizado. O próximo ciclo será também um ciclo inicial, que reavalia cada utilizador e grupo com base na nova configuração. Considere o seguinte ao voltar a uma configuração anterior:

- Os utilizadores serão novamente avaliados para determinar se devem estar no âmbito. Se os filtros de deteção tiverem mudado, o utilizador já não está no âmbito, será desativado. Embora este seja o comportamento desejado na maioria dos casos, há momentos em que pode querer evitar isso e pode usar a funcionalidade [de eliminação de âmbito.](./skip-out-of-scope-deletions.md) 
- Alterar a configuração de provisionamento reinicia o serviço e aciona um [ciclo inicial](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental).

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Exporte e importe a sua configuração de provisionamento utilizando a API do Microsoft Graph

Pode utilizar a API do Microsoft Graph e o Microsoft Graph Explorer para exportar os seus mapeamentos e esquemas de provisão de utilizador para um ficheiro JSON e importá-lo de volta para Azure AD. Também pode utilizar os passos capturados aqui para criar uma cópia de segurança da sua configuração de provisionamento.

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passo 1: Recuperar o ID principal do serviço de aplicações de provisionamento (ID do objeto)

1. Lance o [portal Azure](https://portal.azure.com)e navegue para a secção Propriedades da sua aplicação de provisionamento. Por exemplo, se pretender exportar o seu *Workday para a AD User Provisioning application* mapping navega para a secção Propriedades dessa aplicação.
1. Na secção Propriedades da sua aplicação de provisionamento, copie o valor GUID associado ao campo *de ID* do objeto. Este valor também é chamado de **ServicePrincipalId** da sua App e será utilizado nas operações do Microsoft Graph Explorer.

   ![ID principal do serviço de aplicações workday](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passo 2: Inscreva-se no Microsoft Graph Explorer

1. Lançar [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão "Iniciar com a Microsoft" e inscreva-se utilizando credenciais de administração global do Azure AD Ou app.

    ![Microsoft Graph'in](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Após o sinse-in com sucesso, verá os detalhes da conta do utilizador no painel esquerdo.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Passo 3: Recuperar o ID de trabalho de provisionamento da App de Provisionamento

No Microsoft Graph Explorer, executar a seguinte consulta GET substituindo [servicePrincipalId] pelo **ServicePrincipalId** extraído do [Passo 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Terá uma resposta como mostrado abaixo. Copie o "atributo id" presente na resposta. Este valor é o **ProvisioningJobId** e será utilizado para recuperar os metadados de esquema subjacentes.

   [![ID de trabalho de provisionamento](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Passo 4: Descarregue o esquema de provisionamento

No Microsoft Graph Explorer, executar a seguinte consulta GET, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e o ProvisioningJobId recuperados nos passos anteriores.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copie o objeto JSON da resposta e guarde-o num ficheiro para criar uma cópia de segurança do esquema.

### <a name="step-5-import-the-provisioning-schema"></a>Passo 5: Importar o esquema de provisionamento

> [!CAUTION]
> Execute este passo apenas se precisar de modificar o esquema para configuração que não pode ser alterado usando o portal Azure ou se precisar restaurar a configuração de um ficheiro previamente apoiado com esquema válido e de funcionamento.

No Microsoft Graph Explorer, configuure a seguinte consulta PUT, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e pelo ProvisioningJobId recuperados nos passos anteriores.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

No separador "Request Body", copie o conteúdo do ficheiro de esquema JSON.

   [![Corpo do Pedido](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

No separador "Cabeçalhos de pedido", adicione o atributo cabeçalho do tipo de conteúdo com o valor "aplicação/json"

   [![Pedido cabeçalhos](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Selecione **Run Consulta** para importar o novo esquema.