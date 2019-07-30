---
title: Preparar VMs VMware para avaliação e migração para o Azure com migrações para Azure | Microsoft Docs
description: Descreve como preparar a avaliação e a migração de VMs do VMware locais para o Azure usando as migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 286223e091ab0fe8521c29de12e37f5a112240dc
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640765"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparar VMs VMware para avaliação e migração para o Azure

Este artigo descreve como se preparar para a avaliação e a migração de VMs do VMware locais para o Azure, usando as migrações para [Azure](migrate-services-overview.md).

As migrações para [Azure](migrate-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros. 


Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMs VMware. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare o Azure. Configure permissões para sua conta do Azure e recursos para trabalhar com as migrações para Azure.
> * Prepare servidores VMware locais e VMs para avaliação de VM.
> * Prepare servidores VMware locais e VMs para migração de VM.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, examine o como o tos para avaliação e migração do VMware.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

Você precisa destas permissões do Azure:

- Sua conta do Azure precisa de permissões para criar um projeto de migração do Azure para avaliação e migração. 
- Para a avaliação e a migração sem agente de VMs VMware, as migrações para Azure executam um dispositivo leve que descobre VMs e envia metadados de VM e dados de desempenho para migrações para Azure. No Azure, você precisa de permissões para registrar o dispositivo de migrações para Azure.
- Para migrar VMs VMware usando a migração de servidor de migrações para Azure, as migrações para Azure criam uma Key Vault no grupo de recursos, para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você precisa de permissões de atribuição de função no grupo de recursos no qual o projeto de migração do Azure reside. 


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal do Azure, abra a assinatura e selecione **controle de acesso (iam)** .
2. Em **verificar acesso**, localize a conta relevante e clique nela para exibir permissões.
3. Você deve ter permissões de **colaborador** ou de **proprietário** .
    - Se você acabou de criar uma conta gratuita do Azure, você é o proprietário da sua assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registrar o dispositivo

Se você estiver implantando o dispositivo de migrações para Azure para avaliar ou executar uma migração sem agente de VMs, será necessário registrá-lo.

- Durante o registro do dispositivo, as migrações para Azure criam dois aplicativos Azure Active Directory (Azure AD) que identificam exclusivamente o dispositivo
    - O primeiro aplicativo se comunica com os pontos de extremidade de serviço de migrações para Azure.
    - O segundo aplicativo acessa um Azure Key Vault criado durante o registro para armazenar as informações de aplicativo do Azure AD e as definições de configuração do dispositivo.
- Você pode atribuir permissões para migrações para Azure para criar esses aplicativos do Azure AD usando um dos seguintes métodos:
    - Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
    - Um locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo (que tem as permissões) à conta.

Vale a pena observar que:

- Os aplicativos não têm nenhuma outra permissão de acesso na assinatura diferente daquelas descritas acima.
- Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo for configurado. 


#### <a name="grant-account-permissions"></a>Permissões de conta de concessão

O locatário/administrador global pode conceder permissões da seguinte maneira

1. No Azure AD, o locatário/administrador global deve navegar até **Azure Active Directory** > **configurações de usuário**de**usuários** > .
2. O administrador deve definir **registros de aplicativo** como **Sim**.

    ![Permissões do Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Atribuir função de desenvolvedor de aplicativo 

O locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Atribuir permissões de atribuição de função

Atribua permissões de atribuição de função no grupo de recursos no qual o projeto de migrações para Azure reside, da seguinte maneira:

1. No grupo de recursos no portal do Azure, selecione **controle de acesso (iam)** .
2. Em **verificar acesso**, localize a conta relevante e clique nela para exibir permissões.

    - Para executar a avaliação do servidor, as permissões de **colaborador** são suficientes.
    - Para executar a migração de servidor sem agente, você deve ter permissões de **proprietário** (ou **colaborador** e **administrador de acesso do usuário**).

3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos. 



## <a name="prepare-for-vmware-vm-assessment"></a>Preparar para avaliação de VM do VMware

Para se preparar para a avaliação de VM do VMware, você precisa verificar as configurações do host e da VM do Hyper-V e verificar as configurações de implantação do dispositivo.

### <a name="verify-vmware-settings"></a>Verificar as configurações do VMware

1. [Verificar](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) Requisitos do servidor VMware para avaliação de VM.
2. [Verifique](migrate-support-matrix-vmware.md#assessment-port-requirements) se as portas necessárias estão abertas nos servidores vCenter.


### <a name="set-up-an-account-for-assessment"></a>Configurar uma conta para avaliação

As migrações para Azure precisam acessar o vCenter Server para descobrir VMs para avaliação e migração sem agente. Somente para avaliação, você precisa de uma conta somente leitura para o vCenter Server.

Se você estiver usando um firewall baseado em URL. proxy, permita o acesso às [URLs do Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements)necessárias.

Certifique-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.


### <a name="verify-appliance-settings-for-assessment"></a>Verificar as configurações do dispositivo para avaliação

Antes de configurar o dispositivo migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. Verifique os requisitos de [verificação](migrate-support-matrix-vmware.md#assessment-appliance-requirements) para configurar o dispositivo de migrações para Azure no VMware.
2. [Examine](migrate-support-matrix-vmware.md#assessment-url-access-requirements) as URLs do Azure que o dispositivo precisará acessar.
3. Examine os dados que o dispositivo coletará durante a descoberta e a avaliação.
4. [Anote](migrate-support-matrix-vmware.md#assessment-port-requirements) os requisitos de acesso da porta para o dispositivo.
5. Você implanta o dispositivo migrações para Azure como uma VM VMware usando um arquivo OVA. Em vCenter Server, verifique se sua conta tem permissões para criar uma VM usando um arquivo OVA.


## <a name="prepare-for-agentless-vmware-migration"></a>Preparar para a migração do VMware sem agente

Examine os requisitos para a migração sem agente de VMs VMware.

1. [Examinar](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) Requisitos do servidor VMware para migração sem agente.
2. Configure uma conta para acessar o vCenter Server com as [permissões necessárias](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) para a migração sem agente.
3. [Observe](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) os requisitos para VMs do VMware que você deseja migrar para o Azure usando a migração sem agente.
4. [Examine](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) os requisitos do dispositivo para a migração sem agente.]
5. Observe o [acesso à URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) do dispositivo e os requisitos de [acesso à porta](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) para a migração sem agente.


## <a name="prepare-for-agent-based-vmware-migration"></a>Preparar para a migração VMware baseada em agente

Examine os requisitos para a migração baseada em agente de VMs VMware.

1. [Examinar](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) Requisitos do servidor VMware para migração sem agente. 
2. Configure uma conta para acessar o vCenter Server com as [permissões necessárias](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) para a migração sem agente.
3. [Observe](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) os requisitos para VMs do VMware que você deseja migrar para o Azure usando a migração baseada em agente, incluindo a instalação do serviço de mobilidade em cada VM que você deseja migrar.
4. Observação: [acesso à URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Examine os requisitos de [acesso de porta](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) para o serviço de mobilidade em execução em cada VM e para o servidor de configuração de migrações para Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:
 
> [!div class="checklist"] 
> * Configure as permissões do Azure.
> * VMware preparado para avaliação e migração.


Continue no segundo tutorial para configurar um projeto de migrações para Azure e avaliar as VMs do VMware para migração para o Azure.

> [!div class="nextstepaction"] 
> [Avaliar VMs VMware](./tutorial-migrate-vmware.md) 

