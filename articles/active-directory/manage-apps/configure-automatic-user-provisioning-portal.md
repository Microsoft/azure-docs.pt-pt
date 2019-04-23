---
title: Gestão de aplicações empresariais no Azure Active Directory de aprovisionamento de utilizadores | Documentos da Microsoft
description: Saiba como gerir o aprovisionamento da conta de utilizador para aplicações empresariais com o Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: d03ca64f3f3d2f034433f2aaa49f6babb7f9e5b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790537"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gerir a conta de utilizador de aprovisionamento de aplicações empresariais no portal do Azure

Este artigo descreve como utilizar o [portal do Azure](https://portal.azure.com) para gerir a conta de utilizador automático provisionamento e desprovisionamento para aplicações que o suportam. Para saber mais sobre o aprovisionamento de contas de utilizadores automático e como ele funciona, veja [automatizar o aprovisionamento do utilizador e cancelar o aprovisionamento a aplicações SaaS com o Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Encontrar as suas aplicações no portal

Utilize o portal do Azure Active Directory para ver e gerir todos os aplicativos que estão configurados para início de sessão único num diretório. Aplicações empresariais são as aplicações que são implementadas e utilizadas na sua organização. Siga estes passos para ver e gerir as suas aplicações empresariais:

1. Abra o [portal do Azure Active Directory](https://aad.portal.azure.com).

1. Selecione **aplicações empresariais** no painel à esquerda. É apresentada uma lista de todas as aplicações configuradas, incluindo aplicações que foram adicionadas a partir da galeria.

1. Selecione uma aplicação para carregar o seu painel de recursos, onde pode ver relatórios e gerir as definições da aplicação.

1. Selecione **aprovisionamento** para gerir as definições da aplicação selecionada de configuração de contas de utilizador.

   ![Painel de recursos de aplicação](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modos de aprovisionamento

O **aprovisionamento** painel começa com um **modo** menu, que mostra os modos de aprovisionamento suportados para uma aplicação empresarial e permite-lhe configurá-las. As opções disponíveis incluem:

* **Automática** -esta opção é apresentada se o Azure AD suporta baseada em API aprovisionamento automático ou anular o aprovisionamento de contas de utilizador para esta aplicação. Selecione o modo para apresentar uma interface que ajuda os administradores:

  * Configurar o Azure AD para ligar à API de gestão de utilizador da aplicação
  * Criar mapeamentos de conta e fluxos de trabalho que definem a forma como os dados de conta de utilizador devem fluir entre o Azure AD e a aplicação
  * Gerir o Azure AD que o serviço de aprovisionamento

* **Manual** -esta opção é apresentada se o Azure AD não suporta o aprovisionamento automático de contas de utilizador para esta aplicação. Neste caso, os registos armazenados no aplicativo de conta de utilizador tem de ser gerida utilizando um processo externo, com base nas capacidades de gestão e aprovisionamento de utilizador fornecidas por essa aplicação (o que pode incluir o aprovisionamento de SAML ativaram).

## <a name="configuring-automatic-user-account-provisioning"></a>Configurar o aprovisionamento de conta de utilizador automático

Selecione o **automática** opção para especificar as definições de credenciais de administrador, mapeamentos, iniciar e parar e sincronização.

### <a name="admin-credentials"></a>Credenciais de Administrador

Expanda **credenciais de administrador** para introduzir as credenciais necessárias para o Azure AD ligar à API de gestão de usuário do aplicativo. A entrada necessária varia consoante a aplicação. Para saber mais sobre os requisitos para determinados aplicativos e tipos de credenciais, consulte a [tutorial de configuração para aquele aplicativo específico](user-provisioning.md).

Selecione **Testar ligação** para testar as credenciais ao ter o Azure AD tenta ligar-se para a aplicação do aprovisionamento de aplicação com as credenciais fornecidas.

### <a name="mappings"></a>Mapeamentos

Expanda **mapeamentos** para ver e editar os atributos de utilizador que fluem entre o Azure AD e o aplicativo de destino quando as contas de utilizador são aprovisionadas ou atualizadas.

Existe um conjunto pré-configuradas de mapeamentos entre objetos de utilizador do Azure AD e objetos de utilizador de cada aplicação de SaaS. Algumas aplicações, gerir outros tipos de objetos, tais como grupos ou contactos. Selecione um mapeamento da tabela para abrir o editor de mapeamento para a direita, onde pode ver e personalizá-las.

![Painel de recursos de aplicação](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Personalizações suportadas incluem:

* Ativar e desativar mapeamentos para objetos específicos, como o objeto de utilizador do Azure AD para o objeto de utilizador da aplicação SaaS.
* Editar os atributos que o fluam a partir do objeto de utilizador do Azure AD para o objeto de utilizador da aplicação. Para obter mais informações sobre o mapeamento do atributo, consulte [Noções básicas sobre os tipos de mapeamento de atributos](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrar as ações de aprovisionamento do Azure AD é executado na aplicação de destino. Em vez de precisar do Azure AD totalmente sincronizar objetos, pode limitar as ações a executar. 

  Por exemplo, selecione apenas **atualização** e do Azure AD apenas as atualizações de utilizador existente num aplicativo de contas, mas não cria novos. Apenas a determinados **criar** e apenas o Azure cria novas contas de utilizador mas não atualiza os existentes. Esta funcionalidade permite aos administradores criar mapeamentos diferentes para a criação de conta e atualizar os fluxos de trabalho.

* Adicionar um novo mapeamento do atributo. Selecione **adicionar novo mapeamento** na parte inferior da **mapeamento do atributo** painel. Preencha os **Editar atributo** formam e selecione **Ok** para adicionar o mapeamento de novo à lista. 

### <a name="settings"></a>Definições

Pode iniciar e parar o Azure AD para a aplicação selecionada do serviço de aprovisionamento a **configurações** área da **aprovisionamento** ecrã. Também é possível limpar a cache de aprovisionamento e reiniciar o serviço.

Se o aprovisionamento está a ser ativado pela primeira vez para uma aplicação, ative o serviço, alterando a **estado de aprovisionamento** ao **no**. Esta alteração faz com que o Azure AD do serviço de aprovisionamento executar uma sincronização inicial. Ele lê os utilizadores atribuídos a **utilizadores e grupos** secção, consulta o aplicativo de destino para os mesmos e, em seguida, executa as ações de aprovisionamento definidas no Azure AD **mapeamentos** secção. Durante este processo, o serviço de aprovisionamento armazena em cache dados sobre as contas de utilizador que está a gerir, pelo que não gerida contas dentro as aplicações de destino que foram nunca no âmbito de atribuição não são afetadas pelo Desconfiguração de operações. Após a sincronização inicial, o serviço de aprovisionamento sincroniza automaticamente os objetos de utilizador e grupo num intervalo de dez minutos.

Alteração da **estado de aprovisionamento** para **desativar** para colocar em pausa o serviço de aprovisionamento. Neste estado, Azure não criar, atualizar ou remover qualquer utilizador ou os objetos de grupo na aplicação. De volta para o estado de alteração **no** e o serviço escolhe onde parou.

Selecione o **Limpar estado atual e reiniciar a sincronização** caixa de verificação e selecione **guardar** para:

* Parar o serviço de aprovisionamento
* Os dados em cache sobre as contas do Azure AD está a gerir a cópia de segurança
* Reinicie os serviços e execute novamente a sincronização inicial

Esta opção permite aos administradores de iniciar o processo de implantação aprovisionamento novamente.

### <a name="synchronization-details"></a>Detalhes da Sincronização

Esta seção fornece detalhes adicionais sobre o funcionamento do serviço de aprovisionamento, incluindo os tempos de primeiros e últimos que serviço de aprovisionamento executou o aplicativo e o número de utilizadores e objetos de grupo que gere.

É fornecida uma ligação para o **relatório de atividade de aprovisionamento**, que fornece um registo de todos os utilizadores e grupos criado, atualizado e removido entre o Azure AD e o aplicativo de destino. Também é fornecida uma ligação para o **relatório de erros de aprovisionamento**, que fornece mais mensagens de erro do utilizador e nos objetos de grupo que falharam a ser lido, criado, atualizado ou removido.
