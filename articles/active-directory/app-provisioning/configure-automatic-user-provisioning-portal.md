---
title: Gestão de provisionamento de utilizadores para aplicações empresariais em Azure AD
description: Saiba como gerir o provisionamento de contas de utilizador para aplicações empresariais utilizando o Diretório Ativo do Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 02f3198a9c3d35a0031fdd65ceefbb72b0f8bb31
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593816"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gestão do provisionamento de conta de utilizador para aplicações empresariais no portal Azure

Este artigo descreve os passos gerais para a gestão do provisionamento automático da conta de utilizador e o desprovisionamento de aplicações que a suportam. *O provisionamento* da conta de utilizador é o ato de criar, atualizar e/ou desativar os registos da conta de utilizador na loja de perfis de utilizador local de uma aplicação. A maioria das aplicações cloud e SaaS armazenam a função e permissões dos utilizadores na própria loja de perfis de utilizador local do utilizador, e a presença de tal registo de utilizador na loja local do utilizador é *necessária* para um único registo e acesso ao trabalho. Para saber mais sobre o fornecimento automático de conta de utilizador, consulte o fornecimento automático de [utilizadores e o deprovisionamento às aplicações SaaS com o Diretório Ativo Azure](user-provisioning.md).

> [!IMPORTANT]
> O Azure Ative Directory (Azure AD) tem uma galeria que contém milhares de aplicações pré-integradas que estão habilitadas para o fornecimento automático com a Azure AD. Deve começar por encontrar o tutorial de configuração específico da sua aplicação na [Lista de tutoriais sobre como integrar aplicações SaaS com o Diretório Ativo Azure](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). É provável que encontre orientação passo a passo para configurar tanto a app como a Azure AD para criar a ligação de provisionamento.

## <a name="finding-your-apps-in-the-portal"></a>Encontrar as suas aplicações no portal

Utilize o portal Azure Ative Diretório para visualizar e gerir todas as aplicações configuradas para um único sign-on num diretório. As aplicações da Enterprise são aplicações que são implementadas e utilizadas dentro da sua organização. Siga estes passos para visualizar e gerir as suas aplicações empresariais:

1. Abra o [portal azure Ative Diretório.](https://aad.portal.azure.com)
1. Selecione **aplicações Enterprise** a partir do painel esquerdo. É apresentada uma lista de todas as aplicações configuradas, incluindo aplicações que foram adicionadas a partir da galeria.
1. Selecione qualquer aplicação para carregar o painel de recursos, onde possa ver relatórios e gerir as definições da aplicação.
1. Selecione **Provisioning** para gerir as definições de provisionamento da conta de utilizador para a aplicação selecionada.

   ![O rastreio de provisionamento para gerir as definições de provisionamento da conta do utilizador](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modos de provisionamento

O painel de **provisionamento** começa com um menu **Modo,** que mostra os modos de provisionamento suportados para uma aplicação empresarial, e permite configurá-los. As opções disponíveis incluem:

* **Automática** - Esta opção é mostrada se a Azure AD suporta o fornecimento automático de API ou o desprovisionamento das contas de utilizador para esta aplicação. Selecione este modo para exibir uma interface que ajude os administradores:

  * Configure Azure AD para ligar à API de gestão de utilizadores da aplicação
  * Criar mapeamentos de conta e fluxos de trabalho que definam como os dados da conta do utilizador devem fluir entre a AD Azure e a app
  * Gerir o serviço de provisionamento de AD Azure

* **Manual** - Esta opção é mostrada se a Azure AD não suportar o fornecimento automático de contas de utilizador a esta aplicação. Neste caso, os registos de conta de utilizador armazenados na aplicação devem ser geridos utilizando um processo externo, com base nas capacidades de gestão e provisionamento do utilizador fornecidas por essa aplicação (que pode incluir o provisionamento SAML Just-In-Time).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurar o provisionamento automático da conta do utilizador

Selecione a opção **Automática** para especificar as definições para credenciais de administração, mapeamentos, arranque e paragem e sincronização.

### <a name="admin-credentials"></a>Credenciais de administrador

Expandir **as credenciais de administrador** para introduzir as credenciais necessárias para que a AD Azure se conectem à API de gestão de utilizadores da aplicação. A entrada necessária varia consoante a aplicação. Para conhecer os tipos e requisitos credenciais para aplicações específicas, consulte o tutorial de [configuração para essa aplicação específica](user-provisioning.md).

Selecione **Test Connection** para testar as credenciais, fazendo com que o Azure AD tente ligar-se à aplicação de provisionamento da aplicação utilizando as credenciais fornecidas.

### <a name="mappings"></a>Mapeamentos

Expandir **Mapeamentos** para visualizar e editar os atributos do utilizador que fluem entre a AD Azure e a aplicação-alvo quando as contas de utilizador são aprovisionadas ou atualizadas.

Há um conjunto de mapeamentos pré-configurados entre os objetos do utilizador da AD Azure e os objetos de utilizador de cada aplicação SaaS. Algumas aplicações gerem outros tipos de objetos, como Grupos ou Contactos. Selecione um mapeamento na tabela para abrir o editor de mapeamento à direita, onde pode vê-los e personalizá-los.

![Mostra o ecrã de mapeamento do atributo](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

As personalizações suportadas incluem:

* Ativar e desativar mapeamentos para objetos específicos, como o objeto do utilizador da AD Azure ao objeto de utilizador da aplicação SaaS.
* Editar os atributos que fluem do objeto do utilizador da AD Azure para o objeto de utilizador da aplicação. Para obter mais informações sobre o mapeamento do atributo, consulte [understanding attribute mapeamento tipos](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrar as ações de provisionamento que a Azure AD executa na aplicação-alvo. Em vez de ter a AD Azure totalmente sincronizada de objetos, pode limitar as ações executadas.

  Por exemplo, apenas selecione **Update** e Azure AD apenas atualiza as contas de utilizador existentes numa aplicação, mas não cria novas. Apenas selecione **Create** e Azure apenas cria novas contas de utilizador, mas não atualiza as existentes. Esta funcionalidade permite que os administradores criem diferentes mapeamentos para criação de contas e atualizar fluxos de trabalho.

* Adicionando um novo mapeamento de atributos. **Selecione Adicionar novo mapeamento** na parte inferior do painel de mapeamento do **atributo.** Preencha o formulário **Editar Atributo** e selecione **Ok** para adicionar o novo mapeamento à lista.

### <a name="settings"></a>Definições

Pode iniciar e parar o serviço de provisionamento De AD Azure para a aplicação selecionada na área de **Definições** do ecrã **de Provisionamento.** Também pode optar por limpar o cache de fornecimento e reiniciar o serviço.

Se o provisionamento estiver a ser ativado pela primeira vez para uma aplicação, ligue o serviço alterando o Estado de **Provisionamento** para **On**. Esta alteração faz com que o serviço de provisionamento da AD Azure ecorra um ciclo inicial. Lê os utilizadores atribuídos na secção **Utilizadores e grupos,** consulta a aplicação-alvo para os mesmos e, em seguida, executa as ações de provisionamento definidas na secção de **Mapeamento** sinuoso Da AD Azure. Durante este processo, as lojas de serviços de fornecimento cachedados dados sobre quais as contas de utilizador que está a gerir, pelo que as contas não geridas dentro das aplicações-alvo que nunca estiveram no âmbito da atribuição não são afetadas por operações de desprovisionamento. Após o ciclo inicial, o serviço de fornecimento sincroniza automaticamente os objetos do utilizador e do grupo num intervalo de quarenta minutos.

Alterar o Estado de **Provisionamento** para **Desligar** para interromper o serviço de provisionamento. Neste estado, o Azure não cria, atualiza ou remove quaisquer objetos de utilizador ou grupo na aplicação. Mude o estado de volta para **On** e o serviço retoma onde ficou parado.

O estado atual claro e a **sincronização de reinício** desencadeiam um ciclo inicial. O serviço irá então avaliar todos os utilizadores do sistema de origem novamente e determinar se estão em aberto para o fornecimento. Isto pode ser útil quando a sua aplicação está em quarentena ou precisa de fazer uma alteração nos mapeamentos do seu atributo. Note que o ciclo inicial demora mais tempo a ser concluído do que o ciclo incremental típico devido ao número de objetos que precisam de ser avaliados. Pode saber mais sobre o desempenho dos ciclos iniciais e incrementais [aqui](application-provisioning-when-will-provisioning-finish-specific-user.md). 
