---
title: Preparar servidores físicos para avaliação/migração com a Migração Azure
description: Saiba como se preparar para avaliação/migração de servidores físicos com o Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: b7bde5df943a35bfcf08ace3b454a26dae8c1d89
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901418"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Preparar para avaliação e migração de servidores físicos para OTE

Este artigo descreve como se preparar para a avaliação de servidores físicos no local com [o Azure Migrate](migrate-services-overview.md).

[A Azure Migrate](migrate-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar apps, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV). 

Este tutorial é o primeiro de uma série que mostra como avaliar servidores físicos com o Azure Migrate. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare Azure. Estabeleça permissões para a sua conta Azure e recursos para trabalhar com a Azure Migrate.
> * Prepare os servidores físicos no local para avaliação do servidor.


> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa rapidamente configurar uma prova de conceito. Os tutoriais usam opções padrão sempre que possível, e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, reveja o How-tos para a avaliação dos servidores físicos.


Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure-for-server-assessment"></a>Prepare o Azure para avaliação do servidor

Instale o Azure para trabalhar com a Azure Migrate. 

**Tarefa** | **Detalhes** 
--- | --- 
**Criar um projeto Azure Migrate** | A sua conta Azure necessita de permissões do Colaborador ou proprietário para criar um projeto. 
**Registar fornecedores de recursos (apenas avaliação)** | A Azure Migrate utiliza um aparelho azure migratório leve para descobrir e avaliar máquinas com a Avaliação do Servidor Azure Migrate.Server.<br/><br/> Durante o registo do aparelho, os fornecedores de recursos são registados com a assinatura escolhida no aparelho. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Para registar os fornecedores de recursos, necessita de uma função de Colaborador ou Proprietário na subscrição.
**Criar app Azure AD (apenas avaliação)** | Ao registar o aparelho, a Azure Migrate cria uma aplicação Azure Ative Directory (Azure AD) que é utilizada para a comunicação entre os agentes que estão a trabalhar no aparelho com os respetivos serviços em funcionamento no Azure. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Precisa de permissões para criar aplicações Azure AD (disponíveis no Desenvolvedor de Aplicações).


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto 

Verifique se tem permissões para criar um projeto Azure Migrate.

1. No portal Azure, abra a subscrição e selecione controlo de **acesso (IAM)**.
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.
3. Deve ter permissões de **Contribuinte** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.


### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registar o aparelho 

Pode atribuir permissões para a Azure Migrate criar a aplicação Azure AD durante o registo do aparelho, utilizando um dos seguintes métodos:

- Um inquilino/administrador global pode conceder permissões aos utilizadores do inquilino, para criar e registar aplicações da Azure AD.
- Um inquilino/administrador global pode atribuir a função de Desenvolvedor de Aplicações (que tem as permissões) à conta.

> [!NOTE]
> - A aplicação não tem quaisquer outras permissões de acesso na subscrição que não as descritas acima.
> - Só precisa destas permissões quando regista um novo aparelho. Pode retirar as permissões depois de o aparelho ser montado.


#### <a name="grant-account-permissions"></a>Concessão de permissões de conta

O inquilino/administrador global pode conceder permissões da seguinte forma:

1. Em Azure AD, o inquilino/administrador global deve navegar para**as Definições**de**Utilizadores** > de **Diretório** > Ativo Azure .
2. O administrador deve definir **os registos** da App para **Sim**.

    ![Permissões da AD Azure](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Esta é uma definição padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir papel de desenvolvedor de aplicações

O inquilino/administrador global pode atribuir a função de Desenvolvedor de Aplicações a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-azure-for-physical-server-migration"></a>Prepare o Azure para a migração de servidores físicos

Prepare o Azure para migrar servidores físicos, utilizando a Migração do Servidor.

**Tarefa** | **Detalhes**
--- | ---
**Criar um projeto Azure Migrate** | A sua conta Azure necessita de permissões do Contributer ou do Proprietário para criar um projeto.
**Verifique as permissões da sua conta Azure** | A sua conta Azure precisa de permissões para criar um VM e escrever para um disco gerido pelo Azure.
**Criar uma rede Azure** | Criar uma rede em Azure.


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal Azure, abra a subscrição e selecione controlo de **acesso (IAM)**.
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.
3. Deve ter permissões de **Contribuinte** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.


### <a name="assign-azure-account-permissions"></a>Atribuir permissões de conta Azure

Atribuir a função de Colaborador de Máquina Virtual à conta Azure. Isto fornece permissões para:
  - Criar uma VM no grupo de recursos selecionado.
  - Criar uma VM na rede virtual selecionada.
  - Escreva para um disco gerido pelo Azure. 

### <a name="create-an-azure-network"></a>Criar uma rede Azure

[Criar](../virtual-network/manage-virtual-network.md#create-a-virtual-network) uma rede virtual Azure (VNet). Quando se replica para o Azure, os VMs Azure são criados e unidos ao Azure VNet que especifica quando configura a migração.


## <a name="prepare-for-physical-server-assessment"></a>Preparar para avaliação do servidor físico

Para se preparar para a avaliação física do servidor, tem de verificar as definições físicas do servidor e verificar as definições para a implementação do aparelho:

### <a name="verify-physical-server-settings"></a>Verificar as definições físicas do servidor

1. Verifique [os requisitos físicos do servidor](migrate-support-matrix-physical.md#physical-server-requirements) para a avaliação do servidor.
2. Certifique-se de que as [portas necessárias](migrate-support-matrix-physical.md#port-access) estão abertas nos servidores físicos.


### <a name="verify-appliance-settings"></a>Verifique as definições do aparelho

Antes de configurar o aparelho Azure Migrate e iniciar a avaliação no próximo tutorial, prepare-se para a colocação do aparelho.

1. [Verifique os](migrate-appliance.md#appliance---physical) requisitos do aparelho para servidores físicos.
2. Reveja os URLs Azure que o aparelho precisa de aceder nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
3. [Reveja](migrate-appliance.md#collected-data---vmware) que o aparelho irá recolher durante a descoberta e avaliação.
4. [Note que](migrate-support-matrix-physical.md#port-access) o acesso à porta requer a avaliação física do servidor.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Criar uma conta para a descoberta do servidor físico

A Azure Migrate precisa de permissões para descobrir servidores no local.

- **Janelas:** Configurar uma conta de utilizador local em todos os servidores do Windows que pretende incluir na descoberta. A conta de utilizador precisa de ser adicionada aos seguintes grupos: - Utilizadores de Gestão Remota - Utilizadores de Monitor de Desempenho - Utilizadores de Registo de Desempenho
- **Linux:** Precisa de uma conta de raiz nos servidores Linux que pretende descobrir.

## <a name="prepare-for-physical-server-migration"></a>Preparar para a migração de servidores físicos

Reveja os requisitos para a migração de servidores físicos.

> [!NOTE]
> Ao migrar máquinas físicas, o Azure Migrate:Server Migration usa a mesma arquitetura de replicação que a recuperação de desastres baseada em agentes no serviço de Recuperação de Sítios Azure, e alguns componentes partilham a mesma base de código. Alguns conteúdos podem ligar-se à documentação de Recuperação do Site.

- [Reveja](migrate-support-matrix-physical-migration.md#physical-server-requirements) os requisitos do servidor físico para a migração.
- Azure Migrate:Server Migration usa um servidor de replicação para migração física do servidor:
    - [Reveja](migrate-replication-appliance.md#appliance-requirements) os requisitos de implantação do aparelho de replicação e as [opções](migrate-replication-appliance.md#mysql-installation) para instalar o MySQL no aparelho.
    - Reveja os [URLs Azure necessários](migrate-appliance.md#url-access) para que o aparelho de replicação aceda às nuvens públicas e governamentais.
    - Reveja os requisitos de acesso [porta] (migrate-replication-appliance.md#port-access) para o aparelho de replicação.




## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configurar permissões de conta Azure.
> * Servidores físicos preparados para avaliação.

Continue para o próximo tutorial para criar um projeto Azure Migrate, e avaliar servidores físicos para migração para Azure

> [!div class="nextstepaction"]
> [Avaliar servidores físicos](./tutorial-assess-physical.md)
