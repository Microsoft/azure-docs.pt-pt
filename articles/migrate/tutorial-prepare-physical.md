---
title: Preparar servidores físicos para avaliação/migração com a Azure Migrate
description: Saiba como se preparar para a avaliação/migração de servidores físicos com a Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: ed648458416bacb091212bb569a27ecdf13fe8ee
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771279"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Preparar para avaliação e migração de servidores físicos para Azure

Este artigo descreve como se preparar para a avaliação dos servidores físicos no local com [a Azure Migrate](migrate-services-overview.md).

[A Azure Migrate](migrate-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar aplicações, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas de fornecedores de software independentes de terceiros (ISV). 

Este tutorial é o primeiro de uma série que mostra como avaliar servidores físicos com Azure Migrate. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Prepara o Azure. Configure as permissões para a sua conta Azure e recursos para trabalhar com a Azure Migrate.
> * Prepare os servidores físicos no local para avaliação do servidor.


> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os "How-tos" para avaliação dos servidores físicos.


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure-for-server-assessment"></a>Prepare o Azure para avaliação do servidor

Instale o Azure para trabalhar com a Azure Migrate. 

**Tarefa** | **Detalhes** 
--- | --- 
**Criar um projeto Azure Migrate** | A sua conta Azure necessita de permissões de Colaborador ou Proprietário para criar um projeto. 
**Registar fornecedores de recursos (apenas avaliação)** | A Azure Migrate utiliza um aparelho Azure Migrate leve para descobrir e avaliar máquinas com Azure Migrate:Avaliação do servidor.<br/><br/> Durante o registo do aparelho, os fornecedores de recursos estão registados com a assinatura escolhida no aparelho. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Para registar os fornecedores de recursos, precisa de uma função de Contribuinte ou Proprietário na subscrição.
**Criar app AD Azure (apenas avaliação)** | Ao registar o aparelho, a Azure Migrate cria uma aplicação Azure Ative Directory (Azure AD) que é utilizada para a comunicação entre os agentes que estão a trabalhar no aparelho com os respetivos serviços em funcionamento no Azure. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Precisa de permissões para criar aplicações AD Azure (disponíveis no Desenvolvedor de Aplicações).


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto 

Verifique se tem permissões para criar um projeto Azure Migrate.

1. No portal Azure, abra a subscrição e selecione **Access control (IAM)**.
2. No **Acesso ao Cheque,** encontre a conta relevante e clique nela para ver permissões.
3. Deve ter permissões **de Colaborador** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não é o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.


### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registar o aparelho 

Pode atribuir permissões para a Azure Migrate criar a aplicação AZure AD durante o registo do aparelho, utilizando um dos seguintes métodos:

- Um administrador inquilino/global pode conceder permissões aos utilizadores do arrendatário, para criar e registar aplicações AD Azure.
- Um administrador inquilino/global pode atribuir a função de Desenvolvedor de Aplicações (que tem as permissões) à conta.

> [!NOTE]
> - A aplicação não possui quaisquer outras permissões de acesso na subscrição que não as descritas acima.
> - Só precisa destas permissões quando registar um novo aparelho. Pode retirar as permissões após a instalação do aparelho.


#### <a name="grant-account-permissions"></a>Permissões de conta de subvenção

O arrendatário/administrador global pode conceder permissões da seguinte forma:

1. Em Azure AD, o inquilino/administrador global deve navegar para as Definições de Utilizador de Utilizadores **de Diretório Ativo Azure**  >  **Users**  >  **User Settings**.
2. O administrador deve definir **as inscrições da App** para **Sim.**

    ![Permissões AD AZure](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Esta é uma definição padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Atribuir função de Desenvolvedor de Aplicações

O administrador inquilino/global pode atribuir o papel de Desenvolvedor de Aplicações a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-azure-for-physical-server-migration"></a>Prepare o Azure para a migração de servidores físicos

Prepare o Azure para migrar servidores físicos, utilizando a migração do servidor.

**Tarefa** | **Detalhes**
--- | ---
**Criar um projeto Azure Migrate** | A sua conta Azure precisa de permissões de Contribuidor ou Proprietário para criar um projeto.
**Verifique permissões para a sua conta Azure** | A sua conta Azure necessita de permissões para criar um VM e escrever para um disco gerido pelo Azure.
**Criar uma rede Azure** | Crie uma rede em Azure.


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal Azure, abra a subscrição e selecione **Access control (IAM)**.
2. No **Acesso ao Cheque,** encontre a conta relevante e clique nela para ver permissões.
3. Deve ter permissões **de Colaborador** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não é o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.


### <a name="assign-azure-account-permissions"></a>Atribuir permissões de conta Azure

Atribua a função contribuidora da máquina virtual à conta Azure. Isto fornece permissões para:
  - Criar uma VM no grupo de recursos selecionado.
  - Criar uma VM na rede virtual selecionada.
  - Escreva para um disco gerido pelo Azure. 

### <a name="create-an-azure-network"></a>Criar uma rede Azure

[Crie](../virtual-network/manage-virtual-network.md#create-a-virtual-network) uma rede virtual Azure (VNet). Quando se replica para Azure, os VMs Azure são criados e unidos ao Azure VNet que especifica quando configura a migração.


## <a name="prepare-for-physical-server-assessment"></a>Preparar para avaliação física do servidor

Para se preparar para a avaliação do servidor físico, é necessário verificar as definições do servidor físico e verificar as definições para a implementação do aparelho:

### <a name="verify-physical-server-settings"></a>Verificar as definições do servidor físico

1. Verifique os [requisitos do servidor físico](migrate-support-matrix-physical.md#physical-server-requirements) para a avaliação do servidor.
2. Certifique-se de que as [portas necessárias](migrate-support-matrix-physical.md#port-access) estão abertas nos servidores físicos.


### <a name="verify-appliance-settings"></a>Verifique as definições do aparelho

Antes de configurar o aparelho Azure Migrate e iniciar a avaliação no tutor seguinte, prepare-se para a colocação do aparelho.

1. [Verifique os](migrate-appliance.md#appliance---physical) requisitos do aparelho para servidores físicos.
2. Reveja os URLs Azure que o aparelho precisa de aceder nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
3. [Reveja](migrate-appliance.md#collected-data---vmware) o que o aparelho recolherá durante a descoberta e avaliação.
4. [Note](migrate-support-matrix-physical.md#port-access) os requisitos de acesso à porta física do servidor.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Criar uma conta para a descoberta do servidor físico

A Azure Migrate precisa de permissões para descobrir servidores no local.

- **Janelas:** Tem de ser um administrador de domínio ou administrador local em todos os servidores windows que pretende descobrir. A conta de utilizador deve ser adicionada a estes grupos: Utilizadores de Gestão Remota, Utilizadores de Monitores de Desempenho e Utilizadores de Registos de Desempenho.
- **Linux:** Precisa de uma conta raiz nos servidores Linux que pretende descobrir.

## <a name="prepare-for-physical-server-migration"></a>Preparar para a migração física do servidor

Reveja os requisitos para a migração de servidores físicos.

> [!NOTE]
> Ao migrar máquinas físicas, a Azure Migrate:Server Migration utiliza a mesma arquitetura de replicação que a recuperação de desastres baseada em agentes no serviço de recuperação do local de Azure, e alguns componentes partilham a mesma base de código. Algum conteúdo pode ligar-se à documentação de Recuperação do Site.

1. [Reveja os](migrate-support-matrix-physical-migration.md#physical-server-requirements) requisitos do servidor físico para a migração.
2. Azure Migrate:Server Migration usa um servidor de replicação para migração de servidores físicos:
    - [Reveja](migrate-replication-appliance.md#appliance-requirements) os requisitos de implantação do aparelho de replicação e as [opções](migrate-replication-appliance.md#mysql-installation) de instalação do MySQL no aparelho.
    - Reveja os [URLs Azure necessários](migrate-appliance.md#url-access) para que o aparelho de replicação aceda a nuvens públicas e governamentais.
    - Reveja os requisitos de acesso [porta] (migração-replicação-aparelho.md#porta-acesso) para o aparelho de replicação.
3. São necessárias algumas alterações nos VM antes de as migrar para Azure.
    - É importante fazer estas mudanças antes de começar a migração. Se migrar o VM antes de fazer a alteração, o VM pode não arrancar em Azure.
    - Reveja as alterações do [Windows](prepare-for-migration.md#windows-machines) e [do Linux](prepare-for-migration.md#linux-machines) que precisa de fazer.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Confise as permissões da conta Azure.
> * Servidores físicos preparados para avaliação.

Continue até ao próximo tutorial para criar um projeto Azure Migrate, e avaliar servidores físicos para migração para Azure

> [!div class="nextstepaction"]
> [Avaliar servidores físicos](./tutorial-assess-physical.md)
