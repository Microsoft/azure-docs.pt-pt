---
title: Gerenciamento de provisionamento de usuário para aplicativos empresariais no Azure Active Directory | Microsoft Docs
description: Saiba como gerenciar o provisionamento de conta de usuário para aplicativos empresariais usando o Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395957099a19d11d26fb5bdd2a1eecdd5590dd64
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056142"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gerenciando o provisionamento de conta de usuário para aplicativos empresariais no portal do Azure

Este artigo descreve como usar o [portal do Azure](https://portal.azure.com) para gerenciar o provisionamento e desprovisionamento de conta de usuário automático para aplicativos que dão suporte a ele. Para saber mais sobre o provisionamento automático de conta de usuário e como ela funciona, confira [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Localizando seus aplicativos no portal

Use o portal de Azure Active Directory para exibir e gerenciar todos os aplicativos configurados para logon único em um diretório. Aplicativos empresariais são aplicativos que são implantados e usados em sua organização. Siga estas etapas para exibir e gerenciar seus aplicativos empresariais:

1. Abra o [portal de Azure Active Directory](https://aad.portal.azure.com).
1. Selecione **aplicativos empresariais** no painel esquerdo. Uma lista de todos os aplicativos configurados é mostrada, incluindo os aplicativos que foram adicionados da galeria.
1. Selecione qualquer aplicativo para carregar seu painel de recursos, no qual você pode exibir relatórios e gerenciar configurações de aplicativo.
1. Selecione **provisionamento** para gerenciar as configurações de provisionamento de conta de usuário para o aplicativo selecionado.

   ![Tela de provisionamento para gerenciar as configurações de provisionamento de conta de usuário](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modos de provisionamento

O painel de **provisionamento** começa com um menu de **modo** , que mostra os modos de provisionamento com suporte para um aplicativo empresarial e permite que você os configure. As opções disponíveis incluem:

* **Automático** – essa opção será mostrada se o Azure ad der suporte ao provisionamento automático baseado em API ou ao desprovisionamento de contas de usuário para este aplicativo. Selecione este modo para exibir uma interface que ajuda os administradores:

  * Configurar o Azure AD para se conectar à API de gerenciamento de usuários do aplicativo
  * Criar mapeamentos de conta e fluxos de trabalho que definem como os dados de conta de usuário devem fluir entre o Azure AD e o aplicativo
  * Gerenciar o serviço de provisionamento do Azure AD

* **Manual** -essa opção será mostrada se o Azure ad não der suporte ao provisionamento automático de contas de usuário para este aplicativo. Nesse caso, os registros de conta de usuário armazenados no aplicativo devem ser gerenciados usando um processo externo, com base nos recursos de gerenciamento e provisionamento de usuários fornecidos pelo aplicativo (que pode incluir o provisionamento just-in-time do SAML).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurando o provisionamento automático de conta de usuário

Selecione a opção **automática** para especificar as configurações de credenciais de administrador, mapeamentos, início e parada e sincronização.

### <a name="admin-credentials"></a>Credenciais de Administrador

Expanda **credenciais de administrador** para inserir as credenciais necessárias para que o Azure ad se conecte à API de gerenciamento de usuários do aplicativo. A entrada necessária varia dependendo do aplicativo. Para saber mais sobre os tipos de credencial e os requisitos para aplicativos específicos, consulte o [tutorial de configuração para esse aplicativo específico](user-provisioning.md).

Selecione **testar conexão** para testar as credenciais fazendo com que o Azure ad tente se conectar ao aplicativo de provisionamento do aplicativo usando as credenciais fornecidas.

### <a name="mappings"></a>Mapeamentos

Expanda **mapeamentos** para exibir e editar os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino quando as contas de usuário são provisionadas ou atualizadas.

Há um conjunto pré-configurado de mapeamentos entre objetos de usuário do Azure AD e os objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos, como grupos ou contatos. Selecione um mapeamento na tabela para abrir o editor de mapeamento à direita, onde você pode exibi-los e personalizá-los.

![Mostra a tela de mapeamento de atributo](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

As personalizações com suporte incluem:

* Habilitação e desabilitação de mapeamentos para objetos específicos, como o objeto de usuário do Azure AD para o objeto de usuário do aplicativo SaaS.
* Editando os atributos que fluem do objeto de usuário do Azure AD para o objeto de usuário do aplicativo. Para obter mais informações sobre mapeamento de atributos, consulte [noções básicas sobre tipos de mapeamento de atributo](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrar as ações de provisionamento que o Azure AD executa no aplicativo de destino. Em vez de fazer com que o Azure AD sincronize completamente objetos, você pode limitar as ações executadas.

  Por exemplo, somente selecione **Atualizar** e o Azure ad só atualiza contas de usuário existentes em um aplicativo, mas não cria novas. Somente selecione **criar** e o Azure só cria novas contas de usuário, mas não atualiza as existentes. Esse recurso permite que os administradores criem mapeamentos diferentes para criação de conta e fluxos de trabalho de atualização.

* Adicionando um novo mapeamento de atributo. Selecione **Adicionar novo mapeamento** na parte inferior do painel **mapeamento de atributo** . Preencha o formulário **Editar atributo** e selecione **OK** para adicionar o novo mapeamento à lista.

### <a name="settings"></a>Definições

Você pode iniciar e parar o serviço de provisionamento do Azure AD para o aplicativo selecionado na área **configurações** da tela de **provisionamento** . Você também pode optar por limpar o cache de provisionamento e reiniciar o serviço.

Se o provisionamento estiver sendo habilitado pela primeira vez para um aplicativo, ative o serviço alterando o **status de provisionamento** para **ativado**. Essa alteração faz com que o serviço de provisionamento do Azure AD execute um ciclo inicial. Ele lê os usuários atribuídos na seção **usuários e grupos** , consulta o aplicativo de destino para eles e, em seguida, executa as ações de provisionamento definidas na seção **mapeamentos** do Azure AD. Durante esse processo, o serviço de provisionamento armazena dados armazenados em cache sobre quais contas de usuário ele está gerenciando, por isso as contas não gerenciadas nos aplicativos de destino que nunca estavam no escopo para atribuição não são afetadas pelas operações de desprovisionamento. Após o ciclo inicial, o serviço de provisionamento sincroniza automaticamente objetos de usuário e de grupo em um intervalo de dez minutos.

Altere o **status de provisionamento** para **desativado** para pausar o serviço de provisionamento. Nesse estado, o Azure não cria, atualiza ou remove nenhum objeto de usuário ou grupo no aplicativo. Altere o estado de volta para **ativado** e o serviço será selecionado de onde parou.

Marque a caixa de seleção **limpar estado atual e reiniciar sincronização** e selecione **salvar** em:

* Parar o serviço de provisionamento
* Despejar os dados armazenados em cache sobre quais contas o Azure AD está gerenciando
* Reinicie os serviços e execute o ciclo inicial novamente

Essa opção permite que os administradores iniciem o processo de implantação de provisionamento novamente.
