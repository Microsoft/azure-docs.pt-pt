---
title: Preparar servidores físicos para avaliação/migração com as migrações para Azure
description: Saiba como se preparar para a avaliação/migração de servidores físicos com as migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 42eb603be0152b9e8cfb36d02e8f0602c40afe54
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031208"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Preparar para avaliação e migração de servidores físicos para o Azure

Este artigo descreve como se preparar para a avaliação de servidores físicos no local com [o Azure Migrate](migrate-services-overview.md).

[A Azure Migrate](migrate-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar apps, infraestruturas e cargas de trabalho para o Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros. 

Este tutorial é o primeiro de uma série que mostra como avaliar servidores físicos com as migrações para Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare o Azure. Configure permissões para sua conta do Azure e recursos para trabalhar com as migrações para Azure.
> * Preparar servidores físicos locais para avaliação do servidor.


> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, examine a avaliação de como o-tos para servidores físicos.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

### <a name="azure-permissions"></a>Permissões do Azure

Você precisa configurar permissões para a implantação de migrações para Azure.

**Tarefa** | **Permissões**
--- | ---
**Criar um projeto Azure Migrate** | Sua conta do Azure precisa de permissões para criar um projeto.
**Registe o aparelho Azure Migrate** | A Azure Migrate utiliza um aparelho ligeiro de migração Azure para descobrir e avaliar servidores físicos com avaliação do servidor de migração Azure. Este aparelho descobre servidores e envia os seus metadados e dados de desempenho para o Azure Migrate.<br/><br/>Durante o registo do aparelho, os seguintes Fornecedores de Recursos estão registados com a subscrição escolhida no aparelho: Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault. O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. Para registar os fornecedores de recursos, necessita de uma função de Colaborador ou Proprietário na subscrição.<br/><br/> Como parte do embarque, a Azure Migrate cria uma aplicação Azure Ative Directory (Azure AD):<br/> A aplicação AAD é utilizada para comunicação (autenticação e autorização) entre os agentes que estão a trabalhar no aparelho com os respetivos serviços em funcionamento no Azure. Esta aplicação não tem privilégios para fazer chamadas ARM ou acesso RBAC em qualquer recurso.



### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

Verifique se você tem permissões para criar um projeto de migrações para Azure.

1. No portal Azure, abra a subscrição e selecione controlo de **acesso (IAM)** .
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.
3. Deve ter permissões de **Contribuinte** ou **Proprietário.**
    - Se você acabou de criar uma conta gratuita do Azure, você é o proprietário da sua assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.


### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registrar o dispositivo

Pode atribuir permissões para a Azure Migrate criar a aplicação Azure AD durante o registo do aparelho, utilizando um dos seguintes métodos:

- Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
- Um locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo (que tem as permissões) à conta.

> [!NOTE]
> - A aplicação não tem quaisquer outras permissões de acesso na subscrição que não as descritas acima.
> - Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo for configurado.


#### <a name="grant-account-permissions"></a>Permissões de conta de concessão

O locatário/administrador global pode conceder permissões da seguinte maneira:

1. Em Azure AD, o inquilino/administrador global deve navegar para **o Diretório Ativo do Azure** > **utilizadores** > **Definições**de utilizador .
2. O administrador deve definir **os registos** da App para **Sim**.

    ![Permissões do Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir função de desenvolvedor de aplicativo

O locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Preparar a avaliação do servidor físico

Para se preparar para a avaliação do servidor físico, você precisa verificar as configurações do servidor físico e verificar as configurações da implantação do dispositivo:

### <a name="verify-physical-server-settings"></a>Verificar as configurações do servidor físico

1. Verifique [os requisitos físicos do servidor](migrate-support-matrix-physical.md#physical-server-requirements) para a avaliação do servidor.
2. Certifique-se de que as [portas necessárias](migrate-support-matrix-physical.md#port-access) estão abertas nos servidores físicos.


### <a name="verify-appliance-settings"></a>Verificar as configurações do dispositivo

Antes de configurar o dispositivo migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique os](migrate-appliance.md#appliance---physical) requisitos do aparelho para servidores físicos.
2. [Reveja](migrate-appliance.md#url-access) os URLs Azure a que o aparelho terá de aceder.
3. [Reveja](migrate-appliance.md#collected-data---vmware) se o aparelho irá recolher durante a descoberta e avaliação.
4. [Note que](migrate-support-matrix-physical.md#port-access) o acesso à porta requer a avaliação física do servidor.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Configurar uma conta para descoberta de servidor físico

As migrações para Azure precisam de permissões para descobrir servidores locais.

- **Janelas:** Configurar uma conta de utilizador local em todos os servidores do Windows que pretende incluir na descoberta. A conta de utilizador precisa de ser adicionada aos seguintes grupos: - Utilizadores de Gestão Remota - Utilizadores de Monitor de Desempenho - Utilizadores de Registo de Desempenho
- **Linux:** Precisa de uma conta de raiz nos servidores Linux que pretende descobrir.

## <a name="prepare-for-physical-server-migration"></a>Preparar para migração de servidor físico

Examine os requisitos para a migração de servidores físicos.

- [Reveja](migrate-support-matrix-physical-migration.md#physical-server-requirements) os requisitos do servidor físico para a migração.
- Migrações para Azure: a migração de servidor usa um servidor de replicação para migração de servidor físico:
    - [Reveja](migrate-replication-appliance.md#appliance-requirements) os requisitos de implantação do aparelho de replicação e as [opções](migrate-replication-appliance.md#mysql-installation) para instalar o MySQL no aparelho.
    - Reveja os requisitos de acesso ao [URL](migrate-replication-appliance.md#url-access) e [porta] (migração-replicação-aparelho.md#port-access) para o aparelho de replicação.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configurar permissões de conta do Azure.
> * Servidores físicos preparados para avaliação.

Continue no próximo tutorial para criar um projeto de migrações para Azure e avaliar servidores físicos para migração para o Azure

> [!div class="nextstepaction"]
> [Avaliar servidores físicos](./tutorial-assess-physical.md)
