---
title: Preparar VMware VMs para avaliação e migração para o Azure com o Azure Migrate | Documentos da Microsoft
description: Descreve como preparar a avaliação e migração de VMs de VMware no local para o Azure com o Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 10f559295ff0598dea26fb30b089f020e2985889
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840346"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparar VMware VMs para avaliação e migração para o Azure

Este artigo descreve como preparar a avaliação e migração de VMs de VMware no local para o Azure, utilizando [do Azure Migrate](migrate-services-overview.md).

[O Azure Migrate](migrate-overview.md) fornece um hub de ferramentas que ajudam a detetar, avaliar e migrar cargas de trabalho, aplicações e infraestrutura para o Microsoft Azure. O hub inclui as ferramentas do Azure Migrate e ofertas de (ISV fornecedor) de terceiros independentes de software. 


Este tutorial é a primeira de uma série que mostra como avaliar e migrar VMs do VMware. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare o Azure. Configure permissões para a sua conta do Azure e recursos para trabalhar com o Azure Migrate.
> * Prepare servidores VMware no local e VMs para avaliação da VM.
> * Prepare VMs e servidores de VMware no local para migração de VM.

> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que pode configurar rapidamente a uma prova de conceito. Tutoriais utilizam as opções predefinidas, sempre que possível e não mostram todas as configurações possíveis e caminhos. Para obter instruções detalhadas, consulte os guias de avaliação de VMware e de migração.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

Tem estas permissões do Azure:

- Sua conta do Azure necessita de permissões para criar um projeto do Azure Migrate para avaliação e migração. 
- Para avaliação e sem agente migração das VMs de VMware, o Azure Migrate executa uma aplicação simples que Deteta as VMs e envia dados de metadados e o desempenho da VM para o Azure Migrate. No Azure, precisa de permissões para registar a aplicação do Azure Migrate.
- Para migrar VMs VMware com o Azure Migrate migração do servidor, Azure Migrate cria um cofre de chaves no grupo de recursos, para gerir as chaves de acesso à conta de armazenamento de replicação na sua subscrição. Para criar o cofre, tem as permissões de atribuição de função no grupo de recursos no qual reside o projeto do Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

1. No portal do Azure, abra a subscrição e selecione **controlo de acesso (IAM)** .
2. Na **verificar acesso**, localizar a conta relevante e clique nele para ver as permissões.
3. Deve ter **contribuinte** ou **proprietário** permissões.
    - Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir a função.

### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registar a aplicação da

Se estiver a implementar a aplicação do Azure Migrate para avaliar ou executar uma migração sem agente de VMs, é preciso registrá-la.

- Durante o registo de aplicação, do Azure Migrate cria duas aplicações do Azure Active Directory (Azure AD) que identificam a aplicação
    - A primeira aplicação comunica com pontos finais de serviço do Azure Migrate.
    - A segunda aplicação acede a um Azure Key Vault criado durante o registo para armazenar do Azure AD da aplicação e as informações de definições de configuração.
- Pode atribuir permissões para o Azure Migrate para criar estas aplicações do Azure AD através de um dos seguintes métodos:
    - Um administrador global/inquilino pode conceder permissões aos utilizadores no inquilino, para criar e registar aplicações do Azure AD.
    - Um administrador global/inquilino pode atribuir a função de desenvolvedor do aplicativo (que tem as permissões) para a conta.

É importante observar que:

- As aplicações não tem quaisquer outras permissões de acesso na subscrição que não estão descritas acima.
- Só tem estas permissões ao registar um novo dispositivo. Pode remover as permissões depois de configurar a aplicação. 


#### <a name="grant-account-permissions"></a>Conceder permissões de conta

O administrador de inquilino/global pode conceder permissões da seguinte forma

1. No Azure AD, o administrador global/inquilino deve acessar **do Azure Active Directory** > **utilizadores** > **as definições de utilizador**.
2. O administrador deve definir **registos de aplicações** ao **Sim**.

    ![Permissões do Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Esta é uma definição de predefinição que não seja confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Atribuir a função de programador da aplicação 

O administrador global/inquilino pode atribuir a função de programador da aplicação a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Atribuir permissões de atribuição de função

Atribua permissões de atribuição de função no grupo de recursos em que o projeto do Azure Migrate reside, da seguinte forma:

1. No grupo de recursos no portal do Azure, selecione **controlo de acesso (IAM)** .
2. Na **verificar acesso**, localizar a conta relevante e clique nele para ver as permissões.

    - Ao executar a avaliação de servidor, **contribuinte** permissões são suficientes.
    - Para executar a migração do servidor sem agente, deve ter **proprietário** (ou **contribuinte** e **administrador de acesso de utilizador**) permissões.

3. Se não tiver as permissões necessárias, solicitá-los do proprietário do grupo de recursos. 



## <a name="prepare-for-vmware-vm-assessment"></a>Preparar a avaliação da VM de VMware

Para preparar a avaliação da VM de VMware, terá de verificar o anfitrião Hyper-V e as definições da VM e verifique se as definições para a implementação da aplicação.

### <a name="verify-vmware-settings"></a>Verifique as definições de VMware

1. [Certifique-se de](migrate-support-matrix-vmware.md#assessment-vmware-server-requirements) requisitos do servidor VMware avaliação da VM.
2. [Certifique-se de](migrate-support-matrix-vmware.md#assessment-port-requirements) que as portas necessárias estão abertas nos servidores vCenter.


### <a name="set-up-an-account-for-assessment"></a>Configurar uma conta de avaliação

O Azure Migrate precisa de aceder ao vCenter Server para detetar VMs para avaliação e migração sem agente. Para avaliação, precisa de uma conta só de leitura para o vCenter Server.

Se estiver a utilizar um firewall.proxy baseado em URL, permitir o acesso ao necessários [URLs do Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).

Certifique-se de que o proxy resolve quaisquer registos CNAME receberam ao pesquisar os URLs.


### <a name="verify-appliance-settings-for-assessment"></a>Verifique as definições de aplicação para avaliação

Antes de configurar o Azure Migrate da aplicação e a avaliação de início no tutorial seguinte, prepare-se para a implementação da aplicação.

1. Certifique-se [Verifique se](migrate-support-matrix-vmware.md#assessment-appliance-requirements) os requisitos para configurar a aplicação do Azure Migrate para VMware.
2. [Revisão](migrate-support-matrix-vmware.md#assessment-url-access-requirements) os URLs do Azure que a aplicação precisa de acesso.
3. Reveja os dados que a aplicação irá recolher durante a deteção e avaliação.
4. [Tenha em atenção](migrate-support-matrix-vmware.md#assessment-port-requirements) porta requisitos de acesso da aplicação.
5. Implementar a aplicação do Azure Migrate como uma VM de VMware com um ficheiro OVA. No vCenter Server, certifique-se de que a sua conta tem permissões para criar uma VM com um ficheiro OVA.


## <a name="prepare-for-agentless-vmware-migration"></a>Preparar a migração do VMware sem agente

Reveja os requisitos para a migração sem agente de VMs de VMware.

1. [Revisão](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) os requisitos de servidor do VMware para migração sem agente.
2. Configurar uma conta para aceder ao vCenter Server com o [permissões obrigatórias](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) para a migração sem agente.
3. [Tenha em atenção](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) os requisitos para VMs de VMware que pretende migrar para o Azure utilizando a migração sem agente.
4. [Revisão](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) requisitos da aplicação para a migração sem agente.]
5. Aplicação de observação [acesso por URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) e [acesso de porta](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) requisitos para migração sem agente.


## <a name="prepare-for-agent-based-vmware-migration"></a>Preparar a migração do VMware com base em agente

Reveja os requisitos de migração baseada em agente de VMs de VMware.

1. [Revisão](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) os requisitos de servidor do VMware para migração sem agente. 
2. Configurar uma conta para aceder ao vCenter Server com o [permissões obrigatórias](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) para a migração sem agente.
3. [Tenha em atenção](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) os requisitos para VMs de VMware que pretende migrar para o Azure utilizando a migração baseada em agente, incluindo a instalação de mobilidade do serviço em cada VM que pretende migrar.
4. Tenha em atenção [acesso por URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Revisão [acesso a porta](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) requisitos para o serviço de mobilidade em execução em cada VM e para o servidor de configuração do Azure Migrate.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:
 
> [!div class="checklist"] 
> * Configure as permissões do Azure.
> * Preparar o VMware para avaliação e migração.


Avance para o segundo tutorial para configurar um projeto do Azure Migrate e avaliar VMware VMs para migração para o Azure.

> [!div class="nextstepaction"] 
> [Avaliar VMware VMs](./tutorial-migrate-vmware.md) 

