---
title: Gerenciamento de provisionamento de usuário para aplicativos empresariais no Azure AD
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
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34f9f5935737bcd95082e4f08d0c979a0fac830b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066970"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gerenciando o provisionamento de conta de usuário para aplicativos empresariais no portal do Azure

Este artigo descreve as etapas gerais para gerenciar o provisionamento e desprovisionamento de conta de usuário automático para aplicativos que dão suporte a ele. *O provisionamento* da conta de utilizador é o ato de criar, atualizar e/ou desativar os registos da conta de utilizador na loja de perfis de utilizador local de uma aplicação. A maioria das aplicações cloud e SaaS armazenam a função e permissões dos utilizadores na própria loja de perfis de utilizador local do utilizador, e a presença de tal registo de utilizador na loja local do utilizador é *necessária* para um único registo e acesso ao trabalho. Para saber mais sobre o fornecimento automático de conta de utilizador, consulte o fornecimento automático de [utilizadores e o deprovisionamento às aplicações SaaS com o Diretório Ativo Azure](user-provisioning.md).

> [!IMPORTANT]
> O Azure Active Directory (AD do Azure) tem uma galeria que contém milhares de aplicativos previamente integrados que são habilitados para provisionamento automático com o Azure AD. Deve começar por encontrar o tutorial de configuração específico da sua aplicação na [Lista de tutoriais sobre como integrar aplicações SaaS com o Diretório Ativo Azure](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Você provavelmente encontrará orientações passo a passo para configurar o aplicativo e o Azure AD para criar a conexão de provisionamento.

## <a name="finding-your-apps-in-the-portal"></a>Localizando seus aplicativos no portal

Use o portal de Azure Active Directory para exibir e gerenciar todos os aplicativos configurados para logon único em um diretório. Aplicativos empresariais são aplicativos que são implantados e usados em sua organização. Siga estas etapas para exibir e gerenciar seus aplicativos empresariais:

1. Abra o [portal azure Ative Diretório.](https://aad.portal.azure.com)
1. Selecione **aplicações Enterprise** a partir do painel esquerdo. Uma lista de todos os aplicativos configurados é mostrada, incluindo os aplicativos que foram adicionados da galeria.
1. Selecione qualquer aplicativo para carregar seu painel de recursos, no qual você pode exibir relatórios e gerenciar configurações de aplicativo.
1. Selecione **Provisioning** para gerir as definições de provisionamento da conta de utilizador para a aplicação selecionada.

   ![Tela de provisionamento para gerenciar as configurações de provisionamento de conta de usuário](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modos de provisionamento

O painel de **provisionamento** começa com um menu **Modo,** que mostra os modos de provisionamento suportados para uma aplicação empresarial, e permite configurá-los. As opções disponíveis incluem:

* **Automática** - Esta opção é mostrada se a Azure AD suporta o fornecimento automático de API ou o desprovisionamento das contas de utilizador para esta aplicação. Selecione este modo para exibir uma interface que ajuda os administradores:

  * Configurar o Azure AD para se conectar à API de gerenciamento de usuários do aplicativo
  * Criar mapeamentos de conta e fluxos de trabalho que definem como os dados de conta de usuário devem fluir entre o Azure AD e o aplicativo
  * Gerenciar o serviço de provisionamento do Azure AD

* **Manual** - Esta opção é mostrada se a Azure AD não suportar o fornecimento automático de contas de utilizador a esta aplicação. Nesse caso, os registros de conta de usuário armazenados no aplicativo devem ser gerenciados usando um processo externo, com base nos recursos de gerenciamento e provisionamento de usuários fornecidos pelo aplicativo (que pode incluir o provisionamento just-in-time do SAML).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurando o provisionamento automático de conta de usuário

Selecione a opção **Automática** para especificar as definições para credenciais de administração, mapeamentos, arranque e paragem e sincronização.

### <a name="admin-credentials"></a>Credenciais de administrador

Expandir **as credenciais de administrador** para introduzir as credenciais necessárias para que a AD Azure se conectem à API de gestão de utilizadores da aplicação. A entrada necessária varia dependendo do aplicativo. Para conhecer os tipos e requisitos credenciais para aplicações específicas, consulte o tutorial de [configuração para essa aplicação específica](user-provisioning.md).

Selecione **Test Connection** para testar as credenciais, fazendo com que o Azure AD tente ligar-se à aplicação de provisionamento da aplicação utilizando as credenciais fornecidas.

### <a name="mappings"></a>Mapeamentos

Expandir **Mapeamentos** para visualizar e editar os atributos do utilizador que fluem entre a AD Azure e a aplicação-alvo quando as contas de utilizador são aprovisionadas ou atualizadas.

Há um conjunto pré-configurado de mapeamentos entre objetos de usuário do Azure AD e os objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos, como grupos ou contatos. Selecione um mapeamento na tabela para abrir o editor de mapeamento à direita, onde você pode exibi-los e personalizá-los.

![Mostra a tela de mapeamento de atributo](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

As personalizações com suporte incluem:

* Habilitação e desabilitação de mapeamentos para objetos específicos, como o objeto de usuário do Azure AD para o objeto de usuário do aplicativo SaaS.
* Editando os atributos que fluem do objeto de usuário do Azure AD para o objeto de usuário do aplicativo. Para obter mais informações sobre o mapeamento do atributo, consulte [understanding attribute mapeamento tipos](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrar as ações de provisionamento que o Azure AD executa no aplicativo de destino. Em vez de fazer com que o Azure AD sincronize completamente objetos, você pode limitar as ações executadas.

  Por exemplo, apenas selecione **Update** e Azure AD apenas atualiza as contas de utilizador existentes numa aplicação, mas não cria novas. Apenas selecione **Create** e Azure apenas cria novas contas de utilizador, mas não atualiza as existentes. Esse recurso permite que os administradores criem mapeamentos diferentes para criação de conta e fluxos de trabalho de atualização.

* Adicionando um novo mapeamento de atributo. **Selecione Adicionar novo mapeamento** na parte inferior do painel de mapeamento do **atributo.** Preencha o formulário **Editar Atributo** e selecione **Ok** para adicionar o novo mapeamento à lista.

### <a name="settings"></a>Definições

Pode iniciar e parar o serviço de provisionamento De AD Azure para a aplicação selecionada na área de **Definições** do ecrã **de Provisionamento.** Você também pode optar por limpar o cache de provisionamento e reiniciar o serviço.

Se o provisionamento estiver a ser ativado pela primeira vez para uma aplicação, ligue o serviço alterando o Estado de **Provisionamento** para **On**. Essa alteração faz com que o serviço de provisionamento do Azure AD execute um ciclo inicial. Lê os utilizadores atribuídos na secção **Utilizadores e grupos,** consulta a aplicação-alvo para os mesmos e, em seguida, executa as ações de provisionamento definidas na secção de **Mapeamento** sinuoso Da AD Azure. Durante esse processo, o serviço de provisionamento armazena dados armazenados em cache sobre quais contas de usuário ele está gerenciando, por isso as contas não gerenciadas nos aplicativos de destino que nunca estavam no escopo para atribuição não são afetadas pelas operações de desprovisionamento. Após o ciclo inicial, o serviço de provisionamento sincroniza automaticamente objetos de usuário e grupo em um intervalo de 40 minutos.

Alterar o Estado de **Provisionamento** para **Desligar** para interromper o serviço de provisionamento. Nesse estado, o Azure não cria, atualiza ou remove nenhum objeto de usuário ou grupo no aplicativo. Mude o estado de volta para **On** e o serviço retoma onde ficou parado.

O estado atual claro e a **sincronização de reinício** desencadeiam um ciclo inicial. O serviço irá então avaliar todos os usuários no sistema de origem novamente e determinar se eles estão no escopo do provisionamento. Isso pode ser útil quando seu aplicativo está em quarentena no momento ou você precisa fazer uma alteração nos mapeamentos de atributo. Observe que o ciclo inicial leva mais tempo para ser concluído do que o ciclo incremental típico devido ao número de objetos que precisam ser avaliados. Pode saber mais sobre o desempenho dos ciclos iniciais e incrementais [aqui](application-provisioning-when-will-provisioning-finish-specific-user.md). 
