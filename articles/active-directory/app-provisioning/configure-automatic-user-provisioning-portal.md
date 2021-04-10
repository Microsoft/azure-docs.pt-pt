---
title: Gestão de provisão de utilizadores para aplicações empresariais no Azure Ative Directory
description: Saiba como gerir o fornecimento de conta de utilizador para aplicações empresariais utilizando o Diretório Ativo Azure.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/18/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5dceeb11ed9a4d6af88650a6146f58db412748d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579421"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gestão do fornecimento de conta de utilizador para aplicações empresariais no portal Azure

Este artigo descreve as etapas gerais para a gestão do provisionamento automático da conta de utilizador e de desatentação de aplicações que o suportam. *O fornecimento de conta de utilizador* é o ato de criar, atualizar e/ou desativar registos de contas de utilizador na loja de perfis de utilizador local de uma aplicação. A maioria das aplicações em nuvem e SaaS armazenam o papel e permissões dos utilizadores na própria loja de perfis de utilizador local do utilizador, e a presença de tal registo de utilizador na loja local do utilizador é *necessária* para uma única sessão de acesso e acesso ao trabalho. Para saber mais sobre o fornecimento automático de conta de utilizador, consulte [automatizar o Provisionamento do Utilizador e Deprovisionar para aplicações SaaS com o Azure Ative Directory](user-provisioning.md).

> [!IMPORTANT]
> O Azure Ative Directory (Azure AD) tem uma galeria que contém milhares de aplicações pré-integradas que estão habilitadas para o fornecimento automático com Azure AD. Deve começar por encontrar o tutorial de configuração de provisionamento específico da sua aplicação na [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Ative Directory](../saas-apps/tutorial-list.md). É provável que encontre orientações passo a passo para configurar tanto a app como o AD Azure para criar a ligação de provisionamento.

## <a name="finding-your-apps-in-the-portal"></a>Encontrar as suas apps no portal

Utilize o portal Azure Ative Directory para visualizar e gerir todas as aplicações configuradas para um único sinal de inscrição num diretório. As aplicações da empresa são aplicações que são implementadas e usadas dentro da sua organização. Siga estes passos para visualizar e gerir as suas aplicações empresariais:

1. Abra o [portal Azure Ative Directory](https://aad.portal.azure.com).
1. Selecione **aplicações** Enterprise a partir do painel esquerdo. É mostrada uma lista de todas as aplicações configuradas, incluindo aplicações que foram adicionadas da galeria.
1. Selecione qualquer aplicação para carregar o painel de recursos, onde pode visualizar relatórios e gerir as definições de aplicações.
1. **Selecione Provisioning** para gerir as definições de provisão de conta de utilizador para a aplicação selecionada.

   ![Rastreio de provisionamento para gerir as definições de provisionamento de conta de utilizador](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modos de provisionamento

O **painel de provisionamento** começa com um menu **Modo,** que mostra os modos de provisionamento suportados para uma aplicação da empresa, e permite configurá-los. As opções disponíveis incluem:

* **Automático** - Esta opção é mostrada se a Azure AD suporta o fornecimento automático baseado em API ou o desavisionamento de contas de utilizador a esta aplicação. Selecione este modo para exibir uma interface que ajuda os administradores:

  * Configure AD AZure para ligar à API de gestão de utilizadores da aplicação
  * Crie mapeamentos de conta e fluxos de trabalho que definam como os dados da conta de utilizador devem fluir entre a Azure AD e a app
  * Gerir o serviço de prestação de AD Azure

* **Manual** - Esta opção é mostrada se o Azure AD não suporta o fornecimento automático de contas de utilizador a esta aplicação. Neste caso, os registos de conta de utilizador armazenados na aplicação devem ser geridos utilizando um processo externo, com base na gestão do utilizador e nas capacidades de provisionamento fornecidas por essa aplicação (que pode incluir o provisionamento DAML Just-In-Time).

## <a name="configuring-automatic-user-account-provisioning"></a>Configuração do provisionamento automático da conta de utilizador

Selecione a opção **Automática** para especificar definições para credenciais de administração, mapeamentos, início e paragem e sincronização.

### <a name="admin-credentials"></a>Credenciais de administrador

Expandir credenciais de **administração** para introduzir as credenciais necessárias para que a Azure AD se conecte à API de gestão de utilizadores da aplicação. A entrada requerida varia consoante a aplicação. Para conhecer os tipos e requisitos credenciais para aplicações específicas, consulte o [tutorial de configuração para essa aplicação específica.](user-provisioning.md)

Selecione **a Ligação** de Teste para testar as credenciais, fazendo com que a Azure AD tente ligar-se à aplicação de provisionamento da aplicação utilizando as credenciais fornecidas.

### <a name="mappings"></a>Mapeamentos

Expandir **mapeamentos** para visualizar e editar os atributos do utilizador que fluem entre a Azure AD e a aplicação-alvo quando as contas do utilizador são a provisionadas ou atualizadas.

Há um conjunto pré-configurado de mapeamentos entre objetos de utilizador AZure AD e objetos de utilizador de cada aplicação SaaS. Algumas aplicações também gerem objetos de grupo. Selecione um mapeamento na mesa para abrir o editor de mapeamento, onde pode vê-lo e personalizá-los.

As personalizações suportadas incluem:

* Ativar e desativar mapeamentos para objetos específicos, como o objeto do utilizador Azure AD para o objeto do utilizador da aplicação SaaS.
* Editar os atributos que fluem do objeto do utilizador Azure AD para o objeto do utilizador da aplicação. Para obter mais informações sobre o mapeamento de atributos, consulte [os tipos de mapeamento de atributos Understanding](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrar as ações de provisionamento que o Azure AD executa na aplicação visada. Em vez de ter o Azure AD a sincronizar totalmente os objetos, pode limitar as ações executadas.

  Por exemplo, apenas selecione **Update** e Azure AD apenas atualiza as contas de utilizador existentes numa aplicação, mas não cria novas. Apenas selecione **Criar** e Azure apenas cria novas contas de utilizador, mas não atualiza as existentes. Esta funcionalidade permite que os administradores criem diferentes mapeamentos para a criação de conta e atualizar fluxos de trabalho.

* Adicionando um novo mapeamento de atributos. **Selecione Adicionar Novo Mapeamento** na parte inferior do **painel de mapeamento** do atributo. Preencha o formulário **EditAr Atributo** e selecione **Ok** para adicionar o novo mapeamento à lista.

### <a name="settings"></a>Definições

Expandir **Definições** para definir um endereço de e-mail para receber notificações e se deve receber alertas sobre erros. Também pode selecionar o âmbito de sincronização dos utilizadores. Pode optar por sincronizar todos os utilizadores e grupos ou apenas os que estão atribuídos.

### <a name="provisioning-status"></a>Estatuto de Provisionamento 

Se o provisionamento estiver a ser habilitado pela primeira vez para uma aplicação, ligue o serviço alterando o **Estado de Provisionamento** para **On**. Esta alteração faz com que o serviço de fornecimento de Ad Azure execute um ciclo inicial. Lê os utilizadores designados na secção **Utilizadores e grupos,** consulta a aplicação-alvo para os mesmos e, em seguida, executa as ações de provisionamento definidas na secção Azure AD **Mappings.** Durante este processo, o serviço de fornecimento armazena dados em cache sobre que contas de utilizador está a gerir, pelo que contas não geridas dentro das aplicações-alvo que nunca estiveram em âmbito de atribuição não são afetadas por operações de desavisionamento. Após o ciclo inicial, o serviço de fornecimento sincroniza automaticamente os objetos do utilizador e do grupo num intervalo de 40 minutos.

Altere o **Estado de Provisionamento** para **Off**  para interromper o serviço de prestação. Neste estado, o Azure não cria, atualiza ou remove qualquer utilizador ou objetos de grupo na aplicação. Mude o estado de volta para **On** e o serviço retoma onde deixou de lado.
